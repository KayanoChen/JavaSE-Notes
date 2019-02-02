# Java SE Reflection

## 概念


>JAVA反射机制是在运行状态中，对于任意一个实体类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性；这种动态获取信息以及动态调用对象方法的功能称为java语言的反射机制。


Oracle官网中对Reflection的描述是这样的：

>Reflection is a feature in the Java programming language. It allows an executing Java program to examine or "introspect" upon itself, and manipulate internal properties of the program. For example, it's possible for a Java class to obtain the names of all its members and display them.
>The ability to examine and manipulate a Java class from within itself may not sound like very much, but in other programming languages this feature simply doesn't exist. For example, there is no way in a Pascal, C, or C++ program to obtain information about the functions defined within that program.

## 特点

- 运行时（并非编译时，而是运行时）

- 动态获取与调用（获取与调用取决于运行时，而非编译决定）

- 有可能会破坏原有的封装（能访问私有属性）

## 知识点

 - 对象的Class对象可以通过.class/.getClass/Class.forName三种方式获取

         package com.github.kayano.test
         ...
         Class<?> classType = Test.class;
         Class<?> classType = new Test().getClass()
         Class<?> classType = Class.forName("com.github.kayano.test.Test")

 - 创建实例，newInstance仅用于构造方法可访问且无参数

         Class<?> classType = Test.class;
         Object test = classType.newInstance();

 - 创建实例，构造方法带参数或构造方法为私有时，不能直接newInstance创建实例，需通过获取构造方法来创建实例

         public class Test {
            
            private Test(){
               
            }
            public Test(String msg){
               System.out.println(msg);
            }
            
            public static void main(String[] args) throws Exception
            {
               Class<?> classType = Test.class;
               Constructor constructor = classType.getDeclaredConstructor();
               Object test = constructor.newInstance();
               System.out.println(test.getClass());
               
               Constructor constructor1 = classType.getDeclaredConstructor(String.class);
               Object test1 = constructor1.newInstance("msg");
               System.out.println(test1.getClass());
               
            }
         }


 - 获取属性（Field）、方法(Method)、构造方法(Construtor)等，以及调用方法invoke，获取方法返回值等

         import java.lang.reflect.Field;
         import java.lang.reflect.Method;

         public class Test {

            public Test(){

            }
            private Test(String msg){
               System.out.println(msg);
            }

            private int a = 0;
            public String b;
            
            //定义方法add
            public Integer add(int param1, int param2)
            {
               return param1 + param2;
            }
            //定义方法echo
            private String echo(String message)
            {
               return "hello: " + message;
            }

            public static void main(String[] args) throws Exception
            {
               //获取classType，途径有Class.forName/.getClass/.class三种方法
               Class<?> classType = Test.class;
               //创建实例instance，名为test
               Object test = classType.newInstance();
               //getFields、getField可获得public的属性，包括继承而来的以及自己声明的
               Field[] fields = classType.getFields();
               for (Field field : fields) {
                     System.out.println(field.getName());
                     //通过get方法，获取test对象对应的属性的值
                     System.out.println(field.get(test));
               }
               //getDeclaredFields、getDeclaredField可获得自己声明的方法，无论是什么修饰符
               fields = classType.getDeclaredFields();
               for (Field field : fields) {
                     System.out.println(field.getName());
                     //通过get方法，获取test对象对应的属性的值
                     System.out.println(field.get(test));
               }

               //getMethods、getMethod可获得public的方法，包括继承而来的以及自己声明的
               Method[] methods = classType.getMethods();
               for (Method method : methods) {
                     System.out.println(method.getName());
               }

               //getDeclaredMethods、getDeclaredMethod可获得自己声明的方法，无论是什么修饰符
               methods = classType.getDeclaredMethods();
               for (Method method : methods) {
                     System.out.println(method.getName());
               }
               //同理，Annotation、Constructor等也可像Field、Method一样获取

               //通过方法名、参数列表获取Method对象
               //此处方法名为add，参数列表为int.class, int.class
               Method addMethod = classType.getMethod("add", new Class[] { int.class, int.class });
               //调用Method的invoke方法，执行传入的对象的对应的方法，返回值为Object类型对象，若原方法无返回值（void），Object类型的对象为null
               //此处执行对象invokeTester中对应addMethod的方法，即执行add方法
               Object result = addMethod.invoke(test, new Object[]{1, 2});
               //通过getReturnType获取返回值的classType
               Class<?> returnType = addMethod.getReturnType();
               //通过Class的cast方法进行类型转换，转换成为Class对应的类型的对象
               //此处将invoke方法返回的Object类型的对象转换成为add方法原本的返回值类型（Integer）
               System.out.println(returnType.cast(result));
            }

         }




 - 访问私有属性、方法、构造方法等。Field、Method、Constructor直接或间接继承自类AccessibleObject，该类的官方解释为：The AccessibleObject class is the base class for Field, Method, and Constructor objects (known as reflected objects)，AccessibleObject的accessible属性可判断与控制Field、Method、Constructor等是否可被外部访问。

         import java.lang.reflect.Method;

         public class Test {

            //定义方法echo
            private String echo(String message)
            {
               return "hello: " + message;
            }

            public static void main(String[] args) throws Exception
            {
               //获取classType
               Class<?> classType = Test.class;
               //创建实例instance，名为test
               Object test = classType.newInstance();
               Method echoMethod = classType.getDeclaredMethod("echo", new Class[]{String.class});
               //accessible属性可控制Method是否可被外部访问
               //由于echo方法为private，外部无法访问echo方法，所以需修改Method的accessible熟悉为true，使得外部可访问
               echoMethod.setAccessible(true);
               Object result = echoMethod.invoke(test, new Object[]{"tom"});
               Class<?> returnType = echoMethod.getReturnType();
               //通过Class的cast方法进行类型转换，转换成为Class对应的类型的对象
               //此处将invoke方法返回的Object类型的对象转换成为echo方法原本的返回值类型（String）
               System.out.println(returnType.cast(result));
               
            }
         }


## 关键字

- ```getClass()```

- ```Class.forName()```

- ```.class```

- ```Method```

- ```Field```

- ```Constructor```

- ```Array```

- ```invoke()```

- ```getSuperclass()```

- ```newInstance()```

- ```AccessibleObject```

- ```isAccessible()```

- ```setAccesible()```

- etc...
