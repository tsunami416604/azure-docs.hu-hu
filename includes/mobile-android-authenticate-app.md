
1. Nyissa meg a projekt az Android Studio.

2. A **Project Explorer** az Android Studióban nyissa meg a `ToDoActivity.java` fájlt, és adja hozzá a következő importálási utasításokat:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Adja hozzá a következő metódust a **ToDoActivity** osztály:

    ```java
    // You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
    public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

    private void authenticate() {
        // Sign in using the Google provider.
        mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    createTable();
                } else {
                    // sign-in failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

    Ez a kód egy metódust a Google hitelesítést hoz létre. Egy párbeszédpanel megjeleníti a hitelesített felhasználó Azonosítóját. Csak a sikeres hitelesítés lépne.

    > [!NOTE]
    > Ha eltérő Google identitásszolgáltató használ, módosítsa az értéket, átadott a **bejelentkezési** metódus a következő értékek egyikére: _MicrosoftAccount_, _Facebook_, _Twitter_, vagy _windowsazureactivedirectory_.

4. Az a **onCreate** módszer, példányosítja kód után adja hozzá a következő kódsort a `MobileServiceClient` objektum.

    ```java
    authenticate();
    ```

    Ez a hívás a hitelesítési folyamat elindul.

5. Helyezze át a többi kód után `authenticate();` a a **onCreate** egy új módszer **createTable** módszert:

    ```java
    private void createTable() {

        // Get the table instance to use.
        mToDoTable = mClient.getTable(ToDoItem.class);

        mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);

        // Create an adapter to bind the items with the view.
        mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
        ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
        listViewToDo.setAdapter(mAdapter);

        // Load the items from Azure.
        refreshItemsFromTable();
    }
    ```

6. Átirányítás works elvárt érdekében adja hozzá a következő szövegrészletet `RedirectUrlActivity` való `AndroidManifest.xml`:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}"
                android:host="easyauth.callback"/>
        </intent-filter>
    </activity>
    ```

7. Adja hozzá `redirectUriScheme` való `build.gradle` az Android alkalmazás.

    ```gradle
    android {
        buildTypes {
            release {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
            debug {
                // ...
                manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
            }
        }
    }
    ```

8. Adja hozzá `com.android.support:customtabs:23.0.1` a függőségeit a a `build.gradle`:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. Az a **futtassa** menüben kattintson **-alkalmazás futtatása** el az alkalmazást, és jelentkezzen be a választott identitásszolgáltató számára.

> [!WARNING]
> Az URL-séma említett a kis-és nagybetűket. Győződjön meg arról, hogy minden előfordulását `{url_scheme_of_you_app}` nagybetűket.

Ha Ön sikeresen bejelentkezett, futtasson-e az alkalmazás nem jelenik meg hibaüzenet, és meg tudják lekérdezni a háttér-szolgáltatást, és hogy adatok.
