# Python-Style-Guide
This is a brief summarization of general programming principles and PEP8 Python specific principles.

This document was originally written for data scientists without formal programming education. As an R&D manager I wanted each teammate to have a basic programming intuition combined with Python best practices. Altough there are numerous resources about SOLID and pragmattic programming as well as comprehensive guideline for Python specficially (see https://peps.python.org/pep-0008/), I wanted to create a unified concise practical guide.

## 1. Introduction
This document is divided into two sub-guides:

Programming constitution - contains thumb rules that actually affect program efficiency, correctness, or reusability. This part is also divided into two: the first one is unilingual, describing rules and tips which are relevant to any programming language, while the other part describes python specific rules and tips.

Python programming conventions - conventions that don't affect program correctness explicitly, but rather increase readability. This part is completely pythonic.

At the bottom of this document, there are several open-source libraries that automatically check python scripts according to all specified rules.

## 2. Programming Constitution

### 2.1. General rules
Programming books are probably the longest books ever written. There are so many tips and tricks, design patterns, do’s and don'ts so we couldn’t really cover all. However, there are basic rules which can be described concisely and can guide us safely through the development process:

#### 2.1.1. The SOLID principles
The SOLID principles were originally written for object-oriented programming. However, they make great rules to follow in every large-scale development. Those are relatively high-level tips:

**Single Responsibility Principle (SRP)**

A software entity (whether it’s a module/class/method/code bulk) should have one and only a single responsibility. Why? Coupling two responsibilities together won’t allow us to use only one of them. Breaking this rule will probably make the code messy. It’s harder to debug code that does multiple things.

**Open/Closed Principle**

A software entity (whether it’s a module/class/method/code bulk) should be open for extensions but closed for modification. This rule has two perspectives:

First, when you are writing an entity (again, whether it’s …) - be aware of any use it should satisfy and write it accordingly. Assume that no modifications shall be done to this entity after you’re done. It’s not true usually but it's a good practice.

Second, write your entity as modular as you can. Assume that you aren’t aware of all possible uses of this entity. Therefore, you should design it as generic as possible. For instance, you need some function that for a given ID number, returns the number of times it occurs in the database over the past month. Then, you should write this function more generically, taking a “time range” as an argument and optionally a list of ID numbers.

**Liskov Substitution Principle**

Objects in a program should be replicable with instances of their subtypes without altering the program.

Each time you create an object or do some complicated operation in your code, ask yourself if there’s a chance that in the future these parts of code will be replaced. If so, it should be implemented as an interface with some subclass that satisfies your current need. This rule holds for I/O from DB, building features, training models, computing metrics, and for much more low-level details that you may find.

**Interfaces Segregation Principle**

Client-specific interfaces are better than one general-purpose interface. It can be tempting to implement an almighty interface that satisfies all of your project needs. However, it will probably make it coupled to your project. Each other code that will use it, will probably just ignore most methods. It is preferable to build several modular interfaces.

**Dependency Inversion Principle**

High-level modules shouldn’t depend on low-level modules, but rather on abstractions. Similarly, low-level modules should not depend on their usage. This means high-level modules and low-level modules shall communicate via an abstraction. For example, if you develop a module that evaluates a machine learning model, it shouldn’t care about the model specifics, but only about the model's general interface (fit, predict, etc.).

#### 2.1.2. Some other thumb rules

**Don’t Repeat Yourself (DRY)** 

If you find yourself writing the same thing more than once, you should extract it to a method. It applies to code but also for documentation and any other piece of knowledge.

**Orthogonality**

Minimize the number of modules that need to be changed if a particular functional requirement is changed (new feature, change feature, new ML algorithm). How?

* Build an abstract interface for everything that might be changed.
* Write shy code - don’t connect modules unnecessarily.
* Avoid global data. Instead, each module should get its context as an input.
* Write your code top-down! - It has so many advantages. Mainly, your high-level code raises requirements for your low-level code. Development becomes more fluent.

**Debug Like a Pro - How to Debug?**

* Step 1: Does the code compile?
* Step 2: Does the code pass the existing tests? If not, take the failed test and go to step 5.
* Step 3: Gather all information about your bug - when does it happen? With which parameters? In which stack trace?
* Step 4: Isolate - write a minimal test that reproduces your bug.
* Step 5: Debug.

**Early failure**

Any assumptions upon which your code is based should be verified as soon as possible, and be reported if violated.

**Dependency injection**

Inject your module’s dependencies into it rather than creating them inside the module. It increases testability and orthogonality.

**Configuration**

Put anything you can in the configuration file. It’s a great habit and it’ll make it easier to change your code behavior later on.

**The law of diameter**

Any method of an object should call only methods belonging to:
* The object.
* The parameters of the method.
* Any object the method created.
* Attributes of the object.

**Avoid Temporal Coupling**

Temporal coupling is not an explicit coupling. It occurs when method A must be called before method B although they aren’t programmatically connected. How you avoid this?
* Make both methods private and create a public parent method that encapsulates their temporal relation.
* As a thumb rule - make your code as concurrent as possible.

**Do not rely on code - that doesn’t expect you to rely on it**

It might be changed and hunt you back.

**Write Unit Tests**

You should test your module on rejected values, boundary values, accepted values. Advantages:
* It provides examples of how to use all your module functionality.
* It enables refactoring without worrying about breaking code.

Coverage tools are great to validate this principle.

**Decoupling**

Every module should know as few as possible about others modules. For instance, don’t write code line like: `a.b.c.d.e()`. More generally, make sure your code flow is simple and makes sense. It should be clear and flat as possible. Try to avoid loops if you can.

**Reversibility**

Do not make critical implementation decisions as long as it’s possible. This principle is also called **YAGNI** - “you’re not gonna need it”.

### 2.2. Python Specifics

* Comparisons to singletons like `None` should always be done with `is` or `is not`, never the equality operators.
* Always prefer a `def` statement instead of an assignment statement that binds a lambda expression directly to an identifier. That way, the function has a specific name which makes debugging easier.
* Derive Exceptions from the `Exception` Python interface.
* Do not use mutable objects (such as `list` and `dictionary`) as default values in a function definition.
* For all try/except clauses, limit the try clause to the absolute minimum amount of code necessary. This avoids bugs masking where the program catch a bug it didn't expect to catch and still moves on.
* When a resource is local to a particular section of code, use a `with` statement to ensure it is cleaned up promptly and reliably after use.
* Use Python built-in string methods instead of `+` and `+=` operators. They’re much more efficient.
* If you need to assign something (for instance, in unpacking) but you will not need that variable, use _.
* Use `Set` instead of `List` whenever you can. Most of the times lists are used to store values and then to be sampled. It costs O(n) each time, while Sets are actually built upon hash sets and will do it with O(1). Dictionaries can be also a good idea in this case.
* For sequences (strings, lists, tuples), use the fact that empty sequences are false, namely: `if seq:` and `if not seq:` are preffered over `if len(seq):` and `if not len(seq):` respectively.
* Prefer list comprehension syntax over maps and filters. Prefer them over for and while loops as much as possible.
* Don’t use @staticmethod unless you are forced to (in order to integrate with an API defined in an existing library). Write a module-level function instead.
* Use @classmethod only when writing a named constructor or a class-specific routine that modifies necessary global state such as a process-wide cache.
If a class inherits from no other base classes, explicitly inherit from Object.
* Avoid using the `+` and `+=` operators to accumulate a string within a loop. Since strings are immutable, this creates unnecessary temporary objects and results in quadratic rather than linear running time. Instead, add each substring to a list and ''.join the list after the loop terminates.

## 3. Python Code Conventions

### 3.1. Why Conventions?

This part of the guide is based on the formal PEP-8 guide, written by Python founders. Code conventions help us make our code more readable for ourselves and for others. The main rule of thumb here is that our code is going to be read way more times than it will be written. Thus, we should make it as clear as possible and reduce the amount of time that a new programmer would invest in understanding it.

The Zen of Python describes it more colorfully. You can see this by running “import this” in Python itself:

* Beautiful is better than ugly.
* Explicit is better than implicit.
* Simple is better than complex.
* Complex is better than complicated.
* Flat is better than nested.
* Sparse is better than dense. 
* Readability counts. 
* Special cases aren't special enough to break the rules. 
* Although practicality beats purity.
* Errors should never pass silently. 
* Unless explicitly silenced.
* In the face of ambiguity, refuse the temptation to guess.
* There should be one-- and preferably only one --obvious way to do it.
* Although that way may not be obvious at first unless you're Dutch.
* Now is better than never.
* Although never is often better than *right* now.
* If the implementation is hard to explain, it's a bad idea.
* If the implementation is easy to explain, it may be a good idea.

### 3.2. Documentation Strings (docstrings)

A docstring is a string literal that occurs as the first statement in a module, function, class, or method definition. Such a docstring becomes the ` __doc__ ` special attribute of that object. There are [several](https://stackoverflow.com/questions/3898572/what-are-the-most-common-python-docstring-formats) different docstrings styles out there, but most of the data science industry use the [“numpy”](https://numpydoc.readthedocs.io/en/latest/format.html) style.
 
#### 3.2.1. Modules

The docstring for a module should generally list the classes, exceptions, and functions (and any other objects) that are exported by the module.

TODO: add an example

#### 3.2.2. Classes

The docstring for a class should summarize its behavior and list the public methods and instance variables. If the class is intended to be subclassed and has an additional interface for subclasses, this interface should be listed separately (in the docstring).

If your class has public attributes, they should be documented in an attributes section inside the class docstring.

TODO: add an example

#### 3.2.3. Functions

The docstring for a function / method should summarize its behavior and document its arguments, return value(s), side effects, exceptions raised, and restrictions on when it can be called (all if applicable). Optional arguments should be indicated. It should be documented whether keyword arguments are part of the interface.

After you’ve done writing your method, put a blank line after the function definition. Type three quotation marks and press the “enter” button. It will automatically generate a docstring skeleton, including the methods parameters and return values.

Example:
```
def some_function(param1, param2=None):
    """This is an example of a module level function.

    Function parameters should be documented in the ``Parameters`` section.
    The name of each parameter is required. The type and description of each
    parameter is optional, but should be included if not obvious.

    The format for a parameter is::

        name : type
            description

            The description may span multiple lines. Following lines
            should be indented to match the first line of the description.
            The ": type" is optional.

            Multiple paragraphs are supported in parameter
            descriptions.

    Parameters
    ----------
    param1 : int
        The first parameter.
    param2 : :obj:`str`, optional
        The second parameter.

    Returns
    -------
    bool
        True if successful, False otherwise.

        The return type is not optional. The ``Returns`` section may span
        multiple lines and paragraphs. Following lines should be indented to
        match the first line of the description.

        The ``Returns`` section supports any reStructuredText formatting,
        including literal blocks::

            {
                'param1': param1,
                'param2': param2
            }

    Raises
    ------
    ValueError
        If `param2` is equal to `param1`.
    """
    if param1 == param2:
        raise ValueError('param1 may not be equal to param2')
    return True
```
[source](https://sphinxcontrib-napoleon.readthedocs.io/en/latest/example_numpy.html#example-numpy-style-python-docstrings)

### 3.3. Exceptions

All code should catch any exceptions raised by called functions, and either handle the exception, re-raise the exception, or wrap the exception in an allowed exception type and re-raise it.

Document exceptions raised by public functions. The docstring should say what type of exception is raised and under what conditions.
Minimize the amount of code in a try/except block. The larger the body of the try, the more likely an exception will be raised by a line of code that you didn't expect to raise an exception. In those cases, the try/except block hides a real error.

### 3.4. Naming conventions

As we can see, exceptions should follow the CapWords convention, but preferably with an “Error” suffix.

### 3.5. Imports

Separate imports to different lines (means don’t do “import os, sys”. Separate them). 
Imports should be grouped in the following order:

* Standard library imports.
* Related third-party imports.
* Local application/library-specific imports.

Put a blank line between those groups of imports.

In a different note, write: `from foo.bar.yourclass import YourClass` instead of: `import foo.bar.yourclass`. Also, avoid wildcards imports such as: 
`from <module> import *`

Future imports should be placed first:
`from __future__ import barry_as_FLUFL`

### 3.6. Indentations

First thing, with spaces only (no tabs). Good IDE will make this choice meaningless and you could use whatever you'd like. Make sure your editor is compatible with the automatic conversion of tabs to spaces. Make indentations semantically reasonable. For example:

```
foo = long_function_name(var_one, var_two, 
                         var_three, var_four)
```                     

Here, we indent the second line of parameters to align with the first one. Following that rule, we would like in general to set different semantic code sections with different indentations.

### 3.7. Closing Brackets

The closing brace/bracket/parenthesis on multiline constructs should have a line of its own:

```
my_list = [
 	    1, 2, 3,
	    4, 5, 6,
	]
```

### 3.8. Functions Length

Prefer small and focused functions. We recognize that long functions are sometimes appropriate, so no hard limit is placed on function length. If a function exceeds about 10 lines, think about whether it can be broken up without overcomplicating the code. Even if your long function works perfectly now, someone modifying it in a few months may add a new behavior. This could result in bugs that are hard to find. Keeping your functions short and simple makes it easier for other people to read and modify your code. It also makes the corresponding unit tests more modular and thus more helpful in bugs isolation.

You could find long and complicated functions when working with some code. Do not be intimidated by modifying existing code: if working with such function proves to be difficult, you find that errors are hard to debug, or you want to use a piece of it in several different contexts, consider breaking up the function into smaller and more manageable pieces.

### 3.9. Line length

Limit all lines to a maximum number of 79 characters. Limiting the required editor window width makes it possible to have several files open side-by-side, and works well when using code review tools that present the two versions in adjacent columns.

The preferred way of wrapping long lines is by using Python's implied line continuation inside parentheses, brackets, and braces. If it turns out to be unreadable then we’ll use backslashes at the end of each wrapped line. For example:

Bad:
```
my_very_big_string = """For a long time I used to go to bed early. Sometimes, \
    when I had put out my candle, my eyes would close so quickly that I had not even \
    time to say “I’m going to sleep.”"""
```

Good:
```
my_very_big_string = (
    "For a long time I used to go to bed early. Sometimes, "
    "when I had put out my candle, my eyes would close so quickly "
    "that I had not even time to say “I’m going to sleep.”"
)
```

### 3.10. Line spacing

Use it reasonably to separate between different logical parts of your code. Put blank lines between methods implementations, inside methods, or wherever you find it useful. However, if you find yourself putting blank lines within a method, you probably should extract those different code blocks to submethods.

### 3.11. Trailing Commas

Use when the data structure is expected to be expanded. For example:
```
FILES = [
  'Setup.cfg',
  'Tox.ini',
]
```
It’s better to put each file path in a different line because then it’s easier to track it on the version control system.

### 3.12. String Formatting

Don’t use the old %s style string formatting, e.g. `"i am a %s" % sub`. This kind of string formatting is not helpful for internationalization.

Use `.format()` method instead, and give meaningful names to each replacement field, for example:

`_(' ... {foo} ... {bar} ...').format(foo='foo-value', bar='bar-value')`

### 3.13. Explicit code

Be explicit. Don’t make your code ambiguous.

Bad:
```
def make_complex(*args):
    x, y = args
    return dict(**locals())
```

Good:
```
def make_complex(x, y):
    return {'x': x, 'y': y}
```

## 4. Sources

* https://www.python.org/dev/peps/pep-0008/
* https://docs.ckan.org/en/2.8/contributing/python.html
* https://www.python.org/dev/peps/pep-0257/
* https://docs.python-guide.org/writing/style/
* https://github.com/google/styleguide/blob/gh-pages/pyguide.md
* https://www.datacamp.com/community/tutorials/pep8-tutorial-python-code

## 5. Automatic code conventions checkers

* https://pypi.org/project/pep8/ - python style guide checker
* https://pypi.org/project/autopep8/ - autopep8 automatically formats Python code to conform to the PEP 8 style guide.
* https://pypi.org/project/pylint/ - Pylint is a Python source code analyzer that looks for programming errors, helps to enforce a coding standard, and sniffs for some code smells.
