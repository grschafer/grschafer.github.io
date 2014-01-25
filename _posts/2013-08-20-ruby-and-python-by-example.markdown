---
layout:     post
title:      Ruby and Python by Example
date:       2013-08-20 16:12:01
categories: guides
thumb: "/assets/images/python_ruby_logos.jpg"
---

<style>
/* 2 column code snippets */
.highlight {
  float: left;
  width: 50%;
}
section p, section h1, section h2, section h3, section h4, section h5 {
  clear: both;
}
</style>

After exploring Ruby briefly in a programming languages class last fall, I've finally gotten back around to playing with the language more. Most of my recent experience is in Python, so I've had a lot of fun comparing the two languages and their idioms. This list is a personal reference comparing how the same tasks can be solved in both languages.

In the rest of the post, *Ruby snippets are on the left* and *Python snippets on the right*. In the snippets, *lines of code that correspond between the two languages are aligned*, when possible. These examples were tested with Ruby 2.0.0 and Python 2.7.3.

```ruby
# Ruby snippets on the left
puts "Hello, World!"
```
```python
# Python snippets on the right
print "Hello, World!"
```


### How to Explore

To run a [REPL][repl] from the command line:

```bash
irb
```
```bash
python
```

I always like to see *what* I can do with various objects:

```ruby
"foo".methods.sort
String.instance_methods.sort
("foo".methods - Object.instance_methods).sort
```
```python
dir("foo")
```

To load a file (of function/class definitions, for example) to play with in the REPL:

```bash
irb -I . -r file.rb
```
```bash
python -i file.py
```


### Printing and String Interpolation

Printing values is useful for confirming expectations and debugging. Ruby's .inspect is similar to Python's repr() in that they both return the string form of the object they are called with. Ruby's "p" function calls .inspect on its arguments.

```ruby
a = "test"
puts a
# test
puts a.inspect
# "test"
p a # equivalent to above
# "test"
```
```python
a = "test"
print a
# test
print repr(a)
# 'test'
```

String interpolation/formatting is used to put expression or variable values into a string:

```ruby
puts "value of a: %s" % a
# value of a: test
puts "value of a: #{a}"
# value of a: test
```
```python
print "value of a: %s" % a
# value of a: test
print "value of a: {0}".format(a)
# value of a: test
```


### Nothing and Truthiness

Testing nothing (represented by nil in Ruby and None in Python):

```ruby
my_var.nil?
```
```python
my_var is None
```

True/false values are lowercase in Ruby and capitalized in Python. The following snippets contain the values that evaluate as false in each language:

```ruby
false
nil
```
```python
False
None
0
0.0
""
[]
```


### Boolean Expressions

The potential gotcha in this category is that Ruby's `and` and `or` operators have very low precedence and thus are [generally reserved for control flow][rubyandor]. For boolean expressions in Ruby, use && and ||.

```ruby
!false # true

# these short-circuit:
false && true # false
true || false # true
```
```python
not False # True

# these short-circuit:
False and True # False
True or False # True
```


### Arrays/Lists

An ordered, integer-indexed collection is called an `Array` in Ruby and a `list` in Python.


#### Instantiation

To make arrays/lists:

```ruby
a = []
b = [1,2,3]
c = Array.new
d = Array[1,2,3]
e = (1..5).to_a
```
```python
a = []
b = [1,2,3]
c = list()
d = list([1,2,3])
e = list(range(5))
```


#### Operations

Some simple operations available on arrays:

```ruby
a = [1,2]; b = [3,3]
a.include?(1) # true
a + b # [1,2,3,3]

# makes shallow copies:
[a] * 2 # [[1,2],[1,2]]

a[0] # 1
a.first # 1
a[-1] # 2
a.last # 2
a.size # 2
a.length # 2
a.count # 2
b.index(3) # 0
b.count(3) # 2
```
```python
a = [1,2]; b = [3,3]
1 in a # True
a + b # [1,2,3,3]

# makes shallow copies:
[a] * 2 # [[1,2],[1,2]]

a[0] # 1

a[-1] # 2

len(a) # 2


b.index(3) # 0
b.count(3) # 2
```


#### Slicing

Slicing in Ruby is typically done with Range objects, which have inclusive and exclusive forms relative to the end-value as follows: 0..2 contains [0,1,2] and 0...2 contains [0,1]. Basic slicing:

```ruby
vals = [a,b,c,d]
vals[1..2] # [b,c]
vals[1...3] # [b,c]
vals[-3..-2] # [b,c]
vals[-3...-1] # [b,c]

# alternate form:
# array[start_index, length]
vals[1,2] # [b,c]
vals[-3,2] # [b,c]
```
```python
vals = [a,b,c,d]
vals[1:3] # [b,c]

vals[-3:-1] # [b,c]
```

Slicing to/from the end of list:

```ruby
vals = [a,b,c,d]
vals[2...vals.size] # [c,d]
vals[2,vals.size] # [c,d]
vals.last(vals.size - 2) # [c,d]
vals[0...2] # [a,b]
vals[0,2] # [a,b]
vals.first(2) # [a,b]
```
```python
vals = [a,b,c,d]
vals[2:] # [c,d]


vals[:2] # [a,b]
```

Note: For list comprehensions and iterating, see [Blocks](#blocks).


### Hashes/Dicts

A mapping from keys to values is called a `Hash` in Ruby and a `dict`` in Python.


#### Instantiation

To make hashes/dicts:

```ruby
a = {}
b = {'x'=>1, 2=>2, Fixnum=>3}
c = Hash.new
d = Hash[[['x',1],[2,2]]]
e = Hash.new { |hash,key| hash[key] = [] }
# Blocks! Getting ahead of myself...
```
```python
a = {}
b = {'x':1, 2:2, int:3}
c = dict()
d = dict([['x',1],[2,2]])
e = defaultdict(list) # from collections module
```


#### Operations

Some simple operations available on hashes:

```ruby
a = {'x'=>2}
a['y'] = 5
a.has_key?('y') # true
a.key?('y') # true
a.delete('y') # 5
!a.include?('y') # true
a.keys # ['x']
a.values # [2]
a.to_a # [['x',2]]
```
```python
a = {'x':2}
a['y'] = 5
'y' in a # True

del a['y']
'y' not in a # True
a.keys() # ['x']
a.values() # [2]
a.items() # [('x',2)]
```


### Symbols

A unique piece of Ruby that you quickly run into is symbols. Symbols are [kind of like immutable strings][symbols1] indicated by a prepended colon (e.g. :vehicle) that have performance advantages over regular, mutable strings in Ruby. Symbols are [typically used for naming things][symbols2] like hash keys and for referencing variables, method names, etc.

Some symbol operations:

```ruby
:foo.to_s # "foo"
"foo".intern # :foo
"foo".to_sym # :foo
:foo == :foo # true
:foo.object_id == :foo.object_id # true
```


### Control Flow

Everything is pretty much the same here except Ruby gives more options (an obvious trend at this point):

If-block:

```ruby
if true
  puts "Gets here"
elsif false
  puts "Doesn't get here"
else
  puts "Definitely not getting here"
end
```
```python
if True:
  print "Gets here"
elif False:
  print "Doesn't get here"
else:
  print "Definitely not getting here"
```

Inline if:

```ruby
if answer then "right" else "wrong" end
(answer) ? "right" : "wrong"
puts "You're right!" if answer
puts "You're right!" unless !answer
```
```python
"right" if answer else "wrong"

print "You're right!" if answer
```

For-loop (in Ruby, `for` is just sugar for calling `.each` on the given Enumerable):

```ruby
for i in 0..2
  puts i
end
(0..2).each { |i| puts i }
# Blocks! Coming soon now...
```
```python
for i in range(3):
  print i
```

While-loop:

```ruby
while true
  # do stuff
end
until false
  # do stuff
end
```
```python
while True:
  # do stuff
```

There's a lot more that could be said here, about break/next, [case][case], [and/or][rubyandor], [redo and retry][reflow], [exception-handling][exceptions], and more.


### Methods/Functions

A named chunk of code is a method in Ruby and a function in Python:

```ruby
def myfunc
  puts "Hello!"
end
```
```python
def myfunc():
  print "Hello!"
```

The return keyword is optional in Ruby; if it's omitted then the value of the final expression in the method is returned:

```ruby
def myfunc
  1 + 1
end
myfunc # 2

def myfunc2
  return "foo"
  "nope"
end
myfunc # "foo"
```
```python
def myfunc():
  1 + 1

myfunc # None

def myfunc2():
  return "foo"

myfunc # "foo"
```

Ruby methods and Python functions can accommodate optional parameters:

```ruby
def myfunc(x, y=2)
  x + y
end
myfunc(2) # 4
myfunc(2,3) # 5
```
```python
def myfunc(x, y=2):
  return x + y

myfunc(2) # 4
myfunc(2,3) # 5
```

Both languages can collect extra arguments into an array:

```ruby
def myfunc(x, y=2, *args)
  args
end
myfunc(1, 2, 3, 4, 5) # [3,4,5]
```
```python
def myfunc(x, y=2, *args):
  return args

myfunc(1, 2, 3, 4, 5) # (3,4,5)
```

Ruby allows key-value pairs as arguments and both languages can collect extra keyword arguments:

```ruby
def myfunc(req, optional=2, *args,
           foo: 'bar', **options)
  p args
  p foo
  p options
end
myfunc(1, 2, 3, hey:'there', hi:3)
# [3]
# "bar"
# {:hey=>"there", :hi=>3}
```
```python
def myfunc(req, optional=2, *args, **kwargs):
  print args
  print kwargs

myfunc(1,2,3, hey='there', hi=3)
# (3,)
# {'hi': 3, 'hey': 'there'}
```

Only slightly related to methods/functions but worth mentioning: [the * (splat) and ** (double splat) operators in both languages can be used to unpack arrays and dictionaries (e.g. into method calls or a list of variables)][splat].


### Blocks, Procs, Lambdas<a id="blocks"></a>

At last! Blocks are my favorite part of Ruby so far, because they are the closest thing to Python's comprehensions (my favorite part of Python) and generators and the best way to accomplish tasks such as map/reduce/filter.

[Robert Sosinski covers blocks/procs/lambdas quite thoroughly][blockproc], so I'll keep my treatment to a summary and examples.


#### Summary

Blocks are anonymous functions that are (usually) implicitly passed to methods. Blocks are indicated by braces (common practice for one-line blocks) or do..end (for multi-line blocks). Arguments yielded to the block are inside pipes (|arg1, arg2,...|).

```ruby
# don't do foreach like this normally!
def foreach(arr)
  puts "block given? #{block_given?}"
  for i in 0...arr.size
    yield arr[i] # passes arr[i] to the block
  end
end

foreach([1,2,3]) { |x| puts x**2 }
# block given? true
# 1
# 4
# 9
foreach([1,2,3]) do |x|
  # do..end syntax for multi-line blocks
  puts x**2
end
# block given? true
# 1
# 4
# 9
```
```python
# definitely don't do this!
def foreach(arr):
  for i in range(len(arr)):
    yield arr[i]

for x in foreach([1,2,3]):
  print x**2
# 1
# 4
# 9

# the above is obviously very unnecessary,
# but it illustrates some of the similarity
# between Ruby blocks and Python generators
```

The block can be bound explicitly as a method parameter:

```ruby
def foreach(arr, &block)
  puts "block class: #{block.class}"
  for i in 0...arr.size
    block.call(arr[i]) # passes arr[i] to the block
  end
end

foreach([1,2,3]) { |x| puts x }
# block class: Proc
# 1
# 2
# 3
```
```python
def foreach(arr, myfunc):
  for i in range(len(arr)):
    myfunc(arr[i])

foreach([1,2,3], lambda x: print x)
# this is the equivalent of the Ruby code but
# won't work because lambdas in Python must
# contain 1 expression -- a SyntaxError is
# thrown when they contain statements like "print"
```

The previous example also revealed that blocks are instances of Proc, which can be manually created and called:

```ruby
double = Proc.new { |x| x * 2 }
double.call(3) # 6
```
```python
double = lambda x: x * 2
double(3) # 6
```

Procs (and blocks) are flexible about how many arguments they are passed:

```ruby
exam = Proc.new do |x,y|
  puts "#{x.inspect}, #{y.inspect}"
end
exam.call(1,2) # "1, 2"
exam.call(1) # "1, nil"
exam.call(1,2,3) # "1, 2"
```

Lambdas are pretty similar, but are strict about how many arguments are passed (though parameters can be specified as optional):

```ruby
exam = lambda do |x,y=3|
  puts "#{x.inspect}, #{y.inspect}"
end
exam.call(1,2) # "1, 2"
exam.call() # ArgumentError: wrong number of arguments (0 for 1..2)
exam.call(1) # "1, 3"
exam.call(1,2,3) # ArgumentError: wrong number of arguments (3 for 1..2)

# alternate syntax
exam = ->(x, y=3) do
  puts "#{x.inspect}, #{y.inspect}"
end
```

The other main difference between lambdas and Procs is the return behavior. Lambdas return from themselves, whereas Procs return out of the method in which they are enclosed:

```ruby
def method1
  Proc.new { return "from proc" }.call
  return "from method1"
end
def method2
  lambda { return "from lambda" }.call
  return "from method2"
end

method1 # "from proc"
method2 # "from method2"
```

An [article by Dave Thomas shows how Procs and lambdas can be curried][curry].


#### Examples

Iterating:

```ruby
[1,2,3].each { |x| puts x }
# 1
# 2
# 3

[:a,:b,:c].each_with_index do
  |letter, index| puts "#{index} #{letter.inspect}"
end
# 0 :a
# 1 :b
# 2 :c

```
```python
for x in [1,2,3]:
  print x
# 1
# 2
# 3

for index, letter in enumerate(['a','b','c']):
  print index, letter
# 0 a
# 1 b
# 2 c
```

Mapping:

```ruby
a = [1,2,3]
a.map { |x| x**2 } # [1,4,9]
# map returns a new array
# use map! to mutate the original

a.collect { |x| x**2 } # [1,4,9]
# collect is an alias of map
```
```python
a = [1,2,3]
[x**2 for x in a] # [1,4,9]
map(lambda x: x**2, a) # [1,4,9]
```

Reducing:

```ruby
a = [1,2,3]

a.reduce(:+) # 6 (uses :+ method on Fixnum)
a.reduce(10, :+) # 16 (initial value of 10)
a.reduce(10) { |sum,x| sum + x } # 16
# inject is an alias of reduce
```
```python
a = [1,2,3]
import operator
reduce(operator.add, a) # 6
reduce(operator.add, a, 10) # 16
reduce(lambda sum,x: sum + x, a, 10) # 16
# unfortunately there's no pleasant way to
# reduce with a comprehension
```

Filtering:

```ruby
a = [1,2,3]
a.select { |x| x.even? } # [2]
a.find_all { |x| x.odd? } # [1,3]
```
```python
a = [1,2,3]
[x for x in a if x % 2 == 0] # [2]
filter(lambda x: x % 2 == 1, a) # [1,3]
```

Say you want to find the sum of the cubes of the odd numbers between 0 and 20:

```ruby
# using \ to write across multiple lines

(0...20).select { |x| x.odd? } \
           .collect { |x| x**3 } \
           .inject(0, :+)
# 19900
```
```python
# using \ to write across multiple lines
import operator
reduce(operator.add, \
          [x**3 for x in range(20) \
          if x % 2 == 1], 0)
# 19900
```

Most functions from Python's itertools (combinations, permutations, group_by, etc.) exist as methods on [Enumerable][enumdoc] or [Array][arraydoc] or can be created with blocks and, as appropriate, [Enumerable.lazy][lazyenum] (but know that [lazy is slower!][lazyslow]).


### Classes

Final topic for this list! Classes are blueprints (usually containing related data and behaviors) from which instances are created.

```ruby
class Person
  attr_reader :name # getter for name
  attr_accessor :mood # getter/setter for mood
  @@count = 0 # class var

  # constructor
  def initialize(name, mood=:happy)
    @name = name # instance var
    @mood = mood # instance var
    @@count += 1
  end
  
  def self.count # class method
    @@count
  end
end

Person.count # 0
bob = Person.new('Bob')
bob.mood # :happy
Person.count # 1
amy = Person.new('Amy')
Person.count # 2
```
```python
class Person:
  count = 0 # class var
  
  # constructor
  # all instance methods have 'self' as 1st param
  def __init__(self, name, mood='happy'):
    self.name = name # instance var
    self.mood = mood # instance var
    Person.count += 1

  # class methods have 'cls' as 1st param
  @classmethod
  def get_count(cls):
    return cls.count
    

Person.count # 0
bob = Person('Bob')
bob.mood # 'happy'
Person.count # 1
amy = Person('Amy')
Person.get_count() # 2
```

OOP concepts and tools extend far beyond this example, and hopefully I can cover topics such as inheritance, mixins, metaprogramming, modules, namespaces, and more in the future. I'll wrap up this post here though, as it is already longer than I anticipated!  =)


### Documentation

[Another comparison of Ruby/Python on the Ruby site][rubyfrompython]

Where to find documentation:

* Ruby: <http://www.ruby-doc.org/>
* Python 2.7: <http://docs.python.org/2.7/>


### Attribution

Thanks to my housemates for editing!


I'll happily respond to any comments, corrections, or other responses on twitter, email, or GitHub!


[repl]: http://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop
[rubyandor]: http://devblog.avdi.org/2010/08/02/using-and-and-or-in-ruby/
[symbols1]: http://www.robertsosinski.com/2009/01/11/the-difference-between-ruby-symbols-and-strings/
[symbols2]: http://stackoverflow.com/questions/11447537/using-ruby-symbols
[case]: http://stackoverflow.com/questions/948135/how-can-i-write-a-switch-statement-in-ruby
[reflow]: http://www.tutorialspoint.com/ruby/ruby_loops.htm
[exceptions]: http://www.skorks.com/2009/09/ruby-exceptions-and-exception-handling/
[splat]: http://blog.marc-andre.ca/2013/02/23/ruby-2-by-example/#keyword_arguments
[blockproc]: http://www.robertsosinski.com/2008/12/21/understanding-ruby-blocks-procs-and-lambdas/
[curry]: http://pragdave.blogs.pragprog.com/pragdave/2008/09/fun-with-procs.html
[enumdoc]: http://www.ruby-doc.org/core-2.0/Enumerable.html
[arraydoc]: http://www.ruby-doc.org/core-2.0/Array.html
[lazyenum]: http://www.ruby-doc.org/core-2.0/Enumerable.html#method-i-lazy
[lazyslow]: http://blog.marc-andre.ca/2013/02/23/ruby-2-by-example/#lazy_enumerators
[rubyfrompython]: http://www.ruby-lang.org/en/documentation/ruby-from-other-languages/to-ruby-from-python/>
