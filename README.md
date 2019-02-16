# MuseGAN

[MuseGAN](https://salu133445.github.io/musegan/)은 음악 생성 프로젝트입니다. 우리는 여러 트랙들(악기들)을 화음과 리듬, 상호 독립성, 템포에 맞도록 구성하여 다성 음악을 만들어내고자 합니다. 우리가 아는 한, 이번 작업은 위 이슈들을 모두 다룬 최초 시도입니다.


모델은 [Lakh Pianoroll Dataset](https://salu133445.github.io/lakh-pianoroll-dataset/)
(LPD)과 [multi-track piano-roll](https://salu133445.github.io/musegan/data) 데이터셋을 통해 비지도학습 접근방식으로 훈련시킵니다. 이 모델은 완전히 처음부터 음악을 생성하며, 사용자로부터 주어진 트랙을 기반으로도 할 수 있습니다. 특히 여기서는 베이스, 드럼, 기타, 피아노, 현악기로 구성된 팝송을 생성할 것입니다.

샘플 결과는 [여기](https://salu133445.github.io/musegan/results)에서 확인할 수 있습니다.

## BinaryMuseGAN

[BinaryMuseGAN](https://salu133445.github.io/bmusegan/)은 [MuseGAN](https://salu133445.github.io/musegan/) 프로젝트의 후속 프로젝트입니다.

이 프로젝트에서 생성자가 생성한 피아노 곡으로, CNN 모델을 위한 판별자 학습이 얼마나 어려운 지 살펴볼 겁니다. 이진화(binarization) 이슈를 극복하기 위해 생성자에 추가적으로 리파이너(refiner) 네트워크를 추가하는 것이 좋습니다. 리파이너 네트워크는 미리 학습시킨 생성자가 생성한 실제 예측값을 이진값으로 바꿔줍니다. 이 모델로, 테스트 때 바로 피아노 곡을 이진값으로 생성할 수 있습니다.


[Lakh Pianoroll Dataset](https://salu133445.github.io/lakh-pianoroll-dataset/)
(LPD)를 사용해서 네트워크를 훈련시켰습니다. 이 모델로
*드럼*, *피아노*, *기타*, *베이스*, *앙상블*, *리드*, *Synth lead* ,*Synth Pad*의 8개의 트랙으로 구성된 네 마디 음절을 생성합니다. 오디오 샘플은 [여기](https://salu133445.github.io/bmusegan/samples)에 올라와 있습니다.


## 코드 실행

### 훈련 데이터 준비

- 각자 사용할 데이터를 준비하거나, 다음 학습 데이터를 다운로드 합니다.

  배열은 (-1, `num_bar`, `num_timestep`, `num_pitch`,
  `num_track`)의 형태로 reshaped해야 합니다. 각 변수들은 `config.py`에 정의되어 있습니다.

  - [`lastfm_alternative_5b_phrase.npy`](https://drive.google.com/uc?export=download&id=1F7J5n9uOPqViBYpoPT5GvE4PjCWhOyWc) (2.1 GB)는
    *alternative*로 태그된 2,074개 곡으로부터 12,444개의 네 마디 악절으로 구성되어 있습니다.
    배열의 형태는 (2074, 6, 4, 96, 84, 5)입니다. 다섯 개의 트랙은 각각 *드럼*, *피아노*,
    *기타*, *베이스*, *현악기*입니다.
  - [`lastfm_alternative_8b_phrase.npy`](https://drive.google.com/uc?export=download&id=1x3CeSqE6ElWa6V7ueNl8FKPFmMoyu4ED) (3.6 GB)는
    *alternative*로 태그된 2,291개 곡으로부터 13,746개의 네 마디 악절으로 구성되어 있습니다.
    배열의 형태는 (2291, 6, 4, 96, 84, 8) 입니다. 여덟 개의 트랙은 각각 *드럼*, *피아노*,
    *기타*, *베이스*, *앙상블*, *Reed*, *Synth Lead*, *Synth Pad*입니다.
  - 이 [스크립트](training_data/download.sh)를 통해 다운로드 받습니다.

- (선택사항) 이 [스크립트](training_data/store_to_sa.py)를 통해 학습 데이터를 공유 메모리에 로드합니다.

- `config.py`에서 학습 데이터의 경로와 위치를 수정합니다. (아래 참조)

### 환경 설정

환경 설정을 위해 `config.py` 파일을 수정합니다.

- 빠른 설정

  빠른 설정을 원한다면 `SETUP` 디렉토리의 값을 변경합니다. 문서는 각 키마다 제공되어 있습니다.

- 더 많은 환경 설정 옵션

  `EXP_CONFIG`, `DATA_CONFIG`, `MODEL_CONFIG`, `TRAIN_CONFIG` 디렉토리는 실험, 데이터, 모델, 학습 관련 환경변수를 정의합니다.

  > 미지정시 `SETUP` 딕셔너리에 정의된 값으로 자동설정되니, 꼭 이름을 직접 설정해주세요 (학습된 모델을 덮어쓰지 않도록).
 
### 실행

```sh
python main.py
```

## 관련 자료

- Hao-Wen Dong and Yi-Hsuan Yang,
  "Convolutional Generative Adversarial Networks with Binary Neurons for
  Polyphonic Music Generation",
  *arXiv preprint, arXiv:1804.09399*, 2018.
  [[arxiv](https://arxiv.org/abs/1804.09399)]

- Hao-Wen Dong\*, Wen-Yi Hsiao\*, Li-Chia Yang and Yi-Hsuan Yang,
  "MuseGAN: Multi-track Sequential Generative Adversarial Networks for
  Symbolic Music Generation and Accompaniment,"
  in *AAAI Conference on Artificial Intelligence* (AAAI), 2018.
  [[arxiv](http://arxiv.org/abs/1709.06298)]
  [[slides](https://salu133445.github.io/musegan/pdf/musegan-aaai2018-slides.pdf)]

- Hao-Wen Dong\*, Wen-Yi Hsiao\*, Li-Chia Yang and Yi-Hsuan Yang,
  "MuseGAN: Demonstration of a Convolutional GAN Based Model for Generating
  Multi-track Piano-rolls,"
  in *ISMIR Late-Breaking and Demo Session*, 2017.
  (non-peer reviewed two-page extended abstract)
  [[paper](https://salu133445.github.io/musegan/pdf/musegan-ismir2017-lbd-paper.pdf)]
  [[poster](https://salu133445.github.io/musegan/pdf/musegan-ismir2017-lbd-poster.pdf)]

\* *위의 저자들이 함께 만들었습니다.*
