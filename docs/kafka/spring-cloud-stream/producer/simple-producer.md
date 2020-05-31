
# Simple Producer
spring-cloud-stream-kafka를 이용한 가장 단순한 Kafka Producer 구현 방법입니다.

<br/><br/>

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

<br/><br/>

## 프로젝트 패키지 구조
spring-cloud-stream 라이브러리를 사용하여 producer를 구현하기 위해서 interface/class 들을 생성할 필요가 있으며 이해를 돕기 위해 패키지 구조를 다음과 같이 정리하였습니다.<br/>

![Alt text](https://nerdybros.github.io/resources/kafka/spring-cloud-stream/producer/simple-producer/simple-producer-resource-01.JPG)  

<br/><br/>

## 주요 코드
### SourceBinding.java
spring-cloud-stream 라이브러리는 채널(Channel)이라는 개념을 통해 손쉽게 카프카에 데이터를 송수신할 수 있습니다. producer는 메세지를 송신하는 역할을 하며 `@Output` 이라는 애너테이션을 통해 하나의 메소드를 메세지를 발행할 수 있는 채널을 만들 수 있습니다.
```java
import org.springframework.cloud.stream.annotation.Output;
import org.springframework.messaging.MessageChannel;

public interface SourceBinding {

	String OUTPUT_CHANNEL = "output-channel";

	@Output(OUTPUT_CHANNEL)
	MessageChannel outputChannel();
}
```

<br/><br/>

### SimpleProducer.java
카프카에 메세지를 송신하기 위한 채널을 만들었으니 이제 메세지를 발행하는 역할을 하는 producer를 구현해보도록 하겠습니다. `@EnableBinding` 애너테이션은 통해 SimpleProducer 클래스는 Spring Bean으로 등록됩니다.<br/>
`@EnableBinding`을 통해 등록된 SourceBinding 클래스는 SimpleProducer Bean에서 채널 정보를 가진 Bean으로 주입받아 사용이 가능합니다. 위 `SourceBinding.java` 파일에서 만든 `outputChannel()`을 활용하여 메세지를 카프카에게 전달합니다.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.integration.support.MessageBuilder;

@EnableBinding(value = { SourceBinding.class })
public class SimpleProducer {

	@Autowired
	private SourceBinding bindingChannels;

	public void sendMessage(String message) {
		System.out.println("### producer message to broker : " + message);
		bindingChannels.outputChannel().send(MessageBuilder.withPayload(message).build());
	}
}
```

<br/><br/>

### ProducerController.java
테스트를 위한 RestController 파일을 만들었습니다. Insomnia, PostMan 과 같은 Rest API 테스트용 Tool을 활용하여 테스트를 해보기 위한 클래스입니다.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import io.nerdybros.course.producer.SimpleProducer;

@RestController
@RequestMapping("producer")
public class ProducerController {

	@Autowired
	private SimpleProducer simpleProducer;

	@GetMapping(value = "/sendMessage")
	public void sendMessageToKafka(@RequestParam(value = "message") String message) {
		this.simpleProducer.sendMessage(message);
	}
}
```

<br/><br/>

### application.yml
spring-cloud-stream 라이브러리를 통해 카프카에 접근할 때 가장 중요한 파일은 설정이 명시되어있는 application.yml 파일입니다. 해당 파일에 현재 Application 이 사용하는 토픽, 파티션, 채널, 기타 카프카 관련 설정이 명시되어야만 정확한 동작이 가능합니다.
```yml
spring:
  cloud:
    stream:
      bindings:
        output-channel: # 채널 이름
          destination: nerdy-bros # 토픽 이름
      kafka:
        binder:
          brokers:
					- localhost:9092
```

<br/><br/>

## 테스트 방법
