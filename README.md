# CIFAR-10-CNN-custom
예전에 학교 딥러닝 강의 들을 때 처음으로 만든 것 

1. Introduction 

1학기 동안 배운 내용을 확실하게 이해하기 위해서는 간단하더라도 내가 직접 모델을 설계해보는 것이 좋을 것이라고 생각했다. 이에 따라 MNIST에 비해 좀 더 복잡한 이미지인 CIFAR10을 이용하여 Convolution Neural Network (CNN)을 통해 CIFAR10 이미지 데이터셋을 분류하기 위한 모델들을 만들어보고 성능을 비교해보기로 연구주제를 잡았다.


2. Implementation Details

- 사용된 데이터 
CIFAR10 : 32×32 크기의 컬러 이미지 60000장으로 이루어져 있으며 10개의 class로 분류된다. 60000장 중
에 50000장은 training image이며 10000장은 test image이다. 이를 정규화 시켜서 진행했다. 

- 사용된 모델 및 학습 방법
- 
![image](https://user-images.githubusercontent.com/73826816/132704447-6843cea6-df14-4c82-816e-58cc42dbfd78.png)


Batch size= 64
비용함수는 CrossEntropyLoss를 사용하였으며 optimizer는 Adam을 사용하였다.
(learning rate=0.001, weight_decay=0.000001)
모델1 : 마지막에 fully connected layer를 사용
모델2 : 마지막에 average pooling을 사용
모델3 : 모델2 + dropout 사용

- 아이디어를 생각하게 된 이유
분류하려는 대상이 가장자리도 점유하고 있기 때문에 가장자리 픽셀들이 중앙 픽셀들에 비해 과소평가되는 것을 막기 위해 padding을 사용하였고 이미지의 해상도가 낮아서 정보손실을 막기위해 stride를 1로 최소화해서 진행했다. 또한 depth를 최대한 늘리기 위해서 이미지 크기가 줄어들지 않도록 3×3 크기의 작은 필터를 사용하였다. Max pooling 과정에서 pool들이 겹쳐지면서 최대값들이 중복돼서 나오는 것을 방지하기 위해 pooling 과정에서 2×2 filter를 stride=2로 설정해서 pool들이 겹치는 경우를 막았다. overfitting을 방지하기 위해 데이터를 정규화(normalization)하고 weight_decay를 설정해서 regularization 또한 고려해줬으며 모델 3의 경우에는 dropout을 추가해줬다.

3. Experiment Result

- 실험 결과 및 분석
1) accuracy 및 average loss 비교
(파랑 : fully connected , 노랑 : average pooling, 초록 : average pooling + dropout)

![image](https://user-images.githubusercontent.com/73826816/132704589-f6ee79db-d30d-4d07-a052-639a14d3b9bb.png)


Fully connected에 비해 average pooling이 근소하게 높은 정확도를 보여줬고 loss average도 대략 40 epoch전까지는 더 낮았는데 이는 average pooling이 비교적 매개변수요구량이 적고 일반화 능력이 좋기 때문에 그런 것으로 보인다. 다만 두가지 경우 모두 loss average가 초반에는 급격히 줄어들다가 이후에는 계속 증가하는 경향을 보였는데 이는 overfitting 이슈가 발생한 것으로 보인다. Dropout을 추가한 모델의 경우에는 초반에는 다른 모델들에 비해 loss가 크지만 점차적으로 줄어드는 경향성을 보여줬으며 20epoch이후에는 가장 작은 average loss를 보여주며 overfitting 이슈가 어느정도 해결되었음을 알 수 있었다.

4. Conclusion
- 이미지의 위계적인 패턴을 수월하게 학습하기 위해 층을 최대한 늘리다 보니 매개변수의 개수가 지나치게 많아져서 모델1, 2의 경우에는 정확도가 정체되고 loss가 초반에 잠깐 감소하다가 이후에는 계속해서 증가하는 overfitting 이슈를 보였다. 모델3의 경우에는 dropout을 추가하여 학습과정에서 매개변수 개수를 어느 정도 줄이는 효과를 얻을 수 있었고 overfitting 이슈가 해결됨과 동시에 정확도도 향상된 것을 알 수 있었다.
-  모델의 형태나 학습속도 같은 hyperparameter를 실험적으로만 구해서 이 수치가 왜 좋은지, 왜 이런 결과가 나왔는지에 대해 수학적으로 검토해보지 못한 점이 아쉽다. 수학 공부가 더 필요한 것 같다. 정확도 90퍼센트를 넘기기 위해서는 모델을 단순화해서 불필요한 매개변수들을 줄이는 방향으로 진행해야 될 것 같다.
