## Main
Использование сокращений в именах переменных, методов и в целом везде плохо. Непонятно читающему твой код что значит переменная. 
```java
Simulation sim = new Simulation();
```

Непонятное название переменной. слушает команды пользователя в меню? 
```java
CommandListener commandListener = new CommandListener(sim);
```
Поля без модификаторов доступа. 
```java
    GameMap gameMap;
    MoveCounter moveCounter;
    MessagePrinter printer;
    GameMapConsoleRenderer gameMapConsoleRenderer;
    List<Action> initActions;
    List<Action> turnActions;
```
