---
title: Microsoft Identity platform – Android rövid útmutató | Azure
description: Ismerje meg, hogy az Android-alkalmazások hogyan hívhatnak meg olyan API-t, amely hozzáférési jogkivonatokat igényel a Microsoft Identity platform végpontjának.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee30e52561a5560e572e33fb3bc7d191559bf5d6
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927133"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Rövid útmutató: Felhasználók bejelentkeztetése és a Microsoft Graph API meghívása Android-alkalmazásokból

Ez a rövid útmutató egy kód mintát használ annak bemutatására, hogy az Android-alkalmazások hogyan jelentkezhetnek be személyes, munkahelyi vagy iskolai fiókjaikat a Microsoft Identity platform használatával, majd szerezzen be egy hozzáférési jogkivonatot, és hívja meg a Microsoft Graph API-t.

Az alkalmazásokat a Azure Active Directory alkalmazásnak kell képviselnie, hogy a Microsoft Identity platform meg tudja osztani a jogkivonatokat az alkalmazásával.

> [!div renderon="docs"]
> Kényelmi szempontból a mintakód a `AndroidManifest.xml` fájlban előre konfigurált alapértelmezett `redirect_uri` tartalmaz, így nem kell először regisztrálnia a saját alkalmazás-objektumát. Egy `redirect_uri` részben az alkalmazás aláíró kulcsán alapul. A minta projekt előre konfigurálva van egy aláíró kulccsal, hogy a megadott `redirect_uri` működni fog. Ha többet szeretne megtudni az alkalmazáshoz való regisztrálásról és az alkalmazással való integrálásáról, tekintse meg a [bejelentkezési felhasználók és a Microsoft Graph meghívása androidos alkalmazásból](tutorial-v2-android.md) oktatóanyagot.

![A minta alkalmazás képernyőképe](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Előfeltételek**
> * Android Studio 
> * Android 16 +

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1\. lépés: az alkalmazás konfigurálása a Azure Portalban 
>  Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, hozzá kell adnia egy átirányítási URI-t, amely kompatibilis a hitelesítési közvetítővel.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-android/green-check.png) Az alkalmazás már konfigurálva van ezekkel az attribútumokkal
>
> ### <a name="step-2-download-the-project"></a>2\. lépés: A projekt letöltése 
> * [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> ### <a name="step-3-configure-your-project"></a>3\. lépés: A projekt konfigurálása
> 1. Csomagolja ki és nyissa meg a projektet az Android Studióban.
> 2. Az **app** > **src** > a **Main** > **res** > **RAW**, nyissa meg **auth_config_multiple_account. JSON** fájlt, és cserélje le a következő kódra:
> ```javascript 
> {
>   "client_id" : "Enter_the_Application_Id_Here",
>   "authorization_user_agent" : "DEFAULT",
>   "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>   "account_mode" : "MULTIPLE",
>   "broker_redirect_uri_registered": true,
>   "authorities" : [
>     {
>       "type": "AAD",
>       "audience": {
>         "type": "Enter_the_Audience_Info_Here",
>         "tenant_id": "Enter_the_Tenant_Info_Here"
>       }
>     }
>   ]
> }
> ```

> [!div class="sxs-lookup" renderon="portal"]
> 3. Az **app** > **src** > a **Main** > **res** > **RAW**, nyissa meg **auth_config_single_account. JSON** fájlt, és cserélje le a következő kódra:
> ```javascript 
> {
>   "client_id" : "Enter_the_Application_Id_Here",
>   "authorization_user_agent" : "DEFAULT",
>   "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>   "account_mode" : "SINGLE",
>   "broker_redirect_uri_registered": true,
>   "authorities" : [
>     {
>       "type": "AAD",
>       "audience": {
>         "type": "Enter_the_Audience_Info_Here",
>         "tenant_id": "Enter_the_Tenant_Info_Here"
>       }
>     }
>   ]
> }
> ```

> [!div class="sxs-lookup" renderon="portal"]
> 4. Az **app** > **src** > **Main**oldalon nyissa meg a **AndroidManifest. xml fájlt**.
> 5. A **manifest\application** csomópontban cserélje le az **Android: Name = "com. microsoft. Identity. Client. BrowserTabActivity"** csomópontot a következőre:    
> ```xml
> <!--Intent filter to catch Microsoft's callback after Sign In-->
> <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
>     <intent-filter>
>         <action android:name="android.intent.action.VIEW" />
>         <category android:name="android.intent.category.DEFAULT" />
>         <category android:name="android.intent.category.BROWSABLE" />
>         <!--
>             Add in your scheme/host from registered redirect URI 
>             note that the leading "/" is required for android:path
>         -->
>         <data 
>             android:host="Enter_the_Package_Name"
>             android:path="/Enter_the_Signature_Hash"
>             android:scheme= "msauth" />
>     </intent-filter>
> </activity>
> ```
> 6. Futtassa az alkalmazást!   
> A minta alkalmazás az **Egyfiókos mód** képernyőn indul el. Alapértelmezés szerint az alapértelmezett hatókör, a **User. Read**, amelyet a rendszer akkor használ, amikor a Microsoft Graph API-hívás során beolvassa a saját profil adatait. Alapértelmezés szerint a Microsoft Graph API-hívás URL-címe van megadva. Ha szeretné, mindkettőt módosíthatja.
>
> ![MSAL minta alkalmazás, amely egyetlen és több fiók használatát mutatja be](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Az alkalmazás menü használatával válthat egy és több fiók közötti mód között.
>
> Önálló fiók módban jelentkezzen be egy munkahelyi vagy otthoni fiókkal:
>
> 1. Válassza a **Graph-adatok interaktív beolvasása** lehetőséget a felhasználó hitelesítő adatainak megadásához. A képernyő alján megjelenik a Microsoft Graph API felé irányuló hívás kimenete.
> 2. Miután bejelentkezett, válassza a **Graph-adatok csendes beolvasása** lehetőséget, hogy meghívja a Microsoft Graph API-t anélkül, hogy újra kellene kérnie a felhasználót a hitelesítő adatok megadására. A képernyő alján megjelenik a Microsoft Graph API felé irányuló hívás kimenete.
>
> Több fiókos módban is megismételheti ugyanezeket a lépéseket.  Emellett eltávolíthatja a bejelentkezett fiókot is, amely eltávolítja az adott fiók gyorsítótárazott jogkivonatait is.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ez a rövid útmutató támogatja a Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>1\. lépés: a minta alkalmazás beszerzése
>
> [Töltse le a kódot](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>2\. lépés: a minta alkalmazás futtatása
>
> Válassza ki emulátorát vagy fizikai eszközét Android Studio **elérhető eszközök** legördülő menüből, és futtassa az alkalmazást.
>
> A minta alkalmazás az **Egyfiókos mód** képernyőn indul el. Alapértelmezés szerint az alapértelmezett hatókör, a **User. Read**, amelyet a rendszer akkor használ, amikor a Microsoft Graph API-hívás során beolvassa a saját profil adatait. Alapértelmezés szerint a Microsoft Graph API-hívás URL-címe van megadva. Ha szeretné, mindkettőt módosíthatja.
>
> ![MSAL minta alkalmazás, amely egyetlen és több fiók használatát mutatja be](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Az alkalmazás menü használatával válthat egy és több fiók közötti mód között.
>
> Önálló fiók módban jelentkezzen be egy munkahelyi vagy otthoni fiókkal:
>
> 1. Válassza a **Graph-adatok interaktív beolvasása** lehetőséget a felhasználó hitelesítő adatainak megadásához. A képernyő alján megjelenik a Microsoft Graph API felé irányuló hívás kimenete.
> 2. Miután bejelentkezett, válassza a **Graph-adatok csendes beolvasása** lehetőséget, hogy meghívja a Microsoft Graph API-t anélkül, hogy újra kellene kérnie a felhasználót a hitelesítő adatok megadására. A képernyő alján megjelenik a Microsoft Graph API felé irányuló hívás kimenete.
>
> Több fiókos módban is megismételheti ugyanezeket a lépéseket.  Emellett eltávolíthatja a bejelentkezett fiókot is, amely eltávolítja az adott fiók gyorsítótárazott jogkivonatait is.

## <a name="how-the-sample-works"></a>A minta működése

A kód töredékekre van rendezve, amelyek bemutatják, hogyan írhat egyetlen és több fiókot MSAL-alkalmazásba. A kód fájljai a következőképpen vannak rendszerezve:

| Fájl  | Bemutatja  |
|---------|---------|
| MainActivity | A felhasználói felület kezelése |
| MSGraphRequestWrapper  | A Microsoft Graph API meghívása a MSAL által biztosított jogkivonat használatával |
| MultipleAccountModeFragment  | Inicializál egy többfiókos alkalmazást, betölti a felhasználói fiókot, és lekéri a tokent a Microsoft Graph API meghívásához |
| SingleAccountModeFragment | Inicializál egy egyfiókos alkalmazást, betölti a felhasználói fiókot, és lekéri a tokent a Microsoft Graph API meghívásához |
| res/auth_config_multiple_account. JSON  | A több fiók konfigurációs fájlja |
| res/auth_config_single_account. JSON  | Az egyetlen fiók konfigurációs fájlja |
| Gradle-parancsfájlok/Build. Range (modul: alkalmazás) | A MSAL-függvénytár függőségei itt lesznek hozzáadva |

Most megtekintjük ezeket a fájlokat részletesebben, és megvizsgáljuk a MSAL-specifikus kódokat.

### <a name="adding-msal-to-the-app"></a>MSAL hozzáadása az alkalmazáshoz

A MSAL ([com. microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek. Gradle 3.0 + telepíti a könyvtárat, amikor hozzáadja a következőt a **Gradle parancsfájlokhoz** > **Build. Gradle (modul: app)** a **függőségek**területen:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

Ezt a Build. gradle (modul: alkalmazás) projektben láthatja:

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.+'
    ...
}
```

Ez arra utasítja a Gradle, hogy töltsön le és építsen MSAL a Maven Central-ból.

### <a name="msal-imports"></a>MSAL-importálások

A MSAL-könyvtárhoz tartozó importálások `com.microsoft.identity.client.*`.  Láthatja például, hogy `import com.microsoft.identity.client.PublicClientApplication;`, amely a `PublicClientApplication` osztály névterét jelenti, amely a nyilvános ügyfélalkalmazás.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment. Java

Ez a fájl bemutatja, hogyan hozhat létre egyetlen MSAL alkalmazást, és hogyan hívhat meg Microsoft Graph API-t.

Az egyfiókos alkalmazásokat csak egyetlen felhasználó használja.  Előfordulhat például, hogy csak egy fiókkal rendelkezik, amelybe bejelentkezett a leképezési alkalmazásba.

#### <a name="single-account-msal-initialization"></a>Egy fiók MSAL inicializálása

A `auth_config_single_account.json``onCreateView()`a `auth_config_single_account.json` fájlban tárolt konfigurációs adatok használatával létrejön egy fiók `PublicClientApplication`.  Így inicializálhatja a MSAL-függvénytárat egy egyfiókos MSAL-alkalmazásban való használatra:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                    * This test app assumes that the app is only going to support one account.
                    * This requires "account_mode" : "SINGLE" in the config json file.
                    **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Bejelentkezés felhasználóként

`SingleAccountModeFragment.java`a felhasználó bejelentkezési kódja `initializeUI()`, a `signInButton` kattintson a kezelő elemre.

A tokenek beszerzése előtt hívja meg `signIn()`. `signIn()` úgy viselkedik, mintha a `acquireToken()` meghívása megtörtént, ami interaktív kérést biztosít a felhasználónak a bejelentkezéshez.

A felhasználó bejelentkezése aszinkron művelet. A rendszer visszahívást küld, amely meghívja a Microsoft Graph API-t, és a felhasználó bejelentkezése után frissíti a felhasználói FELÜLETET:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Felhasználó kijelentkezése

`SingleAccountModeFragment.java`a felhasználó kijelentkezésére szolgáló kód `initializeUI()`, a `signOutButton` kattintson a kezelő elemre.  A felhasználó aláírása aszinkron művelet. A felhasználó aláírása törli a fiókhoz tartozó jogkivonat-gyorsítótárat is. A felhasználói fiók kijelentkezése után a rendszer visszahívást hoz létre a felhasználói felület frissítéséhez:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Jogkivonat interaktív vagy csendes beszerzése

Ha a lehető legkevesebb kérést szeretné bemutatni a felhasználónak, akkor a rendszer általában csendes esetben lekérdezi a tokent. Ha hiba merül fel, próbálja meg interaktív módon beolvasni a tokent. Amikor az alkalmazás első alkalommal hívja meg a `signIn()`-t, az hatékonyan meghívja a `acquireToken()`, amely felszólítja a felhasználót a hitelesítő adatok megadására.

Bizonyos esetekben előfordulhat, hogy a felhasználónak meg kell adnia a fiókját, meg kell adnia a hitelesítő adatait, vagy jóvá kell hagynia az alkalmazás által kért engedélyeket:

* Amikor a felhasználó először jelentkezik be az alkalmazásba
* Ha a felhasználó alaphelyzetbe állítja a jelszavát, meg kell adnia a hitelesítő adataikat
* Ha a beleegyezést visszavonják
* Ha az alkalmazás kifejezetten beleegyezést igényel
* Ha az alkalmazás első alkalommal kér hozzáférést egy erőforráshoz
* Ha MFA-vagy más feltételes hozzáférési szabályzatokra van szükség

A token interaktív beszerzésére szolgáló kód, amely a felhasználót magában foglaló felhasználói felülettel rendelkezik, `SingleAccountModeFragment.java`, `initializeUI()``callGraphApiInteractiveButton` kattintson a kezelő:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Ha a felhasználó már bejelentkezett, `acquireTokenSilentAsync()` lehetővé teszi az alkalmazások számára a tokenek kérését csendesen, ahogy az a `initializeUI()`ban is látható, a `callGraphApiSilentButton` kattintson a kezelő:

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Fiók betöltése

A fiók betöltésére szolgáló kód a `loadAccount()``SingleAccountModeFragment.java`ban található.  A felhasználó fiókjának betöltése aszinkron művelet, ezért a fiók betöltésének, módosításának vagy hibájának beolvasásakor visszahívást kell adni a MSAL.  A következő kód a `onAccountChanged()`t is kezeli, amely akkor fordul elő, ha egy fiókot eltávolítanak, a felhasználó másik fiókra vált, és így tovább.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Hívás Microsoft Graph

Ha egy felhasználó be van jelentkezve, a Microsoft Graphre irányuló hívást egy, a `SingleAccountModeFragment.java`ban definiált `callGraphAPI()` által létrehozott HTTP-kérésen keresztül végzi el a rendszer. Ez a függvény egy burkoló, amely leegyszerűsíti a mintát olyan feladatok elvégzésével, mint például a hozzáférési token beszerzése a `authenticationResult`, és a hívás becsomagolása a MSGraphRequestWrapper, valamint a hívás eredményének megjelenítése.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account. JSON

Ez egy olyan MSAL-alkalmazás konfigurációs fájlja, amely egyetlen fiókot használ.

A mezők magyarázatát az [androidos MSAL konfigurációs fájljának megismerése](msal-configuration.md) című részben találja.

Jegyezze fel `"account_mode" : "SINGLE"`jelenlétét, amely az alkalmazást egyetlen fiók használatára konfigurálja.

`"client_id"` előre konfigurálva van egy, a Microsoft által fenntartott alkalmazás-objektum regisztrációjának használatára.
`"redirect_uri"`előre konfigurálva van, hogy a kód mintájában megadott aláíró kulcsot használja.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment. Java

Ez a fájl bemutatja, hogyan hozhat létre több fiókból álló MSAL-alkalmazást, és hogyan hívhat meg Microsoft Graph API-t.

Egy több fiókból álló alkalmazás például egy olyan levelező alkalmazás, amely lehetővé teszi, hogy több felhasználói fiókkal, például munkahelyi fiókkal és személyes fiókkal is működjön.

#### <a name="multiple-account-msal-initialization"></a>Több fiók MSAL inicializálása

A `MultipleAccountModeFragment.java` fájlban `onCreateView()`egy több Account app-objektum (`IMultipleAccountPublicClientApplication`) jön létre a `auth_config_multiple_account.json file`ban tárolt konfigurációs adatok használatával:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

A létrehozott `MultipleAccountPublicClientApplication` objektum egy Class tag-változóban van tárolva, így a MSAL-könyvtárral folytatott kommunikációhoz a tokenek beszerzése és a felhasználói fiók betöltése és eltávolítása is használható.

#### <a name="load-an-account"></a>Fiók betöltése

Az MSAL-műveletekhez használt fiók kiválasztásához több fiókos alkalmazás is hívja a `getAccounts()`-t. A fiók betöltésére szolgáló kód a `loadAccounts()``MultipleAccountModeFragment.java` fájlban található.  A felhasználó fiókjának betöltése aszinkron művelet. Így a visszahívás a fiók betöltését, változásait vagy hiba esetén felmerülő helyzeteket kezeli.

```java
/**
     * Load currently signed-in accounts, if there's any.
    */
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Jogkivonat interaktív vagy csendes beszerzése

Bizonyos esetekben előfordulhat, hogy a felhasználónak meg kell adnia a fiókját, meg kell adnia a hitelesítő adatait, vagy jóvá kell hagynia az alkalmazás által kért engedélyeket:

* Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
* Ha a felhasználó alaphelyzetbe állítja a jelszavát, meg kell adnia a hitelesítő adataikat 
* Ha a beleegyezést visszavonják 
* Ha az alkalmazás kifejezetten beleegyezést igényel 
* Ha az alkalmazás első alkalommal kér hozzáférést egy erőforráshoz
* Ha MFA-vagy más feltételes hozzáférési szabályzatokra van szükség

A több fiókból álló alkalmazások általában interaktív módon szerzik be a jogkivonatokat, amelyek a felhasználóval kapcsolatos felhasználói FELÜLETtel rendelkeznek, és meghívja a `acquireToken()`.  A jogkivonat interaktív beolvasására szolgáló kód a `initializeUI()``MultipleAccountModeFragment.java` fájljában található, `callGraphApiInteractiveButton` kattintson a kezelő elemre:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Az alkalmazásoknak nem kell minden alkalommal bejelentkezniük, amikor jogkivonatot kérnek. Ha a felhasználó már bejelentkezett, `acquireTokenSilentAsync()` lehetővé teszi, hogy az alkalmazások a felhasználó értesítése nélkül igényeljenek jogkivonatokat, ahogy az a `MultipleAccountModeFragment.java` fájlban is látható`initializeUI()`, a `callGraphApiSilentButton` kattintson a kezelőre:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
    accountList.get(accountListSpinner.getSelectedItemPosition()),
    AUTHORITY,
    getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Fiók eltávolítása

A fiók eltávolításához és a fiók gyorsítótárazott jogkivonatához tartozó kód a fiók eltávolítása gomb kezelőjének `initializeUI()` `MultipleAccountModeFragment.java` fájljában található. Mielőtt eltávolít egy fiókot, szüksége lesz egy fiók objektumra, amelyet a MSAL függvények (például a `getAccounts()` és a `acquireToken()`) szerez be. Mivel a fiókok eltávolítása aszinkron művelet, a rendszer a `onRemoved` visszahívást a felhasználói felület frissítéséhez.

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account. JSON

Ez egy olyan MSAL-alkalmazás konfigurációs fájlja, amely több fiókot használ.

Az [androidos MSAL konfigurációs fájljának megismeréséhez](msal-configuration.md) tekintse meg a különböző mezők magyarázatát.

A [auth_config_single_account. JSON](#auth_config_single_accountjson) konfigurációs fájllal ellentétben ez a konfigurációs fájl `"account_mode" : "MULTIPLE"` helyett a `"account_mode" : "SINGLE"`, mert ez egy több fiókból álló alkalmazás.

`"client_id"` előre konfigurálva van egy, a Microsoft által fenntartott alkalmazás-objektum regisztrációjának használatára.
`"redirect_uri"`előre konfigurálva van, hogy a kód mintájában megadott aláíró kulcsot használja.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Következő lépések

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>A jelen rövid útmutatóban használt alkalmazás létrehozási lépéseinek ismertetése

Próbálja ki a [bejelentkezési felhasználókat, és hívja meg a Microsoft Graph egy Android-alkalmazásról](tutorial-v2-android.md) szóló oktatóanyagban egy lépésenkénti útmutatót egy olyan Android-alkalmazás létrehozásához, amely hozzáférési jogkivonatot kap, és azt használja a Microsoft Graph API meghívásához.

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
