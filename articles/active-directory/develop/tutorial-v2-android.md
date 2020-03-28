---
title: Jelentkezzen be a felhasználókba & hívja a Microsoft Graph (Android) - Microsoft identity platform | Azure
description: Hozzáférési jogkivonat bekérése és a Microsoft Graph vagy API-k hívása, amelyek hez hozzáférési jogkivonatok a Microsoft identity platformról (Android)
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a5333d5a8f0972dac80efe6c641c515102a2d714
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77917934"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>Oktatóanyag: Jelentkezzen be a felhasználókhoz, és hívja fel a Microsoft Graphot androidos alkalmazásból 

>[!NOTE]
>Ez az oktatóanyag egyszerűsített példákat mutat be az Android msal-os működésére. Az egyszerűség kedvéért ez a bemutató csak egyfiókos módot használ. Megtekintheti a tárta, és klónozhatja [az előre konfigurált mintaalkalmazás](https://github.com/Azure-Samples/ms-identity-android-java/) összetettebb forgatókönyvek feltárása. Tekintse meg a [rövid útmutató](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android) további a minta alkalmazás, konfiguráció és regisztráció. 

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja androidos alkalmazását a Microsoft identity platformmal az Android Microsoft hitelesítési könyvtárával. Megtudhatja, hogyan jelentkezik be és jelentkezik ki egy felhasználó, kap egy hozzáférési jogkivonatot a Microsoft Graph API hívásához, és kérést tehet a Graph API-hoz. 

> [!div class="checklist"]
> * Az Android-alkalmazás integrálása a Microsoft Identity Platformmal 
> * Felhasználó bejelentkezése 
> * A Microsoft Graph API hívásához hozzon be egy hozzáférési jogkivonatot 
> * Hívja a Microsoft Graph API-t 
> * Felhasználó kijelentkezése 

Az oktatóanyag befejezése után az alkalmazás elfogadja a személyes Microsoft-fiókok (beleértve a outlook.com, live.com és másokat), valamint az Azure Active Directoryt használó vállalatok vagy szervezetek munkahelyi vagy iskolai fiókjainak bejelentkezését.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="how-this-tutorial-works"></a>Hogyan működik ez a bemutató?

![Bemutatja, hogyan működik az oktatóanyag által létrehozott mintaalkalmazás](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Az oktatóanyagban található alkalmazás bejelentkezik a felhasználókhoz, és adatokat kap a nevükben. Ezeket az adatokat egy védett API-n (Microsoft Graph API) keresztül fogják elérni, amely hitelesítést igényel, és amelyet a Microsoft identity platform véd.

Pontosabban:

* Az alkalmazás böngészőn vagy a Microsoft Authenticator és az Intune vállalati portálon keresztül jelentkezik be a felhasználóba.
* A végfelhasználó elfogadja az alkalmazás által kért engedélyeket.
* Az alkalmazás kap egy hozzáférési jogkivonatot a Microsoft Graph API-hoz.
* A hozzáférési jogkivonat szerepelni fog a webes API HTTP-kérelemben.
* Dolgozza fel a Microsoft Graph válaszát.

Ez a minta a Microsoft Android hitelesítési könyvtárát (MSAL) használja a hitelesítés megvalósításához: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

 Az MSAL automatikusan megújítja a jogkivonatokat, egyszeri bejelentkezést (SSO) biztosít az eszközön lévő többi alkalmazás között, és kezeli a fiók(oka)t.

### <a name="prerequisites"></a>Előfeltételek

* Ez a bemutató megköveteli az Android Studio 3.5+ verzióját

## <a name="create-a-project"></a>Projekt létrehozása
Ha még nem rendelkezik Android-alkalmazással, az alábbi lépésekkel új projektet állíthat be. 

1. Nyissa meg az Android Studio alkalmazást, és válassza **az Új Android Studio-projekt indítása**lehetőséget.
2. Válassza **az Alaptevékenység lehetőséget,** majd a **Tovább**gombot.
3. Adjon nevet az alkalmazásnak.
4. Mentse a csomag nevét. Később beírja az Azure Portalra.
5. Változtassa meg a nyelvet **Kotlin** ról **Java**.
6. Állítsa a **Minimális API-szintet** **API 19** vagy újabb szintre, majd kattintson a **Befejezés**gombra.
7. A projektnézetben válassza a **Project** lehetőséget a legördülő menüben a forrás- és nem `28`forrásprojektfájlok megjelenítéséhez, nyissa meg az **alkalmazást/build.gradle fájlt,** és állítsa a programot a beállításra. `targetSdkVersion`

## <a name="integrate-with-microsoft-authentication-library"></a>Integráció a Microsoft hitelesítési tárával 

### <a name="register-your-application"></a>Alkalmazás regisztrálása

1. Nyissa meg az [Azure Portalt.](https://aka.ms/MobileAppReg)
2. Nyissa meg az [Alkalmazásregisztrációk panelt,](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) és kattintson **az +Új regisztráció gombra.**
3. Adja meg az alkalmazás **nevét,** majd az átirányítási URI beállítása **nélkül** kattintson a **Regisztráció gombra.**
4. A megjelenő ablaktábla **Kezelés** szakaszában válassza a **Hitelesítés** > **+ Platform hozzáadása** > **Android**lehetőséget. (Előfordulhat, hogy a fűrészlap tetején ekként a "Váltás az új élményre" lehetőséget kell választania, hogy láthassa ezt a részt)
5. Adja meg a projekt csomagnevét. Ha letöltötte a kódot, `com.azuresamples.msalandroidapp`ez az érték .
6. Az **Android-alkalmazás konfigurálása** lap **Aláírás-kivonatszakaszban** kattintson **a Fejlesztési aláírás-kivonat létrehozása elemre.** és másolja a KeyTool parancsot a platformhoz való használatra.

   > [!Note]
   > A KeyTool.exe a Java Development Kit (JDK) részeként van telepítve. A KeyTool parancs végrehajtásához telepítenie kell az OpenSSL eszközt is. További információt az [Android dokumentációjában](https://developer.android.com/studio/publish/app-signing#generate-key) talál a kulcs létrehozásáról. 

7. Adja meg a KeyTool által létrehozott **aláírási kivonatot.**
8. Kattintson az `Configure` **Android konfigurációs** oldalán megjelenő **MSAL-konfigurációra,** és mentse azt, hogy később beírhassa azt az alkalmazás konfigurálásakor.  Kattintson a **Done** (Kész) gombra.

### <a name="configure-your-application"></a>Az alkalmazás konfigurálása 

1. Az Android Studio projektablaktábláján keresse meg **az app\src\main\res mappát.**
2. Kattintson a jobb gombbal, és válassza az Új > **könyvtár** **parancsot.** **New** Írja `raw` be az új könyvtárnevet, és kattintson az **OK**gombra.
3. Az **app** > **src** > **main** > **res** > **raw**, hozzon létre egy új JSON fájlt nevű, `auth_configbn_single_account.json` és illessze be a MSAL konfiguráció, amely korábban mentett. 

    Az átirányítási URI alatt illessze be a következőt: 
    ```json
      "account_mode" : "SINGLE",
    ```
    A konfigurációs fájlnak a következő példához kell hasonlítania: 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "account_mode" : "SINGLE",
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
    
   >[!NOTE]
   >Ez az oktatóanyag csak azt mutatja be, hogyan konfigurálhat egy alkalmazást egyfiókos módban. Tekintse meg a dokumentációt további információkért [az egy- és többfiókos módról,](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) és [konfigurálja az alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)
   
4. Az **alkalmazás** > **ban src** > **fő** > **AndroidManifest.xml**, add hozzá az `BrowserTabActivity` alábbi tevékenység az alkalmazás törzse. Ez a bejegyzés lehetővé teszi a Microsoft számára, hogy a hitelesítés befejezése után visszahívja az alkalmazást:

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

    Helyettesítse az Azure Portalon regisztrált `android:host=` csomagnevet az értékkel.
    Helyettesítse az Azure Portalon regisztrált `android:path=` kulcskivonatot az értékkel. Az **aláíráskivonatot nem** szabad URL-kódolással kódolni. Győződjön meg arról, hogy van egy vezető `/` elején a signature hash. 
    >[!NOTE]
    >A "Csomag neve" akkor `android:host` cserélje ki az értéket kell kinéznie: "com.azuresamples.msalandroidapp" A `android:path` "Signature Hash" akkor cserélje ki az értéket kell kinéznie: "/1wIqXSqBj7w+h11ZifsnqwgyKrY=" Ön is képes lesz megtalálni ezeket az értékeket a hitelesítési panel az alkalmazás regisztráció. Vegye figyelembe, hogy az átirányítási URI a következőhöz hasonló lesz: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Bár az aláírási kivonat az érték végén url-cím kódolású, az aláírási **kivonatnem** lehet URL-cím kódolású az értékben. `android:path` 

## <a name="use-msal"></a>Az MSAL használata 

### <a name="add-msal-to-your-project"></a>MSAL hozzáadása a projekthez

1. Az Android Studio projektablakban keresse meg az **alkalmazás** > **src** > **build.gradle-t,** és adja hozzá a következőket: 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version") 
    }
    ```
    [További információk a Microsoft Graph SDK-ról](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>A szükséges importálások 

Add hozzá a következőket az **alkalmazás** > **tetején src** > **fő**> **java** > **com.example(yourapp)** > **MainActivity.java** 

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>PublicClientApplication példányos példányosítása
#### <a name="initialize-variables"></a>Változók inicializálása 
```java
private final static String[] SCOPES = {"File.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
Az `MainActivity` osztályon belül olvassa el a következő onCreate() metódust `SingleAccountPublicClientApplication`az MSAL példányosítására a használatával.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount 

```java
//When app comes to the foreground, load existing account to determine if user is signed in 
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

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
}
```

### <a name="initializeui"></a>inicializálásUI
Hallgassa meg a gombokat és a hívási módszereket, vagy ennek megfelelően naplózza a hibákat. 
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);
        
        //Sign in user 
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });
        
        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });
        
        //Interactive 
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> Az MSAL-lal való kijelentkezés eltávolítja a felhasználóösszes ismert információját az alkalmazásból, de a felhasználó nak továbbra is lesz aktív munkamenete az eszközön. Ha a felhasználó ismét megpróbál bejelentkezni, előfordulhat, hogy bejelentkező felhasználói felületet lát, de nem kell újra megadnia a hitelesítő adatait, mert az eszközmunkamenet még aktív. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Az interaktív kérelmekhez használt visszahívás.

```java 
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Visszahívás csendes kérésekhez 
```java 
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Microsoft Graph API meghívása 

A következő kód bemutatja, hogyan lehet hívni a GraphAPI segítségével a Graph SDK. 

### <a name="callgraphapi"></a>callGraphAPI 

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Felhasználói felület hozzáadása
### <a name="activity"></a>Tevékenység 
Ha szeretné modellezni a felhasználói felület ki ezt az oktatóanyagot, az alábbi módszerek útmutatót biztosítanak a szöveg frissítéséhez és a gombok meghallgatásához.

#### <a name="updateui"></a>updateUI
A bejelentkezési állapot alapján engedélyezze/tiltsa le a gombokat, és állítsa be a szöveget.  
```java 
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>megjelenítéshiba
```java 
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
A felhasználói felületszövegben a kijelentkezést tükröző szöveg frissítésének módja. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Elrendezés 

Mintafájl `activity_main.xml` a gombok és a szövegdobozok megjelenítéséhez. 

```xml 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Az alkalmazás tesztelése

### <a name="run-locally"></a>Helyi futtatás

Az alkalmazás létrehozása és üzembe helyezése egy teszteszközre vagy emulátorra. Képesnek kell lennie arra, hogy jelentkezzen be, és szerezzen be jogkivonatokat az Azure AD-hez vagy a személyes Microsoft-fiókokhoz.

Miután bejelentkezett, az alkalmazás megjeleníti a Microsoft `/me` Graph-végpontról visszaadott adatokat.

### <a name="consent"></a>Beleegyezés

Amikor a felhasználó először jelentkezik be az alkalmazásba, a Microsoft identitása kérni fogja, hogy járuljon hozzá a kért engedélyekhez. Egyes Azure AD-bérlők letiltotta a felhasználói beleegyezést, amely megköveteli a rendszergazdák beleegyezését az összes felhasználó nevében. A forgatókönyv támogatásához vagy létre kell hoznia a saját bérlőjét, vagy rendszergazdai jóváhagyást kell kapnia. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az alkalmazásobjektumot, amelyet az [alkalmazás regisztrálása](#register-your-application) lépésben hozott létre.

## <a name="get-help"></a>Segítségkérés

Keresse fel [a Súgót és a támogatást,](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) ha problémája van ezzel az oktatóanyaggal vagy a Microsoft identitásplatformjával.

Segítsen nekünk a Microsoft identitásplatformjának fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdésű felmérés kitöltésével.

> [!div class="nextstepaction"]
> [Microsoft-identitásplatform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
