# Simple Producer

spring-cloud-stream-kafka를 이용한 가장 단순한 Kafka Producer 구현 방법입니다.

## pom.xml dependency 추가
`pom.xml` 파일에 `dependency`를 추가하는 행위는 해당 프로젝트에서 필요한 라이브러리를 연동하겠다는 의미입니다. spring-cloud-stream 라이브러리를 통해 카프카를 사용하기 위해서 다음과 같은 2개의 `dependency`를 `pom.xml` 파일에 추가합니다.
```xml
<dependencies>
  ...
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-stream</artifactId>
	</dependency>

	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-stream-binder-kafka</artifactId>
	</dependency>
	...
</dependencies>
```

## 프로젝트 패키지 구조
spring-cloud-stream 라이브러리를 사용하여 producer를 구현하기 위해서 interface/class 들을 생성할 필요가 있으며 이해를 돕기 위해 패키지 구조를 다음과 같이 정리하였습니다.
![Alt text](https://nerdybros.github.io/resources/kafka/spring-cloud-stream/producer/simple-producer/simple-producer-resource-01.JPG)