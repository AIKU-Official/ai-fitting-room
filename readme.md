# AI Fitting Room

<aside>
👜 클릭 한 번으로 옷을 입어볼 수 있다면?
Virtual Try-On 모델을 이용한 AI 피팅룸

</aside>

## 💪 Our Team

> **김채현** _Lead_

- Research
- Data Processing
- Experiments
- Presentation

> **심하민**

- Research
- Data Processing
- Resource
- Training

> **노성주**

- Research
- Data Processing
- Presentation

## 👗 Introduction

### Motivation

가끔, 내가 가지고 있는 옷들 중 어떤 걸 코디해야 할지 전혀 감이 오지 않는 경우가 있습니다. “이렇게 입으면 괜찮겠지”하고 입어봤더니 내가 상상했던 모습이 아니라 당황스러웠던 적도 있고요. 이런 고민들을 해결하기 위해, 직접 입어보지 않고도 옷을 입은 나의 모습을 확인해볼 수 있도록 피팅해주는 딥러닝 모델을 만들고 싶다는 생각에서 시작되었습니다.

![Image from **[TryOnDiffusion: A Tale of Two UNets](https://tryondiffusion.github.io/)**](https://github.com/kchyun/ai-fitting-room/assets/63688973/bd3ab808-3b0f-4456-bc92-6aaea5ee5978)
\*Image from \*\*[TryOnDiffusion: A Tale of Two UNets](https://tryondiffusion.github.io/)\*\*\*

### Goal

기존의 가상피팅 모델들은 대개 **상의만 적용이 가능**하다는 한계를 가지고 있음

**⇒ 이를 확장해 상의, 하의 및 드레스까지 피팅 가능한 모델을 만들어보자!**

## 📚 Dataset

### Dress-Code [[repo]](https://github.com/aimagelab/dress-code)

\*Proposed in “**[Dress Code: High-Resolution Multi-Category Virtual Try-On](https://arxiv.org/abs/2204.08532)”\***

- 1024 x 768 고화질 이미지
- 5만여 장의 옷, 10만여 장의 전신 이미지 데이터
- keypoint, skeleton, label map, dense pose 등 풍부한 annotation 제공

![Untitled](https://github.com/kchyun/ai-fitting-room/assets/63688973/55be9ea7-22c0-4b96-902b-9677cf535f6f)

## 📐 Modeling

### DAFlow [[repo]](https://github.com/OFA-Sys/DAFlow)

Proposed in **"[Single Stage Virtual Try-on via Deformable Attention Flows](https://arxiv.org/abs/2207.09161)" from ECCV2022**

![Brief description of DAFlow](https://github.com/kchyun/ai-fitting-room/assets/63688973/9aba8dcc-c255-4df0-8fb0-fa9723042253)
_Brief description of DAFlow_

![Input of DAFlow](https://github.com/kchyun/ai-fitting-room/assets/63688973/01cf8d52-bcf9-4ba0-a01f-16acb399b2ee)
_Input of DAFlow_

Deformable attention을 이용한 single stage, end-to-end 구조로, 기존 multi-stage 구조의 복잡성을 해결한 단순한 구조의 모델

- **특징**
  1. Pyramid feature extraction: coarse-to-fine
  2. Cascade flow estimation: DAFN and DAWarp
  3. Shallow encoder-decoder generation

## ♻️ Data Processing

### What is ‘Agnostic’?

Target Garment를 합성하고자 하는 자리를 검정색으로 마스킹한 이미지. 기존 코드에서는 **상의에 대해서만 keypoint와 densepose 데이터를 이용**해 Agnostic 이미지를 생성합니다. 이 프로젝트는 하의와 드레스까지 합성하는 것이 목표이므로, **하반신과 전신 Agnostic 처리가 필요**합니다.

### Agnostic for full-body

상체는 키포인트와 densepose만을 사용해 완벽하게 마스킹이 가능하지만, **골반 부분이 상체로 분류되어 있는 Densepose**의 특성상 주어진 데이터만을 사용해 하체를 완벽하게 마스킹할 수 없습니다.

⇒ 상의, 바지, 신발, 모자 등 다양한 패션 아이템으로 레이블링된 Dress-Code의 **Label map 데이터를 추가로 사용**해 하반신을 보다 정확하게 마스킹했습니다.

- **Upper body**
  - keypoint + densepose
- **Lower body**
  - keypoint + densepose + label map
- **Dresses**
  - Upper + Lower body
    동시에 적용

![Untitled](https://github.com/kchyun/ai-fitting-room/assets/63688973/beaf385e-e63f-4c7f-acf3-2e3467e1b3cc)

## 🎓 Training

### Fine-Tuning

스크래치부터 학습한 경우 초반 Loss가 크고 수렴 속도가 느렸습니다. 따라서 상반신 중심으로 학습된 DAFlow의 체크포인트에서 전처리한 Dress-Code 데이터셋을 사용해 파인튜닝했습니다.

### Implementation Detail

- `Epoch`: 10
- `Batch size`: 1
- `Device`: RTX3080 \* 1
- Data Usage
  - Image Resolution: 512 x 384
  - 1800 paired Upper/Lower/Dresses sets each

## 🧪 Results

### Sample results during training

학습 과정에서 얻은 결과를 왼쪽에서 오른쪽으로 시간순 배열했습니다. 학습할수록 더 정확하고, 자연스럽게 합성하는 모습을 확인할 수 있었습니다. 4번째 epoch 이후부터는 오버피팅이 발생하였습니다.

![상의 합성 결과](https://github.com/kchyun/ai-fitting-room/assets/63688973/64f72b86-3493-4f04-a1b2-a64790eb08f1)
_상의 합성 결과_

![하의 합성 결과](https://github.com/kchyun/ai-fitting-room/assets/63688973/ef36a884-3831-4fb1-9420-ae429173a76b)
_하의 합성 결과_

![드레스 합성 결과](https://github.com/kchyun/ai-fitting-room/assets/63688973/120d30d7-3017-4402-9967-0a722b0ef595)
_드레스 합성 결과_

### Inference with new images

![Untitled](https://github.com/kchyun/ai-fitting-room/assets/63688973/28c894ff-11b4-49cc-b2cd-af2ba2479f3f)

## ⛔ Limitation

![Failure cases.

1. 복잡한 팔 형태에 맞게 합성에 실패한 경우
   2~3. 드레스의 넥라인 디테일이 사라지는 경우](https://github.com/kchyun/ai-fitting-room/assets/63688973/c729ddad-5fde-4c43-96fe-a5f461c45f2f)
   _Failure cases: 1. 복잡한 팔 형태에 맞게 합성에 실패한 경우, 2~3. 드레스의 넥라인 디테일이 사라지는 경우_

- Agnostic mask의 형태에 민감
- 복잡한 포즈에 대한 적응력 떨어짐
- 옷의 디테일이 변형되는 경우 존재

# 🤔 Future Works

### Performance

- 일반적이고 효과적인 Agnostic mask 형태 연구
- 옷의 디테일을 보존하며 합성하도록 개선
- 다양한 배경과 각도의 이미지에 강건하도록 학습

### Application

- 다른 VITON 응용 분야와의 결합
- 데모 페이지 및 서비스 만들기
