<a name="start"></a>
[&larr; вернуться к
содержанию](https://github.com/VitaHub/Design-Patterns#structural)

### Компоновщик (Composite)
*Структурный шаблон* проектирования, объединяющий объекты в древовидную
структуру для представления иерархии от частного к целому. Компоновщик позволяет
клиентам обращаться к отдельным объектам и к группам объектов одинаково.

#### Задача
Построить древовидную иерархию объектов и взаимодействовать со всеми ними
одинаково, независимо от того, является ли объект листовым или нет.

#### Способ решения
В паттерне __Компоновщик__ есть три основных класса: __компонент__, __листок__ и
сам __компоновщик__. Компонент - это базовый класс, который определяет общий
интерфейс для всех компонентов. Листок - неделимая составная часть процесса.
Компоновщик - компонент высшего уровня, состоящий из субкомпонентов и
выступающий одновременно в качестве компонента и коллекции компонентов.

#### Пример
Нужно построить систему, которая отслеживает производство тортов. Ключевое
требование - возможность узнать как долго длится приготовление торта. Процесс
выпекания довольно сложен и состоит из множества мелкий подзадач. Весь процесс
целиком может быть представлен в таком виде:

```
|__ Manufacture Cake
    |__ Make Cake
    |   |__ Make Batter
    |   |   |__ Add Dry Ingredients
    |   |   |__ Add Liquids
    |   |   |__ Mix
    |   |__ Fill Pan
    |   |__ Bake
    |   |__ Frost
    |
    |__ Package Cake
        |__ Box
        |__ Label
```

В шаблоне __Компоновщик__ мы смоделируем каждый шаг в отдельном классе с общим
интерфейсом и возможность возврата количества затраченного на процесс времени.
Определим базовый класс `Task`, который будет играть роль __компонента__.

```ruby
class Task
  attr_accessor :name, :parent

  def initialize(name)
    @name = name
    @parent = nil
  end

  def get_time_required
    0.0
  end
end
```

Теперь мы можем создавать классы, отвечающий за основные задания, так называемые
листовые классы, как `AddDryIngredientsTask`:

```ruby
class AddDryIngredientsTask < Task
  def initialize
    super('Add dry ingredients')
  end

  def get_time_required
    1.0
  end
end
```

Теперь нам нужен контейнер для взаимодействия с комплексными задачами, которые
состоят из любого количества подзадач, но при этом внешне выглядят как любые
други задачи. Напишем класс компоновщика:

```ruby
class CompositeTask < Task
  def initialize(name)
    super(name)
    @sub_tasks = []
  end

  def add_sub_task(task)
    @sub_tasks << task
    task.parent = self
  end

  def remove_sub_task(task)
    @sub_tasks.delete(task)
    task.parent = nil
  end

  def get_time_required
    @sub_tasks.inject(0.0) {|time, task| time += task.get_time_required}
  end
end
```

С этим базовым классом мы можем строить комплексные задачи, которые ведут себя
как обычные, т.к. они реализую интерфейс класса `Task`, а так же дают
возможность добавлять сабтаски в помощью метода `add_sub_task`. Создадим
`MakeButterTask`:

```ruby
class MakeBatterTask < CompositeTask
  def initialize
    super('Make batter')
    add_sub_task(AddDryIngredientsTask.new)
    add_sub_task(AddLiquidsTask.new)
    add_sub_task(MixTask.new)
  end
end
```

Важно понимать, что дерево объектов может углубляться настолько, на сколько
нужно. `MakeButterTask` содержит только листовые объекты, но мы можем создавать
объекты, которые содержат составные объекты и при этом вести себя так же:

```ruby
class MakeCakeTask < CompositeTask
  def initialize
    super('Make cake')
    add_sub_task(MakeBatterTask.new)
    add_sub_task(FillPanTask.new)
    add_sub_task(BakeTask.new)
    add_sub_task(FrostTask.new)
    add_sub_task(LickSpoonTask.new)
  end
end
```

[&larr; вернуться к
содержанию](https://github.com/VitaHub/Design-Patterns#structural)
