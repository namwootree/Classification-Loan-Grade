# 고객 대출등급 분류 AI 해커톤 [(Link)](https://dacon.io/competitions/official/236036/overview/description)

---

## 대회 소개

### 목표

* 고객의 대출등급을 예측하는 AI 알고리즘 개발

### 참여 기간

* 2024.01.15 ~ 2024.02.05

### 참여자

* 권남우

### 사용한 Tool
* Python
* Pandas / Scikit - Learn / matplotlib / seaborn


### 결과

* PRIVATE SCORE : 0.9428 (26등/784팀, 상위 4% 이내)
* EDA 공유 좋아요 20개
* 모델링 공유 좋아요 8개

---

## 데이터셋 소개

### Train Set
* 총 96294 개 고객 데이터 (대출금액, 연간소득, 부채 대비 소득 비율, 총 계좌수, 최근 2년간 연체 횟수, 총상환원금, 총상환이자 등)
* 대출 등급 : A to G

### Test Set
* 총 64197 개 고객 데이터

---

## 프로세스

### EDA [Link](https://dacon.io/competitions/open/236214/codeshare/9571?page=1&dtype=recent)
* 일부 칼럼에서 결측치가 존재하는 것과 데이터 불균형이 발생하는 것을 확인
* 총연체금액이 0이 아니여도 최근 2년간 연체 횟수 및 연체계좌수가 0인 데이터가 존재함을 확인
* 위의 내용을 통해 결측치를 0으로도 표현하였을 것으로 가정함
* 일부 극단값이 존재하는 것을 확인 (신용 등급이 좋아도 연체 횟수가 높은 경우 존재 등)

### Preprocessing
* 총상환원금과 총상환이자는 신용 등급에 따른 이자율에 의해 결정된다고 가정
* 원리금균등 혹은 원금 균등 상환이라고 가정하여 '근사이자율' = ((총상환원금 / 대출기간) / 총상환원금)* 100 변수 생성
* 시각화를 통해 대출등급에 따라 근사이자율이 분포가 차이가 존재함을 확인
* 근사이자율 외에 '대출금 대비 총상환이자', ''대출금 대비 총상환원금', '소득 대비 대출금', '소득 대비 부채 비율' 파생변수 생성
* 연체계좌수 변수를 통해 연체여부를 식별하는 변수 생성
* 비슷한 근로기간 데이터를 묶어 카테고리 데이터로 변환
* 대출목적 변수 내 데이터를 대출등급 분포와 비슷한 유형을 확인하여 범주의 수를 줄임

<img width="735" alt="스크린샷 2024-03-03 14 31 36" src="https://github.com/namwootree/Classification-Loan-Grade/assets/91008734/24ba7bba-a7b3-47c5-a740-2e64d2d89630">

### Modeling

* 결측치를 특정 값으로 대치하는 대신 XGBoost 모델을 활용하여 모델 자체적으로 결측치를 처리할 수 있도록 함

---

## 느낀 점

### 과대 적합 문제 발생 
* 대회 종료 후 근사이자율 변수가 과적합에 매우 민감하게 반응하는 것을 확인함
* 근사이자율 변수를 제거하고 XGBoost 모델에 규제를 강화한 결과 성능이 좋아짐
* 수상한 참여자들의 코드를 확인한 결과 Boosting 기법 대신 일반전인 Decision Tree 모델이 더 좋은 성능을 보임
* 모델 및 변수 선택 시 단계적인 접근이 필요함을 깨닳음
