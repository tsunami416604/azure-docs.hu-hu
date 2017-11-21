
Az előző példában bemutatta szabványos bejelentkezéshez, ami megköveteli, hogy az ügyfél, mind az identitásszolgáltató, és a háttér-Azure szolgáltatás kapcsolódni az alkalmazás minden indításakor. Ez a módszer nem hatékony, és akkor használati kapcsolatos problémák, ha sok ügyfél próbál meg egyidejűleg indítsa el az alkalmazást. Egy jobb megoldás, a gyorsítótár az engedélyezési jogkivonatot az Azure-szolgáltatás által visszaadott, és próbálja meg használni az első olyan szolgáltató alapú bejelentkezés használata előtt.

> [!NOTE]
> A háttér-függetlenül használja-e ügyfél által felügyelt vagy a szolgáltatás által kezelt hitelesítés az Azure szolgáltatás által kiadott tokennek képes gyorsítótárazni. Ez az oktatóanyag a szolgáltatás által kezelt hitelesítést használ.
>
>

1. Nyissa meg a ToDoActivity.java fájlban, és adja hozzá a következő importálási utasításokat:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. A következő tagokat adjanak hozzá a `ToDoActivity` osztály.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. A ToDoActivity.java fájlban adja hozzá a következő definícióját a `cacheUserToken` metódust.

    ```java
    private void cacheUserToken(MobileServiceUser user)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        Editor editor = prefs.edit();
        editor.putString(USERIDPREF, user.getUserId());
        editor.putString(TOKENPREF, user.getAuthenticationToken());
        editor.commit();
    }
    ```

    Ez a módszer is meg van jelölve személyes beállításokat szabályozó fájlban tárolja a felhasználói Azonosítót és a jogkivonatot. Hogy más alkalmazások az eszközön nincs hozzáférése a jogkivonatot, ez a gyorsítótár a hozzáférést kell védeni. A beállítás ki az alkalmazás elkülönített. Azonban ha valaki hozzáfér az eszközre, akkor lehet, hogy előfordulhat, hogy a jogkivonat gyorsítótára más módon hozzáférést kapnak.

   > [!NOTE]
   > Ha a adatokhoz való hozzáférés a jogkivonatokhoz szigorúan bizalmas minősül, és hozzáférhet az eszközt valaki, további védelmet biztosíthat a titkosítás, a jogkivonatot. Teljesen biztonságos megoldást ebben az oktatóanyagban terjed, és attól függ, a biztonsági követelményeinek.
   >
   >

4. A ToDoActivity.java fájlban adja hozzá a következő definícióját a `loadUserTokenCache` metódust.

    ```java
    private boolean loadUserTokenCache(MobileServiceClient client)
    {
        SharedPreferences prefs = getSharedPreferences(SHAREDPREFFILE, Context.MODE_PRIVATE);
        String userId = prefs.getString(USERIDPREF, null);
        if (userId == null)
            return false;
        String token = prefs.getString(TOKENPREF, null);
        if (token == null)
            return false;

        MobileServiceUser user = new MobileServiceUser(userId);
        user.setAuthenticationToken(token);
        client.setCurrentUser(user);

        return true;
    }
    ```

5. Az a *ToDoActivity.java* fájlt, cserélje le a `authenticate` és `onActivityResult` módszereket a következő is, amely jogkivonatok gyorsítótárát használja. Módosítsa a bejelentkezés-szolgáltató, ha szeretné használni a Google nem.

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, login and create a token cache
        else
        {
            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the login request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // login succeeded
                    createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
                    createTable();
                } else {
                    // login failed, check the error message
                    String errorMessage = result.getErrorMessage();
                    createAndShowDialog(errorMessage, "Error");
                }
            }
        }
    }
    ```

6. Hozhat létre. az alkalmazás és a teszt hitelesítési érvényes fiók használatával. Legalább két alkalommal futtassa. Az első futtatás során jelenít meg bejelentkezni, és hozzon létre a jogkivonatok gyorsítótárát kell kapnia. Ezután minden egyes futtatásához megkísérli betölteni a hitelesítési jogkivonat gyorsítótárában. Ön nem köteles jelentkezzen be.
