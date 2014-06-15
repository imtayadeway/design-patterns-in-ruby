The Factory Method Pattern
--------------------------

1. Really just the template method pattern applied to the problem of
   creating new objects.
2. The **creators** are the base and concrete classes that contain the
   factory methods.
3. The **products** are the objects being created.

```ruby
class Duck
  def initialize(name)
    @name = name
  end

  def eat
    puts "Duck #{ @name } is eating."
  end

  def speak
    puts "Duck #{ @name } says Quack!"
  end

  def sleep
    puts "Duck #{ @name } sleeps quietly."
  end
end

class Frog
  def initialize(name)
    @name = name
  end

  def eat
    puts "Frog #{ @name } is eating."
  end

  def speak
    puts "Frog #{ @name } says Crooooaaaak!"
  end

  def sleep
    puts "Frog #{ @name } doesn't sleep; he croaks all night!."
  end
end

class Pond
  def initialize(number_animals)
    @animals = []
    number_animals.times do |i|
      animal = new_animal("Animal#{ i }")
      @animals << animal
    end
  end

  def simulate_one_day
    @animals.each { |animal| animal.speak }
    @animals.each { |animal| animal.eat }
    @animals.each { |animal| animal.sleep }
  end
end

class DuckPond < Pond
  def new_animal(name)
    Duck.new(name)
  end
end

class FrogPond < Pond
  def new_animal(name)
    Frog.new(name)
  end
end

pond = FrogPond.new(3)
pond.simulate_one_day
```

### Parameterized Factory Methods

```ruby
class Algae
  def initialize(name)
    @name = name
  end

  def grow
    puts "The Algae #{ @name } soaks up the sun and grows."
  end
end

class WaterLily
  def initialize(name)
    @name = name
  end

  def grow
    puts "The water lily #{ @name } floats, soaks up the sun, and grows."
  end
end

class Pond
  def initialize(number_animals, number_plants)
    @animals = []
    number_animals.times do |i|
      animal = new_organism(:animal, "Animal#{ i }")
      @animals << animal
    end

    @plants = []
    number_plants.times do |i|
      plant = new_organism(:plant, "Plant#{ i }")
      @plants << plant
    end
  end

  def simulate_one_day
    @plants.each { |plant| plant.grow }
    @animals.each { |animal| animal.speak }
    @animals.each { |animal| animal.eat }
    @animals.each { |animal| animal.sleep }
  end
end

class DuckWaterLilyPond < Pond
  def new_organism(type, name)
    if type == :animal
      Duck.new(name)
    elsif type == :plant
      WaterLily.new(name)
    else
      raise "Unknown organism type: #{ type }"
    end
  end
end

class FrogAlgaePond < Pond
  def new_organism(type, name)
    if type == :animal
      Frog.new(name)
    elsif type == :plant
      Algae.new(name)
    else
      raise "Unknown organism type: #{ type }"
    end
  end
end
```

### Store Classes in instance variables

```ruby
class Pond
  def initialize(number_animals, animal_class, number_plants, plant_class)
    @animal_class = animal_class
    @plant_class = plant_class

    @animals = []
    number_animals.times do |i|
      animal = new_organism(:animal, "Animal#{ i }")
      @animals << animal
    end

    @plants = []
    number_plants.times do |i|
      plant = new_organism(:plant, "Plant#{ i }")
      @plants << plant
    end
  end

  def simulate_one_day
    @plants.each { |plant| plant.grow }
    @animals.each { |animal| animal.speak }
    @animals.each { |animal| animal.eat }
    @animals.each { |animal| animal.sleep }
  end

  def new_organism(type, name)
    if type == :animal
      @animal_class.new(name)
    elsif type == :plant
      @plant_class.new(name)
    else
      raise "Unknown organism type: #{ type }"
    end
  end
end
```
