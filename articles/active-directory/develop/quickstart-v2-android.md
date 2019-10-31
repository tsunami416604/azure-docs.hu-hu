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
ms.date: 09/16/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01c45c3f90fd1c9bf457f5c4024d029bf3819813
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149488"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Rövid útmutató: Felhasználók bejelentkeztetése és a Microsoft Graph API meghívása Android-alkalmazásokból

Ez a rövid útmutató egy kód mintát használ annak bemutatására, hogy az Android-alkalmazások hogyan jelentkezhetnek be személyes, munkahelyi vagy iskolai fiókjaikat a Microsoft Identity platform használatával, majd szerezzen be egy hozzáférési jogkivonatot, és hívja meg a Microsoft Graph API-t.

![A minta alkalmazás Screenshoft](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Előfeltételek**
> * Android Studio 
> * Android 16 + szükséges

## <a name="step-1-get-the-sample-app"></a>1\. lépés: a minta alkalmazás beszerzése

[A kód klónozása](https://github.com/Azure-Samples/ms-identity-android-java.git).

## <a name="step-2-register-your-application"></a>2\. lépés: az alkalmazás regisztrálása

Az alkalmazás-objektum regisztrálásához, majd az alkalmazás-objektum regisztrációs adatainak manuális hozzáadásához az alábbi lépéseket kell követnie:

1. Nyissa meg az [Azure Portal](https://aka.ms/MobileAppReg).
1. Nyissa meg a [Alkalmazásregisztrációk](https://portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) panelt, és kattintson az **+ új regisztráció**elemre.
1. Adja meg az alkalmazás regisztrációjának **nevét** , majd az átirányítási URI beállítása nélkül kattintson a **regisztráció**elemre.
1. A **kezelés** szakaszban válassza a **hitelesítés** >  **+ platform hozzáadása** > **Android**lehetőséget. (Előfordulhat, hogy a panel tetején lévő **új felület** kiválasztásával szeretné megtekinteni ezt a képernyőt)
1. Adja meg a projekt **csomagjának nevét**, amely `com.azuresamples.msalandroidapp`.
1. Az **Android-alkalmazás konfigurálása** lap **aláírás-kivonatolás** szakaszában kattintson a **fejlesztési aláírás kivonatának létrehozása**lehetőségre, és másolja a Főeszköz parancsot a használni kívánt platformra az Android-alkalmazás fejlesztéséhez.

   > [!Note]
   > A. exe a Java Development Kit (JDK) részeként van telepítve. Az OpenSSL eszközt is telepítenie kell, hogy végrehajtsa a parancssori eszközt.  Ehhez az elérési úthoz szüksége lesz az eszközre és a OpenSSL\bin könyvtárra.

1. Futtassa a Főeszköz parancsot, amelyet a portálról másolt egy terminál-ablakban.
1. Adja meg a generált aláírási kivonatot a portálon az **aláírási kivonat**alatt.
1. Kattintson a `Configure` elemre, és készítsen másolatot a **MSAL-konfigurációról**. Másolja és illessze be ezt egy konfigurációs fájlba a következő lépésben. Kattintson a **Done** (Kész) gombra.

## <a name="step-3-add-your-app-registration"></a>3\. lépés: az alkalmazás regisztrációjának hozzáadása

1. Nyissa meg a minta projektet Android Studioban.
1. Az **alkalmazáson** belül > **res** > **RAW**, nyissa meg a **auth_config_multiple_account. JSON**fájlt.  Illessze be a MSAL-konfiguráció tartalmát. Ezzel hozzáadja az ügyfél-azonosítót, a bérlői azonosítót és a redirect_uri a portálról. Ehhez hasonlóan fog kinézni, de az ügyfél-azonosító, a bérlői azonosító és a redirect_uri esetében kitöltött értékekkel:

    ```json
    {
      "client_id" : "<your_client_id_here>",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "<your_redirect_uri_here>",
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

1. Nyissa meg az **app** > **res** > **RAW**, nyissa meg a **auth_config_single_account. JSON**fájlt, és illessze be a MSAL-konfiguráció tartalmát. A fenti **auth_config_multiple_account. JSON** fájlhoz hasonlóan fog kinézni.
1. Az **alkalmazás** > **jegyzékfájlok** > **AndroidManifest. xml fájlban**keresse meg a `BrowserTabActivity` tevékenységet. Ez a bejegyzés lehetővé teszi, hogy a Microsoft visszahívjon az alkalmazásba a hitelesítés befejezése után:

    ```xml
    ...
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
    
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
    
                    <!--
                        Add in your scheme/host from registered redirect URI
                        note that the leading "/" is required for android:path
                        For Example:
                        <data
                            android:host="com.azuresamples.msalandroidapp"
                            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
                            android:scheme="msauth" />
                    -->
    
                    <data
                        android:host="YOUR_PACKAGE_NAME - must be registered at https://aka.ms/MobileAppReg"
                        android:path="/YOUR_DECODED_SIGNATURE_HASH - must be registered at https://aka.ms/MobileAppReg"
                        android:scheme="msauth" />
                </intent-filter>
            </activity>
    ```
    
1. Helyettesítse be a csomag nevét a `android:host=` értékhez tartozó Azure Portalban regisztrált névvel.  Ebben az esetben a következő lesz: `com.azuresamples.msalandroidapp`.

    > [!IMPORTANT]
    > Az **Android: Path** értékének kezdő "/" karakterrel **kell** rendelkeznie, vagy az érték alatti piros vonal jelenik meg, és a minta alkalmazás nem fog futni.
     
1. Helyettesítse be a korábban futtatott és a Azure Portalban megadott kulcs-kivonatot a `android:path=` értékhez. Az aláírási kivonat nem lehet URL-kódolású.

## <a name="step-4-run-the-sample-app"></a>4\. lépés: a minta alkalmazás futtatása

Válassza ki az emulátort vagy az eszközt a Android Studio **elérhető eszközök** legördülő menüből, és futtassa az alkalmazást.

A minta alkalmazás az **Egyfiókos mód** képernyőn indul el. Alapértelmezés szerint az alapértelmezett hatókör, a **User. Read**, amelyet a rendszer akkor használ, amikor a Microsoft Graph API-hívás során beolvassa a saját profil adatait. Alapértelmezés szerint a Microsoft Graph API-hívás URL-címe van megadva. Ha szeretné, mindkettőt módosíthatja.

![MSAL minta alkalmazás, amely egyetlen és több fiók használatát mutatja be](./media/quickstart-v2-android/quickstart-sample-app.png)

Az alkalmazás menü használatával válthat egy és több fiók közötti mód között.

Önálló fiók módban jelentkezzen be egy munkahelyi vagy otthoni fiókkal:

1. Válassza a **Graph-adatok interaktív beolvasása** lehetőséget a felhasználó hitelesítő adatainak megadásához. A képernyő alján megjelenik a Microsoft Graph API felé irányuló hívás kimenete.
2. Miután bejelentkezett, válassza a **Graph-adatok csendes beolvasása** lehetőséget, hogy meghívja a Microsoft Graph API-t anélkül, hogy újra kellene kérnie a felhasználót a hitelesítő adatok megadására. A képernyő alján megjelenik a Microsoft Graph API felé irányuló hívás kimenete.

Több fiókos módban is megismételheti ugyanezeket a lépéseket.  Emellett eltávolíthatja a bejelentkezett fiókot is, amely eltávolítja az adott fiók gyorsítótárazott jogkivonatait is.

## <a name="how-the-sample-works"></a>A minta működése

A kód töredékekre van rendezve, amelyek bemutatják, hogyan írhat egyetlen és több fiókot MSAL-alkalmazásba. A kód fájljai a következőképpen vannak rendszerezve:

| File  | Bemutatja  |
|---------|---------|
| MainActivity | A felhasználói felület kezelése |
| MSGraphRequestWrapper  | A Microsoft Graph API meghívása a MSAL által biztosított jogkivonat használatával |
| MultipleAccountModeFragment  | Inicializál egy többfiókos alkalmazást, betölti a felhasználói fiókot, és lekéri a tokent a Microsoft Graph API meghívásához |
| SingleAccountModeFragment | Inicializál egy egyfiókos alkalmazást, betölti a felhasználói fiókot, és lekéri a tokent a Microsoft Graph API meghívásához |
| res/auth_config_multiple_account. JSON  | A több fiók konfigurációs fájlja |
| res/auth_config_single_account. JSON  | Az egyetlen fiók konfigurációs fájlja |
| Gradle-parancsfájlok/Build. Range (modul: alkalmazás) | A MSAL-függvénytár függőségei itt lesznek hozzáadva |

Most megtekintjük ezeket a fájlokat részletesebben, és megvizsgáljuk a MSAL-specifikus kódokat.

### <a name="add-msal-to-the-app"></a>MSAL hozzáadása az alkalmazáshoz

A MSAL ([com. microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek. Gradle 3.0 + telepíti a könyvtárat, amikor hozzáadja a következőt a **Gradle parancsfájlokhoz** > **Build. Gradle (modul: app)** a **függőségek**területen:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

Ezt a Build. gradle (modul: alkalmazás) projektben láthatja:

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.0-RC7'
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

`onCreateView()`a `auth_config_single_account.json` fájlban tárolt konfigurációs adatok használatával létrehozható egy fiók `PublicClientApplication`.  Így inicializálhatja a MSAL-függvénytárat egy egyfiókos MSAL-alkalmazásban való használatra:

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

A felhasználó bejelentkezési kódja `initializeUI()`, a `signInButton` kattintson a kezelő elemre.

A tokenek beszerzése előtt hívja meg `signIn()`. `signIn()` úgy viselkedik, mintha a `acquireToken()` meghívása megtörtént, ami interaktív kérést biztosít a felhasználónak a bejelentkezéshez.

A felhasználó bejelentkezése aszinkron művelet. A rendszer visszahívást küld, amely meghívja a Microsoft Graph API-t, és a felhasználó bejelentkezése után frissíti a felhasználói FELÜLETET:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Felhasználó kijelentkezése

A felhasználó kijelentkezésére szolgáló kód `initializeUI()`, a `signOutButton` kattintson a kezelő elemre.  A felhasználó aláírása aszinkron művelet. A felhasználó aláírása törli a fiókhoz tartozó jogkivonat-gyorsítótárat is. A felhasználói fiók kijelentkezése után a rendszer visszahívást hoz létre a felhasználói felület frissítéséhez:

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

A token interaktív beszerzésére szolgáló kód, amely a felhasználót érintő felhasználói FELÜLETtel rendelkezik, `initializeUI()``callGraphApiInteractiveButton` kattintson a kezelő:

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

A fiók betöltésére szolgáló kód `loadAccount()`.  A felhasználó fiókjának betöltése aszinkron művelet, ezért a fiók betöltésének, módosításának vagy hibájának beolvasásakor visszahívást kell adni a MSAL.  A következő kód a `onAccountChanged()`t is kezeli, amely akkor fordul elő, ha egy fiókot eltávolítanak, a felhasználó másik fiókra vált, és így tovább.

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

Ha egy felhasználó be van jelentkezve, a Microsoft Graphre irányuló hívást egy `callGraphAPI()`HTTP-kérésen keresztül végzi el. Ez a függvény egy burkoló, amely leegyszerűsíti a mintát olyan feladatok elvégzésével, mint például a hozzáférési token beszerzése a `authenticationResult`, és a hívás becsomagolása a MSGraphRequestWrapper, valamint a hívás eredményének megjelenítése.

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

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
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

`onCreateView()`több Account app Object (`IMultipleAccountPublicClientApplication`) jön létre a `auth_config_multiple_account.json file`ban tárolt konfigurációs adatok használatával:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

A létrehozott `MultipleAccountPublicClientApplication` objektum egy Class tag-változóban van tárolva, így a MSAL-könyvtárral folytatott kommunikációhoz a tokenek beszerzése és a felhasználói fiók betöltése és eltávolítása is használható.

#### <a name="load-an-account"></a>Fiók betöltése

Az MSAL-műveletekhez használt fiók kiválasztásához több fiókos alkalmazás is hívja a `GetAccounts()`-t. A fiók betöltésére szolgáló kód `loadAccount()`.  A felhasználó fiókjának betöltése aszinkron művelet. Így a visszahívás a fiók betöltését, változásait vagy hiba esetén felmerülő helyzeteket kezeli.

```java
/**
    * Load the currently signed-in account, if there's any.
    * In the shared device mode, if the user is signed out from the device, the app can also perform the clean-up work in onAccountChanged().
    */
private void loadAccount() {
    ...
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

A több fiókból álló alkalmazások általában interaktív módon szerzik be a jogkivonatokat, amelyek a felhasználóval kapcsolatos felhasználói FELÜLETtel rendelkeznek, és meghívja a `acquireToken()`.  A jogkivonat interaktív beolvasására szolgáló kód `initializeUI()`, a `callGraphApiInteractiveButton` kattintson a kezelő:

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

Az alkalmazásoknak nem kell minden alkalommal bejelentkezniük, amikor jogkivonatot kérnek. Ha a felhasználó már bejelentkezett, `acquireTokenSilentAsync()` lehetővé teszi, hogy az alkalmazások a felhasználó értesítése nélkül igényeljenek jogkivonatokat, `initializeUI()` ahogy az `callGraphApiSilentButton` kattintson a kezelő:

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

A fiók eltávolításához és a fiókhoz tartozó gyorsítótárazott jogkivonatokhoz tartozó kód a fiók eltávolítása gomb kezelőjében `initializeUI()` található. Mielőtt eltávolít egy fiókot, szüksége lesz egy fiók objektumra, amelyet a MSAL függvények (például a `getAccounts()` és a `acquireToken()`) szerez be. Mivel a fiókok eltávolítása aszinkron művelet, a rendszer a `onRemoved` visszahívást a felhasználói felület frissítéséhez.

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
                loadAccount();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account. JSON

Ez egy olyan MSAL-alkalmazás konfigurációs fájlja, amely több fiókot használ.

A mezők magyarázatát az [androidos MSAL konfigurációs fájljának megismerése](msal-configuration.md) című részben találja.

A [auth_config_single_account. JSON](#auth_config_single_accountjson) konfigurációs fájllal ellentétben ez a konfigurációs fájl `"account_mode" : "MULTIPLE"` helyett `"account_mode" : "SINGLE"`, mert ez egy több fiókból álló alkalmazás.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
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
