# Anki 进阶｜如何自动化生成 Anki 卡片？

本文假定读者掌握了 Anki 的基本操作，理解且能够区分下列概念的含义：

* 卡片（Cards）：包含正面信息和背面信息，在学习时首先展示正面信息，由学习者回想背面信息，然后展示背面信息并检验该卡片的掌握程度。
* 卡组（Deck）：一系列相关卡片的集合。
* 字段（Fields）：卡片中各种信息所对应的名称，一张卡片的正面或背面可能包含一个字段，也可能包含多个字段。比如，正面为「英语单词」这一字段，反面为「释义」和「读音」这两个字段。
* 笔记（Notes）：一条笔记包含了所有相关字段，如「英语单词」、「释义」和「读音」。
* 笔记类型（Note Types）：定义了每条笔记包含哪些字段、每条笔记能生成几张卡片，每张卡片的正面和背面具体包含哪些字段。最简单的一种笔记类型「Basic」，包含「Front」和「Back」两个字段，生成一张卡片，卡片的正面是「Front」，背面是「Back」。<del>当然，这听起来像是废话……</del>，但是当笔记类型变得复杂时，我们会发现这样的数据结构能给我们提供很多自由发挥的空间。

## 1 适用场景

Anki 给我们提供了方便好用的添加笔记的交互界面，如下图：

![](http://bearimg.nengbear.com/20181116154233466028675.png)

我们首先指定一种笔记类型和目标卡组，只需要输入一条笔记的各个字段，就能按照笔记类型的要求，在目标卡组生成相应卡片。但是：

1. 如果我们的字段的信息实际上是可以自动生成的，那么我们显然没有必要手动一个一个复制进去；
2. 即使字段需要我们手动输入（比如，把某门课程的课件做成笔记），那么我们也可以先统一输入到一个地方，然后再现统一导入，这有利于维持笔记之间的统一性，且便于修改。

那么，问题来了：

1. Anki 是怎么实现统一导入的？
2. 为了能够统一导入，我们要首先把笔记做成什么样子？

## 2 原理

### 2.1 其实，只需要一个 txt……

按下 `⌘⇧I`（Mac OS）或`^I`（Win） ，我们会发现下面的对话框，提示我们可以导入「由 Tab 分割的文本」。

![](http://bearimg.nengbear.com/20181116154233670619204.png)

事实上，我们只要准备好一份这样的文本，每一行对应一条笔记，并由 Tab 分为各个字段，就能自动生成卡片。当然，在此之前，我们要先定义好一个笔记类型，并新建一个卡组，这样 Anki 才知道我们要把这个文本做成什么样的卡片、放到哪里。

### 2.2 定义笔记类型

![](http://bearimg.nengbear.com/20181116154234285143112.png)

按下 `⌘⇧N`，打开一个这样的对话框，点击 Add，选择 Clone: Basic，也就是复制一个 Basic 类型然后在此基础上改成我们需要的样子，不妨命名为 Test。

![](http://bearimg.nengbear.com/20181116154234283352830.png)

比方说，现在要背英语单词，需要「单词」「释义」「例句」三个字段，那么就点击`Fields`，改成这个样子：

![](http://bearimg.nengbear.com/20181116154234276918503.png)

然后再点击`Cards`，在左边的编辑区内改成这个样子：

![](http://bearimg.nengbear.com/20181116154234274229124.png)

这里要注意的是：

1. 我们通过把字段的名称用双大括号括起来，来表示在卡片上展示相应的字段。
2. 左侧的编辑区实际上写的是 HTML 代码，不过不了解 HTML 也没关系，只需要知道这里边的`<br />`是换行的意思就行了。<del>（我们以后几乎只会用到这一个 HTML 标签……）</del>

### 2.3 制作素材之一：导入文本

这样的话，我们就完成了笔记类型的定义。我们的导入文本大概会做成这个样子：

![](http://bearimg.nengbear.com/20181116154233892042565.png)

这样的话，我们再去刚才那个导入的页面，点击这个文本文件，就出现了下面的对话框：

![](http://bearimg.nengbear.com/20181116154233900240715.png)

检查一下：

1. `Type`是否为我们之前定义好的笔记类型；
2. `Deck`是否为我们的目标卡组；
3. `Field mapping`中文本文件的各个字段是否和我们笔记类型的各个字段对应。

导入之后，就能在相应牌组看到卡片了。

![](http://bearimg.nengbear.com/20181116154233927340369.png)

### 2.4 制作素材之二：媒体文件

现在讲究「多媒体教学」，我们在利用 Anki 学习的过程中也可能会希望添加一些图片或音频辅助我们学习。「百词斩」背单词软件以使用单词对应的图片辅助记忆为特色，假设我们现在也要做类似的事情，添加两个字段分别为图片和音频：

![](http://bearimg.nengbear.com/20181116154233991624548.png)

那么，我们就要在导入文本中也同时加入这个字段。图片和音频的语法分别是：

```
<img src="apple.jpg">
[sound:apple.mp3]
```

添加到导入文本中就是：

![](http://bearimg.nengbear.com/20181116154233998652895.png)

那么，这些图片和音频（统称媒体文件）存放在哪里，才能被这些引用识别到呢？Anki 的媒体文件目录是：

> Mac OS：`~/资源库/Application Support/Anki2/$user/collection.media`（~代表用户的根目录，`$user`表示 Anki 中的用户名称）
>
> Win：`%APPDATA%/Roaming/Anki/$user/collection.media`（`%APPDATA%`代表APPDATA目录，`$user`表示 Anki 中的用户名称）

也就是说，我们先把媒体文件放到这个目录里边，然后导入文本文件，Anki 就能自动引用他们并放到卡片上了！

## 3 编程实现

在上一部分，我们把问题归结为**制作一个导入文本**和**将媒体文件放入 Anki 的媒体文件目录**两件事情。一般来讲，利用 Excel 表格可以便捷地输入含有 Tab 符的文本，然后复制粘贴到 txt 文件中就可以；但是如果文本、媒体文件都是现成的（但不符合导入 Anki 的格式要求），那么我们可能需要写一个脚本来完成下面两件事：

1. 生成一个导入文本；
2. 把媒体文件复制粘贴到 Anki 的媒体文件目录中。

笔者在学习大学中的数学和物理类课程时，常常记录纸质笔记（因为使用 LaTeX 等工具输入公式比较麻烦）。如果能够利用 Anki 复习这些笔记，效果会比直接看笔记更好，原因有二：

1. 间隔重现：按照一定的算法在长的时间跨度（一个学期）中安排多次复习；
2. 主动回想：先提问，进行一定思考再显示相应笔记。

因此，将这些笔记扫描为图片，然后利用 Python 的 PIL 模块对图片进行裁剪并保存到媒体文件目录，同时生成一个导入文本包含了这些图片的题目和图片的引用（也就是 `<img src=...>`）即可。

这一脚本发布在 [GitHub](https://github.com/tansongchen/Automation-with-Python/blob/master/%E4%BB%8E%E6%89%AB%E6%8F%8F%E7%AC%94%E8%AE%B0%E5%88%B0%20Anki%20%E5%8D%A1%E7%89%87/main.py) 上，并有注释具体说明。

## 4 关于 HTML 和 CSS

前面提到 `Cards`的编辑界面其实是编写 HTML 代码。HTML （和 CSS）能够使得一张卡片看起来更为赏心悦目，<del>增加学习的乐趣（x）</del>。

### 4.1 利用 HTML 修饰导入文本

显然，我们可以在导入文本中直接写 HTML 代码。加个粗：

![](http://bearimg.nengbear.com/20181116154234169714558.png)

加个换行：

![](http://bearimg.nengbear.com/20181116154234175653220.png)

什么的都不在话下。

### 4.2 利用 HTML/CSS 修饰卡片

把单词的字号放大一倍：

![](http://bearimg.nengbear.com/2018111615423419064155.png)

文本改成左对齐：

![](http://bearimg.nengbear.com/20181116154234205343045.png)

<del>……反正应该没有什么是 HTML/CSS 干不了的。</del>

当然也不用搞的太花哨了，毕竟还是内容为主嘛（笑）。