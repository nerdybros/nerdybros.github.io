# Simple Producer

spring-cloud-stream-kafka를 이용한 가장 단순한 Kafka Producer 구현 방법입니다.

## pom.xml dependency 추가
다음과 같은 2개의 `dependency`를 `pom.xml` 파일에 추가합니다.

```xml
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-stream</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-stream-binder-kafka</artifactId>
		</dependency>
```
