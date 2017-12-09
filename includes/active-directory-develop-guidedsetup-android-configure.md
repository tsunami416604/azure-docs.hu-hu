
## <a name="create-an-application-express"></a>Hozzon létre egy alkalmazást (Express)
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálra*:
1. Az alkalmazás regisztrálása a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)
2.  Adjon meg egy nevet az alkalmazás és az e-maileket
3.  Győződjön meg arról, hogy az interaktív telepítés beállítást
4.  Az utasítások beszerzéséhez, és illessze be a kódot

### <a name="add-your-application-registration-information-to-your-solution-advanced"></a>Az alkalmazás regisztrációs adatokat hozzáadni a megoldás (speciális)
Most kell regisztrálnia az alkalmazást a *Microsoft alkalmazásregisztrációs portálra*:
1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app) alkalmazás regisztrálása
2. Adjon meg egy nevet az alkalmazás és az e-maileket 
3. Győződjön meg arról, hogy az interaktív telepítés beállítás nincs bejelölve
4. Kattintson a `Add Platform`, majd jelölje be `Native Application` , majd kattintson a Mentés
5.  Nyissa meg `MainActivity` (alatt `app`  >  `java`  >   *`{host}.{namespace}`* )
6.  Cserélje le a *[adja meg itt az alkalmazás azonosítója]* kezdetű sort a `final static String CLIENT_ID` az imént regisztrált alkalmazás azonosítójával:

```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Nyissa meg `AndroidManifest.xml` (alatt `app`  >  `manifests`) adja hozzá a következő tevékenységek `manifest\application` csomópont. Ezzel a `BrowserTabActivity` engedélyezi a hitelesítés befejezése után az alkalmazás folytatja az operációs rendszer:
</li>
</ol>

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
<!-- Workaround for Docs conversion bug -->
<ol start="8">
<li>
Az a `BrowserTabActivity`, cserélje le `[Enter the application Id here]` alkalmazás azonosítóval.
</li>
</ol>
