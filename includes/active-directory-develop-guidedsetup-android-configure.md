
## <a name="register-your-application"></a>Alkalmazás regisztrálása
Regisztrálhatja az alkalmazás két módon, a következő két szakaszokban ismertetett módon.

### <a name="option-1-express-mode"></a>1. lehetőség: Expressz mód
Az alkalmazás a következőképpen gyorsan regisztrálhatja:
1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  Az a **alkalmazásnév** mezőbe írjon be egy nevet az alkalmazásnak.

3. Győződjön meg arról, hogy a **interaktív telepítés** jelölőnégyzet be kiválasztva, majd jelölje ki **létrehozása**.

4. Kövesse az utasításokat az beszerzése az Alkalmazásazonosító, és illessze be a kódját.

### <a name="option-2-advanced-mode"></a>2. lehetőség: Speciális módban
Az alkalmazás regisztrálása és az alkalmazás regisztrációs adatokat hozzáadni a megoldás, tegye a következőket:
1. Ha az alkalmazás már még nem regisztrált, folytassa a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app).
2. Az a **alkalmazásnév** mezőbe írjon be egy nevet az alkalmazásnak. 

3. Győződjön meg arról, hogy a **interaktív telepítés** jelölőnégyzet nincs bejelölve, és jelölje ki **létrehozása**.

4. Válassza ki **hozzáadása Platform**, jelölje be **natív alkalmazás**, majd válassza ki **mentése**.

5. A **app** > **java** > **{állomás}. { névtér}**, nyissa meg `MainActivity`. 

6.  Cserélje le *[adja meg itt az alkalmazás azonosítója]* az alkalmazás azonosítójával, amely az imént regisztrálta a következő sorban:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. A **app** > **jelentkezik**, nyissa meg a *AndroidManifest.xml* fájlt.

8. Az a `manifest\application` csomópont, adjon hozzá a következő tevékenységet. Ez igen regiszterekben egy `BrowserTabActivity` tevékenység, amely lehetővé teszi az alkalmazás folytatja, a hitelesítés után az operációs rendszer:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
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
9. Az a `BrowserTabActivity` csomópont, cserélje le `[Enter the application Id here]` alkalmazás azonosítóval.
