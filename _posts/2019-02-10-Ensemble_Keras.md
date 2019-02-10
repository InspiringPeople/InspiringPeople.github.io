---
category: Data Analysis
title: Keras에서 Model Averaging을 통해 variance 낮추는 방법
tags: [Ensemble, Keras]

---
# Model Averaging을 통한 Ensemble 방법



딥러닝 모델은 데이터가 선형으로 나누어 떨어지지 않더라도 유연하게 데이터를 학습할 수 있는 유연성을 가지고 있다. 하지만 때로 이러한 유연성 때문에 모델의 복잡도가 높아질 수 있고, 또는 같은 데이터를 가지고 같은 모델로 실행할 경우라도 여러 솔루션이 제공될 수 있다.  

Model Averaging은 최종 딥러닝 모델의 분산을 줄이기 위한 앙승블 학습 기법으로, 여러 모델을 평균화하여 결과에 대한 분산을 줄이는 것을 목적으로 한다. 다음은 Keras를 이용하여 Model Averaging 기법을 사용하여 모델의 분산도를 줄이는 방법을 알아본다.



## Multi-Class Dataset 만들기
scikit-learn의 make_blobs() function을 이용하여 multi-class classification을 위한 데이터 셋을 생성한다.


```python
# scatter plot of blobs dataset
from sklearn.datasets.samples_generator import make_blobs
from matplotlib import pyplot
from pandas import DataFrame
# generate 2d classification dataset
X, y = make_blobs(n_samples=500, centers=3, n_features=2, cluster_std=2, random_state=2)
# scatter plot, dots colored by class value
df = DataFrame(dict(x=X[:,0], y=X[:,1], label=y))
colors = {0:'red', 1:'blue', 2:'green'}
fig, ax = pyplot.subplots()
grouped = df.groupby('label')
for key, group in grouped:
    group.plot(ax=ax, kind='scatter', x='x', y='y', label=key, color=colors[key])
pyplot.show()
```


![png](/assets/img/output_3_0.png)


위의 scatter plot이 생성된 dataset의 그림이다. standard deviation 2.0으로 각 클래스 라벨은 선형으로 분리되지 않는다. 이는 딥러닝 모델이 각기 다른 결과를 충분히 낼 수 있도록 모호한 점들을 의도적으로 생성한 것이다. 결국 이러한 point는 모델의 high variance를 이끌어낼 수 있다.

## MLP Model for Multi-Class Classification


```python
# fit high variance mlp on blobs classification problem
from sklearn.datasets.samples_generator import make_blobs
from keras.utils import to_categorical
from keras.models import Sequential
from keras.layers import Dense
from matplotlib import pyplot
# generate 2d classification dataset
X, y = make_blobs(n_samples=500, centers=3, n_features=2, cluster_std=2, random_state=2)
y = to_categorical(y)
# split into train and test
n_train = int(0.3 * X.shape[0])
trainX, testX = X[:n_train, :], X[n_train:, :]
trainy, testy = y[:n_train], y[n_train:]
# define model
model = Sequential()
model.add(Dense(15, input_dim=2, activation='relu'))
model.add(Dense(3, activation='softmax'))
model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
# fit model
history = model.fit(trainX, trainy, validation_data=(testX, testy), epochs=200, verbose=0)
# evaluate the model
_, train_acc = model.evaluate(trainX, trainy, verbose=0)
_, test_acc = model.evaluate(testX, testy, verbose=0)
print('Train: %.3f, Test: %.3f' % (train_acc, test_acc))
# learning curves of model accuracy
pyplot.plot(history.history['acc'], label='train')
pyplot.plot(history.history['val_acc'], label='test')
pyplot.legend()
pyplot.show()
```

    Using TensorFlow backend.
    

    Train: 0.833, Test: 0.774
    


![png](/assets/img/output_6_2.png)


30%의 training data, 70%의 test data로 훈련한 모델의 결과는 84% (training data), 76% (test data)이다. 

## High Variance of MLP Model
의도적으로 예측시에 high variance를 가지는 모델을 구현하기 위해 fit / evaulate function을 같은 데이터셋, 같은 모델 configuration을 가지고 반복적으로 실행한 후 final performance를 측정한다.


```python
# demonstrate high variance of mlp model on blobs classification problem
from sklearn.datasets.samples_generator import make_blobs
from keras.utils import to_categorical
from keras.models import Sequential
from keras.layers import Dense
from numpy import mean
from numpy import std
from matplotlib import pyplot

# fit and evaluate a neural net model on the dataset
def evaluate_model(trainX, trainy, testX, testy):
	# define model
	model = Sequential()
	model.add(Dense(15, input_dim=2, activation='relu'))
	model.add(Dense(3, activation='softmax'))
	model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
	# fit model
	model.fit(trainX, trainy, epochs=200, verbose=0)
	# evaluate the model
	_, test_acc = model.evaluate(testX, testy, verbose=0)
	return test_acc

# generate 2d classification dataset
X, y = make_blobs(n_samples=500, centers=3, n_features=2, cluster_std=2, random_state=2)
y = to_categorical(y)
# split into train and test
n_train = int(0.3 * X.shape[0])
trainX, testX = X[:n_train, :], X[n_train:, :]
trainy, testy = y[:n_train], y[n_train:]
# repeated evaluation
n_repeats = 30
scores = list()
for _ in range(n_repeats):
	score = evaluate_model(trainX, trainy, testX, testy)
	print('> %.3f' % score)
	scores.append(score)
# summarize the distribution of scores
print('Scores Mean: %.3f, Standard Deviation: %.3f' % (mean(scores), std(scores)))
# histogram of distribution
pyplot.hist(scores, bins=10)
pyplot.show()
# boxplot of distribution
pyplot.boxplot(scores)
pyplot.show()
```

    > 0.743
    > 0.774
    > 0.766
    > 0.757
    > 0.760
    > 0.769
    > 0.751
    > 0.737
    > 0.774
    > 0.763
    > 0.777
    > 0.777
    > 0.777
    > 0.757
    > 0.780
    > 0.763
    > 0.751
    > 0.766
    > 0.803
    > 0.774
    > 0.763
    > 0.783
    > 0.780
    > 0.786
    > 0.751
    > 0.749
    > 0.769
    > 0.743
    > 0.760
    > 0.769
    Scores Mean: 0.766, Standard Deviation: 0.014
    


![png](/assets/img/output_9_1.png)



![png](/assets/img/output_9_2.png)


결과적으로 77% Accuracy, 1.4% standard deviation이 나왔다. (Gaussian distribution)

## Model Averaging Ensemble
모델 error와 viariance를 줄이기 위한 model averaging 방법이다. 특히, training set의 performance를 높이고 test set의 standard deviation 값을 줄일 수 있을 것이다.


```python
# model averaging ensemble and a study of ensemble size on test accuracy
from sklearn.datasets.samples_generator import make_blobs
from keras.utils import to_categorical
from keras.models import Sequential
from keras.layers import Dense
import numpy
from numpy import array
from numpy import argmax
from sklearn.metrics import accuracy_score
from matplotlib import pyplot

# fit model on dataset
def fit_model(trainX, trainy):
	# define model
	model = Sequential()
	model.add(Dense(15, input_dim=2, activation='relu'))
	model.add(Dense(3, activation='softmax'))
	model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
	# fit model
	model.fit(trainX, trainy, epochs=200, verbose=0)
	return model

# make an ensemble prediction for multi-class classification
def ensemble_predictions(members, testX):
	# make predictions
	yhats = [model.predict(testX) for model in members]
	yhats = array(yhats)
	# sum across ensemble members
	summed = numpy.sum(yhats, axis=0)
	# argmax across classes
	result = argmax(summed, axis=1)
	return result

# evaluate a specific number of members in an ensemble
def evaluate_n_members(members, n_members, testX, testy):
	# select a subset of members
	subset = members[:n_members]
	print(len(subset))
	# make prediction
	yhat = ensemble_predictions(subset, testX)
	# calculate accuracy
	return accuracy_score(testy, yhat)

# generate 2d classification dataset
X, y = make_blobs(n_samples=500, centers=3, n_features=2, cluster_std=2, random_state=2)
# split into train and test
n_train = int(0.3 * X.shape[0])
trainX, testX = X[:n_train, :], X[n_train:, :]
trainy, testy = y[:n_train], y[n_train:]
trainy = to_categorical(trainy)
# fit all models
n_members = 20
members = [fit_model(trainX, trainy) for _ in range(n_members)]
# evaluate different numbers of ensembles
scores = list()
for i in range(1, n_members+1):
	score = evaluate_n_members(members, i, testX, testy)
	print('> %.3f' % score)
	scores.append(score)
# plot score vs number of ensemble members
x_axis = [i for i in range(1, n_members+1)]
pyplot.plot(x_axis, scores)
pyplot.show()
```

    1
    > 0.749
    2
    > 0.751
    3
    > 0.751
    4
    > 0.763
    5
    > 0.760
    6
    > 0.766
    7
    > 0.763
    8
    > 0.769
    9
    > 0.771
    10
    > 0.766
    11
    > 0.769
    12
    > 0.771
    13
    > 0.771
    14
    > 0.771
    15
    > 0.769
    16
    > 0.769
    17
    > 0.769
    18
    > 0.769
    19
    > 0.769
    20
    > 0.769
    


![png](/assets/img/output_12_1.png)


같은 training dataset을 가지고 20개의 모델을 훈련한 경우이다. 각 모델에 대한 test accuracy의 그래프를 보면 5개의 모델을 ensemble 했을 때 76% 정도로 정확도가 향상하는 것을 볼 수 있다.


5개의 모델을 ensemble 했을 때 정확도와 variance를 살펴보면 다음과 같다.


```python
# repeated evaluation of model averaging ensemble on blobs dataset
from sklearn.datasets.samples_generator import make_blobs
from keras.utils import to_categorical
from keras.models import Sequential
from keras.layers import Dense
import numpy
from numpy import array
from numpy import argmax
from numpy import mean
from numpy import std
from sklearn.metrics import accuracy_score

# fit model on dataset
def fit_model(trainX, trainy):
	# define model
	model = Sequential()
	model.add(Dense(15, input_dim=2, activation='relu'))
	model.add(Dense(3, activation='softmax'))
	model.compile(loss='categorical_crossentropy', optimizer='adam', metrics=['accuracy'])
	# fit model
	model.fit(trainX, trainy, epochs=200, verbose=0)
	return model

# make an ensemble prediction for multi-class classification
def ensemble_predictions(members, testX):
	# make predictions
	yhats = [model.predict(testX) for model in members]
	yhats = array(yhats)
	# sum across ensemble members
	summed = numpy.sum(yhats, axis=0)
	# argmax across classes
	result = argmax(summed, axis=1)
	return result

# evaluate ensemble model
def evaluate_members(members, testX, testy):
	# make prediction
	yhat = ensemble_predictions(members, testX)
	# calculate accuracy
	return accuracy_score(testy, yhat)

# generate 2d classification dataset
X, y = make_blobs(n_samples=500, centers=3, n_features=2, cluster_std=2, random_state=2)
# split into train and test
n_train = int(0.3 * X.shape[0])
trainX, testX = X[:n_train, :], X[n_train:, :]
trainy, testy = y[:n_train], y[n_train:]
trainy = to_categorical(trainy)
# repeated evaluation
n_repeats = 30
n_members = 5
scores = list()
for _ in range(n_repeats):
	# fit all models
	members = [fit_model(trainX, trainy) for _ in range(n_members)]
	# evaluate ensemble
	score = evaluate_members(members, testX, testy)
	print('> %.3f' % score)
	scores.append(score)
# summarize the distribution of scores
print('Scores Mean: %.3f, Standard Deviation: %.3f' % (mean(scores), std(scores)))
```

    > 0.774
    > 0.769
    > 0.769
    > 0.766
    > 0.763
    > 0.734
    > 0.769
    > 0.774
    > 0.763
    > 0.771
    > 0.771
    > 0.771
    > 0.783
    > 0.789
    > 0.766
    > 0.777
    > 0.769
    > 0.766
    > 0.771
    > 0.766
    > 0.774
    > 0.766
    > 0.746
    > 0.771
    > 0.786
    > 0.774
    > 0.771
    > 0.746
    > 0.754
    > 0.760
    Scores Mean: 0.768, Standard Deviation: 0.011
    

test dataset의 정확도는 77%로 single model일 경우와 거의 차이가 없다.
여기서 주목할 만한 점은 standard deviation 값이 single model인 경우 1.4%, 5개의 ensemble 모델인 경우 0.6%로 0.8% 정도 감소하였다는 것이다. 이것은 model의 variance를 줄임으로써 test dataset이 변하더라도 신뢰성 있는 결과를 도출할 수 있음을 의미한다. 이렇게 variance 값이 낮은 모델은 높은 신뢰성으로 운영단에 적용해볼만하게 여겨지게 된다.

# Related Post
- https://inspiringpeople.github.io/data%20analysis/feature_selection/

# Reference  
- https://machinelearningmastery.com/model-averaging-ensemble-for-deep-learning-neural-networks/
