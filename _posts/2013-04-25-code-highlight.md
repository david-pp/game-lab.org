---
layout: post
title: 语法高亮
tagline: 代码片段语法高亮了？
---

{% highlight java linenos %}
public class HelloWorld {
    public static void main(String args[]) {
      System.out.println("Hello World!");
    }
}
{% endhighlight %}

{% highlight c linenos %}
int main(int argc, char *argv[])
{
	 for (unsigned int j = 0; j <= 100000; j++)
	 {
	 	size_t count = 0;
	    for (unsigned int i = 0; i <= 100000; i++) {
	           if (hash_long(i, 11) == j)
	           	count++;
	                //printf("pid-->%d\n", hash_long(i,11));
	     }
	     

	   std::cout << j << "--" << count << std::endl;
 	}
     return 0;
}
{% endhighlight %}
