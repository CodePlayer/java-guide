# java-guide
A practical Java development guide, positioned as a supplement to the Alibaba Java Development Manual. It is recommended to use both together.

#### SQL字段拼接优化

```java
// ID、NAME、STATUS、EMAIL、MERCHANT_ID 都是提前定义好的字符串常量
final String prefix = "t.";
var wrapper = new QueryWrapper<User>()
        .eq(prefix + ID, query.getId())
        .eq(prefix + NAME, query.getName())
        .eq(prefix + STATUS, query.getStatus())
        .eq(prefix + EMAIL, query.getEmail())
        .eq(prefix + MERCHANT_ID, query.getMerchantId());
```

进行 **多表联查** 时，需要对每个表设置别名（例如 `... FROM t_user AS t JOIN t_role r ...`），建议 **主表** 的别名统一使用 `t`，声明前缀时，务必记得给 `prefix` 变量加 `final`。
1. 不加 `final`，默认就会在每次执行时进行两个字符串的动态拼接。加了 `final` 后，利用编译器优化，可以直接将 `prefix + ID` 直接预编译为 `"t.id"` 常量，避免了动态拼接的额外开销。
2. 统一使用前缀 `t`，这样方便复制其他表的SQL再进行修改。此外，对于同一个字段，字符串常量池都是 `"t.id"`，而不是 `"a.id"`、`"b.id"`、`"c.id"` 等多种形式，避免增加常量池的空间占用。
