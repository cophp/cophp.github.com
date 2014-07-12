---
layout: post
title: "PHP二维数组增加键值对元素"
description: ""
category: 
tags: [二维数组,语法细节,离歌笑]
---
{% include JB/setup %}

##问题描述    
如何在二维数据中新添加一个键值对的元素？   
 
**例如**：   
{% highlight php %}   
<?php    
$test = 
Array        
(    
	0 => array(   
				'key0' => 'value0',   
				'key1' => 'value1'   
			),    
	1 => array(   
				'key2' => 'value2',   
				'key3' => 'value3'   
			)   
);
?>    
{% endhighlight %}   
添加一个新的名为‘id’的key，而对应的value为：该元素所在一维中的索引值，效果如下：   
{% highlight php %}   
<?php    
$test = 
Array        
(    
	0 => array(   
				'id' => 0, 
				'key0' => 'value0',   
				'key1' => 'value1'   
			),    
	1 => array( 
				'id' => 1,     
				'key2' => 'value2',   
				'key3' => 'value3'   
			)   
);
?>    
{% endhighlight %}   
       

##实现方法    
{% highlight php %}     
<?php    
foreach ( $test as $key => $value )
{
   $value[ 'id'] = $key; // 为value新增id的key
   $test[$key] = $value; 
}
?>    
{% endhighlight %}   
**或者**     
{% highlight php %}     
<?php   
//类C的方法
foreach ( $test as $key => $value )
{
   $test[$key]['id'] = $key; 
}
?>    
{% endhighlight %}      


----------    
By [离歌笑](helloyabo@gmail.com)