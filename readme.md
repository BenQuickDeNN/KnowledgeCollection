[TOC]
# C++
## C++基本概念
### 面向对象的三个基本特征
* 封装
* 继承
* 多态

### 继承与组合
#### 继承
我已了解。

#### 组合
A类里面包含B类的对象，并且使用B类的功能，例如：
```c++
/**
 * 动物
 */
public class Animal {
    public void breathing() {
        System.out.println("呼气...吸气...");
    }
}
/**
 * 爬行动物
 * 组合
 */
public class Reptilia {

    private Animal animal;

    public Reptilia(Animal animal) {
        this.animal = animal;
    }

    public void crawling() {
        System.out.println("爬行...");
    }
    public void breathing() {
        animal.breathing();
    }


    public static void main(String[] args) {
        Animal animal = new Animal();
        Reptilia reptilia = new Reptilia(animal);
        reptilia.breathing();;
        reptilia.crawling();
    }
}

```

### 构造函数
构造函数不能是虚函数，虚函数的调用是通过虚函数表来查找的，虚函数表需要通过构造函数来完成初始化。在调用构造函数前虚函数表还没建立，所以构造函数不能是虚函数。

### 析构函数
* 析构函数可以是虚函数，因为这个时候虚函数表已经初始化。
* 析构函数也常常是虚函数。特别是存在继承关系时，若父类的析构函数不是虚函数，则使用delete删除子类指针时，只释放父类，不释放子类，会造成内存泄漏。

### 对象实例化
* 在栈上创建对象：classA a; 程序自动析构，先构造的后析构。
* 在堆上创建对象：classA *a = new classA(); 需要执行delete才能析构。

### 模板特化
指明模板中的类型，使模板函数对某种类型特例化。例如下面的全特化例子中，普通的cmp模板函数比较t1和t2的大小，但当t1、t2的类型为```char*```时运算符“==”比较的是它们的首地址。所以为了比较```char*```类型需要对```char*```类型进行特化。

#### 全特化
```c++
#include <iostream>
#include <cstring>
using namespace std;
template<typename T>
class A {
  public:
    bool cmp(const T &t1, const T &t2) {
        return t1 == t2;
    }
};
template<>
class A<char*> {   // 这里是全特化，仍然需要tempalte<>声明
  public:
    bool cmp(const char* t1, const char* t2) {
        while(*t1 != '\0' && *t2 != '\0') {
            if(*t1 != *t2) {
                return false;
            }
            ++t1;
            ++t2;
        }
        return true;
    }
};
```
上面的例子就是全特化，模板类全部特化为具体的类型```char*```。

#### 偏特化
部分模板类进行特化，如下面的例子。函数没有偏特化。
```c++
#include <iostream>
#include <cstring>
using namespace std;

template<typename T, typename T1>
class A {
  public:
    A() = default;
    A(const T1& n) {
        cout << n << endl;
    }
    bool cmp(const T &t1, const T &t2) {
        return t1 == t2;
    }
};

template<typename T1>  // 片特化
class A<char*, T1> {
  public:
    A() = default;
    A(T1& n) {
        cout << n << endl;
    }
    bool cmp(const char* t1, const char* t2) {
        while(*t1 != '\0' && *t2 != '\0') {
            if(*t1 != *t2) {
                return false;
            }
            ++t1;
            ++t2;
        }
        return true;
    }
};
int main() {
    char* p1 = "hello";
    char* p2 = "hello";
    A<int, char*>c(p1);
    cout << c.cmp(1, 2) << endl;
    A<char*, char*>c1(p2);  // 即使是片特化，也要全部声明模板
    cout << c1.cmp(p1, p2) << endl;
    return 0;
}
```

### 指针和引用的区别
* 指针是存放内存地址的一种变量，而引用的本质是变量的别名。
* 指针指向的是一个内存地址， 因此可以指向一块为0x00000000的地址，声明时可以暂时不初始化（不推荐），即pointer = NULL；引用是变量的别名，别名就一定对应着一个“本名”，因此必须在声明时就初始化，且不能初始化为空。
* 指针可以重新赋值，使它可以在不同的时期指向不同的对象，而引用必须从一而终。

### 重载、重写、隐藏
#### 重载
重载从overload翻译过来，是指同一可访问区内被声明的几个具有不同参数列表（参数的类型，个数，顺序不同）的同名函数，根据参数列表确定调用哪个函数，重载不关心函数返回类型。重载的特性包括：

* 相同的范围（在同一个作用域中）。
* 函数名字相同。
* 参数不同列表。
* virtual关键字可有可无。
* 返回类型可以不同。

#### 重写
重写翻译自override，是指派生类中存在重新定义的函数。其函数名，参数列表，返回值类型，所有都必须同基类中被重写的函数一致。只有函数体不同（花括号内），派生类调用时会调用派生类的重写函数，不会调用被重写函数。重写的基类中被重写的函数必须有virtual修饰。重写的特性包括：

* 不在同一个作用域（分别位于派生类与基类）。
* 函数名字相同。
* 参数相同列表（参数个数，两个参数列表对应的类型）。
* 基类函数必须有virtual关键字，不能有 static，大概是多态的原因吧。
* 返回值类型（或是协变），否则报错。
* 重写函数的访问修饰符可以不同。尽管virtual是private的，派生类中重写改写为public、protected也是可以的。

#### 隐藏
隐藏是指派生类的函数屏蔽了与其同名的基类函数。注意只要同名函数，不管参数列表是否相同，基类函数都会被隐藏。隐藏的特性包括：

* 不在同一个作用域（分别位于派生类与基类）。
* 函数名字相同。
* 返回类型可以不同。
* 参数不同，此时，不论有无virtual关键字，基类的函数将被隐藏（注意别与重载混淆）而不是被重写。
* 参数相同，但是基类函数没有virtual关键字。此时，基类的函数被隐藏（注意别与覆盖混淆）。

## C++基本特性
### const和constptr的区别
const修饰运行时常量；constptr修饰编译期常量，修饰的对象在编译期间就能计算完毕（是不是不能修饰表达式？）。

### new的作用
对于内置类型而言，new仅仅是分配内存，除非后面显示加(),相当于调用它的构造函数，对于自定义类型而言，只要一调用new，那么编译器不仅仅给它分配内存，还调用它的默认构造函数初始化，即使后面没有加()。

### for循环中++i和i++的区别
仅仅是形式不同，对结果不产生影响。

### 枚举
对于枚举中的全局变量，
如果是函数外定义那么是0 。
如果是函数内定义，那么是随机值，因为没有初始化，不存储在内存的静态存储区。

### 虚函数
* 虚函数不是类的成员函数。
* 虚函数一般用来定义类接口，告诉程序员要在子类中实现这些接口。

### 浅拷贝和深拷贝
浅拷贝与深拷贝的区别可见[CSDN博客](https://www.cnblogs.com/wjcoding/p/10955017.html)。

举个栗子：
```c++
/* 没有重载=之前 */
A a ;
A b; 
a = b; 
// 这里是赋值操作。 
A a; 
A b = a;  
// 这里是浅拷贝操作。 

/* 重载 = 之后 */
A a ; 
A b; 
a = b; 
// 这里是深拷贝操作。 
A a; 
A b = a;  
// 这里还是浅拷贝操作。
```

### 继承
父类指针指向子类实例对象，调用普通重写方法时，会调用父类中的方法。而调用被子类重写的虚函数时，会调用子类中的方法。

### 内存管理
#### 堆、栈、自由存储区、全局/静态存储区
在C++中，内存空间分为：栈、堆、自由存储区、全局/静态存储区、常量存储区。

* **栈**：在执行函数时，函数内局部变量的存储单元都可以在栈上创建，函数执行结束是这些存储单元自动被释放。栈内存分配运算内置于处理器的指令集中，效率高，分配的内存容量有限。
* **堆**：就是那些由malloc等分配的内存块，用free释放内存。
* **自由存储区**：那些由new分配的内存块，由应用程序去控制，一般一个new就要对应一个delete，如果程序员没有释放掉，那么在程序结束后操作系统会自动回收。
* **全局/静态存储区**：全局变量和静态变量被分配到同一块内存中，在以前的C语言中全局变量又分为初始化的和未初始化的，在C++里面没有这个区分了，他们共同占用同一块内存区。
* **常量存储区**：这是一块比较特殊的存储区，他们里面存放的是常量，不允许修改。

#### 堆和自由存储区的区别与联系
* 堆是C语言和操作系统的术语、是操作系统维护的一块内存，而自由存储是C++中通过new与delete动态分配和释放对象的抽象概念。堆与自由存储区并不等价。
* new所申请的内存区域在C++中称为自由存储区。藉由堆实现的自由存储，可以说new所申请的内存区域在堆上。

#### 堆和栈的区别
* **管理方式**：对于栈来讲，是由编译器自动管理，无需我们手工控制；对于堆来说，释放工作由程序员控制，容易产生memory leak（内存泄漏、内存溢出）。
* **空间大小**：一般在32位系统下，堆内存可以达到4G的空间，从这个角度来看堆内存几乎是没有什么限制的。但是对于栈来讲，一般都是有一定的空间大小的，默认的栈空间大小是1M。Linux系统设置栈大小方法：“ulimit -a”显示当前用户栈大小，“ulimit -s X”将当前用户栈大小设置成XKB。
* **碎片问题**：对于堆来讲，频繁的malloc/free势必会造成内存空间的不连续，从而造成大量的碎片，使程序效率降低。栈是先进后出的队列，以至于永远都不可能有一个内存块从栈中间弹出。
* **分配方式**：堆都是动态分配的，没有静态分配的堆。栈有2种分配方式：静态分配和动态分配。静态分配是编译器完成的，比如局部变量的分配。动态分配由alloca函数进行分配，但是栈的动态分配和堆是不同的，它的动态分配是由编译器进行释放，无需我们手工实现。
* **分配效率**：栈是机器系统提供的数据结构，计算机会在底层对栈提供支持：分配专门的寄存器存放栈的地址，压栈出栈都有专门的指令执行，这就决定了栈的效率比较高。堆则是C/C++函数库提供的，它的机制是很复杂的，例如为了分配一块内存，库函数会按照一定的算法在堆内存中搜索可用的足够大小的空间，如果没有足够大小的空间（可能是由于内存碎片太多），就有可能调用系统功能去增加程序数据段的内存空间，这样就有机会分到足够大小的内存，然后进行返回。显然，堆的效率比栈要低得多。

#### 关于内存的一些编程习惯总结
* 用malloc或new申请内存之后，应该立即检查指针值是否为NULL。防止使用指针值为NULL的内存。
* 不要忘记为数组和动态内存赋初值。防止将未被初始化的内存作为右值使用。
* 避免数组或指针的下标越界，特别要当心发生“多1”或者“少1”操作。
* 动态内存的申请与释放必须配对，防止内存泄漏。
* 用free或delete释放了内存之后，立即将指针设置为NULL，防止产生“野指针”。

### static关键字的作用
* 保持内容持久：static修饰的变量存在静态存储区，具有全局生存期。
* 默认初始化为0：static修饰的变量如果在初始化时不赋值，则默认为零。
* 类成员中声明static：使成员成为全局类成员而不是对象的成员。
* 隐藏：当同时编译多个文件时，所有未加static的全局变量和函数都具有全局可见性，但加上static后的变量，只在当前文件可见。

### inline和宏定义的区别
内联函数和宏定义都是在预处理的位置将代码展开，不需要像普通函数那样需要额外的时间和空间开销。

#### 宏定义的缺点
* 没有参数类型检查，和参数类型转换。
* 不能访问类的成员变量。
* 宏定义使用参数时，是严格的替换策略，无论你得参数时是何种形式，在展开代码中都是用形参数代替实参，这样，宏定义很容易产生二义性，它的使用就存在一系列的隐患。

#### 内联函数的优点
* 有参数类型检查，更加安全。
* 内联函数是在程序运行时展开，而且是进行参数传递。在编译时将函数体嵌入每一个调用处，编译时，类似宏替换，使用函数体替换调用处的函数名，在代码中用inline关键字修饰，在类中声明的成员函数，自动转换为内联函数。
* inline关键字只是对编译器的一个定义，如果函数本地不符合内联函数的标准，编译器就会将这个函数当作是普通函数。
* 类中的成员函数默认是内联函数(要求成员函数没有循环和递归，当其中存在循环的递归的时候，编译器会将其默认为一个普通函数处理)。

#### 内联函数缺点
* 不允许有循环或递归。
* 内联函数的定义必须出现在第一次调用内联函数之前。
* 因为内联函数是在调用处展开，所以会使代码边长，占用更多内存。

#### 内联函数与宏定义的区别
* 内联函数在运行时可调试，而宏定义不可以。
* 编译器会对内联函数的参数类型做安全检查或自动类型转换，而宏定义则不会。
* 内联函数可以访问类的成员变量，而宏定义则不能。

### malloc()、free()函数的实现
#### 进程地址空间构成
![Linux进程地址空间构成](image/linux_process_address_space.jpg)

每个Linux进程都有一个地址空间，逻辑上由以下五部分组成：
* **text**：代码段，包含形成程序可执行代码的机器指令。它是由编译器和汇编器把C、C++或者其他程序的源码转化为机器码产生的。通常，代码段只读。
* **data**：包含所有已初始化的程序变量、字符串、数字和其他数据的存储。
* **bss**：包含所有未初始化的静态数据，这些数据将被初始化为0。
* **heap**：包含进程动态申请的内存。向上增长。
* **stack**：包含局部变量、函数参数、指针拷贝。向下增长。

其中，data、bss、heap统称为**数据段**。heap的顶部被一个**brk(break)**指针标示，在heap申请内存时需要请求操作系统增加brk，而在heap上释放内存时需要请求操作系统减小brk。

Linux/Unix类操作系统提供了一个**sbrk**系统调用，可以用于修改brk指针。
```c
#include <unistd.h>
sbrk(0);    // 返回当前brk指针的地址
sbrk(x);    // brk指针增加x字节，内存被申请
sbrk(-x);   // brk指针减少x字节，内存被释放
```

#### malloc()的实现
malloc函数的基本实现：
```c
void *malloc(size_t size)
{
    void *block;
    block = sbrk(size);
    if (block == (void*) - 1)
        return NULL;
    return block;
}
```
其中，void*是一个特殊的指针，其值为0。

如果想释放一块malloc申请的内存，我们需要知道这块内存的首地址、该内存块的大小、该内存块是否被标记为free，我们把记录以上这些数据的结构体叫作**header_t**。
```c
struct header_t
{
    size_t size;        // 内存块大小
    unsigned is_free;   // 当前内存块是否已被标记为free
};
```
特别地，若当前进程申请的内存不连续（比如其他进程在当前进程运行期间调用sbrk申请了内存），则还需要维护一个header_t链表。

![header_t链表](image/header_t_linked_list.jpg)
```c
struct header_t
{
    size_t size;            // 内存块大小
    unsigned char is_free;  // 当前内存块是否已被标记为free
    struct header_t *next;  // 下一个内存块的地址，用于维护header_t链表
};
```
设置header_t链表的head和tail以便于对链表进行追踪：
```c
struct header_t *head, *tail;
```
使用一个简单的线程锁来保证内存分配器是线程安全的（其他线程/进程可能也正要调用malloc，加锁以防止数据污染）：
```c
#include <pthread.h>
pthread_mutex_t global_malloc_lock;
```
综上，比较完整的malloc()函数实现如下：
```c
struct header_t *get_free_block(size_t size)
{
    struct header_t *curr = head;
    while (curr)
    {
        if (curr->is_free && curr->size >= size)
            return curr;
        curr = curr->next;
    }
    return NULL;
};
void *malloc(size_t size)
{
	size_t total_size;
	void *block;
	struct header_t *header;
	if (!size)
		return NULL;
	pthread_mutex_lock(&global_malloc_lock);
	header = get_free_block(size);
	if (header) {
		header->is_free = 0;
		pthread_mutex_unlock(&global_malloc_lock);
		return (void*)(header + 1);
	}
	total_size = sizeof(struct header_t) + size;
	block = sbrk(total_size);
	if (block == (void*) -1) {
		pthread_mutex_unlock(&global_malloc_lock);
		return NULL;
	}
	header = block;
	header->size = size;
	header->is_free = 0;
	header->next = NULL;
	if (!head)
		head = header;
	if (tail)
		tail->next = header;
	tail = header;
	pthread_mutex_unlock(&global_malloc_lock);
	return (void*)(header + 1);
}
```
分析以上代码：
* 如果所申请内存的size为0，返回NULL。
* 申请线程锁，遍历内存块组成的链表，查找是否有满足size并且标记为free的内存块。
* 如果找到了，将该内存块标记为non-free，释放线程锁，返回该内存快的地址。
* 如果没有找到符合条件的内存块，那么我们从heap的顶部重新申请新的内存块，内存的大小: ```total_size = sizeof(struct header_t) + size```，然后调用sbrk(total_size)修改brk指针。

#### free()函数的实现
free() 首先要确定当前要释放的内存块是否位于heap的顶部（链表的尾部）。如果是的话，release it to the OS. 如果不是的话，标记为free，便于以后重用。
```c
void free(void *block)
{
	struct header_t *header, *tmp;
	void *programbreak;

	if (!block)
		return;
	pthread_mutex_lock(&global_malloc_lock);
	header = (struct header_t*)block - 1;

	programbreak = sbrk(0);
	if ((char*)block + header->size == programbreak) {
		if (head == tail) {
			head = tail = NULL;
		} else {
			tmp = head;
			while (tmp) {
				if(tmp->next == tail) {
					tmp->next = NULL;
					tail = tmp;
				}
				tmp = tmp->next;
			}
		}
		sbrk(0 - sizeof(struct header_t) - header->size);
		pthread_mutex_unlock(&global_malloc_lock);
		return;
	}
	header->is_free = 1;
	pthread_mutex_unlock(&global_malloc_lock);
} 
```

### mmap()函数
mmap()用来实现内存与文件之间的映射。

### using关键字
* using可以用来选择命名空间，比如常用的std命名空间：using namespace std;。
* using与也可以发挥类似于宏定义的作用，比如下面的例子用view_t来指代ArrayView<_T, _Rank>：
```c++
using view_t = ArrayView<_T,_Rank>;
view_t& array_view_;
```

### 类和结构体所占内存大小
类所占内存的大小是由成员变量（static修饰的静态变量除外）决定的，成员函数是不计算在内的。

举两个栗子：

#### 例1
```c++
class A
{
private:
    char a1;
    short a2;
    int a3;
    long a4;
public:
    A(){}
};
struct B
{
    char b1;
    short b2;
    int b3;
    long b4;
};
int main()
{
    cout << sizeof(char) << endl;
    cout << sizeof(short) << endl;
    cout << sizeof(int) << endl;
    cout << sizeof(long) << endl;
    A a;
    cout << sizeof(A) << ',' << sizeof(a) << endl;
    B b;
    cout << sizeof(B) << ',' << sizeof(b) << endl;
    return 0;
}

```
输出结果：
```
1
2
4
4
12,12
12,12
```
说明本例中类和结构体都是4字节（32位）对齐。对齐位数和处理器、操作系统有关，在编译时也可以设置。

#### 例2
```c++
class A
{
public:
    char a1:1;
    short a2:1;
    int a3:1;
    //long a4:1;
    A(){}
};
struct B
{
    //char b1:2;
    //short b2:4;
    int b3:1;
    long b4:31;
};
int main()
{
    A a, *pa;
    cout << sizeof(A) << ',' << sizeof(a) << ',' << sizeof(pa) << endl;
    B b, *pb;
    cout << sizeof(B) << ',' << sizeof(b) << ',' << sizeof(pb) << endl;
    return 0;
}
```
输出结果：
```
8,8,8
4,4,8
```
说明位域可以改变变量实际所占的位数。

## C++ STL标准模板库
### map
#### 底层数据结构
![红黑树](image/红黑树.jpg)

红黑树
#### 红黑树的性质
* 节点是红色或黑色。
* 根节点是黑色。
* 所有叶子节点是黑色（叶子节点是NUIL）。
* 每个红色节点的子节点都是黑色（从每个叶子到根的所有路径上不能有两个连续的红色节点）。
* 从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点。

#### 红黑树的特点
* 搜索时间复杂度为logN。
* 与普通AVL相比允许最大深度是最小深度的2倍（普通AVL允许最大深度比最小深度多2层）。
* 维护开销比AVL更低。

#### 节点插入
* 根据一般二叉查找树的插入步骤， 把新节点 z 插入到 某个叶节点的位置上，然后将 z 着 为红色。 为了保证红黑树的性质能继续保持，再对有关节点重新着色并旋转。
* 如果新插入的是黑色节点，那么它所在的路径上就多出一个黑色的节点，所以新插入的节点一定要设成红色。 但是如果z的父节点也是红色，这就违反了每个红色节点的两个子节点都黑色的性质。

#### 节点删除
* 若删除的节点是红色，则不做任何操作，红黑树的任何属性都不会被破坏。
* 若删除的节点是黑色的，显然它所 在的路径上就少一个黑色节点，红黑树的性质就被破坏了，这时执行一个Delete-Fixup()函数来修补这棵树。 一个节点被删除之后，一定 有一个它的节点代替了它的位置，即使是叶节点被删除后，也会有一个空节点来代替它的位置。

#### 用法
##### 检测map对象中是否已包含某元素
```c++
map.count(key) // 若返回结果大于0，则包含key
```
或
```c++
if (map.find(key) != map.end()) // 若条件为真，则包含key
```

##### map[10]=20这种操作和insert(pair<int, int>(10, 20))有什么区别？
* 当key=10存在的时候，map[10]=20会用20去覆盖原来的值，而insert什么都不做。
* []操作符会多调用一次实值对象的构造函数产生一个默认对象，而真正的产生作用的是对默认对象的赋值。

##### map遍历删除所有元素
```c++
for (auto iter = a.begin(); iter != a.end(); ++iter) 
    a.erase(iter);
```
删除后当前迭代器会失效，通过iter=iter+1使迭代器指向下一个元素。

### unordered_map
#### 底层数据结构
哈希表

#### 接口
与map相似

#### 与map比较
优点：哈希表查找时间复杂度是常量级，红黑树查找时间复杂度是logN。

缺点：无序，对于某些需要按序存储的情形，map更胜任。

### vector
#### emplace_back()
类似于push_back函数，但不同，在vector后方创建一个与参数相同的元素，而push_back是将参数的引用推入后方。

### copy()
#### 头文件
```c++
#include <vector>
```
#### 在两个容器之间复制元素
```c++
copy(源首地址, 源末地址, 目标首地址);
```

## C++11新特性
### 智能指针
#### 基本特性
* 智能指针利用了一种叫做RAII（资源获取即初始化）的技术对普通的指针进行封装，这使得智能指针实质上是一个对象，行为表现的却像一个指针。
* 智能指针的作用是防止忘记调用delete释放内存和程序异常的计入catch块忘记释放内存。另外指针释放时机也非常有考究。

#### unique_ptr\<T>
##### 分配内存
与shared_ptr不同，C++11标准中unique_ptr没有定义类似make_shared的操作（C++14中有make_unique\<T>()），因此只可以使用new来分配内存，并且由于unique_ptr不可拷贝和赋值，初始化unique_ptr必须使用直接初始化的方式。
```c++
unique_ptr<int> up1(new int());    //okay,直接初始化
unique_ptr<int> up2 = new int();   //error! 构造函数是explicit
unique_ptr<int> up3(up1);          //error! 不允许拷贝
```

##### 销毁指向的对象
在析构函数中销毁。

##### 接口
```c++
unique_ptr<T> up; // 空的unique_ptr，可以指向类型为T的对象，默认使用delete来释放内存
unique_ptr<T,D> up(d); // 空的unique_ptr同上，接受一个D类型的删除器d，使用删除器d来释放内存
up = nullptr; // 释放up指向的对象，将up置为空
up.release(); // up放弃对它所指对象的控制权，并返回保存的指针，将up置为空，不会释放内存
up.reset(…); // 参数可以为 空、内置指针，先将up所指对象释放，然后重置up的值.
```

##### 传值
一个unique_ptr将指向的内容传递给另一个unique_ptr对象：
```c++
unique_ptr<int> p1 = make_unique<int>();
unique_ptr<int> p2 = std::move(p1);
```

#### shared_ptr\<T>
共享计数指针。

##### 作用
多线程程序经常会遇到在某个线程A创建了一个对象,这个对象需要在线程B使用，在没有shared_ptr时,因为线程A,B结束时间不确定,即在A或B线程先释放这个对象都有可能造成另一个线程崩溃，所以为了省时间一般都是任由这个内存泄漏发生。当然也可以经过复杂的设计，由一个监控线程来统一删除，但这样会增加代码量和复杂度.这下好了,shared_ptr 可以方便的解决问题,因为它是引用计数和线程安全的。shared_ptr不用手动去释放资源，它会智能地在合适的时候去自动释放。

##### 原理
当多个shared_ptr管理同一个指针，仅当最后一个shared_ptr析构时，指针才被delete。这是怎么实现的呢？答案是：引用计数（reference counting）。引用计数指的是，所有管理同一个裸指针（raw pointer）的shared_ptr，都共享一个引用计数器，每当一个shared_ptr被赋值（或拷贝构造）给其它shared_ptr时，这个共享的引用计数器就加1，当一个shared_ptr析构或者被用于管理其它裸指针时，这个引用计数器就减1，如果此时发现引用计数器为0，那么说明它是管理这个指针的最后一个shared_ptr了，于是我们释放指针指向的资源。

### 参数包
详见[CppRef](https://en.cppreference.com/w/cpp/language/parameter_pack)。

A template parameter pack is a template parameter that accepts zero or more template arguments (non-types, types, or templates). A function parameter pack is a function parameter that accepts zero or more function arguments. A template with at least one parameter pack is called a variadic template (可变模板)。

### std::conditional
#### 用法
```c++
#include <type_traits>
template< bool B, class T, class F >
struct conditional;
```
type T if B==true, F if B==false

#### 示例
```c++
#include <iostream>
#include <type_traits>
#include <typeinfo>
int main() 
{
    typedef std::conditional<true, int, double>::type Type1;
    typedef std::conditional<false, int, double>::type Type2;
    typedef std::conditional<sizeof(int) >= sizeof(double), int, double>::type Type3;
 
    std::cout << typeid(Type1).name() << '\n';
    std::cout << typeid(Type2).name() << '\n';
    std::cout << typeid(Type3).name() << '\n';
}
```
上述代码的输出：
```
i
d
d
```

### default constructors
详见[CppRef](https://en.cppreference.com/w/cpp/language/default_constructor)

A default constructor is a constructor which can be called with no arguments (either defined with an empty parameter list, or with default arguments provided for every parameter). A type with a public default constructor is DefaultConstructible.

# 高性能计算
## 并行数值算法

## 并行程序设计
### 脉动阵列（systolic array）

# 数据结构与算法
