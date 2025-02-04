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