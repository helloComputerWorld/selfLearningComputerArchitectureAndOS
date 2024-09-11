# Chapter05. CPU 성능 향상 기법

## 05-1 빠른 CPU를 위한 설계 기법

### 1. 클럭

- 컴퓨터 부품들은 '클럭 신호'에 맞춰 움직임 -> CPU는 '명령어 사이클' 흐름에 맞춰 명령어들을 실행함
- 클럭 신호가 빠르게 반복되면 CPU를 비롯한 컴퓨터 부품들은 그만큼 빠르게 움직임
- 클럭 속도가 높은 CPU는 일반적으로 성능이 좋음
- 클럭 속도
  - 클럭 속도는 CPU 속도 단위로 간주되기도 함
  - 헤르츠(Hz) 단위로 측정함
  - 1초에 클럭이 몇 번 반복되는지를 나타냄
  - 클럭 속도는 일정하지 않음
    - 기본 클럭 속도(Base)와 최대 클럭 속도(Max)로 나뉘어 있음
    - 고성능을 요하는 순간에 순간적으로 클럭 속도를 높이고, 그렇지 않을 때는 유연하게 클럭 속도를 낮춤
    - 최대 클럭 속도를 강제로 더 끌어올리는 기법 -> 오버클럭킹
  - 클럭 속도를 무작정 높이면 발열 문제 심각
  - 클럭 속도만으로 CPU의 성능을 올리는 것에는 한계 존재

### 2. 코어와 멀티코어

- CPU의 코어와 스레드 수를 늘리는 방법
- 코어 - 명령어를 실행하는 부품
- 멀티코어 CPU / 멀티코어 프로세서 - 코어를 여러 개 포함하고 있는 CPU
  - 명령어를 처리하는 일꾼이 여러 명 있는 것과 같음
- CPU의 연산 속도가 코어 수에 비례하여 증가하지는 않음
- 코어마다 처리할 연산이 적절히 분배되지 않으면 코어 수에 비례하여 연산 속도가 증가하지 않음
- 코어마다 처리할 명령어들을 얼마나 적절하게 분배하느냐이며 연산 속도가 크게 달라짐

### 3. 스레드와 멀티스레드

- 스레드
  - 실행 흐름의 단위
  - 하드웨어적 스레드
    - 하나의 코어가 동시에 처리하는 명령어 단위
    - CPU에서 사용하는 스레드라는 용어를 의미
    - 1코어 1스레드 CPU -> 명령어를 실행하는 부품이 하나 있고, 한 번에 하나씩 명령어를 실행하는 CPU
    - 여러 스레드를 지원하는 CPU -> 하나의 코어로도 여러 개의 명령어를 동시에 실행할 수 있음
      - 2코어 4스레드 CPU -> 명령어를 실행하는 부품 2개 / 한 번에 4개의 명령어를 처리할 수 있는 CPU
    - 멀티스레드 프로세서 / 멀티스레드 CPU - 하나의 코어로 여러 명령어를 동시에 처리하는 CPU
      - 하이퍼스레딩: 인텔의 멀티스레드 기술
  - 소프트워어적 스레드
    - 하나의 프로그램에서 독립적으로 실행되는 단위
    - 프로그래밍 언어나 운영체제를 학습할 때 접하는 스레드를 의미
    - 하나의 프로그램은 실행되는 과정에서 한 부분만 실행될 수도 있지만, 프로그램의 여러 부분이 동시에 실행될 수도 있음
    - 1코어 1스레드 CPU도 소프트웨어적 스레드를 수십 개 실행할 수 있음
    - 1코어 1스레드 CPU로도 프로그램의 여러 부분을 동시에 실행할 수 있음
  - 멀티스레드 프로세서
    - 가장 핵심은 레지스터
    - 하나의 명령어를 처리하기 위해 꼭 필요한 레지스터를 여러개 가지고 있으면 됨
    - 메모리 속 프로그램 입장에서 봤을 때 하드웨어 스레드는 '한 번에 하나의 명령어를 처리하는 CPU'나 다름 없음
    - 2코어 4스레드 CPU는 4개의 명령어를 처리할 수 있는데, 프로그램 입장에서는 한 번에 하나의 명령어를 처리할 수 있는 CPU가 4개 있는 것처럼 보임
    - 하드웨어 스레드를 논리 프로세서라고 부르기도 함

## 05-2 명령어 병렬 처리 기법

### 1. 명령어 파이프라인

- 명령어 처리 과정 (클럭 단위)
  - 명령어 인출
  - 명령어 해석
  - 명령어 실행
  - 결과 저장
- 같은 단계가 겹치지만 않는다면 CPU는 각 단계를 동시에 실행할 수 있음
- 예. CPU는 한 명령어를 '인출'하는 동안에 다른 명령어를 '실행'할 수 있음
- 명령어 파이프라이닝 - 동시에 여러 개의 명령어를 겹쳐 실행하는 기법 / 명령어 파이프라인에 넣고 동시에 처리하는 기법
- 파이프라인 위험
  - 성능 향상에 실패하는 상황을 의미
  - 데이터 위험
    - 명령어 간 '데이터 의존성'에 의해 발생
    - 모든 명령어를 동시에 처리할 수는 없음 - 이전 명령어를 끝까지 실행해야만 실행할 수 있는 경우 존재
    - 데이터 의존적인 두 명령어를 무작정 동시에 실행하려고 하면 파이프라인이 제대로 작동하지 않는 것을 의미
  - 제어 위험
    - 분기 등으로 인한 '프로그램 카운터의 갑작스러운 변화'에 의해 발생함
    - 기본적으로 프로그램 카운터는 '현재 실행 중인 명령어의 다음 주소'로 갱신됨
    - 프로그램 실행 흐름이 바뀌어 명령어가 실행되면서 프로그램 카운터 값에 갑작스러운 변화가 생긴다면 명령어 파이프라인에 미리 가지고 와서 처리 중이었던 명령어들은 쓸모가 없어짐
    - 분기예측 - 프로그램이 어디로 분기할지 미리 예측한 후 그 주소를 인출하는 기술
  - 구조적 위험
    - 명령어들을 겹쳐 실행하는 과정에서 서로 다른 명령어가 동시에 ALU, 레지스터 등과 같은 CPU 부품을 사용하려고 할 때 발생
    - 자원 위험이라고 부르기도 함

### 2. 슈퍼스칼라

- CPU 내부에 여러 개의 명령어 파이프라인을 포함한 구조를 의미
- 슈퍼스칼라 프로세서 / 슈퍼스칼라 CPU - 슈퍼스칼라 구조로 명령어 처리가 가능한 CPU
- 매 클럭 주기마다 동시에여러 명령어를 인출할 수도, 실행할 수도 있어야 함
- 이론적으로 파이프라인 개수에 비례하여 프로그램 처리 속도가 빨라짐
- 파이프라인 위험 등의 예상치 못한 문제가 있어 실제 비례하지는 않음
- 여러 개의 파이프라인을 이용하면 하나의 파이프라인을 사용할 때보다 파이프라인 위험을 피하기 까다로움

### 3. 비순차적 명령어 처리 (OoOE)

- 명령어들을 순차적으로 실행하지 않는 기법
- 모든 명령어를 순차적으로만 처리한다면 예상치 못한 상황에서 명령어 파이프라인은 멈춰버리게 됨
- 명령어를 순차적으로만 실행하지 않고 순서를 바꿔 실행해도 무방한 명령어를 먼저 실행하여 파이프라인이 멈추는 것을 방지하는 기법
- 비순차적 명령어 처리가 가능한 CPU는 명령어들이 어떤 명령어와 데이터 의존성을 가지고 있는지, 순서를 바꿔 실행할 수 있는 명령어에 어떤 것들이 있는지를 판단할 수 있어야 함

## 05-3 CISC와 RISC

### 1. 명령어 집합

- 명령어 집합 / 명령어 집합 구조(ISA) - CPU가 이해할 수 있는 명령어들의 모음
- ISA가 다르다는 건 CPU가 이해할 수 있는 명령어가 다르다는 뜻, 명령어가 달라지면 어셈블리어도 달라짐
  - 같은 소스 코드로 만드렁진 같은 프로그램이라 할지라도 ISA가 다르면 CPU가 이해할 수 있는 명령어도 어셈블리어도 달라짐
- ISA는 일종의 CPU의 언어
- ISA가 달라지면 제어장치가 명령어를 해석하는 방식, 사용되는 레지스터의 종류와 개수 등 많은 것이 달라짐 -> CPU 하드웨어 설계에도 큰 영향을 미침
- ISA는 CPU의 언어이자 하드웨어가 소프트웨어를 어떻게 이해할지에 대한 약속
- 명령어 파이프라인, 슈퍼스칼라, 비순차적 명령어 처리를 사용하기에 유리한 명령어 집합이 있고, 그렇지 못한 명령어 집합도 있음

### 2. CISC

- Complex Instruction Set Computer - 복잡한 명령어 집합을 활용하는 컴퓨터
- 복잡하고 다양한 명령어들을 활용하는 CPU 설계 방식
- 명령어의 형태와 크기가 다양한 가변 길이 명령어를 활용함
- 메모리에 접근하는 주소 지정 방식도 다양해서 아주 특별한 상황에서만 사용되는 독특한 주소 지정 방식들도 있음
- 다양하고 강력한 명령어를 활용한다는 말은 상대적으로 적은 수의 명령어로도 프로그램을 실행할 수 있다는 것을 의미 (= 컴파일된 프로그램의 크기가 작음을 의미)
- 장점 - 메모리 공간을 절약할 수 있음 -> 메모리를 최대한 아끼며 개발해야 했던 시절 인기가 높았음
- 단점
  - 활용하는 명령어가 복잡하고 다양한 기능을 제공하여 며령어의 크기와 실행되기까지의 시간이 일정하지 않음
  - 복잡한 명령어 때문에 명령어 하나를 실행하는 데에 여러 클럭 주기를 필요로 함
  - 명령어 파이프라인을 구현하는 데에 큰 걸림돌이 됨
  - 명령어 파이프라인 기법을 위한 이상적인 명령어는 각 단계에 소요되는 시간이 동일해야 함
  - 명령어 파이프라인이 제대로 동작하지 않는다는 것은 현대 CPU에서 아주 치명적인 약점
  - 복잡하고 다양한 명령어를 활용할 수 있지만, 사용 빈도가 낮음

### 3. RISC

- Reduced Instruction Set Computer
- 짧고 규격화된 명령어, 되도록 1크럵 내외로 실행되는 명령어를 지향함
- 고정 길이 명령어를 활용함
- 영령어 파이프라이닝에 최적화되어 있음
- 메모리에 직접 접근하는 명령어를 load, store 두 개로 제한할 만큰 메모리 접근을 단순화하고 최소화를 추구함
- 메모리 접근을 단순화, 최소화하는 대신 레지스터를 적극적으로 활용함
- CISC보다 레지스터를 이용하는 연산이 많고, 일반적인 경우보다 범용 레지스터 개수도 더 많음
- 사용 가능한 명령어 개수가 적기 때문에 RISC는 CISC보다 많은 명령으로 프로그램을 작동시킴