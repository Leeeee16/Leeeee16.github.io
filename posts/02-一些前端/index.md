# 一些前端

  

  
**CSS定位节点**  
  
在CSS中，使用CSS选择器来定位节点  
`<div id="container">`  
表示为 #container  
其中 # 开头表示选择id，后面紧跟id的名称  
  
同样，如果想选择class为wrapper的节点  
可以使用 .wrapper  
以 "." 开头代表选择class，后面紧跟class名称  
  
也可以根据标签名筛选  
例如想选择二级标题，可以直接用 h2 即可。  
  
另外CSS选择器还支持嵌套选择，各个选择器之间加上空格分隔开，可以代表嵌套关系。  
如 #container .wrapper p  
表示先选择id为container的节点，然后选择其内部的class为wrapper的节点，进一步选择其内部的p节点。  
  
  
CSS选择器，CSS一列表示CSS版本（CSS1，CSS2，或对CSS3）  

| 选择器             | 示例                | 示例说明             | CSS |
| --------------------- | --------------------- | --------------------------------------------------------- | --- |
| .class                | .intro                | 选择所有class="intro"的元素|1|
| #id                   | #firstname            | 选择所有id="firstname"的元素 s| 1 |
| *                     | *                     | 选择所有元素              | 2 |
| element               | p                     | 选择所有<p\>元素                | 1 |
| element,element       | div,p                 | 选择所有<div\>元素和<p\>元素        | 1 |
| element element       | div p                 | 选择<div\>元素内的所有<p\>元素   | 1 |
| element>element       | div>p                 | 选择所有父级是 <div\> 元素的 <p\> 元素   | 2 |
| element+element       | div+p                 | 选择所有紧接着<div\>元素之后的<p\>元素  | 2 |
| [attribute]           | [target]              | 选择所有带有target属性元素    | 2 |
| [attribute=value]     | [target=-blank]       | 选择所有使用target="-blank"的元素   | 2 |
| [attribute~=value]    | [title~=flower]       | 选择标题属性包含单词"flower"的所有元素     | 2 |
| [attribute\|=language] | [lang\|=en]            | 选择 lang 属性以 en 为开头的所有元素   | 2 |
| :link                 | a:link                | 选择所有未访问链接       | 1 |
| :visited              | a:visited             | 选择所有访问过的链接                   | 1 |
| :active               | a:active              | 选择活动链接   | 1 |
| :hover                | a:hover               | 选择鼠标在链接上面时                    | 1 |
| :focus                | input:focus           | 选择具有焦点的输入元素                         | 2 |
| :first-letter         | p:first-letter        | 选择每一个<p\>元素的第一个字母       | 1 |
| :first-line           | p:first-line          | 选择每一个<p\>元素的第一行                      | 1 |
| :first-child          | p:first-child         | 指定只有当<p\>元素是其父级的第一个子级的样式。 | 2 |
| :before               | p:before              | 在每个<p\>元素之前插入内容      | 2 |
| :after                | p:after               | 在每个<p\>元素之后插入内容         | 2 |
| :lang(language)       | p:lang(it)            | 选择一个lang属性的起始值="it"的所有<p\>元素 | 2 |
| element1~element2     | p~ul                  | 选择p元素之后的每一个ul元素      | 3 |
| [attribute^=value]    | a[src^="https"]       | 选择每一个src属性的值以"https"开头的元素   | 3 |
| [attribute$=value]    | a[src$=".pdf"]        | 选择每一个src属性的值以".pdf"结尾的元素    | 3 |
| [attribute*=value]    | a[src*="runoob"]      | 选择每一个src属性的值包含子字符串"runoob"的元素 | 3 |
| :first-of-type        | p:first-of-type       | 选择每个p元素是其父级的第一个p元素        | 3 |
| :last-of-type         | p:last-of-type        | 选择每个p元素是其父级的最后一个p元素     | 3 |
| :only-of-type         | p:only-of-type        | 选择每个p元素是其父级的唯一p元素           | 3 |
| :only-child           | p:only-child          | 选择每个p元素是其父级的唯一子元素         | 3 |
| :nth-child(n)         | p:nth-child(2)        | 选择每个p元素是其父级的第二个子元素      | 3 |
| :nth-last-child(n)    | p:nth-last-child(2)   | 选择每个p元素的是其父级的第二个子元素，从最后一个子项计数 | 3 |
| :nth-of-type(n)       | p:nth-of-type(2)      | 选择每个p元素是其父级的第二个p元素        | 3 |
| :nth-last-of-type(n)  | p:nth-last-of-type(2) | 选择每个p元素的是其父级的第二个p元素，从最后一个子项计数 | 3 |
| :last-child           | p:last-child          | 选择每个p元素是其父级的最后一个子级。   | 3 |
| :root                 | :root                 | 选择文档的根元素  | 3 |
| :empty                | p:empty               | 选择每个没有任何子级的p元素（包括文本节点） | 3 |
| :target               | #news:target          | 选择当前活动的#news元素（包含该锚名称的点击的URL） | 3 |
| :enabled              | input:enabled         | 选择每一个已启用的输入元素   | 3 |
| :disabled             | input:disabled        | 选择每一个禁用的输入元素    | 3 |
| :checked              | input:checked         | 选择每个选中的输入元素    | 3 |
| :not(selector)        | :not(p)               | 选择每个并非p元素的元素    | 3 |
| ::selection           | ::selection           | 匹配元素中被用户选中或处于高亮状态的部分 | 3 |
| :out-of-range         | :out-of-range         | 匹配值在指定区间之外的input元素 | 3 |
| :in-range             | :in-range             | 匹配值在指定区间之内的input元素 | 3 |
| :read-write           | :read-write           | 用于匹配可读及可写的元素 | 3 |
| :read-only            | :read-only            | 用于匹配设置 "readonly"（只读） 属性的元素 | 3 |
| :optional             | :optional             | 用于匹配可选的输入元素| 3 |
| :required             | :required             | 用于匹配设置了 "required" 属性的元素| 3 |
| :valid                | :valid                | 用于匹配输入值为合法的元素 | 3 |
| :invalid              | :invalid              | 用于匹配输入值为非法的元素 | 3 |  
  
  
[菜鸟教程-CSS选择器](https://www.runoob.com/cssref/css-selectors.html)

