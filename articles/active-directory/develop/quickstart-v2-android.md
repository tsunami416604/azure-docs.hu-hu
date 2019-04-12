---
title: A Microsoft identity platform Android rövid útmutató |} Az Azure
description: Ismerje meg, hogy az Android-alkalmazások meghívhat egy API, amely szerint a Microsoft identity platform endpoint hozzáférési jogkivonatok szükséges.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1f174229da565627c0e5791f53031b338880cb3
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59495311"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Gyors útmutató: A felhasználók és a Microsoft Graph API hívása Androidos alkalmazásokból

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

A jelen rövid útmutatóban található kódmintán azt mutatjuk be, hogyan tud egy Android-alkalmazás bejelentkezni személyes, munkahelyi vagy iskolai fiókokba, lekérni egy hozzáférési jogkivonatot, és meghívni a Microsoft Graph API-t.

![Ez a rövid útmutató által létrehozott mintaalkalmazás működését mutatja](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Előfeltételek**
> * Android Studio 3+
> * Android 21 + megadása kötelező 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Express] [1. lehetőség: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuális] [2. lehetőség: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálása
> 1. Nyissa meg az új [az Azure portal - alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) ablaktáblán.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot.
> 1. Válassza ki **új regisztrációs**.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>      - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `Android-Quickstart`).
>      - Nyomja le az `Register` gombra.
> 1. Lépjen a `Authentication`  >  `Redirect URIs`  >  `Suggested Redirect URIs for public clients`, és válassza ki az átirányítási URI-formátum **msal {AppId} :/ / auth**. A módosítás mentéséhez.


> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>1. lépés: Az alkalmazás konfigurálása
> Ahhoz, hogy a rövid útmutatóban szereplő kódminta működjön, hozzá kell adnia egy válasz URL-címet a következő formában: **msal{AppId}://auth** (ahol az {AppId} az alkalmazás azonosítója).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás végrehajtása nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már be van állítva](media/quickstart-v2-android/green-check.png) az alkalmazás ezekkel az attribútumokkal van konfigurálva.

#### <a name="step-2-download-the-project"></a>2. lépés: Töltse le a projekt

* [Az Android Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>3. lépés: A projekt konfigurálása

> [!div renderon="docs"]
> Ha a fenti beállítás 1, akkor kihagyhatja ezeket a lépéseket. Nyissa meg a projekt az Android Studióban, és futtassa az alkalmazást. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Csomagolja ki és nyissa meg a projektet az Android Studióban.
> 1. Belül **alkalmazás** > **res** > **nyers**, nyissa meg **auth_config.json**.
> 1. Szerkesztés **auth_config.json** , és cserélje le a `client_id` és `tenant_id`:
>    ```javascript
>    "client_id" : "Enter_the_Application_Id_Here",
>    "type": "Enter_the_Audience_Info_Here",
>    "tenant_id" : "Enter_the_Tenant_Info_Here"
>    ```
> 1. Belül **alkalmazás** > **jegyzékfájlok**, nyissa meg **AndroidManifest.xml**.
> 1. Adja hozzá az alábbi tevékenységet a **manifest\application** csomóponthoz. Ez a kód lehetővé teszi, hogy a Microsoft visszahívási az alkalmazáshoz:   
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msalEnter_The_Application_Id_Here"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```

> [!div renderon="docs"]
> 1. Csomagolja ki és nyissa meg a projektet az Android Studióban.
> 1. Belül **alkalmazás** > **res** > **nyers**, nyissa meg **auth_config.json**.
> 1. Szerkesztés **auth_config.json** , és cserélje le a `client_id` és `redirect_uri`:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. Belül **alkalmazás** > **jegyzékfájlok**, nyissa meg **AndroidManifest.xml**.
> 1. Adja hozzá az alábbi tevékenységet a **manifest\application** csomóponthoz. Ez a kódrészlet egy **BrowserTabActivity** nevű tevékenységet regisztrál, hogy az operációs rendszer a hitelesítés befejezése után folytathassa az alkalmazás futtatását:
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msal<ENTER_YOUR_APPLICATION_ID>"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Az `<ENTER_THE_APPLICATION_ID_HERE>` sztringet cserélje le az alkalmazás *azonosítójára*. Az *alkalmazásazonosítót* az *áttekintési* oldalon találja.

## <a name="more-information"></a>További információ

Az alábbi szakaszok a rövid útmutatóhoz kapcsolódód további információkat tartalmaznak.

### <a name="msal"></a>MSAL

Az MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) segítségével a felhasználók, és egy API-t a Microsoft identity platform által védett eléréséhez használt jogkivonatokat kérhetnek a könyvtár. A telepítést a Gradle segítségével végezheti el. Ehhez a **Dependencies** (Függőségek) területen hozzá kell adnia a **Gradle Scripts** > **build.gradle (Module: app)** elemhez a következőket:

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```java
import com.microsoft.identity.client.*;
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |Az elemek magyarázata: ||
> |---------|---------|
> |`R.raw.auth_config` | Ez a fájl tartalmazza a konfigurációkat az alkalmazáshoz, beleértve az alkalmazás/ügyfél-azonosító, jelentkezzen be a célközönség és számos egyéb testreszabási lehetőségek. |

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Az MSAL jogkivonatok beszerzéséhez használt két módszer van: `acquireToken` és `acquireTokenSilentAsync`

#### <a name="getting-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Bizonyos helyzetekben szükséges együttműködhet a Microsoft identity platform végpont, mely eredmények egy környezetben. Váltson vagy érvényesíteni a felhasználók hitelesítő adatait a rendszer böngészőben vagy a beleegyezést a felhasználók. Néhány példa:

* Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
* Ha a felhasználóknak újból meg kell adniuk a hitelesítési adataikat, mert lejárt a jelszó
* Amikor az alkalmazás olyan erőforráshoz kér hozzáférést, amelyhez szükséges a felhasználó hozzájárulása
* Ha kétfaktoros hitelesítésre van szükség

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Az elemek magyarázata:||
> |---------|---------|
> | `SCOPES` | Tartalmazza a kért hatóköröket (azaz `{ "user.read" }` Microsoft Graph és `{ "<Application ID URL>/scope" }` egyéni webes API-k esetében) (például `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Visszahívás, amelyre akkor kerül sor, amikor a hitelesítés után visszakerül a vezérlés az alkalmazáshoz |

#### <a name="getting-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

Nem érdemes minden egyes alkalommal megkövetelni a felhasználóktól a hitelesítő adatok érvényesítését, amikor hozzá kell férniük egy erőforráshoz. Általában szerencsésebb, ha a jogkivonatok beszerzéséhez és megújításához nincs szükség felhasználói beavatkozásra. Kezdetben használja az `acquireToken` metódust, majd a védett erőforrásokhoz való hozzáféréshez szükséges jogkivonatok beszerzéséhez az `AcquireTokenSilentAsync` metódust használhatja:

```java
List<IAccount> accounts = sampleApp.getAccounts();
if (sample.size() == 1) {
    sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
} else {
    // No or multiple accounts
}
```

> |Az elemek magyarázata:||
> |---------|---------|
> | `SCOPES` | Tartalmazza a kért hatóköröket (azaz `{ "user.read" }` Microsoft Graph és `{ "<Application ID URL>/scope" }` egyéni webes API-k esetében) (például `api://<Application ID>/access_as_user`) |
> | `accounts.get(0)` | A fiók tokenekhez tartozó csendes kívánt tartalmaz |
> | `getAuthInteractiveCallback` | Visszahívás, amelyre akkor kerül sor, amikor a hitelesítés után visszakerül a vezérlés az alkalmazáshoz |

## <a name="next-steps"></a>További lépések

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>A jelen rövid útmutatóban használt alkalmazás létrehozási lépéseinek ismertetése

Próbálja ki az Androidhoz készült oktatóanyagot, amelyben teljes körű, részletes útmutatót talál az alkalmazások és új szolgáltatások létrehozásához, valamint megtalálja ennek a rövid útmutatónak a teljes magyarázatát is.

> [!div class="nextstepaction"]
> [Hívja a Graph API-t Androidos oktatóanyag](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Androidhoz készült MSAL-kódtár – wiki

További információ az Androidhoz készült MSAL-kódtárról:

> [!div class="nextstepaction"]
> [Androidhoz készült MSAL-kódtár – wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
