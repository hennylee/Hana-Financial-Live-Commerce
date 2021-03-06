출처 : [인터넷 라이브 방송은 어떤 기술로 만들어질까요?](https://medium.com/naver-cloud-platform/%EC%9D%B8%ED%84%B0%EB%84%B7-%EB%9D%BC%EC%9D%B4%EB%B8%8C-%EB%B0%A9%EC%86%A1%EC%9D%80-%EC%96%B4%EB%96%A4-%EA%B8%B0%EC%88%A0%EB%A1%9C-%EB%A7%8C%EB%93%A4%EC%96%B4%EC%A7%88%EA%B9%8C%EC%9A%94-98423dc7fcd4)


## 1. 원본 영상이 시청자들의 동영상 플레이어까지 전달되는 흐름

![image](https://user-images.githubusercontent.com/77392444/124441664-da3ab900-ddb6-11eb-8607-ab6ad3149d34.png)



## 2. 라이브 인코더

#### 라이브 인코더의 역할
- 라이브 인코더는 원본 영상을 정해진 방식으로 압축(인코딩) 하고, 압축한 컨텐츠를 미디어 서버로 전송하는 역할을 함

#### 송출이란?
- 인코더에서 미디어 서버로 컨텐츠를 전송하는 것을 보통 `송출`이라고 말합니다.

### 2.1 인코더의 종류

- PC에 설치해서 사용할 수 있는 `소프트웨어 인코더`
- 박스 형태의 `하드웨어 인코더`
- 촬영하면서 송출까지 할 수 있는 `모바일 앱 인코더`
- 오픈소스인 `ffmpeg` 을 이용하여 구현된 간단한 인코더


### 2.2 압축(인코딩)

카메라로 촬영한 원본 영상을 그대로 미디어 서버에 전송하기에는 데이터 용량이 너무 크기 때문에 압축하는 과정이 반드시 필요하다. 
또한 미디어 서버에서 해석할 수 있는 압축 방식(코덱, Codec)을 사용해야 한다. 

- 인터넷 방송에는 H.264/AAC 코덱이 주로 쓰이며, 최근에는 더욱 압축 효율이 좋은 H.265 코덱을 사용하는 경우도 늘어나고 있다.


인코딩은 CPU를 많이 쓰는 작업이므로, 인코딩 작업에 평균 CPU 사용률이 80% 를 넘을 경우 출력 영상의 품질이 나빠질 수 있다.

- 만약 CPU 부하로 인해 송출 중인 영상이 계속 끊기거나 버벅인다면 출력 해상도나 비트레이트를 줄이는 것이 좋다. 

- 소프트웨어 인코더를 사용할 경우 안정적인 방송을 위해서 쿼드코어 CPU 가 탑재된 PC 사용을 권장한다.


### 2.3 송출

미디어 서버로 영상을 보낼 때는 인터넷 스트리밍에 최적화된 `RTMP 프로토콜`을 이용한다. 

- 과거에는 UDP 기반의 `RTSP 프로토콜`을 많이 사용했으나 최근에는 RTMP 프로토콜이 거의 표준처럼 사용되고 있다. 

안정적인 송출을 위해서는 충분한 인터넷 업로드 대역폭이 확보되어야 한다.

가정이나 일반 사업장에서 사용하는 인터넷 품질은 상황에 따라 가변적일 수 있으므로, 절대 끊어지면 안 되는 중요한 방송이라면 별도 전용 회선을 설치하여 송출하는 것을 권장한다. 

- 보통 인터넷 업로드 대역폭은 출력 비트레이트의 두 배 이상 확보되는 것이 안정적이며, 
- 만약 송출 중인 영상이 계속 끊기거나 버벅일 경우 출력 해상도와 비트레이트를 낮춰주면 출력 품질 개선에 도움이 될 수 있다.




## 3. 미디어 서버

#### 미디어 서버의 역할
- 트랜스 코딩 : 인코더가 보내준 영상을 여러 가지 화질 및 비트레이트로 변환
- 트랜스먹싱(혹은 패킷타이징) : 화질별로 변환된 영상을 다시 HLS 형식으로 변환 


### 3.1 화질 및 비트레이트 변환

미디어 서버에서 원본 영상을 여러 화질로 변환해 주어야 한다.

- 인터넷 방송 서비스는 사용자의 다양한 디바이스 크기, 네트워크 환경에 맞는 영상을 시청하도록 한다면,

- 불필요한 데이터 사용과 배터리 소모를 방지하고 최적의 화질을 보장할 수 있기 때문이다. 


이 작업은 앞서 설명한 라이브 인코더와 동일하게 CPU를 많이 사용한다. 

- 이유는 라이브 인코더가 압축해서 보내준 영상을 압축 해제(`디코딩`) 하고 화질 변환 후 다시 압축(`인코딩`) 하기 때문이다. 

- 인코딩 작업에 GPU 가속을 이용하면 CPU 의 인코딩 부하를 상당량 줄일 수 있다. 



### 3.2 HLS 변환

`HLS`(HTTP Live Streaming)는 전 세계에서 가장 널리 사용하고 있는 실질적인 표준 HTTP 스트리밍 프로토콜이다. 

`HLS`는 M3U8 확장자의 재생목록 파일과 영상을 잘게 쪼갠 `TS 청크 파일`들을 전송하며, 이를 통해 모바일과 PC 등 다양한 디바이스에서 영상을 재생할 수 있다.

카메라가 촬영하는 시간과 그 영상이 시청자에게 표시되는 시간의 차이를 지연(latency) 라고 한다. 

- 시청자와 실시간 소통이 필요할수록 짧은 지연(low latency) 이 필요하다. 

과거에는 10초~15초 분량의 TS 청크를 재생 목록에 3개씩 담아 사용하는 것이 일반적이었다. 

- 미디어 서버 입장에서 30초~45초 정도 분량을 버퍼에 담아두었다가 전달하는 셈이다. 

- 따라서 사용자 입장에서는 전송에 소요되는 시간까지 포함하여 최소 30초, 최대 1분에 가까운 지연이 발생한다. 

버퍼가 클수록 재생이 안정적이지만 지연 시간은 길어진다. 

반대로 버퍼를 짧게 가져가면 지연은 적지만 네트워크 상황에 민감해져 재생 품질이 불안정해질 수 있다.

최근에는 네트워크 환경이 좋아져 보다 공격적으로 튜닝하여 사용하는 경우가 많다. 

1~2초 정도의 TS 청크를 사용한다면 지연 시간을 10초 미만으로 줄일 수 있어 소위 말하는 low latency를 구현할 수 있다. 



### 3.3 미디어 서버 솔루션

#### Wowza Media Server (유료)
유료이지만 다양한 기능과 우수한 인코딩 성능으로 널리 사용되고 있다. 

안정적인 운영을 위해 평균 CPU 사용률이 60% 를 초과하지 않도록 관리해 주는 것이 중요하다. 

720P HD 화질을 포함하여 3~4 종류의 품질로 트랜스코딩을 해야 한다면 쿼드코어는 약간 불안하고, 그 이상의 코어를 탑재한 CPU를 사용하는 것이 안정적입니다.


#### Nginx-rtmp (오픈소스)
Nginx-rtmp 는 nginx 의 모듈로 동작하는 미디어 스트리밍 서버이다. 

RTMP 소스를 Pull 혹은 Push 방식으로 입력받을 수 있고 RTMP/HLS/MPEG-DASH 출력을 지원한다. 

여기에 ffmpeg 을 연동하면 트랜스코딩을 비롯하여 기타 다양한 기능들을 구현할 수 있어 유료 솔루션의 좋은 대안이 될 수 있다.



## 4. 전송 서버 (CDN)

미디어 서버에서 실시간으로 생성한 HLS 영상 조각 파일을 사용자에게 전달하려면 전송 서버가 있어야 한다. 

일반적인 미디어 서버는 전송 서버의 역할까지 수행하지만 동시 시청수가 많은 방송일 경우 대규모 트래픽을 안정적으로 처리하기 위해 CDN 을 사용하는 것이 좋다.

- CDN에서 캐싱을 하지 않으면 동시에 많은 시청자가 접속했을 때 미디어 원본 서버로 많은 요청이 들어갈 수 있기 때문이다.

하지만 무작정 캐싱 기간을 늘려놓아도 안된다.

- 최근에는 영상의 지연을 최소화하기 위해 TS 청크의 길이를 수초 단위로 사용하는 경우가 많다. 

- 이때 HLS 재생목록 파일이 TS 청크의 재생 시간 내에 갱신되지 않으면 버퍼링이 발생하거나 플레이어가 영상 재생을 중단할 수 있다. 

따라서 재생 목록 파일은 TS 청크의 절반 정도 시간만 캐싱하고 갱신되도록 설정해 두는 것이 좋다.

아울러 컨텐츠 보호 차원에서 CDN 캐시 남아있는 과거 영상을 다운받는 것을 방지하기 위해 정해진 시간 이후에 들어온 요청은 캐시가 되어 있더라도 컨텐츠를 응답하지 않도록 설정해두는 것이 필요하다.




## 5. 동영상 플레이어

동영상 플레이어는 말 그대로 사용자 단말에서 영상 컨텐츠를 재생하는 역할을 한다. 

미디어 서버에서 최종적으로 만든 HLS 형식은 
- 모바일 단말이라면 안드로이드, 아이폰 구분 없이 내장된 플레이어를 통해 문제없이 재생할 수 있다. 

- 컴퓨터 단말이라면 HLS 가 Apple에서 만든 방식인 만큼 맥 OS에서도 재생을 지원하지만, Windows PC에서 HLS를 재생하려면 별도의 외부 플레이어가 필요하다. 

- Chrome이나 Edge 같이 MSE (Media Source Extensions)를 지원하는 최신 브라우저라면 Javascript 로 구현된 플레이어를 사용할 수 있다. 

  - 대표적으로 hls.js, video.js 등이 있다. 

  - 윈도우의 버전에 따라 다르지만 IE 11 이하에서는 HLS 재생을 지원하는 Flash 플레이어를 사용해야 한다.

동영상 플레이어를 사용할 때 브라우저의 보안적 제약으로 재생이 불가능한 상황이 발생할 수 있다.

- 서비스 페이지의 도메인과 영상이 전송되는 도메인이 다르면 재생이 되지 않는다. 

- 이를 위해 전송 서버나 미디어 서버에서 적절한 크로스 도메인 설정을 해주어야 한다. 

또한 HTTPS 페이지 내에서 HTTP 프로토콜로 전송되는 영상을 재생을 하려고 할 때도 마찬가지로 재생이 되지 않을 수 있으니 영상도 HTTPS 프로토콜로 전송하는 것이 필요하다.
