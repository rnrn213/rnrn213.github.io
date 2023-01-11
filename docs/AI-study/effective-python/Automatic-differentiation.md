---
layout: default
title: Automatic Differentiation
parent: Effective Python
grand_parent: AI study
nav_order: 2
---

Automatic Differentiation
=============
2.5 Automatic Differentiation을 읽고 pytorch를 사용한 automatic differentiation에 대해 더 조사해보았습니다.

<br/>

## Tensors, Functions

신경망을 학습하기 위해 Backpropagation(역전파) algorithm을 통해 주어진 매개 변수의 손실 함수의 gradient(변화도)를 구하고 이를 토대로 매개 변수를 조정합니다. 

우리가 최적화해야할 매개 변수의 손실 함수의 gradient를 계산하기 위해서는 해당 tensor의 requires_grad 속성을 True로 설정해야 합니다.

```python
x = torch.randn(5, 3, requires_grad=True)

y = torch.randn(3)
y.requires_grad_(True)
```

Function class의 객체는 forward(순전파) 방향에서의 함수와 역방향 전파 단계 동안 derivative(도함수)를 계산할 수 있습니다. 역방향 전파 함수에 대한 참조는 tensor의 grad_fn 속성에 저장됩니다.

```python
z = 2 * torch.dot(x, y)

z.grad_fn
```

```
<MulBackward0 at 0x1b2f626e310>
```

<br/>

## Computing Gradients

z.backward()를 호출함으로써 각 매개 변수에 대한 손실 함수의 도함수를 계산할 수 있습니다. x.grad와 y.grad로 그 값을 조회할 수 있습니다.

```python
z.backward()

print(x.grad)
print(y.grad)
```

```
tensor([ 6.3604,  2.5771,  1.9632, -0.3170, -1.4688])
tensor([-7.8855, -6.5292, -3.6108, -6.1005, -1.7767])
```

성능상의 이유로 주어진 그래프에서 backward를 사용한 gradient 계산은 한번만 수행할 수 있습니다. 같은 그래프에서 backward를 여러번 호출해야 하는 경우에는 backward 호출 시 retain_graph=True 를 넘겨주어야 합니다.

## Disabling Gradient Tracking

모델을 학습한 뒤 순전파 연산만 수행하고 싶을 때, 연산 코드를 torch.no_grad()로 감싸서 연산이 추적되는 것을 막을 수 있습니다.

```python
print(z.requires_grad)

with torch.no_grad():
    z = 2 * torch.dot(x, y)
print(z.requires_grad)
```

```
True
False
```

tensor의 detach() method를 사용하면 같은 역할을 수행할 수 있습니다.

```python
z = 2 * torch.dot(x, y)

z_det = z.detach()
print(z_det.requires_grad)
```

```
False
```

신경망에서 몇몇 매개변수들을 frozen parameter로 표시하고 싶을 때,    
forward pass만 할 때 gradient를 추적하지 않는 것이 효율적이기 때문에 연산을 빠르게 하고 싶을 때에 gradient tracking을 비활성화하는 것이 좋습니다.

## More on Computational Graphs

autograd는 데이터(tensors), 모든 실행된 연산들(along with the resulting new tensors)를 Function object로 구성된 directed acyclic graph(DAG, 방향성 비순환 그래프)에 보관합니다. DAG에서 잎(leaves)은 입력 tensors이고, 뿌리(roots)는 출력 tensors입니다. 이 그래프를 뿌리에서 잎으로 따라가면, 자동으로 연쇄 법칙(chain rule)을 사용해 gradients를 계산할 수 있습니다.    
<br/>
forward pass에서, autograd는 두 가지를 동시에 수행합니다:    
* 결과 tensor를 계산하기 위해 요청된 연산을 수행합니다
* DAG에서 연산의 gradient function을 유지합니다.    

DAG 뿌리에서 .backward()가 호출되면 backward pass가 시작됩니다. 그러면 autograd는:    
* 각각의 .grad_fn으로부터 gradient를 계산합니다.
* 각각의 tensor의 .grad 속성에 gradient를 저장합니다.
* 연쇄 법칙을 사용해서, 모든 잎 tensor들에 gradient를 전파합니다.

<hr/>

<br/>

# 참고자료
<https://pytorch.org/tutorials/beginner/basics/autogradqs_tutorial.html>