1. 按一下 Android Studio 工具列上的圖示，或按一下 [開啟 Android SDK Manager **工具** ]-> [ **Android** ]-> [ **SDK Manager** ] 功能表上。 尋找專案中使用之 Android SDK 的目標版本，開啟它，然後選擇 **Google APIs**, ，如果尚未安裝。

2. 向下捲動至 **Extras**, ，加以展開，然後選擇 [ **Google Play 服務**, ，如下所示。 按一下 [ **安裝封裝**。 記錄以下步驟使用的 SDK 路徑。 

    ![](./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png)


3. 開啟 **build.gradle** 應用程式目錄中的檔案。

    ![](./media/mobile-services-android-get-started-push/android-studio-push-build-gradle.png)

4. 加入這一行底下 *相依性*: 

        compile 'com.google.android.gms:play-services-base:6.5.87'

5. 在 *defaultConfig*, ，變更 *minSdkVersion* 到 9。
 
6. 按一下 [ **同步處理專案與 Gradle 檔案** 工具列圖示。

7. 開啟 **AndroidManifest.xml** ，並新增到此標記 *應用程式* 標記。

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 





