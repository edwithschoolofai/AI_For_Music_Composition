# MuseGAN

[MuseGAN](https://salu133445.github.io/musegan/)은 음악 생성 프로젝트입니다. 우리는 본질적으로 여러 개의 트랙 (악기)를 화음과 율동적인 구조, 다중 트랙의 상호의존성과 시간적 구조를 함께 사용하여 다성 음악을 만들어내는 데 목적이 있습니다. 우리의 지식에 있어서, 이 작업은 이러한 이슈들을 모두 고려하는 첫 번째 접근을 상징합니다.


모델은 [Lakh Pianoroll Dataset](https://salu133445.github.io/lakh-pianoroll-dataset/)
(LPD)과 [multi-track piano-roll](https://salu133445.github.io/musegan/data) 데이터 집합을 비지도학습 접근방식으로 훈련시킵니다. 제안된 모델은 사용자로부터 주어진 트랙을 합치거나 잘라내는 방식으로 음절을 생성할 수 있습니다. 구체적으로 여기서는 모델을 사용하여 베이스, 드럼, 기타, 피아노, 현악기 트랙으로 구성된 팝송을 생성할 수 있습니다.

샘플 결과는 [여기](https://salu133445.github.io/musegan/results)에서 확인할 수 있습니다.

## BinaryMuseGAN

[BinaryMuseGAN](https://salu133445.github.io/bmusegan/)은 [MuseGAN](https://salu133445.github.io/musegan/) 프로젝트의 후속 프로젝트입니다.

이 프로젝트에서 먼저 생성자를 통해 생성된 실제 값의 피아노 롤이 CNN 모델에서 판별자를 학습시키기가 얼마나 어려운지 관찰합니다. 이원화 이슈를 극복하기 위해 생성자에 추가적으로 리파이너 네트워크를 추가할 것을 제안합니다. 리파이너 네트워크는 미리 훈련된 생성자로부터 생성된 실제 예측값을 이진값으로 정제합니다. 제안된 모델은 테스트 때 바로 이진값의 피아노 롤을 생성할 수 있습니다.


네트워크를 [Lakh Pianoroll Dataset](https://salu133445.github.io/lakh-pianoroll-dataset/)
(LPD)로 훈련시킵니다. 우리는 모델을 사용하여 
*드럼*, *피아노*, *기타*, *베이스*, *앙상블*, *리드*, *Synth lead* ,*Synth Pad* 등 8개의 트랙으로 구성된 네 마디 음절을 생성합니다. 오디오 샘플은 [여기](https://salu133445.github.io/bmusegan/samples)에 제공되어 있습니다.


## 코드 실행

### 훈련 데이터 준비

- 여러분 자신의 데이터를 준비하거나 제공된 훈련 데이터를 다운로드 합니다.

  배열은 (-1, `num_bar`, `num_timestep`, `num_pitch`,
  `num_track`)의 형태로 재형성되어야 합니다. 변수들은 `config.py`에 정의되어 있습니다.

  - [`lastfm_alternative_5b_phrase.npy`](https://drive.google.com/uc?export=download&id=1F7J5n9uOPqViBYpoPT5GvE4PjCWhOyWc) (2.1 GB)
    *alternative*로 태그된 2074개 곡으로부터 12,444개의 네 마디 악절으로 구성되어 있습니다.
    배열의 형태는 (2074, 6, 4, 96, 84, 5)입니다. 다섯 개의 트랙은 각각 *드럼*, *피아노*,
    *기타*, *베이스*, *현악기*입니다.
  - [`lastfm_alternative_8b_phrase.npy`](https://drive.google.com/uc?export=download&id=1x3CeSqE6ElWa6V7ueNl8FKPFmMoyu4ED) (3.6 GB)
    *alternative*로 태그된 2291개 곡으로부터 13746개의 네 마디 악절으로 구성되어 있습니다.
    배열의 형태는 (2291, 6, 4, 96, 84, 8) 입니다. 여덟 개의 트랙은 각각 *드럼*, *피아노*,
    *기타*, *베이스*, *앙상블*, *Reed*, *Synth Lead*, *Synth Pad*입니다.
  - 이 [스크립트](training_data/download.sh)를 통해 다운로드 받습니다.

- (선택사항) 이 [스크립트](training_data/store_to_sa.py)를 통해 훈련 데이터를 공유 메모리에 저장합니다.

- `config.py`에서 훈련 데이터의 경로와 위치를 수정합니다. (아래 참조)

### 환경 설정

환경 설정을 위해 `config.py` 파일을 수정합니다.

- 빠른 설정

  빠른 설정을 원한다면 `SETUP` 디렉토리의 값을 변경합니다. 문서는 각각의 키마다 제공되어 있습니다.

- 더 많은 환경 설정 옵션

  `EXP_CONFIG`, `DATA_CONFIG`, `MODEL_CONFIG`, `TRAIN_CONFIG` 디렉토리는 각각 실험, 데이터, 모델, 훈련 관련 환경 설정 변수를 정의합니다.

  > 실험 이름은 `SETUP` 딕셔너리에 정의된 값을 가지고 자동으로 설정되니 실험 이름을 꼭 직접 설정하세요(훈련된 모델을 덮어쓰지 않도록요).

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
