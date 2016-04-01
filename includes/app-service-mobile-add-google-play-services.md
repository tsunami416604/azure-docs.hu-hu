1. 按一下 Android Studio 工具列上的圖示，或按一下 [開啟 Android SDK Manager **工具** ]-> [ **Android** ]-> [ **SDK Manager** ] 功能表上。 尋找專案中使用之 Android SDK 的目標版本，開啟它，然後選擇 **Google APIs**, ，如果尚未安裝。

2. 移至 **檔案**, ，**專案結構**。 然後啟用推播通知下 **通知**。

3. 開啟 **AndroidManifest.xml** ，並新增到此標記 *應用程式* 標記。

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 






