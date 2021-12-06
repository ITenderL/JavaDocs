# Java基础

## 基础知识总结

### Java 常见的关键字

***

| 分类                 | 关键字   |            |          |              |            |           |        |
| -------------------- | -------- | ---------- | -------- | ------------ | ---------- | --------- | ------ |
| 访问控制             | private  | protected  | public   |              |            |           |        |
| 类，方法和变量修饰符 | abstract | class      | extends  | final        | implements | interface | native |
|                      | new      | static     | strictfp | synchronized | transient  | volatile  |        |
| 程序控制             | break    | continue   | return   | do           | while      | if        | else   |
|                      | for      | instanceof | switch   | case         | default    |           |        |
| 错误处理             | try      | catch      | throw    | throws       | finally    |           |        |
| 包相关               | import   | package    |          |              |            |           |        |
| 基本类型             | boolean  | byte       | char     | double       | float      | int       | long   |
|                      | short    | null       | true     | false        |            |           |        |
| 变量引用             | super    | this       | void     |              |            |           |        |
| 保留字               | goto     | const      |          |              |            |           |        |

### 基本数据类型

***

### Java 中的基本数据类型？各自占用多少字节？对应的包装类型？

Java 中有 8 种基本数据类型，分别为：

1. 6 种数字类型 ：`byte`、`short`、`int`、`long`、`float`、`double`
2. 1 种字符类型：`char`
3. 1 种布尔型：`boolean`。

| 基本类型 | 位数 | 字节 | 默认值  |
| :------: | :--: | :--: | :-----: |
|   byte   |  8   |  1   |    0    |
|  short   |  16  |  2   |    0    |
|   int    |  32  |  4   |    0    |
|   long   |  64  |  8   |   0L    |
|  float   |  32  |  4   |   0f    |
|  double  |  64  |  8   |   0d    |
| boolean  |  1   |      |  false  |
|   char   |  16  |  2   | 'u0000' |

