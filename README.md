# Auto Launch Android App on Device Boot

This project demonstrates how to automatically launch an Android app when the device boots up. It includes the necessary steps to set up a `BroadcastReceiver` that listens for the `BOOT_COMPLETED` action and starts the main activity of the app. The project also integrates with a Flutter activity and handles necessary permissions and battery optimization settings.

## Features

- Automatically launch app on device boot
- Handle necessary permissions including SYSTEM_ALERT_WINDOW
- Integrate with Flutter activity
- Handle battery optimization settings for consistent behavior

## Prerequisites

- Android Studio
- Flutter SDK (if using Flutter)
- Java Development Kit (JDK)

## Getting Started

1. **Clone the repository:**
   ```sh
   git clone https://github.com/yourusername/auto-launch-on-boot.git
   cd auto-launch-on-boot
Open the project in Android Studio.
Configure Permissions and Receiver:
Ensure the following permissions and receiver are added in your AndroidManifest.xml:
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>

<application
    android:label="boot"
    android:name="${applicationName}"
    android:icon="@mipmap/ic_launcher">
    <activity
        android:name=".MainActivity"
        android:exported="true"
        android:launchMode="singleTop"
        android:theme="@style/LaunchTheme"
        android:configChanges="orientation|keyboardHidden|keyboard|screenSize|smallestScreenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
        android:hardwareAccelerated="true"
        android:windowSoftInputMode="adjustResize">
        <meta-data
          android:name="io.flutter.embedding.android.NormalTheme"
          android:resource="@style/NormalTheme"
          />
        <intent-filter>
            <action android:name="android.intent.action.MAIN"/>
            <category android:name="android.intent.category.LAUNCHER"/>
        </intent-filter>
    </activity>
    <!-- Don't delete the meta-data below.
         This is used by the Flutter tool to generate GeneratedPluginRegistrant.java -->
    <meta-data
        android:name="flutterEmbedding"
        android:value="2" />

    <receiver
        android:enabled="true"
        android:exported="true"
        android:name=".autostart"
        android:permission="android.permission.RECEIVE_BOOT_COMPLETED">

        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
            <action android:name="android.intent.action.QUICKBOOT_POWERON" />
            <category android:name="android.intent.category.DEFAULT"/>
        </intent-filter>

    </receiver>
</application>
Create the BroadcastReceiver:
package com.example.boot;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.util.Log;

public class autostart extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        Log.d("Autostart", "Received BOOT_COMPLETED broadcast");

        if (Intent.ACTION_BOOT_COMPLETED.equals(intent.getAction())) {
            Log.d("Autostart", "Starting MainActivity");
            Intent mIntent = new Intent(context, MainActivity.class);
            mIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            context.startActivity(mIntent);
        }
    }
}

Update MainActivity for Overlay Permission:
package com.example.boot;

import android.content.Intent;
import android.net.Uri;
import android.os.Build;
import android.os.Bundle; // Add this import statement
import android.provider.Settings;
import io.flutter.embedding.android.FlutterActivity;

public class MainActivity extends FlutterActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M && !Settings.canDrawOverlays(getApplicationContext())) {
            Intent intent = new Intent(Settings.ACTION_MANAGE_OVERLAY_PERMISSION);
            Uri uri = Uri.fromParts("package", getPackageName(), null);
            intent.setData(uri);
            startActivityForResult(intent, 7);
        }
    }
}
Handle Battery Optimization:
import android.content.Context;
import android.content.Intent;
import android.net.Uri;
import android.os.Build;
import android.provider.Settings;

public class BatteryOptimizationHelper {

    public static void requestDisableBatteryOptimization(Context context) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            Intent intent = new Intent();
            String packageName = context.getPackageName();
            if (!Settings.canDrawOverlays(context)) {
                intent.setAction(Settings.ACTION_MANAGE_OVERLAY_PERMISSION);
                intent.setData(Uri.parse("package:" + packageName));
                context.startActivity(intent);
            }
        }
    }
}
License
This project is licensed under the MIT License - see the LICENSE file for details.

Contributing
Contributions are welcome! Please open an issue or submit a pull request for any improvements or new features.

Contact
If you have any questions or suggestions, feel free to reach out!
