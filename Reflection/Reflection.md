# Java SE Reflection

## Definition


>JAVA反射机制是在运行状态中，对于任意一个实体类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性；这种动态获取信息以及动态调用对象方法的功能称为java语言的反射机制。


Oracle官网中对Reflection的描述是这样的：

    Reflection is a feature in the Java programming language. It allows an executing Java program to examine or "introspect" upon itself, and manipulate internal properties of the program. For example, it's possible for a Java class to obtain the names of all its members and display them.

    The ability to examine and manipulate a Java class from within itself may not sound like very much, but in other programming languages this feature simply doesn't exist. For example, there is no way in a Pascal, C, or C++ program to obtain information about the functions defined within that program.

## Characteristic

- 运行时（并非编译时，而是运行时）

- 动态获取与调用（获取与调用取决于运行时，而非编译决定）

- 有可能会破坏原有的封装（能访问私有属性）

## Sample

      import java.lang.reflect.Method;

      public class InvokeTester
      {
         public int add(int param1, int param2)
         {
            return param1 + param2;
         }

         private String echo(String message)
         {
            return "hello: " + message;
         }

         public static void main(String[] args) throws Exception
         {
            // InvokeTester test = new InvokeTester();
            // System.out.println(test.add(1, 2));
            // System.out.println(test.echo("tom"));

            Class<?> classType = InvokeTester.class;

            Object invokeTester = classType.newInstance();

            // System.out.println(invokeTester instanceof InvokeTester);

            Method addMethod = classType.getMethod("add", new Class[] { int.class,
                  int.class });
            
            Object result = addMethod.invoke(invokeTester, new Object[]{1, 2});
            
            System.out.println((Integer)result);
            
            System.out.println("---------------------");
            
            Method echoMethod = classType.getMethod("echo", new Class[]{String.class});
            
            echoMethod.setAccessible(true);

            Object result2 = echoMethod.invoke(invokeTester, new Object[]{"tom"});
            
            System.out.println((String)result2);

         }
      }


## Keywords

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
