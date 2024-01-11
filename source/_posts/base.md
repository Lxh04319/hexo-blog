---
title: base
date: 2024-1-10
tags:
---

## 基础语法

updating...
是的，如你所见，正在从后往前补，还没补到 :-(

## 面向对象

updating....
是的，如你所见，正在从后往前补，还没补到 :-(

## API

## 常用API(一)

updating...
是的，如你所见，正在从后往前补，还没补到 :-(

## 常用API(二)

### Objects类

#### base

所有类的对象都可以用，默认继承

```java
public String toString(){//默认输出地址,主要用于子类重载方法，返回具体内容
     return ...;
}
public boolean equals(Object o){//默认比较地址（==），重载比较内容
     return ...;//1.比较地址2.null||比较类型 getClass()3.比较成员
}
public Object clone(){//需要标记接口 implements Cloneable
     return super.clone();//子类重载，抛出异常
}
```

#### 浅克隆/深克隆

浅拷贝：堆内存中拷贝地址
深拷贝：数据直接拷贝，字符串拷贝地址（字符串常量池），其他对象则创建新对象-----重载

#### 一些静态方法

```java
Objects.equals(s1,s2);//相比字符串的equals方法（注意空指针异常），先进行非空判断
Objects.isNull(s1);//s1==null
Objects.nonNull(s1);
```

---

### 包装类

把基本数据类型包装成对象
![Alt text](image-1.png)

#### 一些静态方法(用于泛型和集合（引用数据类型）)

```java
//应用于泛型和集合
ArrayList<Integer> list=new ArrayList();

//转换成字符串
String s1=Integer.toString(a);
String s1=a.toString();//继承Objects类toString()方法
String s1=a+"";

//字符串类型的数值转换成对应
int a=Integer.parseInt(s1);//注意转换类型
int a=Integer.valueOf(s1);

```

---

### StringBuilder

容器，方便修改字符串

```java
StringBuilder s=new StringBuilder("111");//直接返回内容
//拼接 支持链式编程
s.append(1);
s.append(1).append(2);
//反转
s.reverse();
//长度
s.length();
//转换成String类型
String s1=s.toString();
```

相比String，频繁拼接修改等效率更高

#### StringBuffer

与StringBuilder用法一致

StringBuilder  线程不安全
StringBuffer   线程安全

#### StringJoiner

高效简洁

```java
StringJoiner s1=new StringJoiner(",","[","]");
s1.add(1+"");
String s=s1.toString();
```

---

### Math

#### 常用静态方法

```java
public static int abs(int a) //绝对值 Math.abs(1.2)
public static double ceil(double a) //向上取整 Math.ceil(1.2)
public static double floor(double a) //向下取整
public static long round(double a) //四舍五入
public static int max(int a,int b) //最大值
public static double pow(double a,double b) //a的b次方
public static double random(); //随机数[0.0,1.0)
```

---

### System

#### 静态方法

```java
public static void exit(int status)//终止Java虚拟机
public static long currentTimeMillis()//当前系统时间ms
```

---

### Runtime

单例类

#### 常用方法

```java
Runtime r=Runtime.getRuntime();
r.availableProcessors();//虚拟机使用的处理器数
r.totalMemory();//虚拟机内存总量--字节
r.freeMemory();//虚拟机可用内存量
Process p=r.exec("路径“);//启动
```

---

### BigDecimal

解决浮点运算失真

```java
//小数转字符串转BigDecimal
BigDecimal a1=new BigDecimal.valueOf(a);
BigDecimal b1=new BigDecimal.valueOf(b);
BigDecimal c1=a1.add(b1);//subtract multiply divide(可精确位数)
```

---

### 时间日期

#### 传统(非重点)

##### Date

获取时间/日期

```java
Date d=new Date();
long time=d.getTime();//ms
d.setTime(time+=2*1000);
```

##### SimpleDateFormat

日期格式化

```java
//时间/日期格式化
Date d=new Date();
long time=d.getTime();//ms
SimpleDateFormat sdf=new SimpleDateFormat("yyyy年MM月dd日 HH:mm:ss EEE a");//格式化
String rs=sdf.format(d);//日期转换成字符串
String rs1=sdf.format(time);//时间转换成字符串

//解析
String a="2024-1-10 20:07";
SimpleDateFormat sdf1=new SimpleDateFormat("yyyy-MM-dd HH:mm");//格式
Date d1=sdf1.parse(a);//解析 抛出异常
```

##### Calender

当前日历,主要用于修改日期时间
***可变对象

```java
Calendar c=Calendar.getInstance();
int days=c.get(Calendar.DAY_OF_YEAR);
Date d2=c.getTime();//获取
long time1=c.getTimeInMillis();
c.set(Calendar.YEAR,2025);//修改
c.add(Calendar.YEAR,1);//增加
```

#### JDK8新增(主要)

##### LocalDate LocalTime LocalDateTime 代替Calendar

均为不可变对象，三个用法几乎相同

```java

LocalDate ld=LocalDate.now();
int year=ld.getYear();//获取
LocalDate ld1=ld.withYear(2025);//修改
LocalDate ld2=ld.plusYears(1);//minus
LocalDate ld3=LocalDate.of(2025,1,1);
ld1.equals(ld2);

//转换
LocalDateTime ld1=LocalDateTime.now();
LocalDate ld2=ld1.toLocalDate();
LocalTime ld3=ld1.toLocalTime();
```

##### ZoneId ZoneDateTime 代替Calendar

##### Instant

##### DateTimeFormatter

##### Duration Period

### Arrays

### JDK8新特性

#### Lambda表达式

#### 方法引用

## 集合框架

## IO流
