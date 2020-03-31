---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: deb94cab97bd9a402676cdc5c0239da8d07ed8b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179807"
---
Az előző példa egy szabványos bejelentkezést mutatott, amely megköveteli, hogy az ügyfél lépjen kapcsolatba az identitásszolgáltatóval és a háttérbeli Azure-szolgáltatással az alkalmazás minden indításakor. Ez a módszer nem hatékony, és a használattal kapcsolatos problémák at, ha sok ügyfél megpróbálja elindítani az alkalmazást egyszerre. Egy jobb megközelítés az, hogy gyorsítótárazza az Azure-szolgáltatás által visszaadott engedélyezési jogkivonatot, és próbálja meg használni ezt először, mielőtt egy szolgáltató-alapú bejelentkezést használna.

> [!NOTE]
> Gyorsítótárazhatja a háttérbeli Azure-szolgáltatás által kibocsátott jogkivonatot, függetlenül attól, hogy ügyfél által felügyelt vagy szolgáltatás által felügyelt hitelesítést használ.You can cache the token issued by the back-end Azure service regardless whether you are using client-managed or service-managed authentication. Ez az oktatóanyag szolgáltatás által felügyelt hitelesítést használ.
>
>

1. Nyissa meg a ToDoActivity.java fájlt, és adja hozzá a következő importálási kimutatásokat:

    ```java
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;
    ```

2. Add hozzá a `ToDoActivity` következő tagokat az osztályhoz.

    ```java
    public static final String SHAREDPREFFILE = "temp";
    public static final String USERIDPREF = "uid";
    public static final String TOKENPREF = "tkn";
    ```

3. A ToDoActivity.java fájlban adja hozzá `cacheUserToken` a metódus következő definícióját.

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

    Ez a módszer a felhasználói azonosítót és a jogkivonatot egy magánjellegűként megjelölt beállításfájlban tárolja. Ennek meg kell védenie a gyorsítótárhoz való hozzáférést, hogy az eszközön lévő többi alkalmazás ne férhessen hozzá a jogkivonathoz. A preferencia az alkalmazás hoz. Ha azonban valaki hozzáfér az eszközhöz, lehetséges, hogy más módon is hozzáférhet a jogkivonat-gyorsítótárhoz.

   > [!NOTE]
   > A jogkivonatot titkosítással tovább védheti, ha az adatokhoz való jogkivonat-hozzáférés rendkívül bizalmasnak minősül, és valaki hozzáférhet az eszközhöz. A teljesen biztonságos megoldás azonban túlmutat az oktatóanyag hatókörén, és a biztonsági követelményektől függ.
   >
   >

4. A ToDoActivity.java fájlban adja hozzá `loadUserTokenCache` a metódus következő definícióját.

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

5. A *ToDoActivity.java* fájlban `authenticate` cserélje `onActivityResult` le a és a metódusokat a következőkre, amelyek token gyorsítótárat használnak. Módosítsa a bejelentkezési szolgáltatót, ha a Google-tól eltérő fiókot szeretne használni.

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

6. Építse létre az alkalmazást, és tesztelje a hitelesítést egy érvényes fiókhasználatával. Legalább kétszer futtasd le. Az első futtatás során meg kell kapnia egy figyelmeztetést, hogy jelentkezzen be, és hozza létre a jogkivonat-gyorsítótár. Ezt követően minden futtatás megkísérli a tokengyorsítótár betöltését a hitelesítéshez. Nem kell bejelentkeznie.
