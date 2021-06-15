# 안드로이드 ABI
- ffmpeg 라이브러리를 사용하면서, 일부 기종에서는 특정 라이브러리를 찾지 못하는 에러(UnsatisfiedLinkError)가 리포팅되는 것을 인지하게 되었다.
<br><img src="https://user-images.githubusercontent.com/26498433/122062932-ce4c7e80-ce2a-11eb-83e1-249252132119.png" width=700/>
- 요놈의 원인은 무엇이며, 왜 발생한 것인지를 알아보고자 한다.

## 1. abi?

- [Android ABI](!https://developer.android.com/ndk/guides/abis)
- Application Binary Interface의 줄임말. 
- 출처 : [야토팍 블로그](!https://blog.yatopark.net/2016/03/12/%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C%EC%9D%98-abi-%EA%B4%80%EB%A6%AC/)

```
안드로이드 디바이스는 제조사의 사정에 따라서 입맛대로 CPU를 골라 쓸 수 있다. 
이쪽에서 가장 대표적인 ARM을 비롯하여 MIPS, x86을 지원한다. 
이들이 사용하는 명령 세트는 모두 다르며, 각 아키텍쳐 – 명령세트의 조합은 자신들에게 맞는 ABI(Application Binary Interface)를 갖는다.

ABI란, 런타임에 시스템과 앱의 머신코드가 어떻게 상호작용할지를 기술한 인터페이스이다.  
so파일을 로딩하는 경우, 머신코드-아키텍쳐에서 사용하는 ABI와 일치해야 구동이 가능하다. 
ARM 칩에서 x86 머신코드를 네이티브로 실행할 순 없지 않은가?

ABI는 보통 이런 정보들을 포함하고 있다.

- 머신코드가 사용해야 하는 CPU 명령 세트.
- 런타임에 사용할 메모리 로드/스토어 endianness.
- ABI에서 지원하는 실행가능한 바이너리 포맷(프로그램,  shared lib)
- 당신의 코드와 시스템간의 데이터 전달을 위한 다양한 컨벤션. 이 컨벤션들은 시스템이 함수호출 시 스택과 레지스터를 어떻게 사용할지 뿐만 아니라 alignment 제약사항까지 포함한다.
- 일반적으로 매우 특정한 라이브러리들에서, 런타임시 당신의 머신코드에서 사용가능한 함수 심볼의 목록.
```

- 갑자기 학부 때 수강한 컴퓨터 구조 수업이 주마등처럼 지나간다ㅎㅎ 그래도 익숙한 단어들이 보이는걸 보니 대학 등록금이 헛되지는 않았구나!

## 2. 개발중인 앱이 32-bit와 64-bit CPU를 모두 지원하는지 확인해보자
- Ref : https://www.youtube.com/watch?v=E96vmWkUdgA
- 안드로이드 스튜디오 메뉴의 ```Build > Anaylze APK... > 해당 앱의 apk 파일 선택```
<br><img src="https://user-images.githubusercontent.com/26498433/122056156-4cf1ed80-ce24-11eb-9e66-94e7016fcfdf.png"/>
- lib 폴더 선택 후 하위 폴더 확인
<br><img src="https://user-images.githubusercontent.com/26498433/122056510-9cd0b480-ce24-11eb-8670-52f4cadd8c09.png"/>
- armeabi, armeabi-v7a, x86 폴더가 있을 경우, 해당 앱이 32-bit native app component를 가지고 있다는 의미.
- arm64-v8a, x86_64 폴더의 경우, 해당 앱이 64-bit를 지원한다는 의미.
- 2014년 Lollipop 버전부터 64-bit 기기를 지원하기 시작하면서, 2021년까지 플레이스토어의 모든 앱들이 64-bit를 지원하도록 앱을 수정하도록 하는 요구함.
- 가장 간단한 방법은 기존 32-bit 라이브러리(x86, armeabi-v7a)에 64-bit 라이브러리(x86_64, arm64-v8a)를 추가하는 거지만, 이렇게 되면 앱 크기가 너무 커짐.
- 이러한 경우를 위해 구글에서는 Android App Bundle이라는 새로운 배포 형식(publishing format)을 지원함.
- App Bundle로 앱을 배포하면 각 기기에 필요한 라이브러리만 다운로드되도록 해줌.
