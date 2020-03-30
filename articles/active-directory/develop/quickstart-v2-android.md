---
title: Microsoft identity platform Android rövid útmutató | Azure
description: Ismerje meg, hogyan hívhatják meg az Android-alkalmazások a Microsoft identity platform végpontja által hozzáférési jogkivonatokat igénylő API-t.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: a184b035e3296f82ecdacf74a99ea7148d99bd49
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78271114"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Rövid útmutató: Felhasználók bejelentkeztetése és a Microsoft Graph API meghívása Android-alkalmazásokból

Ez a rövid útmutató egy kódmintát használ annak bemutatására, hogy az Android-alkalmazások hogyan jelentkezhetnek be személyes, munkahelyi vagy iskolai fiókokba a Microsoft identity platform használatával, majd kapnak egy hozzáférési jogkivonatot, és hívják meg a Microsoft Graph API-t. (Lásd: [Hogyan működik a minta](#how-the-sample-works) egy illusztrációhoz.)

Az alkalmazásokat egy alkalmazásobjektumnak kell képviselnie az Azure Active Directoryban, hogy a Microsoft identity platform jogkivonatokat biztosíthasson az alkalmazásszámára.

> [!div renderon="docs"]
> A kódminta a `redirect_uri` `AndroidManifest.xml` fájlban egy előre konfigurált alapértelmezett, így nem kell először regisztrálnia a saját alkalmazásobjektumát. Az `redirect_uri` A részben az alkalmazás aláíró kulcsán alapul. A mintaprojekt előre van konfigurálva egy `redirect_uri` aláíró kulccsal, így a megadott működni fog. Ha többet szeretne tudni egy alkalmazásobjektum regisztrálásáról és az alkalmazással való integrálásáról, olvassa el a Felhasználók bejelentkezése című [témakört, és hívja fel a Microsoft Graph-ot egy Android-alkalmazásoktatóanyagból.](tutorial-v2-android.md)


> [!NOTE]
> **Előfeltételek**
> * Android Studio 
> * Android 16+

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon 
>  A kódminta a gyorsindítás működéséhez, hozzá kell adnia egy átirányítási URI kompatibilis az Auth broker.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Hajtsa végre ezeket a módosításokat nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-android/green-check.png) Az alkalmazás már konfigurálva van ezekkel az attribútumokkal
>
> ### <a name="step-2-download-the-project"></a>2. lépés: A projekt letöltése 
> [!div class="sxs-lookup" renderon="portal"]
> Futtassa a projektet az Android Studio segítségével.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [A kódminta letöltése](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: Az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékeivel konfiguráltuk, és készen áll a futtatásra. 
> A mintaalkalmazás az **egyfiókos módban kezdődik.** Alapértelmezés szerint egy alapértelmezett hatókört ( **user.read**) biztosít a rendszer, amely a Saját profiladatok olvasásakor használatos a Microsoft Graph API-hívás során. A Microsoft Graph API-hívás URL-címe alapértelmezés szerint meg van adva. Mindkettőt megváltoztathatod, ha akarod.
>
> ![Az MSAL mintaalkalmazás egy- és többfiókos használatot mutat](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Az alkalmazás menüjében válthat az egy- és többfiókos üzemmódok között.
>
> Egyfiókos módban jelentkezzen be munkahelyi vagy otthoni fiókkal:
>
> 1. Jelölje be **a Diagramadatok interaktív bekérése jelölőnégyzetet,** ha a felhasználótól meg szeretné kapni a hitelesítő adatait. A képernyő alján láthatja a Microsoft Graph API-ra irányuló hívás kimenetét.
> 2. Miután bejelentkezett, válassza a **Graph-adatok csendes bekérése** lehetőséget a Microsoft Graph API hívásához anélkül, hogy a felhasználónak újra hitelesítő adatokat kérne. A képernyő alján láthatja a Microsoft Graph API-ra irányuló hívás kimenetét.
>
> Több fiók módban ugyanazokat a lépéseket megismételheti.  Emellett eltávolíthatja a bejelentkezett fiók, amely szintén eltávolítja a gyorsítótárazott jogkivonatok, hogy a fiók.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>1. lépés: A mintaalkalmazás beszereznie
>
> [Töltse le a kódot](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>2. lépés: A mintaalkalmazás futtatása
>
> Válassza ki az emulátort vagy a fizikai eszközt az Android Studio **elérhető eszközeinek** legördülő menüjéből, és futtassa az alkalmazást.
>
> A mintaalkalmazás az **egyfiókos módban kezdődik.** Alapértelmezés szerint egy alapértelmezett hatókört ( **user.read**) biztosít a rendszer, amely a Saját profiladatok olvasásakor használatos a Microsoft Graph API-hívás során. A Microsoft Graph API-hívás URL-címe alapértelmezés szerint meg van adva. Mindkettőt megváltoztathatod, ha akarod.
>
> ![Az MSAL mintaalkalmazás egy- és többfiókos használatot mutat](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Az alkalmazás menüjében válthat az egy- és többfiókos üzemmódok között.
>
> Egyfiókos módban jelentkezzen be munkahelyi vagy otthoni fiókkal:
>
> 1. Jelölje be **a Diagramadatok interaktív bekérése jelölőnégyzetet,** ha a felhasználótól meg szeretné kapni a hitelesítő adatait. A képernyő alján láthatja a Microsoft Graph API-ra irányuló hívás kimenetét.
> 2. Miután bejelentkezett, válassza a **Graph-adatok csendes bekérése** lehetőséget a Microsoft Graph API hívásához anélkül, hogy a felhasználónak újra hitelesítő adatokat kérne. A képernyő alján láthatja a Microsoft Graph API-ra irányuló hívás kimenetét.
>
> Több fiók módban ugyanazokat a lépéseket megismételheti.  Emellett eltávolíthatja a bejelentkezett fiók, amely szintén eltávolítja a gyorsítótárazott jogkivonatok, hogy a fiók.

## <a name="how-the-sample-works"></a>A minta működése
![Képernyőkép a mintaalkalmazásról](media/quickstart-v2-android/android-intro.svg)


A kód töredékekbe van rendezve, amelyek megmutatják, hogyan kell írni egy és több fiókos MSAL alkalmazást. A kódfájlok a következőképpen vannak rendezve:

| Fájl  | Útmutató ehhez:  |
|---------|---------|
| Fő aktivitás | A felhasználói felület kezelése |
| MSGraphRequestWrapper  | Meghívja a Microsoft Graph API-t az MSAL által biztosított jogkivonat használatával |
| MultipleAccountModeFragment  | Inicializálja a többfiókos alkalmazásokat, betölt egy felhasználói fiókot, és lekér egy jogkivonatot a Microsoft Graph API hívásához |
| SingleAccountModeFragment | Inicializálja az egyfiókos alkalmazásokat, betölt egy felhasználói fiókot, és lekér egy jogkivonatot a Microsoft Graph API hívásához |
| res/auth_config_multiple_account.json  | A többfiókos konfigurációs fájl |
| res/auth_config_single_account.json  | Az egyfiókos konfigurációs fájl |
| Gradle Scripts/build.grade (Module:app) | Az MSAL könyvtárfüggőségek itt kerülnek hozzáadásra |

Most részletesebben megnézzük ezeket a fájlokat, és mindegyikben kihívjuk az MSAL-specifikus kódot.

### <a name="adding-msal-to-the-app"></a>MSAL hozzáadása az alkalmazáshoz

Az MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) a Microsoft identity platform által védett API-k eléréséhez használt felhasználók bejelentkezéséhez és tokenek kéréséhez használt tár. A Gradle 3.0+ akkor telepíti a könyvtárat, amikor hozzáadja a következőket a **Gradle Scripts** > **build.gradle (Module: app)** fájlhoz a **Függőségek**csoportban:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.+'
```

Ezt a build.gradle (Modul: alkalmazás) mintaprojektjében láthatja:

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.+'
    ...
}
```

Ez arra utasítja Gradle-t, hogy töltse le és építse meg az MSAL-t a Maven centralból.

### <a name="msal-imports"></a>MSAL-behozatal

Az MSAL könyvtárra vonatkozó importok a következők. `com.microsoft.identity.client.*`  Például látni `import com.microsoft.identity.client.PublicClientApplication;` fogja, hogy melyik az `PublicClientApplication` osztály névtere, amely a nyilvános ügyfélalkalmazást jelöli.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Ez a fájl bemutatja, hogyan hozhat létre egyetlen fiókos MSAL-alkalmazást, és hogyan hívhat ja meg a Microsoft Graph API-t.

Az egyfiókos alkalmazásokat csak egyetlen felhasználó használja.  Előfordulhat például, hogy csak egy fiókkal rendelkezik, amelyen bejelentkezik a leképezési alkalmazásba.

#### <a name="single-account-msal-initialization"></a>Egyfiókos MSAL inicializálása

A `auth_config_single_account.json`alkalmazásban `onCreateView()`egyetlen fiók `PublicClientApplication` jön létre a `auth_config_single_account.json` fájlban tárolt konfigurációs adatok használatával.  Így inicializálhatja az MSAL-könyvtárat egy egyfiókos MSAL alkalmazásban való használatra:

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

#### <a name="sign-in-a-user"></a>Felhasználó bejelentkezése

A `SingleAccountModeFragment.java`ban a rendszer a bejelentkezéshez szükséges kódot a ban `initializeUI()`található, a `signInButton` kattintáskezelőben.

Hívja `signIn()` meg, mielőtt tokeneket próbálna beszerezni. `signIn()`úgy viselkedik, `acquireToken()` mintha meg lenne hívva, így a felhasználó interaktív figyelmeztetést kap a bejelentkezéshez.

A felhasználó bejelentkezése aszinkron művelet. A rendszer visszahívja a Microsoft Graph API-t, és frissíti a felhasználói felületet, amint a felhasználó bejelentkezik:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Felhasználó kijelentkezése

A `SingleAccountModeFragment.java`ban a, hogy jelentkezzen `initializeUI()`ki egy `signOutButton` felhasználó a, a, a, a kattintáskezelőben.  A felhasználó kijelentkezése aszinkron művelet. A felhasználó kijelentkezése törli a fiók tokengyorsítótárát is. A felhasználói fiók kijelentkezése után visszahívás jön létre a felhasználói felület frissítéséhez:

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

#### <a name="get-a-token-interactively-or-silently"></a>Token interaktív vagy csendes beszerezni

A legkevesebb kérdés bemutatása a felhasználó nak, általában kap egy jogkivonatot csendben. Ezután, ha van egy hiba, próbálja meg, hogy a token interaktívan. Az első alkalommal, `signIn()`amikor az alkalmazás meghívja , hatékonyan `acquireToken()`működik, mint egy hívás , amely kéri a felhasználót a hitelesítő adatokat.

Bizonyos helyzetekben, amikor a rendszer a felhasználót a fiók kiválasztására, a hitelesítő adatai megadására vagy az alkalmazás által kért engedélyekhez való hozzájárulásra kéri:

* Az első alkalommal, amikor a felhasználó bejelentkezik az alkalmazásba
* Ha egy felhasználó alaphelyzetbe állítja a jelszavát, meg kell adnia a hitelesítő adatait
* Ha a hozzájárulást visszavonják
* Ha az alkalmazás kifejezetten beleegyezést igényel
* Amikor az alkalmazás első alkalommal kér hozzáférést egy erőforráshoz
* Ha többes fa- vagy más feltételes hozzáférési házirendre van szükség

A kód, hogy egy token interaktívan, azaz a felhasználói `SingleAccountModeFragment.java`felület, `initializeUI()`amely `callGraphApiInteractiveButton` magában foglalja a felhasználó, a , a , a, a kattintáskezelő:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Ha a felhasználó már `acquireTokenSilentAsync()` bejelentkezett, lehetővé teszi, hogy `initializeUI()`az `callGraphApiSilentButton` alkalmazások csendben kérjenek tokeneket a ( kattintáskezelő) alkalmazásban látható módon:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Fiók betöltése

A fiók betöltéséhez `SingleAccountModeFragment.java` szükséges `loadAccount()`kód a ban található.  A felhasználói fiók betöltése aszinkron művelet, így a fiók betöltésekor, módosításokvagy hiba esetén kezelni szükséges visszahívások továbbítása az MSAL-nak.  A következő kód `onAccountChanged()`kezeli is , amely akkor jelentkezik, amikor egy fiókot eltávolítanak, a felhasználó egy másik fiókba, és így tovább.

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

#### <a name="call-microsoft-graph"></a>A Microsoft Graph hívása

Ha egy felhasználó be van jelentkezve, a Microsoft Graph `callGraphAPI()` hívása http-kérelemen keresztül történik, amely a rendszerben `SingleAccountModeFragment.java`van meghatározva. Ez a függvény egy burkoló, amely leegyszerűsíti a mintát `authenticationResult` bizonyos feladatok elvégzésével, például a hozzáférési jogkivonat beszerzése a és csomagolása a hívást az MSGraphRequestWrapper, és megjeleníti a hívás eredményeit.

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

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Ez egy olyan MSAL-alkalmazás konfigurációs fájlja, amely egyetlen fiókot használ.

[Lásd: Az Android MSAL konfigurációs fájljának](msal-configuration.md) ismertetése a mezők magyarázatáért.

Figyelje meg `"account_mode" : "SINGLE"`a jelenlétét, amely úgy konfigurálja ezt az alkalmazást, hogy egyetlen fiókot használjon.

`"client_id"`a Microsoft által fenntartott alkalmazásobjektum-regisztráció használatára van konfigurálva.
`"redirect_uri"`előre konfigurálva van a kódmintához mellékelt aláíró kulcs használatára.

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

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Ez a fájl bemutatja, hogyan hozhat létre többfiókos MSAL-alkalmazást, és hogyan hívhat meg egy Microsoft Graph API-t.

A többfiókos alkalmazásra példa egy levelezőalkalmazás, amely lehetővé teszi, hogy több felhasználói fiókkal, például munkahelyi és személyes fiókkal dolgozzon.

#### <a name="multiple-account-msal-initialization"></a>Többfiókos MSAL inicializálása

A `MultipleAccountModeFragment.java` fájlban `onCreateView()`egy többfiókos alkalmazásobjektum (`IMultipleAccountPublicClientApplication`) jön létre a `auth_config_multiple_account.json file`alkalmazásban tárolt konfigurációs adatok használatával:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
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

A `MultipleAccountPublicClientApplication` létrehozott objektum egy osztálytag változóban tárolódik, így az MSAL-kódtárral való interakcióval jogkivonatokat szerezhet, és betöltheti és eltávolíthatja a felhasználói fiókot.

#### <a name="load-an-account"></a>Fiók betöltése

Több fiókalkalmazások `getAccounts()` általában hívja, hogy válassza ki a fiókot használni MSAL műveleteket. A fiók betöltéséhez szükséges `MultipleAccountModeFragment.java` kód `loadAccounts()`a fájlban található, a alkalmazásban.  A felhasználói fiók betöltése aszinkron művelet. Így a visszahívás kezeli a fiók betöltésének, módosításának vagy hibájának helyzetét.

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
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

#### <a name="get-a-token-interactively-or-silently"></a>Token interaktív vagy csendes beszerezni

Bizonyos helyzetekben, amikor a rendszer a felhasználót a fiók kiválasztására, a hitelesítő adatai megadására vagy az alkalmazás által kért engedélyekhez való hozzájárulásra kéri:

* Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
* Ha egy felhasználó alaphelyzetbe állítja a jelszavát, meg kell adnia a hitelesítő adatait 
* Ha a hozzájárulást visszavonják 
* Ha az alkalmazás kifejezetten beleegyezést igényel 
* Amikor az alkalmazás első alkalommal kér hozzáférést egy erőforráshoz
* Ha többes fa- vagy más feltételes hozzáférési házirendre van szükség

Több fiók alkalmazások általában beszerezni jogkivonatok interaktívan, azaz a felhasználói felület, amely magában foglalja a felhasználó, a `acquireToken()`hívás.  A token interaktív bekérésének kódja `MultipleAccountModeFragment.java` `initializeUI()`a fájlban található a `callGraphApiInteractiveButton` kattintáskezelőben:

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
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Az alkalmazások nem követelhetik meg a felhasználótól, hogy minden alkalommal jelentkezzen be, amikor jogkivonatot kér. Ha a felhasználó már `acquireTokenSilentAsync()` bejelentkezett, lehetővé teszi, hogy az alkalmazások `MultipleAccountModeFragment.java` a`initializeUI()` `callGraphApiSilentButton` kattintáskezelőben a felhasználó megkérdezése nélkül kérjenek tokeneket, ahogy az a fájlban látható:

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

A fiók eltávolítására szolgáló kód és a fiók gyorsítótárazott `MultipleAccountModeFragment.java` tokenjei a fiók eltávolítása gomb kezelőjében lévő fájlban `initializeUI()` vannak. A fiók eltávolításához szüksége van egy fiókobjektumra, amelyet `getAccounts()` az `acquireToken()`MSAL módszerekből szerezhet be, például a és a. Mivel egy fiók eltávolítása aszinkron művelet, a `onRemoved` visszahívás a felhasználói felület frissítéséhez kerül továbbításra.

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
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

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Ez egy több fiókot használó MSAL-alkalmazás konfigurációs fájlja.

A különböző mezők magyarázatáért [olvassa el Az Android MSAL konfigurációs fájljának](msal-configuration.md) ismertetése című témakört.

A [auth_config_single_account.json](#auth_config_single_accountjson) konfigurációs fájllal ellentétben ez a konfigurációs fájl azért rendelkezik, `"account_mode" : "MULTIPLE"` `"account_mode" : "SINGLE"` mert ez egy többfiókos alkalmazás.

`"client_id"`a Microsoft által fenntartott alkalmazásobjektum-regisztráció használatára van konfigurálva.
`"redirect_uri"`előre konfigurálva van a kódmintához mellékelt aláíró kulcs használatára.

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

## <a name="next-steps"></a>További lépések

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>A jelen rövid útmutatóban használt alkalmazás létrehozási lépéseinek ismertetése

Próbálja ki a [Bejelentkezési felhasználókat, és hívja fel a Microsoft Graph-ot egy Android-alkalmazás oktatóanyagából](tutorial-v2-android.md) egy lépésről lépésre szóló útmutatóhoz egy olyan Android-alkalmazás létrehozásához, amely hozzáférési jogkivonatot kap, és a Microsoft Graph API hívására használja.

> [!div class="nextstepaction"]
> [A Graph API meghívása – oktatóanyag Androidhoz](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Androidhoz készült MSAL-kódtár – wiki

További információ az Androidhoz készült MSAL-kódtárról:

> [!div class="nextstepaction"]
> [Androidhoz készült MSAL-kódtár – wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft identitásplatformjának fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdésű felmérés kitöltésével.

> [!div class="nextstepaction"]
> [Microsoft-identitásplatform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
