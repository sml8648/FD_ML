## 머신러닝을 활용한 이상금융거래 탐지 (ver.1)

현재 사내의 이상금융거래탐지 시스템(FDS)을 운영하고 있지만 룰베이스를 기반으로 운영을 하고 있으며 이에따라 알려지지 않은 이상금융거래(보이스피싱 등) 데이터에 대해서는 탐지율이 높지 않음. 따라서 이상금융거래와 관련된 핵심적인 특성들을 추출하여 머신러닝을 이용하여 학습 후 이상금융거래를 판단하는 모델을 만들고자 함

## 프로젝트 시작 동기 및 목표
* 당사는 카드회사는 아니지만 이상금융거래를 판단하는 AI 모델을 만드는 문제와 관련하여 '신용카드 사기 탐지' 문제와 유사하게 접근할 수 있다는 사실을 발견함. 따라서 Kaggle의 CCFD 데이터 세트와 솔루션들을 참고하여 최대한 유사하게 당사 데이터를 전처리하고 유사한 모델을 적용해보고자 함
  * https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud?sort=votes
  
* 또한 'Reproducible Machine Learning for Credit Card Fraud detection - Practical handbook'의 내용을 참조하여 특성공학을 실시하였음
  * https://fraud-detection-handbook.github.io/fraud-detection-handbook/Chapter_3_GettingStarted/SimulatedDataset.html
  
* 따라서 신용카드 사기 탐지 문제와 유사하게 접근하기 위하여 각각의 출금 요청건에 대하여 정상요청거래인지 사기거래건인지 판단하고자함

## '신용카드 사기 탐지' 문제의 특징
* 불균형한 데이터셋
  * Kaggle credit card fraud detection dataset에서는 99%가 정상거래데이터 였으며 당사에서도 같은 현상이 확인됨.
  * 따라서 Accuracy 보다는 recall,precision,f1_score와 같은 metric이 모델의 성능 척도로 사용됨.
 
* 이진 분류 문제
  * 정상요청거래인지 사기거래건인지 판단하는 문제(개, 고양이 분류문제와 같음).
  
* 개, 고양이 분류문제와는 달리 정상거래와 사기거래가 거의 구분이 되지 않는 케이스들이 존재함.

## 당사 데이터의 특성 (6개월간의 데이터)
* 사기거래 데이터를 모으기 위하여 당사 소비자보호부에서 모니터링 후 출금고정지(사기거래라고 판단)한 거래 요청건수를 모음
* 또한 의심가는 거래요청건수에서 '더치트' 어플리케이션에서 계좌를 조회한 결과 '사기 신고가 된 계좌'라고 뜨는 계좌와 관련된 거래요청건은 사기거래 데이터라고 판단
* 도합 1000여건의 출금요청건을 사기데이터세트로 준비함
* 그 외 출금요청건은 전부 정상으로 판단

## 주요 feature

* 채널
  * 당사 서비스에 접근하는 채널로 [web/모바일(android/ios)/hts]로 이루어져 있으며 One-hot encoding으로 되어있음
  
* 출금요청건수(0/3/5/7/15)
  * 당일/3일간/7일간/15일간 출금을 요청한 건수
   
* 출금요청금액(0/3/5/7/15)
  * 당일/3일간/7일간/15일간 출금을 요청한 금액

* Otp 재발급요청건수(0/3/5/7/15)
  * 당일/3일간/7일간/15일간 Otp 재발급을 요청한 건수

* 증권담보대출요청건수(0/3/5/7/15)
  * 당일/3일간/7일간/15일간 증권담보대출을 요청한 건수

* 증권담보대출요청금액(0/3/5/7/15)
  * 당일/3일간/7일간/15일간 출금을 요청한 금액

* 출금요청금액(0/3/5/7/15)
  * 당일/3일간/7일간/15일간 출금을 요청한 금액

* 당사계좌입금건수(0/3/5/7/15)
  * 당일/3일간/7일간/15일간 당사계좌로 입금된 건수

* Atm출금건수(0/3/5/7/15)
  * 당일/3일간/7일간/15일간 Atm으로 출금을 요청한 건수

* 주간/야간 여부
* 요일(평일/휴일 여부)
* 고객의 연령
* 고객의 등록 날짜
* 비대면 계좌 여부
  
## 프로젝트 소스 코드 

```
FDS_ML/
    __init__.py
    Data_loader/
        __init__.py
        Data_loader.py
        Preprocess.py
        Data_fetch.py
        MongoConnect.py
    train.py
    classify.py
```

## 모델 적용 결과
* 당사 일평균 출금요청건수인 3만 거래 건수를 정상데이터에서 랜덤샘플링 하였으며 샘플링한 정상데이터와 1000건의 사기거래건을 합쳐서 데이터 세트를 만듬
* 훈련데이터 0.8, 테스트 데이터 0.2 비율로 훈련 및 평가를 실시

 ### LGBM classifier
![다운로드 (1)](https://user-images.githubusercontent.com/18714388/181764955-a1ace5d9-5c52-442b-8320-b51cfafe1917.png)

![다운로드 (2)](https://user-images.githubusercontent.com/18714388/181765007-dededed4-4727-4fa3-a1e1-0ceced2175a2.png)

 ### XGB classifier
![다운로드 (3)](https://user-images.githubusercontent.com/18714388/181765213-764ca6a4-633f-4566-af62-a798b5e70b82.png)

![다운로드 (4)](https://user-images.githubusercontent.com/18714388/181765228-8dbb6695-20b7-40c3-849b-81281d6e5970.png)



## 결론

* 3만개의 정상거래건수와 사기거래건수를 학습 시켰을때 precision_recall curve는 0.6, roc_curve는 0.8로 의미가 없지는 않은 수치가 나왔으나 실제 fds에 적용되기에는 부적합하다고 판단
  * 너무 많은 정상거래건수를 사기거래건수라고 판별할 가능성 있음 (실제로 사기거래와 유사한 거래패턴을 보이나 막상 일일히 케이스를 오픈해보면 계좌주인 본인이 실시한 출금요청건임)
  * 현재 데이터 세트에서는 정상거래와 거의 유사한 패턴을 가지는 사기거래는 판별 불가 ( 해당 데이터들을 확실히 분류할 수 있는 새로운 feature 발굴 필요)
  
* shap으로 어떠한 feature가 데이터를 판별하는데 영향을 미쳣는지 확인해본 결과 몇몇 특정 feature들은 정상과 사기데이터 사이에서 뚜렷한 차이를 보임(고객연령, 주간/야간, OTP 발급등)

## 더 생각해볼점

* 모델의 성능을 높이기 위해서는 좀 더 많은 유용한 정보를 담은 feature들을 새로 만들 필요가 있음(현재 오픈뱅킹 신청 및 입출금 데이터는 미활용)

* Sequence 정보를 담은 feature 발굴 필요 (OTP재발급을 요청하고 얼마의 시간이 지난 후 출금을 요청하는지 등)

* 또한 atm 출금요청건에 대해서도 당사 채널에서 출금요청을 하는 건수와 동일하게 취급하여 정상거래 사기거래를 분류하고 데이터세트에 추가할 필요가 있음
