---
layout: post
title: "PHP删除数组元素"
description: ""
category: 
tags: [数组,语法细节,离歌笑]
---
{% include JB/setup %}

##0. 前言    
每门语言都有属于自己的“坑”，PHP也不例外。正所谓：魔鬼藏于细节之下。下面是PHP中删除数组元素所涉及到的一些语法细则，你就会发现，一旦了解过这些细节之后，一些所谓的“坑”其实是可以轻松跨过的。     

##1. array_shift和array_pop    
如果是删除数组中的头部或尾部元素，则可以使用array_shift或array_pop。  
（1）array_shift：将数组开头的单元移出数组   
[函数原型](http://www.php.net/manual/zh/function.array-shift.php)：`mixed array_shift(array &$array);`   
它会将array的第一个单元移出并作为结果返回，同时将array的长度减一且将所有其他单元向前移动一位。如果array为空，则返回NULL。  
**注意‘坑’**：如果是数字索引（包含以字符串形式书写的数字，如“123”），则它从零开始重新建立数字索引（如果是文字键名则将保持不变）。

例如：   
{% highlight php %}
<?php
$test = array("11"=>"first", "22"=>"second", "33"=>"third");                
print_r($test);        
echo '<br>';          
$fruit = array_shift($test);             
print_r($test);     
?>    
{% endhighlight %}  
 
输出：     
Array        
(    
	[11] => first    
    [22] => second   
    [33] => third   
)   
Array     
(    
    [0] => second   
    [1] => third    
)    

（2）array_pop：将数组最后一个元素弹出  
[函数原型](http://www.php.net/manual/zh/function.array-pop.php)：`mixed array_pop(array &$array);`   
它将弹出并返回数组最后一个元素，同时将array的长度减一。如果array为空，则返回NULL。   

##2. unset + array_values    
函数原型：`void unset(mixed $var[,mixed$...]);`    
它用于销毁给定的变量所在内存，当unset函数作用于单个变量时，它是没有任何疑惑的。当unset作用于某一个数字索引数组元素（unset用于文字键名的数组时不会有这种问题）时，则会有[一些需要注意的问题](http://www.cnblogs.com/youxin/p/3246994.html)：    

例如：   
{% highlight php %}
<?php
$test = array('first', 'second', 'third');
print_r($test);
echo '<br>';
unset($test[1]);
print_r($test);
?>    
{% endhighlight %}
       
输出：   
Array
(         
    [0] => first      
    [1] => second        
    [2] => third       
)
<br>
Array   
(         
    [0] => first      
    [2] => third       
)    


**注意到**：在unset一个数组元素后，数组中的数字索引并没有重建，而是保持原来的顺序，这使得如果在遍历过程中造成很大的麻烦。     
**解决办法**就是：unset之后，可以通过[array_vlues](http://cn2.php.net/manual/zh/function.array-values.php)函数，从数组零开始重新建立数字索引。  
{% highlight php %}
<?php
$test = array('first', 'second', 'third');
print_r($test);
echo '<br>';
unset($test[1]);
print_r(array_values($test));
?>    
{% endhighlight %}

输出：   
Array
(         
    [0] => first      
    [1] => second        
    [2] => third       
)
<br>
Array   
(         
    [0] => first      
    [1] => third       
)  

**思考**：为什么会这样？  
因为在PHP中，数组是[由一种hash结构（hashtable）实现](http://www.laruence.com/2009/08/23/1065.html)，这使得PHP数组不但支持线性遍历和随机访问，而且可以在O(1)时间复杂度下完成数组元素的增加和删除。    
这样做的一个好处就是：在删除数组中指定元素（特别当符合条件的元素有多个）时，不用担心索引失效的问题，可通过索引一直继续向后遍历（Javascript中就存在这种问题，解决办法是：在删除一个元素后，就将索引前移一位，然后接着遍历）。     

##3. array_splice     
[函数原型](http://cn2.php.net/manual/zh/function.array-splice.php)：`array array_splice(array &$input, int $offset[, int $length = 0 [,mixed $replacement]]);`
它把数组中的一部分去掉并用其他值取代。首先，删除input数组中由offset和length指定的单元，然后根据replacement参数（如果有的话）则用其中的单元取代。   
例子：   
{% highlight php %}
<?php
$test = array('first', 'second', 'third');
print_r($test);
echo '<br>';
array_splice($test,1,1);
print_r(array_values($test));
?>
{% endhighlight %}

输出：   
Array
(         
    [0] => first      
    [1] => second        
    [2] => third       
)
<br>
Array   
(         
    [0] => first      
    [1] => third       
)    

**注**：由于此函数完成后，会input数字的键名不会被保留，即会重建数字索引，故采用array_splice删除数组中的元素也是“安全的”。


By [离歌笑](helloyabo@gmail.com)