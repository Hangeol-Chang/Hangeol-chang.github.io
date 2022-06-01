---
layout: page
title: Burn Protection Method
description: >
  detail description about Burn Protection Method
hide_description: true
sitemap: false
---



0. this unordered seed list will be replaced by toc as unordered list
{:toc}

# About

이 페이지는 **'사이버-물리 시스템을 이용한 영유아의 화상방지 알고리즘 및 스마트밴드'** 연구에 대해 논문에 등재된 연구 결과 및 작동 방식과, 논문에 등재되지 않은 연구 개발 과정에 대한 세부적인 사항을 포함하고 있습니다.



[논문링크]{:.heading.flip-title}

[논문링크]: https://www.dbpia.co.kr/journal/articleDetail?nodeId=NODE10609420



# Abstract

이 연구는 일상생활에서 영유아의 화상을 방지하기 위한 새로운 알고리즘을 제안한다. 최근 3년동안 일어난 화상사고의 26.9%가 4세 이하의 영유아에게서 발생하였고, 이중 65.9%가 집안에서 발생하였다. 따라서, 본 연구는 팔찌형 장치를 이용하여 아이가 열원에 접근하는지를 판단하며, 아이가 열원에 접근하여 화상을 당할 수 있는 상황이 발생하면, 첫 번째로 사이버-물리 시스템을 이용하여 열원과 먼 곳에서 발생되는 소리로 아이를 열원에서 멀어지게 하고, 두 번째로 보호자의 휴대폰으로 알람을 보내어 2중으로 자녀를 보호하도록 한다.



## 연구 목적

 영유아시기에 발생하는 화상 사고는, 영구적인 상흔을 남기는 사고가 많은 만큼 치명적이다. 이 화상을 방지할 수 있는 스마트밴드를 제작하고, 더 나아가 이 밴드에 사용된 화상방지 프로토콜을 공유함으로써, 다양한 환경에서 화상 방지 알고리즘이 사용될 수 있도록 한다.



## 기술 설명

### 화상방지 알고리즘

#### 기본 기술 개발

##### 개요

 화상 방지를 위해 다양한 기술의 사용을 고민하였다.<br>
라이다 센서, 초음파 센서 + 열화상 센서의 조합 등의 고민을 하였지만, 이 프로젝트의 목적 중 하나는 '상품성'이었고, 이를 위해 생산 단가를 최대한 낮춰야 했다. 결론적으로 열화상카메라만 사용하는 것으로 결정하였고, 열화상 카메라를 이용하여 열원의 접근 및 후퇴, 지나침을 판단할 알고리즘을 구현해야 했다.



##### 개발 과정

우선, 우리가 사용하는 mlx90640은 32*24해상도의 온도 배열을 제공한다. 이를 바탕으로 일정 온도 이상을 열원으로 구분하고, 열원의 위치에 1(A), 열원의 주변부에 5(b), 기타 부위에 0을 넣은 배열로 반환하도록 하였다. 이를 위해 mlx90640 제작처에서 제공한 라이브러리를 수정할 필요가 있었다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Burn_Protection_Method/getThermalImage.png?raw=true" width="600">
</center>

###### 라이브러리 픽스

기존의 mlx90640은 열원의 배열을 얻어올 때, 32*24 배열의 전체를 얻어오는 것이 아닌, 체스판 형태나, 줄무늬 형태로 한 번에 전체 배열의 반을 가져오고, 다음 탐색에서 나머지 반을 가져오게 된다. 하지만, 이는 이 연구에서 열원의 접근을 감지할 때 데이터를 처리하기 어렵게 하고, 오작동을 유발하므로, 이를 한 번의 탐색에 모든 데이터를 가져오도록 수정하였다.

<center>
    <img src="https://www.waveshare.com/w/upload/0/00/MLX90640_Thermal_Camera_manual-4.png" width="500">
</center>

열원의 접근 감지는 아래의 방법으로 이루어진다.<br>

열원을 1(A)로, 열원과 인접한 셀을 5(b)로 변환하여 ‘이동예측범위’를 지정한다. 그 후, 나머지 관련 없는 셀들을 모두 0으로 변환한다. 즉, 배경(0)으로부터 열원(A)을 경계(b)로서 객체분할한 것이다. 예를 들어, 아래의 그림에서와 같이, 뜨거운 냄비를 감지한 50도 이상의 셀 8개는 A로, 이들과 인접한 셀은 b로, 나머지는 0으로 변환된다. 

<br>

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Burn_Protection_Method/changeArray.png?raw=true" width="600">
</center>

 각 탐색마다 해당 과정을 반복하고, 이전 온도배열와 비교하여 배열의 각 자리 셀들이 어떻게 바뀌었는지를 파악하여 A에서 b로, b에서 A로 변화된 셀 수를 카운트한다. 현재 루프 배열 − 이전 루프 배열을 계산했을 때, 배열 각 자리의 결과값을 이용하여 A와 b가 어떻게 바뀌었는지 파악하는 방식으로 진행한다. 위의 그림 에서와 같이, 현재 루프 배열에서 이전 루프 배열을 뺏을 때, ‘A(1) -> b(5)’이면 5 – 1 = 4, ‘b(5) -> A(1)’이면 1 – 5 = –4의 결과가 계산된다. 



###### 배열 인덱싱

데이터 가공을 위해, 1차원으로 들어오는 온도 배열을 32 * 24로 변환해야 했으며, 주변부에 5를 체크하기 위해 이 데이터를 34 * 26 크기의 배열의 중앙에 입력해야 했다. 32 * 24 배열을 만든 뒤 옮기면 간단하지만, 한정된 아두이노의 메모리를 최대한 절약하기 위해 직접 34 * 26 (884크기의 1차원 배열)크기의 배열에 데이터를 입력하였다. 아래는 이와 관련된 코드이다.

```c++
for( int pixelNumber = 0; pixelNumber < 768; pixelNumber++)
    {
        {
            /*
            온도 전처리 과정 생략.
            To가 온도 변수
            */

            if (To < dngtemp) {
              if     (result[loc - 1 ] == 1 ) { result[loc] = 3; countb++; }
              else if(result[loc - 34] == 1 ) { result[loc] = 3; countb++; }
              else                            { result[loc] = 0; }
            }
            else {
              countA ++;
              if(result[loc] == 3) countb --;
              result[loc] = 1;

              coordinatetmploc[pointer] = (pixelNumber + 1) % 32;
              pointer++;
              
              if(result[loc - 34] == 0 ) { result[loc - 34] = 3; countb++; }
              if(result[loc - 1 ] == 0 ) { result[loc - 1 ] = 3; countb++; }
            } 

            if((pixelNumber + 1) % 32 == 0) { 
              if(pointer != 0) {
                int tmpsum = 0;
                for (int t = 0; t < pointer; t++){
                  tmpsum += coordinatetmploc[t];
                }
                // 각 열에서 열원 위치의 합
                coordinatetmp[row][0] = tmpsum;
                // 각 열에서 열원의 개수
                coordinatetmp[row][1] = pointer;
              }
              else {
                coordinatetmp[row][0] = 0;
                coordinatetmp[row][1] = 0;
              }
              row++; pointer = 0;
            }
        }
    }
```

 

#### 위험 판단 기준.

위에서 계산한 값들을 이용하여, 착용자가 열원에 접근하는지를 판단하기 위해 아래와 같이 입실론을 정의한다.<br>
$$
\epsilon = \frac{b의 A로 변화량(-4)}{A의 b로 변화량(4) + 1}
$$
<br>

 판별식은 이전 루프에 비교하여 열원(A)에서 경계(b)로, 경계(b)에서 열원(A)으로 바뀐 셀 수의 비를 나타내는 식이며, 분모의 +1은 ‘A -> b’ 값이 0이 되어 분모가 0이 되는 경우를 방지하기 위하여 추가된 상수이다.<br>

위 식을 통하여, 입실론의 범위에 따라 아래의 표와 같이 열원의 접근, 후퇴 밑 지나침을 판단할 수 있다.

| 입실론 범위     | 물체의 이동 양상 |
| --------------- | ---------------- |
| 0 이상 0.5 미만 | 멀어짐           |
| 0.5 이상 1 미만 | 지나침           |
| 1 이상          | 접근             |

이 입실론 범위 경계값들은 100회 이상의 실험을 통해 경험적으로 얻어냈다.<br>

추가로 설명하자면, 열원이 멀어지는 경우에는 열원을 인식한 셀의 수가 감소하므로, ‘b -> A 변화량’보다 ‘A -> b 변화량’이 더 커짐에 따라 값이 작아진다. 따라서,  입실론 값이 0에 가까워지는 것을 확인할 수 있었다. 열원이 접근하는 경우에는 열원을 인식한 셀의 수가 증가하므로, ‘b -> A 변화량’이 ‘A -> b 변화량’에 비해 커짐에 따라  입실론 값이 커지고, 실험결과 1 이상의 값들이 계산되는 것을 확인하였다. 마지막으로 열원이 스마트밴드와 평행하게 지나쳐가는 경우에는 열원을 인식한 셀의 수에는 변동이 크지 않으므로, 입실론 값이 1 미만의 값에 수렴하는 것을 확인하였다.<br>

하지만, 한 번의 입실론 측정으로 위험을 판단하면, 불필요한 상황에도 경고가 울리는 등 오류가 잦게 발생하였다. 이 오작동을 방지하기 위해 연속된 세 개의 온도 배열을 이용하여 입실론1, 입실론2를 구한 후, 두 입실론이 모두 1보다 크거나 같을 때 위험을 판단하게 하였다.



#### 왜곡 보정

 위의 방법으로 열원의 접근을 판단할 시, 열원의 형태에 따라 접근시에 epsilon이 달라지는 현상이 나타난다. 이는 같은 크기의 물체라도 그 형태에 따라 열원을 감지한 셀의 수와, 주변부를 감지한 셀의 수의 비가 차이가 나기 때문이다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Burn_Protection_Method/distort.png?raw=true" width="600">
</center>

위의 그림에서 열원은 모두 12칸의 셀을 차지하지만, 주변부는 각각 14, 18, 24셀을 차지하는 것을 볼 수 있다. 각각의 물체가 동일하게 이동하여도, A->b 셀의 수와, b->A 셀의 수가 차이가 나기 때문에 화상방지 알고리즘은 각기 다른 판단을 하게 된다. 기존의 알고리즘은 '정사각 형태의 열원'을 기준으로 설계되었기에, 정사각 형태에서 벗어나는 열원에 대해 '왜곡'이라고 지칭하고, 왜곡된 열원에 대한 보정을 진행하였다.



<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Burn_Protection_Method/epsilon_correction_graph.png?raw=true" width="600">
</center>

위의 그래프는, A에 대한 b의 수를 시각화 한 것이다. 그래프에서 검은 영역은 측정되지 않는 영역, 초록색은 정사각형으로 판단할 수 있는 영역, 빨간색은 약간의 왜곡이 있는 영역, 그 바깥은 많은 왜곡이 있는 영역으로 구분하였다. 각각의 그래프는 길험적으로 얻은 값들을 연결하여 curve fitting을 진행하여 도출하였고 식들은 아래와 같다.

<br>
$$
비왜곡 지역 : 4* \sqrt{A} <= b < 7 * A^{\frac{13}{30}}
$$
<br>
$$
저왜곡 지역 : 7 * A^{\frac{13}{30}} <= b < 10 * A^{\frac{2}{5}}
$$
<br>
$$
고왜곡 지역 : 10 * A^{\frac{2}{5}} <= b
$$


각각의 범위에 대해, 아래와 같이 보정계수 c를 도출한다.

<br>
$$
저왜곡 보정계수 : c = 0.25 * (\frac{b}{A})^{0.25}
$$
<br>
$$
고왜곡 보정계수 : c = 0.3 * (\frac{b}{A})^{0.33}
$$
구해진 보정계수를 이용하여, epsilon 도출식을 아래와 같이 수정하였다.

<br>
$$
\epsilon = \frac{b의 A로 변화량(접근경향성)}{A의 b로 변화량(후퇴경향성) * (1+c) + 1}
$$


위 식을 알고리즘에 적용하여 테스트를 진행하였고, 결과는 아래의 표와 같이, 3m 이내의 거리에서 95%의 정확도로 열원의 접근을 감지할 수 있었다.

<center>
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Burn_Protection_Method/detectTest.png?raw=true" width="600">
</center>

```c++
if(countA >= 1){
    coordinate[2] = countA;
    corfac = (float)countb / (float)countA;
    
    // 비왜곡형태
    if( 7*pow(countA,0.43) > corfac >= 4*pow(countA,0.5) ) { 
        corfac = 0; 
    }
    // 저왜곡형태
    else if( 10*pow(countA,0.4) > corfac ) { 
        corfac = (corfac,0.25)*0.25; 
    }
    // 고왜곡형태
    else { 
        corfac = pow(corfac,0.33)*0.3;  
    }
}else { 
    corfac = 0; 
    coordinate[2] = 0; 
}
```



#### 열화상 카메라 배치

mlx90640은 110*60의 화각을 가지고 있으며, 이를 두 개 이용하여, 사용자의 최대한 많은 활동범위의 열원을 감지해야 한다.

<center>
<img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Burn_Protection_Method/cameraRange.png?raw=true" width="600">
</center>

이를 위해 일상 생활을 시뮬레이션하며 두 카메라의 위치를 선정하였다. 영유아의 경우, 기어다니는 경우와 걸어다니는 경우를 모두 고려해야 하므로,

- 기어다닐 때 정면이 되는 **손등 방향**
- 팔을 뻗을 때 정면이 되는 **팔의 축 방향**

을 기준으로 세부적인 위치를 선정하였다.



### 통신

스마트밴드는 UDP 통신을 이용하여 주변기기와 통신한다. 스마트밴드가 주변에 위험 신호를 알리기 위해, 인터넷 통신을 시도할 필요가 있으나, 별도의 서버를 운영할 수 없기 때문에 빠른 속도로 간단하게 데이터를 전송할 수 있는 UDP 방식을 활용하였다.

스마트밴드에서 위험이 감지되었을 때, 연결되어있는 공유기로 신호를 전송한 후, 공유기에서 연결된 모든 주소로 위험 신호를 전달한다. (개발 당시에 웹에 대해 처음으로 공부를 하였었고, HttpStatus 등에 무지하였기 때문에 그저 200이라고 데이터를 송출하였다.) 기본적으로 가정에 설치하는 스피커에서 신호를 받아 알림을 송출하며, 보호자의 휴대폰이 연결되어 있다면, 이를 통해서도 알림을 전송한다.

UDP의 전송 신뢰도 문제를 해결하기 위해, 한 번 위험이 감지되었을 때 신호를 수백번 송출하도록 하였다.



```c++
void loop(void) {
  switch (stat){
    // 위험판단 실행
    case 0:
      readTempValues();
      break;

    // 위험 발생. 위험신호(ON), 안전신호(OFF)
    case 1:         
    
      // wifi
      String s = "HTTP/1.1 200 OK\r\nContent-Type: text/html\r\n\r\n<!DOCTYPE HTML>\r\n<html>\r\n"; 
      s += (ing)? "OFF": "ON";      // vall의 값이 참이면('0'아닌 값은 모두 참) 'ON'저장, 거짓이면'OFF'저장
      s += "</html>\n"; 
      for (int i = 0; i < 1000; i++) { client.print(s); }
      delay(1);

      //연결 끊기
      client.stop();                                        
      Serial.println("disconnect");
          
      //재연결
      while (!client) { client = server.available(); }      
      while (!client.available()){ delay(1); }
      Serial.println("new client");
      
      delay(1);
      if (ing) ing = false;
      else     ing = true ;

      stat = 0;
      break;
  }
}
```



MIT App Inventor를 이용해 개발한 어플의 블록모습
<center>
<img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Burn_Protection_Method/appblock.png?raw=true" width="800">
</center>






### 배터리 최적화

> 이 프로젝트를 완성하는 데에 가장 큰 걸림돌이 되었던 부분은 단연 배터리이다.
> 이 프로젝트가 가진 두가지 조건 (영유아가 사용, 두 개의 열화상 카메라 사용) 을 만족하면서, 사용자가 불편하지 않을 수준이 배터리타임을 만들어내야 했다.

초기 버전의 코드를 적용시킨 스마트밴드의 베터리타임은 3시간이 체 되지 못했다. 두 개의 mlx90640이 2Hz로 작동하며 전력을 소비했고, 이 전력을 줄일 필요가 있었다.

mlx90640은 자체적으로 0.5~64Hz의 동작 속도를 설정할 수 있으나, 우리는 더 상세하게 동작시간을 조정하기 위해

<center>
<img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Burn_Protection_Method/battery1.png?raw=true" width="300">
    <img src="https://github.com/Hangeol-Chang/Hangeol-chang.github.io/blob/main/assets/img/portfolio/Burn_Protection_Method/battery2.png?raw=true" width="300">
</center>



### 기구부 설계

#### 재료 선정

영유아가 착용하고 활동하는 데에 지장을 주지 않아야 하므로, 가볍고 작게 제작하며, 



#### 하네스 와이어링





## 연구 성과

- ICROS 2020 학회 논문 등재
- 경희대학교 창의적 종합설계 경진대회 3등상 수상
- 성균관대 주관 CONNECT 창의연구 2등상 수상
