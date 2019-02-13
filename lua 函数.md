### lua 函数

**lua_pushclosure(L, &function, key)**; 创建一个新的closure。其中第二个参数是个基础函数，第三个参数是upvalue的数量。upvalue位于栈顶。

**lua_setglobal(L, key);** 将栈顶的元素传到Lua环境中作为全局变量

**lua_getglobal(L, key);** 从Lua环境中取得全局变量压入栈顶。

**luaL_newmetatable(L, const char *tname);** 函数会创建一个新的table用作元表，并将其压入栈顶，然后将这个table与注册表指定名称关联起来。

**luaL_getmetatable(lua_State *L, const chat *tname);** 可以在注册表中检索与tname关联的元表

**int luaL_newmetatable (lua_State *L, const char *tname);**可见检测栈中指定位置上是否为一个userdata，并且是否具有与给定名称相匹配的元表。

**lua_setmetatable (L, key)** 从栈中弹出一个table，然后将它设置为指定索引下对象的元表。

**XML_Parser XML_ParseCreate(const char *encoding);** 创建XML分析器

**void XML_ParseFree(XML_Parse p)；** 销毁XML分析器

**void lua_getfenv (lua_State *L, int index) ** 把索引处值的环境表压入堆栈。

**void lua_getfield(lua_State *L, int index, const char *k);** 把 `t[k]` 值压入堆栈，这里的 `t` 是指有效索引 `index` 指向的值。在 Lua 中，这个函数可能触发对应 "index" 事件的元方法（参见 [§2.8](http://www.codingnow.com/2000/download/lua_manual.html#2.8)）。


**void lua_gettable (lua_State *L, int index);** 把 t[k] 值压入堆栈， 这里的 t 是指有效索引 index 指向的值， 而 k 则是栈顶放的值。这个函数会弹出堆栈上的 key （把结果放在栈上相同位置）。 在 Lua 中，这个函数可能触发对应 "index" 事件的元方法 

**void lua_settable (lua_State *L, int index);**
作一个等价于 t[k] = v 的操作， 这里 t 是一个给定有效索引 index 处的值， v 指栈顶的值， 而 k 是栈顶之下的那个值。这个函数会把键和值都从堆栈中弹出。 和在 Lua 中一样，这个函数可能触发 "newindex" 事件的元方法 而lua_rawget和lua_rawset分别类似于lua_gettable和lua_settable


**void lua_rawset (lua_State *L, int index);**类似于 lua_settable， 但是是作一个直接赋值（不触发元方法）。

**void lua_rawget (lua_State *L, int index);**类似于 lua_gettable， 但是作一次直接访问（不触发元方法）。


**void lua_rawgeti (lua_State *L, int index, int n);**把 t[n] 的值压栈， 这里的 t 是指给定索引 index 处的一个值。 这是一个直接访问；就是说，它不会触发元方法。

**void lua_rawseti (lua_State *L, int index, int n);**等价于 t[n] = v， 这里的 t 是指给定索引 index 处的一个值， 而 v 是栈顶的值。函数将把这个值弹出栈。 赋值操作是直接的；就是说，不会触发元方法。
