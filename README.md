# Configuring and Launching Mobile Sessions

*The first step in any mobile app automated test is to create an Appium session. This is more or less the same as what we do with Selenium to create browser session, but there are some important differences.*

What you need to have on hand in order to start an Appium session goes a little bit beyond what you'd need for a Selenium session. Let's have a look at what it is.

1. First, you need to have an Appium server running (or you need to have one on the Internet you're connecting to). The Appium Python client will not start the Appium server or configure Appium drivers for you.
2. Second, you need to have a copy of your app handy so that Appium can install and launch it on the mobile device. This is a bit different from a web test. With Selenium, it's assumed that any app is already going to be hosted on a server somewhere, and you're just going to provide the URL so the browser can navigate to the app to begin testing. With Appium, there are several ways we can start a session. If the app is already on our test device, then we don't need to have another copy of it. We can just direct Appium to launch it. But the more common setup is where we have a copy of our app, let's say it's the latest build that's been created by the dev team. As long as it is somewhere on the same machine where Appium is running, we can give Appium a path to this app, and Appium will install it and launch it for us. This is also preferable because it allows Appium to determine information about the app that you would otherwise have to provide specifically. We'll talk more about what format the app needs to be in shortly.
3. Third, it's best to make sure that the device you want to automate is already connected or running. With Appium, you can automate either virtual devices, meaning emulators and simulators, or real physical devices. In this course we're going to focus on launching emulators and simulators in our examples since they are easier to obtain. Also, setting up a real iOS device for testing can be quite challenging due to Apple's strict app security requirements. When submitting your code challenges, you'll be running them on HeadSpin's platform, which consists entirely of real devices. All that to say you'll get a taste of both real and virtual devices in this course. But for the purposes of this point, it's a good idea to have the specific device you want to automate already launched.
4. Finally, the capabilities required to start an Appium session go beyond the simple <code>browserName</code> and <code>browserVersion</code> required for Selenium sessions. We'll look in detail at which are required here momentarily.

First, let's talk about the format that your mobile app needs to be in in order to automate it with Appium. Of course, if you're automating a web app, you can just use the mobile browser to navigate to the site, and nothing additional is required. But if you're automating a native or hybrid app, then you will need the app binary. For our test app, you downloaded these files in a previous unit. For other apps, you need to make sure they conform to these rules:

1. First, the app should be a debug or development build. It's not possible to install a production or release build onto a virtual device. Whoever generates builds of your app should know how to do this, if it's not you doing it yourself.
2. Second, the app should be in <code>.apk</code> format for Android, or <code>.app</code> format for iOS. There's another format for iOS, namely <code>.ipa</code>, but this is really only used for installing apps on real devices. One thing to note is that <code>.app</code> files for iOS are actually directories, not single files. So to transport them around the internet, it's customary to zip them up, so that the actual extension you will often see is <code>.app.zip</code>. This is an extension that Appium knows how to work with, and this is the extension you downloaded The App with.
3. Third, the app must either be located on the same system as the one running Appium, or somewhere on the internet accessible via a URL. This means that if you're using a cloud service like HeadSpin, there's usually a specific method for pre-uploading your app and then referring to it, since the cloud service won't have any access to your local filesystem where the app is sitting.

Now let's cover the capabilities required to start any Appium session!

1. The first required capability is called <code>platformName</code>. This is a capability you might recall from Selenium. With Appium it's a bit more important. It tells Appium the name of the platform you want to automate. In our course, it will be either iOS or Android.
2. The next one is <code>platformVersion</code>. This capability should be a string representing the version of <code>iOS</code> or <code>Android</code> you want to automate. This helps Appium to target the right device and give you an error if it can't find a connected device at the appropriate version.
3. Third, a capability that does not exist in the web world is the <code>deviceName</code> capability. Basically, it gives the Appium server information about the specific device you want to automate. For example, a value for this capability might be "iPhone 6S" or "Nexus S".
4. Fourth, we need the <code>automationName</code> capability. This tells Appium the name of the driver you want to use for this session. When we set up Appium earlier, we ran the commands to download the UiAutomator2 driver and the XCUITest driver. The output of those install commands will let you know the specific name that can be used with this capability. Don't worry, I'll show you specifically what the names are in our examples.
5. Finally, we need to tell Appium something about the app we want to automate. Here we have a choice of capability to use. If we just want to automate a browser, in order to test a website, we can use the <code>browserName</code> capability, with a value of "Safari" on iOS or "Chrome" on Android. But if we want another kind of app, we need to use the <code>app</code> capability, and the value of this one should be an absolute path to our mobile app apk or .app file. Alternatively it could be a URL where the same .apk or .app.zip file could be downloaded.

With the combination of these five capabilities, Appium will know how to start a session for you.

OK, let's switch over for some hands-on experimentation with starting sessions. In my terminal, in our code project directory, I'm going to create a new directory called <code>mobile</code>, in the main project directory, to hold all our mobile examples:

    mkdir mobile
    cd mobile

And now I'm in that directory as well. In my editor, I'm going to make a new file called <code>sessions_ios.py</code> inside the <code>mobile</code> directory. The first thing we want to do is import the Appium client so we can use it. This is similar but slightly different than importing the Selenium client. In Selenium, we did from <code>selenium import webdriver</code>, but now we are going to do:

    from appium import webdriver

This ensures we're using a client that has all of the Appium command extensions we might need to use. Now, we need to figure out how to get the absolute path to the app we want to test. I'm going to start an iOS session in this file, so I need to somehow find the iOS version of The App. I could just create a string variable holding the path to that downloaded file, if I know where it is. But it's a bit more convenient to locate the app close by and refer to it in terms relative to my project. So I'm going to copy both the apps (iOS and Android) into this <code>mobile</code> directory. And now I need to teach my script where this directory is! To do that, we should first import a module called <code>path</code> from Python's <code>os</code> library:

    from os import path

    CUR_DIR = path.dirname(path.abspath(__file__))

With it, I can create a variable designed to hold the absolute path to the current directory. I use a combination of <code>path.dirname</code> and <code>path.abspath</code> for this, as well as the magic variable <code>__file__</code> which always refers to the file where it is encountered.

Now, I can use another path function called join to <code>join</code> the path of the current directory to the filename of my iOS app:

    APP = path.join(CUR_DIR, 'TheApp.app.zip')

The end result of this will be that the absolute path of our app file is in this <code>APP</code> variable. Notice that I'm using all caps for these variables. It's not necessary to do this and it has no effect on the Python code whether we use all caps or not. But it's a sort of convention to define constant values that will be used globally throughout a script with all caps. It helps to distinguish them from variables that we might define and discard along the way. The next thing that's convenient to define up top is the location of the Appium server we're going to use. And that makes now a great opportunity to start that Appium server. So I'm going to open up a new terminal window, and start Appium there:

    appium

It's as simple as running this <code>appium</code> command, and I'll see Appium's startup info to know that it's listening. I'll also pay attention to the port that Appium is running on, which is the default port of 4723. I can change this using a command line flag if I want to for some reason, but I'll leave it as is for now. Looking at Appium's startup printout, I also notice that I have the XCUITest and UiAutomator2 drivers installed, and Appium even tells me what <code>automationName</code> capabilities to use to target them! That's good to know too. 

Now that I know Appium is running on my localhost interface at port 4723, I can encode this knowledge in a variable:

    APPIUM = 'http://localhost:4723'

This isn't strictly speaking necessary, but I like to put all data strings like this up at the top of the file, so if I ever need to change them, it's easy to do in one place. What else do we need before we start the Appium server? We need to define our capabilities! Let's do that now:

    CAPS = {
        'platformName': 'iOS',
        'platformVersion': '16.2',
        'deviceName': 'iPhone 14 Pro',
        'automationName': 'XCUITest',
        'app': APP,
    }





