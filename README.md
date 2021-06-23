## This Style Guide is based on Ruby Style Guide
https://rubystyle.guide/
Check this link to get common styles.

## Tabs or Spaces?
Use only spaces for indentation. No hard tabs.

## Indentation 
Use two __spaces__ per indentation level (aka soft tabs).

```ruby
# bad
def some_method
    do_something
end

# good
def some_method
  do_something
end
```

## Maximum Line Length
Limit lines to 100 characters.

## Class and Module declaration
Use compact declaration within `app` folder and extended declaration within `lib` folder
```ruby 
# app/controllers/namespace/another_namespace/foo_controller.rb

# bad
module Namespace
  module AnotherNamespace
    class FooController < ApplicationController
    end
  end  
end

# good
class Namespace::AnotherNamespace::FooController < ApplicationController

# lib/namespace/another_namespace/foo.rb

# bad
module Namespace::AnotherNamespace::Foo; end
class Namespace::AnotherNamespace::Foo; end

# good
module Namespace
  module AnotherNamespace
    module Foo
    end
  end
end

module Namespace
  module AnotherNamespace
    class Foo
    end
  end
end
```

## No space after Bang sign
No space after `!`
```ruby 
# bad
! something

# good 
!something
```

## Use Bang sign if method can raise an exception or in dangerous methods
Make use of `!` in the following cases
```ruby 
# bad
def do_something
  object.update!(attribute: 'some value')
end

def some_function
  raise StandardError unless something
end

# good
def do_something!
  object.update!(attribute: 'some_value')
end

def some_function!
  raise StandardError unless something
end
```

## Make use of native functions
Prefer use of native Ruby or Rails functions such as:
- blank?
- any?
- present?
- empty?
- exists?
- zero?
- positive?
- negative?

```ruby 
# bad
def is_zero?
  some_value == 0
end

def greater_than_zero?
  some_value > 0
end

# good
def is_zero?
  some_value.zero?
end

def greater_than_zero?
  some_value.positive?
end
```

## Nested Conditionals
From Ruby Style Guide
> Avoid use of nested conditionals for flow of control.
Prefer a guard clause when you can assert inv alid data. A guard clause is a conditional statement at the top of a function that bails out as soon as it can.

Make use of line break after `return` inside a method definition
```ruby
# bad
def compute_thing(thing)
  if thing[:foo]
    update_with_bar(thing[:foo])
    if thing[:foo][:bar]
      partial_compute(thing)
    else
      re_compute(thing)
    end
  end
end

# not bad but for good readability group the return statements
def compute_thing(thing)
  return unless thing[:foo]
  
  return if thing.is_a?(String)
  
  update_with_bar(thing[:foo])
  return re_compute(thing) unless thing[:foo][:bar]
  
  partial_compute(thing)
end

# good
def compute_thing(thing)
  return unless thing[:foo]
  return if thig.is_a?(String)
  
  update_with_bar(thing[:foo])
  return re_compute(thing) unless thing[:foo][:bar]
  
  partial_compute(thing)
end
```

## Exceptions
__Do not__ rescue from `Exception` as the highest level. Use rescue from `StandardError` class as the highest level.
```ruby 
# bad 
def foo
  # something_that_might_fail
rescue Exception => e
  # some handling error
end

# good
def foo
  # something_that_might_fail
rescue StandardError => e
  # some handling error
end
```

## Suppressing Exceptions
Don’t suppress exceptions.
```ruby
# bad
begin
  do_something # an exception occurs here
rescue SomeError
end

# good
begin
  do_something # an exception occurs here
rescue SomeError
  handle_exception
end

# good
begin
  do_something # an exception occurs here
rescue SomeError
  # Notes on why exception handling is not performed
end

# good
do_something rescue nil
```


## Implicit begin

Use implicit begin blocks where possible.
```ruby
# bad
def foo
  begin
    # main logic goes here
  rescue
    # failure handling goes here
  end
end

# good
def foo
  # main logic goes here
rescue
  # failure handling goes here
end
```

## Numbered parameters
```ruby
# bad
make_something do
  # do large stuff using multiple numbered parameters
  puts _1
  puts _2
  puts _3
  puts _4
end

make_something { puts "#{_1} #{_2} #{_3} #{_4}" }

# good
make_something do
  a = _1 * 5
  puts a + _1  # don't use more than one numbered parameter in a block
end

make_something { puts _1 }
```

## Handling `nil` references

Prefer the use of safe navigation operator (`&`) over the `try` method. It can hide some undesirable behaviour as it will return `nil` in the case of method inexistence.

```ruby
# kinda bad
'foo'.try(:bar)
=> nil

# preferable
'foo'&.bar
=> NoMethodError (undefined method `bar' for "foo":String)
```

## Single vs double quotes

In the majority of cases use single quotes. Use double quotes just in interpolation and when single quotes are need in a string.

```ruby
# bad
"Hello world"

# good
"'Hello world'"
'"Hello world"'
'Hello world'
"The answer is #{40 + 2}"
```

## URI.open
From ruby style guide:
> `Kernel#open` and `URI.open` enable not only file access but also process invocation by prefixing a pipe symbol (e.g., `open(“| ls”)`). So, it may lead to a serious security risk by using variable input to the argument of `Kernel#open` and `URI.open`.

```ruby
# bad
open(something)
URI.open(something)

# good
URI.parse(something).open
```

## Adding gems
__Always__ include versioning when adding gems to `gemfile` and use alphabetic order

```ruby
# bad
gem 'foobar'

# almost but not
gem 'foobar', '~> 1.2'
gem 'barfoo', '~> 3.4'

# good
gem 'barfoo', '~> 3.4'
gem 'foobar', '~> 1.2'
```

## Thin controllers
When logic cannot be written in maximum 3 lines inside a controller method, use an `Interactor`. This must be used just as a layer between controllers and business logic, not as a swiss knife for every occasion. There could be some exceptions to this rule.

## Default scope in models
Avoid the use of default scopes in models.

## Pluck vs map
Prefer `pluck` over `map` when querying DB.
```ruby
# bad
Foo.all.map(&:bar)

# good
Foo.pluck(:bar)
```



## Parallel Assignment
From Ruby Style Guide
> Avoid the use of parallel assignment for defining variables. Parallel assignment is allowed when it is the return of a method call, used with the splat operator, or when used to swap variable assignment. Parallel assignment is less readable than separate assignment.

```ruby
# bad
a, b, c, d = 'foo', 'bar', 'baz', 'foobar'

# good
a = 'foo'
b = 'bar'
c = 'baz'
d = 'foobar'

# good - swapping variable assignment
# Swapping variable assignment is a special case because it will allow you to
# swap the values that are assigned to each variable.
a = 'foo'
b = 'bar'

a, b = b, a
puts a # => 'bar'
puts b # => 'foo'

# good - method return
def multi_return
  [1, 2]
end

first, second = multi_return

# good - use with splat
first, *list = [1, 2, 3, 4] # first => 1, list => [2, 3, 4]

hello_array = *'Hello' # => ["Hello"]

a = *(1..3) # => [1, 2, 3]
```

## Keep Up to Date the .env.template file
When we add logic that makes use of ENV variables, update the used keys to `.env.template`

## Existence Check Shorthand
From Ruby Style Guide
> Use &&= to preprocess variables that may or may not exist. Using &&= will change the value only if it exists, removing the need to check its existence with if.
```ruby
# bad
if something
  something = something.downcase
end

# bad
something = something ? something.downcase : nil

# ok
something = something.downcase if something

# good
something = something && something.downcase

# better
something &&= something.downcase
```

## Predicate Methods Suffix
From Ruby Style Guide
> The names of predicate methods (methods that return a boolean value) should end in a question mark (i.e. Array#empty?). Methods that don’t return a boolean, shouldn’t end in a question mark.

```ruby
# bad
def even(value)
end

# good
def even?(value)
end
```

## Predicate Methods Prefix
From Ruby Style Guide
> Avoid prefixing predicate methods with the auxiliary verbs such as is, does, or can. These words are redundant and inconsistent with the style of boolean methods in the Ruby core library, such as empty? and include?.

```ruby
# bad
class Person
  def is_tall?
    true
  end

  def can_play_basketball?
    false
  end

  def does_like_candy?
    true
  end
end

# good
class Person
  def tall?
    true
  end

  def basketball_player?
    false
  end

  def likes_candy?
    true
  end
end
```

## Omit redundant boolean words

```ruby
# bad
if some == true
  # do_something
end

if some == false
  # do_something
end

# good
if some?
end

unless some?
end
```

## Make use of CoreExtensions to extend some core class existent logic
Make use of a directory inside `lib/core_extensions` to extend a class functionality.

```ruby
# bad
x = 'true'

if x == 'true'
  # do_something
end

# good
# lib/core_extensions/string/booleanable
module CoreExtensions
  module String
    module Booleanable
      def to_bool
        return true if %w(true 1 yes on t sí si).include? self.downcase
        return false if %w(false 0 no off f).include? self.downcase
        nil
      end
    end
  end
end

x = 'true'
if x.to_bool
  # do_something
end
```

## Make use of `deliver_later` for asynchronous jobs
__Always__ make use of `deliver_later` when dealing with jobs and mails.

```ruby
# bad
SomeMailer.deliver_now(some_param)

# good
SomeMailer.deliver_later(some_param)
```

## Use Time with zone when dealing with times.
Make use of `Time.current` instead of `Time.now` when dealing with hours. This will ensure the time always going to have the time zone.
```ruby
# bad
now = Time.now
# do some stuff
some_object.update(updated_at: now)

# good
now = Time.current
# do some stuff
some_object.update(updated_at: now)
```

## `module_function`
From Ruby Style Guide
> Prefer the use of module_function over extend self when you want to turn a module’s instance methods into class methods
```ruby
# bad
module Utilities
  extend self

  def parse_something(string)
    # do stuff here
  end

  def other_utility_method(number, string)
    # do some more stuff
  end
end

# good
module Utilities
  module_function

  def parse_something(string)
    # do stuff here
  end

  def other_utility_method(number, string)
    # do some more stuff
  end
end
```

# Blocks, Procs & Lambdas
## Proc Application Shorthand
Use the Proc call shorthand when the called method is the only operation of a block.
```ruby
# bad
names.map { |name| name.upcase }

# good
names.map(&:upcase)
```

## Single-line Blocks Delimiters
From Ruby Style Guide
> Prefer `{…}` over `do…end` for single-line blocks. Avoid using `{…}` for multi-line blocks (multi-line chaining is always ugly). Always use `do…end` for "control flow" and "method definitions" (e.g. in Rakefiles and certain DSLs). Avoid `do…end` when chaining.
```ruby
names = %w[Bozhidar Filipp Sarah]

# bad
names.each do |name|
  puts name
end

# good
names.each { |name| puts name }

# bad
names.select do |name|
  name.start_with?('S')
end.map { |name| name.upcase }

# good
names.select { |name| name.start_with?('S') }.map(&:upcase)
```

## SOLID design
Try to make your classes as **[SOLID](https://https://en.wikipedia.org/wiki/SOLID)** as possible. Here's a [LINK](https://medium.com/@cponcax/s-o-l-i-d-con-ruby-e5c68508d486) for better understanding

