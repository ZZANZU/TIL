# AWS DA 덤프 문제 풀이 - S3 multipart upload

## 문제

**You are an administrator for a video-on-demand web application where content creators upload videos directly into S3. Recent support requests from customers state that uploading video files near 500GB size causes the website to break. After doing some investigation you find the following error: 'Your proposed upload exceeds the maximum allowed size'.**

**What must you do to solve this error?**

- You need to use multi-part upload for large files(정답)
- The maximum file size is 5 GB
- You need to place a service limit request increase with AWS
- Your IAM permissions are incorrect

### 문제 해석

당신은 콘텐츠 크리에이터들이 S3에 바로 동영상을 업로드하는 VOD 웹 어플리케이션의 관리자입니다. 최근 고객 지원 부서에서 500GB 정도의 비디오 파일을 업로드하는 것이 웹사이트의 오류를 일으킨다고 보고해왔습니다. 당신은 조사 끝에 다음과 같은 에러를 발견했습니다. : '최대 허용 크기를 넘은 업로드를 하려고 합니다.'

이러한 에러를 해결하기 위해서는 무엇을 해야합니까?

---

## 해설

[Uploading objects](https://docs.aws.amazon.com/AmazonS3/latest/dev/UploadingObjects.html)

### 최대 업로드 가능 크기(GB)

- AWS SDK / REST API / AWS CLI ⇒ 하나의 오브젝트 당 **5GB**
- AWS S3 콘솔 ⇒ 하나의 오브젝트 당 **160GB**
- AWS SDK / REST API / AWS CLI 를 통한 **multipart 업로드** API ⇒ 오브젝트의 총 크기 **5TB** 까지 가능.

### multipart upload API

[Uploading objects using multipart upload API](https://docs.aws.amazon.com/AmazonS3/latest/dev/uploadobjusingmpu.html)

- S3에 100MB 이상의 오브젝트 업로드시 권장되는 방법임.
- 하나의 오브젝트를 여러 조각(part)으로 나눠서 업로드하는 방식.
- 순서 상관없이 여러 조각(part)을 업로드함.
- 업로드 중 한 부분(part)이 실패한 경우 다른 부분에 영향을 주지 않고 다시 업로드할 수 있음.
- 모든 part가 업로드되면 S3에서 하나의 오브젝트로 합쳐줌.

- **장점**
    - **개선된 처리량** - 파트를 병렬적으로 업로드하여 처리량을 개선할 수 있습니다.
    - **네트워크 문제로부터 빠른 복구** - 더 작아진 파트 크기는 네트워크 오류로 인해 실패한 업로드 재시작의 영향을 최소화합니다.
    - **객체 업로드 일시 중지 및 재개** – 객체 파트를 장시간에 걸쳐 업로드할 수 있습니다. 일단 멀티파트 업로드가 시작되면 제한 시간이 없습니다. 멀티파트 업로드를 명시적으로 완료하거나 중단해야 합니다.
    - **최종 객체 크기를 알기 전에 업로드를 시작** – 객체(오브젝트)를 생성하는 동안 업로드할 수 있습니다.
    <img src="https://s3.us-west-2.amazonaws.com/secure.notion-static.com/65f809d1-44cc-4f0a-8685-8f24529a3e5d/_2021-01-30__9.57.47.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210519T120300Z&X-Amz-Expires=86400&X-Amz-Signature=eb098fdb959995f10242ed691af1705a53ee80abcc52f75b2bcb45cca183673e&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22_2021-01-30__9.57.47.png%22">
    <img src="https://s3.us-west-2.amazonaws.com/secure.notion-static.com/25dc87f9-34aa-4fce-9a76-2865d373d874/_2021-01-30__10.10.25.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210519T120341Z&X-Amz-Expires=86400&X-Amz-Signature=031004a3098a85e5bbe94a04207fd51ad3851db5151cfe9630ca5ccc64a9fe66&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22_2021-01-30__10.10.25.png%22">

- 프로세스(총 3단계)
    1) **Mulitpart upload initiation**

        multipart 업로드 요청을 시작하게되면 S3에서 **업로드 ID**(multipart 업로드에 대한 unique 식별값)를 리턴해줌. 각 part를 업로드할 때 / part를 list할 때 / 업로드를 완료했을 때 / 업로드를 종료할 때 이 ID값을 담아서 보내줘야함. 업로드하려는 오브젝트의 metadata를 추가하고싶은 경우, 이 단계에 같이 추가해줘야함.
        
        <img src="https://s3.us-west-2.amazonaws.com/secure.notion-static.com/60c387c5-35ea-4eca-a03b-abe9f9a678aa/_2021-01-30__10.04.01.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210519T120401Z&X-Amz-Expires=86400&X-Amz-Signature=c7bc88797dd623546792ad09d6770f2e499e6a556b4d66a67d5ba936d728927b&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22_2021-01-30__10.04.01.png%22">
        업로드 ID 예시

    2) **Parts upload**

        part 업로드시 업로드 ID 뿐만아니라 part number도 명시해줘야함(1에서 10,000 사이).part number는 업로드하려는 오브젝트의 각 part를 구분하는 역할을 함. 각 part number는 연속적인 값일 필요는 없음. 이미 업로드한 part의 number와 똑같은 숫자로 업로드한 part가 있으면 해당 part는 덮어쓰기됨. 각 part를 업로드할 때마다, S3에서는 **ETag header**값을 리턴해줌. 각 part 업로드시마다, part number와 ETag 값을 따로 기록해놔야함. 이 값들을 다음 part 업로드할 때 같이 포함시켜서 업로드해야함.

        <img src="https://s3.us-west-2.amazonaws.com/secure.notion-static.com/2af0e933-82b3-480b-9e90-b7333cb2eb18/_2021-01-30__10.05.24.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210519T120446Z&X-Amz-Expires=86400&X-Amz-Signature=fc07d565ce4e9cc529b641e980ce3a45a674ce81d8d97f51a38458c27ed6d9d0&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22_2021-01-30__10.05.24.png%22">

    3) **Multipart upload completion**

        multipart 업로드가 완료되면 S3는 part number 기준으로 오름차순으로 각 part를 합쳐서 오브젝트로 만듬. 1단계에서 metadata가 추가된 경우, S3는 이 metadata를 오브젝트에 포함시킴. 이 과정이 완료되면 각 part는 지워짐. 이 단계의 요청에는 업로드 ID와 part number-ETag 값을 포함시켜야함. 이 요청에 대한 response로 오브젝트에 대한 ETag를 리턴해줌. multipart 업로드를 중지(stop)하게되면 해당 업로드의 업로드 ID로는 다른 part를 더이상 업로드할 수 없게됨.

        <img src="https://s3.us-west-2.amazonaws.com/secure.notion-static.com/3764732f-bb75-4411-a994-421d46ac5b36/_2021-01-30__10.06.37.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAT73L2G45O3KS52Y5%2F20210519%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20210519T120510Z&X-Amz-Expires=86400&X-Amz-Signature=774a22110bcd71b9860e2b897188d6ff7fa136c40348054ea4dd147990ed9b09&X-Amz-SignedHeaders=host&response-content-disposition=filename%20%3D%22_2021-01-30__10.06.37.png%22">

### 예시

[Spring boot :: Multipart upload API using Amazon S3 API 구현 과정 정리](https://wave1994.tistory.com/152)