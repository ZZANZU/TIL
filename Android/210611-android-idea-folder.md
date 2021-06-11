# 안드로이드 스튜디오 프로젝트에 자리잡은 .idea 폴더는 무엇이며 어떻게 관리할까?

## 1. 문제 인식
- 몇일 전(?)부터 코드를 pull 받고 push할 때마다 .idea 폴더 내의 잡다구리한 파일 때문에 신경이 쓰이기 시작했다. 직접적인 안드로이드 코드도 아니고, 실행에 영향이 있는 코드도 아니어서 뭔가 넘어가기도 애매하고 신경쓰기도 애매한 상황이 되었다.<br><img src="https://user-images.githubusercontent.com/26498433/121682834-890d1180-caf7-11eb-9ad0-0a31ae3f5278.png" width=400>

- 나 혼자 관리하는 코드도 아니고 어쨌든 프로젝트를 관리하는 입장에서 이런 상황을 넘어갈 수만은 없어서 상대적으로 시간이 비는 금요일 저녁(?)에 한 번 어떤 놈인지도 알아보고 해결도 해보고자 한다.

## 2. .idea ?
- .idea 폴더는 Intellij 계열의 IDE(Android Studio, WebStorm 등)에서의 설정값들을 저장한다.
- 이 중에는 프로젝트별로 공통된 파일도 있지만 IDE를 사용하는 사용자별로 다른 파일들도 존재하기 때문에 해당 파일들이 github등으로 다른 개발자들과 공유가되면 곤란(?)한 상황이 발생할 수도 있다.

### 2.1 .idea에 저장되는 파일들
- assetWizardSettings.xml : 가장 최근에 추가된 아이콘 파일을 저장한다. 

## 3. 관리 방법
- .gitignore에 ./idea/ 를 추가해주었다.
- local이 아닌 git 상에서의 .idea 파일을 삭제하였다(로컬 X)
- 기본적으로는 안드로이드 스튜디오로 프로젝트를 생성하면 root 경로와 app 경로에 각각 하나씩 .gitignore 파일을 생성해준다.
- 게다가 .idea 폴더 내의 몇 개 파일에 대해서 기본적으로 추가해주기도 한다.

```
*.iml
.gradle
/local.properties
/.idea/workspace.xml
/.idea/libraries
.DS_Store
/build
/captures
.externalNativeBuild
```

- 

## Ref
- [Deep dive into .idea folder in Android Studio](!https://proandroiddev.com/deep-dive-into-idea-folder-in-android-studio-53f867cf7b70)
- [Android project settings Git ignore file and .idea folder problem](!https://www.programmersought.com/article/46824643626/)
- [I have .idea in gitignore, but it is still in local changes](!https://intellij-support.jetbrains.com/hc/en-us/community/posts/115000001824-I-have-idea-in-gitignore-but-it-is-still-in-local-changes)
