---
title: Android – a Microsoft identity platform – első lépések |} Az Azure
description: Android-alkalmazás hogyan hozzáférési jogkivonatot kapjon, és a Microsoft Graph API vagy a hozzáférési jogkivonatok a Microsoft identity platform szükséges API-hívás.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
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
ms.openlocfilehash: 71c6b0d4cd664b12dbd0fbd4e9423240c8dbebb3
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723817"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>A felhasználók és a Microsoft Graph hívása Androidos alkalmazásokból

Ebben az oktatóanyagban elsajátíthatja a Android-alkalmazás integrálása a Microsoft identity platform lesz. Az alkalmazás bejelentkeztetni egy felhasználót, a Microsoft Graph API meghívása a hozzáférési jogkivonatot kapjon, és indítson egy Microsoft Graph API-t.  

Az útmutató befejezése után, az alkalmazás fogad a bejelentkezések a személyes Microsoft-fiókok (beleértve az Outlook.com-os, live.com, és mások), és a munkahelyi vagy iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Ebben az oktatóanyagban működése

![A mintaalkalmazás által ebben az oktatóanyagban létrehozott működését mutatja](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Ebben a példában az alkalmazás a felhasználók, és a adatok beolvasása a felhasználók nevében.  Ezeket az adatokat fogják elérni egy védett API-val (Microsoft Graph API), amely engedélyt igényel.

Pontosabban:

* Az alkalmazás fogja jelentkezzen be a felhasználó vagy egy böngészőben vagy a Microsoft Authenticator és az Intune vállalati portál használatával.
* A felhasználó elfogadja az alkalmazás igényelt engedélyeket. 
* Az alkalmazás fogja kiállítani egy hozzáférési jogkivonatot a Microsoft Graph API-hoz.
* A hozzáférési jogkivonatot fog szerepelni a HTTP-kérést a webes API-hoz.
* A Microsoft Graph-válasz feldolgozása.

Ez a minta a Microsoft Authentication library for Android (MSAL) használja a hitelesítés végrehajtásához. Az MSAL automatikusan token megújítása, egyszeri bejelentkezés (SSO) az eszközön lévő más alkalmazások között szállítja és kezeli a fiók(ok) lesz.

## <a name="prerequisites"></a>Előfeltételek

* Az interaktív telepítés Android Studiót használ.
* 16 vagy újabb Android megadása kötelező (19 + ajánlott).

## <a name="library"></a>Erőforrástár

Ez az útmutató a következő hitelesítési tárat használja:

|Erőforrástár|Leírás|
|---|---|
|[com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|

## <a name="create-a-project"></a>Projekt létrehozása

Ebben az oktatóanyagban létrehoz egy új projektet. Ha ehelyett töltse le a befejezett oktatóanyaggal szeretné [letölteni a kódot](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

1. Nyissa meg az Android Studio, és válassza ki **indítsa el az új Android Studio-projekt**
2. Válassza ki **alapszintű tevékenység** kattintson **tovább**.
3. Adjon nevet az alkalmazásnak
4. Mentse a csomag nevét. Megadja azt később be az Azure Portalra. 
5. Állítsa be a **minimális API-szintet** való **API 19** vagy újabb, és kattintson a **Befejezés**.
6. A projekt nézetben válassza ki a **projekt** nyissa meg a forrás- és nem-source project fájlok megjelenítése legördülő **app/build.gradle** és állítsa be `targetSdkVersion` való `27`.

## <a name="register-your-application"></a>Alkalmazás regisztrálása

1. Nyissa meg az [Azure Portalt](https://aka.ms/MobileAppReg)
2. Nyissa meg a [regisztrációk panelére](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) kattintson **+ új regisztrációs**.
3. Adjon meg egy **neve** , az alkalmazást, majd egy átirányítási URI megadása nélkül, kattintson a **regisztrálása**.
4. Az a **kezelés** a panel, amely akkor jelenik meg, válassza ki a szakasz **hitelesítési** >  **+ hozzáadása egy platform** > **Android**.
5. Adja meg a projekt csomag nevét. Ha letöltötte a kódot, az értéket nem `com.azuresamples.msalandroidapp`.
6. Az a **aláírás-kivonatoló** szakaszában a **az Android-alkalmazás konfigurálása** kattintson **egy fejlesztési célú aláírás-kivonatoló létrehozása.** és másolja a KeyTool parancs használata a platformhoz. Ne feledje, a Java fejlesztői készlet (JDK) részeként telepíti a KeyTool.exe és is telepítenie kell az OpenSSL eszköz KeyTool parancs végrehajtása.
7. Adja meg a **aláírás-kivonatoló** KeyTool által generált.
8. Kattintson a `Configure` , és mentse a **MSAL konfigurációs** , amely megjelenik **Android-eszközök konfigurációs** lapon, hogy meg lehessen adni azt az alkalmazás későbbi konfigurálásakor.  Kattintson a **Done** (Kész) gombra.

## <a name="build-your-app"></a>Alkalmazás létrehozása

### <a name="configure-your-android-app"></a>Az Android-alkalmazás konfigurálása

1. Az Android Studio project panelen lépjen **app\src\main\res**.
2. Kattintson a jobb gombbal **res** válassza **új** > **Directory**. Adja meg `raw` új könyvtár neve, kattintson **OK**.
3. A **alkalmazás** > **src** > **res** > **nyers**, hozzon létre egy új JSON-fájlt nevű `auth_config.json`, és illessze be a korábban mentett MSAL konfigurációt. Lásd: [további információ az MSAL konfigurációs](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
   <!-- Workaround for Docs conversion bug -->
4. A **alkalmazás** > **src** > **fő** > **AndroidManifest.xml**, adja hozzá a `BrowserTabActivity`alábbi tevékenység. Ez a bejegyzés lehetővé teszi a Microsoft visszahívja az alkalmazáshoz, a hitelesítés befejezése után:

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

    Helyettesítse be a csomag nevét, az Azure Portalon regisztrálta a `android:host=` értéket.
    Helyettesítse be az Azure Portalon regisztrálta a kulcskivonat a `android:path=` értéket. Az aláírás-kivonatoló nem lehet URL-kódolású.

5. Belül a **AndroidManifest.xml**, feladatállapotában a `<application>` címkével, adja hozzá a következő engedélyekkel:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

### <a name="create-the-apps-ui"></a>Az alkalmazás felhasználói felület létrehozása

1. Az Android Studio projekt ablakról, lépjen a **alkalmazás** > **src** > **fő** > **res**  >  **elrendezés** , és nyissa meg **activity_main.xml** , és nyissa meg a **szöveg** megtekintése.
2. Lépjen a tevékenység elrendezés például `<androidx.coordinatorlayout.widget.CoordinatorLayout` való `<androidx.coordinatorlayout.widget.LinearLayout`.
3. Adja hozzá a `android:orientation="vertical"` tulajdonságot a `LinearLayout` csomópont.
4. Illessze be az alábbi kódot a `LinearLayout` csomópontot, és cserélje le a jelenlegi tartalomról:

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

### <a name="add-msal-to-your-project"></a>Az MSAL hozzáadása a projekthez

1. Az Android Studio projekt ablakról, lépjen a **alkalmazás** > **src** > **build.gradle**.
2. A **függőségek**, illessze be a következőt:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Használható az MSAL

Most végezze el a módosításokat belül `MainActivity.java` hozzáadása és használható az MSAL az alkalmazásban.
Az Android Studio projekt ablakról, lépjen a **alkalmazás** > **src** > **fő** > **java**  >  **com.example.msal**, és nyissa meg a `MainActivity.java`

#### <a name="required-imports"></a>Szükséges importálások

Adja hozzá az alábbi importálásokat tetején `MainActivity.java`:

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

Belül a `MainActivity` osztály kell példányosítani MSAL valamint Mik kapcsolatos néhány alkalmazás tegye a hatókörök például lesz, és webes API-t szeretnénk elérni.

Másolja az alábbi változókat belül a `MainActivity` osztály:

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

Cserélje le a tartalmát `onCreate()` MSAL példányt létrehozni a következő kóddal:

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

A fenti kód megpróbál bejelentkezni felhasználói beavatkozás nélkül az alkalmazás megnyitásakor `getAccounts()` , és ha sikeres, `acquireTokenSilentAsync()`.  A következő néhány szakaszban a visszahíváskezelőt Pro nincs az ügy nem bejelentkezett fiókok kell hoznunk.

#### <a name="use-msal-to-get-tokens"></a>A jogkivonatok lekérésére az MSAL használatával

Most hogy valósítható meg az alkalmazás Kezelőfelületén feldolgozó logika és a jogkivonatok interaktív módon az MSAL használatával.

Az MSAL jogkivonatok lekérésének lépéseiről két elsődleges módszerét mutatja: `acquireTokenSilentAsync()` és `acquireToken()`.  

`acquireTokenSilentAsync()` jelentkezik be egy felhasználó és a get jogkivonatok, ha egy fiók található felhasználói beavatkozás nélkül. Ha ez sikeres, az MSAL lesz-e a handoff számára az alkalmazáshoz, ha ez nem sikerül a jogkivonatokat hoz létre egy `MsalUiRequiredException`.  Ha ehhez a kivételhez jön létre, vagy azt szeretné, hogy a felhasználót, hogy rendelkezik egy interaktív bejelentkezési élmény (hitelesítő adatok, az mfa vagy más feltételes hozzáférési szabályzatok feltétlenül nem szükséges), majd a `acquireToken()`.  

`acquireToken()` felhasználói Felületet jeleníti meg, jelentkezzen be a felhasználó és a jogkivonatok lekérésére tett kísérlet közben. Mégis előfordulhat, hogy használni munkamenet-cookie-kat a böngészőben, vagy egy fiókot a Microsoft Authenticator alkalmazást, az a interaktív egyszeri bejelentkezési élményt.

Az alábbi három felhasználói felület módszerrel belül hozzon létre a `MainActivity` osztály:

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

Adja hozzá a következő módszerek lekérése az aktuális tevékenység, és a csendes & interaktivního okna visszahívások feldolgozásához:

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

#### <a name="use-msal-for-sign-out"></a>Használható az MSAL a Kijelentkezés

Ezután adja hozzá a támogatása kijelentkezést.

> [!Important]
> A felhasználó az összes ismert információ lehet kijelentkezni az MSAL eltávolítja az alkalmazást, de továbbra is rendelkezni fog egy aktív munkamenet az eszköz. Ha a felhasználó megpróbál bejelentkezni újra előfordulhat, hogy tekintse meg a bejelentkezési felhasználói felület, hanem előfordulhat, hogy nem kell a hitelesítő adatait adja meg újból, mert az eszköz munkamenet továbbra is aktív.

Kijelentkezési képesség felvételéhez adja hozzá a következő metódust belül a `MainActivity` osztály. Ez a módszer Váltás az összes fiókot, és eltávolítja azokat a:

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

Miután megkaptuk a jogkivonatot, kérést is létrehozunk a [Microsoft Graph API](https://graph.microsoft.com) a hozzáférési jogkivonat helyezkednek majd el a `AuthenticationResult` belül a hitelesítés-visszahívás `onSuccess()` metódust. Egy jogosult kérést hozhatnak létre, az alkalmazás a hozzáférési jogkivonat hozzáadása a HTTP-fejlécben lesz szüksége:

| Fejléc kulcs    | value                 |
| ------------- | --------------------- |
| Authorization | Tulajdonosi \<hozzáférési jogkivonat > |

Adja hozzá az alábbi két módszer belül a `MainActivity` osztály graph hívja, és frissíti a felhasználói felületen:

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

#### <a name="multi-account-applications"></a>Több fiók alkalmazások

Ez az alkalmazás egyetlen fiók esetén épül. Az MSAL is több fiók-forgatókönyveket teszi lehetővé, de alkalmazásokból néhány további munkát igényel. Szüksége lesz a felhasználói felület segítségével a felhasználó számára a jogkivonatok igénylő műveletek használni kívánja, hogy melyik fiók létrehozása. Azt is megteheti, az alkalmazás valósíthat meg, válassza ki, melyik fiókot szeretné használni a keresztül heurisztikát a `getAccounts()` metódust.

## <a name="test-your-app"></a>Az alkalmazás tesztelése

### <a name="run-locally"></a>Helyi futtatás

Hozhat létre és telepítse az alkalmazást a vizsgálati eszközt vagy emulátort a. Meg kell tudni jelentkezzen be, és a jogkivonatok lekérésére, az Azure ad vagy személyes Microsoft-fiókok.

Miután bejelentkezett, az alkalmazás megjeleníti az adatokat a Microsoft Graph által visszaadott `/me` végpont.

### <a name="consent"></a>Hozzájárulás megadása

Bármely felhasználó jelentkezik be az alkalmazáshoz, első alkalommal kéri a Microsoft identity hogy engedélyt adjanak az igényelt engedélyeket.  A felhasználók többsége képes a küldőnek, amelyek minden egyes Azure AD-bérlőre letiltotta a felhasználói beleegyezés, amelyhez szükség van a rendszergazdai jóváhagyást az összes felhasználó nevében. Ez a forgatókönyv támogatása érdekében az alkalmazás hatókörök regisztrálja az Azure Portalon.

## <a name="get-help"></a>Segítségkérés

Látogasson el [Súgó és támogatás](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) Ha nem ez az oktatóanyag vagy a Microsoft identitásplatformjához.
