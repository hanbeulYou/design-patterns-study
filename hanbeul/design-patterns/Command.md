# ✨ Command 패턴

## 1. 패턴 정의: 정의와 핵심 요약

- Command 패턴은 요청(`request`)을 객체(`object`)로 캡슐화함으로써, 요청을 호출하는 쪽과 실행하는 쪽의 결합도를 낮추는 디자인 패턴이다.
- 요청을 나타내는 커맨드 객체가 호출자(`invoker`)와 수신자(`receiver`)를 분리하고, 나중에 실행하거나 취소하거나 큐에 넣거나 기록할 수 있는 유연성을 제공한다.

## 2. 사용 목적: 이 패턴이 필요한 이유

- 메서드를 직접 호출하는 구조에서는 변경(메서드 이름 변경, 내부 로직 변경 등)이 클라이언트 코드에 영향을 줄 수 있는데, Command 패턴을 쓰면 호출자 쪽 코드는 요청을 나타내는 객체만 다루면 되어서 유지보수가 쉬워진다.
- 여러 명령(`command`)을 큐에 넣는다거나, 지연 실행(`delayed execution`), 혹은 실행 취소(`undo`) 기능이 필요할 때 유용하다.

## 3. 패턴 설명: 동작 방식과 구성 요소

![Command](./images/command-structure.png)

### 구성 요소

> Invoker (호출자 역할)

- 명령 객체(`command`)를 받아서 `execute()` 같은 메서드를 호출하여 실제 명령을 실행하는 쪽.
- 호출자 입장에서는 구체적인 명령의 내부 동작을 모르고, 단지 명령 객체를 실행하는 역할만 함.

> Command 객체(인터페이스/추상)

- 하나의 통일된 실행 메서드(`execute` 등)를 정의. 구체 명령(`concrete command`)들은 이 인터페이스를 구현하여 실제 로직과 수신자(`receiver`)를 알고 있음.

> Receiver (수신자 역할)

- 실제 작업을 수행하는 객체. 명령 객체가 수신자에게 위임(`delegate`)해서 작업을 실행함.

> Client (요청 생성자)

- 어떤 명령을 사용할지 결정하고, 명령 객체를 생성해서 호출자(`invoker`)에 넘기는 쪽

### 동작 방식

1. 클라이언트가 수행될 작업(`task`)을 가지고 어떤 커맨드 객체(`concrete command`)를 생성한다.

2. 명령 객체에는 수신자(`receiver`)와 실행할 행동(`execute`) 또는 필요한 인자(`arguments`)가 포함된다.

3. 클라이언트는 인자로 커맨드 객체를 호출자(`invoker`)에 전달하거나 저장한다.

4. 호출자(`invoker`)는 `execute()` 메서드를 호출하여 명령을 실행하며, 실제 작업은 수신자에게 위임된다.

5. 필요하면 명령들을 큐에 쌓아 순차 실행하거나, 실행 취소(`undo`) 기능, 로그 기록 기능 등을 추가할 수 있다.

## 4. 코드 및 활용 예시: 기본 구현과 프론트엔드 적용

```ts
// Receiver: 실제 동작을 아는 객체
class TV {
  turnOn() {
    console.log("TV 켜짐 🔌");
  }
  turnOff() {
    console.log("TV 꺼짐 ❌");
  }
}

// Command 인터페이스 흉내 (execute 메서드만 있으면 됨)
class TurnOnCommand {
  constructor(tv) {
    this.tv = tv; // Receiver 주입
  }
  execute() {
    this.tv.turnOn();
  }
}

class TurnOffCommand {
  constructor(tv) {
    this.tv = tv;
  }
  execute() {
    this.tv.turnOff();
  }
}

// Invoker: 명령을 실행하는 역할
class RemoteControl {
  submit(command) {
    command.execute();
  }
}

// Client: 어떤 명령을 쓸지 결정
const tv = new TV();
const remote = new RemoteControl();

const turnOn = new TurnOnCommand(tv);
const turnOff = new TurnOffCommand(tv);

remote.submit(turnOn); // "TV 켜짐 🔌"
remote.submit(turnOff); // "TV 꺼짐 ❌"
```

## 5. 정리와 확장: 학습 포인트와 추가 학습거리

### 학습 포인트

- Command 패턴은 호출자(invoker)와 실제 작업을 수행하는 객체(receiver)를 분리하여 책임을 명확히 한다.
- 명령 자체를 객체로 다루기 때문에, 실행 지연, 로그 기록, 명령 취소(undo), 혹은 복잡한 커맨드 조합(combine commands) 등의 기능을 쉽게 추가할 수 있다.
- 반면, 단순한 작업에서는 오히려 코드가 복잡해질 수 있으므로 적용 여부는 상황에 따라 판단해야 한다.
