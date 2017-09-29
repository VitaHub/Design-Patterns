[&larr; вернуться к
содержанию](https://github.com/VitaHub/Design-Patterns#structural)

### Адаптер (Adapter)
*Структурный шаблон* проектирования, предназначенный для организации
использования функций объекта, недоступного для модификации, через
специально созданный интерфейс.

#### Задача
Нам нужно, чтобы один объект (клиент) обращался к другому объекту
(адаптируемому), который имеет неподходящий интерфейс.

#### Способ решения
Обернуть адаптируемый объект в новый класс-адаптер. Этот класс предоставит
интерфейс, который будет понятен клиенту, но при этом вся работа будет
выполняться адаптируемым объектом.

#### Пример
Есть класс, который шифрует файл. На входе он принимает два файла: для чтения и
для записи.

```ruby
class Encrypter
  def initialize(key)
    @key = key
  end

  def encrypt(reader, writer)
    key_index = 0
    while not reader.eof?
      clear_char = reader.getc
      encrypted_char = clear_char ^ @key[key_index]
      writer.putc(encrypted_char)
      key_index = (key_index + 1) % @key.size
    end
  end
end
```

Но что, если данные, которые нам нужно зашифровать, являются строкой, а не
файлом? В таком случае нам нужен объект, который выглядит как открытый файл и
поддерживает тот же интерфейс, что и объект класса Ruby `IO`. Для этого мы
создадим `StringIOAdapter`.

```ruby
class StringIOAdapter
  def initialize(string)
    @string = string
    @position = 0
  end

  def getc
    if @position >= @string.length
      raise EOFError
    end
    ch = @string[@position]
    @position += 1
    return ch
  end

  def eof?
    return @position >= @string.length
  end
end
```

Теперь мы можем использовать строку (адаптируемый объект) как открытый файл.
Таким образом наш адаптируемый объект реализует часть `IO` интерфейса, что нам
и требовалось.

```ruby
encrypter = Encrypter.new('XYZZY')
reader= StringIOAdapter.new('We attack at dawn')
writer=File.open('out.txt', 'w')
encrypter.encrypt(reader, writer)
```

[&larr; вернуться к
содержанию](https://github.com/VitaHub/Design-Patterns#structural)
