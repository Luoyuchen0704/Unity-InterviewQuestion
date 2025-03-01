# C#
## 值类型和引用类型的区别
* 存取速度：值类型快，引用类型慢
* 用途：值类型表示实际数据，引用类型表示指向存储在内存中的数据的指针或引用
* 来源：值类型继承自System.ValueType，引用类型继承自System.Object
* 位置：值类型的数据存储在内存的栈中，引用类型存储在内存的堆中，内存单元中只存放堆中对象的地址
* 类型：值类型变量直接存放实际的数据，引用类型变量存放数据的地址，即对象的引用
* 传递方式：值类型用值传递，引用类型用引用传递
* 值类型（value type）：byte，short，int，long，float，double，decimai，char，bool，struct。声明后分配内存，无论是否赋值，会赋默认值
* 引用类型（reference type）：string和class。声明一个类时，只在栈中分配一小片内存用于容纳一个地址（指向NULL），不分配堆上的内存空间。new一个类的实例时，分配堆上的空间，并把堆上空间的地址保存到栈上分配的小片空间中。
## 拆箱装箱
装箱（boxing）和拆箱（unboxing）是C#类型系统的核心概念，不同于C/C++。在值类型和引用类型中架起一座桥梁，实现值类型和引用类型的互相转换，统一考察系统，任何类型的值都按照对象处理。
* 装箱把值类型转换成引用类型
* 拆箱把引用类型转换成值类型
## C#中数组、ArrayList、List的区别
Array类是C#中所有数组的基类，在System命名空间中定义。提供各种用于数组属性和方法
属性：
* IsFixedSize是否带有固定大小
* IsReadOnly是否只读
* Length元素总数，32位
* LongLength元素总数，64位
* Rank数组的维度
方法：
* Clear设置数组中某个范围的元素为0，false或null
* Copy（Array，Array，Int32）从数组的第一个元素开始赋值某个范围的元素到另一个数组的第一个元素位置，长度由32位整数指定
* CopyTo（Array，Int32）
* GetLength获取32位元素总数
* GetLongLength获取64位元素总数
* GetLowerBound获取数组中指定维度的下界
* GetType获取当前实例的类型，从对象（Object）继承
* GetUpperBound获取数组中指定维度的上界
* GetValue（Int32）获取一维数组中指定位置的值，索引32位
* IndexOf（Array，Object）搜索指定对象，返回一维数组第一次出现的索引
* Reverse（Array）逆转整个一维数组中元素顺序
* SetValue（Obect，Int32）给一维数组中指定位置的元素设置值，索引32位
* Sort（Array）使用每个元素的IComparable实现排序
* ToString返回表示当前对象的字符串
### HashTable哈希表
1. System.Collections命名空间提供的一个容器，处理和表现类似key-value的键值对，key用于快速查找，区分大小写；value用于存储对应key值。键值对均为object类型，可以支持任何类型的键值对。
2. 哈希表的简单操作
* Add(key, value)添加一个键值对
* Remove(key, value)
* Clear()
* Contains(key)
3. 遍历哈希表用DictionaryEntry Object
foreach(DictionaryEntry de in ht){ }
4. 对哈希表排序
无法对key重新排列，只能按顺序输出
ArrayList akeys = new ArrayList(ht.Keys);
akeys.Sort();按字母顺序排序
foreach(string skey in akeys)
{
  Console.Write(skey + ":");
  Console.WriteLine(ht[skey]);排序后输出
}
### 数组
* C#中最早出现，内存中连续存储，索引速度快，赋值和修改元素简单  
* 不足：在两个数据间插入数据麻烦，在声明数组的时候必须指定数组的长度，过长浪费内存，过短数据溢出，在不清楚数组长度时容易出问题
### ArrayList
* 处于命名空间System.Collections，使用该类必须进行引用，继承了IList接口，提供了数据存储和检索。
* ArrayList对象的大小暗战存储的数据动态扩充与收缩，声明时无需指定长度
* 允许插入不同类型的数据，所有插入数据当作object，处理数据可能会报类型不匹配，不是类型安全。
* 存储或检索值类型时通常发生装箱和取消装箱操作，带来很大性能损耗
* Array用法和数组几乎一样，定义时需要指定长度，ArrayList通集合一样，定义无需指定长度
### 泛型List
* 是ArrayList类的泛型等效类，大部分用法与ArrayList相似，继承了IList接口，关键在于需要声明List集合内数据的对象类型
* 只有一个维度
* 对比ArrayList，大多数情况下执行更好，类型安全，如果类型T是引用类型，则行为完全相同，需要考虑实现和装箱问题
* 声明List<T> mlist = new List<T>();
* 添加元素mlist.Add("d");
* 添加集合元素string[] Arr2 = {"f", "g", "h"};  mlist.AddRange(Arr2);
* 在index位置添加一个元素Insert(int index, T item);
* 遍历List中元素foreach(T element in mlist)
* 删除元素List.Remove(T item);
* 判断某个元素是否在该List中List.Contains(T item)
* 给List里面元素排序List.Sort() 默认是元素每一个字母按升序
* 给List里面元素顺序反转List.Reverse()可以与List.Sort()配合使用
* List清空List.Clear()
* 获得元素数目List.Clear()
### 泛型强大方法
* 检索与指定谓词所定义的条件相匹配的所有元素List.FindAll();
* List.Find() 搜索与指定谓词所定义的条件相匹配的元素，并返回整个List中第一个匹配的元素
* 委托给拉姆达表达式string listFind = mlist.Find(name => (if(name.length>3) return true;
* 委托给一个函数public bool ListFind(string name)
* List.FindLast()确定是否List中每个元素都与指定谓词所定义的条件相匹配，用法与List.Find相同
* List.TrueForAll()确定是否List中每个元素都与指定的谓词所定义的条件相匹配
## 抽象类和接口的异同
### 相同点
1. 不能被实例化
2. 接口的实现类或抽象类的子类都只有实现了接口或抽象类中的方法后才能被实例化
### 不同点
1. 接口只有定义，不能实现，抽象类的方法在抽象类中被实现
2. 接口使用implements实现，抽象类只能被继承extends
3. 设计理念不同，接口是has-a,抽象类是is-a
4. 接口中成员变量默认修饰符为public static final静态不能被修改，必须赋初值。抽象类可以有自己的数据成员变量，也可以有非抽象的成员变量，成员变量默认为default。抽象类中方法前面有abstract修饰，不能用private、static、synchronize、native修饰，方法必须以分号结尾，不带花括号。
## 什么时候用对象什么时候用接口
抽象类是对类的抽象，接口是对行为的抽象，行为跨越不同类的对象，可使用接口，对于一些现实的类对象，用继承抽象类。抽象类从子类中发现了公共的东西，泛化出父类，然后子类继承父类，而接口是根本不知道子类的存在，方法如何实现还不确定，预先定义。
### in out ref
* in关键字通过引用传递参数，让形参成为实参的别名，这必须是变量。对形参执行的任何操作都是对实参执行的。类似于ref或out关键字，不同之处在于in参数无法通过调用的方法进行修改
* Out关键字通过引用传递参数，让形参成为实参的别名，这必须是变量。换而言之，对形参执行的任何操作都是对实参执行的。与ref相似，只不过ref要求在传递前初始化变量。类似于in关键字，不允许通过调用方法来修改参数值，使用out参数，方法定义和调用方法必须显式使用out关键字
* ref传递变量地址给方法，传递前必须初始化，否则编译器报错，out型不需要初始化，ref传递变量，数值传入方法中，out型无法将数据传入方法中，ref型有进有出，out型只出不进

