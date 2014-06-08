The Template Method Pattern
---------------------------

1. Build an abstract base class with a skeletal method (called the template method).
2. The template method makes calls to abstract methods, which are supplied by concrete subclasses.
3. Non-abstract methods that can be overridden in the concrete class are called hook methods.

```ruby
class Report
  def initialize
    @title = 'Monthly Report'
    @text = ['Things are going', 'really, really well.']
  end
  
  def output_report
    output_start
    output_head
    output_body_start
    output_body
    output_body_end
    output_end
  end
  
  def output_body
    @text.each do |line|
      output_line(line)
    end
  end
  
  def output_start
  end
  
  def output_head
    output_line(@title)  
  end
  
  def output_body_start
  end
  
  def output_line(line)
    raise 'Called abstract method: output_line'
  end
  
  def output_body_end
  end
  
  def output_end
  end
end

class HTMLReport < Report
  def output_start
    puts '<html>'
  end
  
  def output_head
    puts '  <head>'
    puts "    <title>#{ @title }</title>"
    puts '  </head>'
  end
  
  def output_body_start
    puts '<body>'
  end
  
  def output_line(line)
    puts "  <p>#{ line }</p>"
  end
  
  def output_body_end
    puts '</body>'
  end
  
  def output_end
    puts '</html>'
  end
end

class PlainTextReport < Report
  def output_head
    puts "**** #{ @title } ****"
    puts
  end
  
  def output_line(line)
    puts line
  end
end

report = HTMLReport.new
report.output_report

report = PlainTextReport
report.output_report
```
