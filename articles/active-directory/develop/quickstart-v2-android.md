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
ms.date: 04/26/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 547eafac8cc1acf2b60416f93804e819a1c549b0
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67702764"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Gyors útmutató: A felhasználók és a Microsoft Graph API hívása Androidos alkalmazásokból

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

A jelen rövid útmutatóban található kódmintán azt mutatjuk be, hogyan tud egy Android-alkalmazás bejelentkezni személyes, munkahelyi vagy iskolai fiókokba, lekérni egy hozzáférési jogkivonatot, és meghívni a Microsoft Graph API-t.

![Ez a rövid útmutató által létrehozott mintaalkalmazás működését mutatja](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Előfeltételek**
> * Android Studio 
> * Android 16 + megadása kötelező 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Express] [1. lehetőség: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuális] [2. lehetőség: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálása
> 1. Nyissa meg az új [az Azure portal - alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) ablaktáblán.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta
>
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://aka.ms/MobileAppReg) lapot.
> 1. Válassza ki **új regisztrációs**.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>      - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `AndroidQuickstart`).
>      - Egyéb konfigurációk kihagyhatja ezen az oldalon. 
>      - Nyomja le az `Register` gombra.
> 1. Kattintson az új alkalmazás a > lépjen a `Authentication`  >  `Add Platform`  >  `Android`.    
>      - Adja meg az Android studio-projektet a csomag nevét. 
>      - Hozzon létre egy aláírás-kivonatoló. Tekintse meg a portálon talál útmutatást.
> 1. Válassza ki `Configure` , és mentse a ***MSAL konfigurációs*** JSON későbbi használatra. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>1\. lépés: Az alkalmazás konfigurálása
> Az ebben a rövid működéséhez kódmintájához hozzá kell átirányítási URI-t a hitelesítési ügynök kompatibilis. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-android/green-check.png) Az alkalmazás már konfigurálva van ezekkel az attribútumokkal

#### <a name="step-2-download-the-project"></a>2\. lépés: Töltse le a projekt

* [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>3\. lépés: A projekt konfigurálása

> [!div renderon="docs"]
> Ha a fenti beállítás 1, akkor kihagyhatja ezeket a lépéseket. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Csomagolja ki és nyissa meg a projektet az Android Studióban.
> 1. Belül **alkalmazás** > **src** > **fő** > **res**  >   **nyers**, nyissa meg **auth_config.json**.
> 1. Szerkesztés **auth_config.json** és cserélje le a JSON az Azure Portalról. Ha inkább manuálisan szeretné hajtsa végre a módosításokat:
>    ```javascript
>    {
>       "client_id" : "Enter_the_Application_Id_Here",
>       "authorization_user_agent" : "DEFAULT",
>       "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>       "authorities" : [
>          {
>             "type": "AAD",
>             "audience": {
>                "type": "Enter_the_Audience_Info_Here",
>                "tenant_id": "Enter_the_Tenant_Info_Here"
>             }
>          }
>       ]
>    }
> 1. Inside **app** > **manifests**, open  **AndroidManifest.xml**.
> 1. Paste the following activity to the **manifest\application** node: 
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> > 1. Az alkalmazás futtatása! 

> [!div renderon="docs"]
> 1. Csomagolja ki és nyissa meg a projektet az Android Studióban.
> 1. Belül **alkalmazás** > **res** > **nyers**, nyissa meg **auth_config.json**.
> 1. Szerkesztés **auth_config.json** és cserélje le a JSON az Azure Portalról. Ha inkább manuálisan szeretné tenni a módosításokat:
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
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Decoded_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Cserélje le `Enter_the_Package_Name` és `Enter_the_Signature_Hash` azokra az értékekre, hogy regisztrált az Azure Portalon. 
> 1. Az alkalmazás futtatása! 

## <a name="more-information"></a>További információ

Az alábbi szakaszok a rövid útmutatóhoz kapcsolódód további információkat tartalmaznak.

### <a name="getting-msal"></a>Az MSAL beolvasása

Az MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) segítségével a felhasználók, és egy API-t a Microsoft identity platform által védett eléréséhez használt jogkivonatokat kérhetnek a könyvtár. Gradle 3.0 + segítségével telepítheti a következő hozzáadásával **Gradle-szkriptek** > **build.gradle (modul: alkalmazás)** alatt **függőségek**:

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.3.+'
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
> |`R.raw.auth_config` | Ez a fájl tartalmazza a konfigurációkat az alkalmazáshoz, beleértve az alkalmazás/ügyfél-azonosító, bejelentkezési célközönség, átirányítási URI-t és számos egyéb testreszabási lehetőségek. |

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Az MSAL a következő két metódust használja a jogkivonatok beszerzéséhez: `acquireToken` és `acquireTokenSilentAsync`

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Interaktív módon jogkivonatának beolvasása

Bizonyos helyzetekben megköveteli a felhasználóktól, együttműködhet a Microsoft identitásplatformjához. Ezekben az esetekben kiválaszthatják a fiókjukat, adja meg a hitelesítő adataik vagy beleegyezik abba az engedélyeket, az alkalmazás kéri, hogy a végfelhasználó is szükség. Például: 

* Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
* Ha a felhasználó alaphelyzetbe állítja a jelszavát, kell adnia a hitelesítő adatait 
* Ha a jóváhagyási visszavonva 
* Ha az alkalmazás explicit módon kell kapnia. 
* Ha az alkalmazás erőforrásokhoz való hozzáférést az első alkalommal
* Ha az MFA vagy más feltételes hozzáférési házirendek szükségesek

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Az elemek magyarázata:||
> |---------|---------|
> | `SCOPES` | Tartalmazza a kért hatóköröket (azaz `{ "user.read" }` Microsoft Graph és `{ "<Application ID URL>/scope" }` egyéni webes API-k esetében) (például `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Visszahívás, amelyre akkor kerül sor, amikor a hitelesítés után visszakerül a vezérlés az alkalmazáshoz |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent: Felhasználói jogkivonat csendes beszerzése

Alkalmazások a felhasználók jelentkezhetnek be minden alkalommal, amikor a jogkivonat kérnek elvégzéséhez nem szükséges. Ha a felhasználó már bejelentkezett, ez a módszer engedélyezi, hogy csendes kérelem jogkivonatokat.

```java
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (!accounts.isEmpty()) {
                sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
            } else {
                /* No accounts */
            }
        }
    });
```

> |Az elemek magyarázata:||
> |---------|---------|
> | `SCOPES` | Tartalmazza a kért hatóköröket (azaz `{ "user.read" }` Microsoft Graph és `{ "<Application ID URL>/scope" }` egyéni webes API-k esetében) (például `api://<Application ID>/access_as_user`) |
> | `getAccounts(...)` | A fiók tokenekhez tartozó csendes kívánt tartalmaz |
> | `getAuthSilentCallback()` | Visszahívás, amelyre akkor kerül sor, amikor a hitelesítés után visszakerül a vezérlés az alkalmazáshoz |

## <a name="next-steps"></a>További lépések

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>A jelen rövid útmutatóban használt alkalmazás létrehozási lépéseinek ismertetése

Próbálja ki az Androidhoz készült oktatóanyagot, amelyben teljes körű, részletes útmutatót talál az alkalmazások és új szolgáltatások létrehozásához, valamint megtalálja ennek a rövid útmutatónak a teljes magyarázatát is.

> [!div class="nextstepaction"]
> [A Graph API meghívása – oktatóanyag Androidhoz](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Androidhoz készült MSAL-kódtár – wiki

További információ az Androidhoz készült MSAL-kódtárról:

> [!div class="nextstepaction"]
> [Androidhoz készült MSAL-kódtár – wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
