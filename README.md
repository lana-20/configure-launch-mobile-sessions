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

....




