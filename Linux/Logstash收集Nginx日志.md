## 1.配置Nginx日志格式为json
```xml
log_format json '{"@timestamp":"$time_iso8601",'
                 '"host":"$server_addr",'
                 '"clientip":"$remote_addr",'
                 '"size":$body_bytes_sent,'
                 '"responsetime":$request_time,'
                 '"upstreamtime":"$upstream_response_time",'
                 '"upstreamhost":"$upstream_addr",'
                 '"http_host":"$host",'
                 '"url":"$uri",'
                 '"xff":"$http_x_forwarded_for",'
                 '"referer":"$http_referer",'
                 '"agent":"$http_user_agent",'
                 '"status":"$status"}';

access_log  logs/access.log  json;
```
## 2.配置logstash
```xml
input {
    file {
        path => [
            # 这里填写需要监控的文件
            "/usr/local/nginx/logs/access.log"
        ]
        codec => json
    }
}

filter {
    mutate {
        split => [ "upstreamtime", "," ]
    }
    mutate {
        convert => [ "upstreamtime", "float" ]
    }
}

output {
    elasticsearch {
        hosts => ["127.0.0.1:9200"]
        index => "logstash-%{type}-%{+YYYY.MM.dd}"
        flush_size => 2000
        idle_flush_time => 10
    }
    stdout{}
}
```
## 3.刷新索引
打开Kibana，Setting ---> indices ---> logstash-*  刷新即可    
