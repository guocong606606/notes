# Cron表达式

+ 说明

| 字段                     | 允许值                                                       | 允许的特殊字符             |
| ------------------------ | ------------------------------------------------------------ | -------------------------- |
| 秒（Seconds）            | 0~59的整数                                                   | , - * /   四个字符         |
| 分（*Minutes*）          | 0~59的整数                                                   | , - * /   四个字符         |
| 小时（*Hours*）          | 0~23的整数                                                   | , - * /   四个字符         |
| 日期（*DayofMonth*）     | 1~31的整数（但是你需要考虑你月的天数）                       | ,- * ? / L W C   八个字符  |
| 月份（*Month*）          | 1~12的整数  或   月份英文简写（JAN-DEC）                     | , - * /   四个字符         |
| 星期（*DayofWeek*）      | 1~7的整数  或 与月份同理 SUN-SAT （1=SUN：每周的第一天是星期日） | , - * ? / L C #   八个字符 |
| 年(可选，留空)（*Year*） | 1970~2099                                                    | , - * /   四个字符         |

+ 特殊字符说明

| 字符 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| *    | 任意值。假如在Minutes域使用*, 即表示每分钟都会触发事件。     |
| ?    | 任意值。只能用在DayofMonth和DayofWeek两个域。但两个值同时使用会相互影响。 |
| -    | 范围。例如在Minutes域使用5-20，表示从5分到20分钟每分钟触发一次 |
| /    | 起始时间开始触发，然后每隔固定时间触发一次。例如在Minutes域使用0/15，当前小时的0分触发一次，而后每15分钟触发一次 |
| ,    | 列出枚举值。例如：在Minutes域使用5,20，则意味着在5和20分每分钟触发一次。 |
| L    | 最后，只能出现在DayofWeek和DayofMonth域。如果在DayofWeek域使用5L,意味着在最后的一个星期四触发 |
| W    | 有效工作日(周一到周五，,只能出现在DayofMonth域，系统将在离指定日期的最近的有效工作日触发事件。例如：在 DayofMonth使用5W，如果5日是星期六，则将在最近的工作日：星期五，即4日触发。如果5日是星期天，则在6日(周一)触发；如果5日在星期一到星期五中的一天，则就在5日触发。W的最近寻找不会跨过月份 |
| LW   | 某个月最后一个工作日，即最后一个星期五                       |
| C    | 允许在日期域和星期域出现。这个字符依靠一个指定的“日历”。也就是说这个表达式的值依赖于相关的“日历”的计算结果，如果没有“日历”关联，则等价于所有包含的“日历”。如：DayofMonth域是“5C”表示关联“日历”中第一天，或者这个月开始的第一天的后5天。星期域是“1C”表示关联“日历”中第一天，或者星期的第一天的后1天，也就是周日的后一天（周一） |
| #    | 用于确定每个月第几个星期几，只能出现在DayofMonth域。例如在4#2，表示某月的第二个星期三 |



* Cron表达式是一个字符串，字符串以5或6个空格隔开，分为6或7个域，每一个域代表一个含义，Cron有如下两种语法格式
  * Seconds Minutes Hours DayofMonth Month DayofWeek
  * Seconds Minutes Hours DayofMonth Month DayofWeek Year

+ 示例

```cron
0 0 0 * * ?   或  0 0 0 * * ? *
```

+ 注意：SpringBoot定时任务使用Cron表达式时，第七个域（年）是不支持的

```java
/**
* Parse the given pattern expression.
*/
private void parse(String expression) throws IllegalArgumentException {
String[] fields = StringUtils.tokenizeToStringArray(expression, " ");
if (!areValidCronFields(fields)) {
		throw new IllegalArgumentException(String.format(
				"Cron expression must consist of 6 fields (found %d in \"%s\")", fields.length, expression));
	}
	doParse(fields);
}
```

