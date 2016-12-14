# rails-styleguide
A pragmatic WIP style guide for Rails projects. It prefers being explicit and identifiabile over implicit and ambiguous.



## Use explicit returns

Avoid:
```ruby
def do_stuff(value)
  if value > 2
    'potato'
  else value < 2
    'sugar glider'
  else
    'turtle'
  end
end
```

Prefer:
```ruby
def do_stuff(value)
  if value > 2
    return 'potato'
  elsif value > 2
    return 'sugar glider'
  else
    return 'turtle'
  end
end
```

Using explicit returns has some advantages over implicit returns:

**The code is more resilient to changes.**   
If future changes are added below the line that is supposed to return, an explicit return ensures the code still operates as intended unless intentionally changed.

Avoid:
```ruby
def do_stuff(value)
  if value > 2
    'potato'
  else value < 2
    'sugar glider'
  else
    'turtle'
  end
end
```

Above example with a change - oops, error!
```ruby
def do_stuff(value)
  if value > 2
    'potato'
    log_value(value) #Logical error! This returns the return value of log_value now instead of 'potato'
  else value < 2
    'sugar glider'
  else
    'turtle'
  end
end
```

This time, the behavior is preserved:
```ruby
def do_stuff(value)
  if value > 2
    return 'potato' #Previous behavior is preserved
    log_value(value) # The error is isolated to the new code.
  else value < 2
    return 'sugar glider'
  else
    return 'turtle'
  end
end
```

**It lets you ignore parts of the method**  
If you're following the flow of logic and you encounter a return, you know for a fact that the method will stop execution at that point - you can safely ignore anything below it.

Avoid:
```ruby
def do_complex_stuff(params)
  value = nil
  if params[:really_important_field].nil?
    value = 'Error!' #You have to look at the rest of the method to see if value changed. The else hides it, but in other instances it won't.
  else
    do_some_stuff
    do_other_stuff
    if other_stuff_done?
      value = do_more_stuff
    else
      value = do_slightly_less_stuff
    end
  end
  value
end
```

Prefer:
```ruby
def do_complex_stuff(params)
  if params[:really_important_field].nil?
    return 'Error!' #You know it's returning here, no need to look at the rest of the method unless you want to.
  end
  
  do_some_stuff
  do_other_stuff
  if other_stuff_done?
    return do_more_stuff
  else
    return do_slightly_less_stuff
  end
end
```

