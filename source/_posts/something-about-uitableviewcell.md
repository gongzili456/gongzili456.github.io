title: 关于 UITableViewCell 的二三事
date: 2016-01-06 23:14:27
tags: UITableViewCell
---

UITableView 使用 UITableViewCell 对象来绘出那些显示的 rows，并将之缓存。 Table View 的 data source 通过 tableView:cellForRowAtIndexPath 方法向 UITableView 提供 UITableViewCell 对象。

### 典型的 Cell 对象

一个 Cell 对象包含许多部分，通常会包含文本（text），图片（image） 等。

下图为一种普通（normally）状态下的 Cell
![](http://7xpuna.com1.z0.glb.clouddn.com/tv_cell_parts.jpg)

右边的小区域是附件视图（`accessory views`），左边为内容区（`content view`）当需要添加视图到 Cell 的时候，就需要将视图添加的 Cell 的 `contentView` 属性上。

下图为编辑模式下的一种示例
![](http://7xpuna.com1.z0.glb.clouddn.com/tv_cell_parts_edit.jpg)

`editing control` 可以是 `deletion control` 和 `insertion control` 的一种；

### 使用预定义风格的 Cell

iOS 预置了4种风格的 Cell

```
    UITableViewCellStyleDefault,
    UITableViewCellStyleValue1,
    UITableViewCellStyleValue2,
    UITableViewCellStyleSubtitle
```

下图为一种常见的 Cell 风格
![](http://7xpuna.com1.z0.glb.clouddn.com/tv_cell_parts_simple.jpg)


### 自定义 Cell


### 使用 StoryBoard 加载 Cell

### 总结