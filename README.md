# zenject-info
A repo containing a readme with descriptions of my understanding of zenject for dependency injection in Unity

## What is this repo?
This repo is just a readme file that contains my notes on how Zenject works after having to jump into an existing Unity project that uses Zenject with the MVVM pattern.
For Zenject's own documentation see: https://github.com/modesttree/Zenject

## "Container"
The Container is often called in MonoInstallers or other installer. It is a part of Zenject. It is the "root" of your application, and you use it to create bindings.
Think of the container as the big brain that knows where all dependencies need to go in your project. Whenever Container is called in an installer like this:

```
public class FooInstaller : MonoInstaller
{

  private void FooInstaller()
  {
      Container.Bind<Foo>().AsSingle();
  }

}
```

What is happening is: we are telling the Container that "Hey, big brain, I want you to create an instance of the Foo object, which is my Foo class somewhere in my project. Then I want you to inject that instance in any class that is dependent on it!

The ```.AsSingle()``` means that it is made as a singleton (a design pattern). 


## Container and bindings
We already looked at the container, and that it can have bindings.
One important thing that was not mentioned is when your class in the binding has dependencies.
Let's look at an example: ```Container.Bind<Foo>().AsSingle();``` -> in this case we are talking about the class 'Foo', it has some dependency.

The FooClass:
```
public class Foo
{
    private FooDependencyClass _fooDepedency;

    public Foo(FooDependencyClass fooDependency)
        {
            _fooDependency = fooDependency;
        }
}
```

In this case the class Foo is dependent on the FooDependencyClass.
What happens when the Container - the big brain root of our entire project - tries to create the instance of Foo?
Well it does not know how to create the depedency of Foo, the FooDepedency. 

The solution is simple: we need to bind the FooDepdendencyClass as well in the installer.

Let's update the installer.

```
public class FooInstaller : MonoInstaller
{

  private void FooInstaller()
  {
      Container.Bind<Foo>().AsSingle();
      Container.Bind<FooDependencyClass>().AsSingle();
  }

}
```

There we go, now Zenject can create both.
