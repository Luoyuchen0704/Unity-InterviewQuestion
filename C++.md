# C/C++
[菜鸟教程C++](https://www.runoob.com/cplusplus/cpp-data-types.html)
## C++基本语法
* 对象，具有状态和行为
* 类，定义描述对象行为和状态
* 方法，一种行为
* 即时变量，每个对象都有
* 标识符，标识变量、函数、类、模块等
## 数据类型
* signed有符号，unsigned无符号
* auto，自动类型推断
* decltypr,获取表达式的类型
* tuple，元组，存储多个不同类型的值
* 派生数据类型
```
// 数组
int arr[5] = {1, 2, 3, 4, 5};
// 指针
int *ptr = &x;
// 引用
int &ref = x;
// 联合体
union Data {
    int i;
    float f;
}
// 枚举
enum Color {Red, Green, Blue};
```
* 类型别名
```
typedef int MyInt;
using MyInt = int;
```
* 标准库类型
```
using namespace std;
// 字符串
string s = "Hello";
// 动态数组
vector<int> vec = {1, 2, 3, 4, 5};
// 固定大小数组
array<int, 3> arr = {1, 2, 3};
// 存储两个值的容器
pair<int, float> p(1, 2.0);
// 键值对容器
map<int, string> m;
// 唯一值集合
set<int> s = {1, 2, 3};
```
* 枚举类型，由用户定义的若干枚举常量的集合，枚举常量默认从0开始，每个值比前面大一
* 类型转换，静态转换，强制转换 `float f = static_cast<float>(i);`
* 动态转换，向下转换，将基类指针或引用转换为派生类指针或引用，运行时进行类型检查，转换失败，对指针类型返回nullptr，对引用类型抛出bad_cast异常,`dynamic_cast<目标类型>(表达式)`
* 目标类型必须是指针或引用类型，表达式是需要转换的基类指针或引用
* 基类必须具有虚函数
* 基类指针指向派生类对象`Base* ptr_base = new Derived;`
* 基类指针转换为派生类指针`Derived* ptr_derived = dynamic_cast<Derived*>(ptr_base);`
* 基类引用绑定到派生类对象`Base& ref_base = derived_obj;`
* 基类引用转换为派生类引用`Derived& ref_derived = dynamic_cast<Derived&>(ref_base);`
* 常量转换，将const转非const，不能改变对象类型
* 重新解释转换，一个数据类型的值重新解释为另一个数据类型的值，在不同数据类型之间转换，可能导致未定义行为`reinterpret_cast<float&>(i);`
## 变量类型
* float,1,8,23,4字节
* double,1,11,52，8字节
* 类型长度取决于编译器和计算机架构，标准规定了不同整数类型的最小范围，确保不同系统可运行
* 变量声明向编译器保证变量以给定的类型和名称存在，只在编译时有意义，程序连接时编译器需要实际的变量声明，
* 使用extern关键字在任何地方声明一个变量，变量只能在某个文件、函数或代码块中被定义一次。
* 函数声明，提供函数名，实际定义可在任何地方进行
* 左值，指向内存位置，可以出现在复制号的左边或右边，如变量
* 右值，存储在内存中某些地址的数值，不能赋值，只能出现在复制号右边，如数值型常量
## 变量作用域
* 局部变量，需要自行初始化
* 全局变量，自动初始化，0，\0,null
* 局部变量覆盖全局变量
* 类作用域，需要类名和作用域解析运算符`::`访问
## 常量
* 字符常量，L开头，宽字符常量，存储在wchar_t类型的变量中，存储Unicode字符
* 窄字符常量，存储在char类型的简单变量
* 定义常量，#define；const
## 类型限定符
* const，常量
* volatile,可能被程序以外的因素改变，硬件或线程
* restrict,修饰指针，唯一一种访问它所指向的对象的方式
* mutable，修饰类的成员变量，可被修改，即使是const
* static，静态变量，作用域仅限于当前文件或函数内，不会被其他文件或函数访问
* register，定义寄存器变量，会被频繁使用，可以存储在CPU寄存器中，提高程序运行效率，建议，实际上是否存寄存器由编译器决定，C++11废弃
* const常量一般大写
* `const int* ptr = &NUM;`，定义指向常量的指针，指针所指的值不可修改，指针可修改
* `int const* ptr2 = &NUM;`，和上面等价
## 存储类
* aoto：默认的存储类说明符，可省略，自动存储期，生命周期仅限于块，在栈上分配
* static,静态存储器，生命周期贯穿整个程序运行期，在函数调用之间保持不变，只能在定义文件中访问
## Const
1. 修饰变量，不可被改变；
2. 修饰指针，指向常量的指针和自身是常量的指针；
3. 修饰引用，指向常量的引用，用于形参类型，避免了拷贝，又避免了函数对值得修改；
4. 修饰成员函数，表示该成员函数内不能修改成员变量；
## Const的指针和引用
* 指针，指向常量的指针，自身是常量的指针
* 引用，指向常量的引用，没用const修饰，引用只是对象的别名，不能用const修饰
* 常对象成员 const int a;
* 常成员函数 int getValue() const;
* 常对象 const A a;
* 指针变量 const A *p = &a;
## 宏定义#define和const的区别
| 宏定义#define     | const常量 |
| ----------- | ----------- |
| 宏定义，相当于字符替换      | 常量声明       |
| 预处理器处理   | 编译器处理        |
| 无类型安全检查   | 有类型安全检查        |
| 不分配内存   | 要分配内存        |
| 存储在代码段   | 存储在数据段        |
| 可通过#undef取消   | 不可取消        |
## 封装、继承和多态
1. 封装，将具体实现过程和数据封装成一个类，只能通过接口进行访问，降低耦合性