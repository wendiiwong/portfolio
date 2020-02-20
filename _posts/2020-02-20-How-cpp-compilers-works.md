---
layout: post
title:  "How C++ Compiler Works?"
date:   2020-02-20 10:02:00 +0300
tags:   C++
---
---
# C++ Compiler Build Pipeline
Every program we wrote needs to invoke a compiler to convert it into an executable file. Basically, the compiler takes each C++ source file in the working directory and compiles them into object files. The object files produced are then linked together with libraries or symbols to produce an executable files, which is our program.

![]({{ site.baseurl }}/images/20200220_how_compiler_works/01.jpg)
*The compiler compiles Main.cpp to Main.obj*

![]({{ site.baseurl }}/images/20200220_how_compiler_works/02.jpg)
*Each source file (.cpp) will produces one object (.obj) file*

<br /><br />    
#### The compiler works in three main stages : 
* **Stage 1** : Preprocessing
* **Stage 2** : Compiling
* **Stage 3** : Linking 	    

---
# Stage 1 : Preprocessing
First, the compiler will run the _**preprocessor**_ on all the source files (only source files, no header files). 
Each C++ source file will then be built into a _**translation unit**_ which resulted as object file in the later stage.

Compiler will go through all our _**preprocessor directives**_ and resolves them before compilation stage.

---
#### How preprocessor resolve #include ####
The most commonly used preprocessor directive would be _`#include`_, and It is crucial for every C++ developer to know how it works.   

Let's take a look at a simple _`Math.cpp`_ that add 2 numbers,

**Math.h**
{% highlight cpp %}
int num2 = 2;
{% endhighlight %}

**Math.cpp**
{% highlight cpp %}
#include "Math.h"

int Add(int num1)
{
	return num1 + num2;
}
{% endhighlight %}

Note that _`Math.cpp`_ has a preprocessor directive  _`#include`_ that include _`Math.h`_. What the processor will do is open the _`Math.h`_, read all the contents inside, and paste it into our _`Math.cpp`_.

To have a better understanding, we could request the compiler to give us the preprocessed source that look like this:

**Math.i (Preprocessed C/C++ source)**
{% highlight cpp %}
#line 1 "D:\\wendi_blog_code_exp\\HelloWorld\\HelloWorld\\Math.cpp"
#line 1 "D:\\wendi_blog_code_exp\\HelloWorld\\HelloWorld\\Math.h"
int num2 = 2;
#line 2 "D:\\wendi_blog_code_exp\\HelloWorld\\HelloWorld\\Math.cpp"

int Add(int num1)
{
	return num1 + num2;
}
{% endhighlight %}

Noticed that "_`int num2 = 2`_" has been copied from _`Math.h`_ to _`Math.cpp`_. That's all, it's pretty simple.

---

Now, Let's assume we have a _`Main.cpp`_ that prints "Hello World" on screen:

**Main.cpp**
{% highlight cpp %}
#include <iostream>

int main(int argc, char* argv[]) {
    std::cout << "Hello World" << std::endl;
    return 0;
}
{% endhighlight %}

And if we look at the preprocessed C/C++ Source files produced :

![]({{ site.baseurl }}/images/20200220_how_compiler_works/03.PNG)

Noticed that the file size of _`Main.i(1.34MB)`_ is much more larger than _`Math.i(269 bytes)`_ even though the line of codes are similar. That's because we include a huge and massive _`<iostream>`_ in _`Main.cpp`_.



---
# Stage 2 : Compiling

After preprocessor done it's job, the compiler will then take our C++ source code and compiles them into obj files. This obj file / object file is in binary, and contains computer understandable machine code.

We can view these code in assembly file. The compiler is able to produce an assembly listing file as output. 

From the assembly file, we can see that our add operation has been converted into assembly instructions.
first instruction move num1 to registry eax, and second instruction add num2 with num1 and store it in eax.
{% highlight assembly %}
; Line 5
	mov	eax, DWORD PTR _num1$[ebp]
	add	eax, DWORD PTR ?num2@@3HA		; num2
; Line 6
{% endhighlight %}

--- Working in Progress ---
