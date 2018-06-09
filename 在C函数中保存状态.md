## 在C函数中保存状态

对于lua函数来说，有3中地方可以存放非局部的数据，它们时全局变量，函数环境和非局部变量（closure）中。

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

```
lua_newtable(L);
lua_replace(L, LUA_ENVIRONINDEX);
luaL_register(L, <库名>, <函数列表>)
```

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

## 