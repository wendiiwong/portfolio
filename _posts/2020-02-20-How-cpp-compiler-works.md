---
layout: post
title:  "How C++ Compiler Works?"
image: 20200220_how_compiler_works/HowCompilerWorks.jpg
date:   2020-02-20 10:02:00 +0300
tags:   C++
---
---
Every program we wrote needs to invoke a compiler to convert the source files into an executable file. Basically, the compiler takes each C++ source file in the working directory and compiles them into object files. The object files produced are then linked together with libraries or symbols to produce an executable file, which is our program.

Each source file will be compile into one object file. The picture below shows that compiler converts _`Main.cpp`_ into _`Main.obj`_.
![]({{ site.baseurl }}/images/20200220_how_compiler_works/01.jpg)

If our program has _`n`_ source files, the compiler would produces _`n`_ object files as a result. For example, the compiler will generates two object files if there are two source files provided. (_`Main.obj`_ and _`Math.obj`_ in the example below)
![]({{ site.baseurl }}/images/20200220_how_compiler_works/02.jpg)

The compiler works in three main stages. However, the actual compiling process involves more steps. The detailed process is not covered in this post, as this post is meant to be beginner friendly. 
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

Noticed that the file size of _`Main.i(1.34MB)`_ is much larger than _`Math.i(269 bytes)`_ even though the line of codes are similar. That's because we include a huge and massive _`<iostream>`_ in _`Main.cpp`_.



---
# Stage 2 : Compiling

After preprocessor done it's job, the compiler will then take our C++ translation unit and compiles them into object files. Theses object files in binary contains computer understandable machine code, which included instructions and metadata about the addresses of variables and functions (symbols). As we can see from _`Math.obj`_ below, it contains binary data.

**Math.obj**  
![]({{ site.baseurl }}/images/20200220_how_compiler_works/04.PNG)

We can also request the compiler to generate the output in human-readable assembly listing files. The assembly instruction below are extracted from the generated assembly listing file _`Math.asm`_.

**Math.asm**
{% highlight assembly %}
; Line 5
	mov	eax, DWORD PTR _num1$[ebp]
	add	eax, DWORD PTR ?num2@@3HA		; num2
; Line 6
{% endhighlight %}

We can see that our add operation has been converted into assembly instructions. The first instruction move _`num1`_ to registry _`eax`_, and second instruction add _`num2`_ with _`num1`_ stored inside _`eax`_ and update the result in _`eax`_.

Now with all the object files generated, the computer knows what to do and where the symbols and functions located. The next stage is to link them togethers.

---
# Stage 3 : Linking

Object files generated from compiler are standalone and unable to interact with each others, and it is the job of linker to link them together. In a nutshell, the linker links all object files and libraries together and create an executable file.

To have a better understanding on how linker works, let's start with a simple example. Assume that we have an _`Add`_ function definition in _`Math.h`_, which received two integer parameters and return the sum of them. (Of course in real life we won't write code in this way, this is just an example to show what compiler and linker do.)

**Math.h**
{% highlight cpp %}
int Add(int num1, int num2)
{
	return num1 + num2;
}
{% endhighlight %}

And we call the function in _`Main.cpp`_ as below :

**Math.cpp**
{% highlight cpp %}
#include <iostream>

int main(int argc, char* argv[]) {
    std::cout << Add(1, 2) << std::endl;
    return 0;
}
{% endhighlight %}

When we compile the code, noticed that we get an compilation error C3861 telling that _'Add' identifier not found_, because _`Main.cpp`_ has no idea what _`Add`_ is. 
![]({{ site.baseurl }}/images/20200220_how_compiler_works/05.PNG)

One of the way to fix this is to simply copy the function signature into _`Main.cpp`_, to tell the compiler that _`Add`_ is a function received two int parameters and return an int value.

{% highlight cpp %}
#include <iostream>

int Add(int num1, int num2);    // <---- Add function signature

int main(int argc, char* argv[]) {
    std::cout << Add(1, 2) << std::endl;
    return 0;
}
{% endhighlight %}

Now the compilation is succeed.
![]({{ site.baseurl }}/images/20200220_how_compiler_works/06.PNG)

Next, let's try to build it.
![]({{ site.baseurl }}/images/20200220_how_compiler_works/07.PNG)

Noticed that we get a Linking error LNK2019 telling that we have _unresolved external symbol_, which is our _Add_ function. This is happen because the linker do not know where to find the function required.

Now let's include _`Math.h`_ instead, which contains the function definition, and build again.
{% highlight cpp %}
#include <iostream>
#include "Math.h"    // <---- Add this line

int main(int argc, char* argv[]) {
    std::cout << Add(1, 2) << std::endl;
    return 0;
}
{% endhighlight %}

This time the build is succedded, and we get an executable file ( _`HelloWorld.exe`_ ).
![]({{ site.baseurl }}/images/20200220_how_compiler_works/08.PNG)
