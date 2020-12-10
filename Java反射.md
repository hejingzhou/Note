[toc]

#### 目标任意类

##### 1、父类

```java
package com.study.reflection;

public class Person {
    private String name;
    private String gender;

    public Person(String name, String gender) {
        this.name = name;
        this.gender = gender;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }
}
```

##### 2、子类

```java
package com.study.reflection;

public class User extends Person {
    private String school;
    private boolean isVip;

    private User(String name, String gender, String school, boolean isVip) {
        super(name, gender);
        this.school = school;
        this.isVip = isVip;
    }

    public String getSchool() {
        return school;
    }

    public void setSchool(String school) {
        this.school = school;
    }

    public boolean isVip() {
        return isVip;
    }

    public void setVip(boolean vip) {
        isVip = vip;
    }
}
```

#### 获取Class的几种方式

##### 1、通过类型进行获取

```java
Class c = boolean.class;  
Class c = String.class;
```

##### 2、通过类的全路径获取

```java
Class userClass = Class.forName("com.study.reflection.User");
```

##### 3、特殊数组

```java
Class cDoubleArray = Class.forName("[D");    //相当于double[].class
Class cStringArray = Class.forName("[[Ljava.lang.String;");   //相当于String[][].class
```

#### 使用注意点

```java
userClass.getDeclaredFields()
userClass.getFields()
userClass.getDeclaredConstructors()
userClass.getConstructors()
userClass.getDeclaredMethods()
userClass.getMethods()
```

类似这样的区别是`getDeclared···`是只拿本类的一些属性、构造、方法不包含父类的，反之则包含父类

#### 需求一：如果这个类的构造方法为私有方法，利用构造方法创建对象实例

```java
public static void main(String[] args) {
    try {
        Class userClass = Class.forName("com.study.reflection.User");
        Constructor declaredConstructor = userClass.getDeclaredConstructor(String.class, String.class, String.class, boolean.class);
        if (!declaredConstructor.isAccessible()) {
            System.out.println("这个构造不能访问是私有函数，进行设置允许访问");
            declaredConstructor.setAccessible(true);
        }
        User userObject = (User) declaredConstructor.newInstance("贺景洲", "男", "淄博学院", true);
        System.out.println(userObject.getName() + "\t" + userObject.getGender() + "\t" + userObject.getSchool() + "\t" + userObject.isVip());
    } catch (ClassNotFoundException | NoSuchMethodException | IllegalAccessException | InstantiationException | InvocationTargetException e) {
        e.printStackTrace();
    }
}
```

#### 需求二：调用一个类的私有方法

```java
//以下是调取私有方法的方式
Method say = userClass.getDeclaredMethod("say", String.class);
if (!say.isAccessible()) {
    say.setAccessible(true);
}
say.invoke(userObject,"哈哈哈哈");
```

#### 需求三：获取私有变量值

```java
Field[] fields = userClass.getDeclaredFields();
for (Field field : fields) {
    if (!field.isAccessible()) {
        field.setAccessible(true);
    }
    System.out.println(field.getName()+"---->"+field.get(userObject));
}
```

#### 需求四：修改私有变量值

```java
//修改属性值
Field schoolField = userClass.getDeclaredField("school");
if (!schoolField.isAccessible()) {
    schoolField.setAccessible(true);
}
schoolField.set(userObject,"国防大学");
```