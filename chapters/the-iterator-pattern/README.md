The Iterator Pattern
--------------------

### External Iterators

1. Provide a way to access the elements of an aggregate object sequentially without exposing its underlying representation.
2. The iterator is a separate object from the aggregate.

```ruby
class ArrayIterator
  def initialize(array)
    @array = array
    @index = 0
  end

  def has_next?
    @index < @array.length
  end

  def item
    @array[@index]
  end

  def next_item
    value = @array[@index]
    @index += 1
    value
  end
end

array = ['red', 'green', 'blue']

i = ArrayIterator.new(array)
while i.has_next?
  puts "item: #{ i.next_item }"
end

# => item: red
# => item: green
# => item: blue
```

### Internal Iterators

1. By using a code block, you pass the logic down into the aggregate.
2. The aggregate can then call your code block for each of its sub-objects.

```ruby
def for_each_element(array)
  i = 0
  while i < array.length
    yield(array[i])
    i += 1
  end
end

a = [10, 20, 30]
for_each_element(a) { |element| puts "The element is #{ element }"}

# => The element is 10
# => The element is 20
# => The element is 30
```

### Enumerable

```ruby
class Account
  attr_accessor :name, :balance

  def initialize(name, balance)
    @name = name
    @balance = balance
  end

  def <=>(other)
    balance <=> other.balance
  end
end

class Portfolio
  include Enumerable

  def initialize
    @accounts = []
  end

  def each(&block)
    @accounts.each(&block)
  end

  def add_account(account)
    @accounts << account
  end
end


my_portfolio.any? { |account| account.balance > 2000 }
my_portfolio.all? { |account| account.balance >= 10 }
```
