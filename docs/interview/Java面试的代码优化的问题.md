# 一次真实的Java面试的代码优化的问题

### 面试官：以下代码存在什么问题，以及怎么优化？

```java
package com.itender.leecode.interview;

import java.util.List;

/**
 * @Author: ITender
 * @CreateTime: 2022-02-22 16:40
 * @Description: 面试代码优化问题
 */
public class Interview1 {

    @Autowried
    private Mapper mapper;
    /**
     * 假设list中有上万个元素，找出代码问题给出优化方案
     *
     * @param args
     */
    public static void main(String[] args) {
        List<Object> list = null;
        Object o1 = new Object();
        Object o2 = new Object();
        list.add(o1);
        list.add(o2);
        if (list.size() > 0) {
            for (Object o : list) {
                XxxDao xxxDao = mapper.select();
                o.setXxxDao(xxxDao);
            }
        }
        mapper.batchSave();
    }
}

```

我答：

1. 不要在循环中操作DB，可能会耗尽数据库连接。
2. 不要用list.size() > 0 判断集合，要用CollectionUtils的工具类进行判断。
3. `mapper.batchSave()`数据库写操作，要加上事务的处理。

面试官：还有吗？

我：目前就看出了这些。

面试官：我告诉你还有哪些问题。

1. list没有实例化，下面的add()操作会报空指针。
2. xxxDao没有做非空判断，也有可能报空指针。

### 总结

1. 不要在循环中操作DB，可能会耗尽数据库连接。
2. 不要用list.size() > 0 判断集合，要用CollectionUtils的工具类进行判断。
3. `mapper.batchSave()`数据库写操作，要加上事务的处理。
4. list没有实例化，下面的add()操作会报空指针。
5. xxxDao没有做非空判断，也有可能报空指针。

##### 每天进步一点...........