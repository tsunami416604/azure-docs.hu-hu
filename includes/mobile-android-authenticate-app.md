---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: eded2d6a9f2c270a2b3ccca296277b0a016733fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179806"
---
1. Nyissa meg a projektet az Android Studio programban.

2. Az Android Studio **Project** Explorer `ToDoActivity.java` ben nyissa meg a fájlt, és adja hozzá a következő importálási nyilatkozatokat:

    ```java
    import java.util.concurrent.ExecutionException;
    import java.util.concurrent.atomic.AtomicBoolean;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.content.SharedPreferences.Editor;

    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
    import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
    ```

3. Adja hozzá a következő módszert a **ToDoActivity** osztályhoz:

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

    Ez a kód létrehoz egy módszert a Google hitelesítési folyamatának kezelésére. Egy párbeszédpanelen megjelenik a hitelesített felhasználó azonosítója. Csak sikeres hitelesítéssel folytathatja.

    > [!NOTE]
    > Ha nem a Google-t használ, módosítsa a **bejelentkezési** metódusnak átadott értéket a következő értékek egyikére: _MicrosoftAccount_, _Facebook_, _Twitter_vagy _windowsazureactivedirectory_.

4. Az **onCreate** metódusban adja hozzá a következő kódsort az `MobileServiceClient` objektumot példányosan létrehozó kód után.

    ```java
    authenticate();
    ```

    Ez a hívás elindítja a hitelesítési folyamatot.

5. A fennmaradó kód `authenticate();` áthelyezése után az **onCreate** metódusban egy új **createTable** metódusba:

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

6. Az átirányítás várt működésének biztosításához adja `RedirectUrlActivity` hozzá `AndroidManifest.xml`a következő részletet:

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

7. Add `redirectUriScheme` `build.gradle` hozzá az Android alkalmazás.

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

8. Add `com.android.support:customtabs:23.0.1` hozzá a függőségek `build.gradle`a:

    ```gradle
    dependencies {
        // ...
        compile 'com.android.support:customtabs:23.0.1'
    }
    ```

9. A **Futtatás** menüben kattintson az **Alkalmazás futtatása** parancsra az alkalmazás elindításához és a kiválasztott identitásszolgáltatóval való bejelentkezéshez.

> [!WARNING]
> Az említett URL-séma a kis- és nagybetűket is figyelembe vevő. Győződjön meg arról, hogy az összes előfordulása `{url_scheme_of_you_app}` használja ugyanazt az esetet.

Ha sikeresen bejelentkezett, az alkalmazásnak hiba nélkül kell futnia, és képesnek kell lennie a háttérszolgáltatás lekérdezésére és az adatok frissítésére.
