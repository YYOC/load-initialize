# load-initialize
Objective C类方法load和initialize的区别

[Ider博客1](http://www.cnblogs.com/ider/archive/2012/09/29/objective_c_load_vs_initialize.html)
[Ider博客2](http://blog.ibireme.com/2015/10/23/ios_model_framework_benchmark/)


这两个是NSObject中非常特别的两个类方法(Class Method)。它们的特别之处，在于iOS会在运行期提前并且自动调用这两个方法，而且很多对于类方法的规则（比如继承，类别(Category)）都有不同的处理。

## + (void)initialize；
The runtime sends initialize to each class in a program exactly one time just before the class, or any class that inherits from it, is sent its first message from within the program. (Thus the method may never be invoked if the class is not used.) The runtime sends the initialize message to classes in a thread-safe manner. Superclasses receive this message before their subclasses.

## + (void)load;
The load message is sent to classes and categories that are both dynamically loaded and statically linked, but only if the newly loaded class or category implements a method that can respond.
The order of initialization is as follows:

All initializers in any framework you link to.
All +load methods in your image.
All C++ static initializers and C/C++ __attribute__(constructor) functions in your image.
All initializers in frameworks that link to you.
In addition:

A class’s +load method is called after all of its superclasses’ +load methods.
A category +load method is called after the class’s own +load method.
In a custom implementation of load you can therefore safely message other unrelated classes from the same image, but any load methods implemented by those classes may not have run yet.




###
Apple的文档很清楚地说明了initialize和load的区别在于：load是只要类所在文件被引用就会被调用，而initialize是在类或者其子类的第一个方法被调用前调用。所以如果类没有被引用进项目，就不会有load调用；但即使类文件被引用进来，但是没有使用，那么initialize也不会被调用。

它们的相同点在于：方法只会被调用一次。（其实这是相对runtime来说的，后边会做进一步解释）。

文档也明确阐述了方法调用的顺序：父类(Superclass)的方法优先于子类(Subclass)的方法，类中的方法优先于类别(Category)中的方法。
