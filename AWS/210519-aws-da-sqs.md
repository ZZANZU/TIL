# AWS DA 덤프 문제 풀이 - SQS

## 문제

**The development team at an analytics company is using SQS queues for decoupling the various components of application architecture. As the consumers need additional time to process SQS messages, the development team wants to postpone the delivery of new messages to the queue for a few seconds.**

**As a Developer Associate, which of the following solutions would you recommend to the development team?**

애널리틱스 회사의 개발팀에서는 어플리케이션 아키텍처의 여러 구성 요소들을 decoupling하기 위해 SQS 큐를 사용하고있다. consumer에서 SQS 메세지를 처리하기위한 추가적인 시간을 필요로하기 때문에, 개발팀에서는 큐로 들어오는 새로운 메세지를 몇 초 동안 늦추고싶어한다.

DA로써, 다음 중 어떤 방식을 추천하는가?

- Use FIFO queues to postpone the delivery of new messages to the queue for a few seconds
- Use visibility timeout to postpone the delivery of new messages to the queue for a few seconds
- **Use delay queues to postpone the delivery of new messages to the queue for a few seconds**
- Use dead-letter queues to postpone the delivery of new messages to the queue for a few seconds

### 설명

[https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-how-it-works.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-how-it-works.html)

Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications. SQS offers two types of message queues. Standard queues offer maximum throughput, best-effort ordering, and at-least-once delivery. SQS FIFO queues are designed to guarantee that messages are processed exactly once, in the exact order that they are sent.

Delay queues let you postpone the delivery of new messages to a queue for several seconds, for example, when your consumer application needs additional time to process messages. If you create a delay queue, any messages that you send to the queue remain invisible to consumers for the duration of the delay period. The default (minimum) delay for a queue is 0 seconds. **The maximum is 15 minutes.**

<img src="https://lh4.googleusercontent.com/YynP3XW9QCADQRk1TU7CQ0WfQ8oKueALSWSIwtTrxa_yg2PXQH7oyCmNAbT4Sw7hXhC3AgM-JKo4RXuUUtVDlfaYOYX9QvqXAVkroIECYLBNc6tdkTlxss-kJ_m07CNEDLIzrBx6">

via -

[https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-delay-queues.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-delay-queues.html)

[SQS, Lambda를 이용한 작업 대기열 만들기](https://devblog.croquis.com/ko/2017-05-13-1-aws-serverless-1/)

## 용어 정리

### Producer

- 처리할 작업 메시지를 SQS에 등록

### Trigger

- 큐 대기열에 있는 메시지들을 조회하기 위해 CloudWatch의 스케줄 이벤트를 이용하여 매분마다 Lambda Consumer 실행

### Consumer

- Lambda Consumer는 큐 대기열에 있는 메시지 목록을 조회하여 각 메시지를 Lambda Worker에서 처리할 수 있도록 실행

### Worker

- 메시지를 받아 작업을 처리하고 해당 메시지를 삭제

### dead-letter queue

- 메시지가 성공적으로 처리되지 못한 조건을 설정하고, 성공적으로 처리되지 못한 메시지들을 따로 처리할 수 있도록 도와줌.
- 원본 큐와 동일한 형태여야함.

### visibility timeout

- SQS에서 메시지는 특정 컴포넌트에 전달된 뒤 자동으로 삭제되지 않음. 다른 컴포턴트에서 중복된 메세지를 전달받을 수 있는 문제가 있기 때문에, 한번 전달된 메시지는 visible timeout에 설정된 일정 시간 동안은 다시 전달되지 않도록 함. 따라서 메시지를 받아서 visibility timeout 내에 작업을 완료하고 메시지를 delete해야함.

    [AWS SQS(Simple Queue Service) 소개](https://bcho.tistory.com/683)

---

## 설명

**Use FIFO queues to postpone the delivery of new messages to the queue for a few seconds** 

SQS FIFO 큐는 메시지가 들어온 순서대로 딱 한 번만 처리되도록 하기 위해 설계되었음. FIFO 큐로 메시지 들어오는 것을 지연하거나 할 수는 없음.

**Use dead-letter queues to postpone the delivery of new messages to the queue for a few seconds**

Dead-letter 큐는 별도의 큐를 생성하여 다른 큐에서 처리하지 못한 메시지를 받아옴. Dead-letter 큐는 우리가 운영하는 서비스의 디버깅을 할 때 유용함(실패한 케이스들을 모두 받아들이기 때문). delay하는 기능은 없음.

**Use visibility timeout to postpone the delivery of new messages to the queue for a few seconds** 

Visibility timeout은 다른 consumer들이 해당 메시지를 처리하지 못하도록 설정된 시간임. default로는 30초이고 최소 0초로 설정할 수 있음. visibility timeout으로 delay처리를 할 수는 없음.