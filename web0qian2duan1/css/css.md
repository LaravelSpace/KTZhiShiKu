## 布局

### div并排显示

让多个div并排显示通常有这几种方法，float:left，display:inline，display:inline-block。使用display:inline-block的时候，常碰见的问题有两个，一个是并排的div对不齐，另一个是并排的div中间有间隙。div对不齐的问题和基准线有关，需要用vertical-align指定并排的div使用同一个基准线。

div中间有间隙的问题和空白符有关，元素被当成行内元素排版的时候，元素之间的空白符（空格、回车换行等）都会被浏览器处理。根据CSS中white-space属性的处理方式（默认是normal，合并多余空白），原来HTML代码中的回车换行被转成一个空白符，在字体不为0的情况下，空白符占据一定宽度，所以出现了间隙。

方案一是把div代码放到一行，就是两个div中间不要留换行。方案二是，父元素中设置font-size为0，在子元素上重置正确的font-size，这么做要注意如果子元素没有设置font-size，元素内的字体就不会显示。当然还有别的处理手段，上面这两个操作起来比较容易。