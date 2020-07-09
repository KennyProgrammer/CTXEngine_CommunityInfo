# Prepare Project
This is a simple example how you can download and install the `CTXEngine` game engine. This is a step-by-step example of downloading and configuring the project for the app.

### Install
Since the engine currently only supports Windows, this is a Windows installation. Other platforms will appear a little later.
   
- First of all you need to *download* and *install* Git For Windows: https://gitforwindows.org/. Next, when you install Git during installation, don't forget to check the box to enable Git in the Windows console. This is necessary in order to use Git commands from a normal Windows console.
   
- Then you will need to open the console and write `git clone --recursive https://github.com/KennyProgrammer/CTXEngine` to clone the repository. But I strongly advise you not to do so. So the engine is still in early access and cloning can cause a lot of errors that will not allow you to build the app. For this purpose, I created a special [CommnadMasterInstaller.bat](https://github.com/KennyProgrammer/CTXEngine/blob/master/Build/Command%20Master%20Installer.bat) file for automating this process. Simply put, download or copy this file to your desktop and tear it off.
   
- Before starting the file, I recommend going to `Start` -> `Environment Variables` -> `Change System environment Variables`, and there add the variable ```GIT_HOME```, and the value of the variable specify the path to your Git, it is usually set to `C:\Program Files\Git` to make it easier for the installer to find Git.
   
- Next open it and he do all stuff what you need to getting started.
   
- After cloning the repository and submodules, the project will need to be built. You can build it using different IDES, but only Visual Studio is currently available. In the directory `C:\CTXEngine\Build` select the desired ' Build.bat ' file and open. This process takes several nanoseconds.
   
### Project Setup
After build on Visual Studio, you will have the `Application` project, which is what you need. If you want to change the project name, you need to go to the engine directory `C:\CTXEngine`, open the premake5.lua file, and there find the project `Application` line. You can change the project and location parameters to your own name. You can also change the `Application.rc` file below. (Deprecated, if `.rc`, `resource.h` or `.aps` files exist just delete it.)
   
**I strongly do not recommend changing other parameters, because then the engine may fly, and I can't help you. In the future, it will all be automated or in some editor.**
   
Everything is ready! Now you can safely run the `CraftixEngine.sln` file and start writing your application. You can find a tutorial on how to write your own application using the CTX Engine down below.
   
- [Create Application](https://github.com/KennyProgrammer/CTXEngine/blob/master/.github/CreateApplicationTutorial.md) 
- [Create Graphics Application](https://github.com/KennyProgrammer/CTXEngine/blob/master/.github/ExampleGraphicsApplication.md)  
- [Back](https://github.com/KennyProgrammer/CTXEngine/blob/master/README.md)   