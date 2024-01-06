### Mission1
- resize_images : 서로 다른 이미지 크기를 224 x 224 크기로 resize 후 지정한 폴더에 저장
- plot_random_mages : 클래스별 이미지 시각화
  - 랜덤으로 하나를 정해 시각화
  - 한글 깨지는 문제를 Unicode의 encoder 이용해 해결
  - 축 제거 후 깔끔하게 시각화
<br>

- resnet18 모델 사용
  - 클래스가 42개라 마지막 fc layer를 42로 변경 후 사용
- loss : CrossEntropyLoss
- optimizer : Adam
- pretrained : False
- 아무런 처리 없이 돌렸을 때 67.8%의 정확도 보임

#### 시각화 예시
![image](https://github.com/fkrdnjs/Data_Creator_Camp/assets/68600918/becfb917-2140-4595-92ee-69f773884c8a)

#### resnet 정리 내용
[![Tistory's Card](https://github-readme-tistory-card.vercel.app/api?name=paradise999&postId=31&theme=default)](https://paradise999.tistory.com)


---
### Mission2
- 시각화를 통한 클래스별 정답률, 오답률 확인
  - Augmentation으로 다양성 증가, 이미지 증강으로 클래스 불균형 해결
<br>

- 최종적으로 resnet50 모델 사용
  - batch_size : 64
  - Augmentation : 자르기, 수평 뒤집기, 회전, 컷아웃 적용
  - 클래스 불균형 : 클래스별 가중치를 고려해 학습
  - 조기 종료 : 5번 동안 accuracy 개선되지 않으면 종료
- 위 방법들 적용한 결과 83.6%의 정확도 보임

#### 💡시행착오 과정
1. batch size
   - 처음 64로 시작하고 128로 늘렸으나 오히려 성능 저하
   - 다른 하이퍼파라미터를 조정해도 성능 향상이 이뤄지지 않아 64로 시도하니 성능 향상
   - 최종적으로 64로 최종 적용
  
2. Data Augmentation
   - version1 : crop, horizontal flip
   - version2 : version1 + rotation, cutout
   - accuracy만 보면 version1이 우수하나 모델의 일반화 성능을 고려해 version2를 최종 적용

3. Class Balancing
   - 노이즈 추가, 회전, 반전 등의 방법을 이용해 이미지 생성을 통한 불균형 해결
   - 클래스 개수 별 가중치를 조절해 학습
   - 두 번째 방법이 정확도가 높고 어느 모델에서나 적용하기 쉽다고 생각해 최종 적용

4. Resnet
   - 18, 34, 50 모델에서 다양한 기법들 적용
   - resnet50에서 가장 성능이 좋아 최종 적용

#### 시각화 예시
![image](https://github.com/fkrdnjs/Data_Creator_Camp/assets/68600918/0fd280aa-2bca-4268-9924-08db5e5c4767)


---
### Mission3
- Mission2에서 학습한 모델 로드
- resnet50 모델 사용
  - 클래스별 가중치 적용으로 클래스 불균형 해결
  - 전처리 방법을 로드한 모델과 동일하게 적용
  - 초기 층의 파라미터를 동결시켜 학습 진행
- 위 방법들 적용한 결과 98.0%의 정확도 보임

#### 💡시행착오 과정
1. 전처리
   - 초기에는 기존에 시도했던 여러 전처리 방법들을 적용
   - 그러나 일관성, 일반화 성능, 입력 형태 등을 고려했을 때 로드한 모델과 전처리 방법이 일치해야 함을 확인
   - 최종적으로 로드한 모델과 동일한 전처리 방법 적용

2. 파라미터 동결
   - 처음에는 파라미터 동결 방법을 사용하지 않음
   - 그러나 초기 층은 저수준의 특징(색상 등)을 학습
   - 이를 동결시켜 추출 능력은 그대로 유지하면서 이 특징들을 새로운 작업에 활용
   - 또한 과적합 방지에도 도움이 된다고 하여 파라미터 동결 적용
