# https配置

## 场景思路

+ 仅考虑本地SpringCloud-Gateway为基础的后端项目，不考虑整体证书配置
+ cloud项目中，请求由网关转到下游服务，并且参考鉴权/放行逻辑，所以配置的核心是gateway网关服务
+ 当gateway接收到https请求并转至下游服务节点时，下游收到的请求依然会是https，此时两个思路
  + A：每个服务模块配置https
  + B：网关做拦截，将https转为http，再到下游服务，此时下游服务不需要配置

## 证书

+ 理论上证书应该是花钱买的，通过CA仓库识别
+ 本地开发免费渠道：
  + openSSL工具生成
  + JDK的keytool生成（由于只需要配置项目且上一个没操作明白，选这个）
+ 未认证的免费版证书会提示不安全（对开发没什么影响）

## JDK证书生成

+ jdk的bin目录下进入cmd，输入指令，生成证书

```cmd
keytool -genkey -alias root -validity 365 -keyalg RSA -keystore ./server.keystore
```

+ 生成证书时会填写一部分信息，尤其是密码，可以随便输，但是要记好，后面会用
+ 如果别名/类型忘记  可通过命令查看，但是需要密码：

```cmd
keytool -list -v -keystore server.keystore
```



## Gateway配置

+ 将生成好的证书配置到gateway目录下，与pom文件同级即可
+ 配置yml文件

```yml
server:
  port: 10001
  ssl:
    key-store: server.keystore 	# 证书名
    key-store-password: 123456 	# 密码
    key-store-type: JKS			# 证书类型
    key-alias: root				# 别名
    enabled: true				# 字面意思
```

+ 配置httpsConfig，配置tomcatWebServer

```java
/**
 * @author guocong
 * @date 2021/11/17 19:00
 */
@Configuration
public class HttpsConfig {

    /**
     * spring boot 2.0
     * @return
     */
    @Bean
    public TomcatServletWebServerFactory servletContainer() {
        TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory() {
            @Override
            protected void postProcessContext(Context context) {
                SecurityConstraint constraint = new SecurityConstraint();
                constraint.setUserConstraint("CONFIDENTIAL");
                SecurityCollection collection = new SecurityCollection();
                collection.addPattern("/*");
                constraint.addCollection(collection);
                context.addConstraint(constraint);
            }
        };
        tomcat.addAdditionalTomcatConnectors(httpConnector());
        return tomcat;
    }

    @Bean
    public Connector httpConnector() {
        Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
        connector.setScheme("http");
        //Connector监听的http的端口号
        connector.setPort(10001);
        connector.setSecure(false);
        //监听到http的端口号后转向到的https的端口号
        connector.setRedirectPort(10001);
        return connector;
    }

}
```

+ springboot - 1.0版本，估计没啥用

```java
    /**
     * spring boot 1.0
     */
    @Bean
    public EmbeddedServletContainerFactory servletContainer() {
        TomcatEmbeddedServletContainerFactory tomcat = new TomcatEmbeddedServletContainerFactory() {
            @Override
            protected void postProcessContext(Context  context) {
                SecurityConstraint constraint = new SecurityConstraint();
                constraint.setUserConstraint("CONFIDENTIAL");
                SecurityCollection collection = new SecurityCollection();
                collection.addPattern("/*");
                constraint.addCollection(collection);
                context.addConstraint(constraint);
            }
        };
        tomcat.addAdditionalTomcatConnectors(httpConnector());
        return tomcat;
    }
```

+ 截至此时，https已经配置完毕，但核心问题没有解决，下游服务收到的仍然后是https请求，所以对网关接收到的请求做拦截处理

```java
/**
 * https转http拦截
 *
 * @author guocong
 * @date 2021/11/17 19:54
 */
@Component
public class HttpsToHttpFilter implements GlobalFilter, Ordered {

    private static final int HTTPS_TO_HTTP_FILTER_ORDER = 10000;


    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        URI originalUri = exchange.getRequest().getURI();
        ServerHttpRequest request = exchange.getRequest();
        ServerHttpRequest.Builder mutate = request.mutate();
        String forwardedUri = request.getURI().toString();
        // 请求处理
        if (forwardedUri != null && forwardedUri.startsWith("https")) {
            try {
                URI mutatedUri = new URI("http",
                        originalUri.getUserInfo(),
                        originalUri.getHost(),
                        originalUri.getPort(),
                        originalUri.getPath(),
                        originalUri.getQuery(),
                        originalUri.getFragment());
                mutate.uri(mutatedUri);
            } catch (Exception e) {
                throw new IllegalStateException(e.getMessage(), e);
            }
        }
        ServerHttpRequest build = mutate.build();
        return chain.filter(exchange.mutate().request(build).build());
    }

    @Override
    public int getOrder() {
        return HTTPS_TO_HTTP_FILTER_ORDER;
    }
}

```

