---
layout: post
title: Jekyll+github pages 搭建博客的流程
date: 2017-02-16
excerpt: 从0开始使用jekyll+github pages搭建个人博客
---

#### jekyll配置
 jekyll是用ruby编写的模板引擎，配置jekyll需要先配置ruby环境，可以下载[railsinstaller](http://railsinstaller.org/en)，完成安装后，运行其中的git bash应用（以下#表示注释内容），
 在命令行中输入：
 <pre>
<code> $ ruby -v </code>
 #显示所安装的ruby版本，如下：
<code> ruby 2.3.3p222(2016-11-21 revision 56859) [i386-mingw32]</code>

 #以上即确定ruby安装成功，之后在命令行中输入：
<code> $ gem install jekyll </code>
 #此运行完成后，所有jekyll所需的gem都会安装成功。
 偶尔会有个别gem包出现不能安装的情况，可以再次输入
<code> $ gem install ***</code>
 安装（其中***代表所提示未成功安装的gem的名称）。

 #jekyll配置完成后，在命令行中输入
<code> $ cd d: </code>
 表示将博客建立在d盘中，也可以换成其他你需要的文件路径。
 然后在命令行里输入
<code> $ jekyll new blog </code>
 完成后，将在d盘发现一个名为blog的文件夹。
</pre>


#### 基本框架的编写

笔者在建立个人博客时，曾参考[Jekyll和Github搭建个人静态博客](http://pwnny.cn/original/2016/06/26/MakeBlog.html)这篇文章，以上步骤与该文章基本一致，但当笔者准备对建在d盘内的blog文件夹内的文件进行修改时，发现文件夹内的文件与笔者所参考文章中列出的并不一致，并且根据该文评论中显示，有很多人都遇到了这样的情况。笔者最终选择的解决方案是：自己敲代码。

于是笔者暂时切换去简单学习了一下jekyll引擎的模板语言[Liquid](https://github.com/Shopify/liquid/wiki/Liquid-for-Designers)，然后建立了完整的模板骨架结构。

<pre>
 首先，文件结构如下：
 <code>
 _include
 
    |	header.html
    |	head.html
    |	footer.html
    
 _layout
 
	|	default.html
    |	post.html
    
 _post
 
  .gitignore
 
  _config.yml
 
  index.html
 </code>
 </pre>
 
_include文件夹下包含的是一些HTML片段，比如header里的代码只是网页的上部，而head.html里的代码只是完整html代码中body之前的部分代码，同理footer.html是网页底部信息的代码，它们都是可复用的组件，可以使用include的方式引入到其他HTML文件中的相应位置。

_layout文件夹下包含的是模板，他们都是会被其他文件引用的，他们中都会包含 {{ content }}，此处content及为引用此模板的文件的内容。比如default.html文件内容：
<pre>
<code>
&lt;!DOCTYPE html &gt;
 &lt;html lang="en"&gt;
   {% include head.html %}
 &lt;body&gt;
      &lt;div class="header"&gt;
         {% include header.html %}
      &lt;/div&gt;
      &lt;div class="content"&gt;
         {{ content }}
      &lt;/div&gt;
      &lt;div class="footer"&gt;
         {% include footer.html %}
      &lt;/div&gt;
 &lt;/body&gt;
 &lt;/htm&gt;
</code>
</pre>

_post文件夹下就是放到博客里的文章，注意命名方式必须是yyyy-mm-dd-the-name-of-your-article.md ，否则将无法解析，文章内容开头的YAML信息中
<pre>
---
layout：post
---
</pre>
即表明文章引用_layout文件夹下post.html，文章内容将填充到post.html中的{{ content }}里。

.gitignore文件
在这个文件中记录的文件或文件夹在提交本地文件的时候不会被提交

_config.yml文件中包含着网页基本信息，比如网站url，rss订阅信息，对文章列表分页的设置等，具体可参考[这里](http://jekyll.com.cn/docs/configuration/).

index.html文件一般就是文章列表页，我的代码如下：
<pre>
<code>
---
layout: default
---

&lt;div&gt;
  &lt;ul&gt;
     {% for post in site.posts %}
     &lt;li&gt;
        &lt;h2&gt;
           &lt;a href="{{ post.url | prepend: site.url }}"&gt;
           {{ post.title }}
           &lt;/a&gt;
        &lt;/h2&gt;
        &lt;span&gt;
           {{ post.date | date: "%b %-d, %Y" }}
         &lt;/span&gt;
         &lt;p&gt;{{ post.excerpt}}&lt;/p&gt;
      &lt;/li&gt;
      {% endfor %}
  &lt;/ul&gt;
&lt;/div&gt;
</code>
</pre>





jekyll引擎的工作方式类似于这样：拿到_post文件夹里的文章，根据其YAML信息，选择相应的layout并生成一个HTML文件，生成的文件位于_sites文件夹下相应的路径中。此时这一生成的HTML文件即为一般博客中点进一篇文章时该文章的页面。同时我们再将此HTML文件路径放到主页的链接里，当点击该文章标题时，链接就会把我们带到该文章页面。而首页的文章列表，就像上面的index.html文件里的代码一样，是用liqulid的for语句对_post文件夹里所有的文章遍历，生成各自对应的链接。

此时个人博客的本地文件已初步建立，此时在命令行里输入
<pre><code> $ jekyll serve --watch</code></pre>
jekyll 的本地服务器启动，此时打开浏览器，访问localhost:4000,此时显示的就是所建立的博客渲染完成时的样子，如下：
![渲染完成的博客页面]({{ site.url }}/images/firstpage.png)

此后，只需要修改一下文件夹中的HTML文件和CSS文件就可以制作出一个个性化的个人博客了。


当然，如果你只想套用别人的模板，你仅需要在github上把你喜欢的博客项目fork到自己的github进行修改就可以了。


#### 上传到github
首先，到[github](https://github.com)官网注册一个账号，注册完成后，点击create a new repository，命名为username.github.io，点击create repository 完成注册。点击settings，点击launch automatic page generator 启用github pages服务，点击continue to layouts，在出现的页面中选择一个默认的模板，github便自动生成了一个博客页面，地址为https://username.github.io，将username替换为自己的用户名，即可看到自己的github page然后把repository克隆到本地，此时将jekyll定位到该文件位置，启动jekyll serve，然后在浏览器中输入localhost:4000即可在本地看到刚才的github pages，然后将之前jekyll配置过程中完成好的blog内的文件全部移入克隆的文件夹内，此时将文件推送到github的仓库，再访问https://username.github.io时就可以看到之前jekyll配置好的页面了。

以后更新文章只需要按照规定的格式命名后放入 _post 文件夹下，文章就会出现在博客首页了。

















