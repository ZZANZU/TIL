# 안드로이드 ANR 분석하기

## 1. ANR이란?
```
- Android 앱의 UI 스레드가 너무 오랫동안 차단되면 'ANR(애플리케이션 응답 없음)' 오류가 트리거됩니다.

- ANR은 UI 업데이트를 담당하는 앱의 기본 스레드가 사용자 입력 이벤트 또는 그림을 처리하지 못하여 사용자 불만을 초래하므로 문제가 됩니다.
```

## 2. 스택 트레이스 로그 분석
### 2.1 스택 트레이스 로그 형태
<img src="https://user-images.githubusercontent.com/26498433/121126428-18e65d80-c863-11eb-9828-8e22c58682d4.png"/>

```
"{스레드 이름}" prio={스레드 우선순위} tid={스레드 ID} {스레드 상태}
```
### 2.2 스레드 상태(Android 기준)
- running - executing application code(실행중)
- sleeping - called Thread.sleep() 
- monitor - waiting to acquire a monitor lock(다른 쓰레드가 작업을 마치기를 기다리는 중)
- wait - in Object.wait() 
- native - executing native code 
- vmwait - waiting on a VM resource 
- zombie - thread is in the process of dying 
- init - thread is initializing (you shouldn't see this) 
- starting - thread is about to start (you shouldn't see this either)

### 2.3 내가 직면했던 상황
모든 ANR의 리포트된 케이스들이
```
Broadcast of Intent{...cmp=com.petpeotalk.dogibogi_android/com.google.firebase.iid.FirebaseInstanceIdReceiver}
```
로 끝나길래, 지금까지는 이게 FCM 관련한 에러인 줄 알고 FCM 관련한 이슈로 원인을 분석하고 있었다.

하지만 각 케이스들의 스택 트레이스를 들여다보니

Activity A에서 푸시 메세지(FCM)를 수신하는 과정에서 ANR이 발생하기 때문에 FCM 관련한 것으로 리포팅 되었던 것 이었고,

이 과정에서 메인 스레드가 WAITING 상태로 갇히게 되는 것을 발견하였다.

Activity A에서는 푸시 메세지를 받으면 작동중인 스레드를 종료하기위해 메인 스레드에서 join()을 사용하는데,

여기서 스레드가 종료되지 않으면 메인 스레드 자체가 아무것도 안하며 멈추게 되는 것이다. 

따라서 join()이 실행되는 타이밍과 조건에 집중해서 전체적인 로직을 일부 수정하게 되었다. 

## 3. 느낀점

- 이래서 멀티쓰레드 관련한 책들이 도서관에 많았던 거구나... 섬세한 작업인 것 같다.

- 이제 스택 트레이스 로그를 봐도 조금은 알아볼 수 있을 것 같다.

## Ref
- [ANR](!https://developer.android.com/topic/performance/vitals/anr.html)
- [스레드 덤프 분석하기](!https://d2.naver.com/helloworld/10963) 
- [스레딩을 통한 성능 개선](!https://developer.android.com/topic/performance/threads)