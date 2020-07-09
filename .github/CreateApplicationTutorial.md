# Example Application:
### Main Creation   
First you need create some header file with basic constructor and destructor. Let's it be `ExampleApp.h` to hold basic information and instance's of you're application. Then in your header include `CTXEngine.h`, to connect all engine API's.
If your don't include `CTXEngine.h`, your still can run application, because engine take care about it. But in future its will be warning.
```cpp
#pragma once 
#include "CTXEngine.h"

class ExampleApp : public СTXEngine::Core::CoreApp
{
  public:
    ExampleApp();
    ~ExampleApp() = default;
};
```
As you may have noticed, our `ExampleApp` class must inherit from the main engine `CoreApp` class for applications. You must inherit from it because it is the entry point for your application. We'll talk about it later.   
If you want, you can also include the namespace `СTXEngine::Core` to not write something like `CTXEngine::Core::CoreApp`. This is not necessary just for the convenience of the programmer. 
   
Here all list of namespaces/folders of CTXEngine.
   
```cpp
using namespace CTXEngine;
using namespace CTXEngine::Core;
using namespace CTXEngine::Core::IO;
using namespace CTXEngine::Graphics;
using namespace CTXEngine::Graphics::GL;
using namespace CTXEngine::Graphics::VK;
using namespace CTXEngine::Graphics::D3D;
using namespace CTXEngine::Audio;
using namespace CTXEngine::Windows;
using namespace CTXEngine::Maths;
using namespace CTXEngine::Editor;
```
   
Let's start with `CTXEngine` namespace. This is the most important folder where the main tools, includs, and settings are stored. `CTXEngine::Core` is designed for the engine core. it stores all the most important things for the engine and your application. So it contains `::IO` namespace where events, input, and output are stored. In `CTXEngine::Graphics` as you have already understood everything is stored related to graphics, graphics engines, and so on. Inside it there are namespaces `::GL`, `::VK`, `::D3D` that display each API. You don't need to use them this is only for the engine developers side. Next comes the `CTXEngine::Audio` designed for audio and store the Audio Engine. What you will need to use quite often so it `CTXEngine::Math`. This namespace stores vectors, matrices, and all that crap about math and computing. `CTXEngine::Windows` stores Windows for different platforms. You won't need to use it, either.
   
For now let's just connect few important namespaces.

```cpp
using namespace CTXEngine;
using namespace CTXEngine::Core;
using namespace CTXEngine::Graphics;
using namespace CTXEngine::*;
```
And in principle, the header file is ready. Now we can create an implementing file `ExampleApp.cpp` where you need to include three main files.
```cpp
#include "CTXEnginePreCompile.h"
#include "ExampleApp.h" 
#include "CTXEngine/Main.cpp"
```
`CTXEnginePreCompile.h` is a engine pre complie header, its contains all c++ standard libraries. It **must** be included in the file, otherwise you will get the ```fatal error C1083: Cannot open precompiled header file: CTXEnginePreCompile.h```.   
`Main.cpp` is a main file of engine. Without it, you won't be able to launch the app. **Note:** this file need's to include **once** per application project.
   
The files must be included in the order shown above. Because the compiler checks the file first when compiling. Therefore, the application files and `Main.h` entry points are already available. 
   
Before implementing the constructor and other functions created in the `ExampleApp.h` you need to add the most important function to create and run the ExampleApp application. We go down to the bottom `ExampleApp.cpp` file and add the `CTXEngine::runCore()` function that will return `CTXEngine::Core::CoreApp*`.
```cpp
...

CTXEngine::Core::CoreApp* CTXEngine::createApp() noexcept
{
	return new ExampleApp();
}

```
It **must** be at the **bottom** of this file, otherwise you will get an error something like this 
```cpp
unresolved external symbol "class CTXEngine::Core::CoreApp * __cdecl CTXEngine::runCore(void)" referenced in function main Application  C:\CTXEngine\Application\ExampleApp.obj
````
It will be easier in the future, but for now, this is it.
   
Now we can declare our constructor, destructor and application functions.

```cpp

...

ExampleApp::ExampleApp()
	: CoreApp()
{
   //do some stuff, for example setup static instance/reference. see below.
}

...

```
And that's it! Of the base and main things, we have done everything. Now you can run your own application written in CTXEngine. And you see the application window and moveable panel with information about your device.   
![Application Window](/Resources/.github/tut1.png?raw=true "Application Window")
But when your try to move this panel in window then you will see something similar to this   
![Application Window Glitch](/Resources/.github/tut2.png?raw=true "Application Window Glitch")
This is because we clear the screen when trying to draw a new frame. To access the engine loop, we need to configure the scene. But first, let's create our own configuration class.

### Custom configurations
When you first configure and run the engine you will have by default are the default settings of window and so on. If you want to add or change your app's settings you will need to use function 
```cpp
void setParam(uint param, uint value)`
```  
where `param` is input parameter, always starts with `CTX_PARAM_` and `value` is value of that parameter. Examples `1`, `0`, `true`, `false`, or `string`. Or if you want to get the parameter from engine, not always configuration use
```cpp
void* getParam(uint param)`
```
where `param` is input parameter, always starts with `CTX_PARAM_`. As you might have noticed, this function is `void*`, i.e. the memory block. So when you use it, don't forget to cast it to the type you need. This is usually an `uint` or `String`. Otherwise you will get the result as a memory block.  
Let's back to our `ExampleApp.cpp` file and in constructor set few configuration parameters.

```cpp
...
ExampleApp::ExampleApp()
	: CoreApp()
{
	this->setParam(CTX_PARAM_APP_NAME, "Example App");
	this->setParam(CTX_PARAM_WINDOW_WIDTH, 1200);
	this->setParam(CTX_PARAM_WINDOW_HEIGHT, 720);
	this->setParam(CTX_PARAM_WINDOW_FULLSCREEN, false);
	this->setParam(CTX_PARAM_WINDOW_RESIZE, false);
	
	...
}
...

```
Here we set the name of our app, the screen resolution and said that our app will not be in fullscreen and will not change the size when we try to resize it.
This is full list of available parameters
```cpp
CTX_PARAM_APP_NAME          
CTX_PARAM_APP_DESC          
CTX_PARAM_APP_AUTOR         
CTX_PARAM_APP_VERSION       
CTX_PARAM_APP_VERSION_MAJOR 
CTX_PARAM_APP_VERSION_MINOR 
CTX_PARAM_WINDOW_WIDTH      
CTX_PARAM_WINDOW_HEIGHT     
CTX_PARAM_WINDOW_MIN_WIDTH  
CTX_PARAM_WINDOW_MIN_HEIGHT 
CTX_PARAM_WINDOW_RATIO      
CTX_PARAM_WINDOW_RESIZE     
CTX_PARAM_WINDOW_FULLSCREEN 
CTX_PARAM_WINDOW_VSYNC      
CTX_PARAM_WINDOW_ICON      
CTX_PARAM_WINDOW_ICON_SM    
```
...

### Application static instance
If you need to statically access your `ExampleApp` application and not the engine `CoreApp` class, you will need your own static instance to do this. This is done very simply.
First in the ExampleApp.h we need to create a privte instance.
```cpp
...

private:
  static ExampleApp* instance;

...
```
And after this just create a public static funtion to get access to your instance.
```cpp
...

public:
  static ExampleApp& ExampleApp::getInstance();

...
```
Now going to ExampleApp.cpp file and declare our function.
```cpp
...

ExampleApp& ExampleApp::getInstance()
{
  return *instance;
}

...
```
And finally we need to initialize our instance in the `ExampleApp` constructor.
```cpp
...

ExampleApp::ExampleApp()
	: CoreApp()
{
  this->instance = this;
}

...
```
### Scenes / Scripting 
In develepment...

While I'm working on the engine itself and I don't have time yet to create the Scene-GameObject-Component system in general scripting, I created a small API for scenes. General, scenes are very useful things, because they allow you to divide your app into several parts / assets, and not download everything at once. And to do this, you can use the `Scene` class from the `CTXEngine::Core` namespace.   
   
Now we move on to creating scene files. Just like for the app, let's create the `ExampleScene.h` header file. And include the base file `CTXEngine.h`, and if necessary the namespaces.
```cpp
#pragma once
#include "CTXEngine.h" 
					   
//Open basic access to engine files. (Optional)
using namespace CTXEngine::Core;
using namespace CTXEngine::Graphics;

class ExampleScene : public Scene
{
public:
	ExampleScene();
	~ExampleScene() = default;
};
```
And don't forget to inherit our scene from `Scene`, otherwise our class will lose all the properties of the scene.   
Just like the app class, the scene has its own functions. Let's talk about them a little.
      	
- The `void onCreate()` function will create load this scene in to memory witch initialization other game objects, variables, classes and components. And if all loading success then set the global flag in this scene on true. You can check it with   
`scene->isInit()`.   
  
- The `void onEvent(Event& event)` will be accept input events intitalze upper, if it need. Also be called in runtime from engine side. `Event` class is stored in the `CTXEngine::Core::IO` namespace, and represent a single event in the engine.   
   
- The `void onUpdate(Delta delta)` function calls every frame (while loop), i.e update this scene. All the application logic can be performed here, starting from player management and ending with the generate tiles of blocks / objects. The Delta class is stored in the `CTXEngine::Core` namespace, and is required for describes the time difference between the previous frame that was drawn and the current frame.
If your don't understand what is a Delta time in game engine, definitely recommend read this documentation.
   
- The `void onRender()` function calls every frame where and prefers to graphics, rendering some object in the scene. Basically
its sub-function to `onUpdate`, because its part of it. This is just dividing functions to separare game logic updating, and rendering calls.   
 
- The `void onRenderGui()`  function now using for rendering all two-dimensional objects. One good example of that is Dear Im'Gui Interfaces. But in future here you can render any 2-dimensional GUI's/UI's on top of 3D/2D objects.
   
And now we are adding these methods to our scene class.
```cpp
public:
	void onCreate() override;
	void onEvent(Event& event) override;
	void onUpdate(Delta delta) override;
	void onRender() override;
	void onRenderGui() override;
```
After that, as well as for the application class, we create the implementation file `ExampleScene.cpp`. And include `EcampleScene.h`, and `ExampleApp.h` to get access to your application, or you can don't include this file and use static instance `ExampleApp::getInstance()`.
```cpp
#include "ExampleApp/Scenes/ExampleScene.h"
#include "ExampleApp/ExampleApp.h"

ExampleScene::ExampleScene()
	: Scene("Example Scene") {}

void ExampleScene::onCreate()
{
	this->setInit(true);
}

void ExampleScene::onEvent(Event& eventIn)
{
	//reciving the events
}

void ExampleScene::onUpdate(Delta delta)
{
	//update logic
}

void ExampleScene::onRender()
{
	//clearing the screen 
	RenderEngineHelper::clear();
	RenderEngineHelper::clearColor(Vector4f{ 0.4f, 0.4f, 0.4f, 1.0f });

	RenderEngine2D::begin(cameraController);
	{
		//render some object
	}
	RenderEngine2D::end();
}

void ExampleScene::onRenderImGui()
{
	return;
}

```
   
In `onRender()` function i call few commands from `RenderEngineHelper` from `CTXEngine::Graphics` to clear screen with new colour.
```cpp
	RenderEngineHelper::clear();
	RenderEngineHelper::clearColor(Vector4f{ 0.4f, 0.4f, 0.4f, 1.0f });
```
Also begin new rendering scene we need have the basic camera. In `CTXEngine::Component` already has a `ComponentCameraController` for camera. You just need `getCamera()` from `cameraController`.
```cpp
	RenderEngine2D::begin(cameraController.getCamera());
	{
		//render primitives.
	}
	RenderEngine2D::end();
```
Between `begin()` and `end()` from `CTXEngine::Graphics::RenderEngine2D` has a drawing of game primitives/game objects. If you wan't to render some object, now support only 2D primitives, call `RenderEngine2D::render*()` here. I will tell you more about rendering using CTXEngine later, when it is completed.   
   
 We have finished creating your own scene, now you just need to add scene to container in `ExampleScene` constructor
```cpp
this->elemContainer.addElementLast(new ExampleScene());
``` 
You can use two ways to do this by `addElementLast()` to add scene to end of container, and `addElement()` to add scene to begin of container. And after that you can run your app with peace of mind.
  
### Full Code
[Example Application](https://github.com/KennyProgrammer/CTXEngine/blob/master/.github/ExampleApplication.md)   
[Example Graphics Application](https://github.com/KennyProgrammer/CTXEngine/blob/master/.github/ExampleGraphicsApplication.md) 