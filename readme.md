# AI Fitting Room

<aside>
👜 클릭 한 번으로 옷을 입어볼 수 있다면?
Virtual Try-On 모델을 이용한 AI 피팅룸

</aside>

<!-- ## 💪 Our Team

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
- Presentation -->

## 👗 Introduction

### Motivation

가끔, 내가 가지고 있는 옷들 중 어떤 걸 코디해야 할지 전혀 감이 오지 않는 경우가 있습니다. “이렇게 입으면 괜찮겠지”하고 입어봤더니 내가 상상했던 모습이 아니라 당황스러웠던 적도 있고요. 이런 고민들을 해결하기 위해, 직접 입어보지 않고도 옷을 입은 나의 모습을 확인해볼 수 있도록 피팅해주는 딥러닝 모델을 만들고 싶다는 생각에서 시작되었습니다.

![TryonDiffusion](https://github.com/kchyun/ai-fitting-room/assets/63688973/03f33dbe-5ae8-4817-b617-4273a4dfd711)
_Image from **[TryOnDiffusion: A Tale of Two UNets](https://tryondiffusion.github.io/)**_

### Goal

기존의 가상피팅 모델들은 대개 **상의만 적용이 가능**하다는 한계를 가지고 있음

**⇒ 이를 확장해 상의, 하의 및 드레스까지 피팅 가능한 모델을 만들어보자!**

## 📚 Dataset

### Dress-Code [[repo]](https://github.com/aimagelab/dress-code)

Proposed in “**[Dress Code: High-Resolution Multi-Category Virtual Try-On](https://arxiv.org/abs/2204.08532)”**

- 1024 x 768 고화질 이미지
- 5만여 장의 옷, 10만여 장의 전신 이미지 데이터
- keypoint, skeleton, label map, dense pose 등 풍부한 annotation 제공

![Untitled](https://github.com/kchyun/ai-fitting-room/assets/63688973/593e5948-bc28-4511-a584-7dfece020224)

## 📐 Modeling

### DAFlow [[repo]](https://github.com/OFA-Sys/DAFlow)

Proposed in **"[Single Stage Virtual Try-on via Deformable Attention Flows](https://arxiv.org/abs/2207.09161)" from ECCV2022**

Deformable attention을 이용한 single stage, end-to-end 구조로, 기존 multi-stage 구조의 복잡성을 해결한 단순한 구조의 모델

- **특징**
  1. Pyramid feature extraction: coarse-to-fine
  2. Cascade flow estimation: DAFN and DAWarp
  3. Shallow encoder-decoder generation

![Brief description of DAFlow](https://github.com/kchyun/ai-fitting-room/assets/63688973/7e804b3f-6f5f-431c-9dd2-4a458d0ed7c6)
_Brief description of DAFlow_

![Input of DAFlow](https://github.com/kchyun/ai-fitting-room/assets/63688973/064be5e4-d247-4dff-acfb-6947de4cb6de)
_Input of DAFlow_

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

![Untitled](https://github.com/kchyun/ai-fitting-room/assets/63688973/005a6b0f-61fa-4d81-8066-a40a277fbf9b)

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

![상의 합성 결과](https://github.com/kchyun/ai-fitting-room/assets/63688973/84ac9fc3-7055-4378-8c8f-000a4ec1fabe)
_상의 합성 결과_

![하의 합성 결과](https://github.com/kchyun/ai-fitting-room/assets/63688973/690f2a8a-fe59-4317-8d33-e19462f3407a)
_하의 합성 결과_

![드레스 합성 결과](https://github.com/kchyun/ai-fitting-room/assets/63688973/d805fb16-01d1-404b-a9b2-171231bd5b9c)
_드레스 합성 결과_

### Inference with new images

![Untitled](https://github.com/kchyun/ai-fitting-room/assets/63688973/91eade1c-640e-4aeb-9437-cd60f2cffaad)

## ⛔ Limitation

![Failure cases](https://github.com/kchyun/ai-fitting-room/assets/63688973/27c38d46-3e0c-42a3-a52b-4b80f897ad4f)
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
