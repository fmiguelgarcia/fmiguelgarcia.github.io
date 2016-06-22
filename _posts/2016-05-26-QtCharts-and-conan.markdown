---
layout: single
title:  "Qt Charts and conan package manager"
date:   2016-05-20 16:19:48 +0100
lang: en
categories: cpp  
---
Each new Qt release usually comes with new and interesting features. In the last 5.6.0, the shiny new <code>QtCharts</code>'s licence was a temptation for me. However, after downloading this bundle, I discovered that <code>QtCharts</code> was not included. You need to download it from <code>git</code> and compile it by yourself.

{% include toc %}

This is a perfect opportunity to generate my first <code>conan</code> package. If you don't know <code>conan</code> yet, please visit [Conan web page](https://conan.io). It is a promising new system to facilitate the **package management for C++ with a perfect CMake integration**. It also supports other build tools integration, but is there anyone better than <code>CMake</code>? Well, this is my humble opinion, hehe.
From an architectonic point of view, the package architecture is usually postponed until the software solution is huge and difficult to manage. It is a common and _bad praxis_ on lot of companies. A good _package architecture_, at the beginning of the project, will allow us to reach a set of advantages: 

 1. **Work paralellization capacity** in different solution's areas and executed by several work groups. If we don't have versioned and well-splitted packages, the integration of developments which has been executed by different teams (specially when they are remote) could be a *overload* that directly affects to our **time to market**. The *project managers* and *software architect* have to work together to link the **Work Breakdown Structure** with the **package management** because this package architecture could facilitate the achievement of the deliverables and be able to use *fast tracking*.
 2. It **increases the solution quality** because it avoids to fall into the **Dependency hell**. Analyzing periodically the package's evolution, we are able to determine where we need to increase the effort, make *integrations* or *refactoring* components. The main aim is to **reduce the over-cost**, specially cases due to **Technical debt** (we will see that on a future post).


The modern coffee language guys have a good solution for that using <code>Maven</code> or <code>Gradle</code>. However on a <code>C</code> or <code>C++</code> project, my recommendation is to use the tandem <code>CMake</code> and <code>conan</code>. Both tools are specially useful when your solution is multiplaform. I mean not only different operative systems (like Windows, Mac or Linux), also when you work using several compilers (clang, gcc, MSVC, etc) or different versions of them.

# Starting with conan#

Let's do the first step, How can I generate a <code>conan</code> package? Briefly, we have to create a new file, the **package recipe**: <code>conanfile.py</code>. Yes, it is python, so Can you imagine the flexibility to create custom building steps?. There is another option, based on a simpler text file, but <code>python</code> is quite simple, Isn't it?. Well let's come back to our recipe file. This file contains a <code>python</code> class where we can setup things like how the system will get the source code, which are its dependencies, how to build it or what should be in the binary package.

One of the biggest advantages againts <code>Maven</code> is that conan manages very well some aspects needed by native applications, like: compiler and its version, system architecture (x86, x64, etc), operative system, etc. Moreover, it will try to build the binary package whether it does not still exist on your local repository or for your specific configuration. For example, let's assume that our system is based on <code>gcc 4.9</code> and we want to test our library with <code>Microsoft VS 2015</code> compiler. We only need to setup our default configuration or adjust some command line parameters and each dependency will be compiled, linked, and stored in our local repository using the new compiler.

The <code>conan</code> recipe for this post is located in [My QtChart's git repository](https://github.com/fmiguelgarcia/conan.qtcharts.git) and the package is also available in **conan public repository**.

```
$> conan search -v -r="conan.io" QtChart 
```

# Environment setup#

Qt 5.6.0 is a QtCharts' dependency, and we should generate each Qt 5.6.0 package. However, this is too much work for now. Instead of that, we will set them as a system precondition. Actually we only need to update the <code>path</code> to point to the right <code>qmake</code>. In my case, I'm using [Fish shell](https://fishshell.com/) instead of [Bash shell](https://www.gnu.org/software/bash/):
 
```
set -xg PATH ~/bin/Qt/5.6/gcc_64/bin/ $PATH
```

and we just check that <code>qmake</code> version is the proper one:

```
$> qmake --version
QMake version 3.0
Using Qt version 5.6.0 in /home/miguel/bin/Qt/5.6/gcc_64/lib
```

# Export, build and enjoy #

Firstly we need to export the package. This means that we have to copy the package recipe into our local repository. In this way, the package will be available as a dependency for future package builds. 

```
$> conan export fmiguelgarcia/stable

QtCharts/5.7.0@fmiguelgarcia/stable: A new conanfile.py version was exported
QtCharts/5.7.0@fmiguelgarcia/stable: conanfile.py exported to local storage
QtCharts/5.7.0@fmiguelgarcia/stable: Folder: /home/miguel/.conan/data/QtCharts/5.7.0/miguel/stable/export
```

Conan recipes are exported within some kind of positional information, and in these case, <code>fmiguelgarcia/stable</code> identifies this recipe's user is <code>fmiguelgarcia</code> and it is in the <code>stable</code> channel.

This is enough, since dependencies can be built even if it has not been compiled for your specific configuration. We are right now as a package forger, and we need to check that the building and the package generation are correct. The conan's approach is to generate a special test package which has our package as a dependence. In addition, we have to add a test code to use the library and to generate a test executable. 
When test package is executed, it will generate and will store the new package into our local repository.

```
$> set -xg CONAN_USERNAME "fmiguelgarcia"
$> set -xg CONAN_CHANNEL "stable"
$> conan test_package
```

There are two important points:

 1. This recipe downloads the QtCharts' source code from <code>Github</code>. 
 2. The positional information (user and channel) is set using system variables <code>CONAN_USERNAME</code> and <code>CONAN_CHANNEL</code>. In this way, it is more flexible and you can re-use the same test package on several users and/or channels. 

After the source code is downloaded and the QtCharts library built, the test application will be compiled and executed. Finally, a nice chart application will show you.
If you want to use the library on your projects, it will be enough to add the package as a dependency on your project. In this example:

```python
    requires = "QtCharts/5.7.0@fmiguelgarcia/stable" 
```
and adding the information to our build tool, in my case <code>CMake</code>:

```
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()
```
You can see the complete example at [Github repo](https://github.com/fmiguelgarcia/conan.qtcharts.git) in <code>test_package/CMakeLists.txt</code>

# Conclusion #

It is important to provide a proper package management system when you deal with a development with several products or parallel work teams. The functional sharing and the right dependence management are fundamental factors. I have seen some companies where libraries are not shared and each product has its own velocity and, at the end, they make the same thing twice or three times.

Share library versions have also a dark side. If the package split is not proper, you will end suffering in each minimum change and it will require a lot uncountable versions. Welcome to your **Dependency hell**.

The versioning is a tool that will improve the productivity and the quality, on the software and the process. If you want to get the most out of it, it is important to use good tools, such as <code>conan</code> and <code>CMake</code>, and of course, great third-party libraries like <code>Qt</code> and excellent IDE like **KDevelop** or my legacy **Vim**. 
