> 作者：回忆酿的甜
  链接：https://www.cnblogs.com/ygsworld/p/10333366.html

  > 生存期，即从诞生到消失的时间段，在生存期内，对象的值或保持不变，直到改变它的值为止。对象生存期分为静态生存期和动态生存期两种

- 静态生存期
- - 指对象的生存期与程序运行期相同。在namespace中声明的对象都具有静态生存期。但是，在函数内部作用域中申明具有动态生存期的对象，要用static修饰，如static int a
- - 在局部作用域中，静态变量的特点是不会随函数的每次调用而产生一个副本，同时也不会因为函数的返回而消失。该变量会一直保持上一个值，在每次调用中可共享。同时静态变量在定义时也可以赋值，如static int a = 3

- 动态生存期
- - 除上述两种情况，其余为动态生存期。在局部作用域中，有动态生存期的对象，也称为局部生存期对象。局部生存期对象诞生于声明点，结束于声明快执行完毕。在类中不用static修饰的成员，它们的生存期和对象生存期一致

```
#include<iostream>
using namespace std;

int i = 1; //i 全局变量,动态生存期
void Other() 
{
	static int a = 2;
	static int b;
	//a,b 为静态局部变量，全局寿命，局部可见，第一次进入函数时初始化
	int c = 10;
	//c 局部变量，动态生存期，每次进入函数都初始化
	a += 2;
	i += 32;
	c += 5;
	cout << "Other:" << endl;
	cout << "i: " << i << " a:" << a << " b: " << b << " c:" << c << endl;
}
int main()
{
	static int a;//静态局部变量，全局寿命，局部可见
	int b = -1;
	int c = 0;//b,c 局部变量，动态生存期
	cout << "Main：" << endl;
	cout << "i: " << i << " a: " << a << " b: " << b << " c: " << c << endl;
	c += 8;
	Other();
	cout << "Main:" << endl;
	cout << "i: " << i << " a: " << a << " b: " << b << " c: " << c << endl;
	i += 10;
	Other();
	return 0;
}
```
执行结果
``` 
Main：
i: 1 a: 0 b: -1 c: 0
Other:
i: 33 a:4 b: 0 c:15
Main:
i: 33 a: 0 b: -1 c: 8
Other:
i: 75 a:6 b: 0 c:15
```
> 类的静态成员

- 类的静态成员是解决同一个类的不同对象之间数据和函数的共享问题。如每生成一个对象，则计数+1，从而统计一共创建了多少个对象
- 静态数据成员
- - 在类的每一个属性中，如果某个属性为整个类所共享，则用static修饰为静态成员。该成员在每个类中只有一个副本，由所有对象共同维护，从而实现数据共享
- - 静态数据成员具有静态生存期，因为静态数据成员不属于任何对象，可以这访问"类名::成员名 | 对象名.成员名 | 指针->成员名 | 引用.成员名"。另外，在类定义中，仅进行引用性声明，而在namespace作用域中使用类名限定定义性声明，或初始化。(原因：这样定义是因为这种方式要专门分配空间，非静态数据成员的空间与所属对象空间是同时分配的，所有无须这样定义)

```
#include<iostream>
using namespace std;
class Point
{
public:
	Point(int x = 0, int y = 0) :x(x), y(y)
	{
		count++;
	}
	Point(Point& p)//copy
	{
		x = p.x;
		y = p.y;
		count++;
	}
	~Point()
	{
		count--;
	}
	int GetX() { return x; }
	int GetY() { return y; }
	void ShowCount()//静态函数
	{
		cout << "Object count :" << count << endl;
	}
private:
	static int  count;//static
	int x;
	int y;
};

int Point::count = 0;//静态数据成员的初始化，类名限定

int main()
{
	Point a(4, 5);
	cout << "Pont a:" << "(" << a.GetX() << "," << a.GetY() << ")" << endl;
	a.ShowCount();//通过对象名
	Point b(a);
	cout << "Pont b:" << "(" << b.GetX() << "," << b.GetY() << ")" << endl;
	b.ShowCount();
	return 0;
}
```

结果:
```
Pont a:(4,5)
Object count :1
Pont b:(4,5)
Object count :2
```