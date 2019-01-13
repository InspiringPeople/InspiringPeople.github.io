
데이터분석도 자동화가 될 수 있을까?  

## Auto-ML

많은 사람들이 AI 시대, 빅데이터이터 시대에 두려워하는 것 중 하나는 기계로 인해 자동화가 되어 일자리를 잃는 것이다.  
자동화의 첫 번째 단계는 단순 반복 작업이 될 것이다. 하지만 데이터분석 조차도 자동화 할 수 있는 여러 방법들이 연구되고 있다.  
오늘은 그 중에서 AutoKeras를 소개하고자 한다. 

데이터분석을 이용하여 문제를 해결할 때 다음과 같은 프로세스를 반복적으로 거친다.  
- 데이터 수집
- 데이터 전처리  
- 데이터 탐색  
- 모델 학습
- 모델 평가
- 결과 분석
- 파라미터 튜닝
- 모델 평가 및 분석  

데이터 전처리 및 탐색부터 파라미터 튜닝까지 이 작업들은 굉장히 광범위하고 반복적으로 일어난다. 한 스텝 단계에서의 변화가 이후 파이프라인 프로세스에 영향을 줄 수 있기 때문에 경우의 수도 많아지고, 피땀 어린 노력을 하여도 많은 시간이 소요될 수 밖에 없다.  
더욱 어려운 점은, 이것이 최선의 결과인지, 더 좋은 모델은 없는지, 어느 시점에서 분석을 멈춰야 하는지 알기 어렵다는 것이다. 이 과정에서 때로 데이터분석가들은 돌고 도는 챗바퀴 함정에 빠지게 될수도 있다.

마감 시간은 정해져있고, 데이터분석가들은 문제를 파악하고, 데이터를 탐색하며 정제하는데 많은 시간을 쏟기 때문에, 모델 튜닝이나 정확도 개선을 위한 작업을 할 시간이 부족할 수 밖에 없다. 여기서 **AutoML의 아이디어**가 나왔다. **다양한 알고리즘 및 다양한 하이퍼 파라미터를 구성하여 수많은 모델을 생성하고 성능과 정확도를 비교하는 것을 자동화 하는 것이다.**  

이러한 작업을 위해 개발된 파이썬 라이브러리는 다음과 같다.  
- Auto-Sklearn  
- TPOT  
- Auto-Keras  
- H2O.ai  
- Google's AutoML

## MNIST Classification with Keras  
이 중 AutoKeras 사용법을 알아보기 위해 Keras 기반 MNIST 분류를 아래와 같이 수행해보았다. 


```python
'''Trains a simple convnet on the MNIST dataset.
Gets to 99.25% test accuracy after 12 epochs
(there is still a lot of margin for parameter tuning).
16 seconds per epoch on a GRID K520 GPU.
'''

from __future__ import print_function
import keras
from keras.datasets import mnist
from keras.models import Sequential
from keras.layers import Dense, Dropout, Flatten
from keras.layers import Conv2D, MaxPooling2D
from keras import backend as K

batch_size = 128
num_classes = 10
epochs = 12

# input image dimensions
img_rows, img_cols = 28, 28

# the data, split between train and test sets
(x_train, y_train), (x_test, y_test) = mnist.load_data()

if K.image_data_format() == 'channels_first':
    x_train = x_train.reshape(x_train.shape[0], 1, img_rows, img_cols)
    x_test = x_test.reshape(x_test.shape[0], 1, img_rows, img_cols)
    input_shape = (1, img_rows, img_cols)
else:
    x_train = x_train.reshape(x_train.shape[0], img_rows, img_cols, 1)
    x_test = x_test.reshape(x_test.shape[0], img_rows, img_cols, 1)
    input_shape = (img_rows, img_cols, 1)

x_train = x_train.astype('float32')
x_test = x_test.astype('float32')
x_train /= 255
x_test /= 255
print('x_train shape:', x_train.shape)
print(x_train.shape[0], 'train samples')
print(x_test.shape[0], 'test samples')

# convert class vectors to binary class matrices
y_train = keras.utils.to_categorical(y_train, num_classes)
y_test = keras.utils.to_categorical(y_test, num_classes)

model = Sequential()
model.add(Conv2D(32, kernel_size=(3, 3),
                 activation='relu',
                 input_shape=input_shape))
model.add(Conv2D(64, (3, 3), activation='relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))
model.add(Flatten())
model.add(Dense(128, activation='relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes, activation='softmax'))

model.compile(loss=keras.losses.categorical_crossentropy,
              optimizer=keras.optimizers.Adadelta(),
              metrics=['accuracy'])

model.fit(x_train, y_train,
          batch_size=batch_size,
          epochs=epochs,
          verbose=1,
          validation_data=(x_test, y_test))
score = model.evaluate(x_test, y_test, verbose=0)
print('Test loss:', score[0])
print('Test accuracy:', score[1])
```

    Using TensorFlow backend.
    

    Downloading data from https://s3.amazonaws.com/img-datasets/mnist.npz
    11493376/11490434 [==============================] - 6s 0us/step
    x_train shape: (60000, 28, 28, 1)
    60000 train samples
    10000 test samples
    Train on 60000 samples, validate on 10000 samples
    Epoch 1/12
    60000/60000 [==============================] - 138s 2ms/step - loss: 0.2622 - acc: 0.9205 - val_loss: 0.0573 - val_acc: 0.9822
    Epoch 2/12
    60000/60000 [==============================] - 138s 2ms/step - loss: 0.0875 - acc: 0.9744 - val_loss: 0.0393 - val_acc: 0.9866
    Epoch 3/12
    60000/60000 [==============================] - 138s 2ms/step - loss: 0.0622 - acc: 0.9815 - val_loss: 0.0337 - val_acc: 0.9874
    Epoch 4/12
    60000/60000 [==============================] - 144s 2ms/step - loss: 0.0553 - acc: 0.9835 - val_loss: 0.0301 - val_acc: 0.9894
    Epoch 5/12
    60000/60000 [==============================] - 145s 2ms/step - loss: 0.0457 - acc: 0.9866 - val_loss: 0.0336 - val_acc: 0.9887
    Epoch 6/12
    60000/60000 [==============================] - 135s 2ms/step - loss: 0.0403 - acc: 0.9879 - val_loss: 0.0293 - val_acc: 0.9912
    Epoch 7/12
    60000/60000 [==============================] - 140s 2ms/step - loss: 0.0355 - acc: 0.9892 - val_loss: 0.0269 - val_acc: 0.9911
    Epoch 8/12
    60000/60000 [==============================] - 138s 2ms/step - loss: 0.0337 - acc: 0.9894 - val_loss: 0.0268 - val_acc: 0.9914
    Epoch 9/12
    60000/60000 [==============================] - 142s 2ms/step - loss: 0.0309 - acc: 0.9903 - val_loss: 0.0275 - val_acc: 0.9917
    Epoch 10/12
    60000/60000 [==============================] - 139s 2ms/step - loss: 0.0278 - acc: 0.9914 - val_loss: 0.0280 - val_acc: 0.9920
    Epoch 11/12
    60000/60000 [==============================] - 139s 2ms/step - loss: 0.0284 - acc: 0.9914 - val_loss: 0.0265 - val_acc: 0.9921
    Epoch 12/12
    60000/60000 [==============================] - 138s 2ms/step - loss: 0.0271 - acc: 0.9917 - val_loss: 0.0318 - val_acc: 0.9912
    Test loss: 0.031818649467292154
    Test accuracy: 0.9912
    

## MNIST Classification with AutoKeras  
손글씨 분류를 위한 위 keras 예제는 tensorflow, pytorch 버전에 비해 굉장히 간단하게 작성된 것이다. 하지만 AutoKeras를 사용하면 위 실행코드가 단 4줄로 표현된다. 코드양은 획기적으로 줄었지만 내부적으로 여러 모델과 파라미터 등을 비교하기 때문에 소요시간은 꽤 걸린다.



```python
from keras.datasets import mnist
from autokeras import ImageClassifier

(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train = x_train.reshape(x_train.shape + (1,)) # (1,) denotes the channles which is 1 in this case
x_test = x_test.reshape(x_test.shape + (1,)) # (1,) denotes the channles which is 1 in this case
```

## 단상  
사실 예전부터 데이터모델링에 대한 자동화 이야기는 줄곧 화자되어 왔다. 하지만 도메인을 이해해야하고, 전처리 및 feature engineering 등에 대한 기준, 아이디어 등은 사람이 정할 수 있기 때문에 회의적이었던 것이 사실이다. 사실 위의 MNIST 예제를 보고 코드양이 확 줄어든 것에 대해 놀랍기는 했지만, 과연 실무에서 사용되는 dirty-data에서도 AutoKeras가 제대로 동작될지는 아직 의심스럽다. 

하지만 모델링 방법 선택 (어떤 분류기를 사용할 것인지 등), 하이퍼파라미터 튜닝 등은 사람이 직접 하기에 너무 시간이 많이 소요되고 결과 비교를 정리하기에도 노고가 많이 들어가는 일이었기에, 그 부분은 Auto-ML 방식을 사용하면 정말 좋은 결과가 나올 수 있지 않을까 기대된다. 아직은 정말 한 숟가락 맛뵈기만 살펴본 상태이니, 잘 정제된 데이터가 있을 때 AutoKeras 등을 이용해 결과를 비교해봐야겠다. 또 정제되지 않은 데이터에 대한 결과도 무척 궁금하고 추후 데이터분석에서 결과 차이를 살펴봐야겠다.
