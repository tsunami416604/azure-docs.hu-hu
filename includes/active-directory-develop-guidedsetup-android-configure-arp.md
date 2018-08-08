## <a name="add-the-applications-registration-information-to-your-app"></a>Az alkalmazás regisztrációs adatok hozzáadása az alkalmazáshoz

Ebben a lépésben meg kell az ügyfél-azonosító hozzáadása a projekthez.

1.  Nyissa meg `MainActivity` (alatt `app`  >  `java`  >  *`{host}.{namespace}`*)
2.  Cserélje le a sort, kezdve `final static String CLIENT_ID` együtt:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Nyissa meg: `app` > `manifests` > `AndroidManifest.xml`
4. Adja hozzá a következő tevékenységek `manifest\application` csomópont. A regisztráció egy `BrowserTabActivity` , hogy az operációs rendszer, a hitelesítés elvégzése után az alkalmazás folytatásához:

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

