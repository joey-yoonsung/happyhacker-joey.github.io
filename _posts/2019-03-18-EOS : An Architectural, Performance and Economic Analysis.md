# EOS : An Architectural, Performance and Economic Analysis

## II. Architecture
아키텍처 분석은 EOS 플랫폼의 다양한 레이어를 평가하고 주요 컴포넌트들이 적절한 기능을 하는지 확인 하는 방식으로 구성되어 있다.

### A. Overview
 EOS 는 분산된 데이터베이스와 상호작용하는 어플리케이션의 집합이다. 
 [Fig.1]() 에서 보는것과 같이, EOS 는 세개의 주요 컴포넌트로 구성된 소프트웨어 시스템이다.
 Nodeos, Cleos, Keosd. 사용자는 Command line tool 인 Cleos 를 이용해서 네트워크에 접근한다.
 이 Cleos 는 부분적으로 Nodeos 에 연결해서 DBMS 에 연결한다.
 이 시스템은 서로 느슨한 결합구조로 연결된 컴퓨팅 유닛들의 네트워크로 구성되어 있다.
 이 구조를 통해서 서로 다른 프로세스들이 EOS 데이터베이스의 변화에 따라 업데이트되는 것을 가능하다.
 그 동안에 컴퓨팅 부하는 블록 생성자들사이에서 분산되어 처리된다.
 
#### 1) General Architecture
EOS 는 특별한 실행 환경에서 동작하기 위한 시스템 최적화된 접근방식을 이용한다.
이 시스템은 EOS 의 합의 모델에 기초하기 때문에 완전히 자동화 된 시스템은 아니다.
이 합의 모델은 21개의 블록생성자에게 심하게 의존적이다.
데이터와 연산은 중앙의 마스터 데이터베이스 시스템을 만드는 네트워크에 속한 21개의 블록 생성자에게 분산된다.
이 데이터베이스 시스템은 네트워크 사이의 업데이트를 조정한다.
이 분산된 데이터베이스는 동일해야 한다. 
왜냐하면 21개의 블록 생성자들이 EOS 네트워크 안에 있는 모든 분리된 데이터베이스들 사이에서 거래를 처리하고 합의해야 하기 때문이다.
EOS 는 그들 전용의 LLVM를 만들어왔다. 이 LLVM은 EOS C++ tool chain 이 contract를 compile하는데 사용된다.
네트워크의 노드들은 네트워크 전체에 걸쳐서 거래와 계약을 처리하고 인증해야함으로 반드시 같은 프로토콜을 구현해야한다.

#### 2) Components
* **Nodeos**
노드를 구축하기 위한 핵심 컴포넌트이다. 
정확히는 노드를 구동하기 위한 다양한 플러그인들을 사용하는 하나의 데몬을 설정한다.
노드들은 블록 생성자의 역할을 하는 producing node가 되거나 단순히 계정에만 연동된 non-producing node가 될 수 있다.
Non-producing node들은 개발자들을 위해서 하나의 공개된 HTTP-RPC API로 구현될 수 있다.
이 노드들은 로컬 개발환경에서 컴퓨팅 인프라를 사용하기를 원하는 어플리케이션들을 위한 하나의 사적인 시작점으로 활용할 수 있다.

* **Cleos**
클라이언트는 Command line tool 인 Cleos를 이용해서 EOS 네트워크에 접근할 수 있다.
Cleos 는 전체 네트워크와 상호작용이 가능하고, Nodeos를 통해서 네트워크내의 상호작용을 설정할 수도 있다.
네트워크의 블록생성자는 클라이언트에 의해 소비되는 자원의 대부분을 주최, 전달, 관리할 수 있다.
또한 클라이언트에게 직접적으로 거래 정보에 접근하도록 허용할 수도 있다.

* **Keosd**
Keosd는 거래에 서명하는 비밀키들을 저장하는 용도로 고안되었다.
Keosd는 로컬환경에서 동작하고 키 또한 로컬에 저장한다. 
유저는 Keosd의 PRC API를 이용해서 키들을 사용하고 조회할 수 있다.

EOS 를 구동하는 시스템은 구동하기 전에 미리 빌드된 Cleos 와 Nodeos 의 인스턴스가 필요하다.

네트워크가 생성되면 다양한 컴포넌트들이 네트워크에 특정 동작들을 하는 것이 가능해진다.
이 동작들은 몇가지 작업으로 구성된다.
 * 기본 플러그인을 로딩한다.
 * 서버 주소를 세팅한다.
 * CORS가 가능하게 한다.
 * contract 의 디버깅을 추가한다.
 
네트워크를 적절히 설정하는 것은 Nodeos가 네트워크의 블록을들을 검사하고 동기화해야 가능해진다.
우리가 로컬 환경에서 Cleos 에 어떤 명령어를 만들었다면, 그 명령어는 Nodeos 어플리케이션 안에서 처리된다.
이 네트워크 구축은 네트워크의 서로 다른 컴포넌트들 사이에 잘 동작하기 위해서 필수적인 작업이다.
인터페이스는 노드들 사이의 동작을 위해서 만들어졌기 때문에 네트워크는 거래가 블록 생성자의 컴퓨팅 자원을 공유하여 효율적으로 처리되도록 한다.

#### 3) Contract Development Tool
CDT는 contract 의 compile 과 API 생성을위해서 필수적인 도구들의 집합이다.
eosio.cdt 와 연계된 바이너리는 로컬 머신에 저장된다.
eosio.cdt 저장소는 eosio-cpp 컴포넌트를 포함하고 있는데, 이것은 C++ code 를 WASM으로 컴파일한다.
이 방법은 어떻게 ABI 들이 사용자액선을 JSON 과 바이너리 형식 사이에서 


[resource 할당 방법](https://medium.com/leclevietnam/eos-resource-allocation-98bb6cb84497)

![Fig.1 Overview of EOS System Architecture]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.1 Overview of EOS System Architecture.png" | absolute_url}})
![Fig.2 How Accounts Fit Into the EOS System]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.2 How Accounts Fit Into the EOS System.png" | absolute_url}})

### C. Transactions and Contracts
#### 1) Overview
#### 2) Contracts
Smart Contract 는 actions 과 action handler 조합의 집합.
Action handler는 action 을 전송하는 것을 통해서 다른 계정들에 접근하도록 고안되었다.
이 action은 각 계정에 연동되어있는 개인 데이터베이스와 상호작용할 수 있다.
블록생성자는 메모리와 자원 사용에 집중된 action의 충돌을 피하는데 최적화하여 거래를 스케줄링한다. 

#### 3) Web Assembly
WASM은 많은 high level 언어들을 컴파일하는데 사용된다.
WASM은 EOS VM 이 이해할 수 있는 명령어 형태이다.
이것은 Web Assembly module을 통해서 EOS library 와 WASM binary code 사이의 상호작용이 일어난다는 의미이다.

#### 4) Components

**Actions** : single atomic units of operation.
Action 은 contract 와 계정 사이의 인터페이스를 나타낸다. 
대부분의 EOS network 는 action 을 통해서 만들어진다.

**Transactions** : Transaction(거래) 은 actions의 실행을 나타낸다.
Transaction은 개별 액션 또는 여러개의 액션으로 구성되어있다.
거래가 성공했다는 것은 EOS blockchain 안에 있는 어떤 상태가 변경되었다는 의미이다.
Transaction 과 action 의 관계는 Fig.3 에 나타나있다.

![Fig.3 EOS Actions]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.3 EOS Actions.png" | absolute_url}})

#### 5) Communications and Actions
EOS contract 들은 현재의 actions 과 미래의 actions 사이의 의존성을 주어서 서로 커뮤니케이션/상호작용을 할 수 있다.
contract들 사이에 일어나는 커뮤니케이션은 비동기적으로 일어난다.
자원 분산 알고리즘의 다음 단계는 이러한 관점을 나타내도록 고안되었다.
EOS 에서 contract는 노드에의 해서 등록되는 소프트웨어이다.
EOS contract 를 전송하는 과정은 Fig.4 에 나타나있다.

![Fig.4 Contracts in the EOS System Architecture]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.4 Contracts in the EOS System Architecture.png" | absolute_url}})

Contract들과 계정들은 개별 action 으로 커뮤니케이션하는 것으로 나타나있다.
몇개의 action 이 조합되면 그것은 거래들에 담긴다.
이렇게 생성된 거래는 action 들의 실행을 순차적으로 실행한다. Fig.5 를 보자.
거래가 생성되고 응답은 hash 헝태로 받게 된다.
노드가 거래의 응답과 hash를 받게되면, 그 거래는 블록 생성자에의해 처리될 가능성이 높아진다.
하지만 100% confirm 된다는 의미는 아니다.

![Fig.5 Action Handlers Can Apply Multiple Contexts]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.5 Action Handlers Can Apply Multiple Contexts.png" | absolute_url}})

거래는 Fig.6처럼 두가지 커뮤니케이션 모델로 구성된다.
이 거래들은 즉각적인 관심을 요구하는 action들을 점진적으로 구성하는 것을 제공한다.
반면에 deferred actions 은 더 복잡한 비즈니스 로직을 요구한다.


6) Analysis
EOS 는 전체 시스템을 구성하는대 contract based model 을 사용한다. 
이 모델은 system contract 라는 형태로 프로토콜의 가장 낮은 레벨까지 스며든다
EOS의 Contract 는 컴퓨팅 함수를 실행하기 위해 디자인된 전통적인 코드라인을 수행하는데 더 적합하다.
Ethereum 의 컨트랙트는 이더리움 프로토콜 상에서만 구동되는 것을 제약하는 것과 대조적이다.
EOS 의 블록 생성자들은 반드시 플러그인과 모듈들을 필수로 가져야 한다.
Ethereum의 Smart Contract 는 직접적으로 state 전환과 value 전달, 그리고 그들의 경제 시스템을 결정하는 암호학적인 결정 알고리즘에 직접적으로 묶여있다.
state 검증이 머클트리를 통해서.
EOS 는 transaction 단위에서 X.

* 요약 Ethereum 은 블록체인 시스템. EOS 는 분산화된 DBMS 시스템 



![Fig.6 Two Basic EOS Communication Models]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.6 Two Basic EOS Communication Models.png" | absolute_url}})
![Fig.11 Z.Cole's Triangle]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.11 Z.Cole's Triangle.png" | absolute_url}})



### E. Execution Environment

#### 1) Overview
EOS 의 노드 세팅은 탈중앙화, 확장성, 네트워크의 자동화된 안정성 그리고 전체적인 Fault Tolerance 의 특징들을 결정한다.
이후의 내용은 어떻게 메세지가 전달되는지에 집중해서 보자.

#### 2) Setup
EOS 의 세팅은 ecosystem 내의 많은 양의 서버들이 컴퓨팅 자원과 어플리케이션을 사용하기 위한 플랫폼을 목표로 한다.
이 네트워크는 Fig.7 의 그림과 같이 3개의 layer 로 되어있다.
![Fig.7 Overview of How EOS MultiIndex DB Works]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.7 Overview of How EOS MultiIndex DB Works.png" | absolute_url}})

 * EOS Core Network : 가장 내부의 계층
 * EOS Access Layer : Core Network 를 캡슐화한다.
 * EOS Consumers : 누구나 접근할 수있는 네트워크.
 
이 계층은 분리된 것이 아니라 어느 노드든 참여할 수 있고 이동이 가능하다. 
이 구조는 BP(Block Producer)들이 최고의 컴퓨팅 효율을 내고, 그 컴퓨팅 자원에 누구든지 접근할 수 있도록 하는 것을 목표로 디자인 되었다.

#### 3) EOS Core Network
![Fig.8 Network Access Layers of the EOS System]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.8 Network Access Layers of the EOS System.png" | absolute_url}})
![Fig.9 The Role of The Core Network in EOS]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.9 The Role of The Core Network in EOS.png" | absolute_url}})
EOS Core Network 는 21개의 BP로 구성되어 있다.
이 BP 노드들은 최고의 컴퓨팅 파워를 내는데 맞추어 디자인되어있다.
메인 네트워크의 서버들은 Failsafe 기능을 제공하는 서버들이다.
그리고 이서버들은 강력한 방화벽으로 보호되고 있다.
하나의 BP는 안전한 디스크 공간과 수용량을 가진 여러개의 industrial-grade 서버로 구성 될 수 있다.
BP의 위치와 정보는 네트워크의 안전을 위해서 비밀로 유지된다.
각 서버들은 third-party 도구들로 접근이 불가능한 서버이다.
그리고 대단한 컴퓨팅 파워를 지원한다.

#### 4) EOS Access Network
EOS 네트워크는 수직적 확장이 가능한 분산 네트워크 환경을 지원한다.
이러한 확장성은 processing power 를 Core 네트워크에만 위치하도록 해서 가능하다.
이렇게 Core 네트워크가 높은 컴퓨팅 능력을 제공하기 위해서 EOS Access Network 는 consumer 들로부터 오는 네트워크 요청을 필터링하는 역할을 한다.
Fig.10 을 보자.
![Fig.10 The Role of the Access Netwrok in EOS]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.10 The Role of the Access Netwrok in EOS.png" | absolute_url}})
Consumer 로부터 오는 요청을 Access Network 가 받아서 Core Network의 부하를 감소시켜준다.

이 access 네트워크 또한 보안과 방화벽 처리가 되어있다.
access network 의 주요 기능은 다음과 같다.
 * proxy services
 * load balancing
 * DDos proctection
 
이 Access Network 는 두 종류의 노드로 구성된다. : API nodes, Seed nodes.

API 노드가 하는 일, 특징
 * 불필요한 작업을 없애고 proxy 와 load balancer 뒤에서 일한다.
 * transaction 선처리
 * BP 노드에 직접 접근하는 가능성을 감소시킴
 * action request 를 처리
 * bad transaction 필터링
 * 정상 transaction을 BP 나 다른 노드로 relay
 * 각 BP node 는 하나의 API 노드에 결합되어있다.
 
Seed 노드가 하는 일, 특징
 * 동기화 노드이다.
 * EOS network protocol 로만 통신
 * HTTP protocol 로 설정 불가능.
 * Transaction 처리하지 않음
 * Cleos 로 접근 불가능.
 * 모든 BP 는 최소한 한 개의 seed 노드에 결합되어있다.
 
#### 5) EOS Consumer Network
Cleos 나 EOS RPC API 로 EOS network 에 접근
 * Cleos : transaction 발생과 관련
 * EOS RPC API : 동기화
 
#### 6) Analysis
1. EOS 에 access 하기 위해서는 몇개의 layer 를 거쳐야 한다.
2. Top-Down 관점으로 구성되어있고, 탈중앙화 참여를 제한한다.
3. 결국 중앙화된 설정이다. 탈중앙화에 기초하지 않음. Core N/W 는 불투명하다.
4. 정말 BP 들이 같은 S/W 를 쓰고있는지 알고리즘, 암호학적으로 증명해야한다.
5. 앞에서 얘기한 것 처럼 Client-Server 모델이다. AWS 처럼 중앙화된 서버에 접근하는 방식이다.
6. 이더리움과 비교
    * client-server 의 특징을 감소시켰다.
    * P2P 커뮤니케이션이다.
    * 어느 노드던지 네트워크 validate에 참여할 수 있다.
    * 어느 노드던지 네트워크에 transaction 을 발생시킬 수 있다.
    * Middle layer 가 필요없다.
    * Web 3.0 P2P sapce 에 접근하는 새로운 탈중앙화 모델이다.

결론 - EOS 는 중앙화된 클라우드 서버 아키텍처이다. BP들은 SPOF(single point of failure)이다.


### F. Consensus

#### 1) Overview
EOS 는 공격을 막기 위해 DPoS 라는 매커니즘을 선택했다.

처음 블록체인개념은 공격(이중지불)을 막기 위해서 탈중앙화 방법을 사용헀다.
그런데 EOS 는 공격을 막기 위해서 중앙화된 합의를 이용하는 DPoS 를 사용했다.
즉, EOS 는 탈중앙화를 위해서 DPoS 를 선택한 것이 아니다.
탈중앙화 개념이 공격을 막기 위해서 만들어진 것을 생각해보면, EOS는 탈중앙화 시스템이 아니다.

#### 2) Multiple Stages
합의는 두 단계로 이루어짐
 * 1단계 : Stake voting

 * 2단계 : Vote -> apply it next epoch

한번 BP로 선정되면, 다음 epoch 때까지 자격이 유지.
스케줄링에 의해 각 BP 는 0.5초 간격으로 6개의 블록을 생성할 수 있다.
각 epoch 동안 대략 126초 정도가 지속된다. (21개의 BP 가 12개의 블록을 생성하는 것이 최대라고 하면)

#### 3) Execution
BP가 다음 블록을 DB에 생성하면, BP들은 valid 한 transaction 과 contract 를 실행하고 storage에 저장한다.
invalid 한 것은 필터링 시킨다.
하나의 데이터 세트가 처리되고 구성되면, BP 는 그 정보를 다른 BP들에게 broadcast 한다.
15개의 BP가 해당 data set 을 validate 하고 sign 하면 transaction 이 finalize 된다.

만약에 BP 가 어떤 transaction 세트도 생성하지 못하면 빈 transaction 을 담는다.

#### 4) Transactions as Proof of Stake
각 transaction 은 블록 number를 참조한다. BP 들은 블록자체는 validate 하지 않는다.
단지, 그들이 기대하는 방식으로 data streaming service 에 맞게 transaction 들을 구성한다.
Transaction들은 이전 블록에 결합되어있고, Incremental Merkle은 multi index table 에 병렬적으로 구현된다.
Incremental Merkle 은 state 로 직접 사용되지는 않고, 잠시 데이터 구조로 사용된다.

#### 5) Aanalysis
합의 알고리즘이 투표와 위임에 의존하기 때문에, 부패할 수있는 어떤 동작이라도 BFT 의 존재를 완전히 무효화한다.
토큰 집중화 때문에, EOS token 보유자들은 BP집단이 transaction을 처리하는지 확인한다.
BP 가 아닌 노드들에 의해 전혀 방해받지 않는다.
그래서 BP 가 선정되고 나면, 전통적인 DBMS 처럼 transaction 처리량을 최대화한다.

만약 BP 가 악의적인 행동을 하면 EOS protocol 의 의도대로 BP에서 퇴출 당한다.
하지만 이런 투표시스템의 경제성 때문에 BP에서 퇴출하는 것이 막힐 가능성도 있다.
그래서 EOS 의 시스템이 과연 bad actor 르 다루는데 적절한가 의심의 여지가 있다.
개념적으로는 EOS 에서 BFT 를 구현하기는 불가능하다.
진정한 BFT 시스템은 특정 카르텔 민감할 수 없다.
하지만 EOS 에서는 쉽게 카르텔이 형성될 수 있다. (뒤에서 다룬다.)
그러므로 EOS에서 BFT를 위한 어떤 노력도 무효화 될 수 밖에 없다.


또한 PoS로서 transaction 에도 문제가 있다.
과거에 발생했지만 false 인 transaction 이 넘쳐나는 문제가 있다.
EOS의 DB structure 는 수명이 짧다. 동시에 변경하는 경우 분명치 않게 rollback 될 수 있다.
Incremental Merkle 은 sequential 하게 수행되지 않는 각각의 transaction 들의 timing attack 에 민감하다.
PoW는 finality의 가능성을 제공하는데 번면에, EOS 는 어떤 finality 도 제공하지 않는다. 
전체적인 데이터베이스의 구조가 Bitshares 에 기초하기 때문에 특정 키 하나로 state 를 revert 시키던지, genesis block 상태로 되돌릴 수 있다.

결국, EOS 는 결정적인, 알고리즘적인 합의 강제가 부족하다.
BP들은 암호학적 증명없이 다른 BP 가 전달한 transaction을 받아들인다.
데드락과 레이스 컨디션과 같은 비 결정적인 문제를 다루는 많은 문제가 있다.
BP들은 이미 block에 담은 transaction을 보지 못한다.
그래서 트랜잭션에 fault가 있다면 그 결함이 소급 적용된다.
이것은 결국 합의 증명에도 부채를 만든다.
BFT자체가 적용이 안되는 시스템이다.



 