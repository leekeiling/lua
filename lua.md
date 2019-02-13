## 深入函数

函数是一种第一类值，具有热顶的词法域。

**第一类值**：表示lua中函数与其他传统类型的值具有相同的权利。函数可以存储到变量中或他变了中，可以作为实参传递给其他函数，还可以作为其他函数返回值。

**词法域**：一个函数可以嵌套在另一个函数中，内部的函数可以访问外部函数中的变量。

## Closure（闭合函数）

closure：一个closure就是一个函数加上该函数所需访问的所有 “非局部的变量”。 

## 非全局函数

注意定义函数的前向声明。

将函数存储在table中：

```lua
Lib = {}
Lib.foo = function(x, y) return x + y end
Lib.goo = function(x, y) return x - y end 
```

定义local函数

```lua
local foo
foo = function(x, y) return x + y end
```

## 尾调用

一个函数的最后一个动作是调用另一个函数，程序不需要返回哪个尾调用所在的函数了，所以尾调用后，程序也不需要保存任何关于该函数的栈信息。

## 迭代器

迭代器就是一种可以遍历一种集合中所有元素的机制。在Lua中，通常将迭代器表示为函数。每调用一次函数，即返回集合中的下一个元素。

一个closure结构通常涉及到两个函数：closure本身和一个用于创建该closure的工厂函数。

一个简答迭代器：

```lua
function value(t)
	local i = 0
	return function() i = i + 1; return t[i] end
end
```

## 泛型for的语义

保存了迭代器函数，恒定状态和控制变量。可以通过泛型for自身来保存迭代状态。

## 无状态的迭代器

自身不保存任何状态的迭代器。因此，可以在多个循环中使用同一个无状态的迭代器，避免创建新的closure开销。

## 具有复杂状态的迭代器

1、使用closure。

2、将迭代器所需的所有状态打包为一个table，保存在恒定状态中。

## 真正的迭代器

迭代器接收一个函数作为参数，并在其内部的循环中调用这个函数。



## 解释型语言

区别解释型语言的主要特征并不在于是否能编译它们，而是在于编译器是否是语言运行时库的一部分，即是否有能力执行动态生成的代码。

## 编译

loadstring总是在全局环境总编译它的字符串。loadstring最典型的用处是执行外部代码，也就是那些位于程序之外的代码。loadstring的期望输入是一个程序块，如果需要对一个表达式求值，则必须在其之前添加return，这样才能构成一条语句。

load则接收一个 “读取函数”，并在内部调用它来获取程序快。读取器函数可以分几次返回一个程序快，load会反复调用它，直到返回nil为止。用在程序快不在文件中，或者程序快过大而无法放入内存时，才会用到它

load只是将程序块编译为一种中间表示，然后将结果作为一个匿名函数来返回。常见的误解是认为加载了一个程序快，也就是定义了其中的函数。其实在lua中，函数定义是一种赋值操作。也就是说，它们是在运行时才完成的操作。

## C代码

Lua为几种平台实现了一套动态链接机制。

Lua提供的所有关于动态链接的功能都聚集在一个函数中，即package.loadlib。该函数有两个字符串参数：动态库的完成路径和一个函数名称。

```lua
local path = "/usr/local/lib/lua/5.1/socker.so"
local f = package.loadlib(path, "luaopen_socket") 
```

loadlib函数加载指定的库，并将其链接入lua。不过，它并没有调用库中的任何函数。想法，它将一个C函数作为lua函数的返回。如果在加载库或 查找初始化函数时发生任何错误，返回nil即一条错误消息。



## 错误

当一个函数遭遇了一种未预期的状况，可以采取两种基本的行为：返回错误代码（通常是nil）或引发一个错误（error）。易于避免的异常应引发一个错误，否则返回一个错误代码。

## 错误处理与异常

使用error来抛出一个异常或使用pcall来捕获异常，而错误消息则可以标识出错误的类型或内容。

使用pcall来包装需要执行的代码。任何类型的lua值都可以作为错误消息传递给error函数，并且这些值也会成为pcall的返回值。

## 错误消息与追溯

当遇到一个内部错误时，lua就会产生错误消息。而其他时候，错误消息就是传递给error函数的值。只要错误消息是一个字符串，lua就会附加一些关于错误发生位置的消息。

xpcall函数除了接收一个需要被调用的函数之外，还接收第二个参数———一个错误处理函数。当发生错误时，Lua会在调用栈展开之前调用错误处理函数。于是就可以在这个函数中使用debug库来获取额外的信息了。

## 协同程序

 类似于线程，是一条执行序列。拥有自己独立的栈，局部变量和指令指针，同时又与其他协同程序共享全局变量和其他大部分东西。

一个具有多个协同程序的程序在任意时刻只能运行一个协同程序，并且在运行的协同程序只会在其显式要求挂起时，它的执行才会暂停。

## 协同程序基础

状态：挂起、运行、死亡和正常

创建是处于挂起状态。

resume用于启动或再次启动一个协同程序的执行。resume是在保护模式中运行的。因此，如果在一个协同程序的执行中发生错误，Lua是不会显示错误，而是将执行权返回个resume调用。

当一个协同程序A唤醒一个协同程序B时，A处于正常状态。

#### **P75** 

## 管道（pipe）与过滤器（filter）

当一个程序调用yield时，它不是进入了一个新函数，而是从一个悬而未决的调用中返回。同样的，对于resume的调用也不会启动一个新的函数，而是从一次yield调用中返回。

生产者与消费者问题

```lua
function receive (prod)
	local status, value = coroutine.resume(prod)
	return value
end

function send(x)
	coroutine.yield(x)
end

function producer ()
	return corroutine.create(function ()
	 	while true do
		 local x = io.read()  --产生新值 
		 send(x)
		end
	  end)
end

function filter (prod)
	return coroutine.create(function ()
		for line = 1, math.huge do
			local x = receive(prod)
			x = string.format("%5d%s", line, x)
			send(x)	--将新值发送给消费者 
		end
	end)
end

function consumer(prod)
	while true do
		local x = receive(prod)  --获取新值
		io.write(x, "\n")
	end
end 
```



## 以协同程序实现迭代器

协同程序可以改变传统的调用者与被调用者之间关系。

coroutine.wrap：唤醒协同程序。缺乏灵活性，无法检查wrap所创建的协同程序的状态，此外，无法检测出运行时的错误。

**p80**

## 非抢占式的多线程

```lua
function download(host, file)
	local c = assert(socket.connect(host, 80))
	local count = 0 --记录接收到的字节数
	c:send("GET"..file.."HTTP/1.0\r\n\r\n")
	while true do
		local s,status, partial = receive(c)
		count = count + #(s or partial)
		if status == "closed" then break end
	end
	c:close()
	print(file, count)

function receive (connection)
	connection:settimeout(0)  --reveive调用不会阻塞
	local s, status, partial = connection:receive(2^10)
	if status == "timeout"	then
		coroutine.yield(connection)
	end
	return s or partial, status 
end 
 
 function get (host, file)
    --创建协同程序
   	local co = coroutine.create(function ()
      	download(host, file)
    end)
    --将其插入记录表中
    table.insert(threads, co)
 end
  
 function dispatch()
  local i = 1
  while true do                 
   	if threads[i] == nil then     --还有线程吗 
	   if threads[1] == nil then break end  --列表是否为空       
	   i = 1   --重新开始循环 
	end
	local status, res = coroutine.resume(threads[i])
	if not res then
		table.remove(threads, i)
	else
		i = i + 1
	end
  end
end 
```

解决上述程序的忙等待情况

使用LuaSocker中的select函数。这个函数可以用于等待一组socket的状态改变，在等待时程序陷入阻塞（block）状态。若要在当前实现中应用这个函数，只需修改调度程序即可。

```lua
function dispatch()
	local i = 1
	local connections = {}
	while true do
		if threads[i] == nil then --还有线程吗？
			if threads[l] == nil then break end
			i = 1      --重新开始循环 
			connections = {}
		end
		local status,res = corroutine.resume(threads[i])
		if not res then  --线程是否已经完成了任务 
			table.remove(threads, i)
		else   --超时 
			i = i + 1
			connections[#connections + 1] = res
			if #connections == #threads then  --所有线程都阻塞了吗 
				socket.select(connections)
			end
		end
	end
end 
```

receive 会将超时的连接通过yield传递，也就是resume会返回它们。如果所有的连接都超时了，调度程序就调用select来等待这些连接状态发生变化。

## 数组

数组没有一个固定的大小，可以根据需要增长。

## 矩阵与多维数组

创建二维数组

```lua
mt = {}
for i=1, N do
mt[i] = {}
	for j=1, M do
		mt[i][j] = 0
	end
end 
```



**pairs：**

## 链表

## 图表

```lua
local function name2node (graph, name)
	if not graph[name] then

		--结点不存在，创建一个新的
		graph[name] = {name = name, adj={}}
	end
	return graph[name]
end

function readgraph()
	local graph = {}
	for line in io.lines() do
		--切分行中的两个名称
		local namefrom, nameto = string.match(line, "(%S+)%s+(%S+)")
		--查找相应的结点
		local from = name2node(graph, namefrom)
		local to = name2node(graph, nameto)
		--将 to 添加到 from的邻接集合
		from.adj[to] = true
	end
	return graph
end 
```

## 数据文件

可借由table构造式来定义一种文件格式，即将数据作为lua代码来输出，当运行这些代码时，读取数据就会变得相当容易。而table构造式可以使这些输出代码看上去更像是普通的数据文件。

```lua
Entry
{
	"Donald E, Knuth",
	"CSLI",
	1992
}

Entry
{
	"Jon",
	"Addison",
	1990
}

local count = 0
function Entry (_) count = count + 1 end
dofile("data")
print("number of entries: "..count)
```

这些代码片段采用了事件驱动的做法。Entry函数作为一个回调函数，在dofile时为数据文件中的每个条目所调用。

## 串行化

将数据转换为一个字节流或字符流。然后就可以将其存储到一个文件中，或者通过网络连接发送出去了。串行化后的数据可以用代码来表示，这样当运行这些代码时，存储的数据就可以在读取程序中得到重构。

```lua
function serialize (o)
	if type(o) == "number"	then
		io.write(o)
	elseif type(o) == "string"	then
		io.write(string.format("%q", o))
	else
	end
end
```

## 保存无环的table

```lua
function serialize(o)
	if type(o) == "number" then
		io.write(o)
	elseif type(o)== "string" then
		io.write(string.format("%q", o))
	elseif type(o)	== "table" then
		io.write("{\n")
		for k, v in pairs(o) do
			io.write(" ", k, " = ")
			serialize(v)
			io.write(",\n")
		end
		io.write("}\n")
	else
		error("cannot serialize a "..type(o))
	end
end
```

## 保存有环的table

## p112

## 元表与元方法

通过元表修改一个值的行为，使其在面对一个非 预定义的操作时执行一个指定的操作。

任何table都可以作为任何值的元表，而一组相关的table也可以共享一个通用的元表，此元表描述了它们共同的行为。一个table甚至可以作为它自己的元表，用于描述其特有的行为。

## 算术类的元方法

```lua
Set = {}

--根据参数列表的值创建一个新的集合
function Set.new(l)
	local set = {}
	setmetable(set, mt)  --将mt设置为当前所创建的table的元表 
	for _, v in ipairs(l) do set[v] = true end
	return set
end

function Set.union(a, b)
	if getmetable(a) ~= mt or getmatatable(b) ~= mt then
		error("attempt to 'add' a set with a non-set value") 
	local res = Set.new()
	for k in pairs(a) do res[k] = true end
	for k in pairs(b) do res[k] = true end
	return res
end

mt.__add = Set.union  --将元方法加入元表中。这个元方法就是用
					  --描述如何完成加法的__ add字段 

 
```

## 关系类的元方法

元表可以指定关系操作符的含义，元方法为__eq(等于)、__it（小于） 和 __le（小于等于）

## 库定义的元方法

__tostring

__metatable 是用于既看不到页无法修改元表

## table访问的元方法

有两种可以改变的table行为的方法：查询table及修改table中不存在的字段

__index元方法：当访问一个table中不存在的字段时，得到结果为nil。实际上，这些访问会促使解释器去查找一个较 _index的元方法。

```lua
Window = {} --创建一个名字空间
--使用默认值来创建一个原型
Windows.prototype = {x=0, y=0, width=100, height=100}
Window.mt = {} --创建元表
function Window.new(o)
	setmetatable(o, Window.mt)
	return o
end

Window.mt.__index = function(table, key)
	return Window.prototype[key]
end 
```

在这段代码之后，创建一个新窗口，并查询一个它没有的字段：

```lua
w = Window.new{x=10, y=20}
print(w.width)  --> 100 
```

## _ _newindex 元方法

用于table的更新

## 具有默认值的table

```lua
function setDefault (t, d)
	local mt = {_ _index = function () return d end}
	setmetatable(t, mt)
end

tab = {x=10, y=20}
print(tan.x, tab.z)  --> 10 nil
setDefault(tab, 0)
print(tab.x, tab.z)  -->10 0
```

```lua
local mt = {_ _index = function () return t._ _ _ end}
function setDefault (t, d)
	t._ _ _ = d
	setmetatable(t, mt)
end
```

## 跟踪table的访问

P123

## 具有动态名字的全局变量

P126

## 全局变量声明

```lua
setmetatable(_G, {
	--newindex = function (_, n)
	error("attempt to write to undeclared variable"..n, 2)
	end,
	--index = function(_, n)
	error("attempt to read undeclared variable"..n, 2)
	})
	
	function declare (name, initval)
		rawset(_G, name, initval or false)
	end
```

```lua
--newindex = function(t, n, v)
	local w = debug.getinfo(2, "S").what
	if w ~= "main" and w~= "C" then
		error("attempt to write to undecalred variable"..n, 2)
	end
	rawset(t, n, v)
end
```

```lua
local decalreNames = {}

setmetatable(_G, {
	--newindex = function (_, n)
	if not declaredNames[n] then
		local w = debug.getinfo(2, "S").what
		if w ~= "main" and w ~= "C" then
			error("attempt to write to undeclared variable"..n, 2)
		end
		declaredNames[n] = true
	end
	rawset(t, n, v)  --完成实际的设置 
	end,
	--index = function(_, n)
		if not decalredNames[n] then 
			error("attempt to read undeclared variable"..n, 2)
		else
			return nil
		end
	end,
	})
	
	function declare (name, initval)
		rawset(_G, name, initval or false)
	end
```

## 非全局的环境

通过setfenv来改变一个函数的环境。该函数的参数是一个函数和一个新的环境table。

函数继承了创建其函数的环境。所以一个程序块若改变了它自己的环境，那么后续由它创建的函数都将共享这个新环境。这项机制对于创建名称空间是很有用的。

## 模块与包

## require函数

P134

## 编写模块的基本方法

创建一个table，并将所有需要导出的函数放入其中，最后返回这个table。

```lua
local modname = ...
local M = {}
_G[modname] = M
package.loaded[modname] = M

function M.new(r, i)	return {r=r, i=i}	end

--定义一个常量'i'
M.i = M.new(0, 1)

function M.add(c1, c2)
	return M.new(c1.r + c2.r, c1.i + c2.i)
end

return M;
```

## 使用环境

```
local modname = ...
local M = {}
_G[modname] = M
package.loaded[modname] = M
setmetable(M, {__index = _C})
setfenv(1, M)
```

```lua
local modname = ...
local M = {}
_G[modname] = M
package.loaded[modname] = M

--导入段
--声明这个模块从外界需要的东西

local sqrt = math.sqrt
local io = io

--在这句之后就不需要外部访问了
setfenv(1, M) 
```

## module 函数

## 子模块与包

支持具有层级性的模块名，可以用一个点来分隔名称中的层级。

## 面向对象编程

```lua
Account = {balance = 0}

function Account:new(o)
	o = o or {}
	setmetatable(o, self)
	self.--index = self
	return o
end

function Account : withdraw (v)
	if v > self.balance then error"insufficient funds"	end
	self.balance= self.balance - v
end

function Account:deposit(v)
	self.balance = self.balance + v
end

SpecialAccount = Account:new()

function SpecialAccount:withdraw(v)
	if v - self.balance >= self.getLimit() then
		error"insufficient funds"
	end
	self.balance = self.balance - v
end

function SpecialAccount:getLimit ()
	return self.limit or 0
end

s = SpecialAccount:new{limit=1000.00}
```

## 多重继承

```lua
-- 在table 'plist'中查找 'k'

local function search(k, plist)
	for i=1, #plist do
		local v = plist[i][k]
		if v then return v end
	end
end

function createClass(...)
	local c = {} --新类
	local parents = {...}

--类在其父类列表的搜索方法
setmetatable(c, {--index = function(t, k)
	return search(k, parents)
	end})

--将'c'作为其实例的元素
c.--index = c
--为这个新类定义一个新的构造函数
function c:new (o)
	o = o or {}
	setmetatable(o, c)
	return o
end

return c --返回新类
end

Named = {}
function Named:getname ()
	return self.name
end

function Named:setname (n)
	self.name = n
end

--要创建新类同时继承Account和Named
NamedAccount = createClass(Account, Named) 

account = NamedAccount:new(name = "paul")
```

## 私密性

## 单一方法做法

## 弱引用table

## 备忘录函数

## 对象属性

## 数学库

## table库

## table排序

```lua
function pairsByKeys (t, f)
	local a = {}
	for n in pairs(t) do a[#a + 1] = n end
	table.sort(a, f)
	local i = 0 --迭代器变量
	return function () --迭代器函数
		i = i  + 1
		return a[i], t[a[i]]
	end
end 
```

## table连接

```lua
function rconcat (l)
	if type(l) != "table" then return l end
	local res = {}
	for i=1, #l do
		res[i] = rconcat(l[i])
	end
	return table.concat(res)
end 
```

## C API

```c++
#include <stdio.h>
#include <string.h>
#include "lua.h"
#include "lauxlib.h"
#include "lualib.h"

int main (void) {
	char buff[256];
	int error;
	lua_State *L = luaL_newstate(); /*打开Lua */
	luaL_openlibs(L); /* 打开标准库*/ 
	
	while (fgets(buff, sizeof(buff), stdin) != NULL) {
		error = luaL_loadbuffer(L, buff, strlen(buff), "line")
		|| lua_pcall(L, 0, 0, 0);
		if(error){
			fprintf(stderr, "%s", lua_tostring(L, -1));
			lua_pop(L, 1); /*从栈中弹出错误消息 */ 
		}
	} 
	lua_close(L);
	return 0; 
}
```



## 压入元素：

Void lua_push* (lua_State *L);

## 查询元素

```lua
lua_to * （lua_State *L, int index）;

const char* lua_tolstring(lua_State *L, int index, size_t *len)

lua_type

```


lua_tolstring 函数返回一个指向内部字符串副本的指针，并将字符串的长度存入最后一个参数len中。Lua保证只要这个对应的字符串值还在栈中，那么这个指针就是有效的。当Lua调用了
不要在C函数之外使用在C函数内获得的指向Lua字符串的指针
其他栈操作：

```lua
Int lua_gettop(lua_State *L); 返回栈顶元素

void lua_settop(lua_State *L, int index);   将栈顶设置为一个指定的位置

void lua_pushvalue(lua_State*L, int index); 指定索引上值的副本压入栈中

void lua_remove (lua_State *L, int index); 删除指定索引上的元素，栈所有元素下移一填补空缺。

void lua_insert(lua_State *L, int index); 上移指定位置之上的所有元素开辟一个槽空间，然后将栈顶元素移动到该位置。

void lua_replace (lua_State *L, int index); 弹出栈顶元素，并将改制设置到指定索引上。

```

## C API的错误处理

当编写库代码（被lua调用的C函数）时，使用longjmp几乎和使用异常处理机制一样方便，Lua会捕获所有可能的错误。而当编写应用程序代码（调用Lua的C代码）时，必须提供一种捕获错误的方式。

## 应用程序代码中的错误处理

调用lua_pcall来运行Lua代码。因而这些lua代码也都是运行在保护模式中的。如果发生了内存分配的错误，lua_pcall会返回一个错误代码，并将解释器封固在一致的状态。如果要保护那些与lua交互的C代码，使用lua_cpcall。

## 库代码的错误处理

当一个C函数检测到一个错误时，它就应该调用lua_error。lua_error函数会清理lua中所有需要清理的东西，然后跳转回发起执行的那个lua_pcall，并附上一条错误信息。

## 扩展应用程序基础

```c++
--定义窗口大小
width = 200
height = 300

void load(lua_State *L, const char *fname, int *w, int *h) {
	if(luaL_loadfile(L, fname) || lua_pcall(L, 0, 0, 0))
		error(L, "cannot run config. file: %s", lua_tostring(L, -1));
		lua_getglobal(L, "height");
		if(!lua_isnumber(L, -2))
			error(L, "width should be a number\n");
		if(!lua_isnumber(L, -1))
			error(L, "height should be a number\n");
		*w = lua_tointeger(L, -2);
		*h = lua_tointeger(L, -1);
} 
```

loadfile从文件fname加载程序块，然后调用lua_pcall运行编译好的程序块。若发生错误，这两个函数都会把错误消息压入栈，并返回一个非零的错误代码。

## table操作

```c++
lua_getglobal(L, "background");
if(!lua_istable(L, -1))
	error(L, "background is not a table");

red = getfield(L, "r");
green = getfield(L, "g");
blue = getfield(L, "b");

#define MAXCOLOR 255

/*假设table位于栈顶 */
int getfield(lua_State *L, const char *key) {
	int result;
	lua_pushstring(L, key);
	lua_gettable(L, -2); /*获取background[key] */
	if (!lua_isnumber(L, -1))
		error(L, "invalid component in background color");
		result = (int)lua_tonumber(L, -1)* MAX_COLOR;
		lua_pop(L, 1); /*删除数字*/
		return result; 
		 
} 
```

## 调用Lua函数

调用函数的API协议很简单。首先，将待调用的函数压入栈，并压入函数的参数。然后，使用lua_pcall进行实际的调用。最后，将调用结果从栈中弹出。

一个通用的调用函数
p228

## 从Lua调用C

对于一个能被Lua调用的C函数，它必须遵循一个获取一个参数和返回结果的协议。此外，还必须注册C函数，以便用某种适当的方式将函数地址告诉Lua。也使用了一个与C语言调用Lua时相同的栈。


如果发生内存不足，一个程序所能做的最佳处理就是结束运行。

## C模块

Lua通过注册过程记录下C函数，然后使用这些函数地址直接调用它。
一个为Lua编写的C模块可以模仿Lua的用table存储函数的行为。除了C函数的定义外，C模块还必须定义一个特殊的函数，这个函数相当于一个Lua模块中的主程序块。它应该注册模块中所有的C函数，并将它们存储在一个适当的地方。并且，这个函数还应初始化模块中所有需要初始化的东西。

## 编写C函数的技术

## 数组操作


void lua_rawgeti(lua_State *L, int index, int key);
void lua_rawseti(lua_State *L, int index, int key);

## 字符串操作

当一个C函数从Lua收到一个字符串参数时，必须遵守两条规则：不要在访问字符串时从栈中弹出它，不要修改字符串。

lua_pushlstring(L, s + i, j - i +1); 提取一个字符串s的字串（区间为[i, j]）传递给Lua。
lua_concat(L, n) 连接并弹出栈顶的n个值，然后压入结果。
const char* lua_pushfstring(lua_State *L,  const char *fmt,  ...); 根据一个格式字符串和一些额外的参数来创建一个新的字符串。


第一层类似于I/O操作中缓冲，就是一个本地缓冲中收集较小的字符串，并在本地缓冲填满之后将结果传递给Lua。
第二层使用lua_concat或其他的栈算法来连接多次缓冲填满后的结果。

 void luaL_buffinit (lua_State *L, luaL_Buffer *B);
初始化缓冲
 void luaL_addchar (luaL_Buffer *B, char c);
 void lual_addlstring (luaL_Buffer *B, const char *s, siz_t l);
 void luaL_addstring (luaL_Buffer *B, const char *s);
向缓冲添加字符串或者字符
 void luaL_pushresult (luaL_Buffer *B);
更新缓冲，并将最终的字符串留在栈顶。
void luaL_addvalue (luaL_Buffer *B);  
将栈顶的值加入缓冲

## 在C函数中保存状态

对于lua函数来说，有3中地方可以存放费局部的数据，它们时全局变量，函数环境和非局部变量（closure）中。

C API提供3种地方来保存状态：注册表、环境、和upvalue

## 注册表

注册表总是位于一个 “伪索引”上，伪索引就像是一个栈中的索引，但它所关联的值不在栈中。

lua_getfield(L, LUA_REGISRYINDEX, “Key”);

注册表是一个普通的lua table, 可以用任何Lua值来索引它。所有的C模块共享同一个注册表，为了避免冲突，必须谨慎地选择key的值。  

通用唯一标识符

在注册表中不应使用数学类型的key， 因为这种key是被“引用系统”所保留的 。这个系统是辅助库的一系列函数组成，它可以在向一个table存储value时，忽略如何创建唯一的key。

luaL_ref(L, LUA_REGISTRYINDEX); 创建唯一的key。

会从栈中弹出一个值，然后用一个新分配的整数key来将这个值保存到注册表中，最后返回这个整数key。这个key被称为“引用”。

释放值和引用:  lual_unref(L,  LUA_REGISTRYINDEX, r);

另一种创建注册表key的方法是，使用代码中静态变量的地址，C连接器可以确保这种key在所有库中的唯一性。 

## C函数的环境

在Lua中注册的所有C函数都有自己的环境table。一个函数可以像访问注册表那样，通过一个伪索引来访问它的环境table。环境table的伪索引是LUA_ENVIRONINDEX
使用这些环境的方法与在Lua模块中使用环境的方法差不多。就是为模块创建一个新的table，然后使模块中的所有函数都共享这个table。

int luaopen_foo(lua_State *L) {
	lua_newtable(L);
	lua_replace(L, LUA_ENVIRONINDEX);
	luaL_register(L, <库名>, <函数列表>)
}

## upvalue

相当于函数的closure

注册表提供了全局变量的存储，环境提供了模块变量的存储，而upvalue机制则实现了一种类似于C语言中静态变量的机制，这种变量只在一个特定的函数中可见。每当在Lua中创建一个函数时，都可以将任意数量的upvalue与这个函数相关联。每个upvalue都可以保存一个Lua值。以后，在调用这个函数时，就可以通过伪索引来访问这些upvalue了。

```c++
static int counter (lua_State *L); /* 前向声明 */

int newCounter(lua_State *L) {
	lua_pushinteger(L, 0);
	lua_pushcclosure(L, &counter, 1);
	return 1;
}

static int counter (lua_State *L) {
	int val = lua_tointeger(L, lua_upcalueindex(1));
	lua_pushinteger(L, ++var); /* 新的值 */
	lua_pushvalue(L, -1); /*复制它*/
	lua_replace(L, lua_upvalueindex(1)); /*更新upvalue*/
	return 1； 
}
 
```



元组是一种具有匿名字段的常量记录。可以用一个数字索引来检索某个字段，或者一次性检索所有字段。在实现中，将元组表示为函数，元组的值存储在函数的upvalue中。当用以数字参数来调用此函数时，它会返回指定的字段。当不用参数来调用此函数时，则返回所有字段。

```lua
int t_tuple(lau_State *L) {
	int op = luaL_optint(L, 1, 0);
	if (op == 0) { /* 无参数？ */ 
		int i;
		/* 将所有合法的upvalue压入栈中 */
		for (i = 1; !lua_isnone(L, lua_upvalueindex(i)); i++)
			lua_pushvalue(L, lua_upvalueindex(i));
		return i - 1; /* 栈中值的数量 */ 
	}
	else { /* 获取'op'字段 */ 
		luaL_argcheck(L, 0 < op, 1, "index out of range");
		if (lua_isnone(L, lua_upvalueindex(op)))
			return 0; /* 无此字段 */
		lua_pushvalue(L, lua_upvalueindex(op));
		return 1; 
	} 
} 

int t_new (lua_State *L) {
	lua_pushcclosure(L, t_tuple, lua_gettop(L));
	return 1;
}

static const struct luaL_Reg tuplelib [] = {
	{ "new", t_new},
	{NULL, NULL}
}

int luaopen_tuple (lua_State *L) {
	luaL_register(L, "tuple", tuplelib);
	return 1;
}
```

## 用户自定义类型

## userdata

userdata提供了一块原始的内存区域，可以用来存储任何东西。并且，在Lua中，userdata没有任何预定义的操作。

函数lua_newuserdata会根据指定大小分配一块内存，并将对应的userdata压入栈中，最后返回这个内存块的地址。

需要通过其他机制来分配内存。那么可以创建只有一个指针大小的userdata，然后将指向真正内存块的指针存入其中。

```c
static int newarray (lua_State *L) {
	int i, n;
	size_t nbytes;
	NumArray *a;
	
	n = luaL_checkint(L, 1);
	luaL_argcheck(L, n>=1, 1, "invalid size");
	nbytes = sizeof(NumArray) + I_WORD(n-1)* sizeof(unsigned int);
	
	a->size = n;
	for(i =0; i <= I_WORD(n-1); i++)
		a->value[i] = 0;
	return 1; 
}
```

```c
static int setarray (lua_State *L) {
	NumArray *a = (NumArray *)lua_touserdata(L, 1);
	int index = luaL_checkint(L, 2) - 1;
	luaL_checkany(L, 3);
	
	luaL_argcheck(L, a != NULL, 1, "array expected");
	
	luaL_argcheck(L, 0 <= index && index < a->size, 2, "index out of range");
	if(lua_toboolean(L, 3))
		a-values[I_WORD[index]] != I_BIT(index); /*设置bit */
	else
		a->value[I_WORD[index]] &= ~I_BIT(index); /*重置bit */
	return 0; 
}
a = array.new(1000)
print(a)
for i=1,1000 do
	array.set(a, i, i%5 == 0)
end 
```



## 元表

一种辨别不同类型的userdata的方法是，为每种类型创建一个唯一的元表。每当创建了一个userdata后，就用相应的元表来标记它。而每当得到一个userdata后，就检查它是否拥有正确的元表。

另外还需要有个地方来存储这个新的元表，然后才能用它来创建新的userdata，并检查给定的userdata是否具有正确的类型。在Lua中，通常习惯将所有新的C类型注册到注册表中，以一个类型名作为key，元表作为value。

```lua
int luaL_newmetatable (lua_State *L, const char *tname);

void luaL_getmetatable(lua_State *L, const chat *tname);
void *luaL_checkudata (lua_State *L, int index, const char *tname); 
```

luaL_newmetatable函数会创建一个新的table用作元表，并将其压入栈顶，然后将这个table与注册表指定名称关联起来。It  does a dual association: It uses the name as a key to the table and the table as a key to the name. 

luaL_getmetatable可以在注册表中检索与tname关联的元表

luaL_checkudata 可见检测栈中指定位置上是否为一个userdata，并且是否具有与给定名称相匹配的元表。

```lua
static int newarray (lua_State *L) {
	int n = luaL_checkint(L, 1);
	size_t nbytes = sizeof(NumArray) + (n-1) * sizeof(double);
	NumArray *a = (NumArray *) lua_newuserdata(L, nbytes);
	
	luaL_getmetatable(L, "LuaBook.array");
	lua_setmetatable(L, -2);
	
	a->size = n;
	return 1;
	
} 
```

lua_setmetatable function pops a table from the stack and sets it as the metatable of the object at the given index. In this case, this object is the new userdatum.

## 面向对象的访问

在C语言中，实现上述效果可以用数组。这里数组是一种具有操作的对象，可以无须在table array中保存这些操作。程序员只要导出一个用于创建新数组的函数new就可以，所有其他操作都可以作为对象的方法。C代码同样可以直接注册它们的方式。

首先需要设置两个独立的函数列表，一个用于常规的函数，另一个用于方法。

新的打开的函数luaopen_array必须创建元表，并将它赋予--index字段，然后在元表中注册所有方法，最后创建并填充array table。

```lua
static const struct luaL_Reg arraylin_f [] = {
	{"new", newarray},
	{NULL, NULL}
} ;

static const struct luaL_Reg arraylib_m [] = {
	{"set", setarray},
	{NULL, NULL}
};

int luaopen_array (lua_State *L) {
	luaL_newmetatable(L, "LuaBook.array");
	/*元表.--index = 元表 */
	lua_pushvalue(L, -1); /*复制元表 */
	lua_setfield(L, -2, "--index");
	
	luaL_register(L, NULL, arraylib_m);
	
	luaL_register(L, "array", arraylib_f);
	return 1; 
}
```

## 数组访问

另一种面向对象写法是使用常规的数组访问写法。相对于写a：get(i)，可以简单地写为a[i]。

```c++
static const struct luaL_Reg arraylib_f [] = {
	{"new", newarray},
	{"--newindex", setarray},
	{"--index", getarray},
	{"--len", getsize},
	{"--tostring",array2string},
	{NULL, NULL}
};


int luaopen_arrray (lua_State *L) {
	luaL_newmetatable(L, "LuaBook.array");
	luaL_register(L, NULL, arraylib_m);
	luaL_register(L, "array", arraylib_f);
	return 1;
}
```

## 轻量级userdata 

表示一个指针，使用轻量级userdata时用户必须自己管理内存，因为轻量级userdata不属于垃圾收集的范畴。

轻量级userdata的真正用途是相等性判断。可以将轻量级userdata用于查找Lua中的C对象。

## 管理对象

优势一个对象需要使用原始内存之外的其他资源，例如文件描述符、窗口句柄及其他类似的东西。在这种情况中，当一个对象成为垃圾被收集后，这些类型的资源也都必须被释放。可以通过元方法--gc来指定终结函数。这个元方法只能对userdata值有效。

## 目录迭代器

需要3个C函数。首先是这个dir函数，它是个工厂，Lua调用ta来创建迭代器。它还必须打开一个DIR结构，并将其作为迭代器函数的upvalue。其次，需要这个迭代器函数。最后是--gc元方法，用于关闭DIR结构。通常，还需要一个额外的函数，用于进行初始化工作，例如为目录创建一个元表初始化元表。

## XML解释器

```c++
/* 回调函数的前向声明 */
static void f_StartElement (void *ud,
							const char* name,
							const char **atts);
							
static void f_CharData(void *ud, const char*s, int len);
static void f_EndElement (void *ud, const char *name);

static void lxp_make_parse (lua_State *L) {
	XML_Parse p;
	lxp_userdata *xpu;
	
	/* (1) 创建一个分析器对象*/
	xpu = (lxp_userdata *)lua_newuserdata(L,
		sizeof(lxp_userdata));
		
	/*预先初始化它，以备错误情况 */
	xpu->parser = NULL;
	
	/*设置它的元表*/
	luaL_getmetatable(L, "Expat");
	lua_setmetatable(L, -2);
	
	/* 创建expat分析器 */
	p = xpu->parse = XML_ParseCreate(NULL);
	if(!p)
		luaL_error(L, "XML_ParseCreate failed");
	
	/* (3) 检查并保存回调table */
	luaL_checktype(L, 1, LUA_TTABLE);
	lua_pushvalue(L, 1) /*压入table的副本 */
	lua_setfenv(L, -2); /*将它设为userdata的环境 */
	
	/* (4) 配置Expat分析器 */
	XML_SetUserData(p, xpu);
	XML_SetElementHandler(p, f_StartElement, f_EndElement);
	XML_SetCharacterDataHandler(p, f_CharData);
	return 1; 
} 
```

## I/O库

I/O库文件提供了两种不同模式：简单模型和完整模型。简单模型假设有一个当前输入文件和一个当前输出文件，它的I/O操作均作用于这些文件。完整模型则使用显式的文件句柄。它采用了面向对象的风格，并将所有的操作定义为文件句柄上的方法。



## 简单I/O模型









