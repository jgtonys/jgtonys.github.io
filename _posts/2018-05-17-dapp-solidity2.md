---
layout: post
title:  "DApp Challenge : Solidity.part2"
category: DApp
tags: [solidity]
---

지난 포스트에서 크립토좀비 튜토리얼을 이용하여 챕터2까지 졸졸졸 따라다니며 크립토키티를 먹는 나만의 크립토좀비를 생성해 냈다. 이번 포스트에서는 한층 더 깊어진 이더리움 DApp의 속성과 Solidity의 특성들에 대해 공부할 것이다. 크립토좀비 튜토리얼 챕터3를 지금부터 시작한다.

<!-- more -->

# CryptoZombie Practice CH3~

![cryptozombie-main]({{ site.baseurl }}/public/img/cryptozombie-main.jpeg)

## **컨트랙트의 불변성**

이더리움 DApp은 일반적인 어플리케이션과 다른 특성을 가지고 있다. 일반적인 어플리케이션은 배포를 한 후 언제든지 수정을 통해 버그를 잡고, 업데이트를 진행할 수 있다. 그러나 DApp은 한 번 이더리움에 컨트랙트를 배포하고 나면 절대 수정하거나 업데이트 할 수 없다. 이것을 컨트랙트의 불변성(Immutable)이라고 한다.

배포한 최초의 코드는 이더리움 블록체인에 영구적으로 존재하기 때문에 결점이 있는 코드를 올리게 되면 그냥 못쓰는 DApp이 되어버린다. 그래서 솔리디티에는 보안이 가장 큰 이슈가 될 수 밖에 없다. 반대로 완벽한 스마트 컨트랙트를 검증하고 배포를 했다면, 그대로 영구적으로 실행될 것이라는 것을 의심의 여지 없이 확신할 수 있다. 제3자가 수정을 하거나 예상치 못한 결과를 자아낼 수 없다는 것을 뜻한다.

> Contract is Immutable

이러한 특성 때문에 **외부 의존성** 에 대한 문제를 해결할 방법을 찾아야 한다. 이 말은 무슨 말인가 하면, 외부 컨트랙트에 내 DApp이 의존하고 있다면 외부 컨트랙트에 문제가 생겼을 때를 대비하여 코드를 구성해야한다는 것이다. 예를 들어 저번 포스트에서 공부했던 크립토좀비가 크립토키티를 먹는 코드에서 크립토키티 컨트랙트가 오류가 발생한다면 내 크립토좀비 DApp은 망가지게 된다. 따라서 크립토키티가 문제가 생겼을 때 해당 주소를 바꿀 수 있도록 유동성있게 참조하도록 코드를 짜야 한다.

유동성 있게 바꾼 참조. `setKittyContractAddress`함수를 사용하여 언제든 컨트랙트의 주소를 바꾸어 참조할 수 있다.

```python
KittyInterface kittyContract;

function setKittyContractAddress(address _address) external {
  kittyContract = KittyInterface(_address);
}
```

그러나 여기에는 심각한 문제가 있는데.. 함수를 외부에서 내가 제어하려고 만들다 보니 external로 선언하게 되고, 그럼 다른 사람도 내 코드를 수정할 수 있다!

여기에서 솔리디티 **OpenZeppelin** 라이브러리 `Ownable` 컨트랙트를 사용해야 한다. 이 녀석은 안전하고 검증받은 스마트 컨트랙트의 라이브러리로, 이 함수를 사용하는 사람이 소유자가 맞는지를 확인해주고 사용에 제한을 걸어주는 역할을 한다.

*역시 솔리디티도 알아야 할 추가 패키지같은게 있다. 갈길이 멀다..*

```python
contract Ownable {
  address public owner;
  event OwnershipTransferred(address indexed previousOwner, address indexed newOwner);
  /**
   * @dev The Ownable constructor sets the original `owner` of the contract to the sender
   * account.
   */
  function Ownable() public {
    owner = msg.sender;
  }
  /**
   * @dev Throws if called by any account other than the owner.
   */
  modifier onlyOwner() {
    require(msg.sender == owner);
    _;
  }
  /**
   * @dev Allows the current owner to transfer control of the contract to a newOwner.
   * @param newOwner The address to transfer ownership to.
   */
  function transferOwnership(address newOwner) public onlyOwner {
    require(newOwner != address(0));
    OwnershipTransferred(owner, newOwner);
    owner = newOwner;
  }
}
```
Ownable 컨트랙트의 모습이다. 내 컨트랙트의 참조 함수를 이를 이용해 적용해 보겠다.
방법은 간단하다. 메인 컨트랙트에 Ownable 컨트랙트를 상속시키면 된다. 그럼 Ownable 컨트랙트의 onlyOwner 함수 제어자를 내 ZombieFeeding 컨트랙트에서도 사용할 수 있다.

현재 구조는 이러하다.

```python
ZombieFeeding is ZombieFactory
ZombieFactory is Ownable
```

아 맞다 그리고 위에 Ownable 컨트랙트의 onlyOwner를 보면 일반 함수가 아니라 modifier 키워드로 되어있는 것을 볼 수 있다. 이건 함수 제어자로서 직접 호출할 수 없고 함수 정의부 끝에 붙여서 사용한다.

```python
KittyInterface kittyContract;

function setKittyContractAddress(address _address) external onlyOwner {
  kittyContract = KittyInterface(_address);
}
```
onlyOwner를 사용하여 아무나 수정할 수 없는 유동적인 참조(인터페이스)를 만들어 냈다!


## **GAS**

가스가 무엇인지 저저저번 포스팅 `DApp : Concept`에서 알아보았다. DApp의 기능을 실행할 때마다 지불하는 가상화폐이고 가스는 ETH(이더리움 화폐)를 통해 구입이 가능하다. 가스의 비용은 연산수행에 필요한 컴퓨팅 자원들의 합산으로 계산된다. 사용자들은 내 DApp을 사용하는데 실제 돈이 들어가므로 DApp의 코드 효율은 다른 어플리케이션보다도 훨씬 더 중요하다.

실제 돈 == GAS 가 되기 때문에 DApp에서는 **구조체 압축** 이 매우 중요하다. 불필요한 변수의 타입을 압축하고, 데이터 타입을 묶고, 저장공간을 최소화해야 한다.

## **시간 단위**

솔리디티는 시간단위를 제공한다. `now` 변수는 현재의 유닉스 타임스탬프를 제공해주고 `seconds`, `minutes`, `hours`, `days`, `weeks`, `years` 같은 시간 단위 또한 제공해준다.

## **함수 제어자**

앞서 사용했던 onlyOwner 함수 제어자같은 녀석들을 직접 만들어서 제어할 수도 있다. 또한 함수 제어자에 인자를 전달해서 그 인자가 요구조건을 만족하는지를 require를 사용하여 검증하고 값을 던져줄 수 있다.

## **view 함수**

앞선 포스트에서 데이터를 읽기만 하는 함수는 view함수로 정의한다고 했다. view함수는 데이터를 수정하지 않기 때문에 사용자에 의해 외부에서 호출되었을 때 가스를 전혀 소모하지 않는다. 이는 블록체인 상에서 실제로 어떤 수정도 하지 않기 때문이다. 따라서 View함수를 사용해서 가스를 절약하는 코드를 만드는 것은 매우 중요하다.

*단 view 함수가 동일 컨트랙트 내의 다른 함수에서 내부적으로 호출되면 가스가 소모된다. 무조건 외부에서 호출되었을 때에만 무료이다.*

또한 앞서 이야기 했듯 Storage 데이터는 비용이 많이 드는, 연산이 많은 녀석이다. 그래서 view 함수의 특성을 이용해 memory에 배열을 선언하는 방법으로 비용을 절약할 수 있다. 먼저 접근을 `external view`로 하여 storage에서 배열을 직접 업데이트 하지 않고 memory에 배열을 선언한 다음 리턴한다.

---
어쩌다 보니 챕터3까지 완료!

![level3]({{ site.baseurl }}/public/img/level3.PNG)

바로 챕터4로 갑니다.


## **Payable**

크립토 좀비 튜토리얼에서는 payable 함수가 솔리디티와 이더리움을 아주 멋지게 만든다고 주장한다. 이 녀석은 직접 컨트랙트에 이더리움을 보내고 받을 수 있는 특별한 함수이다. 컨트랙트 코드 자체가 모두 이더리움 위에서 존재하기 때문에, 함수 실행과 동시에 컨트랙트에 돈을 지불하는 것이 가능한 것이다.

`payable`로 컨트랙트에 이더를 보낸 후에는 컨트랙트 이더리움 계좌에 이더가 저장된 상태가 된다. 컨트랙트에 있는 잔액을 특정 이더리움 주소로 다시 보내는 방법은 `transfer`함수를 써서 구현 할 수 있다.

```python
function withdraw() external onlyOwner {
  owner.transfer(this.balance);
}
```
다음과 같이 구현하면 사용자는 `this.balance`라는 컨트랙트의 잔액을 꺼낼 수 있다.

---
챕터4 뒷부분은 새로운 개념보다는 그냥 따라 만드는게 주를 이루어서 쓸 내용이 별로 없었다. 어쩌다 보니 이렇게 챕터4도 완료했다!

![level4]({{ site.baseurl }}/public/img/level4.PNG)
