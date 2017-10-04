<a name="start"></a>
[&larr; вернуться к
содержанию](https://github.com/VitaHub/Design-Patterns#behavioral)

### Комманда (Command)
*Поведенческий шаблон* проектирования, используемый при объектно-ориентированном
программировании, представляющий действие. Объект команды заключает в себе само
действие и его параметры.

#### Задача
Нужно выполнить определенную задачу, не имея информации о самой задаче и
получателе запроса.

#### Способ решения
Шаблон __Комманда__ отделяет объект, который должен выполнить определенную
задачу от того, кто знает, как её делать. Он инкапсулирует всю необходимую
информацию для выполнения задания в свой собственный объект, включая
получателя(ей), методы 

#### Пример
Рассмотрим реализацию кнопки какого-либо GUI, у которого есть метод,
вызываемый кликом по кнопке.

```ruby
class SlickButton
  # Lots of button drawing and management
  # code omitted...

  def on_button_push
    # Do something when the button is pushed
  end
end
```

Конечно, мы можем расширить класс кнопки переписав метод `on_button_push` для
выполнения определенных действий, когда пользователь кликает по ней. Например,
если кнопка должна сохранять документ, мы можем сделать так:

```ruby
class SaveButton < SlickButton
  def on_button_push
    # Save the current document...
  end
end
```

Тем не менее, сложный GUI может иметь тучу кнопок, а это значит, что у нас будет
уйма подклассов нашей кнопки. Есть более простой способ. Мы можем вычленить код,
который выполняет действие, в свой собственный объект, который реализует простой
интерфейс. Затем мы можем реорганизовать реализацию нашей кнопки, чтобы получить
объект команды в качестве параметра и вызвать ее при ее нажатии.

```ruby
class SaveCommand
  def execute
    # Save the current document...
  end
end

class SlickButton
  attr_accessor :command

  def initialize(command)
    @command = command
  end

  def on_button_push
    @command.execute if @command
  end
end

save_button = SlickButton.new(SaveCommand.new)
```

Шаблон команды очень полезен, если нам нужно реализовать функцию отмены. Все,
что нам нужно сделать, это реализовать в нашем командном объекте метод
`unexecute`. Например, так мы будем реализовывать задачу создания файла:

```ruby
class CreateFile < Command
  def initialize(path, contents)
    super "Create file: #{path}"
    @path = path
    @contents = contents
  end

  def execute
    f = File.open(@path, "w")
    f.write(@contents)
    f.close
  end

  def unexecute
    File.delete(@path)
  end
end
```

Другая ситуация, когда командный шаблон действительно удобен - это программы
установки. Объединяя его с шаблоном __компоновщик__, мы можем хранить список
задач, которые необходимо выполнить:

```ruby
class CompositeCommand < Command
  def initialize
    @commands = []
  end

  def add_command(cmd)
    @commands << cmd
  end

  def execute
    @commands.each {|cmd| cmd.execute}
  end
end

cmds = CompositeCommand.new
cmds.add_command(CreateFile.new('file1.txt', "hello world\n"))
cmds.add_command(CopyFile.new('file1.txt', 'file2.txt'))
cmds.add_command(DeleteFile.new('file1.txt'))
```

[&larr; вернуться к
содержанию](https://github.com/VitaHub/Design-Patterns#behavioral)
