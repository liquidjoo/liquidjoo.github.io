---
title: 멀티쓰레드와 비동기 통신 사용 후기
date: "2019-04-05T19:27:00.121Z"
layout: post
draft: false
path: "/posts/review-threadpool-and-okhttp3/"
category: "Dev"
description: "비동기 처리를 위해 멀티쓰레드만 사용한 필자의 후기"
---

#### 멀티쓰레드가 이상하다?
이전에 spring cloud를 사용해 특정 작업을 스케줄링하는 개발을 했었는데 같은 로직으로 HttpConnection을 맺으니 갑자기 속도가 느려지며 GC Overhead가 발생!!

#### 문제가 뭐지..
- 가비지 컬렉션 오버헤드가 발생해서 당연히 메모리 부족인 줄.. jvm의 메모리를 올려봤으나 차이가 없다

- 로직의 문제인가 싶어서 ```synchronized ()``` 를 알아봤으나 이 문제는 아니라고 판단 (데이터 자원을 보존하기 위한 보호가 로직에 필요 없다고 판단!)

#### 찾자 일단 하나씩..
- 한줄 한줄 디버깅해가면서 찾아보니 HttpURLConnection 요놈에서 뭔가 문제가 있는 듯 하다!

```java
public String callApi(String query, String method) {
        String response = null;
        HttpURLConnection con = null;
        try {
            URL url = new URL(query);
            con = (HttpURLConnection) url.openConnection();
            con.setRequestMethod(method);
            con.setRequestProperty("X-Requested-With", "Curl");

            InputStreamReader inputStreamReader = new InputStreamReader(con.getInputStream());
            BufferedReader bufferedReader = new BufferedReader(inputStreamReader);
            ...
            ....

            inputStreamReader.close();
            bufferedReader.close();

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (con != null) {
                con.disconnect();
            }
        }
```

- 외부 Api호출에 있어서 단순히 HttpUrlConnection 객체를 사용하고 있었는데!! 이 친구에서 계속 뻑이 난다

#### 고군분투
- 자원해제, 커넥션 객체 할당 해제를 잘 해준 것 같은데도 어느정도 시간이 지나면 GC OverHead 

- 멀티쓰레드를 사용하고 있고 슬립 시간이 거의 없이 계속 많은 양의 데이터를 받는데 할당하고 해제를 계속 하다가 뻑이 나는건지..사실 아직 잘 모르겠다.

- 현재 느낀점은 들어오는 데이터 양에 비해 생각보다 많은 양의 커넥션을 맺고 있고 해제하는 속도보다 객체를 할당하는 부분이 더 많아서 GC(가비지 컬렉션)에서 에러를 뿜는 것 같다.


#### 그럼?
- 코드의 메모리 누수(memory leaks)를 찾아야 한다.

- 힙 메모리를 많이 차지하는 객체가 무엇인지, 어떤 코드인지 찾자

- 우선 다른 일정도 많이 밀려있기에 HttpURLCollection 객체를 사용해서 만든 로직을 폐기..

- 로직이 멀티쓰레드로 동작한다면 로직내의 Http 통신 조차 비동기로 하면 된다고 생각!

- okhttp3 라이브러리 사용!! (비동기 통신)

- 음.. github에서 오픈소스인 facebook sdk를 뜯어서 필요한 것만 추출해서 개발

- 일단 카피카피! 시작

#### 새롭게 만들어진 로직?
```java
public static ListenableFuture<ResponseWrapper> invoke(okhttp3.OkHttpClient client, okhttp3.Request request) {
            final SettableFuture<ResponseWrapper> future = SettableFuture.create();
            client.newCall(request).enqueue(
                    new okhttp3.Callback() {

                        @Override
                        public void onFailure(Call call, IOException e) {
                            future.setException(
                                    new IOException(e)
                            );
                        }

                        @Override
                        public void onResponse(Call call, Response response) throws IOException {
                            future.set(new ResponseWrapper(response.body().string(), convertToString(response.headers())));
                        }
                    }
            );
            return future;
        }

```

#### 잘되네?
- 우선 위의 http call 메소드를 okhttp3로 변경하여 비동기로 GET 하니 잘 된다!

- 일단 HttpURLConnection 객체보단 okhttp3의 내부를 봐야겠다
```java
// 호출 부분
// web socket이 false 처리? 왜징
@Override public Call newCall(Request request) {
    return RealCall.newRealCall(this, request, false /* for web socket */);
  }

// 생성자
private RealCall(OkHttpClient client, Request originalRequest, boolean forWebSocket) {
    this.client = client;
    this.originalRequest = originalRequest;
    this.forWebSocket = forWebSocket;
    this.retryAndFollowUpInterceptor = new RetryAndFollowUpInterceptor(client, forWebSocket);
  }

// 실질적인 로직

static RealCall newRealCall(OkHttpClient client, Request originalRequest, boolean forWebSocket) {
    // Safely publish the Call instance to the EventListener.
    RealCall call = new RealCall(client, originalRequest, forWebSocket);
    call.eventListener = client.eventListenerFactory().create(call);
    return call;
  }
```

- okhttp3는 이벤트 리스너이고, 이 클래스를 활용해서 HTTP 호출 수량, 크기 및 기간을 모니터링 및 작업을 할 수 있다.

- 문서를 보니 파일이나 네트워크에 대한 IO 쓰기 작업은 비동기적으로 실행이 되어야 한다는데.. 내가 호출전에 처리한 비동기 처리가 이 처리를 뜻하는지는 아직 잘 모르겠다.

- 문서링크: https://square.github.io/okhttp/3.x/okhttp/okhttp3/EventListener.html




```
한 마디 - 시간적 여유를 내서 디테일하게 꼭 다시 정리해야지
```