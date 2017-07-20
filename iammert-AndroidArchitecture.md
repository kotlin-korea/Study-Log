# 사전 작업

## 1. Resouce 빌드 실패

### 문제

해당 프로젝트 빌드시 아래와 같은 파일이 없는 것으로 나옵니다

- ic_language_green_24dp
- ic_stats_green_24dp

### 쉽게 해결방안

1. https://github.com/konifar/android-material-design-icon-generator-plugin 를 설치한다
2. 사용 컬러를 `#00E175` 으로 설정
3. 24dp 와 적당한 이름으로 변경한다
4. 끝

![default](https://user-images.githubusercontent.com/1534926/28421392-07250eb0-6da0-11e7-823b-2ac868a56e39.PNG)

## 2. API 키 누락으로 NPE 발생

### 문제

현재 프로젝트에는 API가 누락되어 있는 상태로 아래와 같은 에러 발생

```
FATAL EXCEPTION: AsyncTask #1
Process: iammert.com.androidarchitecture, PID: 10988
java.lang.RuntimeException: An error occurred while executing doInBackground()
    at android.os.AsyncTask$3.done(AsyncTask.java:325)
    at java.util.concurrent.FutureTask.finishCompletion(FutureTask.java:354)
    at java.util.concurrent.FutureTask.setException(FutureTask.java:223)
    at java.util.concurrent.FutureTask.run(FutureTask.java:242)
    at android.os.AsyncTask$SerialExecutor$1.run(AsyncTask.java:243)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1133)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:607)
    at java.lang.Thread.run(Thread.java:761)
Caused by: java.lang.NullPointerException: Attempt to invoke virtual method 'java.util.List iammert.com.androidarchitecture.data.remote.model.MoviesResponse.getResults()' on a null object reference
    at iammert.com.androidarchitecture.data.MovieRepository$1.saveCallResult(MovieRepository.java:36)
    at iammert.com.androidarchitecture.data.MovieRepository$1.saveCallResult(MovieRepository.java:32)
    at iammert.com.androidarchitecture.data.NetworkBoundResource$2.doInBackground(NetworkBoundResource.java:71)
    at iammert.com.androidarchitecture.data.NetworkBoundResource$2.doInBackground(NetworkBoundResource.java:67)
    at android.os.AsyncTask$2.call(AsyncTask.java:305)
    at java.util.concurrent.FutureTask.run(FutureTask.java:237)
    at android.os.AsyncTask$SerialExecutor$1.run(AsyncTask.java:243) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1133) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:607) 
```

### 해결 방안

- iammert.com.androidarchitecture.data.remote.ApiConstants.java 파일 연다
- API_KEY 항목에 `8476a7ab80ad76f0936744df0430e67c` 입력 (https://github.com/chonamdoo 님이 발급하셨어요)
