### switch when
**정의**: `switch`의 `case`에 추가 조건을 붙이는 문법. 선행 조건이 충족되어야 다음 조건 확인

```csharp
switch (hp)
{
    case int n when n <= 0:
        Console.WriteLine("사망");
        break;
    case int n when n < 30:
        Console.WriteLine("위험");
        break;
    default:
        Console.WriteLine("정상");
        break;
}
```

### SOLID 원칙이란?
**정의**: 유지보수와 확장이 쉬운 객체지향 설계를 위한 5가지 원칙
**구성**: SRP, OCP, LSP, ISP, DIP의 앞 글자

### 단일책임원칙 (SRP, Single Responsibility)
**정의**: 클래스는 하나의 책임만 가져야 함. (다 분리해야한다)

**예시**: `Player`가 이동, 저장, UI 표시를 모두 하면 위반 → `PlayerMovement`, `SaveManager`, `PlayerUI`로 분리

### 개방폐쇄원칙 (OCP, Open-Closed)
**정의**: 확장에는 열려 있고 수정에는 닫혀 있어야 함
 **의미**: 새 기능을 추가할 때 기존 코드를 고치지 않고 새 클래스를 추가하는 방식으로 확장
**예시**: 무기 종류마다 `if/switch`를 추가하는 대신 `IWeapon` 인터페이스를 구현하는 새 클래스를 만듦

### 리스코프 치환원칙 (LSP, Liskov Substitution)
**정의**: 자식 클래스는 부모 클래스 자리에 넣어도 문제없이 동작해야 함

**위반 예시**: `Bird`에 `Fly()`가 있는데 `Penguin : Bird`가 `Fly()`에서 예외

### 인터페이스 분리원칙 (ISP, Interface Segregation)
**정의**: 클라이언트가 사용하지 않는 메서드에 의존하지 않도록 인터페이스를 작게 나눠야 함

 **예시**: 거대한 `IMonster { Attack(); Fly(); Swim(); }` 대신 `IAttackable`, `IFlyable`, `ISwimmable`로 분리

### 의존성 역전원칙 (DIP, Dependency Inversion)
**정의**: 상위 모듈은 하위 모듈의 구체 클래스가 아니라 추상화(인터페이스)에 의존해야 함

**예시**: `Player`가 `Sword`를 직접 참조하지 않고 `IWeapon`에 의존 → 무기 교체가 쉬워짐

```csharp
class Player
{
    private IWeapon weapon;                         
    public Player(IWeapon weapon) { this.weapon = weapon; }
}
```

### 의존성 주입 (DI, Dependency Injection)
**정의**: 객체가 필요한 의존성을 **직접 만들지 않고 외부에서 전달받는** 방식. 

**방식**: 생성자 주입, 메서드 주입, 프로퍼티 주입

**장점**: 결합도 감소, 테스트할 때 가짜 객체로 교체 쉬움

```csharp
var player = new Player(new Sword());  
var player2 = new Player(new Bow());    
```


### 싱글톤 패턴

**정의**: 클래스의 인스턴스가 **하나만** 존재하도록 보장하고, 어디서든 접근할 수 있게 하는 패턴

**활용**: `GameManager`, `AudioManager`, 설정 관리자 등

```csharp
public class GameManager
{
    private static GameManager instance;
    public static GameManager Instance => instance ??= new GameManager();

    private GameManager() { }   // 외부 생성 차단
}
```

### 싱글톤 패턴의 문제점
**전역 상태**: 어디서든 값을 바꿀 수 있어서 버그 추적이 어려움

**숨은 의존성**: 코드 안에서 `Instance`를 직접 호출하므로 어떤 클래스에 의존하는지 겉으로 드러나지 않음

**강한 결합**: 구체 클래스에 직접 묶여서 DIP 위반, 교체가 어려움

**테스트 어려움**: 가짜 객체로 바꾸기 힘들고, 테스트끼리 상태가 공유됨

**스레드 안전성**: 동시에 생성되면 인스턴스가 2개 생길 수 있음

**SRP 위반**: 원래 기능 + 인스턴스 개수 관리라는 책임 2개를 가짐

**대안**: DI 컨테이너로 단일 인스턴스를 관리하고 생성자로 주입

### 컴포넌트 패턴
**정의**: 하나의 객체에 모든 기능을 상속으로 몰아넣지 않고, 기능을 **독립된 컴포넌트로 나눠 조합**하는 패턴

**핵심**: 상속보다 **조합(Composition)**

**예시**: Unity의 `GameObject`에 `Rigidbody`, `Collider`, `PlayerMovement` 등을 붙여 기능을 구성

**장점**: 기능 재사용이 쉽고, 깊은 상속 구조를 피할 수 있음

```csharp
class GameObject
{
    List<IComponent> components = new();
    public void AddComponent(IComponent c) => components.Add(c);
    public void Update() { foreach (var c in components) c.Update(); }
}
```

### 상태 패턴
**정의**: 객체의 상태를 **별도의 클래스로 분리**하고, 상태에 따라 행동이 바뀌게 하는 패턴

**해결하는 문제**: 상태가 늘어날수록 커지는 `if/switch` 분기

**구성**: 상태 인터페이스(`IState`), 상태별 구현 클래스, 현재 상태를 가진 컨텍스트

```csharp
interface IState
{
    void Enter();
    void Update();
    void Exit();
}

class IdleState : IState 
class RunState : IState 

class Player
{
    IState current;
    public void ChangeState(IState next)
    {
        current?.Exit();
        current = next;
        current.Enter();
    }
}
```

**활용**: 캐릭터 FSM(대기 → 이동 → 공격 → 피격), 몬스터 AI, UI 화면 전환 //11월 8일

