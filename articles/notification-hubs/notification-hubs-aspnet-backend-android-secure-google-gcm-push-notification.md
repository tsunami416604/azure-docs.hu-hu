---
title: Biztonságos leküldéses értesítések küldése az Azure Notification hubs használatával
description: Ismerje meg, hogyan biztonságos leküldéses értesítések küldésére Android-alkalmazás az Azure-ból. A Java és a C# nyelven írt kódmintákat.
documentationcenter: android
keywords: leküldéses értesítés, leküldéses értesítések, leküldéses üzenetek, android leküldéses értesítések
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 04/25/2018
ms.author: dimazaid
ms.openlocfilehash: 58f6967c59a5060baa10ff83752b9c6ed08226cb
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698028"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Biztonságos leküldéses értesítések küldése az Azure Notification hubs használatával
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Áttekintés
> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez egy aktív Azure-fiókra lesz szüksége. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).
> 
> 

Leküldéses értesítés támogatása a Microsoft Azure segítségével elérheti egy könnyen használható, multiplatform, kibővített leküldéses üzenet infrastruktúra, amely jelentősen egyszerűsíti a leküldéses értesítések a fogyasztói és a vállalati alkalmazások számára mobil platformokra.

Szabályozási miatt, vagy biztonsági okokból, néha egy alkalmazás előfordulhat, hogy szeretne foglalni valamit a értesítést, amelyet nem lehet megadni a standard szintű leküldéses értesítési infrastruktúrát keresztül. Ez az oktatóanyag ugyanazt a felhasználói élményt elérése a bizalmas adatokat az ügyfél Androidos eszközön és az alkalmazási háttérrendszer között biztonságos, hitelesített kapcsolaton keresztül küldésével ismerteti.

Magas szinten a folyamat a következőképpen történik:

1. A háttér:
   * Háttér-adatbázisban tárolja biztonságos adattartalma.
   * Az Android-eszköz (nem biztonságos küld adatokat) küld az értesítés azonosítója.
2. Az alkalmazás az eszközön, az értesítés fogadásakor:
   * Az Android-eszköz kapcsolatba lép a háttéralkalmazás a biztonságos hasznos adatokat kér.
   * Az alkalmazás az eszközön értesítést, a hasznos adatokat jeleníti meg.

Fontos megjegyezni, hogy az előző folyamat (és ebben az oktatóanyagban) azt feltételezzük, hogy az eszköz tárol egy hitelesítési tokent a helyi tárhely, miután a felhasználó bejelentkezik. Ez a megközelítés garantálja a zökkenőmentes élményt, az eszköz kérheti le az értesítési e-jogkivonat használatával biztonságos hasznos. Ha az alkalmazás nem tárolja a hitelesítési tokenek az eszközön, vagy ha ezek a jogkivonatok is járhatott, az eszköz alkalmazást a leküldéses értesítés fogadása után indítsa el az alkalmazást a felhasználó megkérdezése általános értesítést kell megjelenítenie. Az alkalmazás ezután hitelesíti a felhasználót, és az értesítési tartalom látható.

Ez az oktatóanyag bemutatja, hogyan biztonságos leküldéses értesítések küldéséhez. Épül a [– felhasználók értesítése](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , hajtsa végre a lépéseket, hogy az oktatóanyag első Ha még nem tette, az oktatóanyagban.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy létrehozta és leírtak szerint konfigurálta az értesítési központ [Ismerkedés a Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Az Android-projekt módosítása
Most, hogy módosította, a háttér küldése csak a *azonosító* leküldéses értesítés, Android-alkalmazásba, hogy az értesítések kezelésére, és a háttérbeli letölteni a megjeleníteni kívánt biztonságos üzenetet a visszahívási módosítani kell.
E cél eléréséhez, akkor győződjön meg arról, hogy az Android-alkalmazás tudja, hogyan hitelesítse magát a háttér-amikor leküldéses értesítéseket kap.

Most, módosítsa a *bejelentkezési* ahhoz, hogy a hitelesítési fejléc értéke a közös beállításokat az alkalmazás a folyamatot. Ehhez hasonló mechanizmusok bármely hitelesítési jogkivonat (például az OAuth-jogkivonatok), amely az alkalmazás használatához anélkül, hogy a felhasználói hitelesítő adatokkal rendelkezik tárolására használható.

1. Az Android-alkalmazás-projektben adja hozzá a következő állandókat felső részén a **MainActivity** osztály:
   
        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
2. Még mindig a **MainActivity** osztály, frissítse a `getAuthorizationHeader()` módszer a következő kódot tartalmazza:
   
        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
   
            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
   
            return basicAuthHeader;
        }
3. Adja hozzá a következő `import` tetején található utasításokat a **MainActivity** fájlt:
   
        import android.content.SharedPreferences;

Módosítsa a kezelő, amelynek a neve, az értesítés fogadásakor.

1. Az a **MyHandler** osztályban módosítsa a `OnReceive()` metódust tartalmaz:
   
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }
2. Majd adja hozzá a `retrieveNotification()` metódust, és cserélje le a helyőrző `{back-end endpoint}` a háttér-végponttal, a háttér-üzembe helyezése során kapott:
   
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

Ez a metódus meghívja a háttér beolvasni az értesítések tartalmát, a közös beállításokat tárolt hitelesítő adatok használatával, és megjeleníti azt a normál értesítés. Az értesítés az alkalmazás felhasználó bármely más leküldéses értesítés hasonlóan néz ki.

Célszerű az esetek hiányzó hitelesítési fejléc tulajdonság vagy elutasítási kezeléséhez a háttér-szerint. Ezekben az esetekben a meghatározott kezelésének többnyire függ a célként megadott felhasználói élményét. Az egyik lehetőség, hogy értesítést kérek, és általános kéri a felhasználó hitelesítésére lekérni a tényleges értesítést.

## <a name="run-the-application"></a>Az alkalmazás futtatása
Futtassa az alkalmazást, tegye a következőket:

1. Győződjön meg arról, hogy **AppBackend** az Azure-ban üzemel. Ha a Visual Studiót használja, futtassa a **AppBackend** webes API alkalmazást. Egy ASP.NET-weblap jelenik meg.
2. Az eclipse-ben futtassa az alkalmazást egy fizikai Androidos eszközön vagy az emulátorban.
3. Az Android-alkalmazás felhasználói felületén adja meg egy felhasználónevet és jelszót. Ezek bármilyen karakterlánc lehet, de ugyanazt az értéket kell lenniük.
4. Az Android alkalmazás felhasználói felületén kattintson **bejelentkezés**. Kattintson a **küldjön leküldéses**.

