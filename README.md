# Python Language Guide

## What is this doc?

I'm planning on refreshing (and expanding) my Python knowledge so this document is a way to take track my knowledge journey in a way that could be useful to students.

## Installation and Environment Basics

### Installation

This is beyond the scope of this document but I used homebrew on WSL Ubuntu.

`brew install python@3.10`

and then in my `~/.zshrc` added

```sh
alias python='python3.10'
alias pip='pip3.10'
```

so I can just type in `python` or `pip` later to get at the full command.

### REPL vs source file

Calling the command `python` without any arguments will launch the REPL (Read, Evaluate, Print, Loop). This is useful for testing things out but long term we are going to want to be able to run python code from a saved source file.

If you want to a saved python file name it something like `playground.py` and then from the command line execute it with `python playground.py`.

> Note: the `.py` extension (like all extensions) are a convention for the OS to know what to do with the file by default. So naming the file `playground` and executing it with `python playground` works just as well.

> We are going to use code examples throughout this document. By convention, if an expression is preceded by `>>>` that means we are in the REPL, and the following line will be the output. Otherwise, we are using a code example as if in source code.

### REPL + source file (advanced)

Before we move on, sometimes you want to load a source file into the REPL so you can more easily play around with it. This falls apart once you are inside a larger project with tons of dependencies (Django) but it's still useful to know.

If you know you want to do this before you are in the REPL, you simply add the `-i` (interactive) flag like so: `python -i playground.py` and it will start the REPL, run the file, print the output to the REPL, and then work like a normal REPL but any variables or functions you declared should now be accessible.

If you are already in the file, you can import the file with a standard import statement. So assuming we executed `python` from a directory with a file `playground.py` in it, we can load it in after the fact with `import playground`.

Simple enough, but what if you then make changes to `playground.py` and want your REPL to reflect those updates? Actually a little complicated, but it's:

```py
>>> import importlibs # importlibs is a module that has helper functions for this purpose
>>> importlibs.reload(playground) # playground is what you named the module the first time
```

## Numbers

Ok, we are starting to write actual code! It's always good to rememeber, computers are essentially calculators - it might seem like they are more powerful, but at their core they simply manipulate numbers (really, just 0s and 1s when you get down to the CPU) and so learning how your programming language of choice handles arithmetic is always a good place to start.

Open the REPL and type `10` and hit enter. You'll see `10` spit back at you from the REPL. This is a (minimal) valid program! This is a good time to introduce the concept of 'primitive' data types. Some data types are so core to the language that executing them simply returns the value exactly as you input it. This might seem useless, but it's a sign we hit the most essential bit - primitive data types are primitive because they are irreducable, they are in their simplest, final form as far as Python is concerned.

> The `type()` function is useful to mention here. `type()` takes a value as an input and returns some info about that type. So in this case `type(10)` returns `<class 'int'>`. We won't get to classes for a long time, but virtually everything in Python is ultimately implemented as a class, and in this case `10` is of class `int`, i.e. integer.

### Arithmetic

Python supports all the operations you would expect a basic calculator to, and some more you might not expect. They are:

- Addition: `10 + 2` (= 12)
- Subtraction: `10 - 2` (= 8)
- Multiplication: `10 * 2` (= 20)
- Division: `10 / 2` (= 5.0)

That last one is a bit more complex than it might seem at first blush. Division in Python is, be default, 'floating point' division, i.e. 'real' division as you are used to it in math. This isn't always the case in languages, so it's worth paying special attention to. First, note the output, `5.0`, not `5`. What the difference? `type()` could help us:

```py
>>> type(5)
<class 'int'>
>>> type(5.0)
<class 'float'>
```

Okay, that's a new primitive, `float`! What the hell is a `float`? A float is a number, as you normally think of it. Computers tend to work best with integers (i.e. no decimal) and implementing numbers with a decimal turns out to be a relatively complicated topic! It's far too advanced to go into detail but the solution was initially called 'floating point' numbers, and the number stuck. In some languages (like Java) you will more commonly see the type `double` which is the same thing, it jsut means a 'double precision floating point number'. For better or worse you will see this a lot in Computer Science/Software Development in general - things get names that stick but can seem strange to the uninitiated.

- [Floating Point Arithmetic wiki article](https://en.wikipedia.org/wiki/Floating-point_arithmetic#IEEE_754:_floating_point_in_modern_computers)

Ok, we still have a few more essential arithmetic operators to go into:

- Integer (or 'floor') Division: `10 // 2` (= 5)

So named because it 'floors' (rounds down) any float to it's int equivalent. So `8 / 3` = 2.6666666666666665 (approximately) while `8 // 3` = 2. This might not seem useful at first blush but computers tend to work with integers much more efficiently, so if you ever don't care about that decimal component, it's better to use floor division.

> Note: I said `8 / 3` = 2.6666666666666665, but is that really so? No, not exactly. This is why decimals are complicated to represent with computers, technically that should be 2.666666.... and 6's forever. But the computer can't represent infinite precision, so it makes a compormise and rounds off at a certain point. This issue is called `integer underflow`. It's not something you will generally encounter or care about, but it's worth knowing that floating point arithmetic is _always_ an approximation, and places that truly care about precision, ie any financial institution, have to go out of their way to understand and avoid this problem (usually using a class named something like `BigNumber`)

- Exponentiation: `2 ** 4` (= 16)

This isn't syntax you are used to, but Python supports raising a number to another number, even if both are floats, which is useful sometimes.

- Modulus: `8 % 5` (= 3)

You have likely never seen this operator before, but it ends up being very useful for one thing you use all the time in software development (which we will get to soon), so it's built right in. Modulus is like the inverse of floor division - instead of cutting out the decimal, it tells you what wasn't 'captured' by the division. So using the above example, how many 5's are there in 8? 1 (ie 8 // 5 = 1). What's left over when we've taken all the 5's we can from 8? 3. This may seem useless, but it turns out it's by far the most efficient way a computer can determine if a number is cleanly divisible by another. So the common pattern we will see is to check if some value, like the index of an array, is divisible by 2. This way, we will only do the given operation on every _other_ element. This involves some concepts we haven't gotten to yet, but it would look like:

```py
arr = [1, 2, 3, 4]

for index in range(len(arr)):
    if index % 2 == 0:
        print(arr[index])
```

## Variables

A programming language isn't much more powerful than a calculator until we hit the topic of variables. Because it's good to get used to error messages, and see them as your friend, not an indication of faliure, let's try putting an undefined variable into the Python REPL:

```py
>>> result
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'result' is not defined
```

This is a pretty helpful error message, it doesn't know what result is! So let's define it first.

```py
>>> result = 1
>>> result
1
```

A variable is an incredibly simple but powerful idea. We can define names (or 'indetifiers') that can store some value, to be invoked and retrieved later.

### Re-assigning

Python let's you re-assign a variable's value without much fuss, so a variable's value is, well, variable!

```py
>>> x = 1
>>> x
1
>>> x = 2
>>> x
2
```

This is a good or bad thing depending on who you ask. It is simple enough to change, but it means that the value of `x` is determined by the context in which it is run, so the same variable name doesn't equal the same thing depending on what ran before it.

### Naming conventions

The essential rules of variables in Python say:

1. it must start with a letter (no numbers or special characters)
2. some symbols are off limits (like `-`)
3. a variable is a single lexical unit, ie no spaces!

These rules mostly exist so the Python interpreter can reasonably determine that, for example, `my-number` is a single varaible and not the expresion `my - number` (two variables, combined with an operation). Obviously you can see why whitespace in a variable name would break this contract majorly.

There is an additional convention in Python, sometimes called an 'idiom', i.e. a convention that is adopted because it makes your code more reasonable. In Python we use 'snake case' by default for variable names. Technically `ReSULt1` is a perfectly fine variable name, but by convention, for a varaible, we would prefer `result_1`, all lowercase and a `_` used to seperate words. Just so you know, common conventions are:

- flat case (mumble case), `iamaveryhardtoreadvariablename`
- kebab case, `i-am-held-together-like-a-kebab` (not valid in Python but is in other languages)
- camelCase, `thisVariableHasHumps`. Note the lowercase `this` that starts it off. This is the common naming convention for variables in JavaScript.
- PascalCase (cap case), `ThisIsSimilarToCamelButWithAHumpUpfront`. Pascal was the name of an old language that started this convention. In Python usually reserved for the name of a class.
- snake case, `python_prefers_this`. It slithers on the floor and eschews caps.
- upper case, (scream case) `THIS_IS_MY_NORMAL_SPEAKING_VOLUME`. Seems insane, but this is a common case for 'constants', or the handful of variables that should never change but determine how your software runs. They are usually defined at the top of the file and are a call-out to anyone editing your file that THIS_IS_IMPORTANT.

> Remember when we spoke about re-assignment above? Because Python doesn't have any built-in concept of a variable being a 'constant', we would indicate that a variable is meant to be a constant with a variable name like `TOTAL_SIZE`. This is just convention though, there is nothing in the language that enforces it.

> An aside: naming your variables well in incredibly important. It might seem trivial right now, but variable names are ultimately your highest form of abstraction in a programming language and can make the difference between someone new to your code understanding it or not. Especially once you are on a team and a real project, variable named like `x` or `i` or `data` are horribly non-descriptive. At the opposite end of the spectrum, variable names like `InternalFrameInternalFrameTitlePaneInternalFrameTitlePaneMaximizeButtonWindowNotFocusedState` (actual example from a real API) are so verbose they become difficult to work with. You want to find a comfortable middle ground, but in my opinion this is what makes the difference between a good and great software developer, and I tend to agonize over great varaible names when working on real world team-based projects.

## Text (Strings)

Text in a programming language is usually manipulated and worked with using a primitive data type known as a `string`.

```py
>>> text = "hello world"
>>> print(text)
hello world
type(text)
<class 'str'>
```

Python supports both `"` and `'` as text delimiters. They are interchangable (as long as matching) but the reasoning is it makes nesting one in the other easier. So if you had text with a quote it could look like:

`'Thus spoke Zarathustra: "hello world"'`

### String concatenation

Concatenation is a fancy word for 'string addition'. Python uses the `+` operator. Technically this is called 'operator overloading' - the operator is doing something different (calling a different function) based on the types of the inputs. So:

```py
>>> 'h' + 'ello'
'hello'
>>> 'string delimiters ' + "can be mixed"
'string delimiters can be mixed'
>>> 1 + 4 # regular ol' addition
5
>>> 'a' + 1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can only concatenate str (not "int") to str
```

That last one is to show that Python at least gives meaningful error messages when mixing and matching types in a less than clear way. By comparison, in JavaScript `1 + 'a'` evaluates to `1a` :/

### Multiline strings

Python also supports multiline strings, delimited with `"""`, so you could quote Little Wayne like so:

```
"""Paper chasin', tell that paper
"Look, I'm right behind ya"
Real Gs move in silence like lasagna
People say I'm borderline crazy, sorta kinda"""
```

Note that this method can handle both `'` or `"` as regular characters.

### Escaping

You will not need to use this much hopefully, but notice what happens when you print out the above:

```py
>>> lyrics = """Paper chasin', tell that paper
"Look, I'm right behind ya"
Real Gs move in silence like lasagna
People say I'm borderline crazy, sorta kinda"""
>>> lyrics
'Young Money, Cash Money\nPaper chasin\', tell that paper, "Look, I\'m right behind ya"\nBitch, real Gs move in silence like lasagna\nPeople say I\'m borderline crazy, sorta kinda'
```

That's a lot messier looking, what are all those `\`'s? Those are called escape characters. Strings at their core really just use `'` as the delimiter, and any other character is treated as a character. But what if you wanted `'` itself as a character in that string? You 'escape' it with `\'`. I don't know why they are called 'escape' characters, but they are mostly commonly used to represent characters that would otherwise be difficult (or impossible) to represent, so `\n` means newline, `\t` means tab and `\\` means print a literal `\`, ie we are escaping the escape character.

### String operations

Strings are implemented as a class under the hood, which means they support a lot of 'methods', functions defined on a specific class and accessible by instances of that class. So for instance, if we wanted to transform a string to it's uppercase equivalent we could write:

```py
>>> my_str = "hello world"
>>> my_str.upper()
'HELLO WORLD'
```

Other useful string methods include:

```py
>>> my_str = "hello world"
>>> my_str.replace("hello", "goodbye")
'goodbye world'
>>> My_str.split()
['hello', 'world']
```

There are many more!

> Note: all of these functions are taking a string as input, but not modifying it 'in-place', rather they are returning a brand new string (or other data type) as the result. This is necessary because strings in Python are _immutable_, once created they cannot be directly altered.

### Chaining Calls

Part of what makes the above useful is a pattern called 'call chaining'. Because each call returns a brand new output, we can chain calls so we don't need to store thing sin temporary variables.

```py
>>> my_str = "hello world"
>>> my_str.replace("hello", "goodbye").split()
['goodbye', 'world']
```

### Slices

Python supports a built-in way to easily reference a substring within a string.

```py
>>> my_str = 'hello world'
>>> my_str[0]
'h'
>>> my_str[10]
'd'
>>> my_str[len(my_str)-1]
'd'
>>> my_str[-1]
'd'
```

You can access an individual character with `my_str[<index>]`. It can get cumbersome to say 'the last element' so Python slices let you count backwards as well, so `my_str[-1]` is the equivalent of `my_str[len(my_str)-1]`.

The full API for this feature is `my_str[<start>:<stop>:<step_size>]` with reasonably set defaults when a value is omitted. So:

```py
>>> my_str = 'hello world'
>>> my_str[0:4] # <start> is 'inclusive', <stop> is 'up until'
'hell'
>>> my_str[0::2] # from beginning till the end, <step> of 2
'hlowrd'
>>> my_str[::-1] # reverses the string
'dlrow olleh'
```

> As per the immutable idea above, try typing in `my_str[0] = 'H'` - it's a no go!

### String formatting

Sometimes you want to print a variables value within a string. The basic way of doing this is with concatenation, like so:

```py
>>> value = 42
>>> "The value we care about is " + str(value) + ", which I am sure you are pumped about!"
'The value we care about is 42, which I am sure you are pumped about!'
```

A little cumbersome, notice how we have to account for whitespace in one of the strings and we need to call the `str()` function to make a string representation out of the integer that is held by `value`.

The modern convenient way is string formatting (or 'string interpolation')

```py
>>> value = 42
>>> f"The value we care about is {value}, which I am sure you are pumped about!"
'The value we care about is 42, which I am sure you are pumped about!'
```

Okay, same thing, a little neater, that's all. Notice that the interpolation portion could take any expression that evaluates to a meaningful value.

```py
>>> value = 42
>>> f"The value we care about is {value ** 2}, which I am sure you are pumped about!"
'The value we care about is 1764, which I am sure you are pumped about!'
```

There's even a cool convenience aspect of this feature purely for debugging. Often you simply want to print a varaible's value with it's associated name, as a label. Normally you might write:

```py
>>> name = "Ben"
>>> age = 35.32
>>> print("name= " + name + ", age= " + str(age))
name= Ben, age= 35.32
```

But now you can just write

```py
>>> name = "Ben"
>>> age = 35.32
>>> print(f"{name=}, {age=}")
name= Ben, age= 35.32
```

Same result!

## `print()`

We have discussed this a bit already but `print()` is going to be your way of printing to the console, assuming you are _not_ in the REPL (mostly useless in the REPL, as the 'P' in REPL is doing that every time a new value is evaluated). `print()` is a function like any other, so you call it with parameters.

```py
print('Some text') # prints 'Some text'
print(1) # prints '1' (note it converted it to a str implicitly)
print('A tab:\t and some text') # prints 'A tab:   and some text' (escapes are evaluated, not returned raw)
print() # even no input works, prints a new line
print('I am', 'whatever', 'you say I', 'am', 42) # takes many arguments, puts a space between each
```

Look at that last example, how many parameters does it take? This is an advanced idea for implementing yourself, but it's worth knowing about to understand how print works. This is called a 'variadac' function - it takes as many arguments as you give it, and then references them by position (instead of by name).

## Booleans + conditionals

We are still just at primitives, but at the end! The classic primitives almost every language supports are:

1. numbers (ints, floats, sometimes as one type, sometimes many)
2. strings (sometimes a single character (char) is it's own type as well, not in Python)
3. booleans

Boolean is a data type with exactly two possible values: `True` or `False`.

```py
>>> type(True)
<class 'bool'>
>>> type(False)
<class 'bool'>
```

This is as simple as it gets but a necessary component for working with _conditionals_. Conditionals are language constructs that do one thing or another on the basis of an expression (condition) evaluating to True or False.

```py
door_is_locked = True
if door_is_locked:
  print("Mum, open the door!")
```

If we ran this the print statement would run. Note the whitespace in front of our `print` statement, Python uses whitespace to establish nesting. I personally don't love this approach, I prefer the explicitness of using braces `{ ... }` in other languages. But for instance:

```py
door_is_locked = True
if door_is_locked:
  print("Mum, open the door!")
  print("I really mean it!")
```

is not identical to:

```py
door_is_locked = True
if door_is_locked:
  print("Mum, open the door!")
print("I really mean it!")
```

In the first instance, both statements only run if the boolean is True. In the second, only the first statement is 'nested' within the if statment, the second print is executed always (it is back in the general scope, not the if statements scope).

### `Else`, `Elif`

If statements are usually not just `if` but `if/else`. So for example:

```py
door_is_locked = False
if door_is_locked:
  print("Mum, open the door!")
else:
  print("I can use the back door which is never locked!")
```

This if statement would evaluate `door_is_locked`, see it is False, skip the the `'if` block and jump to the `else` block.

What if you have more than just two cases? That's where `elif` comes i, short for `else if`

```py
my_val = 42

if (my_val == 1):
    print("oh it's 1")
elif (my_val < 33):
    print("well it's less than 33!")
else:
    print(f"Ok, is it {my_val}")
```

Note that this works top to bottom, the moment a given if/elif conditional is True that block executes and the rest are ignored.

### Comparison operators

Just like with arithmetic, booleans use operators, and the resulting logic is known as boolean arithmetic. Just like arithmetic there is an order of evaluation to consider, but otherwise these are ideas you implicitly understand:

```py
>>> 2 > 1
True
>>> 2 < 1
False
>>> 2 < 3 < 4 < 5 < 6
True
>>> 2 < 3 > 2
True
>>> 3 <= 3
True
>>> 3 >= 2
True
>>> 2 == 2
True
>>> 4 != 5
True
>>> 'a' == 'a'
True
>>> 'a' > 'b'
False
```

Note that when comparing boolean values directly, Python has unique operators: `and`, `or`, `not`.

```py
>>> not True
False
>>> not False
True
>>> True and True
True
>>> True and False
False
```

### Ternary operator

Python also supports something called a 'ternary' operator, something like an inline if/else statement. Imagine you wanted to set a variable based on a conditional. With what we currently know we could write something like:

```py
a, b, smaller = 10, 20, -1 # this is shorthand to assign multiple varaibles in one line

if a < b:
  smaller = a
else:
  smaller = b

print(smaller) # 10
```

Okay, this works, but there's a few issues. One, we had to set a dummy starter value for 'smaller' so it held something to start with. Second, if/else statements are just that, _statements_, i.e. they _do_ something, but they evaluate to _nothing_.

A ternary expression by comparison is closer to an arithemtic operator, it evaluates to something, but on it's own does' _do_ anything.

```py
a, b = 10, 20

min = a if a < b else b

print(min)
```

`a if a < b else b` is the ternary expression. It _evaluates_ to one thing or another depending on the conditional, and then the wider statement stores it in a new variable `min`. The ternary operator comes in handy when you want to evaluate some expression one of two ways, and then store it, where an if/else statement is more appropriate when you want to execute one block of code or another depending on the conditional. But to be clear, ternary is just convenient, if/else can handle it's job, whereas ternary could not do everything if/else does.

### `match/case` statement

This is new in Python 3.10, but other languages have an equivalent, also sometimes known as a `switch` statement. It works just like an if/else but is cleaner when you have multiple options. So instead of writing something like:

```py
lang = input("What's your favorite programming language?")

if lang == "Python":
  print("You are in this class")
elif lang == "JavaScript":
  print("You are a glutton for punishment, but cool")
elif lang == "Java":
  print("I dislike you on principal")
elif lang == "TypeScript":
  print("Ah, we have a sophisticated gentleman/lady in our midst")
elif lang == "Haskell":
  print("After my own heart")
else:
  print("I mean, you do you")
```

we can transform it into something a bit more readable with:

```py
lang = input("What's your favorite programming language?")

match lang:
  case "Python":
    print("You are in this class")
  case "JavaScript":
    print("You are a glutton for punishment, but cool")
  case "Java":
    print("I dislike you on principal")
  case "TypeScript":
    print("Ah, we have a sophisticated gentleman/lady in our midst")
  case "Haskell":
    print("After my own heart")
  case _:
    print("I mean, you do you")
```

Once again, this is nothing an if/else statement can't do, but it tends to be a cleaner approach when you are explicitly matching a given value against a set of possible matches, and the full match/case syntax supports a fair amount of logic in those 'case' conditionals.

> Note that last case - it's a catch all, equivalent to the else. `_` means 'anything' or 'wildcard' and you will see it in other contexts.

## Loops

Ok, so we have done conditionals, now it's time for loops. Fun fact: all a language needed is conditionals and loops to be Turing-Complete, i.e. it can compute anything computable! The real definition is a little more involved than that, but it's worth knowing that this is your bread and butter, conditionals and loops, and it's also why languages like HTML or CSS are sometimes sneered at as 'not real languages' - they are indeed real languages, and useful for their purpose, but they aren't Turing-Complete, so somethings just aren't possible in them (and this was a design decision, HTML/CSS is merely for representing data, not performing computation).

### `for/in`

Our equivalent of the if/else statment (the contruct all others are ultimately built on top of) is the humble `for/in` loop.

```py
for letter in 'Hello':
  print(letter)

m_ylist = [1, 'a', 'Hello']
for item in my_list:
  print(item)
```

This is our first time seeing a list, which is a data structure we will go in more depth on soon. All you need to know for now is it is a collection of items (which do not all need to be the same type) and it is _iterable_, meaning it has a structure that can be walked through from beginning to end in a predictable order.

### `while`

A while loop is like a for loop but instead of walking through an iterable object it continues running as long as it's condition is true (it quits the first time it's false). So if we wanted to write our above for/in loop with a while we could write:

```py
index = 0
my_list = [1, 'a', 'Hello']

while index < len(my_list):
    print(my_list[index])
    index += 1
```

Some new ideas here. First, we are 'indexing' a list by it's value, so just like with a string `my_list[0]` would grab the first element from that list. Second, we are updating the index with a `+=` operator. This is just shorthand for `index = index + 1` and equivalents exist for the other operators, but most ppl will only ever use `+=` and `-=` to increment or decrement by 1. Note that this works but the downside is we needed to make that index variable on the outside. So prefer for loops when you can. One case that is perfect for `while` is creating a (purposeful) infinite loop.

```py
while True:
    response = input("Gimme a string: ")
    print(f"you said '{response}'")

    match response:
        case "hello world":
            print("how predictable")
        case "quit":
            print("you are released from my infinite game of echo!")
            break
        case _:
            print("That's cool, so ...")
```

This is a simple command line program that will run forever until the phrase 'quit' is input. Note the new keyword `break`. `break` is a command that will break you out of the current loop, and it exists in for loops as well. The only thing to be careful about is nesting, break statements can break you out of the current loop, but you have to rely on tricks if you want to break out of a nested loop.

There's also the `continue` keyword. It's like break, but only ends the current iteration of the loop, and continues onto the next. So if we wanted to write a for loop that only looked at every other element we could write:

```py
my_list = [4, 9, 'hello', True]

for index in range(len(my_list)):
    if index % 2 == 0:
        print(my_list[index])
    else:
        continue
```

This is a contrived example (it would work the same if we simply had no else statement) but compare the output to using `break` where we used `continue`? In that case we would print the first element only, in this case we will print every other element, and skip the others. `continue` is useful when you have determined a given iteration of the loop is unneccessary and want to continue and ignore the rest of the logic in the loop.
