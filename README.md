# DAS-Android-Application
The Android Application for the online driver advisory system

## Introduction
Before you can start developing on an Android phone there are a couple of things that needs to be done. Firstly, the option of USB debugging must be enabled. This can be activated in the developer options on the phone. To get access to the developer options on the phone you need to find the build version (under “about the device”). You click the version number seven times to activate the developer options. This is already done on the S7 phones we used. 

In order for the application to work the Google play services must be installed on the device. In case of this missing the application will automatically prompt you to get this from PLAY store.

If the application is to be installed on a phone without access to the code, i.e. a release build, this is done by accessing the APK files which is included in the StreamDAS folder on dropbox. The APK files is located under StreamDAS\app\build\outputs\apk.

When the application is run the first time it will prompt the user to allow the application to use the necessary permission such as location.

## Operating the application
In the application the first thing you will see is the logo screen. The time this is shown can be easily altered by changing the number located at the very bottom of MainActivity. Currently it is set to show for 2500 ms.  

When the logo screen disappears you will see the select screen which will allow you to choose which route, train, weight and time you want to use. These options are linked to the correct files which can be found in the assets folder. On this screen, if you press the toolbar at the top (the three dots) you can also set the application to go into demo-mode. This mode will use previously gathered GPS data and simulate this route and the application behavior. When the demo-mode is activated this will show in text in the bottom left corner of the screen. Note that at the moment only the route Västerås-Kolbäck and vice versa will work regardless of mode. Thus the dropdown menus time and train won’t make a difference.

When the desired traits have been chosen and the go button is clicked the application will load the corresponding files. The progress can be viewed on the screen both in packages read and time remaining. When the files have been read the mainview-screen will be visible. This shows the initial parameters that are calculated. It will also depict the graph containing the initial optimal speed profile, speed limits, position and elevation. A second screen has been added which can be viewed by swiping right. Although this screen does not show anything at the moment but is to be added with energy information. 

When the start button is clicked, depending on if it is in demo-mode or not, will start collecting GPS-locations and calculate upon these. Note that each route has its own corresponding posts which are placed along the train track which the route is travelled upon. Therefore, the application will not show anything unless you are travelling along the right train track. This is of course not the case in demo-mode. When the application has been started it will show two new things in the graph. The new optimal speed profile as well as travelled position in distance and velocity. 

During this there is an option in the toolbar called recalculate. This option will prompt a message asking if the user is sure. If ok is clicked the application will take the current position and try to figure out how far along the track it has travelled and start the calculations anew with the correct time and velocity. This option is only necessary to use when the train has stopped/been delayed unwillingly. If the time is correct the application will correct itself otherwise. NOTE: this function is untested but should work in theory.

When you have travelled the complete distance and arrive at the goal the application will stop all the calculations and display the entire route on screen with the travelled velocity and distance. The application writes all the usable data it has collected during the trip to log files that are accessible in the Android/data/Stream.sics.streamDas/files/logs folder on your device. The log-files are GPS-coordinates before and after the start button has been clicked. It also saves the GPS-coordinates that has been deemed too bad in a separate log. It also saves another log-file which contains the inputs to the calculations.

Anytime during run-time, you can click the back button which will prompt an “are you sure” message. If agreed the application will kill all the threads and return you to the select screen where a new choice can be made. 

The application is set to always run in any other case, regardless of interruptions such as a new application started or phone-calls etc.

## Android SDK/OS
There are some Android constraints which is included in the OS that can’t be circumvented and needs to be adhered to. One such constraint is that the OS is very picky about which threads that are allowed to update the User-Interface. This is only allowed on the main activity thread (UI-thread) which is the current activity. All other threads must run UI altering code on the UI-thread. Fortunately, this is not as complicated as it first seems. In our case we have implemented a global handler connected to the UI. This can be used to run code in the UI-thread from other threads. 

Another constraint that follows Android is the memory that the application is allowed to use. We have already maxed the memory given by the OS and still we just barely have enough. This problem can be circumvented by splitting up the 3D matrix in the dat-files into smaller parts since we need to store it in a buffer before assigning it to variables. Read more about this in not yet implemented.
Another thing to think about is that the application is developed to work on older phones at the moment. So when, and if, new libraries/methods are added to the code it is important to check that the code also work on older phones.

When creating threads in Android there are multiple ways of doing this. I will briefly summarize the way we do it. There might be better ways to do it but this can all be read on android’s developer pages.  One of the easiest ways to do it is just to create a new java file in the project which implements Runnable. Then you just need to have the function “public void run()” When you create an instance of this class it will automatically start a new thread that starts with the function mentioned above.

There are many libraries and functions which implements callbacks. These are answers that the application gets Asynchronically. Our best explanation is that if a function utilizes callback functions, go to the android developer pages and study up on them.

Every thread has override functions called onStop, onStart, onDestroy etc. These functions are called at certain times such as, onCreate is called when the current instance of the thread is created. onDestroy is called when the current instance of the thread is killed and so on. A flowchart of these can be viewed under activity of the Android developer pages.

## General
The application utilizes two external libraries that can be found for free on the internet. These two libraries are the colt library and the android plot library. Documents about the functions and implementations of these libraries can be found on their respective websites:

Colt homepage: http://dst.lbl.gov/ACSSoftware/colt/

Colt API: http://dst.lbl.gov/ACSSoftware/colt/api/index.html

Android Plot homepage: http://androidplot.com/

Android Plot API: http://androidplot.com/docs/

There are a few variables which have been tuned by testing. These parameters have only been tested a few times and might suffer from bad calibration. The variables are all related to the GPS and the GoogleAPI which will be explained later. The variables are Accuracy – decides how inaccurate the GPS-locations are allowed to be and still be included in the calculations. This is currently set to less than 17 meters radius. Displacement – tells the GPS how far we must have travelled before a new point is collected. This is currently set to 0 meters since it seems to work best. Updatetime – tells the GPS how often we want updates. This is currently set to 5 seconds as it seems to work the best. It is also the recommended setting for the best result. 
One thing to note is that Java differs from the MATlab script the calculations where originally developed in by starting all its arrays from position 0 instead of 1.

The general settings and permissions of the application is set in the manifest file and at the moment we think that all the necessary code is already in this file. If something needs additional code in the manifest file it will certainly say so on the developer pages.
All the layouts of the different views are set in the xml files located in the res/layout folder of your project in android studio. It has a very simple graphical interface which is easy to use and understand if you don’t want to do it programmatically. 
All the resources such as strings colors etc. are located and set in the corresponding resource folders in your project.
At the moment all of the spinners (dropdown menus) on the select screen are hard coded and this should be changed (read in not yet implemented). If a new route is added the dat-files, kmpost-files,stations-files needs to be added in the assets folder of your project. They also need to be hardcoded to the spinners choices. How to generate new kmpost and station files is found in the javascript readme file.

Here follows a brief explanation of all the java files included in this project.

Calculation.java: This file contains all of the mathematical equations necessary to the application. It is also responsible for updating, through the UI-thread, the values of; battery level, tractive effort, next velocity and next tractive effort. It also prints the inputs passed to this file to a log file called online_calculations.

EnergyFragment: This java file is responsible for creating the fragment used in the swipe-view. It is connected to the xml file corresponding to the energy-view. To add more swipe-views in the pageadapter you just need to make a duplicate of this file, with a new name and xml file of course.

GetDat: This java file is responsible for reading the correct dat-file, kmpost-file and station-file. It also handles the updating of the loading bar.

Global: This java file contains all of the global variables in the project. Just declare variables as public static … in this file to add new global variables. These are then accessed by writing: Global.somevariable.

GoogleApi: This java-file contains the code that handles everything about the GPS. Almost all of the functions in this file is callback functions in Google’s own API. Therefore, this code isn’t viewable since it is precompiled and hidden. If questions should arise about these functions you will need to look up Google Api, especially the location bit on their website. It also stores the locations received in a global list and writes them to log-files.

Google API: https://developers.google.com/android/reference/packages

MainActivity: This java-file is just a holder for the logo-screen. It also creates the folder that holds the log-files. It shows the logos for a certain amount of time and then starts the select activity.

MainView (UI-thread): This java-file creates the pageradapter which handles the fragmentviews (swipe-views). It is responsible for starting the correct threads (getDat, Testing, runDas). It also has the implemented recalculate function. This is the UI-thread for the view containing the graph.

MainViewFragment: This is just like EnergyFragment except for the Mainview xml.

Plotter: This java-file handles everything that has to do with the graph. Setting the parameters, updating and also the call for initial calculations.

RunDas: This java-file is the worker-thread which is the center of the background operations. It is the thread that passes received locations into the calculations. It calculates distance travelled and holds the correction algorithm. It decides when we have reached our destination and also updates the distance and time remaining before the next time-step. It also calls the plotter when necessary.

Select (UI-thread): This java-file contains everything that has to do with the select screen. It initializes the spinners with names and updates them. It connects the recalculate option to the right function and it initializes the GPS as well.

Testing: This java-file contains the necessary code to start the demo-version of the application. It enters existing data into the location list which it gets by reading the corresponding file.

## Known issues
Although most of the application works as intended there are a few known (or unknown) bugs left.

Sometimes while building the project it will result in “Gradle build finished with 2600 error(s)” This seems to be a problem with Android Studio. If this happens just run the program again and it will disappear.

Added very late in the project and therefore the recalculate function is untested. This needs to be verified in some way.

We had some problems with speed-spikes in the graph due to GPS-errors. This issue has had a solution made but unfortunately it has not been tested.

The calculations seem to be faulty at the very end of the routes where it claims that the location of the next time-step is very far ahead (200000m +)

If the train does not follow the speed-graph recommended by the application it might calculate the same time-step twice which will give a seemingly useless point in the graph just a couple of meters ahead, regardless of travelled velocity.

The application corrects itself accordingly when it comes to distance travelled. However, the goal (end station) might be different each time (even on the same route). This is due to the fact that the train driver might stop earlier/later, the platform might have two parts (a and b). At the moment the application does not know if we have arrived if the travelled distance is shorter than that declared in the dat-file. 

The application has only been tested on one type of phone. Therefore, the implications of running on a screen with a different resolution is unknown.

The GPS might not always find locations and this is a problem we can’t really do anything about. There is a function which makes sure that the locations given can’t be too bad. However, errors in the GPS will always be there and there might still be subsequent bugs because of this.

The graph library choice for this project was a poor one. Although it displays what we want a lot of desired functionality is missing. Some problems that we know of is that it draws outside of the boundaries of the graph, the legend widget cannot be set to have certain positions for certain series but instead changes this dynamically. Therefore, it is our conclusion that if possible this library should be interchanged with a more complete one. One suggestion (although not researched) is to switch to graphviewer.

In the monitor of android studio there is a graph showing gpu usage. This has a line that shows the recommended updating frequency of drawing on the UI. The recommended time is 16 ms. However, our application is currently around 90ms everytime the graph updates. This might be one reason to the fact that the phone gets very hot when running our application.

Since the application haven’t been tested as much as it should there are some simulation files which are missing data. Since the testing were performed simultaneously as the development some simulation files does not reach the destination, others have speed-spikes and graphing problems. These should be changed to simulation data that are collected since the final version of the application were posted. If a demonstration is to be showed: use västerås-kolbäck AV1 or kolbäck-västerås AV1 since these are good simulation files.

The last thing to mention is that a general debugging of the entire application has not yet been performed. There is a built in tool in Android Studio which is called inspect code. If you do this on the whole project, then you should be able to sort out some of the problems. Also a general debugging using the tools in the developer options of the device should be performed. 









