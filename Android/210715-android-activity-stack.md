# 현재 실행중인 앱의 액티비티 스택 확인하는 방법

## 1. 개요
- 액티비티 전환간의 리소스 할당과 해제 타이밍이 중요한 기능을 테스트하던 도중, 어떻게 효율적으로 확인할 수 있을까? 하는 생각에서 검색을 시작하였다.
- 기존에는 생명주기 메소드(onCreate, onResume 등)에 무식하게(?) 로그를 추가해서 확인했었는데, 너무 비효율적이었다.
- 그러던중 adb 터미널 명령어로 확인이 가능하다는 것을 확인했다.
- 명령어는 아래와 같다.
```bash
$adb shell dumpsys activity activities | sed -En -e “/Stack #/p ” -e “/Running activities/,/Run #0/p”
```
- 터미널에 위의 명령어를 입력하면 아래와 같은 결과를 확인할 수 있다.
```bash
  Stack #1:
    Running activities (most recent first):
      TaskRecord{4844b83d0 #159 A={앱 패키지명}} U=0 StackId=1 sz=2}
        Run #1: ActivityRecord{e348c0dd0 u0 {앱 패키지명}/{앱 패키지명}.{액티비티2} t159}
        Run #0: ActivityRecord{e23e359d0 u0 {앱 패키지명}/{앱 패키지명}.{액티비티1} t159}
  Stack #0:
    Running activities (most recent first):
      TaskRecord{2849e2dd0 #143 A=com.sec.android.app.launcher U=0 StackId=0 sz=1}
        Run #1: ActivityRecord{c99b3f1d0 u0 com.sec.android.app.launcher/.activities.LauncherActivity t143}
      TaskRecord{406b61bd0 #146 A=com.android.systemui U=0 StackId=0 sz=1}
        Run #0: ActivityRecord{72888ffd0 u0 com.android.systemui/.recents.RecentsActivity t146}

```

## 2. 참고
- [ADB란?](https://developer.android.com/studio/command-line/adb?hl=ko)
