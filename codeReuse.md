# 代码复用

## 代码复用几条准则

### Keep the code DRY. DRY means "Don't repeat yourself"

编写代码时，意识到某块逻辑出现重复时，就应该考虑复用**而不是去复制**了。复制过来的代码逻辑，会使得重复的逻辑维护成本变高不少（最直接地修改多个相同逻辑的地方造成遗漏）。**任何时候，复制代码都是最差的实践**。

### Make a class/method do just one thing.

使得模块、方法只做一件事。
举个最常见的例子。
