# Introduction #

This page describes how a libGDX application is structured and how to target different platforms using different back-ends.


# The Application #

This is the main entry point for any libGDX project. The Application determines the target platform and the graphics back-end to be used by the application. The `Application` interface also provides convenience methods to access the generic **Graphics**, **Audio**, **Input** and **File** I/O modules.
It also gives access to a generic **Logging** module which works across all platforms.

LibGDX currently supports 2 back-ends for desktop applications (lwjgl and jogl) and one for Android.

To create a libGDX application, an `ApplicationListener` has to be implemented first.

# The Application Listener #

The listener implementation is responsible for initialising the application, updating the game state (ie. game logic), rendering the output image, pausing the game, saving the state and releasing the resources when exiting the application.

It is also the place where the application life-cycle events are handled.
Every application/game, regardless of the back-end and/or target platform will have to implement the `ApplicationListener` interface.
The implementation is exactly the same for all platforms.

The [Application](http://libgdx.l33tlabs.org/docs/api/com/badlogic/gdx/Application.html) is responsible of the game loop and the [ApplicationListener](http://libgdx.l33tlabs.org/docs/api/com/badlogic/gdx/ApplicationListener.html) is the place where the game logic will be implemented.

A simple `ApplicationListener` implementation looks like this:

```
public class MyGame implements ApplicationListener {
        public void create () {
                // STUB
        }

        public void render () {
                // STUB
        }

        public void resize (int width, int height) { 
                // STUB
        }

        public void pause () { 
                // STUB
        }

        public void resume () {
                // STUB
        }

        public void dispose () { 
                // STUB
        }
}
```

The methods are triggered by events in the application life-cycle as described:

| **Method signature** | **Description** |
|:---------------------|:----------------|
| `create ()` | Method called once when the application is created.|
| `resize(int width, int height)` |This method is called every time the game screen is re-sized and the game is not in the paused state. It is also called once just after the `create()` method.<br /> The parameters are the new width and height the screen has been resized to.|
| `render ()` | Method called by the game loop from the application every time the rendering happens. The game update also takes place here before the actual rendering.|
| `pause ()` | The pause method is called just before the application is destroyed. On Android it is called when the Home button is pressed or an incoming call is happening. On desktop this is called just before `dispose()` when exiting the application.<br /> It is a good place to save the game state on Android as it is not guaranteed to be resumed.|
| `resume ()` | This method is called ONLY on Android, when the application received the focus. On desktop this method will **NOT** be called, ever.|
| dispose () | Called when the application is destroyed. It is preceded by `pause()`.|

### The Application Life-cycle ###
The above-mentioned methods are triggered by the `Application` during its life-cycle.

The following diagram shows the different phases a libGDX application goes through:

![http://libgdx.googlecode.com/svn/wiki/img/application_lifecycle_diagram.png](http://libgdx.googlecode.com/svn/wiki/img/application_lifecycle_diagram.png)

## Creating the Desktop Application ##

The following class creates a desktop application using lwjgl from the previously created `MyGame` listener.

```
import com.badlogic.gdx.backends.lwjgl.LwjglApplication;

public class MyDesktopGame {
	public static void main(String[] args) {
		new LwjglApplication(new MyGame(), "My Game", 480, 320, false);
	}
}
```

Running this will have the following result:

![http://libgdx.googlecode.com/svn/wiki/img/application_lifecycle_screen.png](http://libgdx.googlecode.com/svn/wiki/img/application_lifecycle_screen.png)

It creates a lwjgl backed application 480 pixels wide, 320 pixels tall. The last parameter indicates that OpenGL ES 2 is **NOT** to be used.

The following class creates a desktop application using JOGL as the back-end from the same `MyGame` listener.

```
import com.badlogic.gdx.backends.jogl.JoglApplication;

public class MyDesktopGame {
	public static void main(String[] args) {
		new JoglApplication(new MyGame(), "My Game", 480, 320, false);
	}
}
```

In both cases the appropriate libraries have to be used. In the first case the following jar files need to be included:

  * gdx.jar
  * gdx-natives.jar
  * gdx-backend-lwjgl.jar
  * gdx-backend-lwjgl-natives.jar

In the second case, for jogl, the following jars need to be included:
  * gdx.jar
  * gdx-natives.jar
  * gdx-backend-jogl.jar
  * gdx-backend-jogl-natives.jar

The constructor for the desktop application takes 5 parameters:
|[ApplicationListener](http://libgdx.l33tlabs.org/docs/api/com/badlogic/gdx/ApplicationListener.html) listener | This is an instance of the `ApplicationListener` created for the application game|
|:-------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------|
|`String title`| The title of the application which will be shown on the title bar|
|`int width`| The screen width of the application window|
|`int height`| The screen height of the application window|
|`boolean useGL2`| `true` if the we want the renderer to use OpenGL 2, or `false` to stick with OpenGL 1.x|

## Creating the Android Application ##
Creating the Android Application involves a bit more but it is still very simple.

This involves creating an Android project and link the current one to it.
On how to do this please take a look at the [My First Triangle](MyFirstTriangle.md) article, _Setting up the Android Project_ section.

You will need the following directories and jar files:
  * armeabi/
  * armeabi-v7a/
  * gdx-backend-android.jar
  * gdx.jar