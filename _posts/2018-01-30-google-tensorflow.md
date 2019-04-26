---
layout: post
title:  "Tensorflow"
category: Python
tags: [AI]
---

Google Tensorflow의 원리를 파악하고
python을 이용한 실습을 해보자
<!-- more -->



**용어**

* * *

-오퍼레이션(Operation)

그래프 상의 노드는 오퍼레이션(줄임말 op)으로 불립니다. 오퍼레이션은 하나 이상의 텐서를 받을 수 있습니다. 오퍼레이션은 계산을 수행하고, 결과를 하나 이상의 텐서로 반환할 수 있습니다.

-텐서(Tensor)

내부적으로 모든 데이터는 텐서를 통해 표현됩니다. 텐서는 일종의 다차원 배열인데, 그래프 내의 오퍼레이션 간에는 텐서만이 전달됩니다. (Caffe의 Blob과 유사합니다.)

-세션(Session)

그래프를 실행하기 위해서는 세션 객체가 필요합니다. 세션은 오퍼레이션의 실행 환경을 캡슐화한 것입니다.

-변수(Variables)    

변수는 그래프의 실행시, 패러미터를 저장하고 갱신하는데 사용됩니다. 메모리 상에서 텐서를 저장하는 버퍼 역할을 합니다.


Tensorflow 프로그램은 computational graph 이다. 일련의 Tensorflow Operation을 노드들의 그래프로 구조화(시각화)한 것이다. 노드는 텐서를 입력으로 받아서 텐서를 출력한다.

**노드의 종류**

* * *

- constant 노드 : 변하지 않는 상수를 저장하는 노드이다.
```python
>>>node1 = tf.constant(3.0, tf.float32)
>>>node2 = tf.constant(4.0)
>>>print(node1, node2)
(<tf.Tensor 'Const:0' shape=() dtype=float32>, <tf.Tensor 'Const_1:0' shape=() dtype=float32>)
#노드가 실제 값을 가지려면 일련의 오퍼레이션을 노드들의 그래프로 구조화하여야 한다. 구조화(평가)하려면 세션(session)을 생성해 주면 된다.
>>> sess = tf.Session()
>>> print(sess.run([node1, node2]))
[3.0, 4.0]
```

- operation 노드 : 노드들의 연산이 저장되는 노드이다.
```python
>>> node3 = tf.add(node1, node2)
>>> print("node3: ", node3)
('node3: ', <tf.Tensor 'Add:0' shape=() dtype=float32>)
>>> print("sess.run(node3): ",sess.run(node3))
('sess.run(node3): ', 7.0)
```

- placeholder 노드 : 외부에서 입력 받을 수 있는 값을 저장하는 노드
```python
>>> a = tf.placeholder(tf.float32)
>>> b = tf.placeholder(tf.float32)
>>> adder_node = a + b  # + provides a shortcut for tf.add(a, b)
>>> a
(<tf.Tensor 'Placeholder:0' shape=<unknown> dtype=float32>)
>>> b
(<tf.Tensor 'Placeholder_1:0' shape=<unknown> dtype=float32>)
>>> adder_node
(<tf.Tensor 'add:0' shape=<unknown> dtype=float32>)
>>> print(sess.run(adder_node, {a: 3, b:4.5}))
7.5
>>> print(sess.run(adder_node, {a: [1,3], b: [2, 4]}))
[ 3.  7.]
```

**실습**

* * *

**<선형 방정식 W*x + b 의 computational graph 를 만들어 보자.>**

x는 입력되어야 하는 변수이므로 placeholder 노드를 사용한다.

```python
>>> x = tf.placeholder(tf.float32)
```
가중치 W와 바이어스 b는 placeholder와는 달리, 입력 텐서와 출력텐서가 다르다. 훈련을 통해 파라미터(가중치, 바이어스)는 학습해야 하며, 이 경우 Variable을 이용한다.

```python
>>> W = tf.Variable([.3], tf.float32)
>>> b = tf.Variable([-.3], tf.float32)
>>> linear_model = W * x + b
```

변수는 초기화해서 사용해야 하며, tf.global_variables_initializer()를 이용한다.
```python
>>> init = tf.global_variables_initializer()
>>> sess.run(init)
```
초기화된 변수 W와 b의 출력은 다음과 같다.
```python
>>> print(sess.run(W))
[ 0.30000001]
>>> print(sess.run(b))
[-0.30000001]
```

이제 입력 x = [1,2,3,4]를 이용하여 선형 방정식을 평가해 보자.
```python
>>> print(sess.run(linear_model, {x:[1,2,3,4]}))
[ 0.          0.30000001  0.60000002  0.90000004]
```
<br><br>
**<모델의 정확도 판별>**

모델의 예측값과 실제 결과값 사이의 차이를 평가한다. 이때 여기에서는 squared error 함수를 손실 함수로 사용하여 실습하겠다.

실제 결과값 y
```python
>>> y = tf.placeholder(tf.float32)
```
손실함수(squared error) : 실제 결과값과 예측값의 차이를 제곱하여 모든 테스트마다 더한다. (Y1-y1)^2 + (Y2-y2)^2 + ...

```python
>>> squared_deltas = tf.square(linear_model - y)
>>> loss = tf.reduce_sum(squared_deltas)
```

실제 y=[0, -1, -2, -3] 값을 이용하여 손실을 평가한다.

```python
>>> print(sess.run(loss, {x:[1,2,3,4], y:[0,-1,-2,-3]}))
23.66
```
loss 계산은 다음과 같다.
(0. ? 0.)^2 + (0.3 ? (-1))^2 + (0.6 ? (-2))^2 + (0.9 ? (-3))^2
= 0 + 1.69 + 6.76 + 15.2
= 23.66

실제 모델의 학습하려는 값은 W=1, b=-1 이다.
이를 넣어서 계산하여 보면

```python
>>> fixW = tf.assign(W, [-1.])
>>> fixb = tf.assign(b, [1.])
>>> sess.run([fixW, fixb])
[array([-1.], dtype=float32), array([ 1.], dtype=float32)]
>>> print(sess.run(loss, {x:[1,2,3,4], y:[0,-1,-2,-3]}))
0.0
```

손실은 0이 된다. 즉 정확한 예측이 된다.

<br><br>
**<학습(tf.train API)>**

실제 예측값으로 수렴하기 위해 프로그램을 계속 학습시켜야 한다.

W와 b가 진짜 값인 -1,1에 수렴하도록 학습시키는 것이다.

최적화 함수로 gradient descent를 사용한다.

```python
>>> optimizer = tf.train.GradientDescentOptimizer(0.01)
>>> train = optimizer.minimize(loss)
```

그리고 파라미터를 다시 랜덤값으로 초기화한 후, 반복 훈련시킨다.

```python
>>> sess.run(init)
>>> for i in range(1000):
...  sess.run(train, {x:[1,2,3,4], y:[0,-1,-2,-3]})
```

학습된 파라미터를 출력해 본다.

```python
>>> print(sess.run([W, b]))
[array([-0.9999969], dtype=float32), array([ 0.99999082], dtype=float32)]
```

W와 b가 각각 -1과 1에 근접했음을 확인할 수 있다. 현재까지 훈련된 파라미터와 손실을 출력해 보자.

```python
>>> curr_W, curr_b, curr_loss  = sess.run([W, b, loss], {x:[1,2,3,4], y:[0,-1,-2,-3]})
>>> print("W: %s b: %s loss: %s"%(curr_W, curr_b, curr_loss))
W: [-0.9999969] b: [ 0.99999082] loss: 5.69997e-11
```

W와 b의 값이 거의 -1과 1로 수렴하는 것을 볼 수 있다.

<br><br>
**<고수준 학습(tf.contrib.learn)>**

텐서플로우에서 제공하는 tf.contrib.learn을 이용하여 앞의 예제를 다시 만들어 보겠다.

```python
import tensorflow as tf
import numpy as np
 
# x를 1차원으로 정의한다.
features = [tf.contrib.layers.real_valued_column("x", dimension=1)]
 
# 훈련모델을 정의하는데, 여기서 훈련 모델은 linear regressor 를 사용한다.
estimator = tf.contrib.learn.LinearRegressor(feature_columns=features)
 
# 훈련 데이터셋과 배치사이즈, 에폭스를 정의한다.
x = np.array([1., 2., 3., 4.])
y = np.array([0., -1., -2., -3.])
input_fn = tf.contrib.learn.io.numpy_input_fn({"x":x}, y, batch_size=4, num_epochs=1000)
 
# 모델을 1000번 반복학습시킨다.
estimator.fit(input_fn=input_fn, steps=1000)
 
# 훈련된 모델을 평가한다.
estimator.evaluate(input_fn=input_fn)
```



