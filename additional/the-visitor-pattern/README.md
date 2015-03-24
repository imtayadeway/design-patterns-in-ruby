```ruby
class CarElement
  def accept(visitor)
    NotImpelementedError
  end
end

module Visitable
  def accept(visitor)
    visitor.visit(self)
  end
end

class Wheel < CarElement
  include Visitable

  attr_reader :name
  def initialize(name)
    @name = name
  end
end

class Engine < CarElement
  include Visitable
end

class Body < CarElement
  include Visitable
end

class Car < CarElement
  def initialize
    @elements = []
    @elements << Wheel.new("front left")
    @elements << Wheel.new("front right")
    @elements << Wheel.new("back left")
    @elements << Wheel.new("back right")
    @elements << Body.new
    @elements << Engine.new
  end

  def accept(visitor)
    @elements.each do |element|
      element.accept(visitor)
    end
    visitor.visit(self)
  end
end

class CarElementPrintVisitor
  def visit(subject)
    puts "Visiting: %s" % subject.class.to_s
  end
end

class CarElementDoVisitor
  def visit(subject)
    puts "Do some other visitation: %s" % subject.class.to_s
  end
end

c = Car.new
c.accept(CarElementPrintVisitor.new)
c.accept(CarElementDoVisitor.new)
```
