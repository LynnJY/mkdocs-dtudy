

# **结构体声明与定义**

**第一种**：只有结构体定义

```cpp
struct stuff{
        char job[20];
        int age;
        float height;
};
```

**第二种**：附加该结构体类型的"结构体变量"的初始化的结构体定义

```cpp
//直接带变量名Huqinwei
struct stuff{
        char job[20];
        int age;
        float height;
}Huqinwei;
```

也许初期看不习惯容易困惑，其实这就相当于两步合并一步：先定义结构体stuff，再定义变量Huqinwei

```cpp
struct stuff{
        char job[20];
        int age;
        float height;
};
struct stuff Huqinwei;
```

**第三种：匿名结构体**

如果该结构体你只用一个变量Huqinwei，而不再需要用来定义第二个变量。

```cpp
struct stuff yourname;
```

那么，附加变量初始化的结构体定义还可进一步简化出 **第三种**：

把结构体名称去掉，用匿名结构体直接定义一个结构体对象（习惯用对象这词了，大家都要习惯，没有人用纯C了），这样更简洁，不过也不能定义其他同类型结构体变量了（除非用typeof再逆向找到这个类型。）

```cpp
struct{//匿名结构体
        char job[20];
        int age;
        float height;
}Huqinwei;//变量Huqinwei

int main(){
    struct Huqinwei little_h;//错误，Huqinwei是一个匿名结构体类型的变量，而不是一个结构体类型，struct Huqinwei用法不成立

}
```

**第三种附加：**使用typeof重新找到匿名结构体变量HU的结构体，来定义HU3

并且定义指针ptr1,ptr2

只是理论上可以这样干，但是实际不推荐这样，不可读，无法维护。所以可以无视这种用法，最好是定义struct aa{int a;}，而不是定义struct {int a;}aa; 前者是结构体类型，后者是结构体变量。

```cpp
#include

struct
{
        char a;
        short b;
        int c;
}HU;

struct
{
        char a;
        short b;
        int c;
}HU2;

int main(){

        printf("%ld\n",sizeof(HU));

        typeof(HU) HU3;
        printf("%ld\n",sizeof(HU3));
        printf("%ld\n",sizeof(HU2));
        typeof(HU) *ptr1 = &HU;
        typeof(HU) *ptr2 = &HU3;
        ptr2->b = 444;
        printf("%d\n",ptr2->b);
        ptr1 = ptr2;
        printf("%d\n",ptr1->b);

}
```

同样的写法，再定义一个结构体成员HU2，他们的"类型"不同，因为如果类型相同，肯定会报错了，实际并没有报。

![](https://img-blog.csdnimg.cn/20190318173050769.png)

不过内存操作角度，HU2和HU应该没有任何区别，也可以用指针强行更改，前提是确认安全，比如没有不同文件不同平台对齐不兼容这种问题，所以C很万能，也很危险

# **结构体变量及其内部成员变量的定义及访问**

就像刚才的第二种提到的，结构体变量的声明可以用：

```cpp
struct stuff yourname;
```

其成员变量的定义可以随声明进行：

```cpp
   struct stuff Huqinwei = {"manager",30,185};
```

也可以考虑结构体之间的"赋值"(拷贝构造)：

```cpp
        struct stuff faker = Huqinwei;
//或    struct stuff faker2;
//      faker2 = faker;
打印，可见结构体的每一个成员变量一模一样
```

如果不使用上边两种方法，那么成员数组的操作会稍微麻烦（用for循环可能好点）

```cpp
        Huqinwei.job[0] = 'M';
        Huqinwei.job[1] = 'a';
        Huqinwei.age = 27;
        Huqinwei.height = 185;
```

结构体成员变量的访问除了可以借助符号"."，还可以用"->"访问（下边会提）。

# **引用（C++）、指针和数组**

首先是引用和指针：

```cpp
struct stuff{
        char job[20];
        int age;
        float height;
};

int main()
{
        struct stuff huqinwei987;//定义stuff结构体的变量huqinwei987

        struct stuff &ref = huqinwei987;//定义huqinwei987的引用ref

        ref.age = 100;//通过ref修改huqinwei987的变量
        //打印对比
        printf("huqinwei987.age is %d\n",huqinwei987.age);
        printf("ref.age is %d\n",ref.age);

        struct stuff *ptr = &huqinwei987;//定义到huqinwei987的指针
        ptr->age = 200;//通过指针修改huqinwei987的变量
        //打印对比
        printf("huqinwei987.age is %d\n",huqinwei987.age);
        printf("ptr->age is %d\n",ptr->age);

        //既然都写了，把指针引用也加上吧
        struct stuff *&refToPtr = ptr;//定义到指针ptf的引用，通过指针引用修改huqinwei987的变量
        refToPtr->age = 300;
        printf("huqinwei987.age is %d\n",huqinwei987.age);
        printf("refToPtr->age is %d\n",refToPtr->age);

}
```

更正：之前给引用的初始化语句写错了，而且没注明引用是纯C中没有的东西。

结构体也不能免俗，必须支持数组：

```cpp
//结构体中数组变量定义方法
struct test{
        int array[3];
        int val;
};
//对于数组和变量同时存在的情况，有如下定义方法：
        struct test student[3] =      {{{66,77,55},0},
                                        {{44,65,33},0},
                                        {{46,99,77},0}};
//特别的，可以简化成：
        struct test student[3] =       {{66,77,55,0},
                                        {44,65,33,0},
                                        {46,99,77,0}};
```

# **结构体嵌套**

结构体嵌套其实没有太意外的东西，只要遵循一定规律即可：

```cpp
//对于"一锤子买卖"，只对最终的结构体变量感兴趣，其中A、B也可删，不过最好带着
struct A{
        struct B{
             int c;
        }
        b;
}
a;
//使用如下方式访问：
a.b.c = 10;
```

**特别的,可以一边定义结构体B，一边就使用上：**

```cpp
struct A{
        struct B{
                int c;
        }b;
        struct B sb;
}a;
```

使用方法与测试：

```cpp
        a.b.c = 11;
        printf("%d\n",a.b.c);
        a.sb.c = 22;
        printf("%d\n",a.sb.c);
结果无误。
```

但是如果嵌套的结构体B是在A内部才声明的，并且没定义一个对应的对象实体b，这个结构体B的大小还是不算进结构体A中。

# **结构体与函数传参**

关于传参，首先，把结构体中的int成员变量当做和普通int变量一样的东西来使用（当做函数参数），是不用脑子就想到的一种方法，如下：

```cpp
void func(int);
func(a.b.c);
```

**另外的主要用法就是传递副本和指针了 ：**

```cpp
//20210805更新：用了能完整跑通的代码，降低了文章前后所需的连贯性，避免读者拼接代码编译不过——https://mp.csdn.net/mp_blog/creation/editor/23625823
struct A {
    struct B {
        int c;
    }b;
    struct B sb;
}a;

//设立了两个函数，分别传递struct A结构体和其指针。
void func1(struct A a) {//复制结构体
    printf("%d\n", a.b.c);
}
void func2(struct A* a) {//传递结构体指针
    printf("%d\n", a->b.c);
}
void func3(struct A& a) {//进阶：传递结构体引用，效用上近似结构体指针，但访问形式不同于指针
    printf("%d\n", a.b.c);
}
int main() {
    a.b.c = 112;
    struct A * pa;
    pa = &a;
    func1(a);
    func2(&a);
    func2(pa);
    func3(a);
}
```



# **占用内存空间**

struct结构体，在 **结构体**定义的时候不能申请内存空间，不过如果是 **结构体变量**，声明的时候就可以分配——两者关系就像C++的类与对象，对象才分配内存（不过严格讲，作为代码段，结构体定义部分".text"真的就不占空间了么？当然，这是另外一个范畴的话题）。

结构体的大小通常（只是通常）是结构体所含变量大小的总和，下面打印输出上述结构体的size：

```cpp
        printf("size of struct man:%d\n",sizeof(struct man));
        printf("size:%d\n",sizeof(Huqinwei));
结果毫无悬念，都是28：分别是char数组20，int变量4，浮点变量4.

```

**下边说说不通常的情况：**

对于结构体中比较小的成员，可能会被强行对齐，造成空间的空置，这和读取内存的机制有关，为了效率。通常32位机按4字节对齐，小于的都当4字节，有连续小于4字节的，可以不着急对齐，等到凑够了整，加上下一个元素超出一个对齐位置，才开始调整，比如3+2或者1+4，后者都需要另起（下边的结构体大小是8bytes），相关例子就多了，不赘述。

```cpp
struct s
{
char a;
short b;
int c;
}
```

相应的，64位机按8字节对齐。不过对齐不是绝对的，用#pragma pack()可以修改对齐，如果 **改成1，结构体大小就是实实在在的成员变量大小的总和了。**
补一个代码，压入1字节对齐，定义s，然后弹出，使用默认，定义s2，两个结构体大小分别为7和8

```cpp
#include
#pragma pack(push,1)
struct s
{
        char a;
        short b;
        int c;
};
#pragma pack(pop)
struct s2
{
        char a;
        short b;
        int c;
};

int main(){

        printf("%ld\n",sizeof(struct s));
        printf("%ld\n",sizeof(struct s2));

}
```

```
$ ./a.out
7
8
```

和C++的类不一样，结构体不可以给 **结构体内部变量**初始化，。

如下，为错误示范：

```cpp
#include
//直接带变量名Huqinwei
struct stuff{
//      char job[20] = "Programmer";
//      char job[];
//      int age = 27;
//      float height = 185;
}Huqinwei;
```

PS：结构体的声明也要注意位置的，作用域不一样。

C++的结构体变量的声明定义和C有略微不同，说白了就是更"面向对象"风格化，要求更低。

# **变长结构体**

变长结构体应该越来越不常见了，考虑二八法则和学习效率（和本文阐述的清晰程度），建议可以跳过，收藏，以后再研究。

可以变长的数组

```cpp
#include
#include
#include
typedef struct changeable{
        int iCnt;
        char pc[0];
}schangeable;

main(){
        printf("size of struct changeable : %d\n",sizeof(schangeable));

        schangeable *pchangeable = (schangeable *)malloc(sizeof(schangeable) + 10*sizeof(char));
        printf("size of pchangeable : %d\n",sizeof(pchangeable));

        schangeable *pchangeable2 = (schangeable *)malloc(sizeof(schangeable) + 20*sizeof(char));
        pchangeable2->iCnt = 20;
        printf("pchangeable2->iCnt : %d\n",pchangeable2->iCnt);
        strncpy(pchangeable2->pc,"hello world",11);
        printf("%s\n",pchangeable2->pc);
        printf("size of pchangeable2 : %d\n",sizeof(pchangeable2));
}
```

运行结果

```cpp
size of struct changeable : 4
size of pchangeable : 4
pchangeable2->iCnt : 20
hello world
size of pchangeable2 : 4
```

如上，本例中 **变长结构体**本身长度就是一个int的长度（这个int值通常只为了方便表示后边的数组长度），而后边的数组长度不计算在内，但是该数组可以直接使用。

（说后边是个指针吧？指针也占长度！这个是不占的！原理很简单，这个东西完全是数组后边的尾巴，malloc开辟的是一片连续空间。 **其实这不应该算一个机制，感觉应该更像一个技巧吧**）

非弹性数组不能用"char a[]"这种形式定义弹性（flexible）变量，必须明确大小。

弹性数组在结构体中，下面的形式是唯一允许的：

```cpp
struct s
{
        int a;
        char b[] ;
};
```

顺序颠倒会让b和a数据重合，会在编译时不通过。

char b[] = "hell";也不行（C和C++都不行）

少了整型变量a又会让整个结构体长度为0，compiler不允许编译通过！不同的是，其实C++形式上是允许空结构体的，本质上是通过机制避免了纯空结构体和类对象，自动给空结构体对象分配一个字节（sizeof（）返回1）方便区分对象，避免地址重合！所以呢，C如果有空结构体，定义两个（或一打，或干脆一个数组）该结构体的变量（对象），地址是完全一样的

```cpp
struct s2
{
//      char a[]  = "hasd" ;
//      int c;
};
int main()
{
        struct s2 s22;
        struct s2 s23;
        struct s2 s24;
        struct s2 s25;
}
```


例外的是，C++唯独不给带弹性数组的结构体分配空间（可能怕和变长结构体机制产生某种冲突，比如大小怎么算）:

```cpp
struct s
{
        char b[] ;
};
```

```cpp
struct s
{
//        char b[] ;
};
```

C++中两者是不一样的，空的结构体反而"大"（sizeof()返回1）

结构体的简单初始化方法：

直接用花括号初始化，类似构造函数，auto res为自动类型接受s1返回结果。

```cpp
#include
struct s1{
    int i1;
    double d1;
    bool b1;
};

int main (){

    auto res = s1{4,2.2,true};
    std::cout << res.d1<
```

