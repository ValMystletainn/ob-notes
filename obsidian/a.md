---
created: 2022-11-20 15:21
aliases: []
tags:
---

# activity history

插件代码库: [https://github.com/Darakah/obsidian-activity-history](https://github.com/Darakah/obsidian-activity-history)

显示(默认是当前库但可以添加修改成子库)的活动监视，类似github的activity history 命令

使用方法: markdown代码块，把语言设置为ActivityHistory， 代码内容写需要监视的路径名。

> 语法和惯例略有修改, `/` 表示全仓库，直接目录名(不加任何`/`, `./` 表示子目录或仓库 )
> 如果需要跟踪其他项目，需要在插件设置里添加修改追踪路径

```ActivityHistory
/
```

# admonition

插件代码库: [https://github.com/valentine195/obsidian-admonition](https://github.com/valentine195/obsidian-admonition)

显示更多更丰富的material版式的文本块信息

使用方法: markdown 代码块，语言设置为ad-<type>, 以及可设置title, color, icon, collapse 等效果，且支持块块嵌套，常用type和效果，部分地列举如下


## note

collapse open 的 ad note

``` ad-note
title: ad note style
collapse: open

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod nulla.
```

## abstract

(255,0,0)的红色abstract块

``` ad-abstract
color: 255, 0, 0
```

## tip

``` ad-tip

```

## question

用`~~~`嵌套内层note的question

```ad-question
nested-outer part
~~~ad-note
nested-inner part
~~~
```



## bug
用`~~~`嵌套真实代码块的bug
```ad-bug
~~~javascript
throw new Error("Oops, I'm a bug.");
~~~
```


## example

```ad-example
```
## quote

```ad-quote
```

# Advanced Tables

插件代码库: [https://github.com/tgrosinger/advanced-tables-obsidian](https://github.com/tgrosinger/advanced-tables-obsidian)

增强常规markdown语法下的表格的编辑效率
用`|` + 第一行第一列内容，就可以让插件认为在编辑表格，然后用`Tab` 和 `Shift` + `Tab`就可以在同行上下切换单元格/新建列，用`Enter`换行/新建行。还有其他游泳但低频的功能可以查看readme

|      |     |     | test |
| ---- | --- | --- | ---- |
| test |     |     |      |
| test |     |     |      |


