---
title: Biztonságos leküldéses értesítések küldése az Azure Notification hubs használatával
description: 'Útmutató: a biztonságos leküldéses értesítések küldésére Android-alkalmazás az Azure-ból. Kódminták Java és a C#.'
documentationcenter: android
keywords: leküldéses értesítési, leküldéses értesítések leküldéses üzenetek, android leküldéses értesítések
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
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33776760"
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

Leküldéses értesítési támogatása a Microsoft Azure lehetővé teszi, hogy egy könnyen kezelhető, multiplatform, kibővített leküldéses üzenet infrastruktúra, amely jelentősen egyszerűsíti a leküldéses értesítések az ügyfél és a vállalati alkalmazások eléréséhez mobil platformokon.

Szabályozó miatt vagy biztonsági korlátozások néha egy alkalmazás előfordulhat, hogy szeretne valamit az értesítés, amely a szabványos leküldéses értesítési infrastruktúrát keresztül nem lehet megadni. Ez az oktatóanyag ismerteti, hogyan úgy, hogy a bizalmas adatokat, az ügyfél Android-eszközön, és a háttéralkalmazás közötti biztonságos és hitelesített kapcsolaton keresztül küld a felhasználói élmény eléréséhez.

Magas szinten a folyamat a következőképpen történik:

1. Az alkalmazás háttér:
   * Háttér-adatbázisban tárolja biztonságos hasznos.
   * Az értesítés Azonosítójának küld az Android-eszközön (nem biztonságos információk küldése).
2. Az alkalmazást az eszközön, az értesítés fogadása közben:
   * Az Android-eszközön kapcsolatba lép a háttér-kérő a biztonságos forgalma.
   * Az alkalmazás a tartalom megjeleníthető értesítésként az eszközön.

Fontos megjegyezni, hogy az előző folyamatában (és ebben az oktatóanyagban), feltételezzük, hogy az eszköz tárol egy hitelesítési jogkivonatot a helyi tároló, a felhasználó bejelentkezése után. Ez a megközelítés biztosítja, hogy az zökkenőmentes élményt, az eszköz le az értesítési biztonságos tartalom a token használatával. Ha az alkalmazás nem tárolja a hitelesítési tokenek az eszközön, vagy ezeket a jogkivonatokat is lejárt, az eszköz alkalmazást, és a leküldéses értesítés fogadásakor megjelenjen-e a felhasználó megkérdezése általános értesítési indíthatja el az alkalmazást. Az alkalmazás majd hitelesíti a felhasználót, és az értesítési tartalom jeleníti meg.

Ez az oktatóanyag bemutatja, hogyan biztonságos leküldéses értesítések küldéséhez. Buildekről nyújtanak a [felhasználók értesítése](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) oktatóanyag, ezért el kell végeznie a lépéseket, hogy az oktatóanyagban először Ha még nem tette meg.

> [!NOTE]
> Ez az oktatóanyag feltételezi, hogy létrehozta és leírtak szerint konfigurálta az értesítési központ [Ismerkedés a Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Az Android-projekt módosítása
Most, hogy módosította, a app háttér küldése csak a *azonosító* egy leküldéses értesítés az Android-alkalmazás az értesítés, és a háttér-letölteni a biztonságos üzenetet megjelenítendő visszahívási módosítani kell.
E cél eléréséhez, akkor győződjön meg arról, hogy az Android-alkalmazás meg tudja a háttér-hitelesítse magát a leküldéses értesítések fogadásakor.

Most, módosítsa a *bejelentkezési* folyamat ahhoz, hogy az alkalmazás a közös beállításokat mentse a hitelesítési fejléc értéke. Hasonló mechanizmusok bármely hitelesítési jogkivonat (például az OAuth jogkivonatok), anélkül, hogy a felhasználói hitelesítő adatok használatára van az alkalmazás tárolására használható.

1. Az Android-alkalmazás projektben adja hozzá a következő állandókat tetején a **MainActivity** osztály:
   
        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
2. Még mindig a **MainActivity** osztály, frissítse a `getAuthorizationHeader()` metódus a következő kódot tartalmazza:
   
        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
   
            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
   
            return basicAuthHeader;
        }
3. Adja hozzá a következő `import` tetején lévő utasítások a **MainActivity** fájlt:
   
        import android.content.SharedPreferences;

Módosítsa a kezelő is nevezett, az értesítés fogadásakor.

1. Az a **MyHandler** osztályban módosítsa a `OnReceive()` metódust kell tartalmaznia:
   
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }
2. Majd adja hozzá a `retrieveNotification()` metódus, a helyőrző cseréje `{back-end endpoint}` a háttér-végponthoz, a háttér-másikban kapott:
   
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

Ez a metódus meghívja az alkalmazás háttér-a a közös beállításokat tárolt hitelesítő adatok használatával értesítési tartalmának lekéréséhez, és azt egy normál értesítés jelenik meg. Az értesítés megvizsgálja, hogy az alkalmazás felhasználói akárcsak bármely más leküldéses értesítést.

Célszerű a esetek hiányzó hitelesítési fejléc tulajdonság vagy elutasítása kezelésére által a háttérszolgáltatáshoz. Ezekben az esetekben adott kezelésének legtöbbször a cél felhasználói élményét függ. Egy lehetőség egy értesítés megjelenítése egy általános kérdéshez a felhasználó hitelesítésére a tényleges értesítési beolvasása.

## <a name="run-the-application"></a>Futtassa az alkalmazást
Az alkalmazás futtatásához a következő műveleteket hajthatja végre:

1. Győződjön meg arról, hogy **AppBackend** a rendszer az Azure-ban. Ha a Visual Studio használatával, futtassa a **AppBackend** webes API-alkalmazás. Az ASP.NET-weblap jelenik meg.
2. Az eclipse-ben futtassa az alkalmazást egy fizikai Android-eszköz vagy az emulátorban.
3. Az Android-alkalmazás felhasználói felületén adja meg egy felhasználónevet és jelszót. Bármilyen karakterlánc is lehetnek, de ugyanarra az értékre kell lenniük.
4. Kattintson az Android-alkalmazás felhasználói felületén, **jelentkezzen be**. Kattintson a **leküldéses küldése**.

