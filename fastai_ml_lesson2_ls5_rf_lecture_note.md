# Lesson 5.  Random Forest interpreter 

# - 머신러닝을 사용할 때 주의(?)해야할 점들

머신러닝 사용에서 중요한 것 :  **일반화 정확도** 혹은 **일반화 오류**

## Random Forest

: 머신러닝에서 자주 사용되는 알고리즘으로, 학습 적용 데이터를 샘플링해서 여러 개의 의사결정 트리를 만들고, 만들어진 의사결정 트리를 기반으로 다수결로 결과를 결정하는 방법

- 일반화가 잘 되었는지 확인하는 일반적인 방법 :  무작위 샘플
- make 2nd test dataset = Validation data set
- Validation Set, Test Set, Training Set :  검증용(Validation) 또는 시험용(Test)을 제외한 나머지는 모두 학습용(Training) 데이터
![split_sets](https://miro.medium.com/max/585/1*Ka6WNHrQDnoD1WfvYxKl_w.png)
- Training -> check OOB score -> Check Result On Validation Set -> & Double check On Test Set
  - Validation Set에서는 잘 작동하지만 Test Set에서는 성능이 매우 떨어진다면 일반화에 실패한 것 = 검증용 데이터에 과적합 되었다고 볼 수 있다.
  - 기존 RandomForest에서는 검증용 데이터셋을 필요로 하지 않고, 대신에 OOB 오차 또는 OOB 점수를 사용했었다. (OOB점수는 검증용 데이터셋에 대한 점수와 매우 비슷한 결과를 내준다.)
  - **Question** : OOB점수는 검증용 데이터셋에 대한 점수와 매우 비슷한 결과를 주지만 평균적으로는 조금 덜 좋다. 왜 그럴까?
    - Answer: 모든 행들이 예측을 위해 트리의 부분 집합을 사용하기 때문에, 트리가 적을수록 부정확한 예측을 얻는다. (검증용과 OOB 둘 다 무작위로 선택된 부분집합을 사용, but 검증용 데이터셋은 예측을 위해 포레스트 전체를 사용하지만, OOB에서는 전체 포레스트를 사용할 수 없기 때문 <- ?? 100% 이해가 안되었음)
  - **Question**: 검증용 데이터셋에서 무작위 샘플을 사용할 수 없는 경우가 발생하는 이유?
    - 모델사용 시점은 모델은 만든 시점보다 더 뒤일 것이다. (시간차 존재)
    - 모델 만들 때에 사용하는 데이터의 시점도 오래된 것이라서 발생하는 경우일 수도 있다.
      - 해결방법 1 ?:  데이터의 시간 순에 따른 가중치 설정
      - 해결방법 2 :  학습용 데이터로 training -> Validation 결과가 좋지 않은 경우 -> Training + Validation data를 혼합하여 다시 모델 훈련  **이 경우에는 확인해 볼 검증용 데이터셋이 없기 때문에 반드시 이전과 정확이 동일한 단계와 방법으로 모델을 만들어야 한다**
      - 해결방법 2-1 : 검증용 데이터셋이 시험용 데이터셋을 잘 대표하는지 알아보기
        1. 학습용 데이터셋에서 모델 5개를 만든다 - 최대한 성능이 차이나게 만든다. (특정 시간대의 데이터에 따른 일한봐하는 능력에 다양성을 둔다던가 하는 식으로.. - 하나는 전체, 하나는 지난 2주간, 하나는 지난 6주간 등등)
          - Random sample (4 years)
          - Last month of data (July 15–August 15)
          - Last 2 weeks (August 1–15)
          - Same day range one month earlier (July 15–30)
        2. 각 모델의 점수를 검증용 데이터셋으로 확인한다.
        3. 각 모델의 점수를 시험용 데이터셋에서도 확인한다.
        4. 2와 3의 결과가 일직선상에 위치하는 것을 확인한다. **만약 그렇지 않다면 검증용 데이터셋에서 좋은 피드백을 바라기 어려운 것이므로 이 과정을 계속 반복해서 일직선으로 만들어줘야 한다.** (검증용 데이터 셋을 바꿔가면서..?)
      - 실험용 데이터는 실제와 상당히 유사해야하는 데, 이것이 사실상 힘들다..



### Cross Validation

실행방식이 랜덤 포레스트 유사..

- 단점
  - 시간 : 오래 걸림
  - 무작위로 샘플 추출하면 위험? 무작위 검증용 데이터셋이 문제에 적절하지 않을 수 있다.
- 따라서 이 수업에선 cross validation사용하지 않을 것임



### Tree Interpreter

=> 강의 노트북 (https://github.com/boyoung-place/fastai_for_ml/blob/main/lesson2-rf_interpretation.ipynb)
