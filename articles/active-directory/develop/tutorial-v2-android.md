---
title: Android – a Microsoft identity platform – első lépések |} Az Azure
description: Android-alkalmazás hogyan hozzáférési jogkivonatot kapjon, és a Microsoft Graph API vagy a hozzáférési jogkivonatok a Microsoft identity platform szükséges API-hívás.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2bcb7d93361ccf3d83947222ab6a3937a7eedc4
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935960"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-android-app"></a>A felhasználók és a Microsoft Graph hívása Androidos alkalmazásokból

Ebben az oktatóanyagban megismerheti, hogyan Android-alkalmazás integrálása a Microsoft identity platform lesz. Pontosabban az alkalmazás lesz bejelentkeztetni egy felhasználót, a Microsoft Graph API meghívása a hozzáférési jogkivonatot kapjon és indítson egy Microsoft Graph API-t.  

Az útmutató befejezése után, az alkalmazás fogad a bejelentkezések a személyes Microsoft-fiókok (beleértve az Outlook.com-os, live.com, és mások), és a munkahelyi vagy iskolai fiókok bármely vállalat vagy szervezet, amely az Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Ebben az oktatóanyagban működése

![A mintaalkalmazás által ebben az oktatóanyagban létrehozott működését mutatja](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Ebben a példában az alkalmazás a felhasználók, és a adatok beolvasása a felhasználók nevében.  Ezeket az adatokat fogják elérni egy engedélyezési igénylő védett API (ebben az esetben a Microsoft Graph API) keresztül.

Pontosabban:

* Az alkalmazás fogja jelentkezzen be a felhasználó vagy egy böngészőben vagy a Microsoft Authenticator és az Intune vállalati portál használatával.
* A felhasználó elfogadja az alkalmazás igényelt engedélyeket. 
* Az alkalmazás fogja kiállítani egy hozzáférési jogkivonatot a Microsoft Graph API-hoz.
* A hozzáférési jogkivonatot fog szerepelni a HTTP-kérést a webes API-hoz.
* A Microsoft Graph-válasz feldolgozása.

Ebben a példában a Microsoft Authentication library for Android (MSAL) Outlookhoz megvalósítása Az MSAL automatikusan token megújítása, egyszeri Bejelentkezést biztosít az eszközön lévő más alkalmazások között, és a fiókok kezelése.

## <a name="prerequisites"></a>Előfeltételek

* Az interaktív telepítés Android Studiót használ.
* 16 vagy újabb Android megadása kötelező (19 + ajánlott).

## <a name="library"></a>Kódtár

Ez az útmutató a következő hitelesítési tárat használja:

|Kódtár|Leírás|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|A Microsoft-hitelesítési tár (MSAL)|

## <a name="set-up-your-project"></a>A projekt beállítása

Ebben az oktatóanyagban létrehoz egy új projektet. Ha ehelyett töltse le a befejezett oktatóanyaggal szeretné [letölteni a kódot](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Új projekt létrehozása

1. Nyissa meg az Android Studio, és ezek közül bármelyikre **indítsa el az új Android Studio-projekt**.
    - Ha az Android Studio már meg nyitva, válassza ki a **fájl** > **új** > **új projekt**.
2. Hagyja **üres tevékenység** , válassza ki a **tovább**.
3. Adjon nevet az alkalmazásnak, és állítsa a `Minimum API level` való **19 vagy újabb API-t**, a találatok **Befejezés**.
5. Az a `app/build.gradle`állítsa be a `targetedSdkVersion` – 27. 

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Két módon regisztrálhatja alkalmazását, a következő két szakasz leírtak szerint.

### <a name="register-your-app"></a>Alkalmazás regisztrálása

1. Nyissa meg a [az Azure portal](https://aka.ms/MobileAppReg) > Válasszon `New registration`. 
2. Adjon meg egy **neve** az alkalmazás > `Register`. **Ne állítson be egy átirányítási URI-t ezen a ponton**. 
3. Az a `Manage` nyissa meg a szakaszban `Authentication` > `Add a platform` > `Android`
    - Adja meg a projekt csomag nevét. Ha letöltötte a kódot, az értéket nem `com.azuresamples.msalandroidapp`. 
    - Adja meg a hibakeresési/fejlesztés aláírás-kivonatoló. A paranccsal KeyTool a portálon az aláírás-kivonatoló létrehozásához. 
4. Találati `Configure` és tárolja a ***MSAL konfigurációs*** későbbi használatra. 

## <a name="build-your-app"></a>Alkalmazás létrehozása

### <a name="configure-your-android-app"></a>Az Android-alkalmazás konfigurálása

1. Kattintson a jobb gombbal **res** > **új** > **mappa** > **nyers erőforrások mappájához**
2. A **alkalmazás** > **res** > **nyers**, hozzon létre egy új nevű JSON-fájlt `auth_config.json` , és illessze be a ***MSAL konfigurációs***. Lásd: [további információ az MSAL konfigurációs](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Configuring-your-app).
   <!-- Workaround for Docs conversion bug -->
3. A **alkalmazás** > **jegyzékfájlok** > **AndroidManifest.xml**, adja hozzá a `BrowserTabActivity` alábbi tevékenység. Ez a bejegyzés lehetővé teszi a Microsoft visszahívja az alkalmazáshoz, a hitelesítés befejezése után:

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

    Vegye figyelembe, a használt aláírás-kivonat nem lehet URL-kódolású a a **AndroidManifest.xml**. 

4. Belül a **AndroidManifest.xml** és feladatállapotában a `<application>` címkével, adja hozzá a következő engedélyekkel:

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    ```

5. Az a `BrowserTabActivity`, cserélje le a ***csomagnév*** és ***aláírás-kivonatoló*** azokra az értékekre, regisztrálni az Azure Portalon.

### <a name="create-the-apps-ui"></a>Az alkalmazás felhasználói felület létrehozása

1. Lépjen a **res** > **elrendezés**, majd nyissa meg **activity_main.xml**.
2. A tevékenység elrendezésének módosítása `android.support.constraint.ConstraintLayout` vagy az egyéb `LinearLayout`.
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

1. Az Android Studióban válassza a **Gradle-szkriptek** > **build.gradle (modul: alkalmazás)**.
2. A **függőségek**, illessze be a következő kódot:

    ```gradle  
    implementation 'com.android.volley:volley:1.1.1'
    implementation 'com.microsoft.identity.client:msal:0.3.+'
    ```

### <a name="use-msal"></a>Használható az MSAL 

A következő néhány szakaszban végez módosításokat belül a `MainAcitivty.java`. Azt fogjuk kell ajánljuk figyelmébe az egyes lépések hozzáadása és használható az MSAL az alkalmazásban.

#### <a name="required-imports"></a>Szükséges importálások

Az alábbi importálásokat hozzáadása a projekthez: 

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

#### <a name="instantiating-msal"></a>Instantiating MSAL 

Belül a `MainActivity` osztály kell példányosítani MSAL valamint Mik kapcsolatos néhány alkalmazás tegye a hatókörök például lesz, és webes API-t szeretnénk elérni. 

Másolja az alábbi változókat belül a `MainActivity`:

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

Most már MSAL elindítását, másolja a következő kódot a `onCreate(...)` módszer:

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

A fenti kódblokkot megpróbál bejelentkezni felhasználói beavatkozás nélkül az alkalmazás megnyitásakor `getAccounts(...)` , és ha sikeres, `acquireTokenSilentAsync(...)`.  A következő néhány szakaszban a visszahíváskezelőt Pro nincs az ügy nem bejelentkezett fiókok kell hoznunk. 

#### <a name="use-msal-to-get-tokens"></a>A jogkivonatok lekérésére az MSAL használatával

Most hogy valósítható meg az alkalmazás Kezelőfelületén feldolgozó logika és a jogkivonatok interaktív módon az MSAL használatával. 

Az MSAL jogkivonatok lekérésének lépéseiről két elsődleges módszerét mutatja: `acquireTokenSilentAsync` és `acquireToken`.  

`acquireTokenSilentAsync` jelentkezik be egy felhasználó és a get jogkivonatok, ha egy fiók található felhasználói beavatkozás nélkül. Ha ez sikeres, az MSAL lesz-e a handoff számára az alkalmazáshoz, ha ez nem sikerül a jogkivonatokat hoz létre egy `MsalUiRequiredException`.  Ha ehhez a kivételhez jön létre, vagy azt szeretné, hogy a felhasználókat, hogy rendelkezik egy interaktív bejelentkezési élmény (hitelesítő adatok, az mfa vagy más feltételes hozzáférési szabályzatok feltétlenül nem szükséges), majd használhatja `acquireToken`.  

`acquireToken` minden esetben megjelenik felhasználói felület, amikor a felhasználók bejelentkeztetése és tokenekhez; próbál azonban előfordulhat, hogy használjon a munkamenet-cookie-kat a böngészőben, vagy egy fiókot a Microsoft Authenticator alkalmazást az interaktív egyszeri bejelentkezési felületet biztosíthat. 

Első lépésként hozzon létre a következő három felhasználói felület módszerek belül a `MainActivity` osztály:

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

Ezután adjon hozzá egy metódust az aktuális tevékenység, és csendes & interaktivního okna visszahívások feldolgozásához:

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

Ezután akár, hozzáadjuk támogatása kijelentkezés, az alkalmazásnak. 

Fontos megjegyezni, kijelentkezési az MSAL eltávolítja egy felhasználó összes ismert adatait az alkalmazás, de továbbra is rendelkezni fog egy aktív munkamenet az eszközön. Ha a felhasználó megpróbál bejelentkezni újra előfordulhat, hogy tekintse meg a kapcsolati, hanem előfordulhat, hogy nem kell írja be újra a hitelesítő adatok miatt az eszköz munkamenet nem aktív. 

Kijelentkezés hozzáadása, másolja a következő metódust, amely végighalad az összes fiók, és eltávolítja azokat az alkalmazásba:

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

Miután sikeresen tartalomcsomagból, hogy egy jogkivonatot, azt egy kérelmet a Microsoft Graph API is végezhet. A hozzáférési jogkivonat helyezkednek majd el a `AuthenticationResult` belül a hitelesítés-visszahívás `onSuccess(...)` metódust. Egy jogosult kérést hozhatnak létre, az alkalmazás a hozzáférési jogkivonat hozzáadása a HTTP-fejlécben lesz szüksége:

| Fejléc kulcs    | value                 |
| ------------- | --------------------- |
| Engedélyezés | Tulajdonosi < hozzáférési jogkivonat > |

Ehhez a kódban, adjon hozzá alkalmazásához graph hívja, és a felhasználói felület frissítéséhez az alábbi két módszer: 

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

Tudjon meg többet a [Microsoft Graph API](https://graph.microsoft.com)!

#### <a name="multi-account-applications"></a>Több fiók alkalmazások

Ez az alkalmazás egyetlen fiók esetén épül. Az MSAL, valamint több fiók-forgatókönyveket teszi lehetővé, de alkalmazásokból néhány további munkát igényel. Szüksége lesz a felhasználói felület segítségével a felhasználó számára a jogkivonatok igénylő műveletek használni kívánja, hogy melyik fiók létrehozása. Azt is megteheti, az alkalmazás valósíthat meg, válassza ki, melyik fiókot szeretné használni a keresztül heurisztikát a `getAccounts(...)` metódust. 

## <a name="test-your-app"></a>Az alkalmazás tesztelése

### <a name="run-locally"></a>Helyi futtatás

Ha követte a fenti kód, próbálja meg hozhat létre és telepítse az alkalmazást a vizsgálati eszközt vagy emulátort a. Meg kell tudni jelentkezzen be, és a jogkivonatok lekérésére, az Azure ad vagy személyes Microsoft-fiókok! Után a felhasználó bejelentkezik, az alkalmazás megjeleníti az adatokat a Microsoft Graph által visszaadott `/me` végpont. 

Ha problémája van, nyugodtan nyissa meg ezt a dokumentumot, vagy az MSAL erőforrástárban problémát, és ossza meg velünk. 

### <a name="consent-to-your-app"></a>Hozzájárulás az alkalmazáshoz

Bármely felhasználó jelentkezik be az alkalmazáshoz, első alkalommal kéri a Microsoft identity hogy engedélyt adjanak az igényelt engedélyeket.  A felhasználók többsége képes a küldőnek, amelyek minden egyes Azure AD-bérlőre letiltotta a felhasználói beleegyezés - igénylő rendszergazdai jóváhagyást az összes felhasználó nevében.  Ez a forgatókönyv támogatása érdekében ügyeljen arra, az alkalmazás hatókörök regisztrálása az Azure Portalon.

## <a name="help-and-support"></a>Súgó és támogatás

Volt ebben az oktatóanyagban vagy a Microsoft identity platform bármilyen hiba? Lásd: [Súgó és támogatás](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)
