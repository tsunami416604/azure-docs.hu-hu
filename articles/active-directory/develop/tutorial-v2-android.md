---
title: Első lépések az Androidban – Microsoft Identity platform | Azure
description: Az Android-alkalmazások hozzáférési jogkivonatának beszerzése és a Microsoft Identity platform hozzáférési jogkivonatokat igénylő API-k hívása Microsoft Graph API vagy API-k esetén.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76547fd708f880bdf5167d71db121e69fc5b1d30
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823798"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>Bejelentkezés a felhasználókba és a Microsoft Graph meghívása Android-alkalmazásból

Ebből az oktatóanyagból megtudhatja, hogyan integrálhat egy Android-alkalmazást a Microsoft Identity platformmal. Az alkalmazás bejelentkezik egy felhasználóval, hozzáférési jogkivonatot kap a Microsoft Graph API meghívásához, és kérelmet küld a Microsoft Graph API-nak.  

Az útmutató elvégzése után az alkalmazás elfogadja a személyes Microsoft-fiókok (például a outlook.com, a live.com és mások) és a munkahelyi vagy iskolai fiókok bejelentkezési adatait bármely olyan vállalattól vagy szervezettől, amely Azure Active Directoryt használ.

## <a name="how-this-tutorial-works"></a>Az oktatóanyag működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Az oktatóanyagban szereplő alkalmazás bejelentkezik a felhasználók számára, és az Ön nevében kéri le az adatkérést.  Ezek az adatok egy olyan védett API-n (Microsoft Graph API) keresztül érhetők el, amely az engedélyezést igényli, és a Microsoft Identity platform védi.

Pontosabban:

* Az alkalmazás egy böngészőn vagy a Microsoft Authenticatoron keresztül fog bejelentkezni a felhasználóba, és Intune Céges portál.
* A végfelhasználó el fogja fogadni az alkalmazás által kért engedélyeket.
* Az alkalmazás egy hozzáférési jogkivonatot fog kiadni a Microsoft Graph API-hoz.
* A hozzáférési jogkivonatot a rendszer a webes API-hoz tartozó HTTP-kérelemben fogja tartalmazni.
* Dolgozza fel a Microsoft Graph választ.

Ez a példa az Androidhoz készült Microsoft Authentication Library (MSAL) használatával valósítja meg a hitelesítést: [com. microsoft. Identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 A MSAL automatikusan megújítja a tokeneket, egyszeri bejelentkezést (SSO) tesz elérhetővé az eszköz más alkalmazásai között, és felügyeli a fiók (oka) t.

## <a name="prerequisites"></a>Előfeltételek

* Ehhez az oktatóanyaghoz Android Studio 16-os vagy újabb verzió szükséges (19 + ajánlott).

## <a name="create-a-project"></a>Projekt létrehozása

Ez az oktatóanyag egy új projektet fog létrehozni. Ha ehelyett a kész oktatóanyagot szeretné letölteni, [töltse le a kódot](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

1. Nyissa meg Android Studio, majd válassza **az új Android Studio projekt indítása**lehetőséget.
2. Válassza az alaptevékenység lehetőséget, majd kattintson a **Tovább gombra**.
3. Adjon nevet az alkalmazásnak.
4. Mentse a csomag nevét. Később adja meg a Azure Portal.
5. Állítsa a **minimális API** -szintet **API 19** vagy újabb értékre, majd kattintson a **Befejezés**gombra.
6. A Project nézetben a legördülő menüben válassza a **projekt** lehetőséget a forrás-és a nem forrásként szolgáló projektfájlok megjelenítéséhez, majd nyissa meg `targetSdkVersion` az `27` **app/Build. gradle** fájlt, és állítsa a következőre:.

## <a name="register-your-application"></a>Alkalmazás regisztrálása

1. Nyissa meg az [Azure Portal](https://aka.ms/MobileAppReg).
2. Nyissa meg a [Alkalmazásregisztrációk](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) panelt, és kattintson az **+ új regisztráció**elemre.
3. Adja meg az alkalmazás **nevét** , majd az átirányítási URI beállítása nélkül kattintson a **regisztráció**elemre.
4. A megjelenő panel **kezelés** szakaszában válassza a **hitelesítés** >  **+ platform** > hozzáadása**Android**lehetőséget.
5. Adja meg a projekt csomagjának nevét. Ha letöltötte a kódot, akkor ez az `com.azuresamples.msalandroidapp`érték.
6. Az **Android-alkalmazás konfigurálása** lap **aláírás-kivonatolás** szakaszában kattintson a **fejlesztési aláírás kivonatának létrehozása** lehetőségre. és másolja a Főeszköz parancsot a platformhoz való használatra.

   > [!Note]
   > A. exe a Java Development Kit (JDK) részeként van telepítve. Az OpenSSL eszközt is telepítenie kell, hogy végrehajtsa a parancssori eszközt.

7. Adja meg a Főeszköz által generált aláírási kivonatot.
8. Kattintson `Configure` és mentse az **Android-konfiguráció** lapon megjelenő **MSAL** -konfigurációt, így megadhatja, amikor később konfigurálja az alkalmazást.  Kattintson a **Done** (Kész) gombra.

## <a name="build-your-app"></a>Alkalmazás létrehozása

### <a name="add-your-app-registration"></a>Az alkalmazás regisztrációjának hozzáadása

1. A Android Studio projekt ablaktábláján navigáljon a **app\src\main\res**elemre.
2. Kattintson a jobb gombbal a **res** elemre, és válassza az **új** > **könyvtár**lehetőséget. Adja `raw` meg az új könyvtárnév nevet, majd kattintson az **OK**gombra.
3. Az **app** >  `auth_config.json` **src** **res** **RAW**-ben hozzon létre egy nevű új JSON-fájlt, és illessze be a korábban mentett MSAL-konfigurációt. >  >  [További információért](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app)lásd: MSAL-konfiguráció.
4. Az **app** >  `BrowserTabActivity` **src** **Main** **AndroidManifest. XML fájljában**adja hozzá az alábbi tevékenységet. >  >  Ez a bejegyzés lehetővé teszi, hogy a Microsoft visszahívjon az alkalmazásba a hitelesítés befejezése után:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Helyettesítse be a Azure Portal regisztrált csomag nevét az `android:host=` értékhez.
    Helyettesítse be a Azure Portalban `android:path=` regisztrált kulcs kivonatát az értékhez. Az aláírási kivonat nem lehet URL-kódolású.

5. A **AndroidManifest. xml fájlban**, közvetlenül a `<application>` címke felett adja hozzá a következő engedélyeket:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>Az alkalmazás felhasználói felületének létrehozása

1. A Android Studio projekt ablakban navigáljon az **app** > **src** > **Main** > **res** > **elrendezéshez** , és nyissa meg a **activity_main. xml fájlt** , és nyissa meg a **szöveget.** nézet.
2. Módosítsa a tevékenység elrendezését, például: `<androidx.coordinatorlayout.widget.CoordinatorLayout`. `<androidx.coordinatorlayout.widget.LinearLayout`
3. Adja hozzá `android:orientation="vertical"` a tulajdonságot `LinearLayout` a csomóponthoz.
4. Illessze be a következő kódot a `LinearLayout` csomópontba, és cserélje le az aktuális tartalmat:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```

### <a name="add-msal-to-your-project"></a>MSAL hozzáadása a projekthez

1. A Android Studio projekt ablakban navigáljon az **app** > **src** > **Build. gradle**elemre.
2. A **függőségek**alatt illessze be a következőt:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>MSAL használata

Most végezze `MainActivity.java` el a módosításokat a MSAL hozzáadásához és használatához az alkalmazásban.
A Android Studio projekt ablakban navigáljon az **app** > **src** > **Main** > **Java** > **com. example. msal**elemre, és `MainActivity.java`nyissa meg a következőt:.

#### <a name="required-imports"></a>Szükséges importálások

Adja hozzá az alábbi importálásokat a tetején `MainActivity.java`a következők közelében:

```java
import android.app.Activity;
import android.content.Intent;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import com.android.volley.*;
import com.android.volley.toolbox.JsonObjectRequest;
import com.android.volley.toolbox.Volley;
import org.json.JSONObject;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

#### <a name="instantiate-msal"></a>Instantiate MSAL

Az osztályban `MainActivity` a MSAL-t is létre kell hoznia néhány konfigurációval, hogy mi az alkalmazás, beleértve a hatóköreit és a webes API-t, melyeket szeretnénk elérni.

Másolja a következő változókat a `MainActivity` osztályba:

```java
final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

/* UI & Debugging Variables */
private static final String TAG = MainActivity.class.getSimpleName();
Button callGraphButton;
Button signOutButton;

/* Azure AD Variables */
private PublicClientApplication sampleApp;
private IAuthenticationResult authResult;
```

Cserélje le a tartalmát `onCreate()` a következő kódra a MSAL létrehozásához:

```java
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

callGraphButton = (Button) findViewById(R.id.callGraph);
signOutButton = (Button) findViewById(R.id.clearCache);

callGraphButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onCallGraphClicked();
    }
});

signOutButton.setOnClickListener(new View.OnClickListener() {
    public void onClick(View v) {
        onSignOutClicked();
    }
});

/* Configure your sample app and save state for this activity */
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);

/* Attempt to get a user and acquireTokenSilent */
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {
        if (!accounts.isEmpty()) {
            /* This sample doesn't support multi-account scenarios, use the first account */
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts */
        }
    }
});
```

A fenti kód a felhasználók csendes bejelentkezését kísérli meg, amikor a `getAccounts()` és a `acquireTokenSilentAsync()`sikeres módon megnyitják az alkalmazást.  A következő néhány szakaszban végre fogjuk hajtani a visszahívási kezelőt arra az esetre, ha nincsenek bejelentkezett fiókok.

#### <a name="use-msal-to-get-tokens"></a>A MSAL használata a jogkivonatok lekéréséhez

Most megvalósíthatja az alkalmazás felhasználói felületének feldolgozási logikáját, és interaktív módon lekérdezheti a tokeneket a MSAL-on keresztül.

A MSAL két elsődleges módszert tesz elérhetővé a tokenek `acquireTokenSilentAsync()` beszerzéséhez: és `acquireToken()`.  

`acquireTokenSilentAsync()`bejelentkezik egy felhasználóba, és felhasználói beavatkozás nélkül kap jogkivonatokat, ha van ilyen fiókja. Ha ez sikeres, a MSAL handoff a tokeneket az alkalmazásba, ha a művelet meghiúsul, akkor a rendszer `MsalUiRequiredException`létrehoz egy-t.  Ha ezt a kivételt létrehozták, vagy azt szeretné, hogy a felhasználó interaktív bejelentkezési élményt biztosítson (hitelesítő adatok, MFA vagy más feltételes hozzáférési szabályzatok vagy esetleg nem szükségesek), `acquireToken()`használja a következőt:.  

`acquireToken()`felhasználói FELÜLETET jelenít meg, amikor megpróbál bejelentkezni a felhasználóba, és jogkivonatokat kap. A böngészőben azonban munkamenet-cookie-kat, illetve a Microsoft-hitelesítő egyik fiókját is használhatja az interaktív egyszeri bejelentkezés élményének biztosításához.

Hozza létre a következő három felhasználóifelület-módszert `MainActivity` az osztályban:

```java
/* Set the UI for successful token acquisition data */
private void updateSuccessUI() {
    callGraphButton.setVisibility(View.INVISIBLE);
    signOutButton.setVisibility(View.VISIBLE);
    findViewById(R.id.welcome).setVisibility(View.VISIBLE);
    ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
            authResult.getAccount().getUsername());
    findViewById(R.id.graphData).setVisibility(View.VISIBLE);
}

/* Set the UI for signed out account */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");

    Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
            .show();
}

/* Use MSAL to acquireToken for the end-user
 * Callback will call Graph api w/ access token & update UI
 */
private void onCallGraphClicked() {
    sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
}
```

Adja hozzá a következő metódusokat az aktuális tevékenység beszerzéséhez és a folyamat csendes & interaktív visszahívásához:

```java
public Activity getActivity() {
    return this;
}

/* Callback used in for silent acquireToken calls.
 * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
 * else errors that we need to do an interactive request.
 */
private AuthenticationCallback getAuthSilentCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");

            /* Store the authResult */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            } else if (exception instanceof MsalUiRequiredException) {
                /* Tokens expired or no session, retry with interactive */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}

/* Callback used for interactive request.  If succeeds we use the access
 * token to call the Microsoft Graph. Does not check cache
 */
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, call graph now */
            Log.d(TAG, "Successfully authenticated");
            Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

            /* Store the auth result */
            authResult = authenticationResult;

            /* call graph */
            callGraphAPI();

            /* update the UI to post call graph state */
            updateSuccessUI();
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());

            if (exception instanceof MsalClientException) {
                /* Exception inside MSAL, more info inside the exception */
            } else if (exception instanceof MsalServiceException) {
                /* Exception when communicating with the STS, likely config issue */
            }
        }

        @Override
        public void onCancel() {
            /* User cancelled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

#### <a name="use-msal-for-sign-out"></a>MSAL használata a kijelentkezéshez

Ezután vegyen fel támogatást a kijelentkezéshez.

> [!Important]
> A MSAL-ből való kijelentkezés eltávolítja az alkalmazással kapcsolatos összes ismert információt, de a felhasználó továbbra is aktív munkamenettel fog rendelkezni az eszközön. Ha a felhasználó ismét megkísérli a bejelentkezést, láthatják a bejelentkezési felhasználói felületet, de előfordulhat, hogy nem kell újra megadniuk a hitelesítő adataikat, mert az eszköz munkamenete még aktív.

A kijelentkezési képesség hozzáadásához adja hozzá a következő metódust `MainActivity` a osztályon belül. Ez a módszer az összes fiókra váltást hajt végre, és eltávolítja azokat:

```java
/* Clears an account's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 * User will get interactive SSO if trying to sign back-in.
 */
private void onSignOutClicked() {
    /* Attempt to get a user and acquireTokenSilent
     * If this fails we do an interactive request
     */
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (accounts.isEmpty()) {
                /* No accounts to remove */

            } else {
                for (final IAccount account : accounts) {
                    sampleApp.removeAccount(
                            account,
                            new PublicClientApplication.AccountsRemovedCallback() {
                        @Override
                        public void onAccountsRemoved(Boolean isSuccess) {
                            if (isSuccess) {
                                /* successfully removed account */
                            } else {
                                /* failed to remove account */
                            }
                        }
                    });
                }
            }

            updateSignedOutUI();
        }
    });
}
```

#### <a name="call-the-microsoft-graph-api"></a>A Microsoft Graph API meghívása

Ha megkaptuk a jogkivonatot, a hozzáférési token `AuthenticationResult` a hitelesítési visszahívási `onSuccess()` metóduson belül található [Microsoft Graph API](https://graph.microsoft.com) -nak. Egy jogosult kérelem létrehozásához az alkalmazásnak hozzá kell adnia a hozzáférési jogkivonatot a HTTP-fejléchez:

| fejléc kulcsa    | value                 |
| ------------- | --------------------- |
| Authorization | Tulajdonosi \<hozzáférés – token > |

Adja hozzá a következő két metódust `MainActivity` az osztályban a Graph meghívásához és a felhasználói felület frissítéséhez:

```java
/* Use Volley to make an HTTP request to the /me endpoint from MS Graph using an access token */
private void callGraphAPI() {
    Log.d(TAG, "Starting volley request to graph");

    /* Make sure we have a token to send to graph */
    if (authResult.getAccessToken() == null) {return;}

    RequestQueue queue = Volley.newRequestQueue(this);
    JSONObject parameters = new JSONObject();

    try {
        parameters.put("key", "value");
    } catch (Exception e) {
        Log.d(TAG, "Failed to put parameters: " + e.toString());
    }
    JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
            parameters,new Response.Listener<JSONObject>() {
        @Override
        public void onResponse(JSONObject response) {
            /* Successfully called graph, process data and send to UI */
            Log.d(TAG, "Response: " + response.toString());

            updateGraphUI(response);
        }
    }, new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            Log.d(TAG, "Error: " + error.toString());
        }
    }) {
        @Override
        public Map<String, String> getHeaders() {
            Map<String, String> headers = new HashMap<>();
            headers.put("Authorization", "Bearer " + authResult.getAccessToken());
            return headers;
        }
    };

    Log.d(TAG, "Adding HTTP GET to Queue, Request: " + request.toString());

    request.setRetryPolicy(new DefaultRetryPolicy(
            3000,
            DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
            DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
    queue.add(request);
}

/* Sets the graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```

#### <a name="multi-account-applications"></a>Több fiókból álló alkalmazások

Ez az alkalmazás egyetlen fiókra épül. A MSAL támogatja a többfiókos forgatókönyveket is, de az alkalmazások további munkája szükséges. Létre kell hoznia egy felhasználói FELÜLETET, amellyel a felhasználó kiválaszthatja, hogy melyik fiókot szeretné használni a jogkivonatokat igénylő műveletekhez. Azt is megteheti, hogy az alkalmazás egy heurisztikus módszert alkalmaz, amellyel kiválaszthatja, hogy melyik fiókot szeretné használni a `getAccounts()` metódus segítségével.

## <a name="test-your-app"></a>Az alkalmazás tesztelése

### <a name="run-locally"></a>Helyi futtatás

Az alkalmazás létrehozása és üzembe helyezése tesztelési eszközön vagy emulátoron. A bejelentkezéshez és az Azure AD-vagy személyes Microsoft-fiókokhoz tartozó jogkivonatok beszerzéséhez be kell tudnia jelentkezni.

A bejelentkezést követően az alkalmazás megjeleníti az Microsoft Graph `/me` végpont által visszaadott adatok megjelenítését.

### <a name="consent"></a>Hozzájárulás

Amikor a felhasználó először jelentkezik be az alkalmazásba, a Microsoft Identity a kért engedélyekkel való beleegyezett.  Habár a legtöbb felhasználó képes hozzájárulni, néhány Azure AD-bérlő letiltotta a felhasználói beleegyezését, amelyhez a rendszergazdáknak minden felhasználó nevében hozzá kell járulniuk. A forgatókönyv támogatásához regisztrálja az alkalmazás hatóköreit a Azure Portalban.

## <a name="get-help"></a>Segítségkérés

Ha problémája van az Oktatóanyaggal vagy a Microsoft Identity platformmal, látogasson el a [Súgó és támogatás](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) webhelyre.

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)