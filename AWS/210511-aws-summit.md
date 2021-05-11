## 1. AWS 를 활용한 저지연 라이브 (Low Latency Live) 서비스 구현
- WebRTC는 1:N(many)의 사례보다는 아직 영상통화나 화상회의에 적합하다.
- CMAF(씨맵) 
: 동영상 컨테이너(ex. MP4, TS)
: 오래된 동영상 컨테이너들에 대한 개선점이 적용됨.
<img src="https://user-images.githubusercontent.com/26498433/117749471-cf4b2880-b24c-11eb-8d74-da81f5a0e54a.png">
: chunk를 나눔.
<img src="https://user-images.githubusercontent.com/26498433/117749512-dd994480-b24c-11eb-858f-0366f545c9ae.png">
: 자체로는 라이브 레이턴시를 줄이지 않음. TS보다 효율이 좋은 미디어 형식(컨테이너)임.
<img src="https://user-images.githubusercontent.com/26498433/117749639-146f5a80-b24d-11eb-8a81-91b9073ab690.png">
<img src="https://user-images.githubusercontent.com/26498433/117749739-39fc6400-b24d-11eb-887d-61015133fa79.png">
: 패키징과 플레이 버퍼에서 지연이 많이 일어남.
<img src="https://user-images.githubusercontent.com/26498433/117750194-f7875700-b24d-11eb-9a2e-83ac7c84d88a.png">

- 그렇다면 AWS를 통한 Low-latency 서비스는?
<img src="https://user-images.githubusercontent.com/26498433/117750269-17b71600-b24e-11eb-8439-ad2857ce1056.png">
<img src="https://user-images.githubusercontent.com/26498433/117750283-1f76ba80-b24e-11eb-91b0-22f7d828cae7.png">
: 데모
<img src="https://user-images.githubusercontent.com/26498433/117750346-387f6b80-b24e-11eb-91b3-179b65164dbd.png">
