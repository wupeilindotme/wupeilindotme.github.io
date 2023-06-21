---
title: Markdown学习
date: 2023-06-21 17:49:14
tags: .md
---



# Markdown 学习

## 背景

无论是使用<a href="https://hexo.io/zh-cn/" target="_blank">Hexo</a>、<a href="https://gohugo.io/" target="_blank">Hugo</a>或是<a href="https://www.jekyll.com.cn/" target="_blank">Jekyll</a>搭建自己的博客写博文，还是用来编写文档，markdown语法的使用必不可少。 在本地可以下载 <a href="https://typoraio.cn/" target="_blank">Typora</a> — 一款支持markdown(md) 语法的编辑器， 可以更快速、更效率的编写md文档，这里就不过多介绍。  但如果是想在github 直接进行文档编写，那最好是要熟悉一下md语法。

另外，如果需要编写docx \ pdf 文档， 也可以使用markdown， 只需编写完成后用插件转换即可。



**以下是正题**



## 语法

### 标题

可以看到本文的一些标题，就是用md语法实现的

- Typora中快捷键  `Ctrl`  `1` `2` `3` `4` `5` `6`  为对应的标题 

- 示例代码

```markdown
# 这是一级标题
## 这是二级标题
### 这是三级标题
#### 这是四级标题
##### 这是五级标题
###### 这是六级标题
```

​	当然你也可以使用html，得到的效果是一样的

```html
<h1>这是一级标题</h1>
<h2>这是一级标题</h2>
<h3>这是一级标题</h3>
<h4>这是一级标题</h4>
<h5>这是一级标题</h5>
<h6>这是一级标题</h6>
```

- 效果如下

  ![image-20230621234551156](markdown-learn/image-20230621234551156.png)

### 字体

- 加粗

  - Typora快捷键： `Ctrl`  `B`

  - 示例代码 （在文本两侧加 **）

    ```markdown
    **我变粗了**
    ```

- 斜体

  - Typora快捷键： `Ctrl`  `I`

  - 示例代码 （在文本两侧加 *）

    ```markdown
    *我变斜了*
    ```

- 斜体加粗

  - 示例代码 （在文本两侧加 ***）

    ```markdown
    ***我又斜又粗***
    ```

- 删除

  - 快捷键： `Alt` `Shift` `5`  

  - 示例代码 （在文本两侧加 ~~）

    ~~~~
    ~~我被删除了~~
    ~~~~

- 效果如下

  **我变粗了**

  *我变斜了*

  ***我又粗又斜***

  ~~我被删除了~~



### 代码块

- 示例代码 <br/>

  使用 ``` 加任意可识别的编程语言 

  ```markdown
  ​```java
  	public static void main(String[] args) {
  		System.out.println("Hello Peilin");
  	}
  ​```
  ```

  ```markdown
  ​```javascript
      function hello() {
      	console.log("Hello Peilin")
      }
  ​```
  ```

  

- 效果

  ```java
  public static void main(String[] args) {
      System.out.println("Hello Peilin");
  }
  ```

  ```javascript
  function hello() {
      console.log("Hello Peilin")
  }
  ```



### 分割线

- 代码 <br/>

  使用 三个以上的 --- 或 ***， 效果都是一样的

  ```markdown
  ***
  ****
  ---
  ----
  ```

  

### 图片

```markdown
![图片alt]['图片url' '图片名称']

如

![]
```



