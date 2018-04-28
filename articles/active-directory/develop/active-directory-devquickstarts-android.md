---
title: Ismerkedés az Azure AD Android |} Microsoft Docs
description: Hogyan hozhat létre egy Android-alkalmazás, amely az Azure AD bejelentkezési és a hívások Azure AD számára az API-k használatával OAuth2.0 védett.
services: active-directory
documentationcenter: android
author: danieldobalian
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/30/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 25a908c542bf8fdd8008841a1865cdfb40d847fc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-android-getting-started"></a>Ismerkedés az Azure AD Android
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

Ha az asztali alkalmazások, Azure Active Directory (Azure AD) segítségével egyszerű és magától értetődő, hogy a felhasználók hitelesítése a helyszíni Active Directory-fiókok használatával. Emellett lehetővé teszi az alkalmazás minden webes API-t az Azure AD, például az Office 365 API-k vagy az Azure API által védett biztonságosan felhasználását.

Android-ügyfelek, amelyek a védett erőforrások eléréséhez szükséges az Azure AD az Active Directory Authentication Library (ADAL) biztosít. ADAL kizárólagos célja megkönnyíti a hozzáférési jogkivonatok lekérésére, az alkalmazás. Annak bemutatásához, hogy milyen egyszerűen, azt fogja Android feladatlista alkalmazás létrehozásához, amely:

* Lekérdezi hozzáférési jogkivonatok hívásakor egy tennivalók listája API használatával [OAuth 2.0 hitelesítési protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
* Lekérdezi a felhasználó tennivalók listájára.
* Felhasználók jeleket.

A kezdéshez van szüksége, amelyben felhasználók létrehozása és egy alkalmazás regisztrálása az Azure AD-bérlő. Ha még nem rendelkezik a bérlő [beszerzéséről egy](active-directory-howto-tenant.md).

## <a name="step-1-download-and-run-the-nodejs-rest-api-todo-sample-server"></a>1. lépés: Töltse le és futtassa a Node.js REST API TODO minta kiszolgáló
A Node.js REST API TODO minta kifejezetten a meglévő mintát eredményez, amely a single-bérlő tennivaló REST API létrehozása az Azure AD dolgozhat írása. Ennek előfeltétele a gyors üzembe helyezését.

Beállítására kapcsolatos információkért lásd: a meglévő minták [Azure Active Directory minta REST API szolgáltatás a Node.js](active-directory-devquickstarts-webapi-nodejs.md).


## <a name="step-2-register-your-web-api-with-your-azure-ad-tenant"></a>2. lépés: A webes API regisztrálása az Azure AD-bérlő
Az Active Directory támogatja a két típusú alkalmazások hozzáadása:

- Webes API-t szolgáltatást kínál a felhasználók számára
- (A webhely vagy az eszközön futó) alkalmazások, azokat elérő webes API-khoz

Ebben a lépésben a webes API-t, hogy ez a minta tesztelési helyben fut éppen regisztrálása. A webes API-k általában egy REST-szolgáltatást, amely az alkalmazások eléréséhez használni kívánt funkciót kínál. Az Azure AD segítségével biztosíthatja a tetszőleges végpontot.

Azt még feltéve, hogy van-e regisztrálása a Teendőlista REST API-t korábban hivatkozott. Azonban ez a webes API-k segítségével védheti az Azure Active Directory kívánt működik.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiókját. Az a **Directory** menüben válassza ki az Azure AD-bérlőt, ahová az alkalmazás regisztrálásához.
3. Kattintson a **minden szolgáltatás** a bal oldali ablaktáblán, és válassza a **Azure Active Directory**.
4. Kattintson a **App regisztrációk**, majd válassza ki **Hozzáadás**.
5. Adjon egy rövid nevet az alkalmazáshoz (például **TodoListService**) elemre, jelölje be **webalkalmazás és/vagy webes API**, és kattintson a **következő**.
6. A bejelentkezési URL-címhez adja meg a minta az alap URL-címet. Alapértelmezés szerint ez a `https://localhost:8080`.
7. Kattintson a **OK** a regisztráció befejezéséhez.
8. Miközben továbbra is az Azure-portálon, nyissa meg az alkalmazás oldalát, keresse meg az alkalmazás azonosító értéket, és másolja. Ezt később szüksége az alkalmazás konfigurálásakor.
9. Az a **beállítások** -> **tulajdonságok** lapon, a app ID URI frissítése – adja meg `https://<your_tenant_name>/TodoListService`. Cserélje le `<your_tenant_name>` az Azure AD-bérlő nevét.

## <a name="step-3-register-the-sample-android-native-client-application"></a>3. lépés: A minta Android Native Client alkalmazás regisztrálása
Ez a példa regisztrálnia kell a webalkalmazást. Ez lehetővé teszi az alkalmazás a most regisztrált webes API-k folytatott kommunikációhoz. Az Azure AD utasíthatja el lehetővé teszik az alkalmazás kérni a bejelentkezéshez, kivéve, ha regisztrálva van. A biztonsági modell részét képező.

Azt még feltéve, hogy van-e regisztrálása korábban hivatkozott mintaalkalmazást. De bármely alkalmazás, amely kidolgozása Ez az eljárás használható.

> [!NOTE]
> Először talán miért kívánja menteni egy alkalmazás és a webes API-k egy bérlő. Mivel előfordulhat, hogy Ön rendelkezik kitalál, egy alkalmazás olyan külső API-bérlőhöz egy másik Azure AD-ben regisztrált hozzáférő hozhat létre. Ha így tesz, az ügyfelek az API-nak az alkalmazás használatához beleegyezését kéri. Az IOS rendszerhez készült Active Directory Authentication Library gondoskodik a hozzájárulásukat adják meg. Összetettebb funkciók megismeréséhez azt láthatja, hogy ez az egyik fontos része a munka Azure és az Office, valamint az egyéb szolgáltató a Microsoft APIs programcsomag eléréséhez szükséges. Most mert mind a webes API-t, és ugyanannak a bérlőnek, az alkalmazást regisztrálni nem jelenik meg semmilyen beleegyezést kér. Általában ez a helyzet, ha az alkalmazás csak a saját vállalati használatára.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A felső eszköztáron kattintson a fiókját. Az a **Directory** menüben válassza ki az Azure AD-bérlőt, ahová az alkalmazás regisztrálásához.
3. Kattintson a **minden szolgáltatás** a bal oldali ablaktáblán, és válassza a **Azure Active Directory**.
4. Kattintson a **App regisztrációk**, majd válassza ki **Hozzáadás**.
5. Adjon egy rövid nevet az alkalmazáshoz (például **TodoListClient-Android**) elemre, jelölje be **natív ügyfélalkalmazás**, és kattintson a **következő**.
6. Az átirányítási URI-t, írja be `http://TodoListClient`. Kattintson a **Befejezés** gombra.
7. Az alkalmazás lapon keresse meg az alkalmazás azonosító értéket, és másolja azt. Ezt később szüksége az alkalmazás konfigurálásakor.
8. Az a **beállítások** lapon jelölje be **szükséges engedélyek** válassza **hozzáadása**.  Keresse meg és jelölje ki a TodoListService, vegye fel a **hozzáférés TodoListService** engedélyt a **delegált engedélyek**, és kattintson a **végzett**.

A Maven build, használhatja a legfelső szinten pom.xml:

1. A tárház klónozása egy olyan könyvtárba, az Ön által választott:

  `$ git clone https://github.com/Azure-Samples/active-directory-android.git`  
2. Kövesse a [a Maven környezet beállítása Androidhoz készült előfeltételei](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Maven).
3. Az SDK 19 emulátor beállítása.
4. Nyissa meg a gyökérmappájába, ahol a tárházban klónozott.
5. Futtassa ezt a parancsot: `mvn clean install`
6. Módosítsa a könyvtárat arra a gyors üzembe helyezési minta: `cd samples\hello`
7. Futtassa ezt a parancsot: `mvn android:deploy android:run`

   Meg kell jelennie az alkalmazás elindítása.
8. Adja meg a teszt felhasználói adatokkal.

JAR csomagok nyújtanak a AAR csomag mellett.

## <a name="step-4-download-the-android-adal-and-add-it-to-your-eclipse-workspace"></a>4. lépés: Töltse le az Android ADAL, és adja hozzá az Eclipse-munkaterület
Hajtottunk, könnyen használható adal-t használni az Android-projekt több lehetőség közül választhat:

* A forráskód segítségével ezt a szalagtárat importálása eclipse-ben és a hivatkozás az alkalmazáshoz.
* Android Studio használata, AAR csomag formátumot használja, és a bináris fájlok hivatkozik.

### <a name="option-1-source-zip"></a>1. lehetőség: Forrás Zip
Letöltheti a forráskódot, kattintson a **töltse le a ZIP-** a lap jobb oldalán. Illetve [töltse le a Githubról](https://github.com/AzureAD/azure-activedirectory-library-for-android/releases).

### <a name="option-2-source-via-git"></a>2. lehetőség: Forrás Git keresztül
Ahhoz, hogy az SDK segítségével Git forráskódját, írja be:

    git clone https://github.com/AzureAD/azure-activedirectory-library-for-android.git
    cd ./azure-activedirectory-library-for-android/src

### <a name="option-3-binaries-via-gradle"></a>3. lehetőség: Bináris Gradle keresztül
A bináris fájlok lekérheti a Maven központi tárházban. Az alábbiak szerint a AAR csomagot is tartalmazza a projekt az Android Studio:

```gradle
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
}
```

### <a name="option-4-aar-via-maven"></a>4. lehetőség: AAR Maven keresztül
A beépülő modul M2Eclipse használata, a függőség adhat meg a pom.xml fájlt:

```xml
<dependency>
    <groupId>com.microsoft.aad</groupId>
    <artifactId>adal</artifactId>
    <version>1.1.1</version>
    <type>aar</type>
</dependency>
```


### <a name="option-5-jar-package-inside-the-libs-folder"></a>5. lehetőség: JAR csomag az függvénytárak mappában
A JAR-fájlra beszerezni a Maven-tárház, és helyezze be a **függvénytárak** a projekt mappájára. Meg kell másolnia a szükséges erőforrások a projekthez, valamint a JAR-csomagok nem tartalmazza azokat.

## <a name="step-5-add-references-to-android-adal-to-your-project"></a>5. lépés: Az Android ADAL mutató hivatkozások hozzáadása a projekthez
1. Vegye fel a projektbe egy hivatkozást, és adja meg azt az Android tárként. Ha bizonytalan ennek módjáról, kaphat további információt a [Android Studio hely](http://developer.android.com/tools/projects/projects-eclipse.html).
2. Adja hozzá a projekt függőség a projektbeállításokat a hibakereséshez.
3. A projekt AndroidManifest.xml fájl frissítése:

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <application
            android:allowBackup="true"
            android:debuggable="true"
            android:icon="@drawable/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >

            <activity
                android:name="com.microsoft.aad.adal.AuthenticationActivity"
                android:label="@string/title_login_hello_app" >
            </activity>
            ....
        <application/>

4. A fő tevékenységnél AuthenticationContext példányának létrehozása. A hívás részleteit túlmutat a jelen témakör, de remek kezdőpont kaphat megnézi a [Android Native Client minta](https://github.com/AzureAD/azure-activedirectory-library-for-android). A következő példában SharedPreferences az alapértelmezett gyorsítótár, továbbá hatóság formájában `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`:

    `mContext = new AuthenticationContext(MainActivity.this, authority, true); // mContext is a field in your activity`

5. Másolja a kódblokk AuthenticationActivity végén kezelni, a felhasználó megadja hitelesítő adatait, és megkapja az engedélyezési kód után:

        @Override
         protected void onActivityResult(int requestCode, int resultCode, Intent data) {
             super.onActivityResult(requestCode, resultCode, data);
             if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
             }
         }

6. Kérje meg a jogkivonat, definiálni kell egy visszahívási:

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {

            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    textViewStatus.setText("Cancelled");
                    Log.d(TAG, "Cancelled");
                } else {
                    textViewStatus.setText("Authentication error:" + exc.getMessage());
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                mResult = result;

                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    textViewStatus.setText("Token is empty");
                    Log.d(TAG, "Token is empty");
                } else {
                    // request is successful
                    Log.d(TAG, "Status:" + result.getStatus() + " Expired:"
                            + result.getExpiresOn().toString());
                    textViewStatus.setText(PASSED);
                }
            }
        };

7. Végül kérje meg a jogkivonat, hogy a visszahívás használatával:

    `mContext.acquireToken(MainActivity.this, resource, clientId, redirect, user_loginhint, PromptBehavior.Auto, "",
                   callback);`

A paraméterek leírását itt található:

* *erőforrás* szükséges, azonban az erőforrás elérésére tett kísérlet.
* *ClientID* szükség, és az Azure AD származik.
* *RedirectUri* nincs szükség a acquireToken hívásához meg kell adni. Állíthat be, mint a csomag neve.
* *PromptBehavior* kérje meg a hitelesítő adatokat hagyja ki a gyorsítótárat, és a cookie-k segítségével.
* *a visszahívási* után az engedélyezési kód cseréje a jogkivonat neve. AuthenticationResult, amelynek hozzáférési jogkivonat objektum rendelkezik, lejárt, és a lexikális elem adatainak azonosító.
* *acquireTokenSilent* nem kötelező megadni. Hívása akkor leíró gyorsítótárazás és a token frissítése. A Sync szolgáltatás verzióját is tartalmazza. Elfogadja a *userId* paraméterként.

        mContext.acquireTokenSilent(resource, clientid, userId, callback );

Ez a forgatókönyv segítségével kell mi sikeresen integrálni kell az Azure Active Directoryban. További példák a, keresse fel a AzureADSamples / GitHub tárházából.

## <a name="important-information"></a>Fontos információk

### <a name="broker"></a>Broker
Az Intune vállalati portál vagy a Microsoft Authenticator alkalmazást a broker összetevő biztosít. A fiók AccountManager jön létre. A fiók típusa nem "com.microsoft.workaccount." AccountManager lehetővé teszi, hogy csak egyetlen egyszeri bejelentkezési fiók. A felhasználó számára az egyszeri bejelentkezési cookie létrehozza az alkalmazások közül legalább egy eszköz challenge befejezése után.

További információt a broker segítségével konfigurálása kivételt a [broker wikicikket](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Broker). 

### <a name="authority-url-and-ad-fs"></a>Szolgáltató URL-címe és az AD FS
Active Directory összevonási szolgáltatások (AD FS) értéke nem értelmezhető éles STS, ezért meg kell példány felderítés kapcsolni, és hamis át, a AuthenticationContext konstruktor.

A szolgáltató URL-címe van szüksége az STS-példány és egy [bérlő neve](https://login.microsoftonline.com/yourtenant.onmicrosoft.com).

### <a name="querying-cache-items"></a>Gyorsítótár elemek lekérdezése
Adal-t tartalmaz néhány egyszerű gyorsítótár alapértelmezett gyorsítótár SharedPreferences a lekérdezési funkciók. A jelenlegi gyorsítótár letölthető AuthenticationContext használatával:

    ITokenCacheStore cache = mContext.getCache();

A gyorsítótár-megvalósítással, ha szeretné testre szabni, azt is megadhatja.

    mContext = new AuthenticationContext(MainActivity.this, authority, true, yourCache);

### <a name="prompt-behavior"></a>Parancssor viselkedése
Adal-t Itt adhatja meg a parancssor viselkedése. PromptBehavior.Auto megjelenik a felhasználói felület, ha a frissítési token érvénytelen, és felhasználói hitelesítő adatok szükségesek. PromptBehavior.Always a rendszer kihagyja a gyorsítótár-használati és mindig jelenjen meg a felhasználói felület.

### <a name="silent-token-request-from-cache-and-refresh"></a>A gyorsítótár és a frissítési csendes jogkivonatkérelem
Beavatkozás nélküli kérelmek nem használja a felhasználói felület előugró, és nem igényli a tevékenységet. Vissza a jogkivonatot a gyorsítótárból érhető el. Ha a jogkivonat érvényessége lejárt, ez a módszer próbálja frissíti. Ha a frissítési jogkivonat lejárt vagy nem sikerült, AuthenticationException adja vissza.

    Future<AuthenticationResult> result = mContext.acquireTokenSilent(resource, clientid, userId, callback );

Végezhet szinkronizálást is ez a módszer használatával. Állítsa be a visszahívási null, vagy acquireTokenSilentSync használja.

### <a name="diagnostics"></a>Diagnosztika
Az elsődleges információforrások a problémák diagnosztizálása az alábbiak:

* Kivételek
* Logs
* Hálózati nyomkövetés

Ne feledje, hogy korrelációs azonosító központi helyet foglalnak el a diagnosztika a könyvtárban. A korrelációs állíthatja be az egyéb műveletek során a kérelem egy kérelem alapon, ha azt szeretné, hogy az adal-t összefüggéseket azonosítók. Ha nem állít egy korrelációs azonosító, a ADAL véletlenszerű egy hoz létre. Az összes üzenetek naplózása és hálózati hívások majd kell jelölni a korrelációs azonosítót. Az önállóan létrehozott azonosítója módosítások minden kérelemnél meg.

#### <a name="errors--exceptions"></a>Hibák & kivételek
A felsoroltakat az első diagnosztika. Próbálja meg hasznos hibaüzenetek jelennek meg. Ha talál, amely nem lehet hasznos, adjon fájlt az kapcsolatos problémát, és ossza meg velünk. Eszköz információkat, például a modell és SDK számát tartalmazza.

További információt milyen hibák az alkalmazás kezelje, kivételt a [hiba történt az ajánlott eljárások kezelése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-adal-error-handling). 

#### <a name="logs"></a>Logs
Beállíthatja, hogy a szalagtár készítése a naplózási üzenetek problémák diagnosztizálásához használható. Naplózás konfigurálása egy visszahívást, amelyet az adal-t használja kéz ki a naplóüzenetekben hozza létre a következő hívással konfigurálásához.

Kapcsolja be a naplózást, kivételt a [wikicikket naplózás](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Logging).

### <a name="session-cookies-in-webview"></a>Webes nézet munkamenet cookie-k
Android webes nézet nem törli a munkamenetek cookie-jait, az alkalmazás bezárása után. A mintakód használatával, amely kezelheti:

    CookieSyncManager.createInstance(getApplicationContext());
    CookieManager cookieManager = CookieManager.getInstance();
    cookieManager.removeSessionCookie();
    CookieSyncManager.getInstance().sync();

A cookie-k kapcsolatos részletekért lásd: a [CookieSyncManager az információt az Android](http://developer.android.com/reference/android/webkit/CookieSyncManager.html).

### <a name="ntlm-dialog-box"></a>NTLM párbeszédpanel
1.1.0-ás ADAL-verziót támogatja az NTLM párbeszédpanel, amely a WebViewClient onReceivedHttpAuthRequest esemény feldolgozása. Az elrendezés és a párbeszédpanel karakterláncok személyre is szabhatja.

### <a name="cross-app-sso"></a>Alkalmazások közötti SSO
Ismerje meg, [az Android alkalmazások közötti SSO engedélyezése az ADAL használatával](active-directory-sso-android.md).  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
