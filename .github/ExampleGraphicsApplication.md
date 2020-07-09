# Example Application   
This is a simple example how you can create a application using CTXEngine.   
[Full tutorial here](https://github.com/KennyProgrammer/tree/master/CTXEngine/README.md)
   
### ExampleApp Header file (.h/hpp)
   
```cpp
#pragma once

#include "CTXEngine.h" //hold all others engine imports

using namespace CTXEngine::Core;
using namespace CTXEngine::Core::IO;
using namespace CTXEngine::Graphics;

class ExampleScene : public Scene
{
	public:
		/*Basic constructor of this scene.*/
		ExampleScene();
		/*Basic destructor of this scene.*/
		virtual ~ExampleScene() = default;
		
		/*This is main ortho camera for now.*/
		ControllerCameraOrthographic mainCameraController;

	public:
		/*Create this scene witch initialization other objects and components.*/
		void onCreate() override;
		/*This function calls every frame, process events.*/
		void onEvent(Event& event) override;
		/*This function calls every frame (while loop), i.e update this scene.*/
		void onUpdate(Delta delta) override;
		/*This function need to render all graphics objects.*/
		void onRender() override;
		/*This function need to render 2d graphics objects.*/
		void onRenderGui() override;
};

struct ExampleConfigurations : public CoreConfigurations 
{
	ExampleConfigurations();
};

class ExampleApp : public CoreApp
{
	public:
		/*Default constructor.*/
		ExampleApp();
		/*Default destructor.*/
		~ExampleApp() {}
	private:
		/*This is instance of this kinda of class.*/
		static ExampleApp* instance;
		/*This is instance of this kinda of custom config class.*/
		static Reference<ExampleConfigurations> config;

	public:
		/* Return the pointer to reference of static instance of this app. */
		static ExampleApp& EditorApp::getInstance();
};
		
```

### ExampleApp Source file (.cpp)
   
```cpp
#include "CTXEnginePreCompile.h"
#include "ExampleApp.h"
#include "CTXEngine/Main.cpp" //warning:: NEED INCLUDE ONCE

ExampleApp* ExampleApp::instance = nullptr;
Reference<ExampleConfigurations> ExampleApp::config = CreateReference<ExampleConfigurations>();

ExampleConfigurations::ExampleConfigurations()
	: CoreConfigurations()
{
	this->ctxAppName = "Example Application";
	this->ctxAppVersion = "0.0.1a";
	this->ctxWindowFullscreen = false;
	this->ctxWindowWidth = 1280;
	this->ctxWindowHeight = 720;
}

ExampleApp::ExampleApp()
	: CoreApp(this->config)
{
	this->instance = this;
	
	this->elemContainer.addElementLast(new ExampleScene());
}

ExampleApp& ExampleApp::getInstance()
{
	return *instance;
}

ExampleScene::ExampleScene()
	: Scene("Example Scene"),
	  mainCameraController("PlayerController", 1.6f / 0.9f, true, false, true)
{}

void ExampleScene::onCreate()
{
	this->mainCameraController.setFieldOfView(7.0f);

	this->setInit(true);
}

void ExampleScene::onEvent(Event& event) 
{
	this->mainCameraController.onEvent(event);
}

void ExampleScene::onUpdate(Delta delta) 
{
	this->mainCameraController.onUpdate(delta);
}

void ExampleScene::onRender() 
{
	RenderEngine2D::resetStats();
	{
		RenderEngineHelper::clear();
		RenderEngineHelper::clearColor(this->backgroundSkyColour);
	}

	//===== Begin World =====
	RenderEngine2D::begin(this->mainCameraController.getCamera());
	{
		RenderEngine2D::renderRectangle({ 0, 0 }, { 1, 1 }, {1, 1, 1, 1});
	}
	RenderEngine2D::end();
}

void ExampleScene::onRenderGui() 
{
	return;
}

/*
	This method will be run core engine for this game. Basically calls
	main method and create application thread. Its be return new pointer of
	class ExampleApp, then run method 'run'..
	
	Update: 'noecxept' needs to be after function. Thats means to this func no have
	exception throws.
*/
CTXEngine::Core::CoreApp* createApp() noexcept
{
	return new ExampleApp();
}

```
[Back](https://github.com/KennyProgrammer/CTXEngine/blob/master/.github/CreateApplicationTutorial.md)