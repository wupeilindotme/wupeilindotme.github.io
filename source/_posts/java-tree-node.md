---
title: Java实现树形接口
date: 2023-07-05 16:33:32
tags: 
- Java
- 后端
- 技巧分享
---





# Java实现树形接口

## 前言

做后端开发，遇到树形结构的情况可以说是非常多。菜单、部门机构等都会用到。 这种经常遇见且通用的功能无需针对每个业务去写冗余代码，本文就介绍一下如何实现一个通用的树形结构，将集合树形化。

**注**：JDK版本需要在1.8以上



## 思想

1. 在关系型数据库表中实现树形父子级关系，通常是取一个字段存父结点的id。
2. 在代码中则是在数据结构中定义当前结点id、父级id 和当前结点的子结点集合。
3. 只需要知道当前结点的id，父结点id，当前结点的子结点集合，就可以使用**递归**（Recursion）进行树形构造。



## 代码

### 接口

```java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

/**
 * <T> your pojo class
 * 树形结构工具， pojo类实现 TreeNode 即可
 */
public interface TreeNode<T extends TreeNode<T>> {

    Integer getId();

    Integer getParentId();

    List<T> getChildren();

    void setChildren(List<T> children);

    /**
     * 递归构建树。
     * @param root 当前根结点
     * @param list 待遍历的结点
     */
    default void recursion(T root, List<T> list) {
        if (root == null) {
            return;
        }
        for (T n : list) {
            if (n.getParentId() != null && n.getParentId().equals(root.getId())) {
                if (root.getChildren() == null) {
                    root.setChildren(new ArrayList<>());
                }
                root.getChildren().add(n);
                n.recursion(n, list);
            }
        }
    }

    /**
     * 将传入的 list 树形化
     * @return 根节点集合
     */
    static <T extends TreeNode<T>> List<T> treeify(List<T> list) {
        // 筛出根结点, 这里定义parentId为null的结点为根结点
        List<T> roots = list.stream().filter(x -> x.getParentId() == null).collect(Collectors.toList());
        roots.forEach(x -> x.recursion(x, list));
        return roots;
    }

}

```



### 实现/示例

- 实现

```java
import lombok.AllArgsConstructor;
import lombok.Data;

import java.util.ArrayList;
import java.util.List;

// 模拟部门数据
@Data
@AllArgsContructor
public class DeptDTO implements TreeNode<DeptDTO> {
    private Integer id;
    private Integer parentId;
    private List<DeptDTO> children;
	
    // other fields
	
}
```

 Tips: 这里DeptDTO实现了TreeNode接口，需要实现接口的方法； 接口的定义与lombok生成的getter / setter正好对应。 做到了一句多的代码也不写！  当然了，如果你的字段名称与接口不同，实现一下就行了， 如下

```java
import java.util.ArrayList;
import java.util.List;

public class Menu implements TreeNode<Menu> {
    private Integer menuId;
    private Integer parentId;
    private List<Menu> subMenus;
	
    private String menuName;
    // other fields
	
    @Override
    public Integer getId() {
        return this.menuId;
    }
    
    @Override
    public Integer getParentId() {
        return this.parentId;
    }
    
    @Override
    public List<Menu> getChildren() {
        return this.subMenus;
    }
    
    @Override
    public void setChildren(List<Menu> children) {
        this.subMenus = children;
    }
}
```

- 测试调用

```java
public class TestTreeNode {
    public void testTreeify() {
        List<DeptDTO> nodes = new ArrayList<>();
        nodes.add(new DeptDTO(1, null, null));
        nodes.add(new DeptDTO(2, 1, null));
        nodes.add(new DeptDTO(3, 1, null));
        nodes.add(new DeptDTO(4, 2, null));
        nodes.add(new DeptDTO(5, 2, null));
        List<DeptDTO> roots = TreeNode.treeify(nodes);
    }
}
```





## 总结

通过将自己的业务中的pojo类实现TreeNode， 指定出该类的id 、 parentId、子集合，即可便捷使用



---



***Life Oriented Programming***

