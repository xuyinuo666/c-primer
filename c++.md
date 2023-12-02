# 引用&指针

引用：普通的变量初始化时，将初始值复制一份给变量，而引用是直接和这个值绑定bind的，所以初始化时必须指明对象，而且无法解绑。
2、只能绑定到对象，不能直接绑定值。int &ref = 10
3、它不是对象，没有实际地址

指针：它是一个对象，可以在生命周期内指向多个对象。
2、声明时无需初始化，如果没有初始化，将拥有一个不确定的初始值。
3、不能定义一个指向引用的指针，因为引用没有地址值。





# Const

默认情况下，const对象只在当前文件中生效。

编译时初始化的方式：

```c++
const int a = 10;
```

编译器会在编译阶段将a替换成10



==extern==  解决多个文件中之间的共享变量问题，同一个文件，只需要定义一次，其他文件用的时候加上extern修饰，链接器就会找该变量的定义。该变量并非独有，在其他地方有定义。



之前的引用有一个规则：引用类型和其引用对象类型一致，但是有两种例外：

1. 初始化常量时，可以允许使用任意表达式作为初始值

```c++
int a = 10;
const int &aa = 10;
const int &aaa = a*2;
```

```c++
double dval = 1.1;
const int &a = dval;


```

这种情况是允许的, 过程:

```c++
double dval =1.1;
// 编译器将dval存储到一个临时变量中
const int temp = dval;
const int &temp2  = temp;
// 最终:
const int &a = temp2;
// 因为const修饰了,值不能被改变
// double dval = 1.1;
// int &a = dval; 不允许,因为引用的是中间临时变量,当改变引用a时,并不影响dval;
```

## const指针

const修饰的指针,不能用于修改指向对象的值





constexpr指针必须初始化为nullptr\0,或者有固定的地址,不能在函数体内



## auto

使用auto会抵消顶层const

```c++
auto &a= 34;// no
const auto &aa= 34;//yes
```



## decltype

不会抵消顶层const,

==表达式是解引用表达式时,将得到引用类型==

```c++
int i=1 , *p = &i, &r  = i;
decltype(r+0) b; // pass加法结果是int
decltype(*p) c;// 错误,*p是引用类型
```

表达式的如果多了一层括号,得到的也是引用类型

```c++
decltype((i)) d;// 错误,d是引用类型,必须初始化
decltype(i) dd; //pass
```



# string



```c++
string a = "123"+"234";// no 加号两边至少有一个string
```



```c++
string a = "123";
string aa = "123"+"123"+a;// no 两个字面量不能直接相加
string bb = a+"123"+"123";// pass
```









# 迭代器

```c++
vector<int> a;
const vector<int> aa;
vector<int>::iterator i1 = a.begin();
vector<int>::const_iterator = aa.begin();
```

# 数组



不允许auto推断数组类型.

和vector一样,不能存储引用类型对象

不能复制和直接赋值

```c
int a[] = {1,2};
int a2[] = a; // no
a2 = a; //no
```





```c
int i[]={1,2,3};
auto ia(i); // pass 此时ia是一个指向i[0]的指针,等值于 auto ia(&i[0])


```





`decltype`不会发生上述转变

```c
int a= {1,2};
decltype(a) a2 = {1,2,3};// a2仍然是一个数组

```





```c
int *p = &a[2];// 指向a[2]的指针
int j = p[1];// 等价于 *(p+1) ,a[3]的值

```





如果想要c++的``string`转换为c风格字符串:

```c
const char *cString = "a".c_str();
```

==如果"a"的值改变了,cString也会改变==







decltype`







> 顶层const\顶层const

指针:顶层指向不能改变 底层不能通过指针修改

对象:都是顶层,值不能改变







# 强制转换



> const_cast

只能去掉const,不参与类型转换,需要static_cast再次类型转换



















# 参数传递



```c
void change_ptr(int *p){ 
    // 2,改变指向对象的值,可以改变成功
    *p=9;
    // 3,指针置为0,只是拷贝的指针,不影响原指针
    p=0;
    
}

int a = 999;
change_prt(&a); // 1,这里传递的是&a指针的复制值,可以看做临时指针,并不是原指针,两个指向同一个对象
```



传参时,形参const会被忽略,实参是不是const都可以调用

```c
void func1(const int i){};
const int a =1;
int b =2;
func1(a);// pass
func1(b);// pass
void func1(int i){}; //error ,重复定义

```



## 数组传参

> 数组的特性:1.不能拷贝  2.直接使用数组名时,代表第一个元素的指针,由于1的存在,所以不存在值传递

```c
void print(const int *a);
void print(const int []a);
void print(const int [10]);// 10是期望元素个数,实际并不一定
```

这3种都可以实现数组传递



对于第三个,传递数组的引用不适用:

```c
void f(int (&arr)[10]){
    //-----调用该函数,数组必须是10个元素
}

```



ps:

```c
f(int &arr[10]);//error,将arr声明为引用的数组
f(int (&arr)[10]);//pass,10个整形数组的引用
```

## initializer_list



```c
initializer_list<int> a{1,2};
initializer_list<int> b(a); // a和b共用元素


```

initializer_list内的元素永远是常量



==引用是一个左值==

```

```



==返回数组指针????????????todo== pirmer page205







# 重载







```c
void func(const int a);
void func(int a); // error
```





如果形参是指针或者引用,可以通过形参指向的是常量对象还是非常量对象重载

```c
void func(const int * a);
void func(int *a); //pass

void func(const int & a);
void func(int &a); //pass
```

常量和非常量的函数重载后,实参如果都满足,优先调用非常量版本

```c
void fuc(const int a);
void fuc(int a);

fuc(1);// 调用void fuc(int a)
```





在内层作用域中声明名字,会隐藏外层作用域的同名实体;

```c
string read();
void print(const string&);
void print(int a);

void test(){
    bool read = false; // string read()被隐藏
    string s=read();// error read是布尔类型
    
    void print(double a);// void print(const string&)\
    // void print(int a)都会失效
    print(1);// error
}
```







## 参数默认值

==形参给出默认值后,该参数后面所有参数必须都有默认值.可以放到最后面==



通常将默认值写在函数声明中,函数声明可以被声明多次,但是赋予默认值只能赋值一次

```c
void func(string a, int b,char c);
void func(string a, int b,char c ='a');
void func(string a, int b = 2,char c ='b');//error,不能修改已经存在的默认值
void func(string a, int b = 2,char c); // pass
```





## 

`assert`预处理器处理,不是由编译器处理



## 重载二义性



```c
void f1(int a,int b);
void f1(double a,double b);

int a =1;
double b = 1.1;
f1(a,b);// error,没有最优的函数,二义性
```

可以使用强制类型转换,但是最好不要用





```c
void f1(const int&);
void f1(int&);
int a;
const int b;

f1(a); // 优先调用非常量版本
f1(b); 
```









## 函数指针

```c
bool f1( int&, int&);
bool f1( double&, double&);

//获取函数指针
bool (*f_ptr1) ( int, int)= f1;
bool (*f_ptr2) ( int, int)= f1;
```

> 函数指针的形参

和数组一样,形参传入一个数组时,自动将数组转换为指针.

```c
// 两个写法等价
void f1(const string &s1,const string &s2,bool pf(const string & ,CONST STRING &));
void f1(const string &s1,const string &s2,bool (*pf)(const string & ,CONST STRING &));
// 调用:
f1(s1,s2,func1);//func1 等于 void func1(string &a,string &b ),前提是要有这个函数

```



==todo==

# 顺序容器



复制时,容器类型必须一样,元素可以转换即可



array可以进行复制和赋值



```c
array<int ,3> a;
array<int ,5> b;

a=b;//a的size变为5,a元素是b元素的拷贝

```

`swap`:交换两个容器内容,不会拷贝,相当于只换了个名字,swap之前的引用\迭代器\指针,在swap之后仍然指向原数据,例如:v1的指针:ptr1,v2的ptr2,swap之后,ptr1指向v2,ptr2指向v1.==但是对于string不同,swap之后,引用指针迭代器会失效,array则是内容会拷贝互换!!==

**操作**

`push_back`和`emplace_back`:push适用于已经存在了一个对象,需要追加到容器中,会发生一次拷贝.而emplace是在容器中直接调用对象的构造函数,重写创建了一个对象.

```c
c.push_back(obj);
c.emplace_back(args);// 直接调用参数的构造函数
```

`vector``string`

`at`和下标操作只有`string` `vector`  `deque` `array`才有



```c
auto &cfront = c.front(); // c第一个值的引用
cfront = 1; //pass,容器第一个值改变

auto cfront = c.front(); // c第一个值的拷贝
cfront = 1; // 容器没有改变,只是改变的临时值cfront
```



 











































































![image-20231202163535967](c++.assets/image-20231202163535967.png)













