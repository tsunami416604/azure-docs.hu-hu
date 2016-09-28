
###A jegyzékfájl frissítése az értesítések engedélyezéséhez

Másolja át az alkalmazáson belüli alábbi üzenetküldési erőforrásokat a Manifest.xml fájlba, az `<application>` és `</application>` címkék közé.

        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
        </receiver>

###Az értesítések ikonjának megadása

Illessze be a következő XML-részletet a Manifest.xml fájlba, az `<application>` és `</application>` címkék közé.

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

Ez meghatározza a rendszerben, valamint az alkalmazásbeli értesítésekben megjelenő ikont. A használata alkalmazásbeli értesítések esetén nem, rendszerértesítések esetén viszont kötelező. Az Android rendszer elutasítja az érvénytelen ikonnal rendelkező rendszerértesítéseket.

Győződjön meg arról, hogy olyan ikont használ, amely megtalálható a **drawable** (rajzolható) mappák egyikében (pl. ``engagement_close.png``). A **mipmap** mappa nem támogatott.

>[AZURE.NOTE] Ne használja az **indító**ikont. Az indítóikon más felbontással rendelkezik, és általában a nem támogatott mipmap-mappákban található.

Valós alkalmazások esetén használjon olyan ikont, amely az [Android tervezési útmutatója](http://developer.android.com/design/patterns/notifications.html) szerint használható értesítésekhez.

>[AZURE.TIP] Az [alábbi példák](https://www.google.com/design/icons) megtekintésével meggyőződhet arról, hogy a helyes ikonméretet használja-e.
Görgessen le a **Notification** (Értesítés) szakaszhoz, kattintson egy ikonra, majd kattintson a `PNGS` gombra a rajzolható ikonkészlet letöltéséhez. Itt láthatja, hogy melyik rajzolható mappa és melyik méret használható az ikon egyes verzióihoz.

###GCM leküldéses értesítések fogadásának engedélyezése az alkalmazásban

1. Illessze be a következőt a Manifest.xml fájlba, az `<application>` és `</application>` címkék közé, miután kicserélte a Google Play-konzolból beszerzett `project number` számot. Az \n rész szándékosan került bele, ezért győződjön meg arról, hogy odaírja a projektszám végére.

        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. Illessze be az alábbi kódot a Manifest.xml fájlba, az `<application>` és `</application>` címkék közé. Cserélje ki az alábbi csomagnevet: <Your package name>.

        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
        android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<Your package name>" />
            </intent-filter>
        </receiver>

3. Adja hozzá a kiemelt utolsó engedélykészletet az `<application>` címke elé. Cserélje ki a `<Your package name>` részt az alkalmazás tényleges csomagnevére.

        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />






<!--HONumber=Sep16_HO4-->


