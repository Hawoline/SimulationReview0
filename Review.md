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
## EntityManager
Не возвращай null.
```java
public Creature getCreature(Coordinate coordinate) {
    if (entities.get(coordinate) instanceof Creature<?> creature) {
        return creature;
    } else {
        return null;
    }
}
```
## PathFinder
Сразу выходи из функции, если не выполняется условия для работы метода. Так меньше вложенность.
```java
    private void retracePath() {
        if (targetCoordinate != null) {
            path.add(targetCoordinate);
            Coordinate prev = breadthFirstSearch.get(targetCoordinate);

            while (prev != breadthFirstSearch.get(null)) {
                path.add(prev);
                prev = breadthFirstSearch.get(prev);
            }

            Collections.reverse(path);
        }
    }
```
## EntityManager
Это паттерн Фабрика, поэтому лучше переименовать метод на createEntity(). А build юзается в паттерне Builder для создания кастомного или большого объекта. Ну и не возвращай null.
```java
    public Entity buildEntity(Class<? extends Entity> clazz) {
        if (clazz == Predator.class) {
            return createPredator();
        } else if (clazz == Herbivore.class) {
            return createHerbivore();
        } else if (clazz == Grass.class) {
            return createGrass();
        } else if (clazz == Rock.class) {
            return createRock();
        } else if (clazz == Tree.class) {
            return createTree();
        } else {
            return null;
        }
    }
```
Вот тут наоборот билдер. 
```java
    private Predator createPredator() {
        return new Predator()
                .withDamage(50)
                .withHP(MAX_HEALTH)
                .withEdible(Herbivore.class)
                .withSpeed(1);
    }
```
## Creature, Herbivore и Predator.
Чувствуется незнание наследования. Лучше убрать вот это полностью `<T extends Creature<T>>` и вместо T использовать Creature. Ты и так можешь подставлять наследников класса Creature без дженериков. Также в хищнике и травоядном просто наследуешься от Creature.
```java
// Плохо
public abstract class Creature<T extends Creature<T>>
public class Predator extends Creature<Predator>
// Хорошо
public abstract class Creature {}
public class Predator extends Creature {}
```

Также заметил много разных мелких Action. Не дроби так сильно классы. Много классов также плохо, как и большой класс.
## Итог
Поизучай ООП, наследование. Посмотри стримы Сергея Жукова по написанию шахмат. Самое прикольное там разделение ответственности по тому куда можно ходить всех фигур от определенной.
