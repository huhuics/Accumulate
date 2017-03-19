## Elasticsearch安装
```shell
tar -zxvf elasticsearch-2.3.5.tar.gz
cd elasticsearch-2.3.5/bin/
vim elasticsearch
	在非注释内容第一行加入如下内容：
	ES_JAVA_OPTS="-Des.insecure.allow.root=true"
nohup ./elasticsearch >nohup.log &
```

## Logstash安装
```shell
tar –zxvf logstash-2.1.1.tar.gz
cd logstash-2.1.1
vim logstash-redis.conf
input {
    redis {
        data_type => "list"
        key => "logstash"
        host => "127.0.0.1"
        port => 6379
        threads => 30
        codec => "json"
    }
}
filter{

}

output {
    elasticsearch {
        hosts => ["127.0.0.1:9200"]
        index => "logstash-%{type}-%{+YYYY.MM.dd}"
        document_type => "%{type}"
        flush_size => 2000
        idle_flush_time => 10
        template_overwrite => true
    }
    stdout{}
}
```
 + flush_size: 默认为500，当数据量到达500时logstash会向elasticsearch一次性发送数据
 + idle_flush_time: 数据量未到flush_size但是超过了idle_flush_time时间，也会发送数据。单位为秒

 + 启动
```shell
nohup ./logstash agent -f ../logstash-redis.conf >nohup.log &
```

## kibana安装
```shell
tar -zxvf kibana-4.3.0-linux-x64.tar.gz
cd kibana-4.3.0-linux-x64/bin/
nohup ./kibana >nohup.out &
```

访问http://ip:5601

## 程序配置
+ 引入依赖
```xml
        <dependency>
            <groupId>com.cwbase</groupId>
            <artifactId>logback-redis-appender</artifactId>
            <version>1.1.5</version>
    <!--        
    <exclusions>
                <exclusion>
                    <groupId>redis.clients</groupId>
                    <artifactId>jedis</artifactId>
                </exclusion>
            </exclusions>
     -->
        </dependency>
```
+ logback.xml配置
```xml
<appender name="logstash" class="com.cwbase.logback.RedisAppender">
		<source>logstashdemo</source>
		<type>dev</type>
		<host>168.33.131.164</host>
		<port>6379</port>
		<key>logstash</key>
		<tags>dev</tags>
		<mdc>true</mdc>
		<location>true</location>
		<callerStackIndex>0</callerStackIndex>
	</appender>
```
