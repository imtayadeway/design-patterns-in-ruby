The Null Object Pattern
-----------------------

1. An object with defined neutral ("null") behavior.
2. Instead of using a null reference to convey absence of an object,
   one uses an object which implements the expected interface, but
   whose method body is empty.
3. The advantage of this approach over a
   working default implementation is that a Null Object is very
   predictable and has no side effects: it does nothing.
4. The null object pattern can also be used to act as a stub for
   testing, if a certain feature such as a database is not available
   for testing.

```ruby
class Dog
  def sound
    'bark'
  end
end

class NullAnimal
  def sound
  end
end

def get_animal(type)
  case type
  when :dog
    Dog.new
  when nil
    NullAnimal.new
  end
end

get_animal(:dog).sound
# => "bark"
get_animal(nil).sound
# => nil
```
