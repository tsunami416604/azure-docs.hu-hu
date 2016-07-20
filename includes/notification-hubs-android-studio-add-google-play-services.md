1. Nyissa meg az Android SDK Manager alkalmazást az Android Studióban található ikonnal, vagy a **Tools** (Eszközök) -> **Android** -> **SDK Manager** lehetőségre kattintva a menüben. Keresse meg az alkalmazásában használt Android SDK célverzióját, nyissa meg a **Show Package Details** (Csomag részleteinek megtekintésre) elemre való kattintással, és válassza a **Google APIs** (Google API-k) lehetőséget, ha azok nincsenek még telepítve.

2. Kattintson az **SDK Tools** (SDK-eszközök) fülre. Ha még nem telepítette a Google Play szolgáltatást, kattintson a **Google Play Services** (Google Play szolgáltatások) elemre, ahogy az ábra mutatja. Ezután kattintson az **Apply** (Alkalmaz) gombra. 
 
    Jegyezze fel az SDK elérési útját, mert szükség lesz rá egy későbbi lépésben. 

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)


3. Nyissa meg a **build.gradle** fájlt az alkalmazáskönyvtárban.

    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)

4. Adja hozzá ezt a sort a *dependencies* (függőségek) sor alatt: 

        compile 'com.google.android.gms:play-services-gcm:8.4.0'

5. A *defaultConfig* sor alatt módosítsa a *minSdkVersion* tulajdonságot 9-re.
 
6. Kattintson a **Sync Project with Gradle Files** (Projekt szinkronizálása a Gradle-fájlokkal) ikonra az eszköztárban.

7. Nyissa meg az **AndroidManifest.xml** fájlt, és adja ezt a címkét az *application* (alkalmazás) címkéhez.

        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />
 







<!--HONumber=Jun16_HO2-->


