As developers, we always want users to update to the latest application version as soon as we release new features. You want everyone to have the latest version installed, and you want them to use the latest features you’ve developed.

Google notifies Android users that some applications need updates (some users turn auto-update on), which is excellent. Still, you want your users always to know when your application has a new update available.

Suppose a user has your application installed on their mobile phone. You have added new critical features or fixed a bug to the application. The only way the user can get the latest updated features is by updating the application. Assume that many users don’t have the interest or time to open the Google Play store and update their applications. It can take time for all your users to have the latest version installed on their mobile phones.

To solve this problem, Google I/O introduced an [in-app update API](https://developer.android.com/guide/playcore/in-app-updates). This API alerts users whenever you have a new version on the Play store by poking the user to update the application without opening the Play store. The API introduces an update UI within your application context to show users you have an update available. While users are using the application, they can update it to bring the new updated features on board.

In this guide, we will learn about Google's in-app updates and implement them in our applications. We will discuss the two methods to implement in-app updates. They are:

- Immediate
The immediate update introduces a blocking full-screen UI. When a user starts the update, he/she can't use the application until the update is installed. The app will automatically restart when the update has been completed. This method is preferred when the update introduces critical functionality.

- Flexible
A flexible update allows users to interact with the application while the update takes place in the background. Once the update is downloaded, the user will be prompted to restart the application. While restarting, the application will install the new updates and open the app to the foreground. This method is preferred when the update has minor changes that do not affect the application's critical functionalities.

### Prerequisites
This guide assumes prior knowledge of Android application development using Android Studio.

### Requirements
- A device running Android 5.0 or (API level 21) higher.
- Google Play Core Library version 1.5.0 or higher.

### Implementation
Add the following library on your `app.gradle` file.

```java
implementation 'com.google.android.play:core:1.8.3'
```

Sync to download the library.

At the time of writing this guide, the Play Core version was 1.8.3. It is recommended to have the latest version. Check for the latest version [here](https://developer.android.com/reference/com/google/android/play/core/release-notes).

### Background
Setting the in-app update is simple. The update process is well taken care of by the Google Core API. A user doesn't have activate auto-updates in the play store. The API will handle the update flow within the application that you have implemented the in-app update concept.

Before handling the update type, either flexible or immediate, you should understand the flow of events that this API processes to meet the goal of update completion.

To check if there is an update available in the Play store, we have to create an instance of `appUpdateManager`. It communicates with the `AppUpdateInfo` object. The object triggers a remote communication with the Play store. It holds the property results and status of any available update. The result is a collection of data for the update availability, such as the available app version. The data will then be used to determine whether the API should initiate the update flow.

`AppUpdateInfo` has methods such as

1. `updateAvailability()`. It processes the following:

- `UPDATE_AVAILABLE`

This checks whether an application has a new version available in the play store.

- `DEVELOPER_TRIGGERED_UPDATE_IN_PROGRESS`

This handles a case where a user initiated the update process but closed the application while the update was in progress. `updateAvailability()` will return the state of the update progress.

2. `installStatus()` returns the value of the update milestone. `installStatus()` is an instance of `installStateUpdatedListener` hooked to the `appUpdateManager`. It returns the values of the update status, such as

- `DOWNLOADED`
When the user hits the update, the application will first download the APK. `installStatus()` sets the action that should be done when the APK has been downloaded.

- `INSTALLED` - `installStatus()` sets the action when the newly available update has been installed.

To trigger the update flow, we first check the `updateAvailability()` to determine if there is an update available. If the value returned is equal to `UPDATE_AVAILABLE`,
- Validate whether the update type is allowed with the function `isUpdateTypeAllowed()`.
- Pass `AppUpdateType`, which can be `IMMEDIATE` or `FLEXIBLE`.

Since we have determined whether an update is available or not (the state of the available update), and if the update type is allowed, `appUpdateManager` will return the update status from the `AppUpdateInfo` values, and trigger the update flow with `startUpdateFlowForResult`.

We pass the following parameters to `startUpdateFlowForResult` to start the update flow UI.

- The `appUpdateInfo` that we previously fetched from the play store.
- The updated flow that we want to trigger. Previously passed to `isUpdateTypeAllowed` as `AppUpdateType` that can be `IMMEDIATE` or `FLEXIBLE`.
- The execution context in the current activity.
- A request code to catch `Onactivity` results such as:
        - If a user canceled the update.
        - If the update is OK.
        - If the update flow failed.

With that, we are ready to complete the update with `completeUpdate()`.

### Implementing flexible update flow
This flow does not block a user from interacting with the application. When the update is available, the user downloads the update APK, which occurs in the background. When the download is complete, the user will be prompted to install the newly downloaded application to bring the new updates on board.

Let’s handle the flow.

Before we check this, make sure you implement the update flow on your main launch application page—for example, a log-in activity. This will make sure that a user is alerted as soon they open the application.

#### An instance of `AppUpdateManager`
Create an instance of `AppUpdateManager` within our application execution context.

Go ahead and declare `AppUpdateManager` right above `onCreate`.

```java
private AppUpdateManager appUpdateManager;
```

Below `onCreate`, create the instance.

```java
appUpdateManager = AppUpdateManagerFactory.create(getApplicationContext());
```

#### Check for the update
To implement the update flow, you need first to check whether there is an updated application version in the Google Play store.

Create a function `checkUpdate()` and pass it to the `onCreate` function.

```java
private void checkUpdate() {

   Task<AppUpdateInfo> appUpdateInfoTask = appUpdateManager.getAppUpdateInfo();

   appUpdateInfoTask.addOnSuccessListener(appUpdateInfo -> {
     if (appUpdateInfo.updateAvailability() == UpdateAvailability.UPDATE_AVAILABLE
         && appUpdateInfo.isUpdateTypeAllowed(AppUpdateType.FLEXIBLE)) {
       startUpdateFlow(appUpdateInfo);
     } else if (appUpdateInfo.installStatus() == InstallStatus.DOWNLOADED) {
      popupSnackBarForCompleteUpdate();
     }
   });
}
```

Here is what the function does:

The function communicates with the Play store, to decide if the update is available.

To get update information, an instance of `appUpdateManager` is assigned to a variable `appUpdateInfoTask`.

This requests the update availability for the current application, an intent to start an update flow, and, if applicable, the state of updates currently in progress.

Register a listener to communicate with the `appUpdateInfoTask`. If the conditions check that `UPDATE_AVAILABLE` is `true`, check whether the update is allowed and set the update mode to `FLEXIBLE`. If these conditions are met, we will start the update flow with `startUpdateFlow(appUpdateInfo)` (we will look into that later).

In this case, our update mode is set `Flexible`. The update download happens in the background. We then need to check the install status. If the status shows that the app is `DOWNLOADED`, we need to notify the user that the download has been completed. You can choose to use a `snack bar` or an `alert dialog`. Here I create a function `popupSnackBarForCompleteUpdate()`, and call it when `installStatus()` is equal to `DOWNLOADED`. The UI will react by nudging the user to install the downloaded APK.

***Remember to press alt + enter on a PC and option + enter on a Mac to import the classes after copying and pasting the code blocks into your IDE.***

#### Handling the update flow
Now create the `startUpdateFlow(appUpdateInfo)` function we passed above.

Thus far, we have checked whether the update is available or not. We have also confirmed that the updated platform is supported, and we are ready to request the update.

```java
private void startUpdateFlow(AppUpdateInfo appUpdateInfo) {
   try {
     appUpdateManager.startUpdateFlowForResult(appUpdateInfo, AppUpdateType.FLEXIBLE, this, Flexible.FLEXIBLE_APP_UPDATE_REQ_CODE);
   } catch (IntentSender.SendIntentException e) {
     e.printStackTrace();
   }
}
```

`startUpdateFlowForResult()` will request the update from `AppUpdateInfo` that holds the update information.
To ensure the update flow of the requested update kick-off as expected, we pass some parameters to `startUpdateFlowForResult`; They include

- `appUpdateInfo`
- `AppUpdateType` — the mode of the update flow we want to perform. In this case, we've set that to `FLEXIBLE`.
- `this` — the execution context of the current activity requesting the update.

- `FLEXIBLE_APP_UPDATE_REQ_CODE` — a request code that handles user actions `onActivityResult`. Make sure you declare `FLEXIBLE_APP_UPDATE_REQ_CODE` right above `onCreate`, as shown below.

```java
private static final int FLEXIBLE_APP_UPDATE_REQ_CODE = 123;
```

#### Handling user actions
We need to handle `onActivityResult` to check the user's action, such as instances where the update is canceled or has failed. `FLEXIBLE_APP_UPDATE_REQ_CODE` monitors the update request as implemented in the code below.

```java
public void onActivityResult(int requestCode, int resultCode, Intent data) {
      super.onActivityResult(requestCode, resultCode, data);
      if (requestCode == FLEXIBLE_APP_UPDATE_REQ_CODE) {
          if (resultCode == RESULT_CANCELED) {
              Toast.makeText(getApplicationContext(), "Update canceled by user! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
          } else if (resultCode == RESULT_OK) {
              Toast.makeText(getApplicationContext(),"Update success! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
          } else {
              Toast.makeText(getApplicationContext(), "Update Failed! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
              checkUpdate();
          }
      }
}
```

From the above code, there are several results;

- If a user canceled the installation, you could call the `checkUpdate()` to start the update flow again. You can do a lot of things here, such as maybe returning a dialog to inform the user they need to update the application, or inform them of the new features or the bugs that the update will fix. If the user agrees, you can call `checkUpdate()` to reinitiate the update flow again. You can also decide to close the application with `finish()`.  Otherwise, do nothing and continue with the normal application flow.

- `RESULT_OK` — shows that the update was successful. We can't assign any action to it, as our goal is met. But you can choose to show a message to the user to thank them for taking the time to update the application to the latest version.

- If the update failed, this could be an error such as poor internet connection. Request the update again by calling `checkUpdate()` to restart the update process.

#### Monitoring the update flow

As mentioned earlier, a flexible update flow occurs in the background. We need to monitor the update flow to know when the application download is complete and initiate an install process.

To do this, we need to register a listener to get the status of the update. The listener informs the app of every step in the update process.

Declare the listener.

```java
private InstallStateUpdatedListener installStateUpdatedListener;
```

Add the following code just below the `appUpdateManager` instance created on the `Ocreate` method.

```java
installStateUpdatedListener = state -> {
  if (state.installStatus() == InstallStatus.DOWNLOADED) {
    popupSnackBarForCompleteUpdate();
       } else if (state.installStatus() == InstallStatus.INSTALLED) {
        removeInstallStateUpdateListener();
    } else {
        Toast.makeText(getApplicationContext(), "InstallStateUpdatedListener: state: " + state.installStatus(), Toast.LENGTH_LONG).show();
    }
};
```

The code above tracks down the update status.
- If the status is equal to `DOWNLOADED`, launch a `snack bar` to instruct the user to install the downloaded update.
- If the status is equal to `INSTALLED`, unregister the listener. When the update is installed, there is no need to register a listener anymore.

#### Complete the update flow

Since we now have the download ready, it's time to set the action of the snack bar. Create a function `popupSnackBarForCompleteUpdate()`.

```java
private void popupSnackBarForCompleteUpdate() {
   Snackbar.make(findViewById(android.R.id.content).getRootView(), "New app is ready!", Snackbar.LENGTH_INDEFINITE)
       .setAction("Install", view -> {
         if (appUpdateManager != null) {
           appUpdateManager.completeUpdate();
         }
       })
       .setActionTextColor(getResources().getColor(R.color.purple_500))
       .show();
}
```

This tells the user that the update is APK is downloaded and ready to be installed. When the user clicks Install `appUpdateManager`, automatically install the newly downloaded APK and relaunch it in the foreground.

#### Unregister the listener
We have now achieved the update installation, so we don’t need the `installStateUpdatedListener` anymore.

To do this create a function `removeInstallStateUpdateListener()` and call it when `installStatus()` is equal to `INSTALLED`.

```java
private void removeInstallStateUpdateListener() {
   if (appUpdateManager != null) {
     appUpdateManager.unregisterListener(installStateUpdatedListener);
   }
}
```

This removes the callbacks being triggered when they are no longer required. Unregistering the listener helps to avoid memory leaks.

### Implementing immediate updates
When the `AppUpdateType` is set to `IMMEDIATE`, `appUpdateManager`  initiates a blocking UI, which blocks the user from interacting with the application until the update is complete.

Immediate updates are similar to the flow of events discussed while implementing the flexible mode. To avoid repeating the detailed explanation we have done above, we will state the update flow that implements an immediate update.

#### Create an instance of `appUpdateManager`

```java
appUpdateManager = AppUpdateManagerFactory.create(getApplicationContext());
```

#### Check for the update
Create a `checkUpdate()` function and call it the `onCreate`.

```java
private void checkUpdate() {

   Task<AppUpdateInfo> appUpdateInfoTask = appUpdateManager.getAppUpdateInfo();

   appUpdateInfoTask.addOnSuccessListener(appUpdateInfo -> {
     if (appUpdateInfo.updateAvailability() == UpdateAvailability.UPDATE_AVAILABLE
         && appUpdateInfo.isUpdateTypeAllowed(AppUpdateType.IMMEDIATE)) {
       startUpdateFlow(appUpdateInfo);
     } else if (appUpdateInfo.updateAvailability() == UpdateAvailability.DEVELOPER_TRIGGERED_UPDATE_IN_PROGRESS){
       startUpdateFlow(appUpdateInfo);
     }
   });
 }
```

The app will communicate with the play store and check whether the update is available.

If the update is available, check whether the update type is allowed. In this case, we are checking if the `IMMEDIATE` mode is allowed to start the update flow. If yes, we call `startUpdateFlow(appUpdateInfo)`.

If the user initiated the update and closed the app before the process was over, set the `UpdateAvailability` to `DEVELOPER_TRIGGERED_UPDATE_IN_PROGRESS` so that the user can resume the update process by initiating `startUpdateFlow(appUpdateInfo)` from where the user left before closing the application.

#### Handling the update flow

```java
private void startUpdateFlow(AppUpdateInfo appUpdateInfo) {
   try {
     appUpdateManager.startUpdateFlowForResult(appUpdateInfo, AppUpdateType.IMMEDIATE, this, Immediate.IMMEDIATE_APP_UPDATE_REQ_CODE);
   } catch (IntentSender.SendIntentException e) {
     e.printStackTrace();
   }
 }
```

Initiate the update flow and set the `AppUpdateType` to `IMMEDIATE` in the current activity context. Again, remember to pass a result code that will process any user's decision, such as canceling the update.

#### Handling user action

```java
@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (requestCode == IMMEDIATE_APP_UPDATE_REQ_CODE) {
        if (resultCode == RESULT_CANCELED) {
            Toast.makeText(getApplicationContext(), "Update canceled by user! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
        } else if (resultCode == RESULT_OK) {
            Toast.makeText(getApplicationContext(), "Update success! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
        } else {
            Toast.makeText(getApplicationContext(), "Update Failed! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
            checkUpdate();
        }
    }
}
```

`OnActivityResult` handles the action a user takes when prompted to install an update. This is comprised of three main actions, including:

- `RESULT_CANCELED` — when a user cancels an update, you may choose an action that follows that decision, such as `checkUpdate()` to force the application to restart the update. Or call `finish()` to close the application whenever the user cancels an update.
- `RESULT_OK` — shows that a user has accepted the update to be installed.
- `RESULT_IN_APP_UPDATE_FAILED` — If an update fails with an error, you would want the app to reinitiate the update flow again. For that reason, call `checkUpdate()`, and the update flow will start again.

### Testing
Testing an in-app update is not straightforward. It can be a little tricky to perform a test.

#### What you need
- A Google Play Console account
- An application already published in the Google Play store

#### How to test
1. Generate a signed app bundle/APK. Note the APK signing key, and the `applicationId` should be the same as the already published application.
2. Share the generated APK with a tester. To do that, select the published application in the Google console, navigate to [`Internal App Sharing`](https://play.google.com/console/internal-app-sharing), and upload the generated APK there.
2. Copy the upload's shareable link and share it with a tester. In this case, a tester should be an Android mobile phone.
3. Open the shared link on the phone's browser. You will be prompted to open with the Play store. Do so. This will launch a Play store screen.
4. Download the app and wait for the installation to complete.
5. Once done, generate another sighed app bundle/APK. This time change `versionCode` and `versionName` in your `app.gradle` file to a higher version. If, for example, in the first generated APK, the values were;

```java
versionCode 1
versionName "1"
```

Change these values to a higher version. For example:

```java
versionCode 2
versionName "1.1"
```

To be sure that the update will take place, on your `activity.xml` replace ` android:text="Hello World!"` with `android:text="Congratulations, you now have the newest version of this app installed."`.

6. Once you have generated the app bundle/APK, head to `App Internal Sharing` and upload it.

7. Again copy the shareable link generated by this upload and open it with the tester. When the link launches on the Google Play store, you will get an update button, **don’t click update**.

8. Close the Google Play store and open the application we installed earlier. This will launch an update UI that will be prompt you to update the application. The UI may differ depending on your update type (either flexible or immediate).

### Code setup and output

#### Flexible

```java
public class Flexible extends AppCompatActivity {
    private AppUpdateManager appUpdateManager;
    private InstallStateUpdatedListener installStateUpdatedListener;
    private static final int FLEXIBLE_APP_UPDATE_REQ_CODE = 123;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_update_f);
        appUpdateManager = AppUpdateManagerFactory.create(getApplicationContext());
        installStateUpdatedListener = state -> {
            if (state.installStatus() == InstallStatus.DOWNLOADED) {
                popupSnackBarForCompleteUpdate();
            } else if (state.installStatus() == InstallStatus.INSTALLED) {
                removeInstallStateUpdateListener();
            } else {
                Toast.makeText(getApplicationContext(), "InstallStateUpdatedListener: state: " + state.installStatus(), Toast.LENGTH_LONG).show();
            }
        };
        appUpdateManager.registerListener(installStateUpdatedListener);
        checkUpdate();
    }

    private void checkUpdate() {

        Task<AppUpdateInfo> appUpdateInfoTask = appUpdateManager.getAppUpdateInfo();

        appUpdateInfoTask.addOnSuccessListener(appUpdateInfo -> {
            if (appUpdateInfo.updateAvailability() == UpdateAvailability.UPDATE_AVAILABLE
                    && appUpdateInfo.isUpdateTypeAllowed(AppUpdateType.FLEXIBLE)) {
                startUpdateFlow(appUpdateInfo);
            } else if (appUpdateInfo.installStatus() == InstallStatus.DOWNLOADED) {
                popupSnackBarForCompleteUpdate();
            }
        });
    }

    private void startUpdateFlow(AppUpdateInfo appUpdateInfo) {
        try {
            appUpdateManager.startUpdateFlowForResult(appUpdateInfo, AppUpdateType.FLEXIBLE, this, Flexible.FLEXIBLE_APP_UPDATE_REQ_CODE);
        } catch (IntentSender.SendIntentException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == FLEXIBLE_APP_UPDATE_REQ_CODE) {
            if (resultCode == RESULT_CANCELED) {
                Toast.makeText(getApplicationContext(), "Update canceled by user! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
            } else if (resultCode == RESULT_OK) {
                Toast.makeText(getApplicationContext(),"Update success! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
            } else {
                Toast.makeText(getApplicationContext(), "Update Failed! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
                checkUpdate();
            }
        }
    }

    private void popupSnackBarForCompleteUpdate() {
        Snackbar.make(findViewById(android.R.id.content).getRootView(), "New app is ready!", Snackbar.LENGTH_INDEFINITE)

                .setAction("Install", view -> {
                    if (appUpdateManager != null) {
                        appUpdateManager.completeUpdate();
                    }
                })
                .setActionTextColor(getResources().getColor(R.color.purple_500))
                .show();
    }

    private void removeInstallStateUpdateListener() {
        if (appUpdateManager != null) {
            appUpdateManager.unregisterListener(installStateUpdatedListener);
        }
    }

    @Override
    protected void onStop() {
        super.onStop();
        removeInstallStateUpdateListener();
    }
}
```

![image](/engineering-education/android-application-in-app-update-using-android-studio/flexible.png)

#### Immediate

```java
public class Immediate extends AppCompatActivity {

    private AppUpdateManager appUpdateManager;
    private static final int IMMEDIATE_APP_UPDATE_REQ_CODE = 124;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_update_f);
        appUpdateManager = AppUpdateManagerFactory.create(getApplicationContext());
        checkUpdate();
    }

    private void checkUpdate() {

        Task<AppUpdateInfo> appUpdateInfoTask = appUpdateManager.getAppUpdateInfo();

        appUpdateInfoTask.addOnSuccessListener(appUpdateInfo -> {
            if (appUpdateInfo.updateAvailability() == UpdateAvailability.UPDATE_AVAILABLE
                    && appUpdateInfo.isUpdateTypeAllowed(AppUpdateType.IMMEDIATE)) {
                startUpdateFlow(appUpdateInfo);
            } else if  (appUpdateInfo.updateAvailability() == UpdateAvailability.DEVELOPER_TRIGGERED_UPDATE_IN_PROGRESS){
                startUpdateFlow(appUpdateInfo);
            }
        });
    }

    private void startUpdateFlow(AppUpdateInfo appUpdateInfo) {
        try {
            appUpdateManager.startUpdateFlowForResult(appUpdateInfo, AppUpdateType.IMMEDIATE, this, Immediate.IMMEDIATE_APP_UPDATE_REQ_CODE);
        } catch (IntentSender.SendIntentException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == IMMEDIATE_APP_UPDATE_REQ_CODE) {
            if (resultCode == RESULT_CANCELED) {
                Toast.makeText(getApplicationContext(), "Update canceled by user! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
            } else if (resultCode == RESULT_OK) {
                Toast.makeText(getApplicationContext(), "Update success! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
            } else {
                Toast.makeText(getApplicationContext(), "Update Failed! Result Code: " + resultCode, Toast.LENGTH_LONG).show();
                checkUpdate();
            }
        }
    }
}
```

![image](/engineering-education/android-application-in-app-update-using-android-studio/immediate.png)

I hope this guide helps you implement in-app updates, both immediate and flexible,  within your application context.
