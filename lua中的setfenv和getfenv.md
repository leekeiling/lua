## lua中的setfenv和getfenv

#### 设置函数环境——setfenv

　　当我们在全局环境中定义变量时经常会有命名冲突，尤其是在使用一些库的时候，变量声明可能会发生覆盖，这时候就需要一个非全局的环境来解决这问题。setfenv函数可以满足我们的需求。

　　setfenv(f, table)：设置一个函数的环境

　　（1）当第一个参数为一个函数时，表示设置该函数的环境

　　（2）当第一个参数为一个数字时，为1代表当前函数，2代表调用自己的函数，3代表调用自己的函数的函数，以此类推

　　所谓函数的环境，其实一个环境就是一个表，该函数被限定为只能访问该表中的域，或在函数体内自己定义的变量。下面这个例子，设定当前函数的环境为一个空表，那么在设定执行以后，来自全局的print函数将不可见，所以调用会失败。

```
-- 一个环境就是一个表，该表记录了新环境能够访问的全部域
newfenv = {}
setfenv(1, newfenv)
print(1)        -- attempt to call global `print' (a nil value)
```

　　我们可以这样继承已有的域：

```
a = 10
newfenv = {_G = _G}
setfenv(1, newfenv)
_G.print(1)        -- 1
_G.print(_G.a)        -- 10
_G.print(a)        -- nil 注意此处是nil，新环境没有a域，但可以通过_G.a访问_G的a域
```

　　可以看到，新环境中可以访问_G，但有一点就是_G中的所有函数必须手动调用，这样其实很不方便。我们可以使用metatable来对上述代码进行改进：

```
-- 任何赋值操作都对新表进行，不用担心误操作修改了全局变量表。另外，你仍然可以通过_G修改全局变量：
newfenv = {}
setmetatable(newfenv, {__index = _G})
setfenv(1, newfenv)
print(1)        -- 1 新环境直接继承了全局环境的所有域，好处：可以不需要通过_G来手动调用
```

　　这样，当访问到函数中不存在的变量时，会自动在_G中查找。对于当前函数和_G都存在的变量，可以通过是否用_G显示调用来区分，比如如果有两个a，那么_G.a表示继承来的，a就是当前函数环境的。

　　另外，可以通过getfenv(f)函数查看函数所处的环境，默认会返回全局环境_G。