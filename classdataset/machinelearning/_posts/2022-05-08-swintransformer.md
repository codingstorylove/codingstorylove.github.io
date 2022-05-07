---
layout: single
title: "Swin Transformer: Hierarchical Vision Transformer using Shifted Windows 논문 코딩 과제"
date: "2022-05-08 00:08:22 +0900"
last_modified_at: "2022-05-08 00:08:22 +0900"
---

# Swin Transformer: Hierarchical Vision Transformer using Shifted Windows 논문 코딩 과제 

Ze Liu†* Yutong Lin†* Yue Cao* Han Hu*‡ Yixuan Wei†

Zheng Zhang Stephen Lin Baining Guo

Microsoft Research Asia

{v-zeliu1,v-yutlin,yuecao,hanhu,v-yixwe,zhez,stevelin,bainguo}@microsoft.com

# SwinTransformer 구조


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/머신러닝과제/20220507_193239_1.png")
```




![output_2_0]({{ site.gdrive_url_prefix }}1nd6_Gti1EaYILZ6lybhn1kPkgTL1F-zO)
    

1. 위 그림은 Swin Transformer 전체적인 구조이다. 크게 Patch Partition, Linear Embedding, Swin Transformer, Path Merging으로 구분이 되며 4개의 Stage로 이루어져 있다.

2. 핵심 아이디어인 Swin Transformer Block은 오른쪽 그림(b)에 보이는 것과 같이 두개의 encoder로 구성되어 있으며 일반적인 MSA(Multi head Self-Attention)이 아니라 W-MSA, SW-MAS로 이루어져 있다.

3. 각 stage 아래에 적혀있는 x2/x2/x6/x2 은 Swin Transformer Block의 횟수인데 1개의 Block당 2개의 encoder가 붙어 있으므로 세트로 묶어서 실제로는 1/1/3/1 개의 Block이 반복된다고 보면 된다.

4. 각 stage 위에 적혀있는 H/4 x W/4 x C 는 patch x patch x channel이며 48은 초기 patch size x channel (4x4x3)으로 구해졌으며 C는 base model인 Swin-T에서 96을 사용한다.


```python
! pip install swin-transformer-pytorch
```

    Collecting swin-transformer-pytorch
      Downloading swin_transformer_pytorch-0.4.1-py3-none-any.whl (11 kB)
    Collecting torch>=1.8.1
      Downloading torch-1.11.0-cp38-cp38-win_amd64.whl (158.0 MB)
         ------------------------------------- 158.0/158.0 MB 13.1 MB/s eta 0:00:00
    Collecting einops>=0.3.0
      Downloading einops-0.4.1-py3-none-any.whl (28 kB)
    Requirement already satisfied: typing-extensions in c:\users\mycom\anaconda3\lib\site-packages (from torch>=1.8.1->swin-transformer-pytorch) (3.10.0.0)
    Installing collected packages: einops, torch, swin-transformer-pytorch
    Successfully installed einops-0.4.1 swin-transformer-pytorch-0.4.1 torch-1.11.0
    

    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\appdata\roaming\python\python38\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\appdata\roaming\python\python38\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\appdata\roaming\python\python38\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\appdata\roaming\python\python38\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\appdata\roaming\python\python38\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    

# Swin-Tiny에 대한 구현 코드

1. import torch

> 신경망은 torch.nn 패키지를 사용하여 생성할 수 있다,

2. from einops

> rearrange: 요소를 재정렬 해주는 라이브러리 임포트 해준다.

> height, width 바꿔준다.

> 즉, 처음 두축 (차원)을 바꿔준다.




# 전체 코드 


```python
import torch
from torch import nn, einsum
import numpy as np
from einops import rearrange, repeat


class CyclicShift(nn.Module):
    def __init__(self, displacement):
        super().__init__()
        self.displacement = displacement

    def forward(self, x):
        return torch.roll(x, shifts=(self.displacement, self.displacement), dims=(1, 2))


class Residual(nn.Module):
    def __init__(self, fn):
        super().__init__()
        self.fn = fn

    def forward(self, x, **kwargs):
        return self.fn(x, **kwargs) + x


class PreNorm(nn.Module):
    def __init__(self, dim, fn):
        super().__init__()
        self.norm = nn.LayerNorm(dim)
        self.fn = fn

    def forward(self, x, **kwargs):
        return self.fn(self.norm(x), **kwargs)


class FeedForward(nn.Module):
    def __init__(self, dim, hidden_dim):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(dim, hidden_dim),
            nn.GELU(),
            nn.Linear(hidden_dim, dim),
        )

    def forward(self, x):
        return self.net(x)


def create_mask(window_size, displacement, upper_lower, left_right):
    mask = torch.zeros(window_size ** 2, window_size ** 2)

    if upper_lower:
        mask[-displacement * window_size:, :-displacement * window_size] = float('-inf')
        mask[:-displacement * window_size, -displacement * window_size:] = float('-inf')

    if left_right:   # mask 를 einops 라이브러리 사용하여 새로운 차원으로 만들어준다.
        mask = rearrange(mask, '(h1 w1) (h2 w2) -> h1 w1 h2 w2', h1=window_size, h2=window_size)
        mask[:, -displacement:, :, :-displacement] = float('-inf')
        mask[:, :-displacement, :, -displacement:] = float('-inf')
        mask = rearrange(mask, 'h1 w1 h2 w2 -> (h1 w1) (h2 w2)')

    return mask


def get_relative_distances(window_size):
    indices = torch.tensor(np.array([[x, y] for x in range(window_size) for y in range(window_size)]))
    distances = indices[None, :, :] - indices[:, None, :]
    return distances


class WindowAttention(nn.Module):
    def __init__(self, dim, heads, head_dim, shifted, window_size, relative_pos_embedding):
        super().__init__()
        inner_dim = head_dim * heads

        self.heads = heads
        self.scale = head_dim ** -0.5
        self.window_size = window_size
        self.relative_pos_embedding = relative_pos_embedding
        self.shifted = shifted

        if self.shifted:
            displacement = window_size // 2
            self.cyclic_shift = CyclicShift(-displacement)
            self.cyclic_back_shift = CyclicShift(displacement)
            self.upper_lower_mask = nn.Parameter(create_mask(window_size=window_size, displacement=displacement,
                                                             upper_lower=True, left_right=False), requires_grad=False)
            self.left_right_mask = nn.Parameter(create_mask(window_size=window_size, displacement=displacement,
                                                            upper_lower=False, left_right=True), requires_grad=False)

        self.to_qkv = nn.Linear(dim, inner_dim * 3, bias=False)

        if self.relative_pos_embedding:
            self.relative_indices = get_relative_distances(window_size) + window_size - 1
            self.pos_embedding = nn.Parameter(torch.randn(2 * window_size - 1, 2 * window_size - 1))
        else:
            self.pos_embedding = nn.Parameter(torch.randn(window_size ** 2, window_size ** 2))

        self.to_out = nn.Linear(inner_dim, dim)

    def forward(self, x):
        if self.shifted:
            x = self.cyclic_shift(x)

        b, n_h, n_w, _, h = *x.shape, self.heads

        qkv = self.to_qkv(x).chunk(3, dim=-1)
        nw_h = n_h // self.window_size
        nw_w = n_w // self.window_size

        q, k, v = map(
            lambda t: rearrange(t, 'b (nw_h w_h) (nw_w w_w) (h d) -> b h (nw_h nw_w) (w_h w_w) d',
                                h=h, w_h=self.window_size, w_w=self.window_size), qkv)

        dots = einsum('b h w i d, b h w j d -> b h w i j', q, k) * self.scale

        if self.relative_pos_embedding:
            dots += self.pos_embedding[self.relative_indices[:, :, 0], self.relative_indices[:, :, 1]]
        else:
            dots += self.pos_embedding

        if self.shifted:
            dots[:, :, -nw_w:] += self.upper_lower_mask
            dots[:, :, nw_w - 1::nw_w] += self.left_right_mask

        attn = dots.softmax(dim=-1)

        out = einsum('b h w i j, b h w j d -> b h w i d', attn, v)
        out = rearrange(out, 'b h (nw_h nw_w) (w_h w_w) d -> b (nw_h w_h) (nw_w w_w) (h d)',
                        h=h, w_h=self.window_size, w_w=self.window_size, nw_h=nw_h, nw_w=nw_w)
        out = self.to_out(out)

        if self.shifted:
            out = self.cyclic_back_shift(out)
        return out


class SwinBlock(nn.Module):
    def __init__(self, dim, heads, head_dim, mlp_dim, shifted, window_size, relative_pos_embedding):
        super().__init__()
        self.attention_block = Residual(PreNorm(dim, WindowAttention(dim=dim,
                                                                     heads=heads,
                                                                     head_dim=head_dim,
                                                                     shifted=shifted,
                                                                     window_size=window_size,
                                                                     relative_pos_embedding=relative_pos_embedding)))
        self.mlp_block = Residual(PreNorm(dim, FeedForward(dim=dim, hidden_dim=mlp_dim)))

    def forward(self, x):
        x = self.attention_block(x)
        x = self.mlp_block(x)
        return x


class PatchMerging(nn.Module):
    def __init__(self, in_channels, out_channels, downscaling_factor):
        super().__init__()
        self.downscaling_factor = downscaling_factor
        self.patch_merge = nn.Unfold(kernel_size=downscaling_factor, stride=downscaling_factor, padding=0)
        self.linear = nn.Linear(in_channels * downscaling_factor ** 2, out_channels)

    def forward(self, x):
        b, c, h, w = x.shape
        new_h, new_w = h // self.downscaling_factor, w // self.downscaling_factor
        x = self.patch_merge(x).view(b, -1, new_h, new_w).permute(0, 2, 3, 1)
        x = self.linear(x)
        return x


class StageModule(nn.Module):
    def __init__(self, in_channels, hidden_dimension, layers, downscaling_factor, num_heads, head_dim, window_size,
                 relative_pos_embedding):
        super().__init__()
        assert layers % 2 == 0, 'Stage layers need to be divisible by 2 for regular and shifted block.'

        self.patch_partition = PatchMerging(in_channels=in_channels, out_channels=hidden_dimension,
                                            downscaling_factor=downscaling_factor)

        self.layers = nn.ModuleList([])
        for _ in range(layers // 2):
            self.layers.append(nn.ModuleList([
                SwinBlock(dim=hidden_dimension, heads=num_heads, head_dim=head_dim, mlp_dim=hidden_dimension * 4,
                          shifted=False, window_size=window_size, relative_pos_embedding=relative_pos_embedding),
                SwinBlock(dim=hidden_dimension, heads=num_heads, head_dim=head_dim, mlp_dim=hidden_dimension * 4,
                          shifted=True, window_size=window_size, relative_pos_embedding=relative_pos_embedding),
            ]))

    def forward(self, x):
        x = self.patch_partition(x)
        for regular_block, shifted_block in self.layers:
            x = regular_block(x)
            x = shifted_block(x)
        return x.permute(0, 3, 1, 2)


class SwinTransformer(nn.Module):
    def __init__(self, *, hidden_dim, layers, heads, channels=3, num_classes=1000, head_dim=32, window_size=7,
                 downscaling_factors=(4, 2, 2, 2), relative_pos_embedding=True):
        super().__init__()

        self.stage1 = StageModule(in_channels=channels, hidden_dimension=hidden_dim, layers=layers[0],
                                  downscaling_factor=downscaling_factors[0], num_heads=heads[0], head_dim=head_dim,
                                  window_size=window_size, relative_pos_embedding=relative_pos_embedding)
        self.stage2 = StageModule(in_channels=hidden_dim, hidden_dimension=hidden_dim * 2, layers=layers[1],
                                  downscaling_factor=downscaling_factors[1], num_heads=heads[1], head_dim=head_dim,
                                  window_size=window_size, relative_pos_embedding=relative_pos_embedding)
        self.stage3 = StageModule(in_channels=hidden_dim * 2, hidden_dimension=hidden_dim * 4, layers=layers[2],
                                  downscaling_factor=downscaling_factors[2], num_heads=heads[2], head_dim=head_dim,
                                  window_size=window_size, relative_pos_embedding=relative_pos_embedding)
        self.stage4 = StageModule(in_channels=hidden_dim * 4, hidden_dimension=hidden_dim * 8, layers=layers[3],
                                  downscaling_factor=downscaling_factors[3], num_heads=heads[3], head_dim=head_dim,
                                  window_size=window_size, relative_pos_embedding=relative_pos_embedding)

        self.mlp_head = nn.Sequential(
            nn.LayerNorm(hidden_dim * 8),
            nn.Linear(hidden_dim * 8, num_classes)
        )

    def forward(self, img):
        x = self.stage1(img)
        x = self.stage2(x)
        x = self.stage3(x)
        x = self.stage4(x)
        x = x.mean(dim=[2, 3])
        return self.mlp_head(x)


def swin_t(hidden_dim=96, layers=(2, 2, 6, 2), heads=(3, 6, 12, 24), **kwargs):
    return SwinTransformer(hidden_dim=hidden_dim, layers=layers, heads=heads, **kwargs)


def swin_s(hidden_dim=96, layers=(2, 2, 18, 2), heads=(3, 6, 12, 24), **kwargs):
    return SwinTransformer(hidden_dim=hidden_dim, layers=layers, heads=heads, **kwargs)


def swin_b(hidden_dim=128, layers=(2, 2, 18, 2), heads=(4, 8, 16, 32), **kwargs):
    return SwinTransformer(hidden_dim=hidden_dim, layers=layers, heads=heads, **kwargs)


def swin_l(hidden_dim=192, layers=(2, 2, 18, 2), heads=(6, 12, 24, 48), **kwargs):
    return SwinTransformer(hidden_dim=hidden_dim, layers=layers, heads=heads, **kwargs)
```

# Cyclic shift

1. torch.roll을 사용하면 원하는 차원에 있는 값을 shift 할 수 있다.

> torch.roll(input, shifts, dims=None) → Tensor

>> x값이 입력텐서이다. 롤링 할 축을 (1,2)로 설정한다.

> 주어진 차원을 따라 텐서를 굴립니다. 마지막 위치를 넘어 이동 한 요소는 첫 번째 위치에 다시 도입됩니다.

> 각 차원별로 3씩 밀어버림

2. self.displacement = window_size // 2 , 몫을 반환




```python
class CyclicShift(nn.Module):
    def __init__(self, displacement):
        super().__init__()
        self.displacement = displacement

    def forward(self, x):
        return torch.roll(x, shifts=(self.displacement, self.displacement), dims=(1, 2)) 

```

# Skip Connection & Layer Normalization

1. 여기서 모듈이란 한 개 이상의 레이어가 모여서 구성된 것을 말한다. 

2. nn.Module : 모든 뉴럴 네트워크 모듈의 기본 클래스이다.  

> 일반적인 모델들은 이 클래스를 상속받아야한다. 모듈들은 다른 모듈을 또 포함할 수 있다.

> __init__() 메소드에는 신경망 레이어의 구성요소들을 정의하고, __forward__에서는 호출 될 때 수행되는 연산을 정의한다. torch.nn.Module을 상속받는 모든 클래스에서 override되어야 한다. 일반적으로 이 두가지 메소드는 반드시 정의한다.

> nn.Module 을 이용하여 리팩토링 하기

>> 더 명확하고 간결한 훈련 루프를 위해 nn.Module를 사용합니다. 우리는 nn.Module (자체가 클래스이고 상태를 추척할 수 있는) 하위 클래스(subclass)를 만듭니다. 이 경우에는, 포워드(forward) 단계에 대한 가중치, 절편, 그리고 메소드(method) 등을 유지하는 클래스를 만들고자 합니다. nn.Module 은 우리가 사용할 몇 가지 속성(attribute)과 메소드를 (.parameters() 와 .zero_grad() 같은) 가지고 있습니다.

2. **kwargs

> 딕셔너리 형태로 {'키워드':'특정 값'} 함수 내부로 전달됩니다.

3. super().__init__()이라는 코드가 nn.Module 부모클래스의 속성 및 메소드를 자동으로 불러와 해당 클래스에서도 사용이 가능하도록 해줍니다.

4. nn.LayerNorm는 문서 레이어 정규화에 설명 된대로 입력의 미니 배치인 dim 변수에 대해 레이어 정규화를 적용합니다.


```python
class Residual(nn.Module):
    def __init__(self, fn):
        super().__init__()
        self.fn = fn

    def forward(self, x, **kwargs):
        return self.fn(x, **kwargs) + x


class PreNorm(nn.Module):
    def __init__(self, dim, fn):
        super().__init__()
        self.norm = nn.LayerNorm(dim)
        self.fn = fn

    def forward(self, x, **kwargs):
        return self.fn(self.norm(x), **kwargs)
```

# MLP (Encoder의 MLP구현 )

1. MUlti-Layer Perceptron(다층 퍼셉트론) -> 두개이상 은닉층을 가지고 있다면 이 모델을 multilayer feedforward model or multilayer perceptron model(MLP)이라고 부른다. 두개 이상 은닉층이 필요하므로 nn.Linear 함수를 2번 지정해준다.

2. 사용함수 - nn.Sequential() 

3. PyTorch에서 Neural Network을 정의하고 사용하려면 Net이라는 class를 nn.Module을 상속받아 만든 뒤에 사용해야 한다

4. nn.Linear은 선형회귀 모델을 구현하기 위해 사용된다.

> nn.Linear(input_dim,output_dim)

> 입력되는 x의 차원과 출력되는 y의 차원을 입력해 주면 됩니다.
여기서는 입력으로 dim 변수를 출력으로 hidden_dim 변수를 넣어주었다.

> GELU 활성화 함수(Activation function(활성화 함수)는 말그대로 뉴런을 활성화하는 함수를 뜻한다. )를 사용합니다.

>> 이 함수는 dropout,zoneout,ReLU 함수의 특성을 조합하여 유도되었습니다.


```python
class FeedForward(nn.Module):
    def __init__(self, dim, hidden_dim):
        super().__init__()
        self.net = nn.Sequential(
            nn.Linear(dim, hidden_dim),
            nn.GELU(),
            nn.Linear(hidden_dim, dim),
        )

    def forward(self, x):
        return self.net(x)
```

# Mask


1. torch.zeros 함수로  window_size 제곱 크기인 행,열을 모두 0으로 만들어 준다. 따라서 0으로 채워진 tensor를 생성합니다.

2. CyclicShift 클래스에서 만든 displacement 변수 크기와 window_size 크기를 곱해 음의 무한대로 만들어 주어 upper_lower 크기를 정해준다.

> 만약 window_size가 3이고 displacement가 1이라면 아래 사진에서 upper_lower 마스크는 1과 같고 left_right는 2과 같다


3. mask 를 einops 라이브러리 사용하여 rearrange 함수로 새로운 차원으로 만들어준다.

> rearrange(mask, '(h1 w1) (h2 w2) -> h1 w1 h2 w2'


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/머신러닝과제/1.png")
```




    

    
![output_16_0]({{ site.gdrive_url_prefix }}1Uk1XnU5Lk4kGQuPIC_TGMnpG3cV_xtCo)


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/머신러닝과제/2.png")
```




    
![output_17_0]({{ site.gdrive_url_prefix }}1yDXy_4Ps4vz6l-u9uV3L7vXbLPi6DJbK)


```python
def create_mask(window_size, displacement, upper_lower, left_right):
    mask = torch.zeros(window_size ** 2, window_size ** 2)

    if upper_lower:
        mask[-displacement * window_size:, :-displacement * window_size] = float('-inf')
        mask[:-displacement * window_size, -displacement * window_size:] = float('-inf')

    if left_right:
        mask = rearrange(mask, '(h1 w1) (h2 w2) -> h1 w1 h2 w2', h1=window_size, h2=window_size)
        mask[:, -displacement:, :, :-displacement] = float('-inf')
        mask[:, :-displacement, :, -displacement:] = float('-inf')
        mask = rearrange(mask, 'h1 w1 h2 w2 -> (h1 w1) (h2 w2)')

    return mask
```

# Relative Distance

1. window size를 받아서 상대거리를 반환하는 함수 ( [−M + 1, M −1] 사이의 수)

2. Tensor란 Numpy의 ndarray와 같은 다차원 배열을 담는 자료구조이다.

> Tensor함수를 사용하여 window_size의  x행 y열 크기를 배열로 만들어 주었다.


```python
def get_relative_distances(window_size):
    indices = torch.tensor(np.array([[x, y] for x in range(window_size) for y in range(window_size)]))
    distances = indices[None, :, :] - indices[:, None, :]
    return distances
```

# W-MSA & SW-MSA

> Window based Multihead Self Attention (W-MSA)


1. Transformer의 Multi head Self-Attention을 수행하는 class로 block의 두번째 encoder인 SW-MSA 에서만 self.shifted=True가 되어서 cyclic shift + mask 를 진행한다.

2. relative_position_embedding을 query와 key값의 dot product를 scale로 나눈값에 더해준다.



```python
class WindowAttention(nn.Module):
    def __init__(self, dim, heads, head_dim, shifted, window_size, relative_pos_embedding):
        super().__init__()
        inner_dim = head_dim * heads

        self.heads = heads
        self.scale = head_dim ** -0.5
        self.window_size = window_size
        self.relative_pos_embedding = relative_pos_embedding
        self.shifted = shifted

        if self.shifted:
            displacement = window_size // 2  # 7//2 = 3
            self.cyclic_shift = CyclicShift(-displacement)
            self.cyclic_back_shift = CyclicShift(displacement)
            self.upper_lower_mask = nn.Parameter(create_mask(window_size=window_size, displacement=displacement,
                                                             upper_lower=True, left_right=False), requires_grad=False)
            self.left_right_mask = nn.Parameter(create_mask(window_size=window_size, displacement=displacement,
                                                            upper_lower=False, left_right=True), requires_grad=False)
        self.to_qkv = nn.Linear(dim, inner_dim * 3, bias=False)

        if self.relative_pos_embedding:
            # self.relative_indices -> index (0~12 사이의 수를 가짐) / + window_size - 1 은 음수를 없애기 위해
            self.relative_indices = get_relative_distances(window_size) + window_size - 1
            self.pos_embedding = nn.Parameter(torch.randn(2 * window_size - 1, 2 * window_size - 1))  # (13, 13)
        else:
            self.pos_embedding = nn.Parameter(torch.randn(window_size ** 2, window_size ** 2))

        self.to_out = nn.Linear(inner_dim, dim)

    def forward(self, x):
        if self.shifted:
            x = self.cyclic_shift(x)

        b, n_h, n_w, _, h = *x.shape, self.heads

        qkv = self.to_qkv(x).chunk(3, dim=-1)  # (b, 56, 56, 288) -> tuple len 3 (b, 56, 56, 96)
        nw_h = n_h // self.window_size  # 8
        nw_w = n_w // self.window_size  # 8

        q, k, v = map(
            lambda t: rearrange(t, 'b (nw_h w_h) (nw_w w_w) (h d) -> b h (nw_h nw_w) (w_h w_w) d',
                                h=h, w_h=self.window_size, w_w=self.window_size), qkv)

        # (b, 3, 64, 49, 32), (b, 3, 64, 49, 32)  -> (b, 3, 64, 49, 49)
        # query와 key사이의 연관성(dot product) * scale(1 / root(head_dim))
        dots = einsum('b h w i d, b h w j d -> b h w i j', q, k) * self.scale

        if self.relative_pos_embedding:
            dots += self.pos_embedding[self.relative_indices[:, :, 0].type(torch.long),
                                       self.relative_indices[:, :, 1].type(torch.long)]  # (49, 49)
        else:
            dots += self.pos_embedding
        if self.shifted:  # masking
            dots[:, :, -nw_w:] += self.upper_lower_mask  # 아래쪽 가로모양 윈도우
            dots[:, :, nw_w - 1::nw_w] += self.left_right_mask  # 오른쪽 세로모양 마스킹

        attn = dots.softmax(dim=-1)
        out = einsum('b h w i j, b h w j d -> b h w i d', attn, v)
        out = rearrange(out, 'b h (nw_h nw_w) (w_h w_w) d -> b (nw_h w_h) (nw_w w_w) (h d)',
                        h=h, w_h=self.window_size, w_w=self.window_size, nw_h=nw_h, nw_w=nw_w)
        out = self.to_out(out)

        if self.shifted:
            out = self.cyclic_back_shift(out)  # shift한 값을 원래 위치로
        return out
```

# Swin Transformer Block

1. Swin Transformer를 구성하는 한개의 Encoder에 대한 구현이다.


2. attention과 mlp 모두 Layer Normalization과 Skip Connection을 먼저 한다. (attention과 mlp가 뒤에 있다고 먼저 수행되는게 아님! (위에 Layer Normalization과 Residual 클래스 참고))

3. Residual 함수를 적용시켜 회귀 모델에서 잔차를 생성시켜 준다. 여기서 잔차는 실제값에서 예측값의 차이이다.

4. FeedForward 함수를 사용하여 FeedForward Neural Network를 생성시켜 줍니다.

> Multi-Layer Perceptron 차원을 은닉층 차원과 동일하게 설정해줍니다.

> 그리고 Residual 함수를 사용하여 잔차를 생성시켜준다.





```python
class SwinBlock(nn.Module):
    def __init__(self, dim, heads, head_dim, mlp_dim, shifted, window_size, relative_pos_embedding):
        super().__init__()
        self.attention_block = Residual(PreNorm(dim, WindowAttention(dim=dim,
                                                                     heads=heads,
                                                                     head_dim=head_dim,
                                                                     shifted=shifted,
                                                                     window_size=window_size,
                                                                     relative_pos_embedding=relative_pos_embedding)))
        self.mlp_block = Residual(PreNorm(dim, FeedForward(dim=dim, hidden_dim=mlp_dim)))

    def forward(self, x):
        x = self.attention_block(x)
        x = self.mlp_block(x)
        return x
```

# Patch Partition or Patch Merging & Linear Embedding

1. downscaling_factor는 [4, 2, 2, 2] 로 이루어져 있기 때문에 stage1에서는 이미지가 패치로 partition 되고 그 이후 stage는 자동으로 patch merging 역할을 한다.

2. Linear embedding에서 C는 각 stage에서 [96, 192, 384, 768]를 사용한다. 


3. b,c,h,w 바이어스,채널,높이,넓이를 변수로 설정해 모델을 구성해 준다.

4. linear 함수를 적용해 (b, 56, 56, 48) -> (b, 56, 56, 96) 넓이 길이를 2배로 키운다.

5. view 함수는 reshape 함수와 같은데 행렬을 변경해준다.

6. permute 함수는 모든 차원을 맞교환 할 수 있다.



```python
class PatchMerging(nn.Module):
    def __init__(self, in_channels, out_channels, downscaling_factor):
        super().__init__()
        self.downscaling_factor = downscaling_factor
        self.patch_merge = nn.Unfold(kernel_size=downscaling_factor, stride=downscaling_factor, padding=0)
        self.linear = nn.Linear(in_channels * downscaling_factor ** 2, out_channels)

    def forward(self, x):
        b, c, h, w = x.shape
        new_h, new_w = h // self.downscaling_factor, w // self.downscaling_factor  #  num patches (56 x 56)
        # self.patch_merge(x) : (b, 48, 3136)
        # self.patch_merge(x).view(b, -1, new_h, new_w) : (b, 48, 56, 56)
        # self.patch_merge(x).view(b, -1, new_h, new_w).permute(0, 2, 3, 1)  : (b, 56, 56, 48)
        x = self.patch_merge(x).view(b, -1, new_h, new_w).permute(0, 2, 3, 1)
        x = self.linear(x)  # (b, 56, 56, 48) -> (b, 56, 56, 96)
        return x
```

# StageModule

1. 각 스테이지는 Patch partition(merging)과 Swin Block으로 이루어져 있으며 stage3에서는 Swin Block이 세번 반복된다.

2. assert 가설설정문으로 어떤 조건이 True임을 보증하기 위해서 사용하는 것입니다.

> assert [조건], [오류메시지]

> 조건이 True인 경우 그대로 코드 진행, False인 경우 오류메시지 발생 시킴

> layers % 2 == 0 참인 경우 그대로 코드 진행 , False인 경우 'Stage layers need to be divisible by 2 for regular and shifted block.' 오류메세지 발생 

3. PatchMerging 함수를 사용하여 patch를 합쳐준다.

4. nn.Sequential()과 비슷한 nn.ModuleList() 모듈이 있습니다.

> nn.ModuleList는 nn.Sequential과 마찬가지로 nn.Module의 list를 input으로 받는다.

5. patch_partition 함수를 사용하여 다음층으로 보낼때 패치를 분할합니다


```python
class StageModule(nn.Module):
    def __init__(self, in_channels, hidden_dimension, layers, downscaling_factor, num_heads, head_dim, window_size,
                 relative_pos_embedding):
        super().__init__()
        assert layers % 2 == 0, 'Stage layers need to be divisible by 2 for regular and shifted block.'

        self.patch_partition = PatchMerging(in_channels=in_channels, out_channels=hidden_dimension,
                                            downscaling_factor=downscaling_factor)

        self.layers = nn.ModuleList([])
        for _ in range(layers // 2):
            self.layers.append(nn.ModuleList([
                SwinBlock(dim=hidden_dimension, heads=num_heads, head_dim=head_dim, mlp_dim=hidden_dimension * 4,
                          shifted=False, window_size=window_size, relative_pos_embedding=relative_pos_embedding),
                SwinBlock(dim=hidden_dimension, heads=num_heads, head_dim=head_dim, mlp_dim=hidden_dimension * 4,
                          shifted=True, window_size=window_size, relative_pos_embedding=relative_pos_embedding),
            ]))

    def forward(self, x):
        x = self.patch_partition(x)
        for regular_block, shifted_block in self.layers:
            x = regular_block(x)
            x = shifted_block(x)
        return x.permute(0, 3, 1, 2)  # (4, 56, 56, 96) -> (4, 96, 56, 56)
```

# Swin Transformer

1. 4개의 stage를 거친 후에 나오는 7x7 patch를 average pooling (mean)을 하고 최종 classifier에 넣어 예측을 한다.

2. StageModule 함수를 적용시켜 각 stage 마다 채널의 수를 증가시키고 높이와 넓이의 크기를 작게 해준다.






```python
class SwinTransformer(nn.Module):
    def __init__(self, *, hidden_dim, layers, heads, channels=3, num_classes=1000, head_dim=32, window_size=7,
                 downscaling_factors=(4, 2, 2, 2), relative_pos_embedding=True):
        super().__init__()
        self.stage1 = StageModule(in_channels=channels, hidden_dimension=hidden_dim, layers=layers[0],
                                  downscaling_factor=downscaling_factors[0], num_heads=heads[0], head_dim=head_dim,
                                  window_size=window_size, relative_pos_embedding=relative_pos_embedding)
        # input shape
        self.stage2 = StageModule(in_channels=hidden_dim, hidden_dimension=hidden_dim * 2, layers=layers[1],
                                  downscaling_factor=downscaling_factors[1], num_heads=heads[1], head_dim=head_dim,
                                  window_size=window_size, relative_pos_embedding=relative_pos_embedding)

        self.stage3 = StageModule(in_channels=hidden_dim * 2, hidden_dimension=hidden_dim * 4, layers=layers[2],
                                  downscaling_factor=downscaling_factors[2], num_heads=heads[2], head_dim=head_dim,
                                  window_size=window_size, relative_pos_embedding=relative_pos_embedding)

        self.stage4 = StageModule(in_channels=hidden_dim * 4, hidden_dimension=hidden_dim * 8, layers=layers[3],
                                  downscaling_factor=downscaling_factors[3], num_heads=heads[3], head_dim=head_dim,
                                  window_size=window_size, relative_pos_embedding=relative_pos_embedding)

        self.mlp_head = nn.Sequential(
            nn.LayerNorm(hidden_dim * 8),
            nn.Linear(hidden_dim * 8, num_classes)
        )

    def forward(self, img):
        # image shape(b, 3, 224, 224)
        x = self.stage1(img)  # (b, 96, 56, 56)
        x = self.stage2(x)  # (b, 192, 28, 28)
        x = self.stage3(x)  # (b, 384, 14, 14)
        x = self.stage4(x)  # (b, 768, 7, 7)

        x = x.mean(dim=[2, 3])  # (b, 768)
        return self.mlp_head(x)  # (b, classes)
```

# Test Swin-Tiny

1. pdb 라이브러리 함수를 사용하여 대화형 소스 코드 디버거를 실행시켜줍니다.

2. ! pip install torchsummary 설치 후 input_size 변수에 channel,높이,넓이를 넣어준다. 


```python
!pip install torchsummary
```

    Collecting torchsummary
      Downloading torchsummary-1.5.1-py3-none-any.whl (2.8 kB)
    Installing collected packages: torchsummary
    Successfully installed torchsummary-1.5.1
    

    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\appdata\roaming\python\python38\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\appdata\roaming\python\python38\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\appdata\roaming\python\python38\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\appdata\roaming\python\python38\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -onlpy (c:\users\mycom\appdata\roaming\python\python38\site-packages)
    WARNING: Ignoring invalid distribution -pype1 (c:\users\mycom\anaconda3\lib\site-packages)
    WARNING: Ignoring invalid distribution -ensorflow-gpu (c:\users\mycom\anaconda3\lib\site-packages)
    


```python
def swin_t(hidden_dim=96, layers=(2, 2, 6, 2), heads=(3, 6, 12, 24), **kwargs):
    return SwinTransformer(hidden_dim=hidden_dim, layers=layers, heads=heads, **kwargs)

if __name__ == '__main__':
    import pdb
    from torchsummary import summary
    model = swin_t()
    x = torch.rand(4, 3, 224, 224)

    outputs = model(x)
    print(outputs.shape)

    summary(model, input_size=(3, 224, 224), device='CPU')
```

    torch.Size([4, 1000])
    ----------------------------------------------------------------
            Layer (type)               Output Shape         Param #
    ================================================================
                Unfold-1             [-1, 48, 3136]               0
                Linear-2           [-1, 56, 56, 96]           4,704
          PatchMerging-3           [-1, 56, 56, 96]               0
             LayerNorm-4           [-1, 56, 56, 96]             192
                Linear-5          [-1, 56, 56, 288]          27,648
                Linear-6           [-1, 56, 56, 96]           9,312
       WindowAttention-7           [-1, 56, 56, 96]               0
               PreNorm-8           [-1, 56, 56, 96]               0
              Residual-9           [-1, 56, 56, 96]               0
            LayerNorm-10           [-1, 56, 56, 96]             192
               Linear-11          [-1, 56, 56, 384]          37,248
                 GELU-12          [-1, 56, 56, 384]               0
               Linear-13           [-1, 56, 56, 96]          36,960
          FeedForward-14           [-1, 56, 56, 96]               0
              PreNorm-15           [-1, 56, 56, 96]               0
             Residual-16           [-1, 56, 56, 96]               0
            SwinBlock-17           [-1, 56, 56, 96]               0
            LayerNorm-18           [-1, 56, 56, 96]             192
          CyclicShift-19           [-1, 56, 56, 96]               0
               Linear-20          [-1, 56, 56, 288]          27,648
               Linear-21           [-1, 56, 56, 96]           9,312
          CyclicShift-22           [-1, 56, 56, 96]               0
      WindowAttention-23           [-1, 56, 56, 96]               0
              PreNorm-24           [-1, 56, 56, 96]               0
             Residual-25           [-1, 56, 56, 96]               0
            LayerNorm-26           [-1, 56, 56, 96]             192
               Linear-27          [-1, 56, 56, 384]          37,248
                 GELU-28          [-1, 56, 56, 384]               0
               Linear-29           [-1, 56, 56, 96]          36,960
          FeedForward-30           [-1, 56, 56, 96]               0
              PreNorm-31           [-1, 56, 56, 96]               0
             Residual-32           [-1, 56, 56, 96]               0
            SwinBlock-33           [-1, 56, 56, 96]               0
          StageModule-34           [-1, 96, 56, 56]               0
               Unfold-35             [-1, 384, 784]               0
               Linear-36          [-1, 28, 28, 192]          73,920
         PatchMerging-37          [-1, 28, 28, 192]               0
            LayerNorm-38          [-1, 28, 28, 192]             384
               Linear-39          [-1, 28, 28, 576]         110,592
               Linear-40          [-1, 28, 28, 192]          37,056
      WindowAttention-41          [-1, 28, 28, 192]               0
              PreNorm-42          [-1, 28, 28, 192]               0
             Residual-43          [-1, 28, 28, 192]               0
            LayerNorm-44          [-1, 28, 28, 192]             384
               Linear-45          [-1, 28, 28, 768]         148,224
                 GELU-46          [-1, 28, 28, 768]               0
               Linear-47          [-1, 28, 28, 192]         147,648
          FeedForward-48          [-1, 28, 28, 192]               0
              PreNorm-49          [-1, 28, 28, 192]               0
             Residual-50          [-1, 28, 28, 192]               0
            SwinBlock-51          [-1, 28, 28, 192]               0
            LayerNorm-52          [-1, 28, 28, 192]             384
          CyclicShift-53          [-1, 28, 28, 192]               0
               Linear-54          [-1, 28, 28, 576]         110,592
               Linear-55          [-1, 28, 28, 192]          37,056
          CyclicShift-56          [-1, 28, 28, 192]               0
      WindowAttention-57          [-1, 28, 28, 192]               0
              PreNorm-58          [-1, 28, 28, 192]               0
             Residual-59          [-1, 28, 28, 192]               0
            LayerNorm-60          [-1, 28, 28, 192]             384
               Linear-61          [-1, 28, 28, 768]         148,224
                 GELU-62          [-1, 28, 28, 768]               0
               Linear-63          [-1, 28, 28, 192]         147,648
          FeedForward-64          [-1, 28, 28, 192]               0
              PreNorm-65          [-1, 28, 28, 192]               0
             Residual-66          [-1, 28, 28, 192]               0
            SwinBlock-67          [-1, 28, 28, 192]               0
          StageModule-68          [-1, 192, 28, 28]               0
               Unfold-69             [-1, 768, 196]               0
               Linear-70          [-1, 14, 14, 384]         295,296
         PatchMerging-71          [-1, 14, 14, 384]               0
            LayerNorm-72          [-1, 14, 14, 384]             768
               Linear-73         [-1, 14, 14, 1152]         442,368
               Linear-74          [-1, 14, 14, 384]         147,840
      WindowAttention-75          [-1, 14, 14, 384]               0
              PreNorm-76          [-1, 14, 14, 384]               0
             Residual-77          [-1, 14, 14, 384]               0
            LayerNorm-78          [-1, 14, 14, 384]             768
               Linear-79         [-1, 14, 14, 1536]         591,360
                 GELU-80         [-1, 14, 14, 1536]               0
               Linear-81          [-1, 14, 14, 384]         590,208
          FeedForward-82          [-1, 14, 14, 384]               0
              PreNorm-83          [-1, 14, 14, 384]               0
             Residual-84          [-1, 14, 14, 384]               0
            SwinBlock-85          [-1, 14, 14, 384]               0
            LayerNorm-86          [-1, 14, 14, 384]             768
          CyclicShift-87          [-1, 14, 14, 384]               0
               Linear-88         [-1, 14, 14, 1152]         442,368
               Linear-89          [-1, 14, 14, 384]         147,840
          CyclicShift-90          [-1, 14, 14, 384]               0
      WindowAttention-91          [-1, 14, 14, 384]               0
              PreNorm-92          [-1, 14, 14, 384]               0
             Residual-93          [-1, 14, 14, 384]               0
            LayerNorm-94          [-1, 14, 14, 384]             768
               Linear-95         [-1, 14, 14, 1536]         591,360
                 GELU-96         [-1, 14, 14, 1536]               0
               Linear-97          [-1, 14, 14, 384]         590,208
          FeedForward-98          [-1, 14, 14, 384]               0
              PreNorm-99          [-1, 14, 14, 384]               0
            Residual-100          [-1, 14, 14, 384]               0
           SwinBlock-101          [-1, 14, 14, 384]               0
           LayerNorm-102          [-1, 14, 14, 384]             768
              Linear-103         [-1, 14, 14, 1152]         442,368
              Linear-104          [-1, 14, 14, 384]         147,840
     WindowAttention-105          [-1, 14, 14, 384]               0
             PreNorm-106          [-1, 14, 14, 384]               0
            Residual-107          [-1, 14, 14, 384]               0
           LayerNorm-108          [-1, 14, 14, 384]             768
              Linear-109         [-1, 14, 14, 1536]         591,360
                GELU-110         [-1, 14, 14, 1536]               0
              Linear-111          [-1, 14, 14, 384]         590,208
         FeedForward-112          [-1, 14, 14, 384]               0
             PreNorm-113          [-1, 14, 14, 384]               0
            Residual-114          [-1, 14, 14, 384]               0
           SwinBlock-115          [-1, 14, 14, 384]               0
           LayerNorm-116          [-1, 14, 14, 384]             768
         CyclicShift-117          [-1, 14, 14, 384]               0
              Linear-118         [-1, 14, 14, 1152]         442,368
              Linear-119          [-1, 14, 14, 384]         147,840
         CyclicShift-120          [-1, 14, 14, 384]               0
     WindowAttention-121          [-1, 14, 14, 384]               0
             PreNorm-122          [-1, 14, 14, 384]               0
            Residual-123          [-1, 14, 14, 384]               0
           LayerNorm-124          [-1, 14, 14, 384]             768
              Linear-125         [-1, 14, 14, 1536]         591,360
                GELU-126         [-1, 14, 14, 1536]               0
              Linear-127          [-1, 14, 14, 384]         590,208
         FeedForward-128          [-1, 14, 14, 384]               0
             PreNorm-129          [-1, 14, 14, 384]               0
            Residual-130          [-1, 14, 14, 384]               0
           SwinBlock-131          [-1, 14, 14, 384]               0
           LayerNorm-132          [-1, 14, 14, 384]             768
              Linear-133         [-1, 14, 14, 1152]         442,368
              Linear-134          [-1, 14, 14, 384]         147,840
     WindowAttention-135          [-1, 14, 14, 384]               0
             PreNorm-136          [-1, 14, 14, 384]               0
            Residual-137          [-1, 14, 14, 384]               0
           LayerNorm-138          [-1, 14, 14, 384]             768
              Linear-139         [-1, 14, 14, 1536]         591,360
                GELU-140         [-1, 14, 14, 1536]               0
              Linear-141          [-1, 14, 14, 384]         590,208
         FeedForward-142          [-1, 14, 14, 384]               0
             PreNorm-143          [-1, 14, 14, 384]               0
            Residual-144          [-1, 14, 14, 384]               0
           SwinBlock-145          [-1, 14, 14, 384]               0
           LayerNorm-146          [-1, 14, 14, 384]             768
         CyclicShift-147          [-1, 14, 14, 384]               0
              Linear-148         [-1, 14, 14, 1152]         442,368
              Linear-149          [-1, 14, 14, 384]         147,840
         CyclicShift-150          [-1, 14, 14, 384]               0
     WindowAttention-151          [-1, 14, 14, 384]               0
             PreNorm-152          [-1, 14, 14, 384]               0
            Residual-153          [-1, 14, 14, 384]               0
           LayerNorm-154          [-1, 14, 14, 384]             768
              Linear-155         [-1, 14, 14, 1536]         591,360
                GELU-156         [-1, 14, 14, 1536]               0
              Linear-157          [-1, 14, 14, 384]         590,208
         FeedForward-158          [-1, 14, 14, 384]               0
             PreNorm-159          [-1, 14, 14, 384]               0
            Residual-160          [-1, 14, 14, 384]               0
           SwinBlock-161          [-1, 14, 14, 384]               0
         StageModule-162          [-1, 384, 14, 14]               0
              Unfold-163             [-1, 1536, 49]               0
              Linear-164            [-1, 7, 7, 768]       1,180,416
        PatchMerging-165            [-1, 7, 7, 768]               0
           LayerNorm-166            [-1, 7, 7, 768]           1,536
              Linear-167           [-1, 7, 7, 2304]       1,769,472
              Linear-168            [-1, 7, 7, 768]         590,592
     WindowAttention-169            [-1, 7, 7, 768]               0
             PreNorm-170            [-1, 7, 7, 768]               0
            Residual-171            [-1, 7, 7, 768]               0
           LayerNorm-172            [-1, 7, 7, 768]           1,536
              Linear-173           [-1, 7, 7, 3072]       2,362,368
                GELU-174           [-1, 7, 7, 3072]               0
              Linear-175            [-1, 7, 7, 768]       2,360,064
         FeedForward-176            [-1, 7, 7, 768]               0
             PreNorm-177            [-1, 7, 7, 768]               0
            Residual-178            [-1, 7, 7, 768]               0
           SwinBlock-179            [-1, 7, 7, 768]               0
           LayerNorm-180            [-1, 7, 7, 768]           1,536
         CyclicShift-181            [-1, 7, 7, 768]               0
              Linear-182           [-1, 7, 7, 2304]       1,769,472
              Linear-183            [-1, 7, 7, 768]         590,592
         CyclicShift-184            [-1, 7, 7, 768]               0
     WindowAttention-185            [-1, 7, 7, 768]               0
             PreNorm-186            [-1, 7, 7, 768]               0
            Residual-187            [-1, 7, 7, 768]               0
           LayerNorm-188            [-1, 7, 7, 768]           1,536
              Linear-189           [-1, 7, 7, 3072]       2,362,368
                GELU-190           [-1, 7, 7, 3072]               0
              Linear-191            [-1, 7, 7, 768]       2,360,064
         FeedForward-192            [-1, 7, 7, 768]               0
             PreNorm-193            [-1, 7, 7, 768]               0
            Residual-194            [-1, 7, 7, 768]               0
           SwinBlock-195            [-1, 7, 7, 768]               0
         StageModule-196            [-1, 768, 7, 7]               0
           LayerNorm-197                  [-1, 768]           1,536
              Linear-198                 [-1, 1000]         769,000
    ================================================================
    Total params: 28,247,560
    Trainable params: 28,247,560
    Non-trainable params: 0
    ----------------------------------------------------------------
    Input size (MB): 0.57
    Forward/backward pass size (MB): 269.03
    Params size (MB): 107.76
    Estimated Total Size (MB): 377.37
    ----------------------------------------------------------------
    


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/머신러닝과제/3.png")
```




    

![output_34_0]({{ site.gdrive_url_prefix }}1jZrBZwfvQVb1fu1dI88NV_BoDjtJ9YW7)


# 그 밖에 swin 실행 결과


```python
from IPython.display import Image  # 주피터 노트북에 이미지 삽입
Image("C://Users/MyCom/jupyter-tutorial/머신러닝과제/20220508_000218_1.png")
```




![output_36_0]({{ site.gdrive_url_prefix }}1mYTxeq7557PbWsG_ujyrZWRcQowECci6)


```python
def swin_s(hidden_dim=96, layers=(2, 2, 18, 2), heads=(3, 6, 12, 24), **kwargs):
    return SwinTransformer(hidden_dim=hidden_dim, layers=layers, heads=heads, **kwargs)

if __name__ == '__main__':
    import pdb
    from torchsummary import summary
    model = swin_s()
    x = torch.rand(4, 3, 224, 224)

    outputs = model(x)
    print(outputs.shape)

    summary(model, input_size=(3, 224, 224), device='CPU')
```

    torch.Size([4, 1000])
    ----------------------------------------------------------------
            Layer (type)               Output Shape         Param #
    ================================================================
                Unfold-1             [-1, 48, 3136]               0
                Linear-2           [-1, 56, 56, 96]           4,704
          PatchMerging-3           [-1, 56, 56, 96]               0
             LayerNorm-4           [-1, 56, 56, 96]             192
                Linear-5          [-1, 56, 56, 288]          27,648
                Linear-6           [-1, 56, 56, 96]           9,312
       WindowAttention-7           [-1, 56, 56, 96]               0
               PreNorm-8           [-1, 56, 56, 96]               0
              Residual-9           [-1, 56, 56, 96]               0
            LayerNorm-10           [-1, 56, 56, 96]             192
               Linear-11          [-1, 56, 56, 384]          37,248
                 GELU-12          [-1, 56, 56, 384]               0
               Linear-13           [-1, 56, 56, 96]          36,960
          FeedForward-14           [-1, 56, 56, 96]               0
              PreNorm-15           [-1, 56, 56, 96]               0
             Residual-16           [-1, 56, 56, 96]               0
            SwinBlock-17           [-1, 56, 56, 96]               0
            LayerNorm-18           [-1, 56, 56, 96]             192
          CyclicShift-19           [-1, 56, 56, 96]               0
               Linear-20          [-1, 56, 56, 288]          27,648
               Linear-21           [-1, 56, 56, 96]           9,312
          CyclicShift-22           [-1, 56, 56, 96]               0
      WindowAttention-23           [-1, 56, 56, 96]               0
              PreNorm-24           [-1, 56, 56, 96]               0
             Residual-25           [-1, 56, 56, 96]               0
            LayerNorm-26           [-1, 56, 56, 96]             192
               Linear-27          [-1, 56, 56, 384]          37,248
                 GELU-28          [-1, 56, 56, 384]               0
               Linear-29           [-1, 56, 56, 96]          36,960
          FeedForward-30           [-1, 56, 56, 96]               0
              PreNorm-31           [-1, 56, 56, 96]               0
             Residual-32           [-1, 56, 56, 96]               0
            SwinBlock-33           [-1, 56, 56, 96]               0
          StageModule-34           [-1, 96, 56, 56]               0
               Unfold-35             [-1, 384, 784]               0
               Linear-36          [-1, 28, 28, 192]          73,920
         PatchMerging-37          [-1, 28, 28, 192]               0
            LayerNorm-38          [-1, 28, 28, 192]             384
               Linear-39          [-1, 28, 28, 576]         110,592
               Linear-40          [-1, 28, 28, 192]          37,056
      WindowAttention-41          [-1, 28, 28, 192]               0
              PreNorm-42          [-1, 28, 28, 192]               0
             Residual-43          [-1, 28, 28, 192]               0
            LayerNorm-44          [-1, 28, 28, 192]             384
               Linear-45          [-1, 28, 28, 768]         148,224
                 GELU-46          [-1, 28, 28, 768]               0
               Linear-47          [-1, 28, 28, 192]         147,648
          FeedForward-48          [-1, 28, 28, 192]               0
              PreNorm-49          [-1, 28, 28, 192]               0
             Residual-50          [-1, 28, 28, 192]               0
            SwinBlock-51          [-1, 28, 28, 192]               0
            LayerNorm-52          [-1, 28, 28, 192]             384
          CyclicShift-53          [-1, 28, 28, 192]               0
               Linear-54          [-1, 28, 28, 576]         110,592
               Linear-55          [-1, 28, 28, 192]          37,056
          CyclicShift-56          [-1, 28, 28, 192]               0
      WindowAttention-57          [-1, 28, 28, 192]               0
              PreNorm-58          [-1, 28, 28, 192]               0
             Residual-59          [-1, 28, 28, 192]               0
            LayerNorm-60          [-1, 28, 28, 192]             384
               Linear-61          [-1, 28, 28, 768]         148,224
                 GELU-62          [-1, 28, 28, 768]               0
               Linear-63          [-1, 28, 28, 192]         147,648
          FeedForward-64          [-1, 28, 28, 192]               0
              PreNorm-65          [-1, 28, 28, 192]               0
             Residual-66          [-1, 28, 28, 192]               0
            SwinBlock-67          [-1, 28, 28, 192]               0
          StageModule-68          [-1, 192, 28, 28]               0
               Unfold-69             [-1, 768, 196]               0
               Linear-70          [-1, 14, 14, 384]         295,296
         PatchMerging-71          [-1, 14, 14, 384]               0
            LayerNorm-72          [-1, 14, 14, 384]             768
               Linear-73         [-1, 14, 14, 1152]         442,368
               Linear-74          [-1, 14, 14, 384]         147,840
      WindowAttention-75          [-1, 14, 14, 384]               0
              PreNorm-76          [-1, 14, 14, 384]               0
             Residual-77          [-1, 14, 14, 384]               0
            LayerNorm-78          [-1, 14, 14, 384]             768
               Linear-79         [-1, 14, 14, 1536]         591,360
                 GELU-80         [-1, 14, 14, 1536]               0
               Linear-81          [-1, 14, 14, 384]         590,208
          FeedForward-82          [-1, 14, 14, 384]               0
              PreNorm-83          [-1, 14, 14, 384]               0
             Residual-84          [-1, 14, 14, 384]               0
            SwinBlock-85          [-1, 14, 14, 384]               0
            LayerNorm-86          [-1, 14, 14, 384]             768
          CyclicShift-87          [-1, 14, 14, 384]               0
               Linear-88         [-1, 14, 14, 1152]         442,368
               Linear-89          [-1, 14, 14, 384]         147,840
          CyclicShift-90          [-1, 14, 14, 384]               0
      WindowAttention-91          [-1, 14, 14, 384]               0
              PreNorm-92          [-1, 14, 14, 384]               0
             Residual-93          [-1, 14, 14, 384]               0
            LayerNorm-94          [-1, 14, 14, 384]             768
               Linear-95         [-1, 14, 14, 1536]         591,360
                 GELU-96         [-1, 14, 14, 1536]               0
               Linear-97          [-1, 14, 14, 384]         590,208
          FeedForward-98          [-1, 14, 14, 384]               0
              PreNorm-99          [-1, 14, 14, 384]               0
            Residual-100          [-1, 14, 14, 384]               0
           SwinBlock-101          [-1, 14, 14, 384]               0
           LayerNorm-102          [-1, 14, 14, 384]             768
              Linear-103         [-1, 14, 14, 1152]         442,368
              Linear-104          [-1, 14, 14, 384]         147,840
     WindowAttention-105          [-1, 14, 14, 384]               0
             PreNorm-106          [-1, 14, 14, 384]               0
            Residual-107          [-1, 14, 14, 384]               0
           LayerNorm-108          [-1, 14, 14, 384]             768
              Linear-109         [-1, 14, 14, 1536]         591,360
                GELU-110         [-1, 14, 14, 1536]               0
              Linear-111          [-1, 14, 14, 384]         590,208
         FeedForward-112          [-1, 14, 14, 384]               0
             PreNorm-113          [-1, 14, 14, 384]               0
            Residual-114          [-1, 14, 14, 384]               0
           SwinBlock-115          [-1, 14, 14, 384]               0
           LayerNorm-116          [-1, 14, 14, 384]             768
         CyclicShift-117          [-1, 14, 14, 384]               0
              Linear-118         [-1, 14, 14, 1152]         442,368
              Linear-119          [-1, 14, 14, 384]         147,840
         CyclicShift-120          [-1, 14, 14, 384]               0
     WindowAttention-121          [-1, 14, 14, 384]               0
             PreNorm-122          [-1, 14, 14, 384]               0
            Residual-123          [-1, 14, 14, 384]               0
           LayerNorm-124          [-1, 14, 14, 384]             768
              Linear-125         [-1, 14, 14, 1536]         591,360
                GELU-126         [-1, 14, 14, 1536]               0
              Linear-127          [-1, 14, 14, 384]         590,208
         FeedForward-128          [-1, 14, 14, 384]               0
             PreNorm-129          [-1, 14, 14, 384]               0
            Residual-130          [-1, 14, 14, 384]               0
           SwinBlock-131          [-1, 14, 14, 384]               0
           LayerNorm-132          [-1, 14, 14, 384]             768
              Linear-133         [-1, 14, 14, 1152]         442,368
              Linear-134          [-1, 14, 14, 384]         147,840
     WindowAttention-135          [-1, 14, 14, 384]               0
             PreNorm-136          [-1, 14, 14, 384]               0
            Residual-137          [-1, 14, 14, 384]               0
           LayerNorm-138          [-1, 14, 14, 384]             768
              Linear-139         [-1, 14, 14, 1536]         591,360
                GELU-140         [-1, 14, 14, 1536]               0
              Linear-141          [-1, 14, 14, 384]         590,208
         FeedForward-142          [-1, 14, 14, 384]               0
             PreNorm-143          [-1, 14, 14, 384]               0
            Residual-144          [-1, 14, 14, 384]               0
           SwinBlock-145          [-1, 14, 14, 384]               0
           LayerNorm-146          [-1, 14, 14, 384]             768
         CyclicShift-147          [-1, 14, 14, 384]               0
              Linear-148         [-1, 14, 14, 1152]         442,368
              Linear-149          [-1, 14, 14, 384]         147,840
         CyclicShift-150          [-1, 14, 14, 384]               0
     WindowAttention-151          [-1, 14, 14, 384]               0
             PreNorm-152          [-1, 14, 14, 384]               0
            Residual-153          [-1, 14, 14, 384]               0
           LayerNorm-154          [-1, 14, 14, 384]             768
              Linear-155         [-1, 14, 14, 1536]         591,360
                GELU-156         [-1, 14, 14, 1536]               0
              Linear-157          [-1, 14, 14, 384]         590,208
         FeedForward-158          [-1, 14, 14, 384]               0
             PreNorm-159          [-1, 14, 14, 384]               0
            Residual-160          [-1, 14, 14, 384]               0
           SwinBlock-161          [-1, 14, 14, 384]               0
           LayerNorm-162          [-1, 14, 14, 384]             768
              Linear-163         [-1, 14, 14, 1152]         442,368
              Linear-164          [-1, 14, 14, 384]         147,840
     WindowAttention-165          [-1, 14, 14, 384]               0
             PreNorm-166          [-1, 14, 14, 384]               0
            Residual-167          [-1, 14, 14, 384]               0
           LayerNorm-168          [-1, 14, 14, 384]             768
              Linear-169         [-1, 14, 14, 1536]         591,360
                GELU-170         [-1, 14, 14, 1536]               0
              Linear-171          [-1, 14, 14, 384]         590,208
         FeedForward-172          [-1, 14, 14, 384]               0
             PreNorm-173          [-1, 14, 14, 384]               0
            Residual-174          [-1, 14, 14, 384]               0
           SwinBlock-175          [-1, 14, 14, 384]               0
           LayerNorm-176          [-1, 14, 14, 384]             768
         CyclicShift-177          [-1, 14, 14, 384]               0
              Linear-178         [-1, 14, 14, 1152]         442,368
              Linear-179          [-1, 14, 14, 384]         147,840
         CyclicShift-180          [-1, 14, 14, 384]               0
     WindowAttention-181          [-1, 14, 14, 384]               0
             PreNorm-182          [-1, 14, 14, 384]               0
            Residual-183          [-1, 14, 14, 384]               0
           LayerNorm-184          [-1, 14, 14, 384]             768
              Linear-185         [-1, 14, 14, 1536]         591,360
                GELU-186         [-1, 14, 14, 1536]               0
              Linear-187          [-1, 14, 14, 384]         590,208
         FeedForward-188          [-1, 14, 14, 384]               0
             PreNorm-189          [-1, 14, 14, 384]               0
            Residual-190          [-1, 14, 14, 384]               0
           SwinBlock-191          [-1, 14, 14, 384]               0
           LayerNorm-192          [-1, 14, 14, 384]             768
              Linear-193         [-1, 14, 14, 1152]         442,368
              Linear-194          [-1, 14, 14, 384]         147,840
     WindowAttention-195          [-1, 14, 14, 384]               0
             PreNorm-196          [-1, 14, 14, 384]               0
            Residual-197          [-1, 14, 14, 384]               0
           LayerNorm-198          [-1, 14, 14, 384]             768
              Linear-199         [-1, 14, 14, 1536]         591,360
                GELU-200         [-1, 14, 14, 1536]               0
              Linear-201          [-1, 14, 14, 384]         590,208
         FeedForward-202          [-1, 14, 14, 384]               0
             PreNorm-203          [-1, 14, 14, 384]               0
            Residual-204          [-1, 14, 14, 384]               0
           SwinBlock-205          [-1, 14, 14, 384]               0
           LayerNorm-206          [-1, 14, 14, 384]             768
         CyclicShift-207          [-1, 14, 14, 384]               0
              Linear-208         [-1, 14, 14, 1152]         442,368
              Linear-209          [-1, 14, 14, 384]         147,840
         CyclicShift-210          [-1, 14, 14, 384]               0
     WindowAttention-211          [-1, 14, 14, 384]               0
             PreNorm-212          [-1, 14, 14, 384]               0
            Residual-213          [-1, 14, 14, 384]               0
           LayerNorm-214          [-1, 14, 14, 384]             768
              Linear-215         [-1, 14, 14, 1536]         591,360
                GELU-216         [-1, 14, 14, 1536]               0
              Linear-217          [-1, 14, 14, 384]         590,208
         FeedForward-218          [-1, 14, 14, 384]               0
             PreNorm-219          [-1, 14, 14, 384]               0
            Residual-220          [-1, 14, 14, 384]               0
           SwinBlock-221          [-1, 14, 14, 384]               0
           LayerNorm-222          [-1, 14, 14, 384]             768
              Linear-223         [-1, 14, 14, 1152]         442,368
              Linear-224          [-1, 14, 14, 384]         147,840
     WindowAttention-225          [-1, 14, 14, 384]               0
             PreNorm-226          [-1, 14, 14, 384]               0
            Residual-227          [-1, 14, 14, 384]               0
           LayerNorm-228          [-1, 14, 14, 384]             768
              Linear-229         [-1, 14, 14, 1536]         591,360
                GELU-230         [-1, 14, 14, 1536]               0
              Linear-231          [-1, 14, 14, 384]         590,208
         FeedForward-232          [-1, 14, 14, 384]               0
             PreNorm-233          [-1, 14, 14, 384]               0
            Residual-234          [-1, 14, 14, 384]               0
           SwinBlock-235          [-1, 14, 14, 384]               0
           LayerNorm-236          [-1, 14, 14, 384]             768
         CyclicShift-237          [-1, 14, 14, 384]               0
              Linear-238         [-1, 14, 14, 1152]         442,368
              Linear-239          [-1, 14, 14, 384]         147,840
         CyclicShift-240          [-1, 14, 14, 384]               0
     WindowAttention-241          [-1, 14, 14, 384]               0
             PreNorm-242          [-1, 14, 14, 384]               0
            Residual-243          [-1, 14, 14, 384]               0
           LayerNorm-244          [-1, 14, 14, 384]             768
              Linear-245         [-1, 14, 14, 1536]         591,360
                GELU-246         [-1, 14, 14, 1536]               0
              Linear-247          [-1, 14, 14, 384]         590,208
         FeedForward-248          [-1, 14, 14, 384]               0
             PreNorm-249          [-1, 14, 14, 384]               0
            Residual-250          [-1, 14, 14, 384]               0
           SwinBlock-251          [-1, 14, 14, 384]               0
           LayerNorm-252          [-1, 14, 14, 384]             768
              Linear-253         [-1, 14, 14, 1152]         442,368
              Linear-254          [-1, 14, 14, 384]         147,840
     WindowAttention-255          [-1, 14, 14, 384]               0
             PreNorm-256          [-1, 14, 14, 384]               0
            Residual-257          [-1, 14, 14, 384]               0
           LayerNorm-258          [-1, 14, 14, 384]             768
              Linear-259         [-1, 14, 14, 1536]         591,360
                GELU-260         [-1, 14, 14, 1536]               0
              Linear-261          [-1, 14, 14, 384]         590,208
         FeedForward-262          [-1, 14, 14, 384]               0
             PreNorm-263          [-1, 14, 14, 384]               0
            Residual-264          [-1, 14, 14, 384]               0
           SwinBlock-265          [-1, 14, 14, 384]               0
           LayerNorm-266          [-1, 14, 14, 384]             768
         CyclicShift-267          [-1, 14, 14, 384]               0
              Linear-268         [-1, 14, 14, 1152]         442,368
              Linear-269          [-1, 14, 14, 384]         147,840
         CyclicShift-270          [-1, 14, 14, 384]               0
     WindowAttention-271          [-1, 14, 14, 384]               0
             PreNorm-272          [-1, 14, 14, 384]               0
            Residual-273          [-1, 14, 14, 384]               0
           LayerNorm-274          [-1, 14, 14, 384]             768
              Linear-275         [-1, 14, 14, 1536]         591,360
                GELU-276         [-1, 14, 14, 1536]               0
              Linear-277          [-1, 14, 14, 384]         590,208
         FeedForward-278          [-1, 14, 14, 384]               0
             PreNorm-279          [-1, 14, 14, 384]               0
            Residual-280          [-1, 14, 14, 384]               0
           SwinBlock-281          [-1, 14, 14, 384]               0
           LayerNorm-282          [-1, 14, 14, 384]             768
              Linear-283         [-1, 14, 14, 1152]         442,368
              Linear-284          [-1, 14, 14, 384]         147,840
     WindowAttention-285          [-1, 14, 14, 384]               0
             PreNorm-286          [-1, 14, 14, 384]               0
            Residual-287          [-1, 14, 14, 384]               0
           LayerNorm-288          [-1, 14, 14, 384]             768
              Linear-289         [-1, 14, 14, 1536]         591,360
                GELU-290         [-1, 14, 14, 1536]               0
              Linear-291          [-1, 14, 14, 384]         590,208
         FeedForward-292          [-1, 14, 14, 384]               0
             PreNorm-293          [-1, 14, 14, 384]               0
            Residual-294          [-1, 14, 14, 384]               0
           SwinBlock-295          [-1, 14, 14, 384]               0
           LayerNorm-296          [-1, 14, 14, 384]             768
         CyclicShift-297          [-1, 14, 14, 384]               0
              Linear-298         [-1, 14, 14, 1152]         442,368
              Linear-299          [-1, 14, 14, 384]         147,840
         CyclicShift-300          [-1, 14, 14, 384]               0
     WindowAttention-301          [-1, 14, 14, 384]               0
             PreNorm-302          [-1, 14, 14, 384]               0
            Residual-303          [-1, 14, 14, 384]               0
           LayerNorm-304          [-1, 14, 14, 384]             768
              Linear-305         [-1, 14, 14, 1536]         591,360
                GELU-306         [-1, 14, 14, 1536]               0
              Linear-307          [-1, 14, 14, 384]         590,208
         FeedForward-308          [-1, 14, 14, 384]               0
             PreNorm-309          [-1, 14, 14, 384]               0
            Residual-310          [-1, 14, 14, 384]               0
           SwinBlock-311          [-1, 14, 14, 384]               0
           LayerNorm-312          [-1, 14, 14, 384]             768
              Linear-313         [-1, 14, 14, 1152]         442,368
              Linear-314          [-1, 14, 14, 384]         147,840
     WindowAttention-315          [-1, 14, 14, 384]               0
             PreNorm-316          [-1, 14, 14, 384]               0
            Residual-317          [-1, 14, 14, 384]               0
           LayerNorm-318          [-1, 14, 14, 384]             768
              Linear-319         [-1, 14, 14, 1536]         591,360
                GELU-320         [-1, 14, 14, 1536]               0
              Linear-321          [-1, 14, 14, 384]         590,208
         FeedForward-322          [-1, 14, 14, 384]               0
             PreNorm-323          [-1, 14, 14, 384]               0
            Residual-324          [-1, 14, 14, 384]               0
           SwinBlock-325          [-1, 14, 14, 384]               0
           LayerNorm-326          [-1, 14, 14, 384]             768
         CyclicShift-327          [-1, 14, 14, 384]               0
              Linear-328         [-1, 14, 14, 1152]         442,368
              Linear-329          [-1, 14, 14, 384]         147,840
         CyclicShift-330          [-1, 14, 14, 384]               0
     WindowAttention-331          [-1, 14, 14, 384]               0
             PreNorm-332          [-1, 14, 14, 384]               0
            Residual-333          [-1, 14, 14, 384]               0
           LayerNorm-334          [-1, 14, 14, 384]             768
              Linear-335         [-1, 14, 14, 1536]         591,360
                GELU-336         [-1, 14, 14, 1536]               0
              Linear-337          [-1, 14, 14, 384]         590,208
         FeedForward-338          [-1, 14, 14, 384]               0
             PreNorm-339          [-1, 14, 14, 384]               0
            Residual-340          [-1, 14, 14, 384]               0
           SwinBlock-341          [-1, 14, 14, 384]               0
         StageModule-342          [-1, 384, 14, 14]               0
              Unfold-343             [-1, 1536, 49]               0
              Linear-344            [-1, 7, 7, 768]       1,180,416
        PatchMerging-345            [-1, 7, 7, 768]               0
           LayerNorm-346            [-1, 7, 7, 768]           1,536
              Linear-347           [-1, 7, 7, 2304]       1,769,472
              Linear-348            [-1, 7, 7, 768]         590,592
     WindowAttention-349            [-1, 7, 7, 768]               0
             PreNorm-350            [-1, 7, 7, 768]               0
            Residual-351            [-1, 7, 7, 768]               0
           LayerNorm-352            [-1, 7, 7, 768]           1,536
              Linear-353           [-1, 7, 7, 3072]       2,362,368
                GELU-354           [-1, 7, 7, 3072]               0
              Linear-355            [-1, 7, 7, 768]       2,360,064
         FeedForward-356            [-1, 7, 7, 768]               0
             PreNorm-357            [-1, 7, 7, 768]               0
            Residual-358            [-1, 7, 7, 768]               0
           SwinBlock-359            [-1, 7, 7, 768]               0
           LayerNorm-360            [-1, 7, 7, 768]           1,536
         CyclicShift-361            [-1, 7, 7, 768]               0
              Linear-362           [-1, 7, 7, 2304]       1,769,472
              Linear-363            [-1, 7, 7, 768]         590,592
         CyclicShift-364            [-1, 7, 7, 768]               0
     WindowAttention-365            [-1, 7, 7, 768]               0
             PreNorm-366            [-1, 7, 7, 768]               0
            Residual-367            [-1, 7, 7, 768]               0
           LayerNorm-368            [-1, 7, 7, 768]           1,536
              Linear-369           [-1, 7, 7, 3072]       2,362,368
                GELU-370           [-1, 7, 7, 3072]               0
              Linear-371            [-1, 7, 7, 768]       2,360,064
         FeedForward-372            [-1, 7, 7, 768]               0
             PreNorm-373            [-1, 7, 7, 768]               0
            Residual-374            [-1, 7, 7, 768]               0
           SwinBlock-375            [-1, 7, 7, 768]               0
         StageModule-376            [-1, 768, 7, 7]               0
           LayerNorm-377                  [-1, 768]           1,536
              Linear-378                 [-1, 1000]         769,000
    ================================================================
    Total params: 49,527,304
    Trainable params: 49,527,304
    Non-trainable params: 0
    ----------------------------------------------------------------
    Input size (MB): 0.57
    Forward/backward pass size (MB): 427.52
    Params size (MB): 188.93
    Estimated Total Size (MB): 617.03
    ----------------------------------------------------------------
    


```python

def swin_b(hidden_dim=128, layers=(2, 2, 18, 2), heads=(4, 8, 16, 32), **kwargs):
    return SwinTransformer(hidden_dim=hidden_dim, layers=layers, heads=heads, **kwargs)

if __name__ == '__main__':
    import pdb
    from torchsummary import summary
    model = swin_b()
    x = torch.rand(4, 3, 224, 224)

    outputs = model(x)
    print(outputs.shape)

    summary(model, input_size=(3, 224, 224), device='CPU')
```

    torch.Size([4, 1000])
    ----------------------------------------------------------------
            Layer (type)               Output Shape         Param #
    ================================================================
                Unfold-1             [-1, 48, 3136]               0
                Linear-2          [-1, 56, 56, 128]           6,272
          PatchMerging-3          [-1, 56, 56, 128]               0
             LayerNorm-4          [-1, 56, 56, 128]             256
                Linear-5          [-1, 56, 56, 384]          49,152
                Linear-6          [-1, 56, 56, 128]          16,512
       WindowAttention-7          [-1, 56, 56, 128]               0
               PreNorm-8          [-1, 56, 56, 128]               0
              Residual-9          [-1, 56, 56, 128]               0
            LayerNorm-10          [-1, 56, 56, 128]             256
               Linear-11          [-1, 56, 56, 512]          66,048
                 GELU-12          [-1, 56, 56, 512]               0
               Linear-13          [-1, 56, 56, 128]          65,664
          FeedForward-14          [-1, 56, 56, 128]               0
              PreNorm-15          [-1, 56, 56, 128]               0
             Residual-16          [-1, 56, 56, 128]               0
            SwinBlock-17          [-1, 56, 56, 128]               0
            LayerNorm-18          [-1, 56, 56, 128]             256
          CyclicShift-19          [-1, 56, 56, 128]               0
               Linear-20          [-1, 56, 56, 384]          49,152
               Linear-21          [-1, 56, 56, 128]          16,512
          CyclicShift-22          [-1, 56, 56, 128]               0
      WindowAttention-23          [-1, 56, 56, 128]               0
              PreNorm-24          [-1, 56, 56, 128]               0
             Residual-25          [-1, 56, 56, 128]               0
            LayerNorm-26          [-1, 56, 56, 128]             256
               Linear-27          [-1, 56, 56, 512]          66,048
                 GELU-28          [-1, 56, 56, 512]               0
               Linear-29          [-1, 56, 56, 128]          65,664
          FeedForward-30          [-1, 56, 56, 128]               0
              PreNorm-31          [-1, 56, 56, 128]               0
             Residual-32          [-1, 56, 56, 128]               0
            SwinBlock-33          [-1, 56, 56, 128]               0
          StageModule-34          [-1, 128, 56, 56]               0
               Unfold-35             [-1, 512, 784]               0
               Linear-36          [-1, 28, 28, 256]         131,328
         PatchMerging-37          [-1, 28, 28, 256]               0
            LayerNorm-38          [-1, 28, 28, 256]             512
               Linear-39          [-1, 28, 28, 768]         196,608
               Linear-40          [-1, 28, 28, 256]          65,792
      WindowAttention-41          [-1, 28, 28, 256]               0
              PreNorm-42          [-1, 28, 28, 256]               0
             Residual-43          [-1, 28, 28, 256]               0
            LayerNorm-44          [-1, 28, 28, 256]             512
               Linear-45         [-1, 28, 28, 1024]         263,168
                 GELU-46         [-1, 28, 28, 1024]               0
               Linear-47          [-1, 28, 28, 256]         262,400
          FeedForward-48          [-1, 28, 28, 256]               0
              PreNorm-49          [-1, 28, 28, 256]               0
             Residual-50          [-1, 28, 28, 256]               0
            SwinBlock-51          [-1, 28, 28, 256]               0
            LayerNorm-52          [-1, 28, 28, 256]             512
          CyclicShift-53          [-1, 28, 28, 256]               0
               Linear-54          [-1, 28, 28, 768]         196,608
               Linear-55          [-1, 28, 28, 256]          65,792
          CyclicShift-56          [-1, 28, 28, 256]               0
      WindowAttention-57          [-1, 28, 28, 256]               0
              PreNorm-58          [-1, 28, 28, 256]               0
             Residual-59          [-1, 28, 28, 256]               0
            LayerNorm-60          [-1, 28, 28, 256]             512
               Linear-61         [-1, 28, 28, 1024]         263,168
                 GELU-62         [-1, 28, 28, 1024]               0
               Linear-63          [-1, 28, 28, 256]         262,400
          FeedForward-64          [-1, 28, 28, 256]               0
              PreNorm-65          [-1, 28, 28, 256]               0
             Residual-66          [-1, 28, 28, 256]               0
            SwinBlock-67          [-1, 28, 28, 256]               0
          StageModule-68          [-1, 256, 28, 28]               0
               Unfold-69            [-1, 1024, 196]               0
               Linear-70          [-1, 14, 14, 512]         524,800
         PatchMerging-71          [-1, 14, 14, 512]               0
            LayerNorm-72          [-1, 14, 14, 512]           1,024
               Linear-73         [-1, 14, 14, 1536]         786,432
               Linear-74          [-1, 14, 14, 512]         262,656
      WindowAttention-75          [-1, 14, 14, 512]               0
              PreNorm-76          [-1, 14, 14, 512]               0
             Residual-77          [-1, 14, 14, 512]               0
            LayerNorm-78          [-1, 14, 14, 512]           1,024
               Linear-79         [-1, 14, 14, 2048]       1,050,624
                 GELU-80         [-1, 14, 14, 2048]               0
               Linear-81          [-1, 14, 14, 512]       1,049,088
          FeedForward-82          [-1, 14, 14, 512]               0
              PreNorm-83          [-1, 14, 14, 512]               0
             Residual-84          [-1, 14, 14, 512]               0
            SwinBlock-85          [-1, 14, 14, 512]               0
            LayerNorm-86          [-1, 14, 14, 512]           1,024
          CyclicShift-87          [-1, 14, 14, 512]               0
               Linear-88         [-1, 14, 14, 1536]         786,432
               Linear-89          [-1, 14, 14, 512]         262,656
          CyclicShift-90          [-1, 14, 14, 512]               0
      WindowAttention-91          [-1, 14, 14, 512]               0
              PreNorm-92          [-1, 14, 14, 512]               0
             Residual-93          [-1, 14, 14, 512]               0
            LayerNorm-94          [-1, 14, 14, 512]           1,024
               Linear-95         [-1, 14, 14, 2048]       1,050,624
                 GELU-96         [-1, 14, 14, 2048]               0
               Linear-97          [-1, 14, 14, 512]       1,049,088
          FeedForward-98          [-1, 14, 14, 512]               0
              PreNorm-99          [-1, 14, 14, 512]               0
            Residual-100          [-1, 14, 14, 512]               0
           SwinBlock-101          [-1, 14, 14, 512]               0
           LayerNorm-102          [-1, 14, 14, 512]           1,024
              Linear-103         [-1, 14, 14, 1536]         786,432
              Linear-104          [-1, 14, 14, 512]         262,656
     WindowAttention-105          [-1, 14, 14, 512]               0
             PreNorm-106          [-1, 14, 14, 512]               0
            Residual-107          [-1, 14, 14, 512]               0
           LayerNorm-108          [-1, 14, 14, 512]           1,024
              Linear-109         [-1, 14, 14, 2048]       1,050,624
                GELU-110         [-1, 14, 14, 2048]               0
              Linear-111          [-1, 14, 14, 512]       1,049,088
         FeedForward-112          [-1, 14, 14, 512]               0
             PreNorm-113          [-1, 14, 14, 512]               0
            Residual-114          [-1, 14, 14, 512]               0
           SwinBlock-115          [-1, 14, 14, 512]               0
           LayerNorm-116          [-1, 14, 14, 512]           1,024
         CyclicShift-117          [-1, 14, 14, 512]               0
              Linear-118         [-1, 14, 14, 1536]         786,432
              Linear-119          [-1, 14, 14, 512]         262,656
         CyclicShift-120          [-1, 14, 14, 512]               0
     WindowAttention-121          [-1, 14, 14, 512]               0
             PreNorm-122          [-1, 14, 14, 512]               0
            Residual-123          [-1, 14, 14, 512]               0
           LayerNorm-124          [-1, 14, 14, 512]           1,024
              Linear-125         [-1, 14, 14, 2048]       1,050,624
                GELU-126         [-1, 14, 14, 2048]               0
              Linear-127          [-1, 14, 14, 512]       1,049,088
         FeedForward-128          [-1, 14, 14, 512]               0
             PreNorm-129          [-1, 14, 14, 512]               0
            Residual-130          [-1, 14, 14, 512]               0
           SwinBlock-131          [-1, 14, 14, 512]               0
           LayerNorm-132          [-1, 14, 14, 512]           1,024
              Linear-133         [-1, 14, 14, 1536]         786,432
              Linear-134          [-1, 14, 14, 512]         262,656
     WindowAttention-135          [-1, 14, 14, 512]               0
             PreNorm-136          [-1, 14, 14, 512]               0
            Residual-137          [-1, 14, 14, 512]               0
           LayerNorm-138          [-1, 14, 14, 512]           1,024
              Linear-139         [-1, 14, 14, 2048]       1,050,624
                GELU-140         [-1, 14, 14, 2048]               0
              Linear-141          [-1, 14, 14, 512]       1,049,088
         FeedForward-142          [-1, 14, 14, 512]               0
             PreNorm-143          [-1, 14, 14, 512]               0
            Residual-144          [-1, 14, 14, 512]               0
           SwinBlock-145          [-1, 14, 14, 512]               0
           LayerNorm-146          [-1, 14, 14, 512]           1,024
         CyclicShift-147          [-1, 14, 14, 512]               0
              Linear-148         [-1, 14, 14, 1536]         786,432
              Linear-149          [-1, 14, 14, 512]         262,656
         CyclicShift-150          [-1, 14, 14, 512]               0
     WindowAttention-151          [-1, 14, 14, 512]               0
             PreNorm-152          [-1, 14, 14, 512]               0
            Residual-153          [-1, 14, 14, 512]               0
           LayerNorm-154          [-1, 14, 14, 512]           1,024
              Linear-155         [-1, 14, 14, 2048]       1,050,624
                GELU-156         [-1, 14, 14, 2048]               0
              Linear-157          [-1, 14, 14, 512]       1,049,088
         FeedForward-158          [-1, 14, 14, 512]               0
             PreNorm-159          [-1, 14, 14, 512]               0
            Residual-160          [-1, 14, 14, 512]               0
           SwinBlock-161          [-1, 14, 14, 512]               0
           LayerNorm-162          [-1, 14, 14, 512]           1,024
              Linear-163         [-1, 14, 14, 1536]         786,432
              Linear-164          [-1, 14, 14, 512]         262,656
     WindowAttention-165          [-1, 14, 14, 512]               0
             PreNorm-166          [-1, 14, 14, 512]               0
            Residual-167          [-1, 14, 14, 512]               0
           LayerNorm-168          [-1, 14, 14, 512]           1,024
              Linear-169         [-1, 14, 14, 2048]       1,050,624
                GELU-170         [-1, 14, 14, 2048]               0
              Linear-171          [-1, 14, 14, 512]       1,049,088
         FeedForward-172          [-1, 14, 14, 512]               0
             PreNorm-173          [-1, 14, 14, 512]               0
            Residual-174          [-1, 14, 14, 512]               0
           SwinBlock-175          [-1, 14, 14, 512]               0
           LayerNorm-176          [-1, 14, 14, 512]           1,024
         CyclicShift-177          [-1, 14, 14, 512]               0
              Linear-178         [-1, 14, 14, 1536]         786,432
              Linear-179          [-1, 14, 14, 512]         262,656
         CyclicShift-180          [-1, 14, 14, 512]               0
     WindowAttention-181          [-1, 14, 14, 512]               0
             PreNorm-182          [-1, 14, 14, 512]               0
            Residual-183          [-1, 14, 14, 512]               0
           LayerNorm-184          [-1, 14, 14, 512]           1,024
              Linear-185         [-1, 14, 14, 2048]       1,050,624
                GELU-186         [-1, 14, 14, 2048]               0
              Linear-187          [-1, 14, 14, 512]       1,049,088
         FeedForward-188          [-1, 14, 14, 512]               0
             PreNorm-189          [-1, 14, 14, 512]               0
            Residual-190          [-1, 14, 14, 512]               0
           SwinBlock-191          [-1, 14, 14, 512]               0
           LayerNorm-192          [-1, 14, 14, 512]           1,024
              Linear-193         [-1, 14, 14, 1536]         786,432
              Linear-194          [-1, 14, 14, 512]         262,656
     WindowAttention-195          [-1, 14, 14, 512]               0
             PreNorm-196          [-1, 14, 14, 512]               0
            Residual-197          [-1, 14, 14, 512]               0
           LayerNorm-198          [-1, 14, 14, 512]           1,024
              Linear-199         [-1, 14, 14, 2048]       1,050,624
                GELU-200         [-1, 14, 14, 2048]               0
              Linear-201          [-1, 14, 14, 512]       1,049,088
         FeedForward-202          [-1, 14, 14, 512]               0
             PreNorm-203          [-1, 14, 14, 512]               0
            Residual-204          [-1, 14, 14, 512]               0
           SwinBlock-205          [-1, 14, 14, 512]               0
           LayerNorm-206          [-1, 14, 14, 512]           1,024
         CyclicShift-207          [-1, 14, 14, 512]               0
              Linear-208         [-1, 14, 14, 1536]         786,432
              Linear-209          [-1, 14, 14, 512]         262,656
         CyclicShift-210          [-1, 14, 14, 512]               0
     WindowAttention-211          [-1, 14, 14, 512]               0
             PreNorm-212          [-1, 14, 14, 512]               0
            Residual-213          [-1, 14, 14, 512]               0
           LayerNorm-214          [-1, 14, 14, 512]           1,024
              Linear-215         [-1, 14, 14, 2048]       1,050,624
                GELU-216         [-1, 14, 14, 2048]               0
              Linear-217          [-1, 14, 14, 512]       1,049,088
         FeedForward-218          [-1, 14, 14, 512]               0
             PreNorm-219          [-1, 14, 14, 512]               0
            Residual-220          [-1, 14, 14, 512]               0
           SwinBlock-221          [-1, 14, 14, 512]               0
           LayerNorm-222          [-1, 14, 14, 512]           1,024
              Linear-223         [-1, 14, 14, 1536]         786,432
              Linear-224          [-1, 14, 14, 512]         262,656
     WindowAttention-225          [-1, 14, 14, 512]               0
             PreNorm-226          [-1, 14, 14, 512]               0
            Residual-227          [-1, 14, 14, 512]               0
           LayerNorm-228          [-1, 14, 14, 512]           1,024
              Linear-229         [-1, 14, 14, 2048]       1,050,624
                GELU-230         [-1, 14, 14, 2048]               0
              Linear-231          [-1, 14, 14, 512]       1,049,088
         FeedForward-232          [-1, 14, 14, 512]               0
             PreNorm-233          [-1, 14, 14, 512]               0
            Residual-234          [-1, 14, 14, 512]               0
           SwinBlock-235          [-1, 14, 14, 512]               0
           LayerNorm-236          [-1, 14, 14, 512]           1,024
         CyclicShift-237          [-1, 14, 14, 512]               0
              Linear-238         [-1, 14, 14, 1536]         786,432
              Linear-239          [-1, 14, 14, 512]         262,656
         CyclicShift-240          [-1, 14, 14, 512]               0
     WindowAttention-241          [-1, 14, 14, 512]               0
             PreNorm-242          [-1, 14, 14, 512]               0
            Residual-243          [-1, 14, 14, 512]               0
           LayerNorm-244          [-1, 14, 14, 512]           1,024
              Linear-245         [-1, 14, 14, 2048]       1,050,624
                GELU-246         [-1, 14, 14, 2048]               0
              Linear-247          [-1, 14, 14, 512]       1,049,088
         FeedForward-248          [-1, 14, 14, 512]               0
             PreNorm-249          [-1, 14, 14, 512]               0
            Residual-250          [-1, 14, 14, 512]               0
           SwinBlock-251          [-1, 14, 14, 512]               0
           LayerNorm-252          [-1, 14, 14, 512]           1,024
              Linear-253         [-1, 14, 14, 1536]         786,432
              Linear-254          [-1, 14, 14, 512]         262,656
     WindowAttention-255          [-1, 14, 14, 512]               0
             PreNorm-256          [-1, 14, 14, 512]               0
            Residual-257          [-1, 14, 14, 512]               0
           LayerNorm-258          [-1, 14, 14, 512]           1,024
              Linear-259         [-1, 14, 14, 2048]       1,050,624
                GELU-260         [-1, 14, 14, 2048]               0
              Linear-261          [-1, 14, 14, 512]       1,049,088
         FeedForward-262          [-1, 14, 14, 512]               0
             PreNorm-263          [-1, 14, 14, 512]               0
            Residual-264          [-1, 14, 14, 512]               0
           SwinBlock-265          [-1, 14, 14, 512]               0
           LayerNorm-266          [-1, 14, 14, 512]           1,024
         CyclicShift-267          [-1, 14, 14, 512]               0
              Linear-268         [-1, 14, 14, 1536]         786,432
              Linear-269          [-1, 14, 14, 512]         262,656
         CyclicShift-270          [-1, 14, 14, 512]               0
     WindowAttention-271          [-1, 14, 14, 512]               0
             PreNorm-272          [-1, 14, 14, 512]               0
            Residual-273          [-1, 14, 14, 512]               0
           LayerNorm-274          [-1, 14, 14, 512]           1,024
              Linear-275         [-1, 14, 14, 2048]       1,050,624
                GELU-276         [-1, 14, 14, 2048]               0
              Linear-277          [-1, 14, 14, 512]       1,049,088
         FeedForward-278          [-1, 14, 14, 512]               0
             PreNorm-279          [-1, 14, 14, 512]               0
            Residual-280          [-1, 14, 14, 512]               0
           SwinBlock-281          [-1, 14, 14, 512]               0
           LayerNorm-282          [-1, 14, 14, 512]           1,024
              Linear-283         [-1, 14, 14, 1536]         786,432
              Linear-284          [-1, 14, 14, 512]         262,656
     WindowAttention-285          [-1, 14, 14, 512]               0
             PreNorm-286          [-1, 14, 14, 512]               0
            Residual-287          [-1, 14, 14, 512]               0
           LayerNorm-288          [-1, 14, 14, 512]           1,024
              Linear-289         [-1, 14, 14, 2048]       1,050,624
                GELU-290         [-1, 14, 14, 2048]               0
              Linear-291          [-1, 14, 14, 512]       1,049,088
         FeedForward-292          [-1, 14, 14, 512]               0
             PreNorm-293          [-1, 14, 14, 512]               0
            Residual-294          [-1, 14, 14, 512]               0
           SwinBlock-295          [-1, 14, 14, 512]               0
           LayerNorm-296          [-1, 14, 14, 512]           1,024
         CyclicShift-297          [-1, 14, 14, 512]               0
              Linear-298         [-1, 14, 14, 1536]         786,432
              Linear-299          [-1, 14, 14, 512]         262,656
         CyclicShift-300          [-1, 14, 14, 512]               0
     WindowAttention-301          [-1, 14, 14, 512]               0
             PreNorm-302          [-1, 14, 14, 512]               0
            Residual-303          [-1, 14, 14, 512]               0
           LayerNorm-304          [-1, 14, 14, 512]           1,024
              Linear-305         [-1, 14, 14, 2048]       1,050,624
                GELU-306         [-1, 14, 14, 2048]               0
              Linear-307          [-1, 14, 14, 512]       1,049,088
         FeedForward-308          [-1, 14, 14, 512]               0
             PreNorm-309          [-1, 14, 14, 512]               0
            Residual-310          [-1, 14, 14, 512]               0
           SwinBlock-311          [-1, 14, 14, 512]               0
           LayerNorm-312          [-1, 14, 14, 512]           1,024
              Linear-313         [-1, 14, 14, 1536]         786,432
              Linear-314          [-1, 14, 14, 512]         262,656
     WindowAttention-315          [-1, 14, 14, 512]               0
             PreNorm-316          [-1, 14, 14, 512]               0
            Residual-317          [-1, 14, 14, 512]               0
           LayerNorm-318          [-1, 14, 14, 512]           1,024
              Linear-319         [-1, 14, 14, 2048]       1,050,624
                GELU-320         [-1, 14, 14, 2048]               0
              Linear-321          [-1, 14, 14, 512]       1,049,088
         FeedForward-322          [-1, 14, 14, 512]               0
             PreNorm-323          [-1, 14, 14, 512]               0
            Residual-324          [-1, 14, 14, 512]               0
           SwinBlock-325          [-1, 14, 14, 512]               0
           LayerNorm-326          [-1, 14, 14, 512]           1,024
         CyclicShift-327          [-1, 14, 14, 512]               0
              Linear-328         [-1, 14, 14, 1536]         786,432
              Linear-329          [-1, 14, 14, 512]         262,656
         CyclicShift-330          [-1, 14, 14, 512]               0
     WindowAttention-331          [-1, 14, 14, 512]               0
             PreNorm-332          [-1, 14, 14, 512]               0
            Residual-333          [-1, 14, 14, 512]               0
           LayerNorm-334          [-1, 14, 14, 512]           1,024
              Linear-335         [-1, 14, 14, 2048]       1,050,624
                GELU-336         [-1, 14, 14, 2048]               0
              Linear-337          [-1, 14, 14, 512]       1,049,088
         FeedForward-338          [-1, 14, 14, 512]               0
             PreNorm-339          [-1, 14, 14, 512]               0
            Residual-340          [-1, 14, 14, 512]               0
           SwinBlock-341          [-1, 14, 14, 512]               0
         StageModule-342          [-1, 512, 14, 14]               0
              Unfold-343             [-1, 2048, 49]               0
              Linear-344           [-1, 7, 7, 1024]       2,098,176
        PatchMerging-345           [-1, 7, 7, 1024]               0
           LayerNorm-346           [-1, 7, 7, 1024]           2,048
              Linear-347           [-1, 7, 7, 3072]       3,145,728
              Linear-348           [-1, 7, 7, 1024]       1,049,600
     WindowAttention-349           [-1, 7, 7, 1024]               0
             PreNorm-350           [-1, 7, 7, 1024]               0
            Residual-351           [-1, 7, 7, 1024]               0
           LayerNorm-352           [-1, 7, 7, 1024]           2,048
              Linear-353           [-1, 7, 7, 4096]       4,198,400
                GELU-354           [-1, 7, 7, 4096]               0
              Linear-355           [-1, 7, 7, 1024]       4,195,328
         FeedForward-356           [-1, 7, 7, 1024]               0
             PreNorm-357           [-1, 7, 7, 1024]               0
            Residual-358           [-1, 7, 7, 1024]               0
           SwinBlock-359           [-1, 7, 7, 1024]               0
           LayerNorm-360           [-1, 7, 7, 1024]           2,048
         CyclicShift-361           [-1, 7, 7, 1024]               0
              Linear-362           [-1, 7, 7, 3072]       3,145,728
              Linear-363           [-1, 7, 7, 1024]       1,049,600
         CyclicShift-364           [-1, 7, 7, 1024]               0
     WindowAttention-365           [-1, 7, 7, 1024]               0
             PreNorm-366           [-1, 7, 7, 1024]               0
            Residual-367           [-1, 7, 7, 1024]               0
           LayerNorm-368           [-1, 7, 7, 1024]           2,048
              Linear-369           [-1, 7, 7, 4096]       4,198,400
                GELU-370           [-1, 7, 7, 4096]               0
              Linear-371           [-1, 7, 7, 1024]       4,195,328
         FeedForward-372           [-1, 7, 7, 1024]               0
             PreNorm-373           [-1, 7, 7, 1024]               0
            Residual-374           [-1, 7, 7, 1024]               0
           SwinBlock-375           [-1, 7, 7, 1024]               0
         StageModule-376           [-1, 1024, 7, 7]               0
           LayerNorm-377                 [-1, 1024]           2,048
              Linear-378                 [-1, 1000]       1,025,000
    ================================================================
    Total params: 87,662,952
    Trainable params: 87,662,952
    Non-trainable params: 0
    ----------------------------------------------------------------
    Input size (MB): 0.57
    Forward/backward pass size (MB): 569.64
    Params size (MB): 334.41
    Estimated Total Size (MB): 904.62
    ----------------------------------------------------------------
    


```python

def swin_l(hidden_dim=192, layers=(2, 2, 18, 2), heads=(6, 12, 24, 48), **kwargs):
    return SwinTransformer(hidden_dim=hidden_dim, layers=layers, heads=heads, **kwargs)

if __name__ == '__main__':
    import pdb
    from torchsummary import summary
    model = swin_l()
    x = torch.rand(4, 3, 224, 224)

    outputs = model(x)
    print(outputs.shape)

    summary(model, input_size=(3, 224, 224), device='CPU')
```

    torch.Size([4, 1000])
    ----------------------------------------------------------------
            Layer (type)               Output Shape         Param #
    ================================================================
                Unfold-1             [-1, 48, 3136]               0
                Linear-2          [-1, 56, 56, 192]           9,408
          PatchMerging-3          [-1, 56, 56, 192]               0
             LayerNorm-4          [-1, 56, 56, 192]             384
                Linear-5          [-1, 56, 56, 576]         110,592
                Linear-6          [-1, 56, 56, 192]          37,056
       WindowAttention-7          [-1, 56, 56, 192]               0
               PreNorm-8          [-1, 56, 56, 192]               0
              Residual-9          [-1, 56, 56, 192]               0
            LayerNorm-10          [-1, 56, 56, 192]             384
               Linear-11          [-1, 56, 56, 768]         148,224
                 GELU-12          [-1, 56, 56, 768]               0
               Linear-13          [-1, 56, 56, 192]         147,648
          FeedForward-14          [-1, 56, 56, 192]               0
              PreNorm-15          [-1, 56, 56, 192]               0
             Residual-16          [-1, 56, 56, 192]               0
            SwinBlock-17          [-1, 56, 56, 192]               0
            LayerNorm-18          [-1, 56, 56, 192]             384
          CyclicShift-19          [-1, 56, 56, 192]               0
               Linear-20          [-1, 56, 56, 576]         110,592
               Linear-21          [-1, 56, 56, 192]          37,056
          CyclicShift-22          [-1, 56, 56, 192]               0
      WindowAttention-23          [-1, 56, 56, 192]               0
              PreNorm-24          [-1, 56, 56, 192]               0
             Residual-25          [-1, 56, 56, 192]               0
            LayerNorm-26          [-1, 56, 56, 192]             384
               Linear-27          [-1, 56, 56, 768]         148,224
                 GELU-28          [-1, 56, 56, 768]               0
               Linear-29          [-1, 56, 56, 192]         147,648
          FeedForward-30          [-1, 56, 56, 192]               0
              PreNorm-31          [-1, 56, 56, 192]               0
             Residual-32          [-1, 56, 56, 192]               0
            SwinBlock-33          [-1, 56, 56, 192]               0
          StageModule-34          [-1, 192, 56, 56]               0
               Unfold-35             [-1, 768, 784]               0
               Linear-36          [-1, 28, 28, 384]         295,296
         PatchMerging-37          [-1, 28, 28, 384]               0
            LayerNorm-38          [-1, 28, 28, 384]             768
               Linear-39         [-1, 28, 28, 1152]         442,368
               Linear-40          [-1, 28, 28, 384]         147,840
      WindowAttention-41          [-1, 28, 28, 384]               0
              PreNorm-42          [-1, 28, 28, 384]               0
             Residual-43          [-1, 28, 28, 384]               0
            LayerNorm-44          [-1, 28, 28, 384]             768
               Linear-45         [-1, 28, 28, 1536]         591,360
                 GELU-46         [-1, 28, 28, 1536]               0
               Linear-47          [-1, 28, 28, 384]         590,208
          FeedForward-48          [-1, 28, 28, 384]               0
              PreNorm-49          [-1, 28, 28, 384]               0
             Residual-50          [-1, 28, 28, 384]               0
            SwinBlock-51          [-1, 28, 28, 384]               0
            LayerNorm-52          [-1, 28, 28, 384]             768
          CyclicShift-53          [-1, 28, 28, 384]               0
               Linear-54         [-1, 28, 28, 1152]         442,368
               Linear-55          [-1, 28, 28, 384]         147,840
          CyclicShift-56          [-1, 28, 28, 384]               0
      WindowAttention-57          [-1, 28, 28, 384]               0
              PreNorm-58          [-1, 28, 28, 384]               0
             Residual-59          [-1, 28, 28, 384]               0
            LayerNorm-60          [-1, 28, 28, 384]             768
               Linear-61         [-1, 28, 28, 1536]         591,360
                 GELU-62         [-1, 28, 28, 1536]               0
               Linear-63          [-1, 28, 28, 384]         590,208
          FeedForward-64          [-1, 28, 28, 384]               0
              PreNorm-65          [-1, 28, 28, 384]               0
             Residual-66          [-1, 28, 28, 384]               0
            SwinBlock-67          [-1, 28, 28, 384]               0
          StageModule-68          [-1, 384, 28, 28]               0
               Unfold-69            [-1, 1536, 196]               0
               Linear-70          [-1, 14, 14, 768]       1,180,416
         PatchMerging-71          [-1, 14, 14, 768]               0
            LayerNorm-72          [-1, 14, 14, 768]           1,536
               Linear-73         [-1, 14, 14, 2304]       1,769,472
               Linear-74          [-1, 14, 14, 768]         590,592
      WindowAttention-75          [-1, 14, 14, 768]               0
              PreNorm-76          [-1, 14, 14, 768]               0
             Residual-77          [-1, 14, 14, 768]               0
            LayerNorm-78          [-1, 14, 14, 768]           1,536
               Linear-79         [-1, 14, 14, 3072]       2,362,368
                 GELU-80         [-1, 14, 14, 3072]               0
               Linear-81          [-1, 14, 14, 768]       2,360,064
          FeedForward-82          [-1, 14, 14, 768]               0
              PreNorm-83          [-1, 14, 14, 768]               0
             Residual-84          [-1, 14, 14, 768]               0
            SwinBlock-85          [-1, 14, 14, 768]               0
            LayerNorm-86          [-1, 14, 14, 768]           1,536
          CyclicShift-87          [-1, 14, 14, 768]               0
               Linear-88         [-1, 14, 14, 2304]       1,769,472
               Linear-89          [-1, 14, 14, 768]         590,592
          CyclicShift-90          [-1, 14, 14, 768]               0
      WindowAttention-91          [-1, 14, 14, 768]               0
              PreNorm-92          [-1, 14, 14, 768]               0
             Residual-93          [-1, 14, 14, 768]               0
            LayerNorm-94          [-1, 14, 14, 768]           1,536
               Linear-95         [-1, 14, 14, 3072]       2,362,368
                 GELU-96         [-1, 14, 14, 3072]               0
               Linear-97          [-1, 14, 14, 768]       2,360,064
          FeedForward-98          [-1, 14, 14, 768]               0
              PreNorm-99          [-1, 14, 14, 768]               0
            Residual-100          [-1, 14, 14, 768]               0
           SwinBlock-101          [-1, 14, 14, 768]               0
           LayerNorm-102          [-1, 14, 14, 768]           1,536
              Linear-103         [-1, 14, 14, 2304]       1,769,472
              Linear-104          [-1, 14, 14, 768]         590,592
     WindowAttention-105          [-1, 14, 14, 768]               0
             PreNorm-106          [-1, 14, 14, 768]               0
            Residual-107          [-1, 14, 14, 768]               0
           LayerNorm-108          [-1, 14, 14, 768]           1,536
              Linear-109         [-1, 14, 14, 3072]       2,362,368
                GELU-110         [-1, 14, 14, 3072]               0
              Linear-111          [-1, 14, 14, 768]       2,360,064
         FeedForward-112          [-1, 14, 14, 768]               0
             PreNorm-113          [-1, 14, 14, 768]               0
            Residual-114          [-1, 14, 14, 768]               0
           SwinBlock-115          [-1, 14, 14, 768]               0
           LayerNorm-116          [-1, 14, 14, 768]           1,536
         CyclicShift-117          [-1, 14, 14, 768]               0
              Linear-118         [-1, 14, 14, 2304]       1,769,472
              Linear-119          [-1, 14, 14, 768]         590,592
         CyclicShift-120          [-1, 14, 14, 768]               0
     WindowAttention-121          [-1, 14, 14, 768]               0
             PreNorm-122          [-1, 14, 14, 768]               0
            Residual-123          [-1, 14, 14, 768]               0
           LayerNorm-124          [-1, 14, 14, 768]           1,536
              Linear-125         [-1, 14, 14, 3072]       2,362,368
                GELU-126         [-1, 14, 14, 3072]               0
              Linear-127          [-1, 14, 14, 768]       2,360,064
         FeedForward-128          [-1, 14, 14, 768]               0
             PreNorm-129          [-1, 14, 14, 768]               0
            Residual-130          [-1, 14, 14, 768]               0
           SwinBlock-131          [-1, 14, 14, 768]               0
           LayerNorm-132          [-1, 14, 14, 768]           1,536
              Linear-133         [-1, 14, 14, 2304]       1,769,472
              Linear-134          [-1, 14, 14, 768]         590,592
     WindowAttention-135          [-1, 14, 14, 768]               0
             PreNorm-136          [-1, 14, 14, 768]               0
            Residual-137          [-1, 14, 14, 768]               0
           LayerNorm-138          [-1, 14, 14, 768]           1,536
              Linear-139         [-1, 14, 14, 3072]       2,362,368
                GELU-140         [-1, 14, 14, 3072]               0
              Linear-141          [-1, 14, 14, 768]       2,360,064
         FeedForward-142          [-1, 14, 14, 768]               0
             PreNorm-143          [-1, 14, 14, 768]               0
            Residual-144          [-1, 14, 14, 768]               0
           SwinBlock-145          [-1, 14, 14, 768]               0
           LayerNorm-146          [-1, 14, 14, 768]           1,536
         CyclicShift-147          [-1, 14, 14, 768]               0
              Linear-148         [-1, 14, 14, 2304]       1,769,472
              Linear-149          [-1, 14, 14, 768]         590,592
         CyclicShift-150          [-1, 14, 14, 768]               0
     WindowAttention-151          [-1, 14, 14, 768]               0
             PreNorm-152          [-1, 14, 14, 768]               0
            Residual-153          [-1, 14, 14, 768]               0
           LayerNorm-154          [-1, 14, 14, 768]           1,536
              Linear-155         [-1, 14, 14, 3072]       2,362,368
                GELU-156         [-1, 14, 14, 3072]               0
              Linear-157          [-1, 14, 14, 768]       2,360,064
         FeedForward-158          [-1, 14, 14, 768]               0
             PreNorm-159          [-1, 14, 14, 768]               0
            Residual-160          [-1, 14, 14, 768]               0
           SwinBlock-161          [-1, 14, 14, 768]               0
           LayerNorm-162          [-1, 14, 14, 768]           1,536
              Linear-163         [-1, 14, 14, 2304]       1,769,472
              Linear-164          [-1, 14, 14, 768]         590,592
     WindowAttention-165          [-1, 14, 14, 768]               0
             PreNorm-166          [-1, 14, 14, 768]               0
            Residual-167          [-1, 14, 14, 768]               0
           LayerNorm-168          [-1, 14, 14, 768]           1,536
              Linear-169         [-1, 14, 14, 3072]       2,362,368
                GELU-170         [-1, 14, 14, 3072]               0
              Linear-171          [-1, 14, 14, 768]       2,360,064
         FeedForward-172          [-1, 14, 14, 768]               0
             PreNorm-173          [-1, 14, 14, 768]               0
            Residual-174          [-1, 14, 14, 768]               0
           SwinBlock-175          [-1, 14, 14, 768]               0
           LayerNorm-176          [-1, 14, 14, 768]           1,536
         CyclicShift-177          [-1, 14, 14, 768]               0
              Linear-178         [-1, 14, 14, 2304]       1,769,472
              Linear-179          [-1, 14, 14, 768]         590,592
         CyclicShift-180          [-1, 14, 14, 768]               0
     WindowAttention-181          [-1, 14, 14, 768]               0
             PreNorm-182          [-1, 14, 14, 768]               0
            Residual-183          [-1, 14, 14, 768]               0
           LayerNorm-184          [-1, 14, 14, 768]           1,536
              Linear-185         [-1, 14, 14, 3072]       2,362,368
                GELU-186         [-1, 14, 14, 3072]               0
              Linear-187          [-1, 14, 14, 768]       2,360,064
         FeedForward-188          [-1, 14, 14, 768]               0
             PreNorm-189          [-1, 14, 14, 768]               0
            Residual-190          [-1, 14, 14, 768]               0
           SwinBlock-191          [-1, 14, 14, 768]               0
           LayerNorm-192          [-1, 14, 14, 768]           1,536
              Linear-193         [-1, 14, 14, 2304]       1,769,472
              Linear-194          [-1, 14, 14, 768]         590,592
     WindowAttention-195          [-1, 14, 14, 768]               0
             PreNorm-196          [-1, 14, 14, 768]               0
            Residual-197          [-1, 14, 14, 768]               0
           LayerNorm-198          [-1, 14, 14, 768]           1,536
              Linear-199         [-1, 14, 14, 3072]       2,362,368
                GELU-200         [-1, 14, 14, 3072]               0
              Linear-201          [-1, 14, 14, 768]       2,360,064
         FeedForward-202          [-1, 14, 14, 768]               0
             PreNorm-203          [-1, 14, 14, 768]               0
            Residual-204          [-1, 14, 14, 768]               0
           SwinBlock-205          [-1, 14, 14, 768]               0
           LayerNorm-206          [-1, 14, 14, 768]           1,536
         CyclicShift-207          [-1, 14, 14, 768]               0
              Linear-208         [-1, 14, 14, 2304]       1,769,472
              Linear-209          [-1, 14, 14, 768]         590,592
         CyclicShift-210          [-1, 14, 14, 768]               0
     WindowAttention-211          [-1, 14, 14, 768]               0
             PreNorm-212          [-1, 14, 14, 768]               0
            Residual-213          [-1, 14, 14, 768]               0
           LayerNorm-214          [-1, 14, 14, 768]           1,536
              Linear-215         [-1, 14, 14, 3072]       2,362,368
                GELU-216         [-1, 14, 14, 3072]               0
              Linear-217          [-1, 14, 14, 768]       2,360,064
         FeedForward-218          [-1, 14, 14, 768]               0
             PreNorm-219          [-1, 14, 14, 768]               0
            Residual-220          [-1, 14, 14, 768]               0
           SwinBlock-221          [-1, 14, 14, 768]               0
           LayerNorm-222          [-1, 14, 14, 768]           1,536
              Linear-223         [-1, 14, 14, 2304]       1,769,472
              Linear-224          [-1, 14, 14, 768]         590,592
     WindowAttention-225          [-1, 14, 14, 768]               0
             PreNorm-226          [-1, 14, 14, 768]               0
            Residual-227          [-1, 14, 14, 768]               0
           LayerNorm-228          [-1, 14, 14, 768]           1,536
              Linear-229         [-1, 14, 14, 3072]       2,362,368
                GELU-230         [-1, 14, 14, 3072]               0
              Linear-231          [-1, 14, 14, 768]       2,360,064
         FeedForward-232          [-1, 14, 14, 768]               0
             PreNorm-233          [-1, 14, 14, 768]               0
            Residual-234          [-1, 14, 14, 768]               0
           SwinBlock-235          [-1, 14, 14, 768]               0
           LayerNorm-236          [-1, 14, 14, 768]           1,536
         CyclicShift-237          [-1, 14, 14, 768]               0
              Linear-238         [-1, 14, 14, 2304]       1,769,472
              Linear-239          [-1, 14, 14, 768]         590,592
         CyclicShift-240          [-1, 14, 14, 768]               0
     WindowAttention-241          [-1, 14, 14, 768]               0
             PreNorm-242          [-1, 14, 14, 768]               0
            Residual-243          [-1, 14, 14, 768]               0
           LayerNorm-244          [-1, 14, 14, 768]           1,536
              Linear-245         [-1, 14, 14, 3072]       2,362,368
                GELU-246         [-1, 14, 14, 3072]               0
              Linear-247          [-1, 14, 14, 768]       2,360,064
         FeedForward-248          [-1, 14, 14, 768]               0
             PreNorm-249          [-1, 14, 14, 768]               0
            Residual-250          [-1, 14, 14, 768]               0
           SwinBlock-251          [-1, 14, 14, 768]               0
           LayerNorm-252          [-1, 14, 14, 768]           1,536
              Linear-253         [-1, 14, 14, 2304]       1,769,472
              Linear-254          [-1, 14, 14, 768]         590,592
     WindowAttention-255          [-1, 14, 14, 768]               0
             PreNorm-256          [-1, 14, 14, 768]               0
            Residual-257          [-1, 14, 14, 768]               0
           LayerNorm-258          [-1, 14, 14, 768]           1,536
              Linear-259         [-1, 14, 14, 3072]       2,362,368
                GELU-260         [-1, 14, 14, 3072]               0
              Linear-261          [-1, 14, 14, 768]       2,360,064
         FeedForward-262          [-1, 14, 14, 768]               0
             PreNorm-263          [-1, 14, 14, 768]               0
            Residual-264          [-1, 14, 14, 768]               0
           SwinBlock-265          [-1, 14, 14, 768]               0
           LayerNorm-266          [-1, 14, 14, 768]           1,536
         CyclicShift-267          [-1, 14, 14, 768]               0
              Linear-268         [-1, 14, 14, 2304]       1,769,472
              Linear-269          [-1, 14, 14, 768]         590,592
         CyclicShift-270          [-1, 14, 14, 768]               0
     WindowAttention-271          [-1, 14, 14, 768]               0
             PreNorm-272          [-1, 14, 14, 768]               0
            Residual-273          [-1, 14, 14, 768]               0
           LayerNorm-274          [-1, 14, 14, 768]           1,536
              Linear-275         [-1, 14, 14, 3072]       2,362,368
                GELU-276         [-1, 14, 14, 3072]               0
              Linear-277          [-1, 14, 14, 768]       2,360,064
         FeedForward-278          [-1, 14, 14, 768]               0
             PreNorm-279          [-1, 14, 14, 768]               0
            Residual-280          [-1, 14, 14, 768]               0
           SwinBlock-281          [-1, 14, 14, 768]               0
           LayerNorm-282          [-1, 14, 14, 768]           1,536
              Linear-283         [-1, 14, 14, 2304]       1,769,472
              Linear-284          [-1, 14, 14, 768]         590,592
     WindowAttention-285          [-1, 14, 14, 768]               0
             PreNorm-286          [-1, 14, 14, 768]               0
            Residual-287          [-1, 14, 14, 768]               0
           LayerNorm-288          [-1, 14, 14, 768]           1,536
              Linear-289         [-1, 14, 14, 3072]       2,362,368
                GELU-290         [-1, 14, 14, 3072]               0
              Linear-291          [-1, 14, 14, 768]       2,360,064
         FeedForward-292          [-1, 14, 14, 768]               0
             PreNorm-293          [-1, 14, 14, 768]               0
            Residual-294          [-1, 14, 14, 768]               0
           SwinBlock-295          [-1, 14, 14, 768]               0
           LayerNorm-296          [-1, 14, 14, 768]           1,536
         CyclicShift-297          [-1, 14, 14, 768]               0
              Linear-298         [-1, 14, 14, 2304]       1,769,472
              Linear-299          [-1, 14, 14, 768]         590,592
         CyclicShift-300          [-1, 14, 14, 768]               0
     WindowAttention-301          [-1, 14, 14, 768]               0
             PreNorm-302          [-1, 14, 14, 768]               0
            Residual-303          [-1, 14, 14, 768]               0
           LayerNorm-304          [-1, 14, 14, 768]           1,536
              Linear-305         [-1, 14, 14, 3072]       2,362,368
                GELU-306         [-1, 14, 14, 3072]               0
              Linear-307          [-1, 14, 14, 768]       2,360,064
         FeedForward-308          [-1, 14, 14, 768]               0
             PreNorm-309          [-1, 14, 14, 768]               0
            Residual-310          [-1, 14, 14, 768]               0
           SwinBlock-311          [-1, 14, 14, 768]               0
           LayerNorm-312          [-1, 14, 14, 768]           1,536
              Linear-313         [-1, 14, 14, 2304]       1,769,472
              Linear-314          [-1, 14, 14, 768]         590,592
     WindowAttention-315          [-1, 14, 14, 768]               0
             PreNorm-316          [-1, 14, 14, 768]               0
            Residual-317          [-1, 14, 14, 768]               0
           LayerNorm-318          [-1, 14, 14, 768]           1,536
              Linear-319         [-1, 14, 14, 3072]       2,362,368
                GELU-320         [-1, 14, 14, 3072]               0
              Linear-321          [-1, 14, 14, 768]       2,360,064
         FeedForward-322          [-1, 14, 14, 768]               0
             PreNorm-323          [-1, 14, 14, 768]               0
            Residual-324          [-1, 14, 14, 768]               0
           SwinBlock-325          [-1, 14, 14, 768]               0
           LayerNorm-326          [-1, 14, 14, 768]           1,536
         CyclicShift-327          [-1, 14, 14, 768]               0
              Linear-328         [-1, 14, 14, 2304]       1,769,472
              Linear-329          [-1, 14, 14, 768]         590,592
         CyclicShift-330          [-1, 14, 14, 768]               0
     WindowAttention-331          [-1, 14, 14, 768]               0
             PreNorm-332          [-1, 14, 14, 768]               0
            Residual-333          [-1, 14, 14, 768]               0
           LayerNorm-334          [-1, 14, 14, 768]           1,536
              Linear-335         [-1, 14, 14, 3072]       2,362,368
                GELU-336         [-1, 14, 14, 3072]               0
              Linear-337          [-1, 14, 14, 768]       2,360,064
         FeedForward-338          [-1, 14, 14, 768]               0
             PreNorm-339          [-1, 14, 14, 768]               0
            Residual-340          [-1, 14, 14, 768]               0
           SwinBlock-341          [-1, 14, 14, 768]               0
         StageModule-342          [-1, 768, 14, 14]               0
              Unfold-343             [-1, 3072, 49]               0
              Linear-344           [-1, 7, 7, 1536]       4,720,128
        PatchMerging-345           [-1, 7, 7, 1536]               0
           LayerNorm-346           [-1, 7, 7, 1536]           3,072
              Linear-347           [-1, 7, 7, 4608]       7,077,888
              Linear-348           [-1, 7, 7, 1536]       2,360,832
     WindowAttention-349           [-1, 7, 7, 1536]               0
             PreNorm-350           [-1, 7, 7, 1536]               0
            Residual-351           [-1, 7, 7, 1536]               0
           LayerNorm-352           [-1, 7, 7, 1536]           3,072
              Linear-353           [-1, 7, 7, 6144]       9,443,328
                GELU-354           [-1, 7, 7, 6144]               0
              Linear-355           [-1, 7, 7, 1536]       9,438,720
         FeedForward-356           [-1, 7, 7, 1536]               0
             PreNorm-357           [-1, 7, 7, 1536]               0
            Residual-358           [-1, 7, 7, 1536]               0
           SwinBlock-359           [-1, 7, 7, 1536]               0
           LayerNorm-360           [-1, 7, 7, 1536]           3,072
         CyclicShift-361           [-1, 7, 7, 1536]               0
              Linear-362           [-1, 7, 7, 4608]       7,077,888
              Linear-363           [-1, 7, 7, 1536]       2,360,832
         CyclicShift-364           [-1, 7, 7, 1536]               0
     WindowAttention-365           [-1, 7, 7, 1536]               0
             PreNorm-366           [-1, 7, 7, 1536]               0
            Residual-367           [-1, 7, 7, 1536]               0
           LayerNorm-368           [-1, 7, 7, 1536]           3,072
              Linear-369           [-1, 7, 7, 6144]       9,443,328
                GELU-370           [-1, 7, 7, 6144]               0
              Linear-371           [-1, 7, 7, 1536]       9,438,720
         FeedForward-372           [-1, 7, 7, 1536]               0
             PreNorm-373           [-1, 7, 7, 1536]               0
            Residual-374           [-1, 7, 7, 1536]               0
           SwinBlock-375           [-1, 7, 7, 1536]               0
         StageModule-376           [-1, 1536, 7, 7]               0
           LayerNorm-377                 [-1, 1536]           3,072
              Linear-378                 [-1, 1000]       1,537,000
    ================================================================
    Total params: 196,374,568
    Trainable params: 196,374,568
    Non-trainable params: 0
    ----------------------------------------------------------------
    Input size (MB): 0.57
    Forward/backward pass size (MB): 853.88
    Params size (MB): 749.11
    Estimated Total Size (MB): 1603.57
    ----------------------------------------------------------------
    

