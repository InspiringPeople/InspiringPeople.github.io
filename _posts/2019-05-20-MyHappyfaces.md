---
title: Happy Faces 
category: Data Analysis
tags: [AI for Art, Creative AI, EigenFace]

---

------



AI가 인간을 이해하는데 도움을 줄 수 있을까. 사회가 정의한 '행복'이라는 것은 무엇일까. 또한 그것은 우리가 볼 수 있는 유형의 것으로 표현될 수 있을까.라는 질문들로 시작된, Creative AI 첫번째 프로젝트이다.   
<br>
우선 많은 사람들의 행복한 (웃는) 이미지들을 모아서 'Happy Eigen Face'를 만들었다. 그리고 새로운 얼굴 표정이 입력되면 이 얼굴이 'Happy Eigen Face'로 투영해가는 과정을 표현했다. 한 사람의 감정이 사회의 감정으로 투영해 가는 과정을 그린 것이다. 너와 내가 다르게 보일지라도 우리는 어떤 공통된 모습이 있고, 그 지점으로부터 모두 연결될 수 있다는 메시지를 주고 싶었다. 사람이 아니라서, 동떨어진 시선으로 볼 수 있는 AI라서, 그것으로부터 제공된 '공통요소, 동질감'이라는 것은 어쩌면 가끔 쿨하게 받아들여질 수 있지 않을까.  
<br>
아래에 작업한 모델링 관련 소스 코드와 완성된 영상이 공유한다. 작업은 모두연 DLC 3기 전도희님, 곽현일님과 같이 작업하였고, 나는 모델링 부분을, 두 분은 전처리와 시각화 부분을 맡아주셨다.(시각화 부분 짱, 나도 저렇게 언젠간 interactive한 시각화를 해보고 싶다.) 어쩌면 작업 전, 후에 의도했던 결들이 조금씩 달라졌을지 모르지만, 사람을 돕는 AI라는 주제는 여전히 같다. 프로젝트 팀원분들 모두 적극적이고 아이디어가 샘솟아서 작업하면서 너무 즐거웠다.

------

우선 모델링 결과로 나온 이미지를 interactive하게 시각화한 데모 영상을 먼저 보자.  원래는 마우스 훨이나 키보드 입력에 따라 동적으로 움직이는데, 영상에는 일부만 표현됨 (클릭 시 동영상 재생).

[![youANDI](http://img.youtube.com/vi/opERqW2Jx8Q/0.jpg)](http://www.youtube.com/watch?v=opERqW2Jx8Q "happy")

------

이제 모델링 부분 공유 시작.  
사회가 정의한 행복을 표현하기 위해 우선 아래 링크의 Open dataset에서 얼굴 사진을 획득하고 웃는 얼굴만 사용하기 위해 Face_Emotion_Detecion이라는 Pre-trained Model을 사용하였다

[Dataset : scikit learn open data set 10명의 다양한 표정 데이터 400장 보유](https://scikit-learn.org/0.19/modules/generated/sklearn.datasets.fetch_olivetti_faces.html#sklearn.datasets.fetch_olivetti_faces)  

[Pre-trained Model : Face_emotion_detection model](https://scikit-learn.org/0.19/modules/generated/sklearn.datasets.fetch_olivetti_faces.html#sklearn.datasets.fetch_olivetti_faces
https://github.com/jalajthanaki/Facial_emotion_recognition_using_Keras)

------

작업순서  

- Pretraining 된 face emotion model을 이용하여 data set 중 happy로 라벨링된 표정 사진만 추출
- 10명의 총 400장 사진 중 108장이 추출됨   
- 108장의 happy한 표정 데이터에 대한 eigen face를 도출하여 2차원으로 표현 (AI로 표현되는 Happy face)
- Happy로 대표되는 eigen face에 나/나의 얼굴은 어떻게 투영될 것인가? 
- Input 이미지가 Eigen face에 투영되는 과정을 weight를 변화시켜가며 표현




```python
# -*- coding: utf-8 -*-

import cv2
import numpy as np
from keras.models import load_model
import sys

##Satart Section
''' Keras took all GPU memory so to limit GPU usage, I have add those lines'''

import tensorflow as tf
from keras.backend.tensorflow_backend import set_session

config = tf.ConfigProto()
config.gpu_options.per_process_gpu_memory_fraction = 0.1
set_session(tf.Session(config=config))
''' Keras took all GPU memory so to limit GPU usage, I have add those lines'''
## End section

import tensorflow.keras
from keras.preprocessing import image
from keras.applications.imagenet_utils import decode_predictions, preprocess_input
from keras.models import Model
```

    Using TensorFlow backend.


데이터 셋 다운로드


```python
from sklearn.datasets import fetch_olivetti_faces
from sklearn.decomposition import PCA
import matplotlib.pyplot as plt
import numpy as np
faces_all = fetch_olivetti_faces()


```

Face_emotion_detection을 위해 pre-trained된 모델과 xml 파일 다운로드해서 colab에 업로드 & 로딩


```python

faceCascade = cv2.CascadeClassifier('haarcascade_frontalface_alt2.xml')

model = load_model('model_5-49-0.62.hdf5')


    

```

    WARNING:tensorflow:From /usr/local/lib/python3.6/dist-packages/tensorflow/python/framework/op_def_library.py:263: colocate_with (from tensorflow.python.framework.ops) is deprecated and will be removed in a future version.
    Instructions for updating:
    Colocations handled automatically by placer.
    WARNING:tensorflow:From /usr/local/lib/python3.6/dist-packages/keras/backend/tensorflow_backend.py:3445: calling dropout (from tensorflow.python.ops.nn_ops) with keep_prob is deprecated and will be removed in a future version.
    Instructions for updating:
    Please use `rate` instead of `keep_prob`. Rate should be set to `rate = 1 - keep_prob`.
    WARNING:tensorflow:From /usr/local/lib/python3.6/dist-packages/tensorflow/python/ops/math_ops.py:3066: to_int32 (from tensorflow.python.ops.math_ops) is deprecated and will be removed in a future version.
    Instructions for updating:
    Use tf.cast instead.


    /usr/local/lib/python3.6/dist-packages/keras/engine/saving.py:327: UserWarning: Error in loading the saved optimizer state. As a result, your model is starting with a freshly initialized optimizer.
      warnings.warn('Error in loading the saved optimizer '



```python

model.summary()
```

    _________________________________________________________________
    Layer (type)                 Output Shape              Param #   
    =================================================================
    conv2d_1 (Conv2D)            (None, 64, 48, 48)        640       
    _________________________________________________________________
    dropout_1 (Dropout)          (None, 64, 48, 48)        0         
    _________________________________________________________________
    max_pooling2d_1 (MaxPooling2 (None, 64, 24, 24)        0         
    _________________________________________________________________
    conv2d_2 (Conv2D)            (None, 128, 24, 24)       204928    
    _________________________________________________________________
    dropout_2 (Dropout)          (None, 128, 24, 24)       0         
    _________________________________________________________________
    max_pooling2d_2 (MaxPooling2 (None, 128, 12, 12)       0         
    _________________________________________________________________
    conv2d_3 (Conv2D)            (None, 512, 12, 12)       590336    
    _________________________________________________________________
    dropout_3 (Dropout)          (None, 512, 12, 12)       0         
    _________________________________________________________________
    max_pooling2d_3 (MaxPooling2 (None, 512, 6, 6)         0         
    _________________________________________________________________
    conv2d_4 (Conv2D)            (None, 512, 6, 6)         2359808   
    _________________________________________________________________
    dropout_4 (Dropout)          (None, 512, 6, 6)         0         
    _________________________________________________________________
    max_pooling2d_4 (MaxPooling2 (None, 512, 3, 3)         0         
    _________________________________________________________________
    flatten_1 (Flatten)          (None, 4608)              0         
    _________________________________________________________________
    dense_1 (Dense)              (None, 256)               1179904   
    _________________________________________________________________
    dropout_5 (Dropout)          (None, 256)               0         
    _________________________________________________________________
    dense_2 (Dense)              (None, 512)               131584    
    _________________________________________________________________
    dropout_6 (Dropout)          (None, 512)               0         
    _________________________________________________________________
    dense_3 (Dense)              (None, 7)                 3591      
    =================================================================
    Total params: 4,470,791
    Trainable params: 4,470,791
    Non-trainable params: 0
    _________________________________________________________________


Pre-trained 모델을 사용하여 새로운 얼굴 사진이 들어오면 다음 7가지 감정 중 하나로 라벨링한다.  
- 'angry', 'disgust', 'fear', 'happy', 'sad', 'surprise' ,'neutral'


```python
def test_image(addr):
    target = ['angry','disgust','fear','happy','sad','surprise','neutral']
    font = cv2.FONT_HERSHEY_SIMPLEX
    faces = addr
    faces = cv2.resize(faces,(48,48))
    faces = faces.reshape(1, 1,faces.shape[0],faces.shape[1])
    
    result = target[np.argmax(model.predict(faces))]

    #print(result)
    return result

```


```python
print(len(faces_all.images))

h_imgs=[]
h_data=[]
for i in range(len(faces_all.images)):
  if(test_image(faces_all.images[i])=='happy'):
    h_imgs.append(faces_all.images[i])
    h_data.append(faces_all.data[i])
```

    400



```python
print(len(h_imgs))
```

    108


모델을 이용하여 happy로 라벨링 된 이미지 확인  
전체 400장 얼굴 사진 중 108장의 얼굴이 Happy로 라벨링되었고, 이 사진들을 확인해본다.


```python


N = 10
M = 10
fig = plt.figure(figsize=(10, 10))
plt.subplots_adjust(top=1, bottom=0, hspace=0, wspace=0.05)
for i in range(N):
    for j in range(M):
        k = i * M + j
        ax = fig.add_subplot(N, M, k+1)
        ax.imshow(h_imgs[k], cmap=plt.cm.bone)
        ax.grid(False)
        ax.xaxis.set_ticks([])
        ax.yaxis.set_ticks([])

plt.tight_layout()
plt.show()
```


![png](/assets/img/output_14_0.png)


위 108장에 대한 Happy Face를 표현하는 주성분을 파악하기 위해 2개로 PCA 분석 진행한다


```python
from sklearn.decomposition import PCA
pca3 = PCA(n_components=2)
X3 = h_data
W3 = pca3.fit_transform(X3)
X32 = pca3.inverse_transform(W3)
```

주성분으로 각 표정 데이터를 근사화 시키고 이미지를 저장함


```python
N = 10
M = 10
fig = plt.figure(figsize=(10, 10))
plt.subplots_adjust(top=1, bottom=0, hspace=0, wspace=0.05)
for i in range(N):
    for j in range(M):
        k = i * M + j
        ax = fig.add_subplot(N, M, k+1)
        ax.imshow(X32[k].reshape(64, 64), cmap=plt.cm.bone)
        #cv2.imwrite(str(i)+'_'+str(j)+'_happy_common.jpg', X32[k].reshape(64, 64)*255)
        ax.grid(False)
        ax.xaxis.set_ticks([])
        ax.yaxis.set_ticks([])
#plt.suptitle("PCA : Happy faces")
plt.tight_layout()
plt.show()
```


![png](/assets/img/output_18_0.png)



```python
pca3.mean_.shape
```




    (4096,)



주성분으로 표현된 eigen face, 여기서는 100장의 happy face를 대표하는 얼굴과 각 주성분 PCA1, PCA2의 이미지를 확인한다


```python
face_mean = pca3.mean_.reshape(64, 64)
face_p1 = pca3.components_[0].reshape(64, 64)
face_p2 = pca3.components_[1].reshape(64, 64)

plt.subplot(131)
plt.imshow(face_mean, cmap=plt.cm.bone)
plt.grid(False)
plt.xticks([])
plt.yticks([])
plt.title("Mean Face")
plt.subplot(132)
plt.imshow(face_p1, cmap=plt.cm.bone)

plt.grid(False)
plt.xticks([])
plt.yticks([])
plt.title("PCA 1")
plt.subplot(133)
plt.imshow(face_p2, cmap=plt.cm.bone)
plt.grid(False)
plt.xticks([])
plt.yticks([])
plt.title("PCA 2")
plt.show()
```


![png](/assets/img/output_21_0.png)


mean_face, pca1_face, pca2_face 저장


```python
cv2.imwrite('mean_face.jpg', face_mean*255)
cv2.imwrite('p1_face.jpg', face_p1*255)
cv2.imwrite('p2_face.jpg', face_p2*255)
```




    True




```python
face_mean.shape
```




    (64, 64)



특정 인물의 사진을 happy eigen face로 투영해가는 과정  
- happy face 얼굴 중 0번째 얼굴을 특정 인물이라고 가정
- 특정 인물 -> happy eigen face 변화를 50개 레벨로 세분화해서 표현
- 각 이미지의 가로/세로 padding을 2씩 넣어서 저장


```python
plt.imshow(h_imgs[0], cmap=plt.cm.bone)
cv2.imwrite('org.jpg', h_imgs[0]*255)
```




    True




![png](/assets/img/transfer.png)

------

