### Lua数据结构

#### 数组

整数索引table即可实现数组。数组没有固定大小，可以根据需要增长。

#### 矩阵与多维数组

1、数组的数组。

2、两个索引合并为一个索引。

#### 链表

每个结点以一个table来表示，一个“连接”只是结点table中的一个字段，该字段包含了对其他table的引用。

```lua
list = {next = list, value = v}
```

#### 队列

在lua中实现队列的简单方法是调用table中insert和remove函数，但是如果数据量较大的话，效率还是很慢的。一种更高效的方法是实现两个索引，分别用于首尾的两个元素：

```
List = {}
function ListNew ()
	return {first = 0, last = -1}
end
```

#### 集合与无序组

在Lua中用table实现集合是非常简单的，见如下代码：

```lua
    reserved = { ["while"] = true, ["end"] = true, ["function"] = true, }
    if not reserved["while"] then
        --do something
    end
```

#### Stringbuild

如果在lua中将一系列字符串连接成大字符串的话，有下面的方法：

低效率：

```lua
local buff=""
for line in io.lines() do
   buff=buff..line.."\n"
end
```

高效率：

```lua
local t={}

for line in io.lines() do
   if(line==nil) then break end
   t[#t+1]=line
end

local s=table.concat(t,"\n")  --将table t 中的字符串连接起来
```

