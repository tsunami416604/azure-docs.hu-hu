---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440191"
---
Az előző példa bemutatta, szabványos bejelentkezési, ami megköveteli, hogy az ügyfél kapcsolatba az identitásszolgáltató és a háttér Azure-szolgáltatás, az alkalmazás minden indításakor. Ez a módszer nem hatékony, és a használattal kapcsolatos problémák is rendelkezik, ha sok ügyfél próbál indítsa el az alkalmazást egyszerre. Jobb módszer, hogy az engedélyezési jogkivonatra, az Azure-szolgáltatás által visszaadott gyorsítótárazza, és próbálja használni az első olyan szolgáltató alapú bejelentkezés használata előtt.

> [!NOTE]
> A háttéralkalmazás függetlenül attól, hogy a hitelesítési ügyfél által felügyelt vagy a szolgáltatás által kezelt Azure-szolgáltatás által kiállított biztonsági jogkivonat képes gyorsítótárazni. Ebben az oktatóanyagban a szolgáltatás által kezelt hitelesítést használ.
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

    Ez a módszer a felhasználói Azonosítót és a jogkivonat titkos megjelölt szabályozó fájlban tárolja. Ez a gyorsítótárhoz való hozzáférést kell védelemmel, úgy, hogy más alkalmazások az eszközön nincs hozzáférése a token. A beállítás: az alkalmazás vendégünk. Azonban ha valaki hozzáfér az eszközhöz, akkor lehet, hogy a jogkivonatok gyorsítótárát más módon hozzáférést is kapnak.

   > [!NOTE]
   > Ha számít, hogy az adatokhoz való hozzáférés a jogkivonatokhoz szigorúan bizalmas, és valaki hozzáférést nyújthat az eszközt, további védelmet biztosíthat a titkosítás, a jogkivonat. Egy teljesen biztonságos megoldás az túlmutat a jelen oktatóanyag esetében azonban, és a biztonsági követelményeitől függ.
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

5. Az a *ToDoActivity.java* fájlt, cserélje le a `authenticate` és `onActivityResult` módszereit, valamint a következő is, ami egy token gyorsítótárát használja. Ha használja a Google aktuálistól eltérő fiókkal szeretne a bejelentkezés-szolgáltató módosítása

    ```java
    private void authenticate() {
        // We first try to load a token cache if one exists.
        if (loadUserTokenCache(mClient))
        {
            createTable();
        }
        // If we failed to load a token cache, sign in and create a token cache
        else
        {
            // Sign in using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // When request completes
        if (resultCode == RESULT_OK) {
            // Check the request code matches the one we send in the sign-in request
            if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
                MobileServiceActivityResult result = mClient.onActivityResult(data);
                if (result.isLoggedIn()) {
                    // sign-in succeeded
                    createAndShowDialog(String.format("You are now signed in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                    cacheUserToken(mClient.getCurrentUser());
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

6. Az alkalmazás és a teszt hitelesítési érvényes fiók használatával hozhat létre. Legalább kétszer futtatni. Az első futás közben jelentkezzen be, és a token gyorsítótár létrehozása felszólítást kapni. Ezt követően minden egyes futtatásához megkísérli betölteni a hitelesítési jogkivonat gyorsítótárba. Ön nem köteles jelentkezzen be a.
