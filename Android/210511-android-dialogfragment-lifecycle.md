# DialogFragment의 Life Cycle

## 1. 개요
- DialogFragment위에 기존의 목적 이상의 기능(canvas, view 전환 등)이 동작하다보니, life cycle을 제대로 알고 구현할 필요가 생겼음.

## 2. Life cycle
- [공식 문서](!https://developer.android.com/reference/android/app/DialogFragment#Lifecycle)
- 일반적인 Dialog로써 사용한다면 신경쓸 필요가 없음.
- Fragment의 life cycle과 유사함.

```
onAttach
onCreate
(onCreateDialog))
onCreateView
(onActivityCreated)
onStart
onResume
```

```
onPause
onStop
onDestroyView
onDestroy
onDetach
```