# Memery

Memery is a gem for memoization in Ruby.

## Usage

```ruby
class A
  include Memery

  memoize def call
    puts "calculating"
    42
  end

  # or:
  # def call
  #   ...
  # end
  # memoize :call
end

a = A.new
a.call # => 42
a.call # => 42
a.call # => 42

# Text will be printed only once.
```

## Difference with other gems
Memery is very similar to [Memoist](https://github.com/matthewrudy/memoist). The difference is that it doesn't override methods, instead it uses Ruby 2 `Module.prepend` feature. This approach is cleaner and it allows subclasses' methods to work properly: if you redefine a memoized method in a subclass, it's not memoized by default, but you can memoize it normally (without using awkward `identifier: ` argument) and it will just work:

```ruby
class A
  include Memery

  memoize def x(param)
    param
  end
end

class B < A
  memoize def x(param)
    super(2) * param
  end
end

b = B.new
b.x(1) # => 2
b.x(2) # => 4
b.x(3) # => 6

b.instance_variable_get(:@_memery_memoized_values)
# => {:x_70318201388120=>{[1]=>2, [2]=>4, [3]=>6}, :x_70318184636620=>{[2]=>2}}
```

Note how both method's return values are cached separately and don't interfere with each other.

The other key difference is that it doesn't change method's signature (no extra `reload` param). If you need to get unmemoized result of method, just create an unmemoized version like this:

```ruby
memoize def users
  get_users
end

def get_users
  # ...
end
```

Alternatively, you can clear the whole instance's cache:

```ruby
a.clear_memery_cache!
```

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).
