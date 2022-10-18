# Python实例属性控制的四种手段总结

Python 作为动态语言，其一大特性就是对其属性（变量）的动态控制。而这种控制主要表现在 set（赋值）、get（访问）、delete（删除）这三方面。在学习 Python 过程中，也涉及到了大量相关的方法，比如`@.setter`、`__setarri__`、`__set__`等等一系列容易混淆的概念，今特作整理，以享。

&emsp;&emsp;<small>tip：Python本身内容庞杂，所以这里除了描述符，基本上只涉及到实例属性的控制，类属性、父类属性等尚未完全总结测试。另本文中称特殊方法（special method）为魔术方法。</small>

## 属性绑定

### 实例属性添加、删除

首先从定义 Account 开始，我们介绍 Python 类中对属性的动态绑定，并一步步介绍对属性的控制。

```python
class Account(object):
    def __init__(self, name):
        self.name = name
```

在 Python 中，一切皆对象，所有实例都维护一个包含所有实例属性的字典，我们可以通过访问实例的特殊成员`__dict__`来获取该字典，对实例属性的控制也都会反映到字典中，同样我们也可以通过直接控制字典来控制实例属性。上面我们设计了一个类`Account`，接下来我们对一个实例`a`的属性进行一系列操作，如下：

```python
>>> a=Account("xzhang")
>>> (a.__dict__)["name"]="xyzhang"
>>> a.__dict__
{'name': 'xyzhang'}
>>> a.number=1201
>>> a.__dict__
{'name': 'xzhang', 'number': 1201}
>>> del a.error
AttributeError: error
```

在实际代码中，我们更倾向于使用`a.number = 1201`，即`.`操作这种“优雅”的方式来为`a`添加一个新的属性，然而 Python 的解释器会去自动调用魔术方法`__setattr__(self, key, value)`，该方法是为某个属性赋值时解释器去自动调用的，需要注意的是“**只要是属性被修改或者是赋值，不管这个属性是实例属性、类属性、父类的类属性；亦或者是已经存在的属性、不存在的属性，只要是修改和赋值，都会调用到该方法**”。当我们想删除这个属性时,同样我们更喜欢使用`del a.number`，实际则会调用魔术方法`__delattr__(self, key)`，需要注意的是“**该函数只能够删除已经存在的实例属性，对于不存在的属性和类属性是不能够删除的**”。

这是我们接触到的第一类属性控制方法，但我们往往很少直接使用这种方法，因为有更"优雅"的代码写法。不过作为魔术方法，我们是可以自定义的，比如`print`一些 log。

### `__getattr__()`与`__getattrribute__()`

这时你可能会发现我们好像遗漏了 get 方法。没错，Python 中确实存在`__getattr__()`方法，但是另有它用，因为访问（get）一个已经存在的属性是一件很自然的事情。

那么在介绍该函数之前，我们先介绍另一个魔术方法`__getattrribute__(self, key)`，该方法就是完成我们上面提到的这个自然的事情——当我们访问一个**已经存在**的属性（也包括方法）时都会**首先自动**调用该方法，因此它还被称作“属性拦截器”。同样它也支持自定义。

接下来我们回过头介绍`__getattr__(self, key)`，该方法是**当用户尝试访问一个根本不存在的属性时**，来定义类的行为的。比如：

```python
def __getattr__(self, key):
    if key == "id":
        return 180
    if key == "full_number"
        return self.number*2
    else:
        return "no attrributed"
>>> a.id
180
```

最后补充一下解释器如何查找`object.key`以确定属性不存在，并去调用`__getattr__()`方法的。其查找属性先后顺序为：

1. 特性
2. 实例属性（实例的`__dict__`）
3. 类属性（类的`__dict__`）
4. 父类的类属性

如果均查找失败，表示此属性不存在，这才会调用`__getattr__()`方法，如果再次失败则会抛出 AttributeError 异常。如果出现重名属性，解释器则依据顺序访问。

### 注意无限递归问题

最后，需要提醒的就是在自定义上面的函数时，要注意无效递归问题。如下：

```python
def __getattribute__(self, key):
    if key == "name":
        print("call __getattribute__()")
        return self.name
    else:
        return super(Account, self).__getattribute__(key)
>>> a.name
······
RecursionError: maximum recursion depth exceeded in comparison
```

代码中我们只是想添加一行打印记录，再返回`self.name`，但是解释器提示“RecursionError”，即无限递归。原因是当我们想返回`self.name`时，解释器会“傻傻地”再次调用`__getattrribute__`方法，造成递归调用。而如果我们不显式地返回值，系统将为我们返回`None`。办法如代码中`else:`段所示，我们去调用父类中的同名方法便是，让父类替我们去完成，因为我们知道所有用户自定义的类都有一个默认的父类——`object`。

### 内省方法`getattr()`,`setattr()`,`delattr()`

我们再引入一套新的属性管理手段。这三个内省方法共同组成了一套控制属性 set、get、delete 的手段。

- `getattr(object, key[, default])` 获取对象`object`中`key`属性的值，如果不存在，则返回默认值`default`。
- `setattr(object, key, value)` 给对象`object`的属性`key`赋值`value`，如果属性不存在，先创建再赋值。
- `delattr(object, key)` 删除对象`object`的属性`key`，如果属性`key`不存在，则报`AttributeError`异常。

我们同样需要注意其无限递归问题，如下代码：

```python
class Account(object):
    def __init__(self, name):
        self.name = name

    def __setattr__(self, key, value):
        if key == "number":
            print("key.number call __setattr__")
            setattr(key, value)
        else:
            print("call __setattr__")
            super(Account, self).__setattr__(key, value)


>>> a=Account("xzhang")
call __setattr__
>>> a.name="xyzhang"
call __setattr__
>>> a.number=12
······
RecursionError: maximum recursion depth exceeded while calling a Python object
```

当解释器检测到没有 number 属性并调用`__setattr__`方法，当运行到`setattr()`时，解释器也会递归调用`__setattr__`方法。另外代码中也演示了在初始化实例过程中，当代码运行到

`self.name = name`

时，也会调用`__setattr__()`方法，因此一定要注意在引入上面讲到的魔术方法后`self.name`的使用。

## 属性控制方式

以上分别介绍了两种控制属性的手段，一套是魔术方法，一套是内省方法。然而 Python 设计者认为这样的 Python 还不够“优雅”。主要有以下两个痛点：

- 当我们想单独控制类内的某一个固定属性时，使用`if key == "name:"`这种方式不够“优雅”。
- 当我们想设计一套控制属性的方法，并拿来在同一类的不同属性甚至是多个类中复用时，Python 还缺少一种“优雅”的调用手段。

### 特性（property）

于是为了解决第一个问题，特性作为一种特殊的属性被提了出来，包括了：

- `@property` 作为 get 方法。
- `@name.setter` 作为 set 方法，将其中 name 改写为你要控制的属性名 name，而非 key（即非字符串不包括引号）。
- `@name.deleter` 作为 delete 方法，其中 name 与上同理。
  只要在类中包含以上三个被装饰器修饰的方法（或者只有第一个方法），则具备了特性功能，这里给出设计和使用特性的关键代码如下：

```python
@property
def name(self):
    pass
@name.setter
def name(self,value):
    pass
@name.deleter
def name(self):
    pass
>>> a.name          # 调用property name()方法
······
>>> a.name="xzhang" # 调用setter name(a, "xzhang")
>>> del name        # 调用deleter name(a)
```

可以看到特性的实现手法是利用了类装饰器，但是要注意其被提高到了类似于关键字的层面，即你不需要`import`任何东西便可以使用它们。

实际上特性的使用更多的是为了遵循统一访问原则，即当我们想访问实例属性时我们虽然通过`=`赋值符号，但是其内部自动调用了被装饰的方法，从而减少了()函数作用符号的使用，使得代码更加“优雅”的同时，实现了对内部数据的封装。

### 描述符（descriptor）

为了解决第二个问题，Python 引入了一个新的功能————描述符（官方文档翻译为描述器）。其作用就是将一种自定义的属性控制机制单独抽象出来使用。在 Python 中我们规定，如果在一个类中给定义了魔术方法`__get__`、`__set__`、`__delete__`中的任意一个，那么我们就可以将这个类称之为描述符。对应这三个魔术方法：

- `__get__(self, instance, cls)`
  - `self` 描述符的实例
  - `instance` 使用描述符的那个类的实例
  - `cls` 使用描述符的那个类
- `__set__(self, instance,value)`
- `__delete__(self, instance)`
  这里给出创建和使用描述符的关键代码：

```python
class Mydescriptor():
    def __init__(self, name, type, default=None):
        self.name = "_" + name
        self.type = type
        self.default = default if default else type()
    def __get__(self, instance, cls):
        print("call __get__()")
        return getattr(instance, self.name, self.default)
    def __set__(self, instance, value):
        print("call __set__()")
        setattr(instance, self.name, value)
    def __delete__(self, instance):
        print("call __delete__()")
class Foo():
    def __init__(self, name):
        self.m_name = Mydescriptor(name, str, " ")
    g_name = Mydescriptor("xzhang", str, " ")
a = Foo("xzhang")
my_name = a.m_name
my_name = a.g_name    # 隐式调用Foo.name.__get__(a,Foo)
a.g_name = "xyzhang"  # 隐式调用Foo.name.__set__(a,"xzhang")
del a.g_name          # 隐式调用Foo.name.__delete__(a)
```

如上所示，需要注意的一点是：**“描述符只能在类级别上进行实例化，不能通过在`__init__()`和其他方法中创建描述符对象来为一个实例创建描述符”**，换句话说，描述符所创建的实例一定是类属性，而不是实例属性。也就是说代码中`my_name = a.m_name`实际并不会调用`__get__()`方法。

&emsp;&emsp;<small>tip：类装饰器属性可以通过类名和实例名访问，但是测试发现混用时可能发生无法自动调用对应魔术方法的情况。</small>

总结我们介绍了一共四种属性控制手段，包括魔术方法、内省函数、特性、描述符，以及一个`__getattribute__()`方法。实际上我们还同时给自己留了一个坑：特性属性、描述符属性与普通的实例属性、类属性的访问优先级，虽然正常情况，我们应该避免重名情况的发生；以及众多魔术方法自动调用顺序问题，精力有限，待以后再填。
