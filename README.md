# Scrolls logging library

A simple logging library that allows to use different log destinations, most importantly allows to log to a file. Also has tools to take care of file sharing, cleanup. Also to allows to browse log files in the integrating application itself. 

This library can be very easily used as s sub-Tree for Timber library, allowing easily configurable extended logging for debug versions.

## Getting started

#### Adding the library to a project

1) Add the Scrolls dependency declaration to your module's `build.gradle` file, to the dependencies enclosure:

```groovy
dependencies {
// ..
	implementation "lab.mobi.scrolls:scrolls:X:Y:Z"
// ..
}
```

Replace the "X.Y.Z" part with the latest version of the library, available versions are visible at https://bintray.com/mobilab/mobi.lab.scrolls/scrolls-android.

2) Update you application manifest, add the provider:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest package="sample.application"
          xmlns:android="http://schemas.android.com/apk/res/android">

    <application>
        <activity
            android:name="mobi.lab.scrolls.activity.LogListActivity"
            android:label="Logs"
            android:taskAffinity="sample.application.loglist"
            android:theme="@style/AppTheme">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
        <activity
            android:name="mobi.lab.scrolls.activity.LogPostActivity"
            android:process=".LogPostProcess"
            android:theme="@style/AppTheme"/>
        <activity
            android:name="mobi.lab.scrolls.activity.LogReaderActivity"
            android:theme="@style/AppTheme"/>

        <provider
            android:name="mobi.lab.scrolls.ScrollsFileProvider"
            android:authorities="${applicationId}.logs"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_provider_paths"/>
        </provider>
    </application>
</manifest>
```

Notes:
* `LogListActivity` creates a new `Log` launcher icon to show logs
* `@style/AppTheme` styles the activities to better match the consuming application

## Usage

### Logging

```java
/*
 * 1. Most basic usage is as follows:
 * Set the implementation to use. 
 * Needs to be done only once, do it from your launch class or Application object. 
 */
Log.setImplementation(LogImplCat.class);  
/*
 * Get a new instance to use and use the current class name as a log tag. 
 * Also allows to supply a simple String as a tag to use.
 */
Log log = Log.getInstance(this);
/*
 * Start logging. 
 */
log.v("This is a verbose message.");
log.i("This is an info message.");
log.d("This is a debug message.");
log.w("This is a warning message.");
log.e("This is an error message.");
log.wtf("This is an assert message.");
/*
 * 2. In case you want to use more than one log implementation at a time you can use LogImplComposite class.
 * Some log implementations need you to call init() on them first before usage.
 * Let's call init for a classes called LogImplFile and LogImplComposite.
 */
/*
 *  Init LogImplFile by giving the directory to store log files in. 
 *  (Needs to be done only once)
 */
LogImplFile.init(getFilesDir());
/*
 * Init LogImplComposite by giving it two actual log implementations to use, the LogCat one and the File one
 * (Needs to be done only once)
 */
LogImplComposite.init(new Class[] {LogImplCat.class, LogImplFile.class});
/*
 * Now we are ready to set the overall log implementation as the LogImplComposite class
 * (Needs to be done only once)
 */
Log.setImplementation(LogImplComposite.class);
/*
 * Get a new instance and log like always.
 */
Log log = Log.getInstance(this);
log.d("This is a debug message.");

```

### Posting

```java
/* Configure posting to backend giving the project name */
LogPostImpl.configure(this, BuildConfig.APPLICATION_ID + ".logs" /** Provider **/);
/*
 * Get a new LogPostBuilder instance.
 * The builder object has several methods for configuring parameters.
 */
LogPostBuilder builder = new LogPostBuilder();
builder.addTags("my","first","post");    // Sample method to configure tags for the post
/*
 * Do the actual post by starting log posting activity.
 */
builder.launchActivity(context);

```



## Building the library

### Build everything

To build the library and the sample app use the command

```groovy
./gradlew buildAllRelease
```

After the build the Scrolls library `*.aar` and the sample application `*.apk` can be found under `scrolls-android\scrolls-lib\build\outputs` folder.

### Build only the library

To build only the library use the command

```groovy
./gradlew buildLibRelease
```

After the build the Scrolls library `*.aar` can be found under the `scrolls-android\scrolls-lib\build\outputs\aar` folder.

### Release a new version of the library

Please see `RELEASE_GUIDE.md` and `PUBLISHING.md`.