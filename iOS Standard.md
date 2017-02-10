订单来了移动开发团队 iOS 开发规范
======================

<a name='TOC'/></a>目录
----
* [命名规范](#naming)
  * [命名基础](#naming-basic-principle)
  * [方法命名](#naming-method)
  * [函数命名](#naming-function)
  * [属性与数据类型命名](#naming-property-data-type)
  * [协议名](#naming-protocol)
  * [其他](#naming-other)
* [格式排版规范](#formatting)
  * [注释规范](#note)
  * [代码结构与格式](#code-structure)
* [开发实践](#dev-practice)
* [参考](#reference)

<a name='naming'/></a>命名规范
-----
### <a name='naming-basic-principle'></a>命名基础

* 最好是既清晰又简短,但不要为简短而丧失清晰性 `insertObject: atIndex:` not `insert: at:`(不清晰：要插入什么？“at”表示什么？)
* 名称通常不缩写（禁止拼音！！！）
* 类名采用大驼峰（UpperCamelCase）`ps:订单来了项目有DD前缀，eg:DDUserModel`
* 类成员、方法小驼峰（lowerCamelCase）
* 局部变量大小写首选小驼峰，也可使用小写下划线的形式（snake_case）

### <a name='naming-method'></a>方法命名
* 以 `alloc`、`copy`、`init`、`mutableCopy`、`new` 开头的方法要注意，它们会改变ARC的行为,不能作为方法前缀
* 以 `get`、`set` 开头的方法有特殊的意义，不要随意定义。
  1. set 是属性默认的设置方法，如果函数不是为了设置类成员，则不要用 `set` 开头，可用 `setup` 替代。
  2. get 和属性方法无关，但在 Cocoa 中，其标准行为是通过引用传值，而不是直接返回结果的。欲获取变量，直接以变量名为名，如：`userInfomation`，而不是 `getUserInfomation`。

例：

```Objective-C
// OK
- (NSString *)name;

// 糟糕，应用上面的写法
- (NSString *)getName;

// OK，但极少使用
- (void)getName:(NSString **)buffer range:(NSRange)inRange;


// OK
- (NSSize)cellSize;

// 糟糕，应用上面的写法
- (NSSize)calcCellSize;
 

// 对 controller 做一般设置，OK
- (void)setupController;

// 列出具体设置的内容，更好
- (void)setupControllerObservers;

// 糟糕，set 专用于设置属性
- (void)setController;
```

```Objective-C
// 来自官方文档
insertObject:atIndex:    // OK
insert:at:               // 不清晰，插入了什么？at 具体指哪里？
removeObjectAtIndex:     // OK
removeObject:            // OK
remove:                  // 糟糕，什么被移除了？
```
### <a name='naming-function'></a>函数命名
Objective-C 允许通过函数(C 形式的函数)描述行为,就如成员方法一样。如果隐含的类为单例或在处理函数子系统时,你应当优先使用函数,而不是类方法。
函数命名应该遵循如下几条规则:

* 函数命名与方法命名相似,但有两点不同:
	
	1、它们有前缀,其前缀与你使用的类和常量的前缀相同
	
	2、大写前缀后紧跟的第一个单词首字符
	
* 大多数函数名称以动词开头,这个动词描述该函数的行为

```
NSHighlightRect
NSDeallocateObject
```

### <a name='naming-property-data-type'></a>属性与数据类型命名
#### 属性声明与实例变量
属性声明的命名大体上和访问方法的命名是一致的。假如属性是动词或名词。例如：
	
```
@property (strong) NSString *title;
@property (assign) BOOL showsAlpha;
```
如果属性是形容词，名字去掉"is"前缀，但是要特别说明一下符合规范的get访问方法，例如
	
```
@property (assign, getter=isEditable) BOOL editable;
```
#### 常量
常量命名规则根据常量创建的方式不同而大不同。

##### 枚举常量
* 使用枚举来定义一组相关的整数常量
* 枚举常量与其 typedef 命名遵守函数命名规则

```
typedef NS_ENUM(NSInteger, WalletCampType) {
    WalletCampTypeNone = 0,
    WalletCampTypeCreate,
    WalletCampTypeAdd,
    WalletCampTypeBoth
};
```

* 位掩码常量可以使用不具名枚举

```
typedef NS_OPTIONS(NSUInteger, YYTextDirection) {
    YYTextDirectionNone   = 0,
    YYTextDirectionTop    = 1 << 0,
    YYTextDirectionRight  = 1 << 1,
    YYTextDirectionBottom = 1 << 2,
    YYTextDirectionLeft   = 1 << 3,
};
```
##### const常量
* 尽量用const来修饰浮点数常数，以及彼此没有关联的整数常量（否则使用枚举）
* const常量命名范例：

```
const float NSLightGray;
```

##### 其他常量
* 通常不使用 #define 来创建常量。如上面所述，整数常量请使用枚举，浮点数常量请使用const
* 使用大写字母来定义预处理编译宏。如：

```
#ifdef  DEBUG
```
##### 通知
如果一个类有委托,那它的大部分通知可能由其委托的委托方法来处理。这些通知的名称应该能够反应其 响应的委托方法。订单来了项目中消息基本命名格式是：`[DD] + Notification + [UniquePartOfName]`，例：

```Objective-C
DDNotificationPermissionSettings
```


### <a name='naming-protocol'></a>协议名
好的协议名应能立刻让人分辨出这不是一个类名，除了以常用的 delegate、dateSource 做结尾外，还可以使用 …ing 这种形式，如：`NSCoding`、`NSCopying`、`NSLocking`。

### <a name='naming-other'></a>其他
好的命名规则可以不看代码就知道具体是做什么的。所以一个良好的命名规则是很重要的。结合订单来了，举一些例子：

```
ViewController: DDLoginViewController ([DD] + [UniquePartOfName] + ViewController)

Model: DDUserModel ([DD] + [UniquePartOfName] + Model)

View: DDShareView ([DD] + [UniquePartOfName] + View)

Logic: DDLoginApi ([DD] + [UniquePartOfName] + Api)
...
```

总之。最重要的一点就是清晰易懂，让人看文件命名就知道是做什么的。


<a name='formatting'/></a>格式排版规范
-----
### <a name='note'></a>注释规范
* 方法注释

	1、采用javadoc的格式，可以使用XCode插件VVDocumenter-Xcode快速添加，只需输入`///`即可

	```Objective-C
	/**
 	*  功能描述
 	*
 	*  @param tableView 参数说明
 	*  @param section   参数说明
 	*
 	*  @return 返回值说明
 	*/
	- (NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section
	{
    	return [self.familyNames objectAtIndex:section];
	}
	```
	
	2、`//`注释
	
	```
	// -tableView:shouldHighlightRowAtIndexPath: is called when a touch comes down on a row. 
	// Returning NO to that message halts the selection process and does not cause the currently selected row to lose its selected look while the touch is down.
	- (BOOL)tableView:(UITableView *)tableView shouldHighlightRowAtIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(6_0);
	- (void)tableView:(UITableView *)tableView didHighlightRowAtIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(6_0);
	- (void)tableView:(UITableView *)tableView didUnhighlightRowAtIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(6_0);
	```

* TODO注释
注释确实是为了标记一些未完成的或完成的不尽如人意的地方, 这样一搜索, 就知道还有哪些活要干, 日志都省了。
格式：//TODO:说明

```Objective-C
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    //TODO:增加初始化
    return YES;
}
```

### <a name='code-structure'></a>代码结构与格式
* 用`#pragma mark -`将函数或方法按功能进行分组。
* `dealloc`方法放到实现文件的最顶部 

	> 这样是为了时刻提醒你要记得释放相关资源。
	> 

* `delgate`或协议相关方法放到一般内容之后。

	```Objective-C
	@property (...,...)
	#pragma mark - life cycle
	- (void)dealloc {}
	- (instancetype)init {}
	- (void)viewDidLoad {}
	- (void)viewWillAppear:(BOOL)animated {}
	- (void)didReceiveMemoryWarning {}
	#pragma mark - delegate
	#pragma mark - event response
	#pragma mark - private methods (说严格不应该写在这里，可以写成category或者模块)
	#pragma mark - getters and setters

	```

<a name='dev-practice'></a>开发实践
------
主要描述开发过程中一些比较固定的实践技巧，写代码时可以直接套用
### 初始化
* 初始化方法的返回类型用`instancetype`，不要用`id`

### 字面量
每当创建 `NSString`， `NSDictionary`， `NSArray`，和 `NSNumber` 类的不可变实例时，都应该使用字面量。要注意 `nil` 值不能传给 `NSArray` 和 `NSDictionary` 字面量，这样做会导致崩溃。
推荐：

```
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

### 布尔值
* Objective-C的布尔值只使用`YES`和`NO`
* `true`和`false`只能用于CoreFoundation，C或C++的代码中
* 禁止将某个值或表达式的结果与`YES`进行比较
	
	> 因为BOOL被定义成signed char。这意味着除了YES(1)和NO(0)以外，它还可能是其他值。
因此C或C++中的非0为真并不一定就是YES
	>

* 在C或C++代码中，要注意NULL指针的检测。
	> 向一个nil的Objective-C对象发送消息不会导致崩溃。但由于Objective-C运行时不会处理给NULL指针的情况，所以为了避免崩溃，需要自行处理对于C/C++的NULL指针的检测。
	>
* 在方法实现中，如果有block参数，要注意检测block参数为nil的情况。

```
 - (void)exitWithCompletion:(void(^)(void))completion {
        // 错误。 如果外部调用此方法时completion传入nil，此处会发生EXC_BAD_ACCESS
        completion();

        // 正确。如果completion不存在则不调用。
        if (completion) {
            completion();
        }
    }
```
### 代码瘦身
就拿某个ViewController来说，如果其包含的功能已经很多或者还要增加更多功能的时候，就需要考虑到ViewController瘦身的问题
(ps:如果本身业务逻辑很简单，没必要瘦身)

* 自定义View，开发Api接口和delegate
* 把UITableView或者UICollectionView的DataSource隔离，使用block进行回调
* 使用扩展来设置基本属性
* ...

<a name='reference'></a>参考
------
* <https://calvingit.gitbooks.io/appscomm-coding-guidelines-for-cocoa/content/Naming_Methods.html>
* <https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html#//apple_ref/doc/uid/10000146-SW1>