---
title: Vim实用技巧
date: 2016-07-23 09:57:12
updated: 2016-07-23 09:57:21
categories: [Reading, Vim实用技巧]
tags: [Vim]
---

[Vim实用技巧](http://pan.baidu.com/s/1dFujR4d)是一本好书:)。

<!-- more -->

### 第1章 Vim解决问题的方式

简化重复性操作

- `.` 重复上次修改

`x`删除光标下的字符，结合`.`重复上次修改，就可以让Vim删除光标下的字符。

`dd`删除行，结合`.`可继续删除行

`>G`增加当前行的缩进级别

`j`调转到下一行，结合`.`可重复操作（好像有点不管用）

> 从进入插入模式的那一刻起（输入`i`），到返回普通模式为止（输入`<Esc>`，或`Ctrl+[`），Vim会记录每一个按键操作。做出这样一个修改再用`.`命令的话，它就会重新执行所有这些按键操作。

`.`是一个微型的宏

- 不要自我重复

对于行尾添加内容如分号这种操作，可合并为一步。

如将如下代码行尾添加分号：

```
var foo=1
var bar='a'
var foobar=foo+bar
```

每行执行`$a;<Esc>`即可，`$`可移位至行尾，`a`在光标后添加。可用`.`替换`a;<Esc>`。

减少无关的移动，`a`在光标后添加，`A`则在行尾添加。

类似命令：

|复合命令|等效长命令|
|:---|:---|
|C|c$|
|s|cl|
|S|^c|
|I|^i|
|A|$a|
|o|`A<CR>`|
|O|ko|

- 以退为进

将下面的代码中的加号两边添加空格

```
var foo = "method("+argument1+","+argument2+")";
```

```
f+s␣+␣<Esc>;.;.;.
```

`f+`找到第一个加号，`s`删除插入，输入`␣+␣`，退出`<Esc>`，`;`寻找下一个加号，`.`重复上次操作。

- 执行、重复、回退

对于`.`命令，可用`u`撤销；对于`f{char}`，`;`正向查找，`,`反向查找

|目的|操作|重复|回退|
|:---|:---|:---|:---|
|做出一个修改|`{edit}`|`.`|`u`|
|行内查找下一指定字符|`f{char}`/`t{char}`|`;`|`,`|
|行内查找上一指定字符|`F{char}`/`T{char}`|`;`|`,`|
|文档中查找下一处匹配项|`/pattern<CR>`|`n`|`N`|
|文档中查找上一处匹配项|`?pattern<CR>`|`n`|`N`|
|执行替换|`:s/target/replacement`|`&`|`u`|
|执行一系列修改|`qx{changes}q`|`@x`|`u`|


- 查找并手动替换

将下文中`content（内容）`替换成`copy`。

We're waiting for content before the site can go live...
If you are content with this, let's go ahead with it...
We'll lauch as soon as we have the content...

若使用如下命令：

:%s/content/copy/g

则将替换所有`content`，并不是所想要的，第二个`content`是不需要删除的。

可使用如下命令：

`/content<CR>cwcopynn.`

`cw`删字插入，`nn`跳转到第三个重复操作。


- 结识`.`范式

一键移动，一键执行

## 第一部分 模式（mode）

### 第2章 普通模式（normal mode）

Vim的自由放松状态

- 停顿时请移开画笔

- 把撤销单元切成块

可以看做`i{insert some text}<Esc>`是一次修改。

> 在插入模式中移动光标会重置修改状态
> 在插入模式中`<Up>`、`<Down>`、`<Left>`或`<Right>`这些光标键，将会产生一个新的撤销块。等同于退出插入模式、进入普通模式一次，并使用h（左移一个字符）、j（下一行）、k（上一行）或l（右移一个字符）对光标进行了移动。同时，这也会对`.`产生影响。

- 构造可重复的修改

|按键操作|说明|
|:---|:---|
|`db`|反向删除字，保留最后一个字符，可用`x`删除|
|`dw`|正向删除字|
|`daw`|delete a word，删除光标所在的字和相邻的空格，**常用**|

`b`移至字首

- 用次数做简单的算术运算

大多数普通模式命令可以在执行时指定次数。

`[count]<C-a>`和`[count]<C-z>`可对数字做加减操作，`count`表示加减数，默认为1，并自动正向查找第一个数进行操作。

下面是CSS片段，现在需要复制最后一行，然后用news替换blog，并将第一个0px改成-180px。

```
.blog, .news { background-image: url(/sprite.png); }
.blog { background-position: 0px 0px }
```

`yypcW.news<Esc>180<C-x>`

先将光标移至第二行，`yy`复制第二行，`p`粘帖至第三行，`cW`删除光标字符及下一个空格前所有字符，`180<C-x>`将第一个0减至-180

- 能够重复，就别用次数

删除下面文字中的`more than`

```
Delete more than one word
```

有三种方式：d2w、2dw、dw.

- 双剑合璧，天下无敌

Vim的强大很大程度上源自操作符和动作命令相结合。

**操作符 + 动作命令 = 操作**

d{motion} 命令可以对字符`dl`、一个完整单词（`dw`、`daw`)或整个段落（dap）进行操作。`c{motion}`删除插入、`y{motion}`复制以及其他一些命令

`g~`、`gu`和`gU`命令

`gUaw`把当前单词转换成大写形式（好像不行）

Vim的操作符命令

|命令|用途|
|:---|:---|
|c|修改（删除插入）|
|d|删除|
|y|复制到寄存器|
|g~|反转大小写|
|gu|转换为小写|
|gU|转换为大写|
|>|增加缩进|
|<|减小缩进|
|=|自动缩进|
|!|使用外部程序过滤（motion）所跨越的行|

### 第3章 插入模式

- 在插入模式中个即时更正错误

插入模式下的按键操作

|按键操作|用途|
|:---|:---|
|`<C-h>`|删除前一个字符（同退格键）|
|`<C-w>`|删除前一个字|
|`<C-u>`|删至行首|

- 返回普通模式

|按键操作|用途|
|:---|:---|
|`<Esc>`|切换到普通模式|
|`<C-[>`|切换到普通模式|
|`<C-o>`|切换到**插入-普通模式**|

结识**插入-普通模式**

- 不离开插入模式，粘帖寄存器中的文本

重新映射大小写转换键（Caps Lock），很多Vim用户把它当成另一个键用，比如`<Esc>`或`<Ctrl>`。

有如下内容，要求将书名插到最后一行。

```
Practical Vim, by Drew Neil
Read Drew Neil's
```

`yt<C-r>0`这好像不行

`yt`是将书名复制到复制专用寄存器中，`<C-r>0`把刚复制的文本粘帖到光标所在位置。

一般格式：`<C-r>{register}`，`register`表示寄存器名

`<C-r><C-p>{register}`会按原义插入寄存器内的文本，并修正任何不必要的缩进。

- 随时随地做运算

表达式寄存器可用来执行一段Vim脚本，并返回结果。

`<C-r>=3+5<CR>`（好像也不行）

- 用字符编码插入非常用字符

`<C-v>{code}`，如插入`A`，可输入`<C-v>065`，code十进制最多包含3位数字，且不超过255，若插入的编码超过3位数，可使用十六进制，`<C-v>u{code}`，十六进制最多包含四位，如`<C-v>u1234`。查看文档中字符的编码，只需将光标移至上面，使用`ga`命令。

`<ÿ> 255, Hex 00ff, Octal 377`

Unicode基本多文种平面（Unicode Basic Multilingual Plane）的地址空间最大会有65545个字符。

|按键操作|用途|
|:---|:---|
|`<C-v>123`|以十进制字符编码插入字符|
|`<C-v>u1234`|以十六进制字符编码插入字符|
|`<C-v>{nondigit}`|按原义插入非数字字符|
|`<C-k>{char1}{char2}`|插入以二合字母`{char1}{char2}`表示的字符|

- 用二合字母插入非常用字符

这个不懂

如`<C-k>?I`插入`¿`，`<C-k>12`插入`½`。

- 用替换模式替换已有文本

在替换模式（Replace mode, REPLACE）中输入会替换文档中的已有文本，除此之外，该模式与插入模式完全相同。

将如下两句话给成一句话，第二句首字母小写。

```
Type in Insert mode extends the line. But in Replace mode
the line length doesn't change.
```

`f.R,␣b<Esc>`

`R`进入替换模式，`<Insert>`键也可。

用虚拟替换模式（Virtual replace, VREPLACE)替换制表符，制表符在文件中以单个字符表示，在屏幕中却占据若干列的宽度，替换模式中会被单个字符替换。虚拟替换模式中会将制表符当成一组空格进行处理，即按屏幕上实际显示的宽度替换字符。

`gR`进入虚拟替换模式

`r{char}`和`gr{char}`为单次版本的替换模式和虚拟替换模式修改。

### 第4章 可视模式

Vim有3种不同的可视模式（Visual mode），分别用于操作字符文本、行文本或块文本。

- 深入理解可视模式


当需要将`March`改成`April`时，有2种方式：

1. 先选中`March`，删除，插入`April`，可视模式中使用这种方式
2. 选中`March`，直接修改成`April`，替换模式中始终这种方式

`viwcApril`

`viw`选中字，`c`删除字，插入`April`

`<C-g>`可以在可视模式及选择模式（Select mode）间切换。

- 选择高亮选区

激活可视模式

|命令|用途|
|:---|:---|
|`v`|激活面向字符的可视模式`Visual`|
|`V`|激活面向行的可视模式`Visual Line`|
|`<C-v>`|激活面向列块的可视模式`Visual Block`|
|`gv`|重选上次的高亮选区|

在可视模式间切换

|按键操作|用途|
|:---|:---|
|`<Esc>`/`<C-[>`|回到普通模式|
|`v`/`V`/`<C-v>`|可视模式回到普通模式|
|`o`|切换到高亮选区的活动端|

切换选区的活动端，高亮选区的范围有两个端点界定。一端固定，一端随光标自由移动，按`o`即可切换。。

- 重复执行面向行的可视命令

对下面的代码中第四行、第五行进行缩进：

```
def fib(n):
    a, b = 0, 1
    while a < n:
print a,
a, b = b, a+b
fib(42)
```
`Vj>.`

`V`进入面向行可视视图，`j`选择跳至第二行，`>`进行缩进，`.`重复操作

设置缩进长度

```
:set shiftwidth=4 softtabstop=4 expandtab
```

- 只要可能，最好用操作符命令，而不是可视命令

将如下链接文字转换为大写格式：

```html
<a href="#">one</a>
<a href="#">two</a>
<a href="#">three</a>
```

普通模式下：

`gUitj.j.`

`gU`转换为大写，`it`是一种被称为文本对象（text object）的特殊动作命令。`j.j.`重复操作。

此处不可使用可视模式`vitUj.j.`，它只能操作等长字符。

- 面向列块的可视模式编辑表格数据

```
Chapter Page
Normal mode 15
Insert mode 31
Visual mode 44
```

这节不懂。

- 修改列文本

将下面代码中`images`目录改成`components`：

```css
li.one      a{ background.image: url('/images/sprite.png'); }
li.two      a{ background.image: url('/images/sprite.png'); }
li.three    a{ background.image: url('/images/sprite.png'); }
```

`<C-v>jjeccomponents<Esc>`

插入单词`components`时，单词只出现在顶行，下面两行无变化，回到普通模式后，会发现刚输入的文本出现在下面两行里。

- 在长短不一的高亮后添加文本

在下面每行代码后添加分号：

```JavaScript
var foo = 1
var bar = 'a'
var foobar = foo + bar
```

`<C-v>jj$A;<Esc>`

`$`把选区扩大到每行的行尾。

Vim对切换至插入模式命令`i`和`a`的约定：

|操作命令|说明|
|:---|:---|
|`i`|光标置字符前|
|`a`|光标置字符后|
|`I`|光标置行首|
|`i`|光标置行尾|



### 第5章 命令行模式

- 结识Vim的命令行模式

命令行模式会提示我们输入一条Ex命令、一个查找模式，或一个表达式

可以用Ex命令读写文件（`:edit`和`:write`），创建新标签页（`:tabnew`）及分割窗口（`:split`），或是操作参数列表（`:prev/:next`）及缓冲区列表（`:bprev/:bnext`）。

操作缓冲区文本的Ex命令：

|命令|用途|
|:---|:---|
|`:[range]delete [x]`|删除指定范围内的行[到寄存器x中]|
|`:[range]yank [x]`|复制指定范围的行[到寄存器x中]|
|`:[line]put [x]`|在指定行后粘帖寄存器x中的内容|
|`:[range]copy {address}`|把指定范围内的行拷贝到{address}所指定的行之下|
|`:[range]move {address}`|把指定范围内的行移动到{address}所指定的行之下|
|`:[range]join`|连接指定范围内的行|
|`:[range]normal {commands}`|对指定范围内的每一行执行普通模式命令{commands}|
|`:[range]substitute/{pattern}/{string}/[flags]`|把指定范围内出现{pattern}的地方替换为{string}|
|`:[range]global/{pattern}/[cmd]`|对指定范围内匹配{pattern}的所有行，在其上执行Ex命令{cmd}|

- 在一行或多个连续行上执行命令

操作，`:print`、`:delete`、`:join`、`:substitute`或`:normal`

对下面代码部分行执行打印操作：

```html
<!DOCTYPE html>
<html>
    <head><title>Practical Vim</title></head>
    <body><h1>Practical Vim</h1></body>
</html>
```

打印第1行：

```shell
:1
:print
```

或`:1p`

打印最后一行：`:$p`

`$`表示最后一行

打印2-5行：`:2,5p`

或

```shell
:2
:.,$p
```

`.`代表当前行。

打印所有行：`:%p`，`%`表示所有行。

或`:1,$p`

将所有的`Practical`替换成`Pragmatic`：

```
:%s/Practical/Pragmatic/
```

`s`是`substitute`的缩写。

**用高亮选区指定范围**

`2GVG:`，此时命令行预填充一个范围`:'<,'>`，打印输入`p`

`2G`跳至第二行，`V`面向行可视模式，`G`不知道是啥。

**用模式指定范围**

`:/<html>/,/<\/html>/p`或`:/<html>/,/<//html>/p`

格式：`:/xxx/,/xxx/p`

**用偏移地址进行修正**

`:/<html>/+1,/<\/html>/-1p`

偏移的一般形式：`:{address}+n`，如：

```
:2
:.,.+3p
```

概括：

|符号|地址|
|`1`|文件第一行|
|`$`|文件最后一行|
|`0`|虚拟行，位于文件第一行上方|
|`.`|光标所在行|
|`'m`|包含位置标记m的行|
|`'<`|高亮选区起始行|
|`'>`|高亮选区结束行|
|`%`|整个文件（`:1,$`的缩写形式）|

- 使用`:t`和`:m`命令复制和移动行

`:[range]copy {address}`（简写形式为`:t`）把文档中一行或多行复制到另一部分

`:[range]move {address}`把文档中一行或多行移到其它地方

|命令|用途|
|:---|:---|
|`:6t.`|把第6行复制到当前行下方|
|`:t6`|把当前行复制到第6行下方|
|`:t.`|复制当前行，类似`yyp`|
|`:t$`|复制当前行到文本结尾|
|`:'<,'>t0`|复制高亮选中的行到文件开头|

- 在指定范围上执行普通模式命令

对下面的每行末尾加上分号

```
var foo = 1
var bar = 'a'
var baz = 'z'
var foobar = foo + bar
var foobarbaz = foo + bar + baz
```

`A;jVG`

`:'<,'>normal .`

`A;`第一行末尾加上分号，`jVG`跳转至第二行并切换至面向行可视模式选中余下全文。

`normal .`对高亮部分执行普通模式命令`.`

或`VG`，`:normal A;`，先选中全文，使用普通模式命令`A;`

注释第二、三行：`jVj:normal i//`

- 重复上次的Ex命令

`@:`重复上次Ex命令（不懂）

- 自动补全Ex命令

`<Tab>`键自动补全命令，`<C-d>`列出所有候选项

如下，修改配色方案：

`:col<C-d>`
`:color<Tab>`
`:colorscheme <C-d>`

在多个补全项间选择，默认以以第一个补全项补全，然后遍历。

`<Tab>`、`<C-n>`或`<Right>`正向遍历，`<S-Tab>`、`<C-p>`或`<Left>`反向遍历。

- 把当前单词插入到命令行

`<C-r><C-w>`复制光标下的单词到命令行中

将下面代码中的变量`tally`重命名为`counter`：

```
var tally;
for (tally=1; tally <= 10; tally++){
    // do something with tally
}
```

`*cwcounter<Esc>`，`:%s//<C-r><C-w>/g`

光标移至`tally`，`*`查找它出现的每处地方（等同于`/\<C-r><C-w>\><CR>`
`cw`删除，

- 回溯历史命令

先使用`:`切换至命令行，`<Up>`或`<Down>`可滚动历史记录，`<C-f>`可在单独窗口查看历史记录。

Vim默认可记录最后20条记录，可在vimrc文件修改，添加如下：

```
set history=200
```

- 结识命令行窗口

不会

- 运行Shell命令

执行Shell中的程序

给命令加个`!`前缀，如`:!ls`，`:!ls`和`:ls`不同，前者是调用shell中的ls命令，后者是调用Vim的内置命令，用来显示缓冲区列表的内容。

`:!{cmd}`适用于执行一次性命令，可使用`:shell`启动交互的shell会话，`exit`可退出会话，回到Vim。

把缓冲区内容作为标准输入或输出

`:read !{cmd}`会将命令的标准输出重定向到缓冲区。

比较以下命令：

```shell
:write !sh
:write ! sh
:write! sh
```

前两个命令会吧缓冲区的内容传到外部的sh命令作为标准输入，而最后一条命令则调用`:write!`命令把缓冲区内容写到一个名为sh的文件，`!`会覆盖已有的sh文件。

**使用外部命令过滤缓冲区内容**

格式：`:[range]!{filter}`

给定一个范围时，由[range]指定的行传给`{cmd}`作为标准输入，然后`{cmd}`的输出覆盖[range]内原本的内容。也就是[range]中的内容被`{cmd}`过滤了一次。

基于第二个字段name重排序：

```
first name,last name,email
john,smith,john@example.com
drew,neil,drew@vimcasts.org
jane,doe,jane@example.com
```

`:2,$!sort -t',' -k2`

`-t','`参数告诉sort命令这些记录以逗号分隔，然后在用`-k2`参数指定按第二个字段进行排序

可将光标移到第2行，执行`!G`，Vim就会打开命令行并把范围`:.,$!`预置在命令行，然后输入`{filter}`。

概括：

|命令|用途|
|:---|:---|
|`:shell`|启动一个shell（exit回到Vim）|
|`:!{cmd}`|在shell中执行{cmd}|
|`:read !{cmd}`|在shell中执行{cmd}，并把标准输出插入到光标下方|
|`:[range]write !{cmd}`|在shell中执行{cmd}，以[range]作为其标准输入|
|`:[range]!{filter}`|使用外部程序{filter}过滤指定的[range]|

## 第二部分 文件

关键字：文件、缓冲区、分隔窗口、标签页

### 第6章 管理多个文件

- 用缓冲区列表管理打开的文件

**了解文件与缓冲区的区别**

通常所说的“编辑文件”其实编辑的是文件在内存中的映像，即“缓冲区”。

文件时存储在磁盘上，而缓冲区则存在内存中。当Vim打开一个文件时，文件内容被读入一个具有相同名字的缓冲区。当对缓冲区修改时，二者内容就出现了差别，若要保留修改，则需将缓冲区内容写回到文件中。

对文件的操作命令有：`:write`、`:update`、`:saveas`。

**结识缓冲区列表**

`$ vim vim_*`可同时打开多个文件，`:ls`查看缓冲区列表，`:bn[ext]`、`bp[revious]`切换缓冲区，`bfirst`、`blast`分别跳到列表的开头和结尾。

列表的开头有一个Vim自动分配的编号，`buffer N`可直接跳转至N缓冲区。`buffer {bufname}`可实现相同功能（利用`<Tab>`）。

`:bufdo`允许列出缓冲区后执行Ex命令（不会）。

**删除缓冲区**

`:bdelete N1 N2 N3` 删除缓冲区N1、N2、N3
`:N,M bdelete` 删除N-M的缓冲区

删除缓冲区并不影响缓冲区所关联的文件，只是简单地将该文件在内存中的映像删掉。

- 用参数列表将缓冲区分组

`:args`参数列表记录了在启动时作为参数传递给Vim的文件列表。“[]”字符指明了活动文件。实际上，可在任意时刻改变参数列表的内容。

**填充参数列表**

`:args {arglist}`{arglist}可以包括文件名、通配符，或一条shell命令的输出结果。

`$ vim`启动Vim

用文件名指定文件：

`:args vim_1 vim_2`填充参数列表，即打开两个文件

用Glob模式指定文件：

`*`符号用户匹配0个或多个字符，但它的范围仅限于指定的目录，不递归子目录。

`**`符号用户匹配0个或多个字符，递归子目录。

|Glob模式|所匹配的文件|
|:---|:---|
|`:args *.*`|index.html<br/>app.js|
|`:args **/*.js`|app.js<br/>lib/framework.js<br/>app/controllers/Mailer.js<br/>...etc|
|`:args **/*.*`|app.js<br/>index.html<br/>lib/framework.js<br/>lib/theme.css<br/>app/controllers/Mailer.js<br/>...etc|

用反引号结构指定文件：

.chapters

```
vim_1
vim_2
vim_hello
```

```
:args `cat .chapters`
```

格式：

```
:args `{cmd}`
```

- 管理隐藏缓冲区

`:ls`缓冲区名前的`+`表示被修改过，`a`表示它当前是活动缓冲区（active），`h`表示它是隐藏缓冲区（hidden）

`:bnext!`中`!`表示强制切换

在退出时，处理隐藏缓冲区的方式：

|命令|用途|
|:---|:---|
|`:w[rite]`|把缓冲区内容写入磁盘|
|`:e[dit]!`|把磁盘文件内容重新读入缓冲区（回滚修改）|
|`:qa[ll]!`|关闭所有窗口，摒弃修改无需警告|
|`:wa[ll]!`|把所有改变的缓冲区写入磁盘|

缺省Vim不允许从一个改动过的缓冲区切换到其它缓冲区，除非使用带`!`的命令。启用了`hidden`选项后，可以不带`!`进行切换。（这里好像没有说如何启用的，`argdo`、`bufdo`不知道干嘛的）

- 将工作区切分成窗口

**创建分割窗口**

`<C-w>s`水平切分窗口（standard，应该是这个单词），`<C-w>v`垂直切分窗口（vertical）

|命令|用途|
|:---|:---|
|`<C-w>s`|水平切分当前窗口，新窗口仍显示当前缓冲区|
|`<C-w>v`|垂直切分当前窗口，新窗口仍显示当前缓冲区|
|`:sp[lit] {file}`|水平切分当前窗口，新窗口载入{file}|
|`:vsp[lit] {file}`|垂直切分当前窗口，新窗口载入{file}|

**窗口间切换（h,j,k,l同字符移动）**

|命令|用途|
|:---|:---|
|`<C-w>w`|循环切换|
|`<C-w>h`|至左边窗口|
|`<C-w>j`|至下边窗口|
|`<C-w>k`|至上边窗口|
|`<C-w>l`|至右边窗口|

**关闭窗口**

|Ex命令|普通模式命令|用途|
|:---|:---|:---|
|`:clo[se]`|`<C-w>c`|关闭活动窗口|
|`:on[ly]`|`<C-w>o`|只保留活动窗口，关闭其它所有窗口|

**改变窗口大小及重新排列窗口**

|命令|用途|
|:---|:---|
|`<C-w>=`|使所有窗口等宽、等高|
|`<C-w>_`|最大化活动窗口的高度|
|`<C-w>|`|最大化活动窗口的宽度|
|`[N]<C-w>_`|把活动窗口的高度设为[N]行|
|`[N]<C-w>|`|把活动窗口的宽度设为[N]行|

- 用标签页将窗口分组

`page 110 2016-02-05 22:46:50 星期五`

Vim的标签页与缓冲区不是一一对应的关系，而应把标签页看成容纳一系列窗口的容器。

**如何使用标签页**

用Vim标签页可以把工作分隔到不同的工作区。

`:lcd{path}`可用来设置当前窗口的本地工作目录。

**打开及关闭标签页**

`:tabedit {filename}`打开一个新的标签页，忽略{filename}参数，Vim会创建一个新标签页，里面包含一个空缓冲区。

|命令|用途|
|:---|:---|
|`:tabe[dit] {filename}`|在新标签页中打开{filename}|
|`<C-w>T`|把当前窗口移到一个新标签页|
|`:tabc[lose]`|关闭当前标签页及其中的所有窗口|
|`:tabo[nly]`|只保留活动标签页，关闭所有其他标签页|

**在标签页间切换**

|Ex命令|普通模式命令|用途|
|:---|:---|:---|
|`:tabn[ext] {N}`|`{N}gt`|切换到编号为{N}的标签页|
|`:tabn[ext]`|`gt`|切换到下一标签页|
|`:tabp[revious]`|`gT`|切换到上一标签页|

**重排标签页**

`:tabmove [N]`可重排标签页，[N]为0时，当前标签页会被移到开头；忽略[N]，当前标签页会被移到结尾。


### 第7章 打开及保存文件

- 用`:edit`命令打开文件

**相对于当前工作目录打开一个文件**

`:pwd`打印工作目录（print working directory）

`:edit lib/framework.js`

**相对于活动文件目录打开一个文件**

当前编辑的文件时`app/controllers/Navigation.js`，现在需编辑`app/controllers/Main.js`。

`:edit %<Tab>`显示`app/controllers/Navigation.js`，`%`代表活动缓冲区的完整文件路径

`:edit %:h<Tab>`显示`app/controllers/`，`h`表示去除文件名。

- 使用`:find`打开文件

`:file`命令可通过文件名打开文件，无需输入文件的完整路径，但需配置`path`选项。

**配置`path`选项**

`path`选项允许执行一些目录，当调用`:find`命令时，Vim会在这目录中进行查找。

`:set path+=app/**`，输入`:find Main.js`，`<Tab>`可遍历匹配项。

- 使用netrw管理文件系统

这节的功能是由一个名为netrw的插件实现的。

确保Vim已被配置为可加载插件，需在vimrc加入如下配置：

```shell
set nocompatible
filetype plugin on
```

**结识netrw，Vim原生的文件管理器**

一一个指向**目录**的路径启动Vim，Vim就会打开一个文件管理器窗口（如下），光标移动同缓冲区普通模式，`<CR>`打开目录或载入文件至缓冲区。`-`可回到上级目录。

`vim .`

**打开文件管理器**

`:edit {path}`可打开文件管理器，`{path}`为目录名。

|Ex命令|缩写|用途|
|:---|:---|:---|
|`.edit .`|`:e .`|打开文件管理器，并显示当前工作目录|
|`:Explore`|`:E`（好像不管用）|打开文件管理器，并显示活动缓冲区所在的目录|

**与分隔窗口协同工作**

典型的文件编辑器图形用户界面一般使用**侧边栏显示文件管理器**，称作**工程目录树（project drawer）**。

然而，Vim并不是以上面的方式工作的。Vim中，可将每个窗口看成一张纸牌，纸牌的一面显示文件内容，另一面显示文件管理器。

**使用netrw完成更多功能**

`:h netrw-%`创建新文件
`:h netrw-d`创建新目录
`:h netrw-rename`重命名文件或目录
`:h retrw-del`删除文件或目录

基于scp、ftp、curl及wget通过网络读写文件。

- 把文件保存到不存在的目录中

`:edit {file}`指定了一个不存在的文件路径时，Vim会创建一个新的空白缓冲区。

`<C-g>`显示当前文件的文件名和状态,新文件显示[New file]。

无法写入新文件，可使用`:!mkdir -p %:h`补救，`-p`参数创建任何不存在的中间目录，`%`表示当前完整路径，`:h`表示隐藏文件名。

- 以超级用户权限保存文件

对于只读文件修改后可用如下命令写入：

```
:w !sudo tee % > /dev/null
```

`%`代表文件的完整路径。

## 第三部分 更快地移动及跳转

### 第8章 用动作命令在文档中移动

- 让手指保持在本位行（home row）上

|命令|光标动作|
|`h`|左移一列|
|`l`|右移一列|
|`j`|下移一行|
|`k`|上移一行|

**让右手待在它盖在的位置上**

如果觉得太过依赖光标键，可在vimrc文件中添加如下配置：

```shell
noremap <Up> <Nop>
noremap <Down> <Nop>
noremap <Left> <Nop>
noremap <Right> <Nop>
```

- 区分实际行与屏幕行

|命令|光标动作|
|:---|:---|
|`gj`|向下移动一个屏幕行|
|`gk`|向上移动一个屏幕行|
|`0`|移动到实际行的行首|
|`g0`|移动到屏幕行的行首|
|`$`|移动到实际行的行尾|
|`g$`|移动到屏幕行的行尾|
|`^`|移动到实际行的第一个非空白字符|
|`g^`|移动到屏幕行的第一个非空白字符|

如果让j、k操作屏幕行而不是实际行，可在vimrc文件中重新映射。

- 基于单词移动

|命令|光标动作|
|:---|:---|
|`w`|正向移动到下一单词的开头|
|`b`|反向移动到当前单词/上一单词的开头|
|`e`|正向移动到当前单词/下一单词的结尾|
|`ge`|反向移动到上一单词的结尾|

**理解单词与字串**

单词有字母、数字、下划线或其他非空白字符的序列组成，以空白字符分隔；字串有非空白字符序列组成，以非空白字符分隔。

面向字串的操作包括`W`、`B`、`E`、`GE`。

- 对字符进行查找

|命令|用途|
|:---|:---|
|`f{char}`|正向**在光标为知与当前行行尾之间**查找字符|
|`F{char}`|反向**在光标为知与当前行行尾之间**查找字符|
|`t{char}`|正向**在光标为知与当前行行尾之间**查找字符，移动到前一个字符|
|`T{char}`|反向**在光标为知与当前行行尾之间**查找字符，移动到前一个字符|
|`;`|查找一下匹配项|
|`,`|回退|

`dt.`删除光标处到句尾的所有文本，同`dtd`。

- 通过查找移动

`page138 2016-02-06 20:51:31 星期六`

`/{pattern}`模式匹配查找，`n`查找下一匹配项，`N`回退

**用查找动作操作文本**

查找命令不限于在普通模式下使用，在在可视模式及*操作符待决模式*中也可使用。

- 用精确的文本对象选择选区

需在面向块可视模式下：

分隔符文本对象：

|文本对象|选择区域|
|:---|:---|
|`a)`或`ab`|一对圆括号`（parentheses）`|
|`a}`或`aB`|一对花括号`{braces}`|
|`a]`|一对方括号`[brackets]`|
|`a>`|一对尖括号`<angle brackets>`|
|`a'`|一对单引号`'single quotes'`|
|`a"`|一对双引号`"double quotes"`|
|`` a` ``|一对反引号`` `backticks` ``|
|`at`|一对XML标签`<xml>tags</xml>`|
|`i)`或`ib`|圆括号内部|
|`i}`或`iB`|花括号内部|
|`i]`|方括号内部|
|`i>`|尖括号内部|
|`i'`|单引号内部|
|`i"`|双引号内部|
|`` i` ``|反引号内部|
|`it`|XML标签内部|

配合`c{motion}`、`y{motion}`、`d{motion}`使用。

**用文本对象执行操作**

可视模式适用于介绍文本对象，应为可以很容易看到发生的变化。然而，在操作符待决模式中使用文本对象，才能真正展现出它们的强大能力。（不懂咯）![](6b3ab4d7-8611-4417-845b-abeea454dea5_files/0091AD17.png)

将下面文本中的{url}替换为#，然后再用一个文本标记把{title}替换掉：

```
'<a href="{url}">{title}</a>'
```

`ci"#<Esc>citclick here<Esc>`

`ci"`解读为修改双引号内部的内容，`cit`命令解读为修改为标签内部的内容。`yit`拷贝标签内的文本，或`dit`删除文本。



- 删除周边，修改内部

Vim的文本对象分为两类：一类是操作分隔符的文本对象，如`i)`、`i"`和`it`；另一类用于操作文本块，如单词、句子和段落。

可视模式下：

|文本对象|选择范围|
|:---|:---|
|`aw`|当前单词及一个空格(a word）|
|`aW`|当前字串及一个空格|
|`as`|当前句子及一个空格|
|`ap`|当期段落及一个空格|
|`iw`|当前单词|
|`iW`|当前字串|
|`is`|当前句子|
|`ip`|当期段落|

配合`c{motion}`、`y{motion}`、`d{motion}`在普通模式下使用。

- 设置位置标记（mark），以便快速调回

`m{a-zA-Z}`会用选定的字母标记当前光标所在位置。小写位置标记只在每个缓冲区里局部可见，大写位置标记全局可见。

普通模式下两条跳转到位置标记命令（书上将反了吧）：

`` `{mark} ` ``跳转到位置标记所在行，并把光标置于设置位置标记时光标所在之处。

`'{mark}`跳转到位置标记所在行，并把光标置于该行第一个非空白字符上。

**自动位置标记**

|位置标记|跳转到|
|:---|:---|
|\`|当前文件中上次跳转动作之前的位置|
|\`.|上次修改的地方|
|\`^|上次插入的地方|
|\`[|上次修改或复制的起始位置|
|\`}|上次修改或复制的结束位置|
|\`<|上次高亮选区的起始位置|
|\`>|上次高亮选区的结束位置|

- 在匹配括号间跳转

`%`允许在一组开闭括号间跳转，可作用于()、[]和{}

### 第9章 在文件间跳转

- 遍历跳转列表

`<C-o>`回退到跳转之前
`<C-i>`前进到跳转之后

`:jumps`可查看跳转列表

任何该表当前窗口中活动文件的命令，都可以被称为跳转命令。

跳转动作：

|命令|用途|
|:---|:---|
|`[count]G`|跳转到指定行号|
|`/pattern<CR>/?<pattern<CR>/n/N`|跳转到下一个/上一个模式出现之处|
|`%`|跳转到匹配的括号所在之处|
|`(/)`|跳转到上一句/下一句的开头|
|`{/}`|跳转到上一段/下一段的开头|
|`H/M/L`|跳转到屏幕最上方/正中间/最下方|
|`gf`|跳转到光标下的文件名|
|`<C-]>`|跳转到光标下关键字的定义之处|
|\`{mark}/'{mark}|跳转到下一个位置标记|

Vim可以同时维护多份跳转列表，每个单独的窗口都拥有一份自己的跳转列表。

插入模式中，`<C-i>`等同于`<Tab>`

- 遍历改变列表

Vim会在编辑会话期间维护一张表，记载对每个缓冲区所做的修改，即改变列表（change list）。

`:changes`查看改变列表

Vim为每次修改都记录了行号与列号，可以用`g;`或`g,`反向或正向遍历改变列表。`u<C-r>`查看改变的内容。

- 跳转到光标下的文件

Vim会把文档中的文件名当成一个超链接，进行了正确配置后，可用`gf`（go to file）命令跳转到光标下的文件。

**指定文件的扩展名**

`:set suffixesadd+=.rb`将`.rb`添加到文件扩展名。

**指定要搜寻的目录**

`:set path?`查看`path`

- 用全局位置标记在文件间快速跳转

全局位置标记是一种书签，可在文件间跳转。

`:vimgrep /fooBar/ **`可查找所有出现`fooBar`的地方，包括其他文件。

## 第四部分 寄存器

### 第10章 复制与粘帖

术语：删除/剪切（**delete**）、复制（**yank**）与粘帖（**put**）

- 用无名寄存器实现删除、复制与粘帖操作

通常情况下，剪切、复制与粘帖指的是操作系统剪切板，不过，在Vim中，操作的是寄存器。


调换文本行`ddp`、创建文本行的副本`yyp`、复制一个单词（不带空格）`yiw`、其余类似y{motion}

`P`粘帖到光标之前

复制与剪切均拷贝到无名寄存器中了。

- 深入理解Vim寄存器

**引用一个寄存器**

给命令加`{register}`前缀指定要用的寄存器，若不指定，Vim缺省使用无名寄存器，同上。

`"ayiw`把当前单词复制到寄存器a中，`"bdd`剪切整行至寄存器b中，`"ap`粘帖来自寄存器a的单词。

除普通模式，Vim也提供删除、复制与粘帖操作的Ex命令，如`:delete c`剪切当前行到寄存器c中，`:put c`粘帖c寄存器内容至光标所在行下方。

**无名寄存器（""）**

不指定寄存器时，缺省使用无名寄存器，它用`""`表示，也可显示地引用该寄存器，如`""p`，等同于`p`

`x`、`s`、`d{motion}`、`c{motion}`与`y{motion}`（以及对应的大写命令）都会覆盖无名寄存器。

**复制专用寄存器（"0）**

`y{motion}`命令不仅使文本复制到无名寄存器中，而且也被拷贝到复制专用寄存器中。

`x`、`s`、`d{motion}`与`c{motion}`命令都不会覆盖该寄存器。

复制专用寄存器是稳定的，无名寄存器是易变的。

`:reg` 查看寄存器中的内容，`:reg "0` 查看"0寄存器中的内容

**有名寄存器（"a-"z）**

`"ayiw`复制一个单词到"a寄存器，`"Ayiw`追加一个单词到"a寄存器。

**黑洞寄存器"_**

黑洞寄存器是个有去无回的地方。

`"_d{motion}`删除文本且不保存任何副本。

**系统剪切板（"+）与选择专用寄存器（"\*）**

Vim的"+寄存器与系统剪切板等效，可通过`"+p`（或插入模式下用`<C-r>+`）粘帖到Vim内部。

**表达式寄存器（"=）**

这个不会用

**其它寄存器**

|寄存器|内容|
|:---|:---|
|`"%`|当前文件名|
|`"#`|轮换文件名|
|`".`|上次插入的文本|
|`":`|上次执行的Ex命令|
|`"/`|上次查找的模式|

- 用寄存器中的内容替换高亮选区的文本

交换两个词`chips`和`fish`：

```
I like chips and fish.
```

``fcdemmwwvep`mP``

`fc`找到chips，`de`剪切chips至""寄存器，`mm`添加位置标记，`ww`跳至fish，`ve`可视模式选择fish，`p`粘帖chips并剪切fish至""寄存器，`` `m ``回到位置标记m，`P`粘帖fish。

- 把寄存器的内容粘帖出来

`page169 2016-02-08 00:21:16 星期一`

**粘帖面向字符的区域**

假设当前缺省寄存器存有单词“collection",将它粘帖出来

第一种：

```
collection = getCollection();
process光标(, target);
```

第二种：

```
collection = getCollection();
process(光标, target);
```

第一种光标所在处是`(`，应使用`p`命令，粘帖在光标之后；第二种贯标所在处是`,`，应使用`P`命令，粘帖在光标之前。

插入模式中，`<C-r>{register}`粘帖寄存器内容，`<C-r>"`插入无名寄存器的内容，`<C-r>0`插入复制专用寄存器的内容。

**粘帖面向行的区域**

`p`粘帖至当前行的上一行，`P`粘帖至当前行的下一行，且光标置于粘帖文本的开头。

`gp`和`gP`功能同上，光标置于粘帖文本的结尾。

- 与系统剪切板进行交互

此节略过。。。（不懂）


### 第11章 宏

用宏可以把任意数目的按键操作录制到寄存器，用于之后的回放。

在一组目标执行宏可以有两种方式，以串行方式回放或者以并行方式多次运行。

- 宏的读取与执行

**把命令序列录制成宏**

`q`录制（recording）、停止录制，`q{register}`录制时指定保存宏的寄存器。

如：`qaA;<Esc>Ivar␣<Esc>q`，录制了一段命令序列到寄存器a中。

`:reg a`查看寄存器a中的内容`"a   A;^[Ivar ^[`，`^[`代表`<Esc>`，可参见**宏中的键盘编码**

**通过执行宏来回放命令序列**

`@{register}`执行指定寄存器的内容，`@@`重复最近调用过的宏。

**以串行方式执行宏**

**以并行方式执行宏**

- 规范光标位置、直达目标以及中止宏

黄金法则：在录制一个宏时，要确保每条命令都可被重复执行

**规范光标的位置**

**用可重复的动作命令直达目标**

**当动作命令失败时，宏将中止执行**

- 加次数回放宏

将下面文本`+`号两边加上空格：

```
x = "("+a+","+b+","+c+","+d+","+e+")";
```

录制宏：`qqf+s + <Esc>q22@q`，次数并不重要，只要大于所需要的次数就行，动作命令失败后，宏将中止执行。

更简短的录制：`f+s + <Esc>qq;.q22@q`

- 在连续的文本行上重复修改

实现下面文本的转换，`.`变成`)`，首字母大写：

```
1. one
2. two
3. three
4. four
```

录制宏：`qq0f.s)<Esc>w~jq`

**以串行方式执行宏：**`3@q`

**以并行方式执行宏：**

对于下面一段，中间包含了一些注释，如果串行方式执行宏，一定会发生中止。所以可用并行方式执行宏，即选中对象列，再执行宏`:normal @q`（宏录制在寄存器"q中）

```
1. one
2. two
3. three
4. four
// break up
5. five
6. six
// break up
7. seven
```

- 给宏追加命令

`q{register}`寄存器名小写重新录制宏，大写追加录制。

- 在一组文件中执行宏

可跨文件回放宏。

（不会不懂）

- 用迭代求值的方式给列表编号

给下面的每行文本进行编号：

```
partridge in a pear tree
turtle doves
French hens
calling birds
golden rings
```

**基本的Vim脚本**

`:let i=0`创建一个变量，`:echo i`查看变量i

`<C-r>=i<CR>`插入模式插入变量i

`:let i=0<CR>qq:let i+=1<CR>I<C-r>=i<CR>) <Esc>q`

`Vjjjj:normal @q`

- 编辑宏的内容

**将宏粘帖到文档中**

具体动作如同访问寄存器

## 第五部分 模式（patterns）

### 第12章 按模式匹配及按原义匹配

- 调整查找模式的大小写敏感性

**全局设置大小写敏感性**

若启动`ignorecase`设置，Vim的查找模式将不区分大小写。

**每次查找时设置大小写敏感性**

`\c`后缀会让查找模式忽略大小写，`\C`强制区分大小写。

**启用更具智能的大小写敏感性设置**

`smartcase`选项启用后，查找模式中输入了大写字母，`ignorecase`设置就不再生效；如果模式全是由小写字母组成，就会按照忽略大小写的方式进行查找，只要输入一个大写字母，查找方式就会变成区分大小写的了。

- 按正则表达式查找时，使用`\v`模式开关

**与Perl相比，Vim正则表达式的语法风格更接近POSIX。**

构造正则表达式，匹配下面的颜色代码：

```
body { color: #3c3c3c; }
a { color: #0000EE; }
strong { color: #000; }
```

**用magic搜索模式查找十六进制颜色代码**

`/#\([0-9a-fA-F]\{6}\|[0-9a-fA-F]\{3}\)`

**一些字符本身并不具有特殊含义，需要对其转义才能具有特殊含义（与我们平时使用的正则正好相反）。**

方括号缺省具有特殊含义，不用转义

圆括号会按原义匹配字符，需要转义

花括号需要转义

开括号转义，闭括号不用转义

**用very magic搜索模式查找十六进制颜色代码**

利用`\v`模式开关来统一所有特殊符号的规则。

`/\v#([0-9a-fA-F]{6}|[0-9a-fA-F]{3})`

**用十六进制字符类进一步优化模式**

用`\x`代替完整字符集`[0-9a-fA-F]`，以下模式等同于前面：`/\v#(\x{6}|\x{3})`

- 按原义查找文本时，使用`\V`原义开关

```
The N key searches backward...
...the \v pattern switch (a.k.a. very magic search)...
```

`/a\.k\.a\.`或`/\Va.k.a.`

- 使用圆括号捕获子匹配

当指定一个模式时，可以捕获其自匹配，并在其他地方引用它们。

```
I love Paris in the the springtime.
```

上面文本中多写了一个`the`，通过捕获子匹配可查找到。

`/\v<(\w+)\_s+\1>`

诀窍在于`()`与`\1`的组合使用，任何圆括号内部的匹配文本都会被自动保存到一个临时仓库，可以用`\1`引用这段被捕获的文本。如果有多组圆括号，可以用`\1`到`\9`，`\0`永远会引用整个匹配，无论有无圆括号。

- 界定单词的边界

在定义模式时，Vim单词界定符能指定单词从哪里开始到哪里结束。

```
the problem with these new recruits is that
they don't keep their boots clean.
```

`/the<CR>`将匹配到多项。

在very magic搜索模式下，用<与>符号表示单词界定符。

`/\v<the><CR>`只匹配到一项。

`\w`匹配单词类字符，包括字母、数字以及符号`_`；`\W`用来匹配除单词类字符以外的其他字符。

- 界定匹配的边界

`vimrc`中配置`set hlsearch`，高亮显示查找匹配项。

元字符`\zs`标志着一个匹配的起始，`\ze`界定匹配的结束。与单词定界符类似，`\zs`和`\ze`均为零宽度元字符。

结合着两个字符，可以定义一个模式匹配一个较大的文本范围，然后在收窄范围。

```
Match "quoted words"---not quote marks.
```

`/\v"[^"]+"<CR>`将匹配双引号和其内部的文本。

`/\v"\zs[^"]+\ze"<CR>`将只匹配双引号内部的文本。

`[^"]+`匹配除双引号之外的一个或多个字符。

- 转义问题字符

**正向查找时要转义`/`字符**


```
Search items: [http://vimdoc.net/search?q=/\\][s]
...
[s]:http://vimdoc.net/search?q=/\\
```

光标置于方括号内，`"uyi[`复制链接。

`/\V<C-r>u<CR>`查找时，只匹配到`http:`，因为`/`被解释成了**查找域结束符**，所有位于首个正斜杠之后的内容都被忽略到了，因此可用\转义。

**反向查找时要转义?号**

执行反向查找时，`?`会被当作查找域的结束符，所以此时只要转义`?`，无需转义`/`。

`?<C-r>u`查找

**每次都要转义符号\**

**用编程的方式转义字符**

 不会不懂

### 第13章 查找

- 结识查找命令

**执行一次查找**

**指定查找的方向**

`/`正向查找，`?`反向查找

**重复上一次查找**

|命令|用途|
|:---|:---|
|`n`|跳至下一处匹配，保持查找方向与偏移不变|
|`N`|跳至上一处匹配，保持查找方向与偏移不变|
|`/<CR>`|正向跳转至相同模式的下一处匹配|
|`?<CR>`|反向跳转至相同模式的下一处匹配|

**回溯之前的查找**

`/<Up>`

- 高亮查找匹配

**启用高亮查找功能**

配置文件中启用`hlsearch`，默认不高亮

**禁用高亮查找功能**

`:set nohlsearch`（使用`se nohls`或`:se hls`效果相同）可彻底禁用该功能。

`:nohlsearch`可暂时关闭查找高亮，下一查找会自动打开。

- 在执行查找前预览第一处匹配

激活了**增量查找功能**后，对于每次输入的模式，都自动查找第一处匹配

vimrc文件中配置`set incsearch`即可激活。

**检查是否存在一处匹配**

在启用增量查找功能后，匹配到后按`<Esc>`而不是`<CR>`，光标即可返回原位。

**根据预览结果对查找域自动补全**

`<C-r><C-w>`会自动根据预览匹配项自动补全模式。

- 统计当前模式的匹配个数

`:%s///gn`

这实际上调用的是`:substitute`命令，但标志位n会抑制正常的替换动作，这里的**查找域**留空，旨在让Vim使用当前的查找模式，**替换域**为空，由于标志位n会自动被忽略。

- 将光标偏移到查找匹配的结尾

将下面文本中`lang`改成`language`：

```
Aim to learn a new programming lang  each year.
Which lang did you pick up last year?
Which langs would you like to learn?
```

方法一：`:%s/lang/language/g`

方法二：`/lang<CR>eauage`，然后`n`跳转下一匹配项，使用点范式。`e`跳转到单词末尾。

方法三：`/lang/e<CR>uage<Esc>`，然后`n`跳转下一匹配项，使用点范式。

`//e<CR>`可重复上次查找，并将光标移至匹配项末尾。

- 对完整的查找匹配进行操作

将文本中的`Xml`和`html`都转成大写。

```
class XhtmlDocument < XmlDocument; end
class XhtmlTag < XmlTag; end
```

对于`Xml`可以运行`gU3l`或`3gUl`，对于`html`可以运行`gUtD`，将光标到第一个D之间的所有字符都转换为大写。

或者如下：

`/\vX(ht)?ml\C<CR>`匹配，`gU//e<CR>`转成大写，`//<CR>.`匹配下一项重复操作

这里是将`//e<CR>`作为一个动作命令使用，范围涵盖有查找匹配的起始到结尾之间的全部内容。

原文一大段，冗长不懂不会。

- 利用查找历史，迭代完成复杂的模式

匹配单引号里面的文本：

```
This string contains a 'quoted' word.
This string contains 'two' quoted 'words.'
This 'string doesn't make things easy.'
```

1. 粗略匹配`/\v'.+'<CR>`
2. 逐步求精`/\v'[^']+'<CR>`
3. 精益求精`/\v'([^']|'\w)+'<CR>`
4. 画上完美句号`/\v'([^']|'\w)+)'<CR>`

替换成双引号`:%s//"\1"/g`

上面的命令等同于：`:%s/\v'(([^']|'\w)+)'/"\1"/g`

- 查找当前高亮选区中的文本

**在可视模式下查找当前单词**

`*`可以匹配光标处的单词。

**查找当前选中的文本**

要弄个vim脚本，好累的哟，不懂啦。

### 第14章 替换

可重用上次的查找模式来快速构造替换命令。

- 结识substitute命令

语法：`[range]s[ubstitute]/{pattern}/{string}/[flags]`

**利用标志位调整substitute命令的行为**

标志位：

`g`使替换命令在全局范围内执行，即修改**一行内**的所有匹配。

`c`让我们有机会可以确认或拒绝每一处修改。

`n`抑制正常的替换行为

`e`专门屏蔽错误提示信息

`&`重用上一次substitute命令所用过的标志位

**替换域中的特殊字符**

|符号|描述|
|:---|:---|
|`\r`|换行符|
|`\t`|制表符|
|`\\`|反斜杠|
|`\1`|第1个子匹配|
|`\9`|第9个子匹配|
|`\0`|匹配模式的所有内容|
|`&`|匹配模式的所有内容|
|`~`|上一次调用`:s`的`{string}`|
|`\={Vim script}`|执行{Vim script}表达式，并将返回的结果作为替换{string}|

- 在文件范围内查找并替换每一处匹配

缺省情况下，substitute命令你仅仅作用于当前行，而且只会修改第一处匹配。

可将文件看成二维平面，字符沿着x轴增加，文本行沿着y轴向下增加。`g`标志位全选x轴，`%`前缀全选y轴。

如将下文所有的`going`替换成`rolling`：

```
When the going gets tough, the tough get going.
If you are going through hell, kepp going.
```

`:%s/going/rolling/g`

- 手动控制每一次替换操作

`c`标志位使每次替换时都进行询问

|答案|用途|
|:---|:---|
|`y`|替换此处匹配|
|`n`|忽略此处匹配|
|`q`|退出替换过程|
|`l`|`last`，替换此处匹配后退出|
|`a`|`all`，替换此处与之后所有匹配|
|`<C-e>`|向上滚动屏幕|
|`<C-y>`|向下滚动屏幕|

- 重用上次的查找模式

可把查找域留空，让Vim使用当前的模式，即`"/`寄存器中的内容。

对命令历史的影响：重用之后，命令历史中的记录可能就不完整了，可用`<C-r>/`插入上次查找的内容，避免查找域留空。

- 用寄存器的内容替换

**传值**

`<C-r>{register}`传值，粘帖寄存器中的内容至查找域。

**引用**

替换域中`\=`指示Vim执行一段表达式脚本，`@{register}`来引用某个寄存器的内容。如`:%s//\=@0/g`

**比较**

`:let @{register}='{string}'`给寄存器赋值

- 重复上一次substitute命令

**在整个文件范围内重复面向行的替换操作**

输入`:s/target/replacement/g<CR>`之后，发现应该加上前缀`%`。此时只需输入`g&`（详见`:h g&`），它等同于`:%s//-/&`

**修正substitute命令的执行范围**

`:&&`中`:&`重复上一次`:substitute`命令，`&`参数重用上一次`:s`命令的标志位

- 使用自匹配重排CSV文件的字段

重排字段，email置首列，其次first name，最后last name。

```
last name,first name,email
neil,drew,drew@vimcasts.org
doe,john,john@example.com
```

`/\v^([^,]*),([^,]*),([^,]*)$`匹配，`:%s//\3,\2,\1`子匹配重排

- 在替换过程中执行算术运算

```
<h2>Heading number 1</h2>
<h3>Heading number 2</h3>
<h4>Heading number 3</h4>
```

将`<h?>`标签提升一级。

`/\v\<\/?h\zs\d`匹配。`:s//\=submatch(0)-1/g`替换，`submatch(0)`即可获得当前匹配的内容。

- 交换两个或更多的单词

交换dog和man：

```
The dog bit the man.
```

`:let swapper={"dog":"man", "man":"dog"}`添加一个变量。`:echo swapper["dog"]`查看变量。

`/\v(<man>|<dog>)`匹配，`:%s//\={"dog":"man", "man":"dog"}[submatch(1)]/g`，对`submatch(1)`不是很懂。

- 在多个文件中执行查找与替换

将所有文件Pragmatic Vim中的Pragmatic改成Praticle：

`:args **/*.txt`打开所有文件，`:set hidden`启动hidden，保证在不保存文件改动的情况下，能跳转至其它文件，`:argdo %s/Pragmatic\ze Vim/Praticle/ge`，`e`参数保证不提示错误信息。

**创建一个只包含目标模式的文件列表**

待续。。。

### 第15章 global命令

`:global`命令结合了Ex命令与Vim的模式匹配两方面能力，所以很强大。

- 结识global命令

语法：`:[range]global[!]/{pattern}/[cmd]`

缺省的作用范围是整个文件（%）

`page250 2016-02-10 22:04:13 星期三`
