---
layout: single
title:  "Conan, working with several Qt versions"
date:   2017-06-16 21:00:00 +0100
lang: en
categories: cpp  
---
Sometimes you have to deal with C++ projects that are using [Qt](https://qt.io), as a main framework, and [Conan](https://conan.io), as dependency manager. It is quite usual that **Qt** is installed as a system library (or integrated into a IDE), and your dependencies also use **Qt**.

In this environment, if you want to *upgrade* (or just test) your **Qt**, you will also need to rebuild each dependency using **Qt**. This situation is going to be worst in case you need to keep or work with different *Qt* versions and the same dependencies.

To remove this tedious work, we will use Conan recipe's *'options'*, because the [package ABI compatibility](http://docs.conan.io/en/latest/howtos/define_abi_compatibility.html) is defined by three thins in Conan:
	- Package settings,
	- Package options, and
	- Package requires

Let's see the following conan recipe:

```python
class QEEntityConan(ConanFile):
    name = "QEEntity"
    version = "1.0.0"
    requires = "QEAnnotation/1.0.0@fmiguelgarcia/stable"
    settings = "os", "compiler", "build_type", "arch"
    license = "https://www.gnu.org/licenses/lgpl-3.0-standalone.html"
    description = "QE Annotated Entity library"
    options = { "qt_version": "ANY"}

    def configure(self):
        self.options.qt_version = os.popen("qmake -query QT_VERSION").read().strip()
        self.output.info("Configure Qt Version: %s" % self.options.qt_version)
	 # ....
```

In the above code, we have defined the option *qt_version*, which can contains any value. Then, we use the *configure()* function to load *Qt* version from system, using *qmake --query*. In this way, our package ABI compatibility will be linked with the current Qt version. This means that, two
 packages:
	- Package QEEntity 1.0.0 on Linux, x64, GCC-5.0, Release, with qt_version=5.6.2, and
	- Package QEEntity 1.0.0 on Linux, x64, GCC-5.0, Release, with qt_version=5.9

are different from *Conan* point of view.

And done, using this method, in each of our dependencies, **it allows us to change easily the *Qt* on our solution, and *Conan* will use or compile any dependency package that are not yet in its cache using the right *Qt* version.** 

