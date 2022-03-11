Cmake基本概念

常用命令

- cmake_minimum_required(VERSION 3.2)    
- project(hello)
- add_executable(hello hello.cpp fun.cpp)
  -     add_executable(targetName [WIN32] [MACOSX_BUNDLE]
        [EXCLUDE_FROM_ALL]
        source1 [source2 ...]
        )
- add_library(hello hello.cpp)
  -     add_library(targetName [STATIC | SHARED | MODULE]
        [EXCLUDE_FROM_ALL]
        source1 [source2 ...]
        )
  - cmake -DBUILD_SHARED_LIBS=YES /path/to/source  即使CMakeLists.txt里指定了STATIC也会被编译成shared      
  - 也可以在cmake文件里，add_library()之前指定  set(BUILD_SHARED_LIBS YES)
  - 除非有明确的理由，否则不要在add_library里指定STATIC 或者 SHARED，这样可以通过变量和命令行参数来控制编译，实现更大的灵活性。
- target_link_libraries(collector 
                                       PUBLIC ui
                                        PRIVATE algo engine)
  -     target_link_libraries(targetName
        <PRIVATE|PUBLIC|INTERFACE> item1 [item2 ...]
        [<PRIVATE|PUBLIC|INTERFACE> item3 [item4 ...]]
        ...
        )
  - A PRIVATE依赖B ： B用于A的一些内部功能实现，依赖A的不需要知道B的存在。
  - A PUBLIC 依赖B：这意味着B不仅用于实现A内部的一些功能，A还用B中的一些东西作为他自己的接口。也就是说没有B，A就不能用；依赖A的对象也同样直接依赖B
  - A INTERFACE 依赖B：这意味着为了使用A，也要用到一部分B的实现。但是与public不同之处在于，A的内部实现没有用到B，只是把B作为它接口的一部分
  - targets的名字不建议直接复用project的名字，不灵活。
  - 尽可能指定PUBLIC、PRIVATE、INTERFACE关键字

变量

    set(varName value... [PARENT_SCOPE])
    # 变量是有作用域的
    
    set(myVar a b c) # myVar = "a;b;c"
    set(myVar "a b c") # myVar = "a b c"
    
    ${myVar}  # 引用方式
    
    # 撤销一个变量
    set(myVar)
    unset(myVar)   

1. cmake把所有的变量都当做string处理
2. 使用变量前不需要定义

    # cache变量，存储在CMakeCache.txt里，设置时要指定类型
    set(varName value... CACHE type "docstring" [FORCE])
    
    # bool型cache变量的简便写法，option
    option(optVar helpString [initialValue])
    set(optVar initialValue CACHE BOOL helpString)
    
    
    # 重名的cmake带来的运行结果不一致问题，应避免
    set(myVar foo)                         # Local myVar
    set(result ${myVar})                   # result = foo
    set(myVar bar CACHE STRING “”)         # Cache myVar
    set(result ${myVar})                   # First run: result = bar
                                           # Subsequent runs: result = foo
    set(myVar fred)
    set(result ${myVar})                   # result = fred
    
    # 如何操纵cache变量：命令行或者GUI
    cmake -D foo:BOOL=ON     # 定义变量，并不实用
    cmake -U 'help*' -U foo  # 删除变量，支持通配符
    

调试手段

1. message("The value of myVar = ${myVar}")      打印，用于调试
2. message(SEND_ERROR， "The value of myVar = ${myVar}")     支持指定打印级别
3. variable_watch(myVar [command])    监控对该变量的读写，会记录相应的log， 不常用

string、list and math

- string ，字符串处理的一些特性，包括正则匹配等，比较高级，适合很复杂的项目
- list，对list 的一些列处理
- math，对变量做一些数学操作，高级特性
      set(myList a b c) # Creates the list "a;b;c"
      list(LENGTH myList len)
      message("length = ${len}")
      list(GET myList 2 1 letters)
      message("letters = ${letters}")
      
      set(myList a b c)
      list(APPEND myList d e f)
      message("myList (first) = ${myList}")
      list(INSERT myList 2 X Y Z)
      message("myList (second) = ${myList}")
      
      set(myList a b c d e)
      list(FIND myList d index)
      message("index = ${index}")
      
      list(REMOVE_ITEM myList value [value...])
      list(REMOVE_AT myList index [index...])
      list(REMOVE_DUPLICATES myList)
      
      set(x 3)
      set(y 7)
      math(EXPR z "(${x}+${y}) / 2")
      message("result = ${z}")

控制流

- if
      if(expression1)
      # commands ...
      elseif(expression2)
      # commands ...
      else()
      # commands ...
      endif()
      
      # A quoted string always evaluates to false in CMake 3.1 or later, regardless of the string’s value
      
      # Logical operators
      if(NOT expression)
      if(expression1 AND expression2)
      if(expression1 OR expression2)
      # Example with parentheses
      if(NOT (expression1 AND (expression2 OR expression3)))
  - comparison tests
      Numeric       	String           	Version numbers      
      LESS          	STRLESS          	VERSION_LESS         
      GREATER       	STRGREATER       	VERSION_GREATER      
      EQUAL         	STREQUAL         	VERSION_EQUAL        
      LESS_EQUAL1   	STRLESS_EQUAL1   	VERSION_LESS_EQUAL1  
      GREATER_EQUAL1	STRGREATER_EQUAL1	VERSION_GREATER_EQUAL
  - file system tests
        if(EXISTS pathToFileOrDir)
        if(IS_DIRECTORY pathToDir)
        if(IS_SYMLINK fileName)
        if(IS_ABSOLUTE path)
        if(file1 IS_NEWER_THAN file2)
        
        if(DEFINED name)
        if(COMMAND name)
        if(POLICY name)
        if(TARGET name)
        if(TEST name) # Available from CMake 3.4 onward
  - 
- foreach
      
  
- while


