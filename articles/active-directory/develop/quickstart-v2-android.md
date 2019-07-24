---
title: Microsoft Identity platform – Android rövid útmutató | Azure
description: Ismerje meg, hogy az Android-alkalmazások hogyan hívhatnak meg olyan API-t, amely hozzáférési jogkivonatokat igényel a Microsoft Identity platform végpontjának.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c22d93d39f086aaa6e2f103d3becbe9376b49b0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324547"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Gyors útmutató: Bejelentkezés a felhasználókba és a Microsoft Graph API meghívása Android-alkalmazásból

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

A jelen rövid útmutatóban található kódmintán azt mutatjuk be, hogyan tud egy Android-alkalmazás bejelentkezni személyes, munkahelyi vagy iskolai fiókokba, lekérni egy hozzáférési jogkivonatot, és meghívni a Microsoft Graph API-t.

![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Előfeltételek**
> * Android Studio 
> * Android 16 + szükséges 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * Express [1. lehetőség: Regisztráljon és automatikusan konfigurálja az alkalmazást, majd töltse le a kód mintáját](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Kézi [2. lehetőség: Alkalmazás-és kód-minta regisztrálása és manuális konfigurálása](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. lehetőség: Regisztráljon és automatikusan konfigurálja az alkalmazást, majd töltse le a kód mintáját
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálása
> 1. Nyissa meg az új [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. lehetőség: Alkalmazás-és kód-minta regisztrálása és manuális konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://aka.ms/MobileAppReg) oldalára.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>      - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `AndroidQuickstart`).
>      - Ezen a lapon más konfigurációk is kihagyhatók. 
>      - Nyomja meg `Register` a gombot.
> 1. Kattintson az új alkalmazásra > Ugrás a `Authentication`  >   >  `Add Platform` `Android`következőre:.    
>      - Adja meg a csomag nevét az Android Studio-projektből. 
>      - Aláírási kivonat létrehozása Útmutatásért tekintse meg a portált.
> 1. Válassza `Configure` ki és mentse a ***MSAL-konfiguráció*** JSON-t későbbi verzióra. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>1\. lépés: Az alkalmazás konfigurálása
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, hozzá kell adnia egy átirányítási URI-t, amely kompatibilis a hitelesítési közvetítővel. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-android/green-check.png) Az alkalmazás már konfigurálva van ezekkel az attribútumokkal

#### <a name="step-2-download-the-project"></a>2\. lépés: A projekt letöltése

* [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>3\. lépés: A projekt konfigurálása

> [!div renderon="docs"]
> Ha a fenti 1. lehetőséget választotta, akkor kihagyhatja ezeket a lépéseket. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Csomagolja ki és nyissa meg a projektet az Android Studióban.
> 1. Az **app** > **src** Main res RAW, nyissa meg a auth_config. JSON fájlt. >  >  > 
> 1. Szerkessze a **auth_config. JSON** fájlt, és cserélje le a JSON-ra a Azure Portal. Ha ehelyett manuálisan szeretné elvégezni a módosításokat:
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
>    ```
> 
> 1. Az **alkalmazás** > -**jegyzékfájlokon**belül nyissa meg a **AndroidManifest. xml fájlt**.
> 1. Illessze be a következő tevékenységet a **manifest\application** csomópontba: 
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
> 1. Futtassa az alkalmazást! 

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ez a rövid útmutató támogatja a Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> 1. Csomagolja ki és nyissa meg a projektet az Android Studióban.
> 1. Az **alkalmazás** > -**res** > **RAW**-ben nyissa meg a **auth_config. JSON**fájlt.
> 1. Szerkessze a **auth_config. JSON** fájlt, és cserélje le a JSON-ra a Azure Portal. Ha ehelyett manuálisan szeretné elvégezni ezeket a módosításokat:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. Az **alkalmazás** > -**jegyzékfájlokon**belül nyissa meg a **AndroidManifest. xml fájlt**.
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
> 1. Cserélje `Enter_the_Package_Name` le `Enter_the_Signature_Hash` a és a értéket a Azure Portalban regisztrált értékekre. 
> 1. Futtassa az alkalmazást! 

## <a name="more-information"></a>További információ

Az alábbi szakaszok a rövid útmutatóhoz kapcsolódód további információkat tartalmaznak.

### <a name="getting-msal"></a>MSAL beolvasása

A MSAL ([com. microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek. A Gradle 3.0 + használatával telepítheti azt úgy, hogy a következőt adja hozzá a **Gradle Scripts** > **Build. Gradle (modul: app)** elemhez a **függőségek**területen:

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
> |`R.raw.auth_config` | Ez a fájl tartalmazza az alkalmazás konfigurációit, beleértve az alkalmazást, az ügyfél-azonosítót, a bejelentkezési célközönséget, az átirányítási URI-t és számos más testreszabási lehetőséget. |

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Az MSAL a következő két metódust használja a jogkivonatok beszerzéséhez: `acquireToken` és `acquireTokenSilentAsync`

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Jogkivonat interaktív beolvasása

Bizonyos helyzetekben a felhasználóknak a Microsoft Identity platformmal való interakcióra van szükségük. Ezekben az esetekben előfordulhat, hogy a végfelhasználónak ki kell választania a fiókját, meg kell adnia a hitelesítő adatait, vagy jóvá kell hagynia az alkalmazás által kért engedélyeket. Például: 

* Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
* Ha a felhasználó alaphelyzetbe állítja a jelszavát, meg kell adnia a hitelesítő adataikat 
* Ha a beleegyezést visszavonják 
* Ha az alkalmazás kifejezetten beleegyezést kér. 
* Ha az alkalmazás első alkalommal kér hozzáférést egy erőforráshoz
* Ha MFA-vagy más feltételes hozzáférési szabályzatokra van szükség

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Az elemek magyarázata:||
> |---------|---------|
> | `SCOPES` | Tartalmazza a kért hatóköröket (azaz `{ "user.read" }` Microsoft Graph és `{ "<Application ID URL>/scope" }` egyéni webes API-k esetében) (például `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Visszahívás, amelyre akkor kerül sor, amikor a hitelesítés után visszakerül a vezérlés az alkalmazáshoz |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent: Felhasználói jogkivonat csendes beszerzése

Az alkalmazásoknak nem kell minden alkalommal bejelentkezniük a felhasználóktól, amikor jogkivonatot kérnek. Ha a felhasználó már bejelentkezett, ez a módszer lehetővé teszi, hogy az alkalmazások csendesen igényeljenek jogkivonatokat.

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
> | `getAccounts(...)` | Azt a fiókot tartalmazza, amellyel a jogkivonatokat le szeretné kérni |
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

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)