title: Java中equals测试
date: 2014-02-11 13:07:22
tags: Java
---

摘自Core Java
Java语言规范要求equals方法具有下面的特性：

* 自反性：对应任何**非空**引用x, x.equals(x)返回true
* 对称性：对应任何引用x和y,当且仅当y.equals(x)返回true, x.equals(y)才应该返回true
* 传递性：对于任何引用x、y、和z，如果x.equals(y)返回true，y.equals(z)返回true， x.equals(z)也应该返回true
* 一致性： 如果x和y引用的对象没有发生变化，返回调用x.equals(y)都一样返回同样的结果
* 对应任何非空x, x.equals(null)应该返回false

比如只用instanceof就会出现问题。Employee.equals(Manager)用instanceof可能会返回true,但是Manager.equals(Employee)会返回false. 不满足对称性。

书中给出了一个完美的equals方法建议：
* 显示参数命名为otherObject, 稍后需要将它转换成另一个叫做other的变量
* 检测this与otherObject是否引用同一个对象.  (优化)
* 检测otherObject是否为null.  (优化)
* 比较this与otherObject是否同属于一个类
	**如果equals语义在每个子类中有所改变，使用getClass检测**
	**如果所有子类都拥有统一的语义，就使用instanceof检测**
* 将otherObject转换成相应的类类型变量
* 最后对所有需要比较的域进行比较，基本域用==,对象域用equals.

**Note**: 如果在子类中重新定义了equals， 就要在其中包含调用super.equals(other)

