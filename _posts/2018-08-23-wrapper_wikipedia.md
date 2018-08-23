---
category : Data Analysis
title : 관심 단어 위키피디아에서 크롤링하기     
tags : [Data Analysis, Python, Text Mining, wikipedia crawling]
---

지난 번 포스팅 했던 영어 단어를 naver dictionary API와 연결하여 크롤링하기 다음편 내용이다.  
(링크 : https://inspiringpeople.github.io/data%20analysis/words_dictionary/)  

사실 논문에 있는 단어들은 naver나 일반 영어 사전보다는 wikipedia 또는 나무위키에 있는 의미를 이해할 때 더욱 유용하다. 같은 단어라도 관련 학술 카테고리에 따라 그 의미가 크게 달라지기 때문이다. 예를 들어 clustering의 경우, 아래와 같이 의미가 달라진다.  
- 네이버 사전    
-- (축산학) 뭉치기  
-- 봉군 형성  
- wikipedia  
-- 클러스터링 계수  
-- 클러스터 분석의 알고리즘 : 클러스터 분석(Cluster analysis)이란 주어진 데이터들의 특성을 고려해 데이터 집단(클러스터)을 정의하고 데이터 집단의 대표할 수 있는 대표점을 찾는 것으로 데이터 마이닝의 한 방법이다.   

## 위키피디아에서 검색 후 크롤링 하기

언어를 영어/한국어 2가지로 설정하고 python 관련된 페이지 크롤링 하기


```python
wiki_en = wikipediaapi.Wikipedia('en')
wiki_ko = wikipediaapi.Wikipedia('ko')

page_py = wiki_en.page('Python_(programming_language)')
page_py_ko = wiki_ko.page('Python_(programming_language)')
```

영어/한국어 버전에 python 관련된 페이지가 있는지 확인  
한국어로 검색했을 때 '파이썬'과 '파이선'은 다른 결과  
영어 python과 링크 되어 있는 한국어 페이지는 '파이썬'페이지임


```python
page_py = wiki_en.page('Python_(programming_language)')
page_py_ko = wiki_ko.page('파이썬')

print("Page - Exists: %s" % page_py.exists())
print("Page - Exists: %s" % page_py_ko.exists())


page_missing = wiki_en.page('NonExistingPageWithStrangeName')
print("Page - Exists: %s" %     page_missing.exists())
```

    Page - Exists: True
    Page - Exists: True
    Page - Exists: False


한국어 wikipedia 페이지 속성안에는 summary가 없는지? 다른 키워드 검색 필요


```python
print("Page - Title: %s" % page_py.title)
print("Page - Summary: %s" % page_py.summary)

print("Page - Title: %s" % page_py_ko.title)
print("Page - Summary: %s" % page_py_ko.summary)
```

    Page - Title: Python (programming language)
    Page - Summary: Python is an interpreted high-level programming language for general-purpose programming. Created by Guido van Rossum and first released in 1991, Python has a design philosophy that emphasizes code readability, notably using significant whitespace. It provides constructs that enable clear programming on both small and large scales. In July 2018, Van Rossum stepped down as the leader in the language community after 30 years.Python features a dynamic type system and automatic memory management. It supports multiple programming paradigms, including object-oriented, imperative, functional and procedural, and has a large and comprehensive standard library.Python interpreters are available for many operating systems. CPython, the reference implementation of Python, is open source software and has a community-based development model, as do nearly all of Python's other implementations. Python and CPython are managed by the non-profit Python Software Foundation.
    Page - Title: 파이썬
    Page - Summary: 파이썬(영어: Python)은 1991년 프로그래머인 귀도 반 로섬(Guido van Rossum)이 발표한 고급 프로그래밍 언어로, 플랫폼 독립적이며 인터프리터식, 객체지향적, 동적 타이핑(dynamically typed) 대화형 언어이다. 파이썬이라는 이름은 귀도가 좋아하는 코미디 〈Monty Python's Flying Circus〉에서 따온 것이다.
    파이썬은 비영리의 파이썬 소프트웨어 재단이 관리하는 개방형, 공동체 기반 개발 모델을 가지고 있다. C언어로 구현된 C파이썬 구현이 사실상의 표준이다.



```python
print(page_py.fullurl)
print(page_py_ko.fullurl)

print(page_py.canonicalurl)
print(page_py_ko.canonicalurl)
```

    https://en.wikipedia.org/wiki/Python_(programming_language)
    https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%B4%EC%8D%AC
    https://en.wikipedia.org/wiki/Python_(programming_language)
    https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%B4%EC%8D%AC


해당 키워드 관련한 전체 페이지 내용 얻기 


```python
wiki_en = wikipediaapi.Wikipedia(
        language='en',
        extract_format=wikipediaapi.ExtractFormat.WIKI
)

p_wiki = wiki_en.page("Python_(programming_language)")
print(p_wiki.text)

p_wiki_ko = wiki_ko.page("파이썬")
print(p_wiki_ko.text)

# Summary
# Section 1
# Text of section 1
# Section 1.1
# Text of section 1.1
# ...
```

    Python is an interpreted high-level programming language for general-purpose programming. Created by Guido van Rossum and first released in 1991, Python has a design philosophy that emphasizes code readability, notably using significant whitespace. It provides constructs that enable clear programming on both small and large scales. In July 2018, Van Rossum stepped down as the leader in the language community after 30 years.Python features a dynamic type system and automatic memory management. It supports multiple programming paradigms, including object-oriented, imperative, functional and procedural, and has a large and comprehensive standard library.Python interpreters are available for many operating systems. CPython, the reference implementation of Python, is open source software and has a community-based development model, as do nearly all of Python's other implementations. Python and CPython are managed by the non-profit Python Software Foundation.
    
    History
    Python was conceived in the late 1980s, and its implementation began in December 1989 by Guido van Rossum at Centrum Wiskunde & Informatica (CWI) in the Netherlands as a successor to the ABC language (itself inspired by SETL) capable of exception handling and interfacing with the Amoeba operating system. Van Rossum remains Python's principal author. His continuing central role in Python's development is reflected in the title given to him by the Python community: Benevolent Dictator For Life (BDFL) – a post from which he gave himself permanent vacation on July 12, 2018.On the origins of Python, Van Rossum wrote in 1996:
    ...In December 1989, I was looking for a "hobby" programming project that would keep me occupied during the week around Christmas. My office ... would be closed, but I had a home computer, and not much else on my hands. I decided to write an interpreter for the new scripting language I had been thinking about lately: a descendant of ABC that would appeal to Unix/C hackers. I chose Python as a working title for the project, being in a slightly irreverent mood (and a big fan of Monty Python's Flying Circus). 
    
    Python 2.0 was released on 16 October 2000 and had many major new features, including a cycle-detecting garbage collector and support for Unicode. With this release, the development process became more transparent and community-backed.Python 3.0 (initially called Python 3000 or py3k) was released on 3 December 2008 after a long testing period. It is a major revision of the language that is not completely backward-compatible with previous versions. However, many of its major features have been backported to the Python 2.6.x and 2.7.x version series, and releases of Python 3 include the 2to3 utility, which automates the translation of Python 2 code to Python 3.Python 2.7's end-of-life date was initially set at 2015, then postponed to 2020 out of concern that a large body of existing code could not easily be forward-ported to Python 3. In January 2017, Google announced work on a Python 2.7 to Go transcompiler to improve performance under concurrent workloads.
    
    Features and philosophy
    Python is a multi-paradigm programming language. Object-oriented programming and structured programming are fully supported, and many of its features support functional programming and aspect-oriented programming (including by metaprogramming and metaobjects (magic methods)). Many other paradigms are supported via extensions, including design by contract and logic programming.Python uses dynamic typing, and a combination of reference counting and a cycle-detecting garbage collector for memory management. It also features dynamic name resolution (late binding), which binds method and variable names during program execution.
    Python's design offers some support for functional programming in the Lisp tradition. It has filter(), map(), and reduce() functions; list comprehensions, dictionaries, and sets; and generator expressions. The standard library has two modules (itertools and functools) that implement functional tools borrowed from Haskell and Standard ML.The language's core philosophy is summarized in the document The Zen of Python (PEP 20), which includes aphorisms such as:
    Beautiful is better than ugly
    Explicit is better than implicit
    Simple is better than complex
    Complex is better than complicated
    Readability countsRather than having all of its functionality built into its core, Python was designed to be highly extensible. This compact modularity has made it particularly popular as a means of adding programmable interfaces to existing applications. Van Rossum's vision of a small core language with a large standard library and easily extensible interpreter stemmed from his frustrations with ABC, which espoused the opposite approach.While offering choice in coding methodology, the Python philosophy rejects exuberant syntax (such as that of Perl) in favor of a simpler, less-cluttered grammar. As Alex Martelli put it: "To describe something as 'clever' is not considered a compliment in the Python culture." Python's philosophy rejects the Perl "there is more than one way to do it" approach to language design in favor of "there should be one—and preferably only one—obvious way to do it".Python's developers strive to avoid premature optimization, and reject patches to non-critical parts of CPython that would offer marginal increases in speed at the cost of clarity. When speed is important, a Python programmer can move time-critical functions to extension modules written in languages such as C, or use PyPy, a just-in-time compiler. Cython is also available, which translates a Python script into C and makes direct C-level API calls into the Python interpreter.
    An important goal of Python's developers is keeping it fun to use. This is reflected in the language's name—a tribute to the British comedy group Monty Python—and in occasionally playful approaches to tutorials and reference materials, such as examples that refer to spam and eggs (from a famous Monty Python sketch) instead of the standard foo and bar.A common neologism in the Python community is pythonic, which can have a wide range of meanings related to program style. To say that code is pythonic is to say that it uses Python idioms well, that it is natural or shows fluency in the language, that it conforms with Python's minimalist philosophy and emphasis on readability. In contrast, code that is difficult to understand or reads like a rough transcription from another programming language is called unpythonic.
    Users and admirers of Python, especially those considered knowledgeable or experienced, are often referred to as Pythonists, Pythonistas, and Pythoneers.
    
    Syntax and semantics
    Python is meant to be an easily readable language. Its formatting is visually uncluttered, and it often uses English keywords where other languages use punctuation. Unlike many other languages, it does not use curly brackets to delimit blocks, and semicolons after statements are optional. It has fewer syntactic exceptions and special cases than C or Pascal.
    
    Indentation
    Python uses whitespace indentation, rather than curly brackets or keywords, to delimit blocks. An increase in indentation comes after certain statements; a decrease in indentation signifies the end of the current block. Thus, the program's visual structure accurately represents the program's semantic structure. This feature is also sometimes termed the off-side rule.
    
    Statements and control flow
    Python's statements include (among others):
    
    The assignment statement (token '=', the equals sign). This operates differently than in traditional imperative programming languages, and this fundamental mechanism (including the nature of Python's version of variables) illuminates many other features of the language. Assignment in C, e.g., x = 2, translates to "typed variable name x receives a copy of numeric value 2". The (right-hand) value is copied into an allocated storage location for which the (left-hand) variable name is the symbolic address. The memory allocated to the variable is large enough (potentially quite large) for the declared type. In the simplest case of Python assignment, using the same example, x = 2, translates to "(generic) name x receives a reference to a separate, dynamically allocated object of numeric (int) type of value 2." This is termed binding the name to the object. Since the name's storage location doesn't contain the indicated value, it is improper to call it a variable. Names may be subsequently rebound at any time to objects of greatly varying types, including strings, procedures, complex objects with data and methods, etc. Successive assignments of a common value to multiple names, e.g., x = 2; y = 2; z = 2 result in allocating storage to (at most) three names and one numeric object, to which all three names are bound. Since a name is a generic reference holder it is unreasonable to associate a fixed data type with it. However at a given time a name will be bound to some object, which will have a type; thus there is dynamic typing.
    The if statement, which conditionally executes a block of code, along with else and elif (a contraction of else-if).
    The for statement, which iterates over an iterable object, capturing each element to a local variable for use by the attached block.
    The while statement, which executes a block of code as long as its condition is true.
    The try statement, which allows exceptions raised in its attached code block to be caught and handled by except clauses; it also ensures that clean-up code in a finally block will always be run regardless of how the block exits.
    The raise statement, used to raise a specified exception or re-raise a caught exception.
    The class statement, which executes a block of code and attaches its local namespace to a class, for use in object-oriented programming.
    The def statement, which defines a function or method.
    The with statement, from Python 2.5 released on September 2006, which encloses a code block within a context manager (for example, acquiring a lock before the block of code is run and releasing the lock afterwards, or opening a file and then closing it), allowing Resource Acquisition Is Initialization (RAII)-like behavior and replaces a common try/finally idiom.
    The pass statement, which serves as a NOP. It is syntactically needed to create an empty code block.
    The assert statement, used during debugging to check for conditions that ought to apply.
    The yield statement, which returns a value from a generator function. From Python 2.5, yield is also an operator. This form is used to implement coroutines.
    The import statement, which is used to import modules whose functions or variables can be used in the current program. There are three ways of using import: import <module name> [as <alias>] or from <module name> import * or from <module name> import <definition 1> [as <alias 1>], <definition 2> [as <alias 2>], ....
    The print statement was changed to the print() function in Python 3.Python does not support tail call optimization or first-class continuations, and, according to Guido van Rossum, it never will. However, better support for coroutine-like functionality is provided in 2.5, by extending Python's generators. Before 2.5, generators were lazy iterators; information was passed unidirectionally out of the generator. From Python 2.5, it is possible to pass information back into a generator function, and from Python 3.3, the information can be passed through multiple stack levels.
    
    Expressions
    Some Python expressions are similar to languages such as C and Java, while some are not:
    
    Addition, subtraction, and multiplication are the same, but the behavior of division differs. There are two types of divisions in Python. They are floor division and integer division. Python also added the ** operator for exponentiation.
    From Python 3.5, the new @ infix operator was introduced. It is intended to be used by libraries such as NumPy for matrix multiplication.
    In Python, == compares by value, versus Java, which compares numerics by value and objects by reference. (Value comparisons in Java on objects can be performed with the equals() method.) Python's is operator may be used to compare object identities (comparison by reference). In Python, comparisons may be chained, for example a <= b <= c.
    Python uses the words and, or, not for its boolean operators rather than the symbolic &&, ||, ! used in Java and C.
    Python has a type of expression termed a list comprehension. Python 2.4 extended list comprehensions into a more general expression termed a generator expression.
    Anonymous functions are implemented using lambda expressions; however, these are limited in that the body can only be one expression.
    Conditional expressions in Python are written as x if c else y (different in order of operands from the c ? x : y operator common to many other languages).
    Python makes a distinction between lists and tuples. Lists are written as [1, 2, 3], are mutable, and cannot be used as the keys of dictionaries (dictionary keys must be immutable in Python). Tuples are written as (1, 2, 3), are immutable and thus can be used as the keys of dictionaries, provided all elements of the tuple are immutable. The + operator can be used to concatenate two tuples, which does not directly modify their contents, but rather produces a new tuple containing the elements of both provided tuples. Thus, given the variable t initially equal to (1, 2, 3), executing t = t + (4, 5) first evaluates t + (4, 5), which yields (1, 2, 3, 4, 5), which is then assigned back to t, thereby effectively "modifying the contents" of t, while conforming to the immutable nature of tuple objects. Parentheses are optional for tuples in unambiguous contexts.
    Python features sequence unpacking where multiple expressions, each evaluating to anything that can be assigned to (a variable, a writable property, etc.), are associated in the identical manner to that forming tuple literals and, as a whole, are put on the left hand side of the equal sign in an assignment statement. The statement expects an iterable object on the right hand side of the equal sign that produces the same number of values as the provided writable expressions when iterated through, and will iterate through it, assigning each of the produced values to the corresponding expression on the left.
    Python has a "string format" operator %. This functions analogous to printf format strings in C, e.g. "spam=%s eggs=%d" % ("blah", 2) evaluates to "spam=blah eggs=2". In Python 3 and 2.6+, this was supplemented by the format() method of the str class, e.g. "spam={0} eggs={1}".format("blah", 2). Python 3.6 added "f-strings": blah = "blah"; eggs = 2; f'spam={blah} eggs={eggs}'.
    Python has various kinds of string literals:
    Strings delimited by single or double quote marks. Unlike in Unix shells, Perl and Perl-influenced languages, single quote marks and double quote marks function identically. Both kinds of string use the backslash (\) as an escape character. String interpolation became available in Python 3.6 as "formatted string literals".
    Triple-quoted strings, which begin and end with a series of three single or double quote marks. They may span multiple lines and function like here documents in shells, Perl and Ruby.
    Raw string varieties, denoted by prefixing the string literal with an r. Escape sequences are not interpreted; hence raw strings are useful where literal backslashes are common, such as regular expressions and Windows-style paths. Compare "@-quoting" in C#.
    Python has array index and array slicing expressions on lists, denoted as a[key], a[start:stop] or a[start:stop:step]. Indexes are zero-based, and negative indexes are relative to the end. Slices take elements from the start index up to, but not including, the stop index. The third slice parameter, called step or stride, allows elements to be skipped and reversed. Slice indexes may be omitted, for example a[:] returns a copy of the entire list. Each element of a slice is a shallow copy.In Python, a distinction between expressions and statements is rigidly enforced, in contrast to languages such as Common Lisp, Scheme, or Ruby. This leads to duplicating some functionality. For example:
    
    List comprehensions vs. for-loops
    Conditional expressions vs. if blocks
    The eval() vs. exec() built-in functions (in Python 2, exec is a statement); the former is for expressions, the latter is for statements.Statements cannot be a part of an expression, so list and other comprehensions or lambda expressions, all being expressions, cannot contain statements. A particular case of this is that an assignment statement such as a = 1 cannot form part of the conditional expression of a conditional statement. This has the advantage of avoiding a classic C error of mistaking an assignment operator = for an equality operator == in conditions: if (c = 1) { ... } is syntactically valid (but probably unintended) C code but if c = 1: ... causes a syntax error in Python.
    
    Methods
    Methods on objects are functions attached to the object's class; the syntax instance.method(argument) is, for normal methods and functions, syntactic sugar for Class.method(instance, argument). Python methods have an explicit self parameter to access instance data, in contrast to the implicit self (or this) in some other object-oriented programming languages (e.g., C++, Java, Objective-C, or Ruby).
    
    Typing
    Python uses duck typing and has typed objects but untyped variable names. Type constraints are not checked at compile time; rather, operations on an object may fail, signifying that the given object is not of a suitable type. Despite being dynamically typed, Python is strongly typed, forbidding operations that are not well-defined (for example, adding a number to a string) rather than silently attempting to make sense of them.
    Python allows programmers to define their own types using classes, which are most often used for object-oriented programming. New instances of classes are constructed by calling the class (for example, SpamClass() or EggsClass()), and the classes are instances of the metaclass type (itself an instance of itself), allowing metaprogramming and reflection.
    Before version 3.0, Python had two kinds of classes: old-style and new-style. The syntax of both styles is the same, the difference being whether the class object is inherited from, directly or indirectly (all new-style classes inherit from object and are instances of type). In versions of Python 2 from Python 2.2 onwards, both kinds of classes can be used. Old-style classes were eliminated in Python 3.0.
    The long term plan is to support gradual typing and from Python 3.5, the syntax of the language allows specifying static types but they are not checked in the default implementation, CPython. An experimental optional static type checker named mypy supports compile-time type checking.
    
    Mathematics
    Python has the usual C language arithmetic operators (+, -, *, /, %). It also has ** for exponentiation, e.g. 5**3 == 125 and 9**0.5 == 3.0, and a new matrix multiply @ operator is included in version 3.5. Additionally, it has a unary operator (~), which essentially inverts all the bits of its one argument. For integers, this means ~x=-x-1. Other operators include bitwise shift operators x << y, which shifts x to the left y places, the same as x*(2**y) , and x >> y, which shifts x to the right y places, the same as x//(2**y) .The behavior of division has changed significantly over time:
    Python 2.1 and earlier use the C division behavior. The / operator is integer division if both operands are integers, and floating-point division otherwise. Integer division rounds towards 0, e.g. 7/3 == 2 and -7/3 == -2.
    Python 2.2 changes integer division to round towards negative infinity, e.g. 7/3 == 2 and -7/3 == -3. The floor division // operator is introduced. So 7//3 == 2, -7//3 == -3, 7.5//3 == 2.0 and -7.5//3 == -3.0. Adding from __future__ import division causes a module to use Python 3.0 rules for division (see next).
    Python 3.0 changes / to be always floating-point division. In Python terms, the pre-3.0 / is classic division, the version-3.0 / is real division, and // is floor division.Rounding towards negative infinity, though different from most languages, adds consistency. For instance, it means that the equation (a + b)//b == a//b + 1 is always true. It also means that the equation b*(a//b) + a%b == a is valid for both positive and negative values of a. However, maintaining the validity of this equation means that while the result of a%b is, as expected, in the half-open interval [0, b), where b is a positive integer, it has to lie in the interval (b, 0] when b is negative.Python provides a round function for rounding a float to the nearest integer. For tie-breaking, versions before 3 use round-away-from-zero: round(0.5) is 1.0, round(-0.5) is −1.0. Python 3 uses round to even: round(1.5) is 2, round(2.5) is 2.Python allows boolean expressions with multiple equality relations in a manner that is consistent with general use in mathematics. For example, the expression a < b < c tests whether a is less than b and b is less than c.  C-derived languages interpret this expression differently: in C, the expression would first evaluate a < b, resulting in 0 or 1, and that result would then be compared with c.Python has extensive built-in support for arbitrary precision arithmetic. Integers are transparently switched from the machine-supported maximum fixed-precision (usually 32 or 64 bits), belonging to the python type int, to arbitrary precision, belonging to the Python type long, where needed. The latter have an "L" suffix in their textual representation. (In Python 3, the distinction between the int and long types was eliminated; this behavior is now entirely contained by the int class.) The Decimal type/class in module decimal (since version 2.4) provides decimal floating point numbers to arbitrary precision and several rounding modes. The Fraction type in module fractions (since version 2.6) provides arbitrary precision for rational numbers.Due to Python's extensive mathematics library, and the third-party library NumPy that further extends the native capabilities, it is frequently used as a scientific scripting language to aid in problems such as numerical data processing and manipulation.
    
    Libraries
    Python's large standard library, commonly cited as one of its greatest strengths, provides tools suited to many tasks. For Internet-facing applications, many standard formats and protocols such as MIME and HTTP are supported. It includes modules for creating graphical user interfaces, connecting to relational databases, generating pseudorandom numbers, arithmetic with arbitrary precision decimals, manipulating regular expressions, and unit testing.
    Some parts of the standard library are covered by specifications (for example, the Web Server Gateway Interface (WSGI) implementation wsgiref follows PEP 333), but most modules are not. They are specified by their code, internal documentation, and test suites (if supplied). However, because most of the standard library is cross-platform Python code, only a few modules need altering or rewriting for variant implementations.
    As of  March 2018, the Python Package Index (PyPI), the official repository for third-party Python software, contains over 130,000 packages with a wide range of functionality, including:
    
    Graphical user interfaces
    Web frameworks
    Multimedia
    Databases
    Networking
    Test frameworks
    Automation
    Web scraping
    Documentation
    System administration
    Scientific computing
    Text processing
    Image processing
    
    Development environments
    Most Python implementations (including CPython) include a read–eval–print loop (REPL), permitting them to function as a command line interpreter for which the user enters statements sequentially and receives results immediately.
    Other shells, including IDLE and IPython, add further abilities such as auto-completion, session state retention and syntax highlighting.
    As well as standard desktop integrated development environments (see Wikipedia's "Python IDE" article), there are Web browser-based IDEs; SageMath (intended for developing science and math-related Python programs); PythonAnywhere, a browser-based IDE and hosting environment; and Canopy IDE, a commercial Python IDE emphasizing scientific computing.
    
    Implementations
    Reference implementation
    CPython is the reference implementation of Python. It is written in C, meeting the C89 standard with several select C99 features. It compiles Python programs into an intermediate bytecode which is then executed by its virtual machine. CPython is distributed with a large standard library written in a mixture of C and native Python. It is available for many platforms, including Windows and most modern Unix-like systems. Platform portability was one of its earliest priorities.
    
    Other implementations
    PyPy is a fast, compliant interpreter of Python 2.7 and 3.5. Its just-in-time compiler brings a significant speed improvement over CPython.Stackless Python is a significant fork of CPython that implements microthreads; it does not use the C memory stack, thus allowing massively concurrent programs. PyPy also has a stackless version.MicroPython and CircuitPython are Python 3 variants optimised for microcontrollers.
    
    Unsupported implementations
    Other just-in-time Python compilers have been developed, but are now unsupported:
    
    Google began a project named Unladen Swallow in 2009 with the aim of speeding up the Python interpreter fivefold by using the LLVM, and of improving its multithreading ability to scale to thousands of cores.
    Psyco is a just-in-time specialising compiler that integrates with CPython and transforms bytecode to machine code at runtime. The emitted code is specialised for certain data types and is faster than standard Python code.In 2005, Nokia released a Python interpreter for the Series 60 mobile phones named PyS60. It includes many of the modules from the CPython implementations and some additional modules to integrate with the Symbian operating system. The project has been kept up-to-date to run on all variants of the S60 platform, and several third-party modules are available. The Nokia N900 also supports Python with GTK widget libraries, enabling programs to be written and run on the target device.
    
    Cross-compilers to other languages
    There are several compilers to high-level object languages, with either unrestricted Python, a restricted subset of Python, or a language similar to Python as the source language:
    
    Jython compiles into Java byte code, which can then be executed by every Java virtual machine implementation. This also enables the use of Java class library functions from the Python program.
    IronPython follows a similar approach in order to run Python programs on the .NET Common Language Runtime.
    The RPython language can be compiled to C, Java bytecode, or Common Intermediate Language, and is used to build the PyPy interpreter of Python.
    Pyjs compiles Python to JavaScript.
    Cython compiles Python to C and C++.
    Pythran compiles Python to C++.
    Somewhat dated Pyrex (latest release in 2010) and Shed Skin (latest release in 2013) compile to C and C++ respectively.
    Google's Grumpy compiles Python to Go.
    MyHDL compiles Python to VHDL.
    Nuitka compiles Python into C++
    
    Performance
    A performance comparison of various Python implementations on a non-numerical (combinatorial) workload was presented at EuroSciPy '13.
    
    Development
    Python's development is conducted largely through the Python Enhancement Proposal (PEP) process, the primary mechanism for proposing major new features, collecting community input on issues and documenting Python design decisions. Outstanding PEPs are reviewed and commented on by the Python community and Guido Van Rossum, Python's Benevolent Dictator For Life.Enhancement of the language corresponds with development of the CPython reference implementation. The mailing list python-dev is the primary forum for the language's development. Specific issues are discussed in the Roundup bug tracker maintained at python.org. Development originally took place on a self-hosted source-code repository running Mercurial, until Python moved to GitHub in January 2017.CPython's public releases come in three types, distinguished by which part of the version number is incremented:
    
    Backward-incompatible versions, where code is expected to break and need to be manually ported. The first part of the version number is incremented. These releases happen infrequently—for example, version 3.0 was released 8 years after 2.0.
    Major or "feature" releases, about every 18 months, are largely compatible but introduce new features. The second part of the version number is incremented. Each major version is supported by bugfixes for several years after its release.
    Bugfix releases, which introduce no new features, occur about every 3 months and are made when a sufficient number of bugs have been fixed upstream since the last release. Security vulnerabilities are also patched in these releases. The third and final part of the version number is incremented.Many alpha, beta, and release-candidates are also released as previews and for testing before final releases. Although there is a rough schedule for each release, they are often delayed if the code is not ready. Python's development team monitors the state of the code by running the large unit test suite during development, and using the BuildBot continuous integration system.The community of Python developers has also contributed over 86,000 software modules (as of  20 August 2016) to the Python Package Index (PyPI), the official repository of third-party Python libraries.
    The major academic conference on Python is PyCon. There are also special Python mentoring programmes, such as Pyladies.
    
    Naming
    Python's name is derived from the British comedy group Monty Python, whom Python creator Guido van Rossum enjoyed while developing the language. Monty Python references appear frequently in Python code and culture; for example, the metasyntactic variables often used in Python literature are spam and eggs instead of the traditional foo and bar. The official Python documentation also contains various references to Monty Python routines.The prefix Py- is used to show that something is related to Python. Examples of the use of this prefix in names of Python applications or libraries include Pygame, a binding of SDL to Python (commonly used to create games); PyQt and PyGTK, which bind Qt and GTK to Python respectively; and PyPy, a Python implementation originally written in Python.
    
    Uses
    Since 2003, Python has consistently ranked in the top ten most popular programming languages in the TIOBE Programming Community Index where, as of  January 2018, it is the fourth most popular language (behind Java, C, and C++). It was selected Programming Language of the Year in 2007 and 2010.An empirical study found that scripting languages, such as Python, are more productive than conventional languages, such as C and Java, for programming problems involving string manipulation and search in a dictionary, and determined that memory consumption was often "better than Java and not much worse than C or C++".Large organizations that use Python include Wikipedia, Google, Yahoo!, CERN, NASA, Facebook, Amazon, Instagram, Spotify and some smaller entities like ILM and ITA. The social news networking site Reddit is written entirely in Python.
    Python can serve as a scripting language for web applications, e.g., via mod_wsgi for the Apache web server. With Web Server Gateway Interface, a standard API has evolved to facilitate these applications. Web frameworks like Django, Pylons, Pyramid, TurboGears, web2py, Tornado, Flask, Bottle and Zope support developers in the design and maintenance of complex applications. Pyjs and IronPython can be used to develop the client-side of Ajax-based applications. SQLAlchemy can be used as data mapper to a relational database. Twisted is a framework to program communications between computers, and is used (for example) by Dropbox.
    Libraries such as NumPy, SciPy and Matplotlib allow the effective use of Python in scientific computing, with specialized libraries such as Biopython and Astropy providing domain-specific functionality. SageMath is a mathematical software with a "notebook" programmable in Python: its library covers many aspects of mathematics, including algebra, combinatorics, numerical mathematics, number theory, and calculus.
    Python has been successfully embedded in many software products as a scripting language, including in finite element method software such as Abaqus, 3D parametric modeler like FreeCAD, 3D animation packages such as 3ds Max, Blender, Cinema 4D, Lightwave, Houdini, Maya, modo, MotionBuilder, Softimage, the visual effects compositor Nuke, 2D imaging programs like GIMP, Inkscape, Scribus and Paint Shop Pro, and musical notation programs like scorewriter and capella. GNU Debugger uses Python as a pretty printer to show complex structures such as C++ containers. Esri promotes Python as the best choice for writing scripts in ArcGIS. It has also been used in several video games, and has been adopted as first of the three available programming languages in Google App Engine, the other two being Java and Go. Python is also used in algorithmic trading and quantitative finance. Python can also be implemented in APIs of online brokerages that run on other languages by using wrappers.Python has been used in artificial intelligence projects. As a scripting language with modular architecture, simple syntax and rich text processing tools, Python is often used for natural language processing.Many operating systems include Python as a standard component. It ships with most Linux distributions, AmigaOS 4, FreeBSD, NetBSD, OpenBSD and macOS, and can be used from the command line (terminal). Many Linux distributions use installers written in Python: Ubuntu uses the Ubiquity installer, while Red Hat Linux and Fedora use the Anaconda installer. Gentoo Linux uses Python in its package management system, Portage.
    Python is used extensively in the information security industry, including in exploit development.Most of the Sugar software for the One Laptop per Child XO, now developed at Sugar Labs, is written in Python.The Raspberry Pi single-board computer project has adopted Python as its main user-programming language.
    LibreOffice includes Python, and intends to replace Java with Python. Its Python Scripting Provider is a core feature since Version 4.0 from 7 February 2013.
    
    Languages influenced by Python
    Python's design and philosophy have influenced many other programming languages:
    
    Boo uses indentation, a similar syntax, and a similar object model.
    Cobra uses indentation and a similar syntax, and its "Acknowledgements" document lists Python first among languages that influenced it. However, Cobra directly supports design-by-contract, unit tests, and optional static typing.
    CoffeeScript, a programming language that cross-compiles to JavaScript, has Python-inspired syntax.
    ECMAScript borrowed iterators and generators from Python.
    Go is designed for the "speed of working in a dynamic language like Python" and shares the same syntax for slicing arrays.
    Groovy was motivated by the desire to bring the Python design philosophy to Java.
    Julia was designed "with true macros [.. and to be] as usable for general programming as Python [and] should be as fast as C". Calling to or from Julia is possible; to with PyCall.jl and a Python package pyjulia allows calling, in the other direction, from Python.
    Kotlin is a functional programming language with an interactive shell similar to python. However, Kotlin is strongly typed with access to standard Java libraries.
    Ruby's creator, Yukihiro Matsumoto, has said: "I wanted a scripting language that was more powerful than Perl, and more object-oriented than Python. That's why I decided to design my own language."
    Swift, a programming language developed by Apple, has some Python-inspired syntax.
    GDScript, dynamically typed programming language used to create video-games. It is extremely similar to Python with a few minor differences.Python's development practices have also been emulated by other languages. For example, the practice of requiring a document describing the rationale for, and issues surrounding, a change to the language (in Python, a PEP) is also used in Tcl and Erlang.Python received TIOBE's Programming Language of the Year awards in 2007 and 2010. The award is given to the language with the greatest growth in popularity over the year, as measured by the TIOBE index.
    
    See also
    History of Python
    Comparison of integrated development environments for Python
    Comparison of programming languages
    List of programming languages
    pip (package manager)
    Off-side rule
    
    References
    Further reading
    Downey, Allen B. (May 2012). Think Python: How to Think Like a Computer Scientist (Version 1.6.6 ed.). ISBN 978-0-521-72596-5. 
    Hamilton, Naomi (5 August 2008). "The A-Z of Programming Languages: Python". Computerworld. Archived from the original on 29 December 2008. Retrieved 31 March 2010. 
    Lutz, Mark (2013). Learning Python (5th ed.). O'Reilly Media. ISBN 978-0-596-15806-4. 
    Pilgrim, Mark (2004). Dive Into Python. Apress. ISBN 978-1-59059-356-1. 
    Pilgrim, Mark (2009). Dive Into Python 3. Apress. ISBN 978-1-4302-2415-0. Archived from the original on 2011-10-17. 
    Summerfield, Mark (2009). Programming in Python 3 (2nd ed.). Addison-Wesley Professional. ISBN 978-0-321-68056-3.
    
    External links
    
    Official website 
    Python at Curlie (based on DMOZ)
    파이썬(영어: Python)은 1991년 프로그래머인 귀도 반 로섬(Guido van Rossum)이 발표한 고급 프로그래밍 언어로, 플랫폼 독립적이며 인터프리터식, 객체지향적, 동적 타이핑(dynamically typed) 대화형 언어이다. 파이썬이라는 이름은 귀도가 좋아하는 코미디 〈Monty Python's Flying Circus〉에서 따온 것이다.
    파이썬은 비영리의 파이썬 소프트웨어 재단이 관리하는 개방형, 공동체 기반 개발 모델을 가지고 있다. C언어로 구현된 C파이썬 구현이 사실상의 표준이다.
    
    개요
    파이썬은 초보자부터 전문가까지 사용자층을 보유하고 있다. 동적 타이핑(dynamic typing) 범용 프로그래밍 언어로, 펄 및 루비와 자주 비교된다. 다양한 플랫폼에서 쓸 수 있고, 라이브러리(모듈)가 풍부하여, 대학을 비롯한 여러 교육 기관, 연구 기관 및 산업계에서 이용이 증가하고 있다. 또 파이썬은 순수한 프로그램 언어로서의 기능 외에도 다른 언어로 쓰인 모듈들을 연결하는 풀언어(glue language)로써 자주 이용된다. 실제 파이썬은 많은 상용 응용 프로그램에서 스크립트 언어로 채용되고 있다. 도움말 문서도 정리가 잘 되어 있으며, 유니코드 문자열을 지원해서 다양한 언어의 문자 처리에도 능하다.
    
    파이썬은 기본적으로 해석기(인터프리터) 위에서 실행될 것을 염두에 두고 설계되었다.
    
    주요 특징
    동적 타이핑(dynamic typing). (실행 시간에 자료형을 검사한다.)
    객체의 멤버에 무제한으로 접근할 수 있다. (속성이나 전용의 메서드 훅을 만들어 제한할 수는 있음.)
    모듈, 클래스, 객체와 같은 언어의 요소가 내부에서 접근할 수 있고, 리플렉션을 이용한 기술을 쓸 수 있다.해석 프로그램의 종류
    C파이썬: C로 작성된 인터프리터.
    스택리스 파이썬: C 스택을 사용하지 않는 인터프리터.
    자이썬: 자바 가상 머신 용 인터프리터. 과거에는 제이파이썬(JPython)이라고 불렸다.
    IronPython: .NET 플랫폼 용 인터프리터.
    PyPy: 파이썬으로 작성된 파이썬 인터프리터.현대의 파이썬은 여전히 인터프리터 언어처럼 동작하나 사용자가 모르는 사이에 스스로 파이썬 소스 코드를 컴파일하여 바이트 코드(Byte code)를 만들어 냄으로써 다음에 수행할 때에는 빠른 속도를 보여 준다.
    파이썬에서는 들여쓰기를 사용해서 블록을 구분하는 독특한 문법을 채용하고 있다. 이 문법은 파이썬에 익숙한 사용자나 기존 프로그래밍 언어에서 들여쓰기의 중요성을 높이 평가하는 사용자에게는 잘 받아들여지고 있지만, 다른 언어의 사용자에게서는 프로그래머의 코딩 스타일을 제한한다는 비판도 많다. 이 밖에도 실행 시간에서뿐 아니라 네이티브 이진 파일을 만들어 주는 C/C++ 등의 언어에 비해 수행 속도가 느리다는 단점이 있다. 그러나 사업 분야 등 일반적인 컴퓨터 응용 환경에서는 속도가 그리 중요하지 않고, 빠른 속도를 요하는 프로그램의 경우에도 프로토타이핑한 뒤 빠른 속도가 필요한 부분만 골라서 C 언어 등으로 모듈화할 수 있다(ctypes, SWIG, SIP 등의 래퍼 생성 프로그램들이 많이 있다). 또한 Pyrex, Psyco, NumPy 등을 이용하면 수치를 빠르게 연산할 수 있기 때문에 과학, 공학 분야에서도 많이 이용되고 있다. 점차적인 중요성의 강조로 대한민국에서도 점차 그 활용도가 커지고 있다.
    
    역사
    파이썬은 1980년대 말 고안되어 네덜란드 CWI의 귀도 반 로섬이 1989년 12월 구현하기 시작하였다. 이는 역시 SETL에서 영감을 받은 ABC 언어의 후계로서, 예외 처리가 가능하고, 아메바 OS와 연동이 가능하였다. 반 로섬은 파이썬의 주 저자로 계속 중심적 역할을 맡아 파이썬의 방향을 결정하여, 파이썬 공동체로부터 '자선 종신 이사'의 칭호를 부여받았다. 이 같은 예로는 리눅스의 리누스 토발즈 등이 있다.
    
    파이썬 2
    파이썬 2.0은 2000년 10월 16일 배포되었고, 많은 기능이 추가되었다. 그중 전면적인 쓰레기 수집기(GC, Garbage Collector)탑재와 유니코드 지원이 특징적이다. 그러나 가장 중요한 변화는 개발 절차 그 자체로, 더 투명하고 공동체 지원을 받는 형태가 되었다.
    
    파이썬 3
    파이썬3000(혹은 파이썬3k)이라는 코드명을 지닌 파이썬의 3.0버전의 최종판이 긴 테스트를 거쳐 2008년 12월 3일자로 발표되었다. 2.x대 버전의 파이썬과 하위호환성이 없다는 것이 가장 큰 특징이다. 파이썬 3의 주요 기능 다수가 이전 버전과 호환되게 2.6과 2.7 버전에도 반영되기도 하였다.
    파이썬 공식 문서에서는 "파이썬 2.x 는 레거시(낡은 기술)이고, 파이썬 3.x가 파이썬의 현재와 미래가 될 것"이라고 요약을 했는데, 처음 배우는 프로그래머들은 파이썬 3으로 시작하는 것을 권장하고 있다.2.x대 버전 과의 차이를 간략히 요약하면 다음과 같다.
    
    사전형과 문자열형과 같은 내장자료형의 내부적인 변화 및 일부 구형의 구성 요소 제거.
    표준 라이브러리 재배치.
    향상된 유니코드 지원. (2.x 에서는 유니코드를 표현하기 위해 u"문자열" 처럼 유니코드 리터럴을 사용했지만 3.0 부터는 모든 문자열이 유니코드이기 때문에 "문자열" 처럼 표현하면 된다.)
    한글 변수 사용 가능.
    print 명령문이 print() 함수로 바뀌게 되었다.
    
    기능과 철학
    파이썬은 다양한 프로그래밍 패러다임을 지원하는 언어이다. 객체 지향 프로그래밍과 구조적 프로그래밍을 완벽하게 지원하며 함수형 프로그래밍, 관점 지향 프로그래밍 등도 주요 기능에서 지원 된다.
    파이썬의 핵심 철학은
    
    "아름다운게 추한 것보다 낫다." (Beautiful is better than ugly)
    "명시적인 것이 암시적인 것 보다 낫다." (Explicit is better than implicit)
    "단순함이 복잡함보다 낫다." (Simple is better than complex)
    "복잡함이 난해한 것보다 낫다." (Complex is better than complicated)
    "가독성은 중요하다." (Readability counts)와 같이 PEP 20 문서에 잘 정리되어 있다.파이썬은 언어의 핵심에 모든 기능을 넣는 대신, 사용자가 언제나 필요로 하는 최소한의 기능만을 사용하면서 확장해나갈 수 있도록 디자인되었다. 이것은 펄의 TIMTOWTDI(there's more than one way to do it - '문제를 해결하는 방법은 단 한 가지가 아니다') 철학과는 대조적인 것이며, 파이썬에서는 다른 사용자가 썼더라도 동일한 일을 하는 프로그램은 대체로 모두 비슷한 코드로 수렴한다. 라이브러리는 기본 기능에 없는 많은 기능을 제공한다.
    또, 파이썬에서는 프로그램의 문서화가 매우 중시되고 있어 언어의 기본 기능에 포함되어 있다. 파이썬은 원래 교육용으로 설계되었기 때문에 읽기 쉽고, 그래서 효율적인 코드를 되도록 간단하게 쓸 수 있도록 하려는 철학이 구석 구석까지 침투해 있어, 파이썬 커뮤니티에서도 알기 쉬운 코드를 선호하는 경향이 강하다.
    
    라이브러리
    파이썬에는 「건전지 포함("Battery Included")」이란 기본 개념이 있어, 프로그래머가 바로 사용할 수 있는 라이브러리와 통합 환경이 이미 배포판과 함께 제공된다. 이로써 파이썬의 표준 라이브러리는 매우 충실하다. 여기에는 정규 표현식을 비롯해 운영 체제의 시스템 호출이나 XML 처리, 직렬화, HTTP ,FTP 등의 각종 통신 프로토콜, 전자 메일이나 CSV 파일의 처리, 데이터베이스 접속, 그래픽 사용자 인터페이스, HTML, 파이썬 코드 구문 분석 도구 등을 포함한다.
    서드파티 라이브러리도 풍부하며, 행렬 연산 패키지 넘피(NumPy)이나 이미지 처리를 위한 필로우(Pillow), SDL 래퍼인 파이게임(PyGame), HTML/XML 파싱 라이브러리인 뷰티풀수프(Beautiful Soup) 등은 잘 알려져 있다. 다만, 가장 낮은 수준의 라이브러리까지 포함하면 너무 많아서 감당하기 쉽지 않으므로, 최근 파이썬 패키지 인덱스, 곧 PyPI (Python Packages Index)로 불리는 라이브러리의 저장소(repository)를 관리하는 공식 기구를 새롭게 도입하게 되었다. 2018년 1월 기준으로 파이썬 패키지 인덱스는 125,762 개의 다양한 기능을 가진 패키지를 관리하고 있다.
    
    문법
    파이썬의 문법에서 가장 잘 알려진 특징은 들여쓰기를 이용한 블록 구조를 들 수 있다. 이것은 보통 C 등에서 쓰이는 괄호를 이용한 블록 구조를 대신한 것으로 줄마다 처음 오는 공백으로 눈에 보이는 블록 구조가 논리적인 제어 구조와 일치하게 하는 방식이다. 아래는 C와 파이썬으로 재귀 호출을 사용한 차례곱을 계산하는 함수를 정의한 것이다.
    
    파이썬
    들여쓰기가 잘 된 C
    이렇게 비교해 보면 파이썬과 "정리되어 들여쓰기가 된" C 언어와는 차이가 거의 없어 보인다. 그러나 여기서 중요한 것은 위쪽의 C 형식은 가능한 여러 스타일 가운데 하나일 뿐이라는 사실이다.
    즉, C로는 똑같은 구문을 다음과 같이 쓸 수도 있다.
    
    읽기 어렵게 쓰인 C
    파이썬으로는 이렇게 쓰는 것을 허용하지 않는다. 파이썬에서 들여쓰기는 한 가지 스타일이 아니라 필수적인 문법에 속한다. 파이썬의 이러한 엄격한 스타일 제한은 쓰는 사람에 관계 없이 통일성을 유지하게 하며, 그 결과 가독성이 향상될 수 있는 장점이 있지만, 다른 한편으로는 프로그램을 쓰는 스타일을 선택할 자유를 제약하는 것이란 의견도 있다.
    C와 다르지만 아래와 같이 줄바꿈을 하지 않고 사용할 수도 있다.
    
    자료형
    파이썬은 다음과 같은 자료형들을 갖고 있다.
    
    기본 자료형:
    정수형
    긴 정수형(long integer) - 메모리가 허락하는 한 무제한의 자릿수로 정수를 계산할 수 있다. 파이썬 3 버전에서는 사라지고, 대신 정수형의 범위가 무제한으로 늘어났다.
    부동 소수점수형
    복소수형
    문자형
    유니코드 문자형
    함수형
    논리형(boolean)집합형 자료형:
    리스트형 - 내부의 값을 나중에 바꿀 수 있다.
    튜플(tuple)형 - 한 번 값을 정하면 내부의 값을 바꿀 수 없다.
    사전형 - 내부의 값을 나중에 바꿀 수 있다.
    집합형 - 중복을 허락하지 않는다. 변경 가능하게도, 변경 불가능하게도 만들 수 있다.또 많은 객체 지향 언어와 같이, 사용자가 새롭게 자신의 형을 정의할 수도 있다.
    파이썬은 동적 타이핑의 일종인 덕 타이핑을 사용하는 언어이기 때문에, 변수가 아닌 값이 타입을 가지고 있고, 변수는 모두 값의 참조(C++의 참조)이다.
    
    동작하는 플랫폼
    첫 파이썬 버전은 매킨토시에서 사용할 목적으로 개발되었지만, 지금은 다양한 플랫폼에서 동작한다. 하지만 안드로이드/ios에서는 동작하지 않는다.
    또한 동작이 되도록 만들 가능성도 적어 보인다.
    
    마이크로소프트 윈도우(9x/NT 계열은 최신판, 3.1 및 MS-DOS는 옛 버전만)
    매킨토시(맥 OS 9 이전, 맥 OS X 이후 포함)
    각종 유닉스
    리눅스
    팜 OS
    노키아 시리즈 60
    
    한글 다루기
    원래 파이썬은 미국 지역에서 개발되었기 때문에, 한글이나 한자와 같은 2바이트 문자를 지원하지 않았다. 그러나 파이썬 2.0 에서 유니코드 문자형을 새로 도입하여 여러 나라의 언어를 다룰 수 있게 되었다. 다른 스크립트 언어와 달리, 파이썬에서는 문자의 인코딩과 내부 유니코드 표현을 명확하게 구별한다. 유니코드 문자는 메모리에 저장되는 추상적인 개체이다. 화면에 나타내거나 파일 입출력을 할 때는 변환 코덱의 힘을 빌려서 특정 인코딩으로 변환한다. 또, 소스 코드의 문자 코드를 인식하는 기능이 있어, 다른 문자 코드로 쓰여진 프로그램의 동작이 달라질 위험을 줄여 준다. 파이썬 2.4 에서는 한중일 코덱이 표준으로 배포판에 포함되었으므로 이제 한글 처리에 문제는 거의 없다. 예를 들어 윈도우 판의 IDLE에서 한글 입출력을 잘 지원한다.
    
    사용 현황
    파이썬은 많은 제품이나 기업 및 연구기관에서 쓰이고 있다. 대표적인 몇 가지는 다음과 같다.
    
    파이썬으로 작성된 자유-오픈 소스 소프트웨어
    아나콘다(Anaconda)
    비트토렌트(BitTorrent)
    메일맨(MailMan)
    모인모인(MoinMoin Wiki)
    플러커(Plucker)
    포티지(Portage)
    파이솔(PySol)
    뷰CVS(ViewCVS)
    Zope / Plone
    Trac
    장고 (웹 프레임워크)
    드롭박스(Dropbox)
    
    파이썬을 내부적으로 사용하는 소프트웨어
    softimage|xsi (3D 애니메이션 소프트웨어)
    잉크스케이프(Inkscape)
    페인트샵 프로(Paint Shop Pro)
    문명 IV
    셰이드(Shade)
    TRIBON (3D CAD 소프트웨어)
    오토데스크 마야 (3D 애니메이션 소프트웨어)
    MotionBuilder (3D 애니메이션 소프트웨어)
    Softimage (3D 애니메이션 소프트웨어)
    Cinema 4D (3D 애니메이션 소프트웨어)
    BodyPaint 3D (3D 애니메이션 소프트웨어)
    Blender 3D (3D 애니메이션 소프트웨어)
    Sidefx Houdini (3D 애니메이션 소프트웨어)
    Abaqus (유한요소해석 소프트웨어)
    TORRENT (공유프로그램)
    Rhino 3D CAD (3D 모델링 소프트웨어)
    카카오톡 (모바일/PC 메신저)
    
    파이썬을 이용하고 있는 기업·정부 기관
    야후
    구글 (자체 언어인 Go언어로 넘어가려 하고 있다.)
    인더스트리얼 라이트 앤드 매직 (ILM)
    미국항공우주국 (NASA)
    미국 해양 대기청 (NOAA)
    카카오
    
    실행 속도 향상 관련
    저스트 인 타임 컴파일러: Psyco, PyPy
    외부 함수 호출 라이브러리 : ctypes
    파이썬 모듈 생성 언어 : 사이썬(Cython), Pyrex
    Wrapper 생성 유틸리티 : SWIG, SIP, Boost.Python, F2PY, Pyfort, PyCXX, Babel, Modulator
    수치 연산 라이브러리 : NumPy
    병렬 처리 모듈: 다중 처리
    기타 : PyInline, Weave, Py2Cmod, RPython, Shed Skin, doctest, VPython
    
    비평
    파이썬은 들여쓰기에 대해 비평을 받아왔다. 파이썬의 들여쓰기는 비정규적이고 자동화가 불가능하다. 또, 공백의 양에 따라 워드의 의미가 바뀔 수 있다. 들여쓰기에만 의지할 경우 잠재적으로 위험해질 수도 있는데, 감지하지 못하는 논리적인 버그를 만들어낼 수 있기 때문이다.
    
    같이 보기
    람다 대수
    doctest
    
    각주
    외부 링크
    (영어) 파이썬   - 공식 웹사이트
    점프 투 파이썬
    (영어) Scientific Tools for Python
    (영어) Python best practices


해당 페이지 관련한 전체 페이지 내용 얻기 (html 버전)


```python
wiki_html_en = wikipediaapi.Wikipedia(
        language='en',
        extract_format=wikipediaapi.ExtractFormat.HTML
)
p_html = wiki_html.page("Python_(programming_language)")
print(p_html.text)

wiki_html_ko = wikipediaapi.Wikipedia(
        language='ko',
        extract_format=wikipediaapi.ExtractFormat.HTML
)
p_html_ko = wiki_html_ko.page("파이썬")
print(p_html_ko.text)
```

    <p><b>Python</b> is an interpreted high-level programming language for general-purpose programming. Created by Guido van Rossum and first released in 1991, Python has a design philosophy that emphasizes code readability, notably using significant whitespace. It provides constructs that enable clear programming on both small and large scales. In July 2018, Van Rossum stepped down as the leader in the language community after 30 years.</p><p>Python features a dynamic type system and automatic memory management. It supports multiple programming paradigms, including object-oriented, imperative, functional and procedural, and has a large and comprehensive standard library.</p><p>Python interpreters are available for many operating systems. CPython, the reference implementation of Python, is open source software and has a community-based development model, as do nearly all of Python's other implementations. Python and CPython are managed by the non-profit Python Software Foundation.
    </p><p><br></p>
    
    <h2>History</h2>
    <p>Python was conceived in the late 1980s, and its implementation began in December 1989 by Guido van Rossum at Centrum Wiskunde &amp; Informatica (CWI) in the Netherlands as a successor to the ABC language (itself inspired by SETL) capable of exception handling and interfacing with the Amoeba operating system. Van Rossum remains Python's principal author. His continuing central role in Python's development is reflected in the title given to him by the Python community: <i>Benevolent Dictator For Life</i> (BDFL) – a post from which he gave himself permanent vacation on July 12, 2018.</p><p>On the origins of Python, Van Rossum wrote in 1996:</p>
    <blockquote class="templatequote"><p>...In December 1989, I was looking for a "hobby" programming project that would keep me occupied during the week around Christmas. My office ... would be closed, but I had a home computer, and not much else on my hands. I decided to write an interpreter for the new scripting language I had been thinking about lately: a descendant of ABC that would appeal to Unix/C hackers. I chose Python as a working title for the project, being in a slightly irreverent mood (and a big fan of <i>Monty Python's Flying Circus</i>). </p>
    </blockquote>
    <p>Python 2.0 was released on 16 October 2000 and had many major new features, including a cycle-detecting garbage collector and support for Unicode. With this release, the development process became more transparent and community-backed.</p><p>Python 3.0 (initially called Python 3000 or py3k) was released on 3 December 2008 after a long testing period. It is a major revision of the language that is not completely backward-compatible with previous versions. However, many of its major features have been backported to the Python 2.6.x and 2.7.x version series, and releases of Python 3 include the <code>2to3</code> utility, which automates the translation of Python 2 code to Python 3.</p><p>Python 2.7's end-of-life date was initially set at 2015, then postponed to 2020 out of concern that a large body of existing code could not easily be forward-ported to Python 3. In January 2017, Google announced work on a Python 2.7 to Go transcompiler to improve performance under concurrent workloads.</p>
    
    <h2>Features and philosophy</h2>
    <p>Python is a multi-paradigm programming language. Object-oriented programming and structured programming are fully supported, and many of its features support functional programming and aspect-oriented programming (including by metaprogramming and metaobjects (magic methods)). Many other paradigms are supported via extensions, including design by contract and logic programming.</p><p>Python uses dynamic typing, and a combination of reference counting and a cycle-detecting garbage collector for memory management. It also features dynamic name resolution (late binding), which binds method and variable names during program execution.
    </p><p>Python's design offers some support for functional programming in the Lisp tradition. It has <code>filter()</code>, <code>map()</code>, and <code>reduce()</code> functions; list comprehensions, dictionaries, and sets; and generator expressions. The standard library has two modules (itertools and functools) that implement functional tools borrowed from Haskell and Standard ML.</p><p>The language's core philosophy is summarized in the document <i>The Zen of Python</i> (<i>PEP 20</i>), which includes aphorisms such as:</p>
    <ul><li>Beautiful is better than ugly</li>
    <li>Explicit is better than implicit</li>
    <li>Simple is better than complex</li>
    <li>Complex is better than complicated</li>
    <li>Readability counts</li></ul><p>Rather than having all of its functionality built into its core, Python was designed to be highly extensible. This compact modularity has made it particularly popular as a means of adding programmable interfaces to existing applications. Van Rossum's vision of a small core language with a large standard library and easily extensible interpreter stemmed from his frustrations with ABC, which espoused the opposite approach.</p><p>While offering choice in coding methodology, the Python philosophy rejects exuberant syntax (such as that of Perl) in favor of a simpler, less-cluttered grammar. As Alex Martelli put it: "To describe something as 'clever' is <i>not</i> considered a compliment in the Python culture." Python's philosophy rejects the Perl "there is more than one way to do it" approach to language design in favor of "there should be one—and preferably only one—obvious way to do it".</p><p>Python's developers strive to avoid premature optimization, and reject patches to non-critical parts of CPython that would offer marginal increases in speed at the cost of clarity. When speed is important, a Python programmer can move time-critical functions to extension modules written in languages such as C, or use PyPy, a just-in-time compiler. Cython is also available, which translates a Python script into C and makes direct C-level API calls into the Python interpreter.
    </p><p>An important goal of Python's developers is keeping it fun to use. This is reflected in the language's name—a tribute to the British comedy group Monty Python—and in occasionally playful approaches to tutorials and reference materials, such as examples that refer to spam and eggs (from a famous Monty Python sketch) instead of the standard foo and bar.</p><p>A common neologism in the Python community is <i>pythonic</i>, which can have a wide range of meanings related to program style. To say that code is pythonic is to say that it uses Python idioms well, that it is natural or shows fluency in the language, that it conforms with Python's minimalist philosophy and emphasis on readability. In contrast, code that is difficult to understand or reads like a rough transcription from another programming language is called <i>unpythonic</i>.
    </p><p>Users and admirers of Python, especially those considered knowledgeable or experienced, are often referred to as <i>Pythonists</i>, <i>Pythonistas</i>, and <i>Pythoneers</i>.</p>
    
    <h2>Syntax and semantics</h2>
    <p>Python is meant to be an easily readable language. Its formatting is visually uncluttered, and it often uses English keywords where other languages use punctuation. Unlike many other languages, it does not use curly brackets to delimit blocks, and semicolons after statements are optional. It has fewer syntactic exceptions and special cases than C or Pascal.</p>
    
    <h3>Indentation</h3>
    <p>Python uses whitespace indentation, rather than curly brackets or keywords, to delimit blocks. An increase in indentation comes after certain statements; a decrease in indentation signifies the end of the current block. Thus, the program's visual structure accurately represents the program's semantic structure. This feature is also sometimes termed the off-side rule.
    </p>
    
    <h3>Statements and control flow</h3>
    <p>Python's statements include (among others):
    </p>
    <ul><li>The assignment statement (token '=', the equals sign). This operates differently than in traditional imperative programming languages, and this fundamental mechanism (including the nature of Python's version of <i>variables</i>) illuminates many other features of the language. Assignment in C, e.g., <code>x = 2</code>, translates to "typed variable name x receives a copy of numeric value 2". The (right-hand) value is copied into an allocated storage location for which the (left-hand) variable name is the symbolic address. The memory allocated to the variable is large enough (potentially quite large) for the declared type. In the simplest case of Python assignment, using the same example, <code>x = 2</code>, translates to "(generic) name x receives a reference to a separate, dynamically allocated object of numeric (int) type of value 2." This is termed <i>binding</i> the name to the object. Since the name's storage location doesn't <i>contain</i> the indicated value, it is improper to call it a <i>variable</i>. Names may be subsequently rebound at any time to objects of greatly varying types, including strings, procedures, complex objects with data and methods, etc. Successive assignments of a common value to multiple names, e.g., <code>x = 2</code>; <code>y = 2</code>; <code>z = 2</code> result in allocating storage to (at most) three names and one numeric object, to which all three names are bound. Since a name is a generic reference holder it is unreasonable to associate a fixed data type with it. However at a given time a name will be bound to <i>some</i> object, which <b>will</b> have a type; thus there is dynamic typing.</li>
    <li>The <code>if</code> statement, which conditionally executes a block of code, along with <code>else</code> and <code>elif</code> (a contraction of else-if).</li>
    <li>The <code>for</code> statement, which iterates over an iterable object, capturing each element to a local variable for use by the attached block.</li>
    <li>The <code>while</code> statement, which executes a block of code as long as its condition is true.</li>
    <li>The <code>try</code> statement, which allows exceptions raised in its attached code block to be caught and handled by <code>except</code> clauses; it also ensures that clean-up code in a <code>finally</code> block will always be run regardless of how the block exits.</li>
    <li>The <code>raise</code> statement, used to raise a specified exception or re-raise a caught exception.</li>
    <li>The <code>class</code> statement, which executes a block of code and attaches its local namespace to a class, for use in object-oriented programming.</li>
    <li>The <code>def</code> statement, which defines a function or method.</li>
    <li>The <code>with</code> statement, from Python 2.5 released on September 2006, which encloses a code block within a context manager (for example, acquiring a lock before the block of code is run and releasing the lock afterwards, or opening a file and then closing it), allowing Resource Acquisition Is Initialization (RAII)-like behavior and replaces a common try/finally idiom.</li>
    <li>The <code>pass</code> statement, which serves as a NOP. It is syntactically needed to create an empty code block.</li>
    <li>The <code>assert</code> statement, used during debugging to check for conditions that ought to apply.</li>
    <li>The <code>yield</code> statement, which returns a value from a generator function. From Python 2.5, <code>yield</code> is also an operator. This form is used to implement coroutines.</li>
    <li>The <code>import</code> statement, which is used to import modules whose functions or variables can be used in the current program. There are three ways of using import: <code>import &lt;module name&gt; [as &lt;alias&gt;]</code> or <code>from &lt;module name&gt; import *</code> or <code>from &lt;module name&gt; import &lt;definition 1&gt; [as &lt;alias 1&gt;], &lt;definition 2&gt; [as &lt;alias 2&gt;], ...</code>.</li>
    <li>The <code>print</code> statement was changed to the <code>print()</code> function in Python 3.</li></ul><p>Python does not support tail call optimization or first-class continuations, and, according to Guido van Rossum, it never will. However, better support for coroutine-like functionality is provided in 2.5, by extending Python's generators. Before 2.5, generators were lazy iterators; information was passed unidirectionally out of the generator. From Python 2.5, it is possible to pass information back into a generator function, and from Python 3.3, the information can be passed through multiple stack levels.</p>
    
    <h3>Expressions</h3>
    <p>Some Python expressions are similar to languages such as C and Java, while some are not:
    </p>
    <ul><li>Addition, subtraction, and multiplication are the same, but the behavior of division differs. There are two types of divisions in Python. They are floor division and integer division. Python also added the <code>**</code> operator for exponentiation.</li>
    <li>From Python 3.5, the new <code>@</code> infix operator was introduced. It is intended to be used by libraries such as NumPy for matrix multiplication.</li>
    <li>In Python, <code>==</code> compares by value, versus Java, which compares numerics by value and objects by reference. (Value comparisons in Java on objects can be performed with the <code>equals()</code> method.) Python's <code>is</code> operator may be used to compare object identities (comparison by reference). In Python, comparisons may be chained, for example <code>a &lt;= b &lt;= c</code>.</li>
    <li>Python uses the words <code>and</code>, <code>or</code>, <code>not</code> for its boolean operators rather than the symbolic <code>&amp;&amp;</code>, <code>||</code>, <code>!</code> used in Java and C.</li>
    <li>Python has a type of expression termed a <i>list comprehension</i>. Python 2.4 extended list comprehensions into a more general expression termed a <i>generator expression</i>.</li>
    <li>Anonymous functions are implemented using lambda expressions; however, these are limited in that the body can only be one expression.</li>
    <li>Conditional expressions in Python are written as <code>x if c else y</code> (different in order of operands from the <code>c ? x : y</code> operator common to many other languages).</li>
    <li>Python makes a distinction between lists and tuples. Lists are written as <code>[1, 2, 3]</code>, are mutable, and cannot be used as the keys of dictionaries (dictionary keys must be immutable in Python). Tuples are written as <code>(1, 2, 3)</code>, are immutable and thus can be used as the keys of dictionaries, provided all elements of the tuple are immutable. The <code>+</code> operator can be used to concatenate two tuples, which does not directly modify their contents, but rather produces a new tuple containing the elements of both provided tuples. Thus, given the variable <code>t</code> initially equal to <code>(1, 2, 3)</code>, executing <code>t = t + (4, 5)</code> first evaluates <code>t + (4, 5)</code>, which yields <code>(1, 2, 3, 4, 5)</code>, which is then assigned back to <code>t</code>, thereby effectively "modifying the contents" of <code>t</code>, while conforming to the immutable nature of tuple objects. Parentheses are optional for tuples in unambiguous contexts.</li>
    <li>Python features <i>sequence unpacking</i> where multiple expressions, each evaluating to anything that can be assigned to (a variable, a writable property, etc.), are associated in the identical manner to that forming tuple literals and, as a whole, are put on the left hand side of the equal sign in an assignment statement. The statement expects an <i>iterable</i> object on the right hand side of the equal sign that produces the same number of values as the provided writable expressions when iterated through, and will iterate through it, assigning each of the produced values to the corresponding expression on the left.</li>
    <li>Python has a "string format" operator <code>%</code>. This functions analogous to <code>printf</code> format strings in C, e.g. <code>"spam=%s eggs=%d" % ("blah", 2)</code> evaluates to <code>"spam=blah eggs=2"</code>. In Python 3 and 2.6+, this was supplemented by the <code>format()</code> method of the <code>str</code> class, e.g. <code>"spam={0} eggs={1}".format("blah", 2)</code>. Python 3.6 added "f-strings": <code>blah = "blah"; eggs = 2; f'spam={blah} eggs={eggs}'</code>.</li>
    <li>Python has various kinds of string literals:
    <ul><li>Strings delimited by single or double quote marks. Unlike in Unix shells, Perl and Perl-influenced languages, single quote marks and double quote marks function identically. Both kinds of string use the backslash (<code>\</code>) as an escape character. String interpolation became available in Python 3.6 as "formatted string literals".</li>
    <li>Triple-quoted strings, which begin and end with a series of three single or double quote marks. They may span multiple lines and function like here documents in shells, Perl and Ruby.</li>
    <li>Raw string varieties, denoted by prefixing the string literal with an <code>r</code>. Escape sequences are not interpreted; hence raw strings are useful where literal backslashes are common, such as regular expressions and Windows-style paths. Compare "<code>@</code>-quoting" in C#.</li></ul></li>
    <li>Python has array index and array slicing expressions on lists, denoted as <code>a[key]</code>, <code>a[start:stop]</code> or <code>a[start:stop:step]</code>. Indexes are zero-based, and negative indexes are relative to the end. Slices take elements from the <i>start</i> index up to, but not including, the <i>stop</i> index. The third slice parameter, called <i>step</i> or <i>stride</i>, allows elements to be skipped and reversed. Slice indexes may be omitted, for example <code>a[:]</code> returns a copy of the entire list. Each element of a slice is a shallow copy.</li></ul><p>In Python, a distinction between expressions and statements is rigidly enforced, in contrast to languages such as Common Lisp, Scheme, or Ruby. This leads to duplicating some functionality. For example:
    </p>
    <ul><li>List comprehensions vs. <code>for</code>-loops</li>
    <li>Conditional expressions vs. <code>if</code> blocks</li>
    <li>The <code>eval()</code> vs. <code>exec()</code> built-in functions (in Python 2, <code>exec</code> is a statement); the former is for expressions, the latter is for statements.</li></ul><p>Statements cannot be a part of an expression, so list and other comprehensions or lambda expressions, all being expressions, cannot contain statements. A particular case of this is that an assignment statement such as <code>a = 1</code> cannot form part of the conditional expression of a conditional statement. This has the advantage of avoiding a classic C error of mistaking an assignment operator <code>=</code> for an equality operator <code>==</code> in conditions: <code>if (c = 1) { ... }</code> is syntactically valid (but probably unintended) C code but <code>if c = 1: ...</code> causes a syntax error in Python.
    </p>
    
    <h3>Methods</h3>
    <p>Methods on objects are functions attached to the object's class; the syntax <code>instance.method(argument)</code> is, for normal methods and functions, syntactic sugar for <code>Class.method(instance, argument)</code>. Python methods have an explicit <code>self</code> parameter to access instance data, in contrast to the implicit <code>self</code> (or <code>this</code>) in some other object-oriented programming languages (e.g., C++, Java, Objective-C, or Ruby).</p>
    
    <h3>Typing</h3>
    <p>Python uses duck typing and has typed objects but untyped variable names. Type constraints are not checked at compile time; rather, operations on an object may fail, signifying that the given object is not of a suitable type. Despite being dynamically typed, Python is strongly typed, forbidding operations that are not well-defined (for example, adding a number to a string) rather than silently attempting to make sense of them.
    </p><p>Python allows programmers to define their own types using classes, which are most often used for object-oriented programming. New instances of classes are constructed by calling the class (for example, <code>SpamClass()</code> or <code>EggsClass()</code>), and the classes are instances of the metaclass <code>type</code> (itself an instance of itself), allowing metaprogramming and reflection.
    </p><p>Before version 3.0, Python had two kinds of classes: <i>old-style</i> and <i>new-style</i>. The syntax of both styles is the same, the difference being whether the class <code>object</code> is inherited from, directly or indirectly (all new-style classes inherit from <code>object</code> and are instances of <code>type</code>). In versions of Python 2 from Python 2.2 onwards, both kinds of classes can be used. Old-style classes were eliminated in Python 3.0.
    </p><p>The long term plan is to support gradual typing and from Python 3.5, the syntax of the language allows specifying static types but they are not checked in the default implementation, CPython. An experimental optional static type checker named <i>mypy</i> supports compile-time type checking.</p>
    
    <h3>Mathematics</h3>
    <p>Python has the usual C language arithmetic operators (<code>+</code>, <code>-</code>, <code>*</code>, <code>/</code>, <code>%</code>). It also has <code>**</code> for exponentiation, e.g. <code>5**3 == 125</code> and <code>9**0.5 == 3.0</code>, and a new matrix multiply <code>@</code> operator is included in version 3.5. Additionally, it has a unary operator (<code>~</code>), which essentially inverts all the bits of its one argument. For integers, this means <code>~x=-x-1</code>. Other operators include bitwise shift operators <code>x &lt;&lt; y</code>, which shifts <code>x</code> to the left <code>y</code> places, the same as <code>x*(2**y) </code>, and <code>x &gt;&gt; y</code>, which shifts <code>x</code> to the right <code>y</code> places, the same as <code>x//(2**y) </code>.</p><p>The behavior of division has changed significantly over time:</p>
    <ul><li>Python 2.1 and earlier use the C division behavior. The <code>/</code> operator is integer division if both operands are integers, and floating-point division otherwise. Integer division rounds towards 0, e.g. <span><code>7/3 == 2</code></span> and <span><code>-7/3 == -2</code>.</span></li>
    <li>Python 2.2 changes integer division to round towards negative infinity, e.g. <code>7/3 == 2</code> and <code>-7/3 == -3</code>. The floor division <code>//</code> operator is introduced. So <code>7//3 == 2</code>, <code>-7//3 == -3</code>, <code>7.5//3 == 2.0</code> and <code>-7.5//3 == -3.0</code>. Adding <code>from __future__ import division</code> causes a module to use Python 3.0 rules for division (see next).</li>
    <li>Python 3.0 changes <code>/</code> to be always floating-point division. In Python terms, the pre-3.0 <code>/</code> is <i>classic division</i>, the version-3.0 <code>/</code> is <i>real division</i>, and <code>//</code> is <i>floor division</i>.</li></ul><p>Rounding towards negative infinity, though different from most languages, adds consistency. For instance, it means that the equation <code>(a + b)//b == a//b + 1</code> is always true. It also means that the equation <code>b*(a//b) + a%b == a</code> is valid for both positive and negative values of <code>a</code>. However, maintaining the validity of this equation means that while the result of <code>a%b</code> is, as expected, in the half-open interval [0, <i>b</i>), where <code>b</code> is a positive integer, it has to lie in the interval (<i>b</i>, 0] when <code>b</code> is negative.</p><p>Python provides a <code>round</code> function for rounding a float to the nearest integer. For tie-breaking, versions before 3 use round-away-from-zero: <code>round(0.5)</code> is 1.0, <code>round(-0.5)</code> is −1.0. Python 3 uses round to even: <code>round(1.5)</code> is 2, <code>round(2.5)</code> is 2.</p><p>Python allows boolean expressions with multiple equality relations in a manner that is consistent with general use in mathematics. For example, the expression <code>a &lt; b &lt; c</code> tests whether <code>a</code> is less than <code>b</code> and <code>b</code> is less than <code>c</code>.  C-derived languages interpret this expression differently: in C, the expression would first evaluate <code>a &lt; b</code>, resulting in 0 or 1, and that result would then be compared with <code>c</code>.</p><p>Python has extensive built-in support for arbitrary precision arithmetic. Integers are transparently switched from the machine-supported maximum fixed-precision (usually 32 or 64 bits), belonging to the python type <code>int</code>, to arbitrary precision, belonging to the Python type <code>long</code>, where needed. The latter have an "L" suffix in their textual representation. (In Python 3, the distinction between the <code>int</code> and <code>long</code> types was eliminated; this behavior is now entirely contained by the <code>int</code> class.) The <code>Decimal</code> type/class in module <code>decimal</code> (since version 2.4) provides decimal floating point numbers to arbitrary precision and several rounding modes. The <code>Fraction</code> type in module <code>fractions</code> (since version 2.6) provides arbitrary precision for rational numbers.</p><p>Due to Python's extensive mathematics library, and the third-party library NumPy that further extends the native capabilities, it is frequently used as a scientific scripting language to aid in problems such as numerical data processing and manipulation.</p>
    
    <h2>Libraries</h2>
    <p>Python's large standard library, commonly cited as one of its greatest strengths, provides tools suited to many tasks. For Internet-facing applications, many standard formats and protocols such as MIME and HTTP are supported. It includes modules for creating graphical user interfaces, connecting to relational databases, generating pseudorandom numbers, arithmetic with arbitrary precision decimals, manipulating regular expressions, and unit testing.
    </p><p>Some parts of the standard library are covered by specifications (for example, the Web Server Gateway Interface (WSGI) implementation <code>wsgiref</code> follows PEP 333), but most modules are not. They are specified by their code, internal documentation, and test suites (if supplied). However, because most of the standard library is cross-platform Python code, only a few modules need altering or rewriting for variant implementations.
    </p><p>As of  March 2018, the Python Package Index (PyPI), the official repository for third-party Python software, contains over 130,000 packages with a wide range of functionality, including:
    </p>
    <ul><li>Graphical user interfaces</li>
    <li>Web frameworks</li>
    <li>Multimedia</li>
    <li>Databases</li>
    <li>Networking</li>
    <li>Test frameworks</li>
    <li>Automation</li>
    <li>Web scraping</li>
    <li>Documentation</li>
    <li>System administration</li>
    <li>Scientific computing</li>
    <li>Text processing</li>
    <li>Image processing</li></ul>
    
    <h2>Development environments</h2>
    <p>Most Python implementations (including CPython) include a read–eval–print loop (REPL), permitting them to function as a command line interpreter for which the user enters statements sequentially and receives results immediately.
    </p><p>Other shells, including IDLE and IPython, add further abilities such as auto-completion, session state retention and syntax highlighting.
    </p><p>As well as standard desktop integrated development environments (see Wikipedia's "Python IDE" article), there are Web browser-based IDEs; SageMath (intended for developing science and math-related Python programs); PythonAnywhere, a browser-based IDE and hosting environment; and Canopy IDE, a commercial Python IDE emphasizing scientific computing.</p>
    
    <h2>Implementations</h2>
    <h3>Reference implementation</h3>
    <p>CPython is the reference implementation of Python. It is written in C, meeting the C89 standard with several select C99 features. It compiles Python programs into an intermediate bytecode which is then executed by its virtual machine. CPython is distributed with a large standard library written in a mixture of C and native Python. It is available for many platforms, including Windows and most modern Unix-like systems. Platform portability was one of its earliest priorities.</p>
    
    <h3>Other implementations</h3>
    <p>PyPy is a fast, compliant interpreter of Python 2.7 and 3.5. Its just-in-time compiler brings a significant speed improvement over CPython.</p><p>Stackless Python is a significant fork of CPython that implements microthreads; it does not use the C memory stack, thus allowing massively concurrent programs. PyPy also has a stackless version.</p><p>MicroPython and CircuitPython are Python 3 variants optimised for microcontrollers.
    </p>
    
    <h3>Unsupported implementations</h3>
    <p>Other just-in-time Python compilers have been developed, but are now unsupported:
    </p>
    <ul><li>Google began a project named Unladen Swallow in 2009 with the aim of speeding up the Python interpreter fivefold by using the LLVM, and of improving its multithreading ability to scale to thousands of cores.</li>
    <li>Psyco is a just-in-time specialising compiler that integrates with CPython and transforms bytecode to machine code at runtime. The emitted code is specialised for certain data types and is faster than standard Python code.</li></ul><p>In 2005, Nokia released a Python interpreter for the Series 60 mobile phones named PyS60. It includes many of the modules from the CPython implementations and some additional modules to integrate with the Symbian operating system. The project has been kept up-to-date to run on all variants of the S60 platform, and several third-party modules are available. The Nokia N900 also supports Python with GTK widget libraries, enabling programs to be written and run on the target device.</p>
    
    <h3>Cross-compilers to other languages</h3>
    <p>There are several compilers to high-level object languages, with either unrestricted Python, a restricted subset of Python, or a language similar to Python as the source language:
    </p>
    <ul><li>Jython compiles into Java byte code, which can then be executed by every Java virtual machine implementation. This also enables the use of Java class library functions from the Python program.</li>
    <li>IronPython follows a similar approach in order to run Python programs on the .NET Common Language Runtime.</li>
    <li>The RPython language can be compiled to C, Java bytecode, or Common Intermediate Language, and is used to build the PyPy interpreter of Python.</li>
    <li>Pyjs compiles Python to JavaScript.</li>
    <li>Cython compiles Python to C and C++.</li>
    <li>Pythran compiles Python to C++.</li>
    <li>Somewhat dated Pyrex (latest release in 2010) and Shed Skin (latest release in 2013) compile to C and C++ respectively.</li>
    <li>Google's Grumpy compiles Python to Go.</li>
    <li>MyHDL compiles Python to VHDL.</li>
    <li>Nuitka compiles Python into C++ </li></ul>
    
    <h3>Performance</h3>
    <p>A performance comparison of various Python implementations on a non-numerical (combinatorial) workload was presented at EuroSciPy '13.</p>
    
    <h2>Development</h2>
    <p>Python's development is conducted largely through the <i>Python Enhancement Proposal</i> (PEP) process, the primary mechanism for proposing major new features, collecting community input on issues and documenting Python design decisions. Outstanding PEPs are reviewed and commented on by the Python community and Guido Van Rossum, Python's Benevolent Dictator For Life.</p><p>Enhancement of the language corresponds with development of the CPython reference implementation. The mailing list python-dev is the primary forum for the language's development. Specific issues are discussed in the Roundup bug tracker maintained at python.org. Development originally took place on a self-hosted source-code repository running Mercurial, until Python moved to GitHub in January 2017.</p><p>CPython's public releases come in three types, distinguished by which part of the version number is incremented:
    </p>
    <ul><li>Backward-incompatible versions, where code is expected to break and need to be manually ported. The first part of the version number is incremented. These releases happen infrequently—for example, version 3.0 was released 8 years after 2.0.</li>
    <li>Major or "feature" releases, about every 18 months, are largely compatible but introduce new features. The second part of the version number is incremented. Each major version is supported by bugfixes for several years after its release.</li>
    <li>Bugfix releases, which introduce no new features, occur about every 3 months and are made when a sufficient number of bugs have been fixed upstream since the last release. Security vulnerabilities are also patched in these releases. The third and final part of the version number is incremented.</li></ul><p>Many alpha, beta, and release-candidates are also released as previews and for testing before final releases. Although there is a rough schedule for each release, they are often delayed if the code is not ready. Python's development team monitors the state of the code by running the large unit test suite during development, and using the BuildBot continuous integration system.</p><p>The community of Python developers has also contributed over 86,000 software modules (as of  20 August 2016) to the Python Package Index (PyPI), the official repository of third-party Python libraries.
    </p><p>The major academic conference on Python is PyCon. There are also special Python mentoring programmes, such as Pyladies.
    </p>
    
    <h2>Naming</h2>
    <p>Python's name is derived from the British comedy group Monty Python, whom Python creator Guido van Rossum enjoyed while developing the language. Monty Python references appear frequently in Python code and culture; for example, the metasyntactic variables often used in Python literature are <i>spam</i> and <i>eggs</i> instead of the traditional <i>foo</i> and <i>bar</i>. The official Python documentation also contains various references to Monty Python routines.</p><p>The prefix <i>Py-</i> is used to show that something is related to Python. Examples of the use of this prefix in names of Python applications or libraries include Pygame, a binding of SDL to Python (commonly used to create games); PyQt and PyGTK, which bind Qt and GTK to Python respectively; and PyPy, a Python implementation originally written in Python.
    </p>
    
    <h2>Uses</h2>
    <p>Since 2003, Python has consistently ranked in the top ten most popular programming languages in the TIOBE Programming Community Index where, as of  January 2018, it is the fourth most popular language (behind Java, C, and C++). It was selected Programming Language of the Year in 2007 and 2010.</p><p>An empirical study found that scripting languages, such as Python, are more productive than conventional languages, such as C and Java, for programming problems involving string manipulation and search in a dictionary, and determined that memory consumption was often "better than Java and not much worse than C or C++".</p><p>Large organizations that use Python include Wikipedia, Google, Yahoo!, CERN, NASA, Facebook, Amazon, Instagram, Spotify and some smaller entities like ILM and ITA. The social news networking site Reddit is written entirely in Python.
    </p><p>Python can serve as a scripting language for web applications, e.g., via mod_wsgi for the Apache web server. With Web Server Gateway Interface, a standard API has evolved to facilitate these applications. Web frameworks like Django, Pylons, Pyramid, TurboGears, web2py, Tornado, Flask, Bottle and Zope support developers in the design and maintenance of complex applications. Pyjs and IronPython can be used to develop the client-side of Ajax-based applications. SQLAlchemy can be used as data mapper to a relational database. Twisted is a framework to program communications between computers, and is used (for example) by Dropbox.
    </p><p>Libraries such as NumPy, SciPy and Matplotlib allow the effective use of Python in scientific computing, with specialized libraries such as Biopython and Astropy providing domain-specific functionality. SageMath is a mathematical software with a "notebook" programmable in Python: its library covers many aspects of mathematics, including algebra, combinatorics, numerical mathematics, number theory, and calculus.
    </p><p>Python has been successfully embedded in many software products as a scripting language, including in finite element method software such as Abaqus, 3D parametric modeler like FreeCAD, 3D animation packages such as 3ds Max, Blender, Cinema 4D, Lightwave, Houdini, Maya, modo, MotionBuilder, Softimage, the visual effects compositor Nuke, 2D imaging programs like GIMP, Inkscape, Scribus and Paint Shop Pro, and musical notation programs like scorewriter and capella. GNU Debugger uses Python as a pretty printer to show complex structures such as C++ containers. Esri promotes Python as the best choice for writing scripts in ArcGIS. It has also been used in several video games, and has been adopted as first of the three available programming languages in Google App Engine, the other two being Java and Go. Python is also used in algorithmic trading and quantitative finance. Python can also be implemented in APIs of online brokerages that run on other languages by using wrappers.</p><p>Python has been used in artificial intelligence projects. As a scripting language with modular architecture, simple syntax and rich text processing tools, Python is often used for natural language processing.</p><p>Many operating systems include Python as a standard component. It ships with most Linux distributions, AmigaOS 4, FreeBSD, NetBSD, OpenBSD and macOS, and can be used from the command line (terminal). Many Linux distributions use installers written in Python: Ubuntu uses the Ubiquity installer, while Red Hat Linux and Fedora use the Anaconda installer. Gentoo Linux uses Python in its package management system, Portage.
    </p><p>Python is used extensively in the information security industry, including in exploit development.</p><p>Most of the Sugar software for the One Laptop per Child XO, now developed at Sugar Labs, is written in Python.</p><p>The Raspberry Pi single-board computer project has adopted Python as its main user-programming language.
    </p><p>LibreOffice includes Python, and intends to replace Java with Python. Its Python Scripting Provider is a core feature since Version 4.0 from 7 February 2013.
    </p>
    
    <h2>Languages influenced by Python</h2>
    <p>Python's design and philosophy have influenced many other programming languages:
    </p>
    <ul><li>Boo uses indentation, a similar syntax, and a similar object model.</li>
    <li>Cobra uses indentation and a similar syntax, and its "Acknowledgements" document lists Python first among languages that influenced it. However, Cobra directly supports design-by-contract, unit tests, and optional static typing.</li>
    <li>CoffeeScript, a programming language that cross-compiles to JavaScript, has Python-inspired syntax.</li>
    <li>ECMAScript borrowed iterators and generators from Python.</li>
    <li>Go is designed for the "speed of working in a dynamic language like Python" and shares the same syntax for slicing arrays.</li>
    <li>Groovy was motivated by the desire to bring the Python design philosophy to Java.</li>
    <li>Julia was designed "with true macros [.. and to be] as usable for general programming as Python [and] should be as fast as C". Calling to or from Julia is possible; to with PyCall.jl and a Python package pyjulia allows calling, in the other direction, from Python.</li>
    <li>Kotlin is a functional programming language with an interactive shell similar to python. However, Kotlin is strongly typed with access to standard Java libraries.</li>
    <li>Ruby's creator, Yukihiro Matsumoto, has said: "I wanted a scripting language that was more powerful than Perl, and more object-oriented than Python. That's why I decided to design my own language."</li>
    <li>Swift, a programming language developed by Apple, has some Python-inspired syntax.</li>
    <li>GDScript, dynamically typed programming language used to create video-games. It is extremely similar to Python with a few minor differences.</li></ul><p>Python's development practices have also been emulated by other languages. For example, the practice of requiring a document describing the rationale for, and issues surrounding, a change to the language (in Python, a PEP) is also used in Tcl and Erlang.</p><p>Python received TIOBE's Programming Language of the Year awards in 2007 and 2010. The award is given to the language with the greatest growth in popularity over the year, as measured by the TIOBE index.</p>
    
    <h2>See also</h2>
    <ul><li>History of Python</li>
    <li>Comparison of integrated development environments for Python</li>
    <li>Comparison of programming languages</li>
    <li>List of programming languages</li>
    <li>pip (package manager)</li>
    <li>Off-side rule</li></ul>
    
    <h2>References</h2>
    <h2>Further reading</h2>
    <ul><li><cite class="citation book">Downey, Allen B. (May 2012). <i>Think Python: How to Think Like a Computer Scientist</i> (Version 1.6.6 ed.). ISBN 978-0-521-72596-5.</cite><span title="ctx_ver=Z39.88-2004&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook&amp;rft.genre=book&amp;rft.btitle=Think+Python%3A+How+to+Think+Like+a+Computer+Scientist&amp;rft.edition=Version+1.6.6&amp;rft.date=2012-05&amp;rft.isbn=978-0-521-72596-5&amp;rft.aulast=Downey&amp;rft.aufirst=Allen+B.&amp;rft_id=http%3A%2F%2Fwww.greenteapress.com%2Fthinkpython%2Fhtml%2F&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3APython+%28programming+language%29"><span> </span></span></li>
    <li><cite class="citation news">Hamilton, Naomi (5 August 2008). "The A-Z of Programming Languages: Python". <i>Computerworld</i>. Archived from the original on 29 December 2008<span>. Retrieved <span>31 March</span> 2010</span>.</cite><span title="ctx_ver=Z39.88-2004&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Ajournal&amp;rft.genre=article&amp;rft.jtitle=Computerworld&amp;rft.atitle=The+A-Z+of+Programming+Languages%3A+Python&amp;rft.date=2008-08-05&amp;rft.aulast=Hamilton&amp;rft.aufirst=Naomi&amp;rft_id=http%3A%2F%2Fwww.computerworld.com.au%2Findex.php%2Fid%3B66665771&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3APython+%28programming+language%29"><span> </span></span></li>
    <li><cite class="citation book">Lutz, Mark (2013). <i>Learning Python</i> (5th ed.). O'Reilly Media. ISBN 978-0-596-15806-4.</cite><span title="ctx_ver=Z39.88-2004&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook&amp;rft.genre=book&amp;rft.btitle=Learning+Python&amp;rft.edition=5th&amp;rft.pub=O%27Reilly+Media&amp;rft.date=2013&amp;rft.isbn=978-0-596-15806-4&amp;rft.aulast=Lutz&amp;rft.aufirst=Mark&amp;rft_id=http%3A%2F%2Fshop.oreilly.com%2Fproduct%2F0636920028154.do&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3APython+%28programming+language%29"><span> </span></span></li>
    <li><cite class="citation book">Pilgrim, Mark (2004). <i>Dive Into Python</i>. Apress. ISBN 978-1-59059-356-1.</cite><span title="ctx_ver=Z39.88-2004&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook&amp;rft.genre=book&amp;rft.btitle=Dive+Into+Python&amp;rft.pub=Apress&amp;rft.date=2004&amp;rft.isbn=978-1-59059-356-1&amp;rft.aulast=Pilgrim&amp;rft.aufirst=Mark&amp;rft_id=http%3A%2F%2Fdiveintopython.net&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3APython+%28programming+language%29"><span> </span></span></li>
    <li><cite class="citation book">Pilgrim, Mark (2009). <i>Dive Into Python 3</i>. Apress. ISBN 978-1-4302-2415-0. Archived from the original on 2011-10-17.</cite><span title="ctx_ver=Z39.88-2004&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook&amp;rft.genre=book&amp;rft.btitle=Dive+Into+Python+3&amp;rft.pub=Apress&amp;rft.date=2009&amp;rft.isbn=978-1-4302-2415-0&amp;rft.aulast=Pilgrim&amp;rft.aufirst=Mark&amp;rft_id=http%3A%2F%2Fdiveintopython3.net&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3APython+%28programming+language%29"><span> </span></span></li>
    <li><cite class="citation book">Summerfield, Mark (2009). <i>Programming in Python 3</i> (2nd ed.). Addison-Wesley Professional. ISBN 978-0-321-68056-3.</cite><span title="ctx_ver=Z39.88-2004&amp;rft_val_fmt=info%3Aofi%2Ffmt%3Akev%3Amtx%3Abook&amp;rft.genre=book&amp;rft.btitle=Programming+in+Python+3&amp;rft.edition=2nd&amp;rft.pub=Addison-Wesley+Professional&amp;rft.date=2009&amp;rft.isbn=978-0-321-68056-3&amp;rft.aulast=Summerfield&amp;rft.aufirst=Mark&amp;rft_id=http%3A%2F%2Fwww.qtrac.eu%2Fpy3book.html&amp;rfr_id=info%3Asid%2Fen.wikipedia.org%3APython+%28programming+language%29"><span> </span></span></li></ul>
    
    <h2>External links</h2>
    
    <ul><li><span><span>Official website</span></span> </li>
    <li>Python at Curlie (based on DMOZ)</li></ul>
    
    
    
    
    <p class="mw-empty-elt">
    
    </p>
    <p><b>파이썬</b>(<span>영어: </span><span lang="en">Python</span>)은 1991년 프로그래머인 귀도 반 로섬(Guido van Rossum)이 발표한 고급 프로그래밍 언어로, 플랫폼 독립적이며 인터프리터식, 객체지향적, 동적 타이핑(dynamically typed) 대화형 언어이다. 파이썬이라는 이름은 귀도가 좋아하는 코미디 〈Monty Python's Flying Circus〉에서 따온 것이다.
    </p><p>파이썬은 비영리의 파이썬 소프트웨어 재단이 관리하는 개방형, 공동체 기반 개발 모델을 가지고 있다. C언어로 구현된 C파이썬 구현이 사실상의 표준이다.
    </p>
    
    <h2>개요</h2>
    <p>파이썬은 초보자부터 전문가까지 사용자층을 보유하고 있다. 동적 타이핑(dynamic typing) 범용 프로그래밍 언어로, 펄 및 루비와 자주 비교된다. 다양한 플랫폼에서 쓸 수 있고, 라이브러리(모듈)가 풍부하여, 대학을 비롯한 여러 교육 기관, 연구 기관 및 산업계에서 이용이 증가하고 있다. 또 파이썬은 순수한 프로그램 언어로서의 기능 외에도 다른 언어로 쓰인 모듈들을 연결하는 풀언어(glue language)로써 자주 이용된다. 실제 파이썬은 많은 상용 응용 프로그램에서 스크립트 언어로 채용되고 있다. 도움말 문서도 정리가 잘 되어 있으며, 유니코드 문자열을 지원해서 다양한 언어의 문자 처리에도 능하다.
    </p>
    
    <p>파이썬은 기본적으로 해석기(인터프리터) 위에서 실행될 것을 염두에 두고 설계되었다.
    </p>
    <ul><li>주요 특징
    <ul><li>동적 타이핑(dynamic typing). (실행 시간에 자료형을 검사한다.)</li>
    <li>객체의 멤버에 무제한으로 접근할 수 있다. (속성이나 전용의 메서드 훅을 만들어 제한할 수는 있음.)</li>
    <li>모듈, 클래스, 객체와 같은 언어의 요소가 내부에서 접근할 수 있고, 리플렉션을 이용한 기술을 쓸 수 있다.</li></ul></li></ul><ul><li>해석 프로그램의 종류
    <ul><li>C파이썬: C로 작성된 인터프리터.</li>
    <li>스택리스 파이썬: C 스택을 사용하지 않는 인터프리터.</li>
    <li>자이썬: 자바 가상 머신 용 인터프리터. 과거에는 제이파이썬(JPython)이라고 불렸다.</li>
    <li>IronPython: .NET 플랫폼 용 인터프리터.</li>
    <li>PyPy: 파이썬으로 작성된 파이썬 인터프리터.</li></ul></li></ul><p>현대의 파이썬은 여전히 인터프리터 언어처럼 동작하나 사용자가 모르는 사이에 스스로 파이썬 소스 코드를 컴파일하여 바이트 코드(Byte code)를 만들어 냄으로써 다음에 수행할 때에는 빠른 속도를 보여 준다.
    </p><p>파이썬에서는 들여쓰기를 사용해서 블록을 구분하는 독특한 문법을 채용하고 있다. 이 문법은 파이썬에 익숙한 사용자나 기존 프로그래밍 언어에서 들여쓰기의 중요성을 높이 평가하는 사용자에게는 잘 받아들여지고 있지만, 다른 언어의 사용자에게서는 프로그래머의 코딩 스타일을 제한한다는 비판도 많다. 이 밖에도 실행 시간에서뿐 아니라 네이티브 이진 파일을 만들어 주는 C/C++ 등의 언어에 비해 수행 속도가 느리다는 단점이 있다. 그러나 사업 분야 등 일반적인 컴퓨터 응용 환경에서는 속도가 그리 중요하지 않고, 빠른 속도를 요하는 프로그램의 경우에도 프로토타이핑한 뒤 빠른 속도가 필요한 부분만 골라서 C 언어 등으로 모듈화할 수 있다(ctypes, SWIG, SIP 등의 래퍼 생성 프로그램들이 많이 있다). 또한 Pyrex, Psyco, NumPy 등을 이용하면 수치를 빠르게 연산할 수 있기 때문에 과학, 공학 분야에서도 많이 이용되고 있다. 점차적인 중요성의 강조로 대한민국에서도 점차 그 활용도가 커지고 있다.</p>
    
    <h2>역사</h2>
    <p>파이썬은 1980년대 말 고안되어 네덜란드 CWI의 귀도 반 로섬이 1989년 12월 구현하기 시작하였다. 이는 역시 SETL에서 영감을 받은 ABC 언어의 후계로서, 예외 처리가 가능하고, 아메바 OS와 연동이 가능하였다. 반 로섬은 파이썬의 주 저자로 계속 중심적 역할을 맡아 파이썬의 방향을 결정하여, 파이썬 공동체로부터 '자선 종신 이사'의 칭호를 부여받았다. 이 같은 예로는 리눅스의 리누스 토발즈 등이 있다.
    </p>
    
    <h3>파이썬 2</h3>
    <p>파이썬 2.0은 2000년 10월 16일 배포되었고, 많은 기능이 추가되었다. 그중 전면적인 쓰레기 수집기(GC, Garbage Collector)탑재와 유니코드 지원이 특징적이다. 그러나 가장 중요한 변화는 개발 절차 그 자체로, 더 투명하고 공동체 지원을 받는 형태가 되었다.
    </p>
    
    <h3>파이썬 3</h3>
    <p>파이썬3000(혹은 파이썬3k)이라는 코드명을 지닌 파이썬의 3.0버전의 최종판이 긴 테스트를 거쳐 2008년 12월 3일자로 발표되었다. 2.x대 버전의 파이썬과 하위호환성이 없다는 것이 가장 큰 특징이다. 파이썬 3의 주요 기능 다수가 이전 버전과 호환되게 2.6과 2.7 버전에도 반영되기도 하였다.
    </p><p>파이썬 공식 문서에서는 "파이썬 2.x 는 레거시(낡은 기술)이고, 파이썬 3.x가 파이썬의 현재와 미래가 될 것"이라고 요약을 했는데, 처음 배우는 프로그래머들은 파이썬 3으로 시작하는 것을 권장하고 있다.</p><p>2.x대 버전 과의 차이를 간략히 요약하면 다음과 같다.
    </p>
    <ul><li>사전형과 문자열형과 같은 내장자료형의 내부적인 변화 및 일부 구형의 구성 요소 제거.</li>
    <li>표준 라이브러리 재배치.</li>
    <li>향상된 유니코드 지원. (2.x 에서는 유니코드를 표현하기 위해 <code>u"문자열"</code> 처럼 유니코드 리터럴을 사용했지만 3.0 부터는 모든 문자열이 유니코드이기 때문에 <code>"문자열"</code> 처럼 표현하면 된다.)</li>
    <li>한글 변수 사용 가능.</li>
    <li><code>print</code> 명령문이 <code>print()</code> 함수로 바뀌게 되었다.</li></ul>
    
    <h2>기능과 철학</h2>
    <p>파이썬은 다양한 프로그래밍 패러다임을 지원하는 언어이다. 객체 지향 프로그래밍과 구조적 프로그래밍을 완벽하게 지원하며 함수형 프로그래밍, 관점 지향 프로그래밍 등도 주요 기능에서 지원 된다.
    </p><p>파이썬의 핵심 철학은
    </p>
    <ul><li>"아름다운게 추한 것보다 낫다." (Beautiful is better than ugly)</li>
    <li>"명시적인 것이 암시적인 것 보다 낫다." (Explicit is better than implicit)</li>
    <li>"단순함이 복잡함보다 낫다." (Simple is better than complex)</li>
    <li>"복잡함이 난해한 것보다 낫다." (Complex is better than complicated)</li>
    <li>"가독성은 중요하다." (Readability counts)</li></ul><p>와 같이 <i>PEP 20</i> 문서에 잘 정리되어 있다.</p><p>파이썬은 언어의 핵심에 모든 기능을 넣는 대신, 사용자가 언제나 필요로 하는 최소한의 기능만을 사용하면서 확장해나갈 수 있도록 디자인되었다. 이것은 펄의 TIMTOWTDI(<i>there's more than one way to do it</i> - '문제를 해결하는 방법은 단 한 가지가 아니다') 철학과는 대조적인 것이며, 파이썬에서는 다른 사용자가 썼더라도 동일한 일을 하는 프로그램은 대체로 모두 비슷한 코드로 수렴한다. 라이브러리는 기본 기능에 없는 많은 기능을 제공한다.
    </p><p>또, 파이썬에서는 프로그램의 문서화가 매우 중시되고 있어 언어의 기본 기능에 포함되어 있다. 파이썬은 원래 교육용으로 설계되었기 때문에 읽기 쉽고, 그래서 효율적인 코드를 되도록 간단하게 쓸 수 있도록 하려는 철학이 구석 구석까지 침투해 있어, 파이썬 커뮤니티에서도 알기 쉬운 코드를 선호하는 경향이 강하다.
    </p>
    
    <h3>라이브러리</h3>
    <p>파이썬에는 「건전지 포함("Battery Included")」이란 기본 개념이 있어, 프로그래머가 바로 사용할 수 있는 라이브러리와 통합 환경이 이미 배포판과 함께 제공된다. 이로써 파이썬의 표준 라이브러리는 매우 충실하다. 여기에는 정규 표현식을 비롯해 운영 체제의 시스템 호출이나 XML 처리, 직렬화, HTTP ,FTP 등의 각종 통신 프로토콜, 전자 메일이나 CSV 파일의 처리, 데이터베이스 접속, 그래픽 사용자 인터페이스, HTML, 파이썬 코드 구문 분석 도구 등을 포함한다.
    </p><p>서드파티 라이브러리도 풍부하며, 행렬 연산 패키지 넘피(NumPy)이나 이미지 처리를 위한 필로우(Pillow), SDL 래퍼인 파이게임(PyGame), HTML/XML 파싱 라이브러리인 뷰티풀수프(Beautiful Soup) 등은 잘 알려져 있다. 다만, 가장 낮은 수준의 라이브러리까지 포함하면 너무 많아서 감당하기 쉽지 않으므로, 최근 파이썬 패키지 인덱스, 곧 PyPI (Python Packages Index)로 불리는 라이브러리의 저장소(repository)를 관리하는 공식 기구를 새롭게 도입하게 되었다. 2018년 1월 기준으로 파이썬 패키지 인덱스는 125,762 개의 다양한 기능을 가진 패키지를 관리하고 있다.
    </p>
    
    <h2>문법</h2>
    <p>파이썬의 문법에서 가장 잘 알려진 특징은 들여쓰기를 이용한 블록 구조를 들 수 있다. 이것은 보통 C 등에서 쓰이는 괄호를 이용한 블록 구조를 대신한 것으로 줄마다 처음 오는 공백으로 눈에 보이는 블록 구조가 논리적인 제어 구조와 일치하게 하는 방식이다. 아래는 C와 파이썬으로 재귀 호출을 사용한 차례곱을 계산하는 함수를 정의한 것이다.
    </p>
    <dl><dt>파이썬</dt></dl>
    <dl><dt>들여쓰기가 잘 된 C</dt></dl>
    <p>이렇게 비교해 보면 파이썬과 "정리되어 들여쓰기가 된" C 언어와는 차이가 거의 없어 보인다. 그러나 여기서 중요한 것은 위쪽의 C 형식은 가능한 여러 스타일 가운데 하나일 뿐이라는 사실이다.
    </p><p>즉, C로는 똑같은 구문을 다음과 같이 쓸 수도 있다.
    </p>
    <dl><dt>읽기 어렵게 쓰인 C</dt></dl>
    <p>파이썬으로는 이렇게 쓰는 것을 허용하지 않는다. 파이썬에서 들여쓰기는 한 가지 스타일이 아니라 필수적인 문법에 속한다. 파이썬의 이러한 엄격한 스타일 제한은 쓰는 사람에 관계 없이 통일성을 유지하게 하며, 그 결과 가독성이 향상될 수 있는 장점이 있지만, 다른 한편으로는 프로그램을 쓰는 스타일을 선택할 자유를 제약하는 것이란 의견도 있다.
    </p><p>C와 다르지만 아래와 같이 줄바꿈을 하지 않고 사용할 수도 있다.
    </p>
    
    <h2>자료형</h2>
    <p>파이썬은 다음과 같은 자료형들을 갖고 있다.
    </p>
    <ul><li>기본 자료형:
    <ul><li>정수형</li>
    <li>긴 정수형(long integer) - 메모리가 허락하는 한 무제한의 자릿수로 정수를 계산할 수 있다. 파이썬 3 버전에서는 사라지고, 대신 정수형의 범위가 무제한으로 늘어났다.</li>
    <li>부동 소수점수형</li>
    <li>복소수형</li>
    <li>문자형</li>
    <li>유니코드 문자형</li>
    <li>함수형</li>
    <li>논리형(boolean)</li></ul></li></ul><ul><li>집합형 자료형:
    <ul><li>리스트형 - 내부의 값을 나중에 바꿀 수 있다.</li>
    <li>튜플(tuple)형 - 한 번 값을 정하면 내부의 값을 바꿀 수 없다.</li>
    <li>사전형 - 내부의 값을 나중에 바꿀 수 있다.</li>
    <li>집합형 - 중복을 허락하지 않는다. 변경 가능하게도, 변경 불가능하게도 만들 수 있다.</li></ul></li></ul><p>또 많은 객체 지향 언어와 같이, 사용자가 새롭게 자신의 형을 정의할 수도 있다.
    </p><p>파이썬은 동적 타이핑의 일종인 덕 타이핑을 사용하는 언어이기 때문에, 변수가 아닌 값이 타입을 가지고 있고, 변수는 모두 값의 참조(C++의 참조)이다.
    </p>
    
    <h2>동작하는 플랫폼</h2>
    <p>첫 파이썬 버전은 매킨토시에서 사용할 목적으로 개발되었지만, 지금은 다양한 플랫폼에서 동작한다. 하지만 안드로이드/ios에서는 동작하지 않는다.
    </p><p>또한 동작이 되도록 만들 가능성도 적어 보인다.
    </p>
    <ul><li>마이크로소프트 윈도우(9x/NT 계열은 최신판, 3.1 및 MS-DOS는 옛 버전만)</li>
    <li>매킨토시(맥 OS 9 이전, 맥 OS X 이후 포함)</li>
    <li>각종 유닉스</li>
    <li>리눅스</li>
    <li>팜 OS</li>
    <li>노키아 시리즈 60</li></ul>
    
    <h2>한글 다루기</h2>
    <p>원래 파이썬은 미국 지역에서 개발되었기 때문에, 한글이나 한자와 같은 2바이트 문자를 지원하지 않았다. 그러나 파이썬 2.0 에서 유니코드 문자형을 새로 도입하여 여러 나라의 언어를 다룰 수 있게 되었다. 다른 스크립트 언어와 달리, 파이썬에서는 문자의 인코딩과 내부 유니코드 표현을 명확하게 구별한다. 유니코드 문자는 메모리에 저장되는 추상적인 개체이다. 화면에 나타내거나 파일 입출력을 할 때는 변환 코덱의 힘을 빌려서 특정 인코딩으로 변환한다. 또, 소스 코드의 문자 코드를 인식하는 기능이 있어, 다른 문자 코드로 쓰여진 프로그램의 동작이 달라질 위험을 줄여 준다. 파이썬 2.4 에서는 한중일 코덱이 표준으로 배포판에 포함되었으므로 이제 한글 처리에 문제는 거의 없다. 예를 들어 윈도우 판의 IDLE에서 한글 입출력을 잘 지원한다.
    </p>
    
    <h2>사용 현황</h2>
    <p>파이썬은 많은 제품이나 기업 및 연구기관에서 쓰이고 있다. 대표적인 몇 가지는 다음과 같다.
    </p>
    
    <h3>파이썬으로 작성된 자유-오픈 소스 소프트웨어</h3>
    <ul><li>아나콘다(Anaconda)</li>
    <li>비트토렌트(BitTorrent)</li>
    <li>메일맨(MailMan)</li>
    <li>모인모인(MoinMoin Wiki)</li>
    <li>플러커(Plucker)</li>
    <li>포티지(Portage)</li>
    <li>파이솔(PySol)</li>
    <li>뷰CVS(ViewCVS)</li>
    <li>Zope / Plone</li>
    <li>Trac</li>
    <li>장고 (웹 프레임워크)</li>
    <li>드롭박스(Dropbox)</li></ul>
    
    <h3>파이썬을 내부적으로 사용하는 소프트웨어</h3>
    <ul><li>softimage|xsi (3D 애니메이션 소프트웨어)</li>
    <li>잉크스케이프(Inkscape)</li>
    <li>페인트샵 프로(Paint Shop Pro)</li>
    <li>문명 IV</li>
    <li>셰이드(Shade)</li>
    <li>TRIBON (3D CAD 소프트웨어)</li>
    <li>오토데스크 마야 (3D 애니메이션 소프트웨어)</li>
    <li>MotionBuilder (3D 애니메이션 소프트웨어)</li>
    <li>Softimage (3D 애니메이션 소프트웨어)</li>
    <li>Cinema 4D (3D 애니메이션 소프트웨어)</li>
    <li>BodyPaint 3D (3D 애니메이션 소프트웨어)</li>
    <li>Blender 3D (3D 애니메이션 소프트웨어)</li>
    <li>Sidefx Houdini (3D 애니메이션 소프트웨어)</li>
    <li>Abaqus (유한요소해석 소프트웨어)</li>
    <li>TORRENT (공유프로그램)</li>
    <li>Rhino 3D CAD (3D 모델링 소프트웨어)</li>
    <li>카카오톡 (모바일/PC 메신저)</li></ul>
    
    <h3>파이썬을 이용하고 있는 기업·정부 기관</h3>
    <ul><li>야후</li>
    <li>구글 (자체 언어인 Go언어로 넘어가려 하고 있다.)</li>
    <li>인더스트리얼 라이트 앤드 매직 (ILM)</li>
    <li>미국항공우주국 (NASA)</li>
    <li>미국 해양 대기청 (NOAA)</li>
    <li>카카오</li></ul>
    
    <h2>실행 속도 향상 관련</h2>
    <ul><li>저스트 인 타임 컴파일러: Psyco, PyPy</li>
    <li>외부 함수 호출 라이브러리 : ctypes</li>
    <li>파이썬 모듈 생성 언어 : 사이썬(Cython), Pyrex</li>
    <li>Wrapper 생성 유틸리티 : SWIG, SIP, Boost.Python, F2PY, Pyfort, PyCXX, Babel, Modulator</li>
    <li>수치 연산 라이브러리 : NumPy</li>
    <li>병렬 처리 모듈: 다중 처리</li>
    <li>기타 : PyInline, Weave, Py2Cmod, RPython, Shed Skin, doctest, VPython</li></ul>
    
    <h2>비평</h2>
    <p>파이썬은 들여쓰기에 대해 비평을 받아왔다. 파이썬의 들여쓰기는 비정규적이고 자동화가 불가능하다. 또, 공백의 양에 따라 워드의 의미가 바뀔 수 있다. 들여쓰기에만 의지할 경우 잠재적으로 위험해질 수도 있는데, 감지하지 못하는 논리적인 버그를 만들어낼 수 있기 때문이다.</p>
    
    <h2>같이 보기</h2>
    <ul><li>람다 대수</li>
    <li>doctest</li></ul>
    
    <h2>각주</h2>
    <h2>외부 링크</h2>
    <ul><li><b><span title="언어: 영어">(영어)</span></b> <span><span>파이썬</span></span>   - 공식 웹사이트</li>
    <li>점프 투 파이썬</li>
    <li><b><span title="언어: 영어">(영어)</span></b> Scientific Tools for Python</li>
    <li><b><span title="언어: 영어">(영어)</span></b> Python best practices</li></ul>


해당 페이지에 있는 section 정보 가져오기  


```python
def print_sections(sections, level=0):
        for s in sections:
                print("%s: %s - %s" % ("*" * (level + 1), s.title, s.text[0:40]))
                print_sections(s.sections, level + 1)

print_sections(page_py.sections)
```

    *: History - Python was conceived in the late 1980s, 
    *: Features and philosophy - Python is a multi-paradigm programming l
    *: Syntax and semantics - Python is meant to be an easily readable
    **: Indentation - Python uses whitespace indentation, rath
    **: Statements and control flow - Python's statements include (among other
    **: Expressions - Some Python expressions are similar to l
    **: Methods - Methods on objects are functions attache
    **: Typing - Python uses duck typing and has typed ob
    **: Mathematics - Python has the usual C language arithmet
    *: Libraries - Python's large standard library, commonl
    *: Development environments - Most Python implementations (including C
    *: Implementations - 
    **: Reference implementation - CPython is the reference implementation 
    **: Other implementations - PyPy is a fast, compliant interpreter of
    **: Unsupported implementations - Other just-in-time Python compilers have
    **: Cross-compilers to other languages - There are several compilers to high-leve
    **: Performance - A performance comparison of various Pyth
    *: Development - Python's development is conducted largel
    *: Naming - Python's name is derived from the Britis
    *: Uses - Since 2003, Python has consistently rank
    *: Languages influenced by Python - Python's design and philosophy have infl
    *: See also - History of Python
    Comparison of integrat
    *: References - 
    *: Further reading - Downey, Allen B. (May 2012). Think Pytho
    *: External links - 
    Official website 
    Python at Curlie (bas


wikipedia에서 제공하는 다른 나라 언어로 페이지 크롤링이 가능하다.  
영어 페이지는 각 나라 언어로 page.langlinks로 연결되어 있다.  


```python
def print_langlinks(page):
        langlinks = page.langlinks
        for k in sorted(langlinks.keys()):
            v = langlinks[k]
            print(k)
#             print("%s: %s - %s: %s" % (k, v.language, v.title, v.fullurl))

print_langlinks(page_py)
```

    af
    als
    an
    ar
    as
    ast
    az
    azb
    be
    bg
    bn
    bs
    bug
    ca
    ceb
    ckb
    cs
    da
    de
    el
    eo
    es
    et
    eu
    fa
    fi
    fr
    gl
    gu
    he
    hi
    hr
    hu
    hy
    ia
    id
    is
    it
    ja
    jbo
    ka
    kk
    km
    ko
    ky
    la
    lmo
    lt
    lv
    mk
    ml
    mn
    mr
    ms
    my
    nds
    ne
    nl
    nn
    no
    or
    pl
    pnb
    pt
    ro
    ru
    sco
    sh
    si
    simple
    sk
    sl
    sq
    sr
    sv
    ta
    te
    tg
    th
    tl
    tr
    uk
    ur
    uz
    vi
    wuu
    zh
    zh-min-nan
    zh-yue


langlinks['ko']로 해당 wikipedia 페이지와 연결되어 있는 한국어 페이지 크롤링  
summary, title, url  


```python
def print_langlinks(page):
        langlinks = page.langlinks
        v = langlinks['ko']
        print(v.summary)
        print("%s: %s" % (v.title, v.fullurl))

print_langlinks(page_py)
```

    파이썬(영어: Python)은 1991년 프로그래머인 귀도 반 로섬(Guido van Rossum)이 발표한 고급 프로그래밍 언어로, 플랫폼 독립적이며 인터프리터식, 객체지향적, 동적 타이핑(dynamically typed) 대화형 언어이다. 파이썬이라는 이름은 귀도가 좋아하는 코미디 〈Monty Python's Flying Circus〉에서 따온 것이다.
    파이썬은 비영리의 파이썬 소프트웨어 재단이 관리하는 개방형, 공동체 기반 개발 모델을 가지고 있다. C언어로 구현된 C파이썬 구현이 사실상의 표준이다.
    파이썬: https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%B4%EC%8D%AC


해당 페이지가 포함하고 있는 Category 정보를 가져온다  


```python
def print_categories(page):
        categories = page.categories
        for title in sorted(categories.keys()):
            print("%s: %s" % (title, categories[title]))


print("Categories")
print_categories(page_py)
```

    Categories
    Category:All articles containing potentially dated statements: Category:All articles containing potentially dated statements (id: ??, ns: 14)
    Category:All articles with unsourced statements: Category:All articles with unsourced statements (id: ??, ns: 14)
    Category:Articles containing potentially dated statements from August 2016: Category:Articles containing potentially dated statements from August 2016 (id: ??, ns: 14)
    Category:Articles containing potentially dated statements from January 2018: Category:Articles containing potentially dated statements from January 2018 (id: ??, ns: 14)
    Category:Articles containing potentially dated statements from March 2018: Category:Articles containing potentially dated statements from March 2018 (id: ??, ns: 14)
    Category:Articles with Curlie links: Category:Articles with Curlie links (id: ??, ns: 14)
    Category:Articles with unsourced statements from May 2018: Category:Articles with unsourced statements from May 2018 (id: ??, ns: 14)
    Category:Articles with unsourced statements from October 2017: Category:Articles with unsourced statements from October 2017 (id: ??, ns: 14)
    Category:CS1 errors: external links: Category:CS1 errors: external links (id: ??, ns: 14)
    Category:Class-based programming languages: Category:Class-based programming languages (id: ??, ns: 14)
    Category:Computational notebook: Category:Computational notebook (id: ??, ns: 14)
    Category:Computer science in the Netherlands: Category:Computer science in the Netherlands (id: ??, ns: 14)
    Category:Cross-platform free software: Category:Cross-platform free software (id: ??, ns: 14)
    Category:Cross-platform software: Category:Cross-platform software (id: ??, ns: 14)
    Category:Dutch inventions: Category:Dutch inventions (id: ??, ns: 14)
    Category:Dynamically typed programming languages: Category:Dynamically typed programming languages (id: ??, ns: 14)
    Category:Educational programming languages: Category:Educational programming languages (id: ??, ns: 14)
    Category:Good articles: Category:Good articles (id: ??, ns: 14)
    Category:High-level programming languages: Category:High-level programming languages (id: ??, ns: 14)
    Category:Information technology in the Netherlands: Category:Information technology in the Netherlands (id: ??, ns: 14)
    Category:Object-oriented programming languages: Category:Object-oriented programming languages (id: ??, ns: 14)
    Category:Programming languages: Category:Programming languages (id: ??, ns: 14)
    Category:Programming languages created in 1991: Category:Programming languages created in 1991 (id: ??, ns: 14)
    Category:Python (programming language): Category:Python (programming language) (id: ??, ns: 14)
    Category:Scripting languages: Category:Scripting languages (id: ??, ns: 14)
    Category:Text-oriented programming languages: Category:Text-oriented programming languages (id: ??, ns: 14)
    Category:Use dmy dates from August 2015: Category:Use dmy dates from August 2015 (id: ??, ns: 14)
    Category:Wikipedia articles needing clarification from May 2018: Category:Wikipedia articles needing clarification from May 2018 (id: ??, ns: 14)
    Category:Wikipedia articles with BNF identifiers: Category:Wikipedia articles with BNF identifiers (id: ??, ns: 14)
    Category:Wikipedia articles with GND identifiers: Category:Wikipedia articles with GND identifiers (id: ??, ns: 14)
    Category:Wikipedia articles with LCCN identifiers: Category:Wikipedia articles with LCCN identifiers (id: ??, ns: 14)
    Category:Wikipedia articles with SUDOC identifiers: Category:Wikipedia articles with SUDOC identifiers (id: ??, ns: 14)



```python
def print_categorymembers(categorymembers, level=0, max_level=2):
        for c in categorymembers.values():
            print("%s: %s (ns: %d)" % ("*" * (level + 1), c.title, c.ns))
            if c.ns == wikipediaapi.Namespace.CATEGORY and level <= max_level:
                print_categorymembers(c.categorymembers, level + 1)


cat = wiki_en.page("Category:Physics")
print("Category members: Category:Physics")
print_categorymembers(cat.categorymembers)
```

    Category members: Category:Physics
    *: Physics (ns: 0)
    *: Outline of physics (ns: 0)
    *: Glossary of classical physics (ns: 0)
    *: Portal:Physics (ns: 100)
    *: Action-angle coordinates (ns: 0)
    *: Aerometer (ns: 0)
    *: Bayesian model of computational anatomy (ns: 0)
    *: Group actions in computational anatomy (ns: 0)
    *: Blasius–Chaplygin formula (ns: 0)
 ...


## Reference
- 추가 필요  
