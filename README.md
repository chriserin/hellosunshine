

# Android in the Command Line and Vim

## Make sure you have your requirements installed!

*  JDK 
*  Android SDK
*  Genymotion -- You need a user id, but these emulators are faster than what you get with the SDK.

## Setup the environment.

1.  Android sdk needs a home:
```
ln -s /opt/android-sdk /wherever/you/unzipped/the/sdk/to
```

And in your .zshrc.local file:
```
export ANDROID_HOME=/opt/android-sdk/tools
export PATH=$PATH:/opt/android-sdk/tools
export PATH=$PATH:/opt/android-sdk/platform-tools
```

2. Genymotion needs to know about it, so it can use the adb tool (android
   development bridge) to connect to the emulator from the cmdline.

a. Open Genymotion and navigate to settings.
b. Choose the adb tab and enter the path to your android sdk.

3. run the android command

```
android
```

This opens the "Android SDK Manager" which helps you get the versions necessary
of android for the specific decives and api levels you are targeting.

## Create the Project!

1. Navigate to the parent dir of your future project and enter this simple command.

```
android create project -p hellomoon -n HelloMoon -a Main -k com.hashrocket.hellomoon -g -v 0.11.+ -t 1
```

Yes thats complicated and yes all options are required, here's what they mean

```
-p path
-n name
-a initial activity name (activity is like a controller/view)
-k package name (com.hashrocket.<your_app>)
```

Those are the simple ones, the next 3 require some understanding!  

```
-t android device target id
```

Find the id for the device you'd like to target with:

```
android list device
```

This list changes depending on .... something.  Run this to get the right id
before creating each project.

```
-g Choose to create a gradle project
```

Create a gradle project please!  Gradle is the new build system for java and
android.  Without the -g flag you can create an ant project, but then you'd be
living in 2003 listening to Eminem and getting excited about the LOTR finale.
We're beyond that now!  Gradle is a build, deploy and dependency management
tool utilizing the Groovy language.  Groovy is a jvm scripting language.

```
-v declare your android gradle plugin version
```

The current latest android gradle is 0.11.1 but they change quickly.  You can see which version is latest at the maven central repo [site][m].
[m]: http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.android.tools.build%22%20AND%20a%3A%22gradle%22

2. The gradle plugin updates faster than the sdk, this has created a situation
   where android creates a src/instrumentTest for you, but the gradle plugin
   expects androidTest.  You must change the dirname.

```
mv src/instrumentTest src/androidTest
```
Yep, that's how you create the project!

## Building, Deploying, Running, Testing The Project

Start your genymotion emulator by selecting a device and pressing play to run
the emulator.  You should see the android emulator boot up and load the lock
screen.  Unlock the phone to be able to run applications on it.

Gradle comes with many tasks installed, you can list them with.

```
./gradlew tasks
```

First compile the application.  This will initially download some dependencies and cache them in your ~/.gradle dir.

```
./gradlew build
```

The build task will create a build dir with the final artifacts at
build/outputs/apk.  An apk file is the artifact deployed to your phone.

Next install the application

```
./gradlew installDebug
```

The create project command also created a blank test suite in src/androidTest.
You can deploy this too.  You deploy it because it must run on your phone to
use the instrumentation.

```
./gradlew installDebugTest
```

Check this out, you can open a shell on your phone and check out what packages are installed.

```
adb shell
cd /data/app
ls
```

Lastly, lets "run" the tests.  Obviously in this scenario there wouldn't be any
tests there but you can run the blank test suite anyways.

```
adb shell am instrument -w com.hashrocket.hellomoon.test/android.test.InstrumentationTestRunner
```

Awesome!  Thats it!  Next up is installing and running espresso.
