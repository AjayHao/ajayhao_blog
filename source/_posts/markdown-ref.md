title: Markdown语法速查表(原)
tags:
  - markdown
categories:
  - 技术
thumbnail: 'http://7xth6d.com1.z0.glb.clouddn.com/thumbnails/markdown_thumbnail.jpg'
cc: true
comments: true
date: 2016-02-03 21:00:00

---
#### **`原创贴，转发请注明作者或出处`**

开始写博以后，Markdown会频繁接触，网上已经有了很多参考资料，大多也很全很详细。可惜几乎全是纵排的，类似wiki中表格型排版的很少，可能把这些语法集中表格里也比较费时，所以整理了张简便易用的速查表格,供参考。

<!-- more --><!-- indicate-the-source -->

<table class="table table-condensed table-bordered table-hover table-valign-middle" style="font-size: 14px"><thead><tr class="success"><th width="33%">Markdown语法</th><th width="33%">对应Html</th><th width="33%">浏览器效果</th></tr></thead><tbody><tr><td><div>一级标题<br/>(Setext格式,只支持到二级)</div><div>=======</div></td><td><span>&lt;h1&gt;</span>一级标题 <span>&lt;/h1&gt;</span></td><td><h1>一级标题</h1></td></tr><tr><td><div>二级标题</div><div>\-\-\-\-\-\-</div></td><td><span>&lt;h2&gt;</span>二级标题 <span>&lt;/h2&gt;</span></td><td><h2>二级标题</h2></td></tr><tr><td><div>### 三级标题<br/>(atx格式,最低到六级)</div></td><td><span>&lt;h3&gt;</span> 三级标题 <span>&lt;/h3&gt;</span></td><td><h3>三级标题</h3></td></tr><tr><td><div>    换行：行尾留2个<strong>空格</strong><div></div></div></td><td>&lt;p&gt;换行：行尾留2个&lt;br /&gt;空格&lt;/p&gt;</td><td><p>    换行：行尾留2个<br>空格<br></p></td></tr><tr><td><div>\*斜体\*</div></td><td>&lt;em&gt;斜体&lt;/em&gt;</td><td><em>斜体</em></td></tr><tr><td><div>\*\*粗体\*\*</div></td><td>&lt;strong&gt;粗体&lt;/strong&gt;</td><td><strong>粗体</strong></td></tr><tr><td><div>\`标注\`</div></td><td>&lt;code&gt;标注&lt;/code&gt;</td><td><code>标注</code></td></tr><tr><td><div>\~\~删除线\~\~</div></td><td>&lt;s&gt;删除线&lt;/s&gt;</td><td><s>删除线</s></td></tr><tr><td><div>长连接\-\-\-线</div></td><td>长连接&amp;mdash;线</td><td>长连接&mdash;线</td></tr><tr><td><div>行首留4个空格，此行变为代码块</div></td><td>&lt;pre&gt;&lt;code&gt;代码块&lt;/code&gt;&lt;/pre&gt;</td><td><pre><code>代码块</code></pre></td></tr><tr><td><div>链接:</div><div>\[链接\]\(http://example\.com\)</div></td><td>&lt;a href=&quot;<a href="http://example.com" target="_blank" rel="external">http://example.com</a>  &quot;&gt;链接 &lt;/a&gt;</td><td><a href="http://example.com" target="_blank" rel="external">链接</a></td></tr><tr><td><div>图片:暂不支持宽高设置</div><div>\!\[Alt text\]\(/path/to/img\.jpg "title optional"\) </div></td><td>&lt;img alt=&quot;Alt text&quot; src=&quot;path&quot; title=&quot;title&quot; /&gt;</td><td><img alt="填充文字" src="http://7xth6d.com1.z0.glb.clouddn.com/banners/markdown.jpg" title="title"></td></tr><tr><td><div>&gt;在段落的最前方加大于号引用区块</div></td><td>&lt;blockquote&gt;引用内容&lt;/blockquote&gt;</td><td><blockquote>引用内容</blockquote></td></tr><tr><td><div>无序列表：星号、加号或是减号均可,中间留空格</div><div>\* 条目1</div><div>\* 条目2</div><div>\* 条目3</div></td><td><div>&lt;ul&gt;</div><div>&lt;li&gt;条目1&lt;/li&gt;</div><div>&lt;li&gt;条目2&lt;/li&gt;</div><div>&lt;li&gt;条目3&lt;/li&gt;</div><div>&lt;/ul&gt;</div></td><td><ul><li>条目1</li><li>条目2</li><li>条目3</li></ul></td></tr><tr><td><div>有序列表：数字开头,中间留空格</div><div>1 条目1</div><div>2 条目2</div><div>3 条目3</div></td><td><div>&lt;ol&gt;</div><div>&lt;li&gt;条目1&lt;/li&gt;</div><div>&lt;li&gt;条目2&lt;/li&gt;</div><div>&lt;li&gt;条目3&lt;/li&gt;</div><div>&lt;/ol&gt;</div></td><td><ol><li>条目1</li><li>条目2</li><li>条目3</li></ol></td></tr><tr><td><div>分隔线:一行中仅包含三个以上的星号、减号、底线</div></td><td>&lt;hr&gt;</td><td><hr></td></tr></tbody></table>
<p>最后以下符号在Markdown中需要在前面加上反斜杠转义</p>
<pre><code>\       反斜线
`       反引号
*       星号
_       底线
{}      花括号
[]      方括号
()      括弧
#       井字号
+       加号
-       减号
.       英文句点
!       惊叹号
</code></pre>