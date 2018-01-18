
## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>A Microsoft Graph API a szolgáltatáshitelesítést egy token MSAL segítségével

1.  A **app** > **java** > **{tartomány}. { AppName}**, nyissa meg `MainActivity`. 
2.  Adja hozzá az alábbi importálásokat:

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
    ```

3. Cserélje le a `MainActivity` osztály a következő kóddal:

    ```java
    public class MainActivity extends AppCompatActivity {

        final static String CLIENT_ID = "[Enter the application Id here]";
        final static String SCOPES [] = {"https://graph.microsoft.com/User.Read"};
        final static String MSGRAPH_URL = "https://graph.microsoft.com/v1.0/me";

        /* UI & Debugging Variables */
        private static final String TAG = MainActivity.class.getSimpleName();
        Button callGraphButton;
        Button signOutButton;

        /* Azure AD Variables */
        private PublicClientApplication sampleApp;
        private AuthenticationResult authResult;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
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
            sampleApp = null;
            if (sampleApp == null) {
                sampleApp = new PublicClientApplication(
                        this.getApplicationContext(),
                        CLIENT_ID);
            }

    /* Attempt to get a user and acquireTokenSilent
    * If this fails we do an interactive request
    */
            List<User> users = null;

            try {
                users = sampleApp.getUsers();

                if (users != null && users.size() == 1) {
            /* We have 1 user */

                    sampleApp.acquireTokenSilentAsync(SCOPES, users.get(0), getAuthSilentCallback());
                } else {
            /* We have no user */

            /* Let's do an interactive request */
                    sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
                }
            } catch (MsalClientException e) {
                Log.d(TAG, "MSAL Exception Generated while getting users: " + e.toString());

            } catch (IndexOutOfBoundsException e) {
                Log.d(TAG, "User at this position does not exist: " + e.toString());
            }

        }

    //
    // App callbacks for MSAL
    // ======================
    // getActivity() - returns activity so we can acquireToken within a callback
    // getAuthSilentCallback() - callback defined to handle acquireTokenSilent() case
    // getAuthInteractiveCallback() - callback defined to handle acquireToken() case
    //

        public Activity getActivity() {
            return this;
        }

        /* Callback method for acquireTokenSilent calls 
        * Looks if tokens are in the cache (refreshes if necessary and if we don't forceRefresh)
        * else errors that we need to do an interactive request.
        */
        private AuthenticationCallback getAuthSilentCallback() {
            return new AuthenticationCallback() {
                @Override
                public void onSuccess(AuthenticationResult authenticationResult) {
                /* Successfully got a token, call Graph now */
                    Log.d(TAG, "Successfully authenticated");

                /* Store the authResult */
                    authResult = authenticationResult;

                /* call graph */
                    callGraphAPI();

                /* update the UI to post call Graph state */
                    updateSuccessUI();
                }

                @Override
                public void onError(MsalException exception) {
                /* Failed to acquireToken */
                    Log.d(TAG, "Authentication failed: " + exception.toString());

                    if (exception instanceof MsalClientException) {
                    /* Exception inside MSAL, more info inside MsalError.java */
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
                public void onSuccess(AuthenticationResult authenticationResult) {
                /* Successfully got a token, call graph now */
                    Log.d(TAG, "Successfully authenticated");
                    Log.d(TAG, "ID Token: " + authenticationResult.getIdToken());

                /* Store the auth result */
                    authResult = authenticationResult;

                /* call Graph */
                    callGraphAPI();

                /* update the UI to post call Graph state */
                    updateSuccessUI();
                }

                @Override
                public void onError(MsalException exception) {
                /* Failed to acquireToken */
                    Log.d(TAG, "Authentication failed: " + exception.toString());

                    if (exception instanceof MsalClientException) {
                    /* Exception inside MSAL, more info inside MsalError.java */
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

        /* Set the UI for successful token acquisition data */
        private void updateSuccessUI() {
            callGraphButton.setVisibility(View.INVISIBLE);
            signOutButton.setVisibility(View.VISIBLE);
            findViewById(R.id.welcome).setVisibility(View.VISIBLE);
            ((TextView) findViewById(R.id.welcome)).setText("Welcome, " +
                    authResult.getUser().getName());
            findViewById(R.id.graphData).setVisibility(View.VISIBLE);
        }

        /* Use MSAL to acquireToken for the end-user
        * Callback will call Graph api w/ access token & update UI
        */
        private void onCallGraphClicked() {
            sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
        }

        /* Handles the redirect from the System Browser */
        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            sampleApp.handleInteractiveRequestRedirect(requestCode, resultCode, data);
        }

    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>További információ
#### <a name="get-a-user-token-interactively"></a>A felhasználó interaktív token beszerzése
Hívja a `AcquireTokenAsync` módszer eredményezi egy ablak, amely felszólítja a felhasználók jelentkezhetnek be. Alkalmazások általában a felhasználóknak az első alkalommal védett erőforrás eléréséhez szükséges interaktív bejelentkezéshez. Előfordulhat, hogy is szükségük jelentkezzen be, amikor egy csendes jogkivonat megszerzése sikertelen (például, ha a jelszó lejárt).

#### <a name="get-a-user-token-silently"></a>A felhasználói beavatkozás nélkül token beszerzése
A `AcquireTokenSilentAsync` metódus kezeli a token kérése és megújításokat felhasználói beavatkozás nélkül. Miután `AcquireTokenAsync` végrehajtása az első alkalommal `AcquireTokenSilentAsync` lekérdezni a jogkivonatokat, amelyek további hívások, a védett erőforrások eléréséhez, vagy megújítása jogkivonatok hívások csendes válnak, mert a szokásos módszer.

Végül a `AcquireTokenSilentAsync` metódus sikertelen lesz. A hiba oka lehet, hogy a felhasználó rendelkezik-e vagy kijelentkezteti, vagy módosítani a jelszavát egy másik eszközön. Amikor MSAL azt észleli, hogy a probléma megoldásához érdemes egy interaktív műveletet igénylő, akkor következik be egy `MsalUiRequiredException` kivétel. Az alkalmazás kezeli ezt a kivételt, két módon:

* Az elleni hívás kezdeményezése `AcquireTokenAsync` azonnal. A hívás eredménye jelentkezzen be a felhasználótól. Ebben a mintában általában az online alkalmazások használják, amennyiben a felhasználó nem érhető el kapcsolat nélküli tartalmat. A minta az interaktív telepítő által létrehozott követi, ebben a mintában a művelet az első alkalommal a minta végrehajtása meg. 
    * Mivel a felhasználó nem használta az alkalmazás `PublicClientApp.Users.FirstOrDefault()` null értéket tartalmaz, és egy `MsalUiRequiredException` kivétel történt. 
    * A kód a minta majd kezeli a kivételt meghívásával `AcquireTokenAsync`, ennek eredményeképpen a felhasználótól a bejelentkezéshez. 

* Azt is inkább jelenthet vizuális jelzést felhasználók számára, amelyek egy interaktív bejelentkezés szükség, hogy a megfelelő időben való bejelentkezéshez választhatja. Vagy az alkalmazás újra `AcquireTokenSilentAsync` később. Ezt a mintát gyakran használt, amikor a felhasználók használhatják más alkalmazás működésének szüneteltetése--nélkül például, ha helyben tárolt tartalom érhető el az alkalmazást. Ebben az esetben felhasználók dönthet arról szeretne bejelentkezni a védett erőforrások eléréséhez, vagy az elavult adatok frissítése. Másik lehetőségként az alkalmazás dönt, hogy újra `AcquireTokenSilentAsync` amikor állítják vissza. a hálózati elvégzése után átmenetileg nem érhető el. 
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>A Microsoft Graph API hívása csak megszerzett jogkivonattal használatával
Az alábbi módszerek hozzáadása a `MainActivity` osztály:

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
        public Map<String, String> getHeaders() throws AuthFailureError {
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

/* Sets the Graph response */
private void updateGraphUI(JSONObject graphResponse) {
    TextView graphText = (TextView) findViewById(R.id.graphData);
    graphText.setText(graphResponse.toString());
}
```
<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>További információt a REST-hívást ellen védett API

A mintaalkalmazás az `callGraphAPI` hívások `getAccessToken` és majd lehetővé teszi a HTTP `GET` jogkivonat szükséges, és a tartalom visszaadó erőforrás kérelmet. Ez a módszer a megszerzett token hozzáadja a HTTP-hitelesítési fejlécéhez. Ez a minta az erőforrás a Microsoft Graph API *me* végpont, amely a felhasználói profil adatait jeleníti meg.
<!--end-collapse-->

## <a name="set-up-sign-out"></a>Kijelentkezési beállítása

Az alábbi módszerek hozzáadása a `MainActivity` osztály:

```java
/* Clears a user's tokens from the cache.
 * Logically similar to "sign out" but only signs out of this app.
 */
private void onSignOutClicked() {

    /* Attempt to get a user and remove their cookies from cache */
    List<User> users = null;

    try {
        users = sampleApp.getUsers();

        if (users == null) {
            /* We have no users */

        } else if (users.size() == 1) {
            /* We have 1 user */
            /* Remove from token cache */
            sampleApp.remove(users.get(0));
            updateSignedOutUI();

        }
        else {
            /* We have multiple users */
            for (int i = 0; i < users.size(); i++) {
                sampleApp.remove(users.get(i));
            }
        }

        Toast.makeText(getBaseContext(), "Signed Out!", Toast.LENGTH_SHORT)
                .show();

    } catch (MsalClientException e) {
        Log.d(TAG, "MSAL Exception Generated while getting users: " + e.toString());

    } catch (IndexOutOfBoundsException e) {
        Log.d(TAG, "User at this position does not exist: " + e.toString());
    }
}

/* Set the UI for signed-out user */
private void updateSignedOutUI() {
    callGraphButton.setVisibility(View.VISIBLE);
    signOutButton.setVisibility(View.INVISIBLE);
    findViewById(R.id.welcome).setVisibility(View.INVISIBLE);
    findViewById(R.id.graphData).setVisibility(View.INVISIBLE);
    ((TextView) findViewById(R.id.graphData)).setText("No Data");
}
```
<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>További információ a felhasználó kijelentkezési

A `onSignOutClicked` metódus az előzőekben látható kód a eltávolítja azokat a felhasználókat, a felhasználó az aktuális felhasználó törlésére, így a későbbi kérelmek egy jogkivonat akkor sikeres, csak akkor, ha történik, hogy interaktív MSAL gyakorlatilag megmondja MSAL gyorsítótárból.

Bár ez a példa az alkalmazás egyetlen felhasználót támogat, MSAL támogatja a forgatókönyvekben, ahol több fiók is bejelentkezhet a egyszerre. Példa: Ha a felhasználó rendelkezik-e a több fiók e-mail alkalmazást.
<!--end-collapse-->
