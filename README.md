# MuseGAN

[MuseGAN](https://salu133445.github.io/musegan/) 은 음악 생성 프로젝트입니다. 우리는 본질적으로 여러 개의 트랙 (악기)를 화음과 율동적인 구조, 다중 트랙의 상호의존성과 시간적 구조를 함께 사용하여 다성 음악을 만들어내는 데 목적이 있습니다. 우리의 지식에 있어서, 우리의 작업은 이러한 이슈들을 모두 고려하는 첫 번째 접근을 상징합니다.


모델은 [Lakh Pianoroll Dataset](https://salu133445.github.io/lakh-pianoroll-dataset/)
(LPD)과 [multi-track piano-roll](https://salu133445.github.io/musegan/data) 데이터 집합을 비지도학습 접근방식으로 훈련시킵니다. 제안된 모델은 사용자로부터 주어진 트랙을 합치거나 잘라내는 방식으로 음악을 생성할 수 있습니다. 구체적으로 우리는 모델을 사용하여 베이스, 드럼, 기타, 피아노, 현악기 트랙으로 구성된 팝송을 생성할 수 있습니다.

샘플 결과는 [여기](https://salu133445.github.io/musegan/results)에서 확인할 수 있습니다.

## BinaryMuseGAN

[BinaryMuseGAN](https://salu133445.github.io/bmusegan/) 은 [MuseGAN](https://salu133445.github.io/musegan/) 프로젝트의 후속 프로젝트입니다.

이 프로젝트에서 우리는 먼저 생성자를 통해 생성된 실제 값의 piano-rolls?가 CNN 모델에서 판별자를 학습시키기가 얼마나 어려운지 관찰합니다. 이원화 이슈를 극복하기 위해 생성자에 추가적으로 refiner(정제기?) 네트워크를 추가할 것을 제안합니다. refiner 네트워크는 미리 훈련된 생성자로부터 생성된 실제 예측값을 이진값으로 정제합니다. 제안된 모델은 테스트 때 바로 이진값의 piano-rolls를 생성할 수 있습니다.


우리는 네트워크를 [Lakh Pianoroll Dataset](https://salu133445.github.io/lakh-pianoroll-dataset/)
(LPD)로 훈련시킵니다. 우리는 모델을 사용하여 
*드럼*, *피아노*, *기타*, *베이스*, *앙상블*, *리드*, *Synth lead* ,*Synth Pad*등 8개의 트랙으로 구성된 four-bar? 음악을 생성합니다. 오디오 샘플은 [여기](https://salu133445.github.io/bmusegan/samples)에 제공되어 있습니다.


## 코드 실행

### 훈련 데이터 준비

- Prepare your own data or download our training data

  The array will be reshaped to (-1, `num_bar`, `num_timestep`, `num_pitch`,
  `num_track`). These variables are defined in `config.py`.

  - [`lastfm_alternative_5b_phrase.npy`](https://drive.google.com/uc?export=download&id=1F7J5n9uOPqViBYpoPT5GvE4PjCWhOyWc) (2.1 GB)
    contains 12,444 four-bar phrases from 2,074 songs with *alternative* tags.
    The shape is (2074, 6, 4, 96, 84, 5). The five tracks are *Drums*, *Piano*,
    *Guitar*, *Bass* and *Strings*.
  - [`lastfm_alternative_8b_phrase.npy`](https://drive.google.com/uc?export=download&id=1x3CeSqE6ElWa6V7ueNl8FKPFmMoyu4ED) (3.6 GB)
    contains 13,746 four-bar phrases from 2,291 songs with *alternative* tags.
    The shape is (2291, 6, 4, 96, 84, 8). The eight tracks are *Drums*, *Piano*,
    *Guitar*, *Bass*, *Ensemble*, *Reed*, *Synth Lead* and *Synth Pad*.
  - Download the data with this [script](training_data/download.sh).

- (optional) Save the training data to shared memory with this [script](training_data/store_to_sa.py).

- Specify training data path and location in `config.py`. (see below)

### Configuration

Modify `config.py` for configuration.

- Quick setup

  Change the values in the dictionary `SETUP` for a quick setup. Documentation
  is provided right after each key.

- More configuration options

  Four dictionaries `EXP_CONFIG`, `DATA_CONFIG`, `MODEL_CONFIG` and
  `TRAIN_CONFIG` define experiment-, data-, model- and training-related
  configuration variables, respectively.

  > The automatically-determined experiment name is based only on the values
defined in the dictionary `SETUP`, so remember to provide the experiment name
manually (so that you won't overwrite a trained model).

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

\* *These authors contributed equally to this work.*
