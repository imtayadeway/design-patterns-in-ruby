The Adapter Pattern
-------------------

1. An adapter is an object that crosses the chasm between the interface that you have and the interface that you need.

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

reader = File.open('message.txt')
writer = File.open('message.encrypted', 'w')
encrypter = Encrypter.new('my secret key')
encrypter.encrypt(reader, writer)

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
    ch
  end

  def eof?
    @position >= @string.length
  end
end

encrypter = Encrypter.new('XYZZY')
reader = StringIOAdapter.new('We attach at dawn')
writer = File.open('out.txt', 'w')
encrypter.encrypt(reader, writer)
```

### Near Misses

1. Modify the class:

```ruby
class Renderer
  def render(text_object)
    text = text_object.text
    size = text_object.size_inches
    color = text_object.color

    # Render the text...
  end
end

class TextObject
  attr_reader :text, :size_inches, :color

  def initialize(text, size_inches, color)
    @text = text
    @size_inches = size_inches
    @color = color
  end
end

class BritishTextObject
  attr_reader :string, :size_mm, :colour
end

#
# ....reopen the class....
#

require 'british_text_object'

class BritishTextObject
  alias_method :text, :string
  alias_method :color, :colour

  def size_inches
    size_mm / 25.4
  end
end
```

2. Modify a single instance:

```ruby
bto = BritishTextObject.new('hello', 50.8, :blue)

class << bto
  def color
    colour
  end

  def text
    string
  end

  def size_inches
    size_mm / 25.4
  end
end
```
