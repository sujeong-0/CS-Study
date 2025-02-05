# **Factory Pattern**
![img](./img/dp_factory_pattern_1.png)

정확하게는 팩토리 메서드 패턴과 추상 팩토리 패턴이 있다.

# **Factory Method Pattern**

## 팩토리 메소드 패턴이란?
**팩토리 메소드 패턴**은 생성 패턴 중 하나다. 객체를 만들어 반환하는 함수를 제공하여 초기화 과정을 외부에서 보지 못하게 숨기고 반환 타입을 제어하는 방법이다. 인터페이스나 추상 클래스로 구현할 수 있다. **다양한 객체들을 생성할 때 사용한다.**

## 예제
스타크래프트에서 여러 종류의 유닛을 정의한 후, 건물에서 유닛을 생성하는 기능을 만들어본다고 생각해보자. 대기열에 등록된 객체들은 모두 유닛(Unit) 이지만, 실제 생성되는 유닛의 실체는 다양하다.

```C++
enum UnitType
{
    Marine,
    Firebat,
    //...
}

class Unit
{
    //...
}

class Marine : public Unit
{
    //...
}

class Firebat : public Unit
{
    //...
}

class Barracks
{
public:
    Unit createUnit(UnitType unitType);
}

Unit Barracks::createUnit(UnitType unitType)
{
    Unit* unit = nullptr;
    switch(unitType)
    {
        case UnitType::Marine:
            unit = new Marine();
        break;
        case UnitType::Firebat:
            unit = new Firebat();
        break;
    }
    return unit;
}

int main()
{
    Barracks barracks;
    Unit myUnit1 = barracks.createUnit(UnitType::Marine);
    Unit myUnit2 = barracks.createUnit(UnitType::Firebat);
    return 0;
}
```

# **Abstract Factory Pattern**

## 추상 팩토리 패턴이란?
**추상 팩토리 패턴**은 생성 패턴 중 하나이다. 서로 연관있는 객체들의 조합이 필요할 때 사용한다. 팩토리 메소드 패턴과 마찬가지로 추상화와 캡슐화를 통해 팩토리 클래스를 만든 다음에, 만들어진 팩토리 클래스를 이용하는 상위 팩토리를 만들어 객체를 생성한다.

## 예제


```C++
enum UnitType
{
    Marine,
    Firebat,
    //...
}

class Unit
{
    //...
}

/*
다양한 유닛 클래스 생략
etc

class Marine : public Unit
{
    //...
}

class Firebat : public Unit
{
    //...
}
*/

class Building
{
public:
    Unit createUnit(UnitType unitType);
}

class Barracks : public Building
{
    //...
}

class Starport : public Building
{
    //...
}

class Gate : public Building
{
    //...
}

class Stargate : public Building
{
    //...
}

class Brood
{
private:
    Building tier1Building;
    Building tier2Building;
public:
    Building getTier1Building();
    Building getTier2Building();
}

class Terran : public Brood
{
    Terran()
    {
        tier1Building = new Barracks();
        tier2Building = new Starport();
    }

    ~Terran()
    {
        delete tier1Building;
        delete tier2Building;
    }
}

class Protoss : public Brood
{

    Protoss()
    {
        tier1Building = new Gate();
        tier2Building = new Stargate();
    }

    ~Protoss()
    {
        delete tier1Building;
        delete tier2Building;
    }
}

int main()
{
    Brood terran = new Terran();
    Brood protoss = new Protoss();
    terran.getTier1Building().createUnit(...);
    terran.getTier2Building().createUnit(...);
    protoss.getTier1Building().createUnit(...);
    protoss.getTier2Building().createUnit(...);

    delete terran;
    delete protoss;
    return 0;
}
```

## 추상 팩토리 vs 팩토리 메소드
둘다 팩토리 객체를 통해 구체적인 타입을 감추고 객체 생성에 관여하는 패턴 임에는 동일하다. 또한 공장 클래스가 제품 클래스를 각각 나뉘어 느슨한 결합 구조를 구성하는 모습 역시 둘이 유사하다.

그러나 주의할 것은 추상 팩토리 패턴이 팩토리 메서드 패턴의 상위 호환이 아니라는 점이다. 두 패턴의 차이는 명확하기 때문에 상황에 따라 적절한 선택을 해야 한다.예를들어 팩토리 메서드 패턴은 객체 생성 이후 해야 할 일의 공통점을 정의하는데 초점을 맞추는 반면, 추상 팩토리 패턴은 생성해야 할 객체 집합 군의 공통점에 초점을 맞춘다.

단, 이 둘을 유사점과 차이점을 조합해서 복합 패턴을 구성하는 것도 가능하다.

<table>
    <tr>
        <th></th>
        <th>팩토리 메서드</th>
        <th>추상 팩토리</th>
    </tr>
    <tr>
        <td>공통점</td>
        <td colspan="2">객체 생성 과정을 추상화한 인터페이스를 제공<br/>객체 생성을 캡슐화함으로써 구체적인 타입을 감추고 느슨한 결합 구조를 표방</td>
    </tr>
    <tr>
        <td>차이점</td>
        <td>구체적인 객체 생성과정을 하위 또는 구체적인<br/>클래스로 옮기는 것이 목적</td>
        <td>
            관련 있는 여러 객체를 구체적인 클래스에<br/>의존하지 않고 만들 수 있게 해주는 것이 목적
            <br/><br/>
            한 Factory에서 서로 연관된 여러 종류의<br/>객체 생성을 지원
            <br/><br/>
            클래스 레벨에서 포커스를 맞춤으로써<br/>클라이언트의 ConcreteProduct 인스턴스 군의<br/>생성 및 구성에 대한 의존을 감소
        </td>
    </tr>
</table>

### 출처
- [추상 팩토리 패턴 vs 팩토리 메소드 패턴](https://inpa.tistory.com/entry/GOF-💠-추상-팩토리Abstract-Factory-패턴-제대로-배워보자#abstract_factory_vs_factory_method)