## Welcome to My GitHub Pages
-----------------------
> ``https://chengxxxxwang.github.io/chengxxxwang.github.io/``


# Learn-Efficient-Objective-C

## 1. Objective-C 是一门动态语言
> ``Objective-C``编写的程序不能直接编译成可执行机器语言(二进制编码),而是在程序运行时(``Runtime``)把程序转译成可执行的机器语言.
	``Objective-C``会尽可能的将编译和链接时要做的事情推迟到运行时来做
1. ``Objective-C`` 是动态语言,C++,Swift是静态语言
2. 静态语言执行效率和安全环境要比动态语言高,但其简便性没有动态语言那么高
3. 运行时(Runtime)环境可处理弱类型,函数存在检查工作,会检测注册表里是否存在对应的函数,类型是否正确,最后确定正确的函数地址,在进行寄存器状态,压栈,函数调用等实际操作,确保了``Objective-C``的类活性

## 2. 在头文件中尽量减少其他头文件的引用
1. 在头文件(.h)中,关键字``@class``只是为了在头文件中引用这个类,把这个类作为一个类型来引用,这就要求引用的头文件(.h)名与类名一致
2. 在头文件(.h)中使用``@class``,在源文件(.m)中使用``#import``不但可以减少不必要的编译时间,降低类之间的耦合度,而且还可以避免产生循环引用
3. 在设计类时,尽量多采用协议,避免``#import``过多,引进不必要的部分
4. 如果头文件(.h)中有多个类的定义,尽量采用模块方式,只针对性引进所需的类

## 3.尽量使用 ``const``,``enum``来替换 预处理命令 ``#define``
> ``#define``定义了一个宏,在编译开始之前就会被替换
> ``const`` 只是对变量进行修饰,当试图去修改变量时,编译器就会报错(在一些场景下只能使用``#define``,而不能使用``const``,理论上,``const``不仅在运行时需要占用空间,而且还需要一个内存的引用,``#define``在理论上更为高效,但是在现在的编译器上开始变得无关紧要了. ``const`` 在编译和调试的时候会比``#define``要友好.对于整形类型来说,我们通常建议使用``enum``)

1. 尽量避免使用``#define``预处理命令,``#define``预处理命令不包含任何类型的的信息,仅仅是在编译前进行替换操作.它们在重复定义时不会发出警报(最新的xcode 版本中 重复定义宏会有重复提示),容易在整个程序中产生不一致的值
2. 在源文件 .m 中定义的``static const``类型常量,因为无需全局引用,所以他们的名字不需要包含命名空间
3. 在头文件 .h 中定义的全局引用常量,需要关联定义在源文件 .m中的部分.因为需要全局引用,所以它们的名字需要包含命名空间,通常是它的类名作为前缀
4. 尽量使用 ``NS_ENUM`` 和 ``NS_OPTION``宏来实现枚举

## 4. 优先使用对象的字面量语法而非等效方法
### 数字写法(`NSNumber`) 

```
//:简化前
NSNumber *val ;
val = [NSNumber numberWithInt:123456];
val = [NSNumber numberWithFloat:1234.56f];
val = [NSNumber numberWithDouble:1234.56];
val = [NSNumber numberWithBool:YES];

//:化简后
NSNumber *val ;

val = @123456;
val = @1234.56f;
val = @1234.56;
val = @YES;
```

### 数组(`NSArray`)

```
NSArray *arr;

arr = @[];//空数组
arr = @[a];//存有对象a的数组
arr = @[a,b,c];//存有多个对象的数组
```

### 字典(`NSDictionary`)

```
NSDictionary *dict;

dict = @{}; //空字典
dict = @{k1:v1}; //包含一个键值对的字典
dict = @{k1:v1,k2:v2,k3:v3};//包含多个键值对的字典
```

### 下标法与容器类

容器类可以通过下标的方式存取数组和字典的数据

```
//:数组
NSArray *array = @[a,b,c];
//:可以这样写 通过下标获取数组对象替换原有的写法 [array objectAtIndex:i] 

id obj = array[i]
//:也可以直接为数组对象赋值,替换原有的方法 [array replaceObjectAtIndex:i withObject:obj];
array[i] = obj

同理适用于 字典

NSDictionary *dict = @{k1:v1,k2:v2,k3:v3};

id obj = dict[k2]

dict[k2] = obj
```

### 容器类数据结构简化的限制
1. 容器类为不可变,如果需要生成可变容器,可以传递``mutableCopy``消息 比如 

```
NSMutableArray *muArr = [@[
	@"Monday",
	@"Tuesday",
	"Wednesday",
	@"Thursday",
	@"Friday",
	@"Saturday",
	@"Sunday"] mutableCopy];

```
2. 不能对常量数组进行直接赋值(在类方法``+ (void)initialize中进行赋值``)

> 数组``NSArray``和字典``NSDictionary``等类,由于能像'容器'一样容纳东西,所以,也把这些具有容器属性的类称为**容器类**

1. 尽量使用对象字面量语法来创建字符串,数字,数组和字典等,使用它比使用常规对象的创建方法更为简洁,同时可以避免一些常见陷阱
2. 对象字面量语法特性是完全向下兼容的,使用心得特性编写出的代码,通过编译器后形成的二进制程序可以运行在之前发布的任何OS系统中
3. 在数字和字典中,要使用关键字和索引做下标来获取数据
4. 使用对象的字面量语法时,容器类不能为nil,否则抛出异常

## 5. 处理隐藏的返回类型,优先选择实例类型``instancetype``而非 ``id``

1. ``instancetype`` 仅仅用来作为``Objective-C``方法的返回值类型
2. 使用``instancetype``可以避免隐式转换``id``而造的欺骗性编译无法通过的现象,防止程序正式运行时出现崩溃的现象,可以大大的改善``Objective-C``代码的类型安全
3. 在某一特定区域,``instancetype``可以替代`id`,并非所有的区域都可以替代`id`








