---
layout: page
title: 三人行，必有我师焉。
tagline:
---
{% include JB/setup %}


##Profile        
> Name :    3phpers     
> Email :   3phpers@gmail.com  
> Major :   Computer Science     
> City :    Shanghai, China  



##Posts List
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>


##学习PHP，一起来！ 

<img src="php_together.jpeg" alt="学习PHP，一起来！" height="297" width="525"/>

图片来自：<a href="http://www.duitang.com/people/mblog/83589456/detail/">堆糖网</a>




*****     

这里的文章均采用<a href="http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh" target="_blank">知识共享3.0许可协议</a>进行版权许可。您可以自由复制或转载，但必须注明来源。您不得将其用于商业目的。    
谢谢您的关注 :)