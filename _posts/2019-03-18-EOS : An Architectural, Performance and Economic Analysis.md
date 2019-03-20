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
![Fig.3 EOS Actions]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.3 EOS Actions.png" | absolute_url}})
![Fig.4 Contracts in the EOS System Architecture]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.4 Contracts in the EOS System Architecture.png" | absolute_url}})
![Fig.5 Action Handlers Can Apply Multiple Contexts]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.5 Action Handlers Can Apply Multiple Contexts.png" | absolute_url}})
![Fig.6 Two Basic EOS Communication Models]({{ "/images/EOS:An Architectural, Performance and Analysis/Fig.6 Two Basic EOS Communication Models.png" | absolute_url}})


 
   




 