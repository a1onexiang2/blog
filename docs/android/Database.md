[Home](../../README)

# Android

## Database

#### LitePal
LitePal 仅需要配置 `litepal.xml` 文件就可以声明数据库版本、数据库名称、需要 ORM 的类等，实体类继承 `LitePalSupport`（旧版本为 `DataSupport`）后即可对类所对应的表执行增、删、改操作，查询操作则可以通过静态语句方法来实现。
需要注意的是，LitePal 只是对 SQLite 的封装，在多进程/多线程/高并发的情况下有较大概率操作失败。并且由于内部使用了 Reflection，在高强度下会有效率问题。

#### GreenDao
GreenDao 在数据库的第三方库中性能是最好的。它会在编译器预先生成部分代码，在运行期不依赖反射。

#### Realm
Realm 是跨平台的数据库方案，不基于 SQLite，实现了自己的存储引擎。它允许在持久层直接和数据对象工作。

[Home](../../README)