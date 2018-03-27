摘抄自：[文章出处](https://my.oschina.net/zhuqingbo0501/blog/1784693)

## Java8几个新特性
### 1.接口默认方法
Java 8允许给接口添加一个非抽象的方法实现，只需要使用default关键字即可，代码如下：

```java
interface Formula {

    double calculate(int a);
    
    /**
     * 默认方法
     */
    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
```

### 2.Lambda表达式

### 3.函数式接口

### 4.方法与构造函数引用

### 5.Date API
在包java.time下包含了一组全新的时间日期API，下面是比较重要的几个类:

+ Clock：提供了访问当前日期和时间的方法，Clock是时区敏感的，可以用来取代`System.currentTimeMillis()`来获取当前的微秒数
```java
Clock clock = Clock.systemDefaultZone();
long millis = clock.millis();
Instant instant = clock.instant();
Date legacyDate = Date.from(instant); 
```

+ ZoneId：新API中时区使用ZoneId来表示，时区可以很方便的使用静态方法of来获取
```java
// prints all available timezone ids
System.out.println(ZoneId.getAvailableZoneIds());

ZoneId zone1 = ZoneId.of("Europe/Berlin");
ZoneId zone2 = ZoneId.of("Brazil/East");
```

+ LocalTime：定义了一个没有时区信息的时间。下面的例子创建两个不同时区的本地时间，并比较两个时间的时间差
```java
LocalTime now1 = LocalTime.now(zone1);
LocalTime now2 = LocalTime.now(zone2);

long hoursBetween = ChronoUnit.HOURS.between(now1, now2);
long minutesBetween = ChronoUnit.MINUTES.between(now1, now2);
```

+ LocalDate：本地时间，下面的例子展示了如何给Date对象加减天/月/年
```java
LocalDate today = LocalDate.now();
LocalDate tomorrow = today.plus(1, ChronoUnit.DAYS);
LocalDate yesterday = tomorrow.minusDays(2);
LocalDate independenceDay = LocalDate.of(2014, Month.JULY, 4);
DayOfWeek dayOfWeek = independenceDay.getDayOfWeek();


System.out.println(dayOfWeek);    // FRIDAY
```

+ LocalDateTime：本地日期时间，相当于前两个API合并到一个对象上

### 6.支持多重注解
允许我们把同一个类型的注解使用多次，只需要给该注解标注`@Repeatable`即可，如：
```java
@Rule("rule1")
@Rule("rule2")
class Student {}
```




