[Home](../../README.md)

# Java

## JVM

#### Class
JVM 无关平台与 CPU 架构的原因是统一使用了字节码格式 Class 文件。Class 文件是一组以 8 位字节为基础单位的二进制流，按照顺序紧凑排列，没有任何分隔符。u1，u2，u4，u8 分别代表 1，2，4，8 个字节，整个 Class 文件本质上就是一张表，由下列部分组成。

类型 | 名称 | 数量
-- | -- | --
u4 | magic | 1
u2 | minor_version | 1
u2 | major_version | 1
u2 | constant_pool_count | 1
cp_info | constant_pool | constant_pool_count - 1
u2 | access_flags | 1
u2 | this_class | 1
u2 | super_class | 1
u2 | interfaces_count | 1
u2 | interfaces | interfaces_count
u2 | fields_count | 1
field_info | fields | fields_count
u2 | methods_count | 1
method_info | methods | methods_count
u2 | attributes_count | 1
attribute_info | attributes | attributes_count

- **魔数与版本**
前 4 个字节是魔数，是 Class 文件的标识，值为 0xCAFEBABE。第 5、6 个字节是次版本号，第 7、8 个字节是主版本号，主版本号从 0x30 开始，对应 JDK 1.1 版本，0x31 对应 JDK 1.2 版本，以此类推。
- **常量池**
常量池计数从 1 开始，主要存放两大类常量：字面量（Literal）和符号引用（Symbolic References）。字面量即代码中的常量概念，符号引用包括：类和接口的全限定名、字段的名称和描述符、方法的名称和描述符。
- **类索引、父类索引、接口索引集合、字段表集合、方法表集合、属性表集合**
具体查表。


[Home](../../README.md)