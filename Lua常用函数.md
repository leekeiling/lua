## Lua常用函数

```
lua_Alloc lua_getallocf (lua_State *L, void **ud);
```

返回给定状态机的内存分配器函数。如果 `ud` 不是 `NULL` ，Lua 把调用[`lua_newstate`](http://www.codingnow.com/2000/download/lua_manual.html#lua_newstate) 时传入的那个指针放入`*ud` 。

```
void lua_getfenv (lua_State *L, int index);
```

把索引处值的环境表压入堆栈。

```
void lua_getfield (lua_State *L, int index, const char *k);
```

把 `t[k]` 值压入堆栈，这里的 `t` 是指有效索引 `index` 指向的值。在 Lua 中，这个函数可能触发对应 "index" 事件的元方法

```
void lua_getglobal (lua_State *L, const char *name);
```

把全局变量 `name` 里的值压入堆栈

```
int lua_getmetatable (lua_State *L, int index);
```

把给定索引指向的值的元表压入堆栈。如果索引无效，或是这个值没有元表，函数将返回 0 并且不会向栈上压任何东西。

```
void lua_gettable (lua_State *L, int index);
```

把 `t[k]` 值压入堆栈，这里的 `t` 是指有效索引 `index` 指向的值，而 `k` 则是栈顶放的值。

这个函数会弹出堆栈上的 key （把结果放在栈上相同位置）。在 Lua 中，这个函数可能触发对应 "index" 事件的元方法。

```
int lua_gettop (lua_State *L);
```

返回栈顶元素的索引。因为索引是从 1 开始编号的，所以这个结果等于堆栈上的元素个数（因此返回 0 表示堆栈为空）。

```
void lua_insert (lua_State *L, int index);
```

把栈顶元素插入指定的有效索引处，并依次移动这个索引之上的元素。不要用伪索引来调用这个函数，因为伪索引不是真正指向堆栈上的位置。

```
int lua_isboolean (lua_State *L, int index);
```

当给定索引的值类型为 boolean 时，返回 1 ，否则返回 0 。

```
int lua_iscfunction (lua_State *L, int index);
```

当给定索引的值是一个 C 函数时，返回 1 ，否则返回 0 。

```
int lua_isfunction (lua_State *L, int index);
```

当给定索引的值是一个函数（ C 或 Lua 函数均可）时，返回 1 ，否则返回 0 。

```
int lua_islightuserdata (lua_State *L, int index);
```

当给定索引的值是一个 light userdata 时，返回 1 ，否则返回 0 。

```
int lua_isnil (lua_State *L, int index);
```

当给定索引的值是 **nil** 时，返回 1 ，否则返回 0 。

```
int lua_isnumber (lua_State *L, int index);
```

当给定索引的值是一个数字，或是一个可转换为数字的字符串时，返回 1 ，否则返回 0 。

```
int lua_isstring (lua_State *L, int index);
```

当给定索引的值是一个字符串或是一个数字（数字总能转换成字符串）时，返回 1 ，否则返回 0 

```
int lua_istable (lua_State *L, int index);
```

当给定索引的值是一个 table 时，返回 1 ，否则返回 0 。

```
int lua_isthread (lua_State *L, int index);
```

当给定索引的值是一个 thread 时，返回 1 ，否则返回 0 。

```
int lua_isuserdata (lua_State *L, int index);
```

当给定索引的值是一个 userdata （无论是完整的 userdata 还是 light userdata ）时，返回 1 ，否则返回 0 。