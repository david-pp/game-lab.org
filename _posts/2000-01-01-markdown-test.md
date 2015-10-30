---
layout: post
title: Markdown渲染器测试
category : 
tags : []
published : true
date: 2000/01/01  00:00  +0800
---

目录如下[^toc]：

* 目录
{:toc}


### 语法高亮

没有指定语言：

```
aaaa
```


C代码：
 
``` c

int main()
{
	printf("hello world!");
}

```

C++代码（pygments）：

{% highlight c %}
int main()
{
	printf("hello world!");
}
{% endhighlight %}

ruby代码：

~~~ ruby

def what?
  42
end

~~~


### 表格  

语法参考[^1]。

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |




### 公式

<script type="text/x-mathjax-config">
MathJax.Hub.Config({
                  tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}
                          });
</script>
<script type="text/javascript"
  src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>

比如行内公式：

$$E=mc^2$$ is a inline formula


比如行间公式(Lorentz方程)：

$$ 
\begin{aligned} 
\dot{x} & = \sigma(y-x) \\ 
\dot{y} & = \rho x - y - xz \\ 
\dot{z} & = -\beta z + xy 
\end{aligned} 
$$

### 自动链接

<http://game-lab.org/>


### 参考资料

[^1]: <http://kramdown.gettalong.org/syntax.html#tables>
[^toc]: <http://kramdown.gettalong.org/documentation.html>