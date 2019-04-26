---
layout: post
title:  "DApp Challenge : Solidity.part1"
category: DApp
tags: [solidity]
---

이전 포스트에서는 DApp을 시작하기 위한 기초적인 배경지식에 대해 공부했다. 이더리움 네트워크는 어떻게 작동하는 것인가를 파악했으니 이제는 실전이다. Solidity를 사용해서 나만의 컨트랙트를 만드는 것부터 시작해 보겠다.

<!-- more -->

# CryptoZombie Practice CH1~Ch2

![cryptozombie-main]({{ site.baseurl }}/public/img/cryptozombie-main.jpeg)


## **기초적인 문법**

Solidity는 객체 지향형 언어로서 스마트 컨트랙트를 구현하게 해주는 언어이다. 클래스와 비슷하게 변수,함수,구조체 등을 포함하고 상속과 다형성을 지원한다.

솔리디티 파일의 확장자는 `.sol` 이고 파일의 맨 처음에 solidity 컴파일러 버전을 지정한다.

```
pragma solidity ^0.4.19;
```

하나의 컨트랙트가 있고 그 안에 상태 변수, 이벤트, 함수 등 넣는 구조이다.

아래는 크립토좀비 튜토리얼 코드이다.
```python
#컨트랙트 ZombieFactory
contract ZombieFactory {
    #정수 변수 선언
    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    #구조체 선언
    struct Zombie {
        string name;
        uint dna;
    }

    Zombie[] public zombies;
    #함수 선언
    function _createZombie(string _name, uint _dna) private {
        zombies.push(Zombie(_name, _dna));
    }
}
```

솔리디티 함수는 view 와 pure라는 제어자가 있는데 해당 함수가 데이터를 보기만 하고 변경하지 않는다면 view 함수, 데이터에 아예 접근하지 않는다면 pure 함수로 정의할 수 있게 해 놓았다.

```python
function _generateRandomDna(string _str) private view returns (uint) {

}
```

## **간단한 형변환 연습**

이더리움은 SHA3의 버전인 keccak256을 내장 해시 함수로 가지고 있다. 이는 입력된 스트링을 랜덤
256비트 16진수로 매핑한다. 따라서 입력 스트링이 달라지면 해시값이 달라지게 된다.

크립토 좀비 튜토리얼에서는 이 해시 함수를 **의사난수발생기** 로서 사용한다. 참고로 **의사난수발생기** 란 소프트웨어에서 연관성이 없는 듯한 수열을 만들어내는 난수 생성 방법이다. 이는 진정한 난수가 아니다.

아무튼 형 변환 연습을 해보자.

```python
uint rand = uint(keccak256(_str));
```

위의 코드는 `_str` 문자열을 이더리움 내장함수인 keccak256으로 변환한 후 uint로 매핑한다.


## **이벤트**

이벤트는 컨트랙트가 액션이 발생했을때 의사소통하는 방법이다. 이벤트 선언의 방법은 다음과 같다.

```python
event IntegersAdded(uint x, uint y, uint result);
```

---
여기까지는 왠지 그냥 조금 다른 언어의 문법을 배우는 기분이다. 실제로 이게 어떻게 이더리움 네트워크와 통신하는지 등은 아직 잘 모르겠지만 일단 튜토리얼 레슨1 완료하니까 내 크립토 좀비가 나왔다.

![cryptozombie]({{ site.baseurl }}/public/img/cryptozombie.PNG)

못생겼어..<br>
링크도 준다ㅎㅎ
https://share.cryptozombies.io/ko/lesson/1/share/Jungyu_Kim

---
이제 챕터2로 넘어갔다. 이거 생각보다 재밌다.

## **매핑과 주소**

이더리움 블록체인은 자신의 지갑 계좌번호인 주소를 가지고 있다. 즉 여기서 주소는 특정 유저나 스마트컨트랙트가 가지고 있는 고유한 값이라는 것을 알 수 있다.

매핑은 구조화된 데이터를 저장하는 방법 중 하나인데, (key-value) 형식으로 저장한다.

```python
mapping (address => uint) public accountBalance;
```

## **msg.sender**

msg.sender는 모든 함수에서 이용 가능한 전역변수이다. 이녀석은 현재 함수를 호출한 주소를 가리키고 있어서 누가 함수에 접근하였는지 저장할 수 있고, 또한 외부에서 임의로 추적할 수 없도록 하는 보안성을 가지고 있다.

```python
mapping (uint => address) public zombieToOwner;
mapping (address => uint) ownerZombieCount;

function _createZombie(string _name, uint _dna) private {
    uint id = zombies.push(Zombie(_name, _dna)) - 1;
    zombieToOwner[id] = msg.sender;
    ownerZombieCount[msg.sender]++;
    NewZombie(id, _name, _dna);
}
```
위의 코드를 보면 `_createZombie`함수가 실행되었을 때 매핑된 `zombieToOwner`는 `id(uint)`에 대한 `msg.sender(address)`를 저장하고 또 `ownerZombieCount`는 `msg.sender(address)`에 대한 `uint`형 을 생성하여(초기에는 0) 변형한다.

내가 봐도 좀 외계어처럼 설명하기는 했는데, 간단히 말하자면 좀비를 생성한 사용자를 사용자의 id와 묶고, 사용자가 만든 좀비개수를 업데이트하는 것이다.

*mapping 이거 겁나게 멋있다.*

## **require**

함수에게 제약조건을 거는 녀석이다. 만약 ~라면 함수를 실행해라 라는 if같은 기능을 제공해 준다.

```python
function createRandomZombie(string _name) public {
    require(ownerZombieCount[msg.sender] == 0);
    uint randDna = _generateRandomDna(_name);
    _createZombie(_name, randDna);
}
```
`require(ownerZombieCount[msg.sender] == 0);` 부분은 한 사용자당 이 함수를 한번만 실행하여 좀비를 생성할 수 있도록 제약을 걸어준다.

## **상속**

뭐 이건 익숙한 내용이라 자세한 설명은 패스한다.

```python
import "./zombiefactory.sol";

contract ZombieFeeding is ZombieFactory {

}
```
코드를 분리해서 ZombieFactory를 상속한 ZombieFeeding을 만들었다.

## **Storage, Memory**

Storage는 블록체인 상에 영구적으로 저장되는 하드디스크의 느낌이고 Memory는 임시적으로 저장되는 RAM의 느낌으로 이해하면 된다. 실용적으로 설명해 보면 상태변수가 storage고 함수 안에서 선언된 임시 변수들이 memory이다.

## **Internal, External**

함수 접근 제어자에는 public, private 말고도 internal 과 external이 있다. internal은 컨트랙트를 상속하는 컨트랙트에서 접근이 가능하도록 하고 external은 컨트랙트 바깥에서만 호출될 수 있고 안에서는 호출될 수 없도록 한다.

## **Interface**

외부의 컨트랙트에서 필요한 데이터를 가져오는 상호작용을 할 수 있게 해주는 기능이다. 간단한 함수명만 정의를 해주면 된다. 아래는 크립토 키티 컨트랙트에서 크립토키티를 가져오는 인터페이스이다.

```python
#getKitty의 함수 모습

function getKitty(uint256 _id) external view returns (
    bool isGestating,
    bool isReady,
    uint256 cooldownIndex,
    uint256 nextActionAt,
    uint256 siringWithId,
    uint256 birthTime,
    uint256 matronId,
    uint256 sireId,
    uint256 generation,
    uint256 genes
) {
    Kitty storage kit = kitties[_id];
    isGestating = (kit.siringWithId != 0);
    isReady = (kit.cooldownEndBlock <= block.number);
    cooldownIndex = uint256(kit.cooldownIndex);
    nextActionAt = uint256(kit.cooldownEndBlock);
    siringWithId = uint256(kit.siringWithId);
    birthTime = uint256(kit.birthTime);
    matronId = uint256(kit.matronId);
    sireId = uint256(kit.sireId);
    generation = uint256(kit.generation);
    genes = kit.genes;
}

#우리의 크립토 좀비에서 interface 정의
contract KittyInterface {
    function getKitty(uint256 _id) external view returns (
        bool isGestating,
        bool isReady,
        uint256 cooldownIndex,
        uint256 nextActionAt,
        uint256 siringWithId,
        uint256 birthTime,
        uint256 matronId,
        uint256 sireId,
        uint256 generation,
        uint256 genes
    );
}
```

getKitty 함수 첫줄(반환자까지) 적고 세미콜론 적어주면 정의된다. 이렇게 설정한 Interface는 다음과 같이 활용한다.

```python
address ckAddress = 0x06012c8cf97BEaD5deAe237070F9587f8E7A266d;
KittyInterface kittyContract = KittyInterface(ckAddress);
```

---
여차여차해서 챕터2까지 완료했다. 드디어 크립토키티를 먹은 크립토좀비를 생성해냈다.

![atekitty]({{ site.baseurl }}/public/img/atekitty.PNG)

아오 고양이 먹더니 더못생겼네.

챕터2가 끝나고 챕터3로 들어가니 부자연스럽게 번역된 한글 문구가 경고를 했다. 재밌는건 여기까지였고 이제 솔리디티에 대한 기술적인 부분을 깊게 다룰꺼야.

집중력이 조금 떨어진 것 같은데, 마음을 다잡고 다음 포스팅에서 챕터3로 들어가자.
