# Java SE Static Proxy

## 概念

>代理指的是，通过操作代理类，实现对被代理类的操作，而不是直接操作被代理类。
>静态代理于编译时便能确定代理类与被代理类的关系，所以称为静态代理。

## 图例

![proxy](./Proxy.png)

## Sample

- Subject.class
        
        public abstract class Subject{
            public abstract void request();
        }


- RealSubject.class

        public class RealSubject extends Subject{
            public void request()
            {
                System.out.println("From real subject.");
            }
        }


- ProxySubject.class

        public class ProxySubject extends Subject{

            private RealSubject realSubject; //代理角色内部引用了真实角色
            
            public void request()
            {
                this.preRequest(); //在真实角色操作之前所附加的操作
                
                if(null == realSubject)
                {
                    realSubject = new RealSubject();
                }
                
                realSubject.request(); //真实角色所完成的事情
                
                this.postRequest(); //在真实角色操作之后所附加的操作
            }
            
            private void preRequest()
            {
                System.out.println("pre request");
            }
            
            private void postRequest()
            {
                System.out.println("post request");
            }

        }


- Client.class

        public class Client{
            public static void main(String[] args)
            {
                Subject subject = new ProxySubject();
                subject.request();
            }
        }