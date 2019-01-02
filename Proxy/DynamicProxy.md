# Java SE Dynamic Proxy

## Definition

代理指的是，通过操作代理类，实现对被代理类的才做。而不是直接操作被代理类。和静态代理类似的是，动态代理也实现了同样的功能；和静态代理不同的是，静态代理于编译时便能确定关系，动态代理的发生在运行时，动态确定代理类与被代理类的关系。

oracle官网的解释如下：

>A dynamic proxy class is a class that implements a list of interfaces specified at runtime such that a method invocation through one of the interfaces on an instance of the class will be encoded and dispatched to another object through a uniform interface. Thus, a dynamic proxy class can be used to create a type-safe proxy object for a list of interfaces without requiring pre-generation of the proxy class, such as with compile-time tools. Method invocations on an instance of a dynamic proxy class are dispatched to a single method in the instance's invocation handler, and they are encoded with a java.lang.reflect.Method object identifying the method that was invoked and an array of type Object containing the arguments.

## Sample

    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Method;
    import java.lang.reflect.Proxy;
    import java.util.List;
    import java.util.Vector;

    public class VectorProxy implements InvocationHandler
    {
        private Object proxyObj;

        public VectorProxy(Object obj)
        {
            this.proxyObj = obj;
        }

        public static Object factory(Object obj)
        {
            Class<?> classType = obj.getClass();

            return Proxy.newProxyInstance(classType.getClassLoader(),
                    classType.getInterfaces(), new VectorProxy(obj));
        }
        
        public Object invoke(Object proxy, Method method, Object[] args)
                throws Throwable
        {
            System.out.println("before calling: " + method);
            
            if(null != args)
            {
                for(Object obj : args)
                {
                    System.out.println(obj);
                }
            }
            
            Object object = method.invoke(proxyObj, args);
            
            System.out.println("after calling: " + method);
            
            return object;
        }
        
        public static void main(String[] args)
        {
            List v = (List)factory(new Vector());
            
            System.out.println(v.getClass().getName());
            
            v.add("New");
            v.add("York");
            
            System.out.println(v);
            
            v.remove(0);
            System.out.println(v);
            
        }
        
    }

## Keywords

- ```java.lang.reflect.InvocationHandler```

- ```java.lang.reflect.Proxy```

- ```newProxyInstance()```

- etc...
