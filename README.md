# 快速开始
## Spring Boot 应用暴露监控指标
添加依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>io.prometheus</groupId>
    <artifactId>simpleclient_spring_boot</artifactId>
    <version>0.3.0</version>
</dependency>
```
启动类添加注解
```java
package com.hoo.monitor;

import io.prometheus.client.spring.boot.EnablePrometheusEndpoint;
import io.prometheus.client.spring.boot.EnableSpringBootMetricsCollector;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@EnablePrometheusEndpoint
@EnableSpringBootMetricsCollector
public class MonitorApplication {

	public static void main(String[] args) {
		SpringApplication.run(MonitorApplication.class, args);
	}
}
```
关闭安全设置(生产建议使用用户名密码)
```
management.security.enabled: false
```
访问测试
```
http://localhost:8080/prometheus
```

## Prometheus 采集 Spring Boot 数据
```bash
docker pull prom/prometheus
```
编写配置文件 prometheus.yml
```yaml
global:
  scrape_interval: 10s
  scrape_timeout: 10s
  evaluation_interval: 10m
scrape_configs:
  - job_name: spring-boot
    scrape_interval: 5s
    scrape_timeout: 5s
    metrics_path: /application/prometheus
    scheme: http
    basic_auth:
      username: user
      password: pwd
    static_configs:
      - targets:
        - 127.0.0.1:8080  #此处填写 Spring Boot 应用的 IP + 端口号
```
启动 prometheus
```bash
docker run -d \
--name prometheus \
-p 9090:9090 \
-m 500M \
-v "$(pwd)/prometheus.yml":/prometheus.yml \
-v "$(pwd)/data":/data \
prom/prometheus \
-config.file=/prometheus.yml \
-log.level=info
```
访问测试
```
http://localhost:9090/targets
```

## Grafana 可视化监控数据
```
$ docker run --name grafana -d -p 3000:3000 grafana/grafana
```
登录 grafana 配置数据源
> 登录 admin/admin

配置单个指标的可视化面板
> graph

参考地址  
http://www.spring4all.com/article/265
