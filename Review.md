## Simulation
Использование сокращений в именах переменных, методов и в целом везде плохо. Непонятно читающему твой код что значит переменная. 
```java
private final GameManager gm;
```

Зачем добавлять приставку I, пиши просто Action
```java
private final List<IAction> initActions;
```
В конструкторе лучше извне брать renderer, чтобы можно было подставлять разные реализации рендерера. Тоже самое про GameManager
```java
// Плохо
public Simulation() {
    renderer = new Renderer();
}
// Хорошо
public Simulation(IRenderable renderer) {
    this.renderer = renderer;
}
```
Имена методов называй глаголами
```java
// Плохо
init.action(gm)
// Хорошо
init.act(gm)
```
Зачем кастить?
```java
.map(Init.class::cast)
```

## GameManager
у тебя много методов, которые стоит переложить на Action: создание, удаление сущностей и тогда можно было бы переименовать класс в GameMap или Field.
У тебя есть константы DX и DY. Такие названия отражают скорость по x и y, а на деле у тебя они ширина и высота карты. Их лучше переименовать в width и height. Также лучше их сделать полями, чтобы можно было задавать разные значения или несколько экземпляров разных карт.
```java
// Плохо
public static final int DX = 50;
public static final int DY = 20;

// Хорошо
private final int width;
private final int height;
public GameManager(int width, int height) {
    this.width = width;
    this.height = height;
}
```
Фишечка языка, примитивы в полях инициализирутся по дефолту значениями. Так что это лишнее:
```java
public GameManager(int width, int height) {
    turnCount = 0;
}
```
Это обобщение `Creature<?>` точно нужно? Мне кажется можно и без него.
```java
Creature<?> creature = em.getCreature(src);
```
Указания ключевых слов в именовании плохая практика. Можно попробовать назвать `putEntitiesToAction`
```java
em.collectEntitiesToActionByInterfaceClass()
```
Именование And. Либо разделяй, либо находи суть метода. В данном случае у тебя получение количества возрождаемых существ. То есть лучше назвать `getRespawnableEntityCount`
```java
Set<Entry<Class<? extends Entity>, Integer>> set = em.getRespawnableEntitiesAndCounts();
```
Указания ключевых слов в именовании плохая практика. Можно попробовать назвать `putEntitiesToAction`
```java
em.collectEntitiesToActionByInterfaceClass()
```
Указания ключевых слов в именовании плохая практика. Можно попробовать назвать `putEntitiesToAction`
```java
em.collectEntitiesToActionByInterfaceClass()
```
