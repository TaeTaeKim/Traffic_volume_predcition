# 고속도로 통행량 예측 Task
* 35개 도로의 시간별 물류 통행량 데이터를 학습, 이를 기반으로 기준시점 이후 물류 통행량을 예측하는 과제
## 목차
1. **EDA, 전처리**
2. **SARIMA**
3. **DeepAR**

## Data Processing
### Data EDA
* 35개의 고속도로별 통행량 1월1일부터 5월 23일까지 통행량
* 대체로 2월달 설날로 인해 통행량이 많고 3월에 이상한 결측치가 존재

![트래픽전처리전](https://user-images.githubusercontent.com/70123707/156531981-e1d18d35-b6b4-4bb9-a298-0bd48d342dae.png)

* 35개 고속도로 시간대별 통행량
* 모든 도로에서 시간대별로 비슷한 추이를 보인다.
![시간별그래프](https://user-images.githubusercontent.com/70123707/156532373-b7440909-e548-4375-ad01-336e57f54c9e.png)
### Data 전처리 1 : 3월달 결측치 대체
* 3월과 2월에 측정되지 않은 시간이 있고 그로인한 결측치발생
* 해당 일 전 2주와 후 2주의 통행량의 평균으로 값을 대체
![전처리후](https://user-images.githubusercontent.com/70123707/156532603-0a8713c4-631f-4bea-a910-951e3225b5ec.png)
### Data 전처리 2 : 설날 통행량 대체
* 예측이 필요한 5월의 경우 명절이 없는 달로써 설날처럼 통행량이 급변할 일이 없을 것으로 설날 값을 대체
![설날평준화](https://user-images.githubusercontent.com/70123707/156532832-a2222c3c-5183-4b68-81e3-8dd0a3257c75.png)

## SARIMA
* code 10번 고속도로의 ACF, PACF분석
* Sktime의 AutoARIMA를 이용한 모델 fitting
```
forcaster = AutoARIMA(start_p=7,max_p=16,suppress_warnings=True)
forcaster.fit(train10)
forcaster.summary()
```
![10](https://user-images.githubusercontent.com/70123707/156535917-16d749c6-10af-439e-988e-31d56a7a3ec7.png)
![화면 캡처 2022-03-03 183019](https://user-images.githubusercontent.com/70123707/156536493-de8d4a47-3c91-4273-a1d6-ce5f99e09c70.png)


![sarima](https://user-images.githubusercontent.com/70123707/156543702-dce4bc5c-f52c-4405-b2e5-dddb5831a153.png)
* SARIMA는 큰 RSME를 보여서 다른 고속도로 모델링은 진행하지 않고 Deep AR로 진행

## Deep AR
* gluonts라이브러리를 이용한 Deep AR 구현
* context_lenth(학습에 들어갈 데이터범위)는 2주로 설정
* 도로별로 개별 모델링
* 10번 도로 Validation
![image](https://user-images.githubusercontent.com/70123707/156546198-ee0c7407-573a-4b5c-8be5-1abdc57be005.png)
* 모델로 모든 도로 예측 후 RMSE : 19810.50
* LSTM 5 layer로 변경 후 RMSE : 19228.56
## 결론
* Deep AR모델 역시 좋은 성능을 보이지 못함. Leader Board 1등 : 3501.93
* 팀원의 N Beat나 Prophet이 좋은 성능을 보였다 약 4900, 5600
* n beat와 prophet에 대한 공부필요
