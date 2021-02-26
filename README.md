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


## Installers
There are different types of installers.

### MonoInstaller
The most common is ```MonoInstaller```, like in the example above.

These are like ```MonoBehaviour``` -> They can be attached to GameObjects.

Why is that good?

It means that an installer can appear when a gameobject appears to the scene, for example upon loading a new scene. This has benefits because it means we won't have to run all our installers at the initial load of the game. It also means it will disappear when the scene is unloaded and the gameobjects disappear from the game.

### Scriptable Ojbect Installer
Very much like a ```MonoInstaller```, can drag and drop onto gameobjects into the Scriptable Object Installer fields. The values of the installer can be edited in the object inspector in Unity during runtime, so you can use it to fine tune values - for example damage or hp of enemies, while running and teting the game. 

### Base Installers
Exposed via a static method - NOT SURE WHAT THIS MEANS YET

Scriptalbe Ojbect Installers are stored in Assets in Unity.

MonoInstallers must be attached to a GameObject. Pro tip: attach it to a SceneContext GameObject in your scene, right click in the hierarchy list of your scene and select the Zenject option in the right click menu, this will reveal the SceneContext object. Here you can attach your MonoInstallers.

MonoInstallers can also just be attached to a GameObject and then dragged into an Assets Folder -> creating a PreFab of the GameObject with the MonoInstaller -> thus you have a prefab of the MonoInstaller, allowing it to be reused.

When would you want a prefab MonoInstaller? An example is this: Let's say you have a PlayerModel and you want it to be initiated in every scene. You could use a MonoInstaller to let the Container know what to inject into the model. Then you could use this MonoInstaller as a prefab to use it in every Scene. Furthermore you can change certain values depending on the scenes by adjusting the prefab that you put into the scene. Maybe your player has more health in one scene than another?
