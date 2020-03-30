---
title: Biztonságos leküldéses értesítések küldése az Azure Értesítési központtal
description: Ismerje meg, hogyan küldhet biztonságos leküldéses értesítéseket egy Android-alkalmazásba az Azure-ból. Java és C#-ban írt kódminták.
documentationcenter: android
keywords: push értesítés, push értesítések, push üzenetek, android push értesítések
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 419a9f9b5ce698c7516edd55856cbea9891ba029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212179"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Biztonságos leküldéses értesítések küldése az Azure Értesítési központtal

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Áttekintés

> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

A Microsoft Azure leküldéses értesítési támogatása lehetővé teszi egy könnyen használható, többplatformos, kibővített leküldéses üzenet-infrastruktúrához való hozzáférést, ami nagyban leegyszerűsíti a leküldéses értesítések megvalósítását mind a fogyasztói, mind a nagyvállalati alkalmazások számára mobil platformokon.

Szabályozási vagy biztonsági korlátok miatt előfordulhat, hogy egy alkalmazás szeretne felvenni valamit az értesítésben, amely nem továbbítható a szabványos leküldéses értesítési infrastruktúrán keresztül. Ez az oktatóanyag ismerteti, hogyan érheti el ugyanazt a felhasználói élményt azáltal, hogy bizalmas adatokat küld az ügyfél Android-eszköz és az alkalmazás háttérrendszere közötti biztonságos, hitelesített kapcsolaton keresztül.

Magas szinten az áramlás a következő:

1. Az alkalmazás háttér-vége:
   * Biztonságos tartalom tárolható a háttér-adatbázisban.
   * Az értesítés azonosítóját elküldi az Android-eszköznek (nem küld biztonságos adatokat).
2. Az alkalmazás az eszközön, amikor megkapja az értesítést:
   * Az Android-eszköz kapcsolatba lép a biztonsági adatért, a biztonsági adatért.
   * Az alkalmazás értesítésként jelenítheti meg a hasznos adatot az eszközön.

Fontos megjegyezni, hogy az előző folyamat (és ebben az oktatóanyagban), feltételezzük, hogy az eszköz tárolja a hitelesítési jogkivonatot a helyi tárolóban, miután a felhasználó bejelentkezik. Ez a megközelítés garantálja a zökkenőmentes élményt, mivel az eszköz lekérheti az értesítés biztonságos hasznos adatát ezzel a jogkivonattal. Ha az alkalmazás nem tárolja a hitelesítési jogkivonatokat az eszközön, vagy ha ezek a jogkivonatok lejárhatnak, az eszközalkalmazásnak a leküldéses értesítés kézhezvételekor meg kell jelenítenie egy általános értesítést, amely felszólítja a felhasználót az alkalmazás elindítására. Az alkalmazás ezután hitelesíti a felhasználót, és megjeleníti az értesítési hasznos terhet.

Ez az oktatóanyag bemutatja, hogyan küldhet biztonságos leküldéses értesítéseket. Ez épül a [Felhasználók értesítése](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) bemutató, így el kell végeznia a lépéseket, hogy a bemutató az első, ha még nem tette meg.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy az értesítési központot az [Első lépések az értesítési központtal (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md)című részben leírtak szerint hozta létre és konfigurálta.

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Az Android-projekt módosítása

Most, hogy módosította az alkalmazás háttér-rendszerű küldését, hogy csak a leküldéses értesítés *azonosítóját* küldje el, meg kell változtatnia az Android-alkalmazást az értesítés kezeléséhez, és vissza kell hívnia a háttérrendszert a biztonságos üzenet megjelenítéséhez.
E cél elérése érdekében gondoskodnia kell arról, hogy az Android-alkalmazás tudja, hogyan hitelesítheti magát a háttérrendszerrel, amikor megkapja a leküldéses értesítéseket.

Most módosítsa a *bejelentkezési* folyamatot annak érdekében, hogy mentse a hitelesítési fejléc értékét az alkalmazás megosztott beállításaiba. Hasonló mechanizmusok segítségével tárolható bármely hitelesítési jogkivonat (például OAuth-tokenek), amelyeket az alkalmazásnak felhasználói hitelesítő adatok nélkül kell használnia.

1. Az Android-alkalmazásprojektben adja hozzá a következő `MainActivity` állandókat az osztály elejére:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```
2. Még mindig `MainActivity` az osztályban, frissítse a `getAuthorizationHeader()` módszert, hogy tartalmazza a következő kódot:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```
3. A `MainActivity` fájl `import` tetején a következő utasítások vannak felrakva:

    ```java
    import android.content.SharedPreferences;
    ```

Most módosítsa a hívott kezelőt, amikor az értesítés érkezik.

1. Az `MyHandler` osztályban `OnReceive()` változtassa meg a módszert, hogy tartalmazza:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. Ezután `retrieveNotification()` adja hozzá a `{back-end endpoint}` metódust, és cserélje le a helyőrzőt a háttérvégpontra, amelyet a háttér-végpont telepítése során kapott:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Ez a módszer meghívja az alkalmazás háttér-rendszerű lekérni az értesítési tartalom a megosztott beállításokban tárolt hitelesítő adatokat, és megjeleníti azt a szokásos értesítést. Az értesítés úgy néz ki, hogy az alkalmazás felhasználópontosan úgy, mint bármely más leküldéses értesítés.

Célszerű kezelni a hiányzó hitelesítési fejléc tulajdonság vagy elutasítás a háttér-rendszer általi kezelése. Ezeknek az eseteknek a konkrét kezelése leginkább a megcélzott felhasználói élménytől függ. Az egyik lehetőség az, hogy egy általános kéréssel ellátott értesítést jelenít meg, amelynek segítségével a felhasználó hitelesíti magát a tényleges értesítés lekéréséhez.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás futtatásához hajtsa végre a következő műveleteket:

1. Győződjön meg arról, hogy az **AppBackend** telepítve van az Azure-ban. Visual Studio használata esetén futtassa az **AppBackend** Web API-alkalmazást. Megjelenik egy ASP.NET weblap.
2. Az Eclipse alkalmazásban futtassa az alkalmazást fizikai Android-eszközön vagy emulátoron.
3. Az Android-alkalmazás felhasználói felületén adjon meg egy felhasználónevet és egy jelszót. Ezek bármilyen karakterlánc lehetnek, de azonos értékűeknek kell lenniük.
4. Az Android-alkalmazás felhasználói felületén kattintson a **Bejelentkezés**gombra. Ezután kattintson **a Leküldéses küldés**gombra.
