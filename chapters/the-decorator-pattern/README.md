The Decorator Pattern
---------------------

1. Enables you to add an enhancement to an existing object easily.
2. Allows you to layer features atop one another so that you can
   construct objects that have exactly the right set of capabilities
   that you need for any given situation.
3. The Concrete Component is the 'real' objet, the object that
   implements the basic component functionality.
4. The Decorator class has a reference to a Component - the next
   Component in the Decorator chain - and it implements all the
   methods of the Component type.

### Formal Decoration

```ruby
class SimpleWriter
  def initialize(path)
    @file = File.open(path, 'w')
  end

  def write_line(line)
    @file.print(line)
    @file.print("\n")
  end

  def pos
    @file.pos
  end

  def rewind
    @file.rewind
  end

  def close
    @file.close
  end
end

require 'forwardable'

class WriterDecorator
  extend Forwardable

  def_delegators :@real_writer, :write_line, :rewind, :pos, :close

  def initialize(real_writer)
    @real_writer = real_writer
  end
end

class NumberingDecorator < WriterDecorator
  def initialize(real_writer)
    super(real_writer)
    @line_number = 1
  end

  def write_line(line)
    @real_writer.write_line("#{ @line_number }: #{ line }")
    @line_number += 1
  end
end

writer = NumberingWriter.new(SimpleWriter.new('final.txt'))
write.write_line('Hello out there.')

class CheckSummingWriter < WriterDecorator
  attr_reader :check_sum

  def initialize(real_writer)
    @real_writer = real_writer
    @check_sum = 0
  end

  def writee_line(line)
    line.each_byte { |byte| @check_sum = (@check_sum + byte) % 256 }
    @check_sum += "\n"[0] % 256
    @real_writer.write_line(line)
  end
end

class TimeStampingWriter < WriterDecorator
  def write_line(line)
    @real_writer.write_line("#{ Time.now }: #{ line }")
  end
end

writer = CheckSummingWriter.new(TimeStampingWriter.new(
         NumberingWriter.new(SimpleWriter.new('final.txt'))
        ))

writer.write_line('Hello out there')
```

### Wrapping Methods

```ruby
w = SimpleWriter.new('out)

class << w
  alias old_write_line write_line

  def write_line(line)
    old_write_line("#{ Time.now ): #{ line }}")
  end
end
```

### Decorating with Modules

```ruby
module TimeStampingWriter
  def write_line(line)
    super("#{ Time.now }: #{ line }")
  end
end

module NumberingWriter
  attr_reader :line_number

  def write_line(line)
    @line_number = 1 unless @line_number
    super("#{ @line_number }: #{ line }")
    @line_number += 1
  end
end

w = SimpleWriter.new('out')
w.extend(NumberingWriter)
w.extend(TimeStampingWriter)

w.write_line('hello')
```
