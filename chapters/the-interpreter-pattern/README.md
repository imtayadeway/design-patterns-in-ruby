The Interpreter Pattern
-----------------------

1. First, the parser reads in the program text and produces a data
   structure, called an **abstract syntax tree (AST)**.
2. The AST represents the same information as the original program,
   but transformed into a tree of objects that, unlike the original
   program text, can be executed with reasonable efficiency.
3. Second, the AST is evaluated against some set of external
   conditions, or context, to produce the desired computation.
4. The leaf nodes, or **terminals**, represent the most basic
      building blocks.
5. The nonleaf nodes, or **nonterminals**, represent the higher-order concepts.
6. The GoF called the key method 'interpret', but 'evaluate' or
   'execute' make sense too.
7. The values or conditions supplied at the time the AST is
   interpreted are the **context**.
8. ASTs are specialized examples of the Composite pattern, with the
   nonterminal expressions playing the parts of the composites.

```ruby
require 'find'

class Expression
  def |(other)
    Or.new(self, other)
  end

  def &(other)
    And.new(self, other)
  end
end

class All < Expression
  def evaluate(dir)
    results = []
    Find.find(dir) do |p|
      next unless File.file?(p)
      results << p
    end
    results
  end
end

class Filename < Expression
  def initialize(pattern)
    @pattern = pattern
  end

  def evaluate(dir)
    results = []
    Find.find(dir) do |p|
      next unless File.file?(p)
      name = File.basename(p)
      results << p if File.fnmatch(@pattern, name)
    end
    results
  end
end

class Bigger < Expression
  def initialize(size)
    @size = size
  end

  def evaluate(dir)
    results = []
    Find.find(dir) do |p|
      next unless File.file?(p)
      results << p if File.size(p) > @size
    end
    results
  end
end

class Writable < Expression
  def evaluate(dir)
    results = []
    Find.find(dir) do |p|
      next unless File.file?(p)
      results << p if File.writable?(p)
    end
    results
  end
end

class Not < Expression
  def initialize(expression)
    @expression = expression
  end

  def evaluate(dir)
    All.new.evaluate(dir) - @expression.evaluate(dir)
  end
end

expr_not_writable = Not.new(Writable.new)
readonly_files = expr_not_writable.evaluate('test_dir')

small_expr = Not.new(Bigger.new(1024))
small_files = small_expr.evaluate('test_dir')

not_mp3_expr = Not.new(FileName.new('*.mp3'))
not_mp3s = not_mp3_expr.evaluate('test_dir')

class Or < Expression
  def initialize(expression1, expression2)
    @expression1 = expression1
    @expression2 = expression2
  end

  def evaluate(dir)
    result1 = @expression1.evaluate(dir)
    result2 = @expression2.evaluate(dir)
    (result1 + result2).sort.uniq
  end
end

class And < Expression
  def initialize(expression1, expression2)
    @expression1 = expression1
    @expression2 = expression2
  end

  def evaluate(dir)
    result1 = @expression1.evaluate(dir)
    result2 = @expression2.evaluate(dir)
    (result1 & result2)
  end
end

big_or_mp3_expr = Or.new(Bigger.new(1024), FileName.new('*.mp3'))
big_or_mp3s = big_or_mp3_expr.evaluate('test_dir')

complex_expression = And.new(
                             And.new(Bigger.new(1024), FileName.new('*.mp3')),
                             Not.new(Writable.new)
                             )

# different contexts:

complex_expression.evaluate('test_dir')
complex_expression.evaluate('/tmp')

# parser-less interpreter

def all
  All.new
end

def bigger(size)
  Bigger.new(size)
end

def name(pattern)
  FileName.new(pattern)
end

def except(expression)
  Not.new(expression)
end

def writable
  Writeable.new
end

(bigger(2000) & except(writable)) | file_name('*.mp3')
```

### A Parser

```ruby
class Parser
  def initialize(text)
    @tokens = text.scan(/\(|\)|[\w\.\*]+/)
  end

  def next_token
    @tokens.shift
  end

  def expression
    token = next_token

    if token == nil
      return nil

    elsif token == '('
      result = expression
      raise 'Expected )' unless next_token == ')'
      result

    elsif token == 'all'
      return All.new

    elsif token == 'writable'
      return Writable.new

    elsif token == 'bigger'
      return Bigger.new(next_token.to_i)

    elsif token == 'filename'
      return FileName.new(next_token)

    elsif token == 'not'
      return Not.new(expression)

    elsif token == 'and'
      return And.new(expression, expression)

    elsif token == 'or'
      return Or.new(expression, expression)

    else
      raise "Unexpected token: #{token}"
    end
  end
end
```
