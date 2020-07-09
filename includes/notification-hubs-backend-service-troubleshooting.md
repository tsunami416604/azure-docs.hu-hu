---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 343fef941870b6deaad777fac9b9d258d454aa2b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081424"
---
#### <a name="no-response-from-the-backend-service"></a>Nincs válasz a háttérrendszer-szolgáltatástól

Helyi tesztelés esetén győződjön meg arról, hogy a háttérbeli szolgáltatás fut, és a megfelelő portot használja.

Ha teszteli az **Azure API-alkalmazást**, ellenőrizze, hogy a szolgáltatás fut-e, és hogy van-e telepítve, és hiba nélkül indult el.

Ellenőrizze, hogy az alapcímet helyesen adta-e meg a **[Poster](https://www.postman.com/downloads)** vagy a Mobile App konfigurációjában az ügyfélen keresztüli tesztelés során. A kiindulási címnek indicatively kell lennie `https://<api_name>.azurewebsites.net/` , vagy a `https://localhost:5001/` helyi tesztelés során.

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>A hibakeresési munkamenet elindítása vagy leállítása után nem fogadhat értesítéseket az Android rendszerre

A hibakeresési munkamenet elindítása vagy leállítása után ellenőrizze újra a regisztrációt. A hibakereső új **Firebase** -token generálását eredményezi. Az értesítési központ telepítését is frissíteni kell.

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>401 állapotkód fogadása a háttérrendszer-szolgáltatásból

Ellenőrizze, hogy a **apikey** -kérelem fejléce be van-e állítva, és ez az érték megegyezik-e a háttér-szolgáltatáshoz konfigurálttal.

Ha a helyi tesztelés során ezt a hibaüzenetet kapja, győződjön meg arról, hogy az ügyfél konfigurációjában megadott kulcs értéke megegyezik a következővel **: ApiKey** felhasználói beállítás értéke az [API](#create-the-api-app)által használt érték.

Ha egy **API-alkalmazással**végez tesztelést, győződjön meg arról, hogy az ügyfél-konfigurációs fájlban található kulcs értéke megegyezik az [API](#create-the-api-app)-alkalmazásban használt **hitelesítéssel: ApiKey** -alkalmazás beállításaival.

> [!NOTE]
> Ha a háttér-szolgáltatás üzembe helyezése után már létrehozott vagy módosította ezt a beállítást, akkor annak érvénybe léptetéséhez újra kell indítania a szolgáltatást.

Ha úgy döntött, hogy nem fejezi be az [ügyfelek hitelesítése API-kulcs használatával](#authenticate-clients-using-an-api-key-optional) szakaszt, ügyeljen arra, hogy ne alkalmazza az **Engedélyezés** attribútumot a **NotificationsController** osztályra.

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>404 állapotkód fogadása a háttérrendszer-szolgáltatásból

Ellenőrizze, hogy helyes-e a végpont és a HTTP-kérelem módszere. A végpontoknak például a következőknek kell indicatively:

- **[Put]**`https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[Törlés]**`https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[Post]**`https://<api_name>.azurewebsites.net/api/notifications/requests`

Vagy helyi teszteléskor:

- **[Put]**`https://localhost:5001/api/notifications/installations`
- **[Törlés]**`https://localhost:5001/api/notifications/installations/<installation_id>`
- **[Post]**`https://localhost:5001/api/notifications/requests`

Amikor megadja az alapcímet az ügyfélalkalmazás számára, győződjön meg róla, hogy az a-vel végződik `/` . A kiindulási címnek indicatively kell lennie `https://<api_name>.azurewebsites.net/` , vagy a `https://localhost:5001/` helyi tesztelés során.

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>Nem lehet regisztrálni, és az értesítési központ hibaüzenete jelenik meg

Ellenőrizze, hogy a teszt eszköznek van-e hálózati kapcsolata. Ezután határozza meg a http-válasz állapotkódot úgy, hogy egy töréspontot állít be a **statuscode** tulajdonság értékének vizsgálatához a **HttpResponse**.

Tekintse át az előző hibaelhárítási javaslatokat, ha alkalmazhatók az állapotkód alapján.

Állítson be egy töréspontot azokon a sorokon, amelyek az adott API-hoz tartozó specifikus állapotkódot adják vissza. Ezután próbálja meg a háttér-szolgáltatást a helyi hibakeresés során hívni.

Ellenőrzi, hogy a háttér-szolgáltatás a várt módon működik-e a **[Poster](https://www.postman.com/downloads)** használatával. Használja az adott platformhoz tartozó ügyfél kódja által létrehozott tényleges adattartalmat.

Tekintse át a platform-specifikus konfigurációs szakaszt, és győződjön meg arról, hogy nem lettek kihagyva a lépések. Győződjön meg arról, hogy a megfelelő értékek megoldhatók a `installation id` `token` megfelelő platformon, illetve változókban.

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>Nem sikerült feloldani az eszközhöz tartozó hibaüzenet AZONOSÍTÓját.

Tekintse át a platform-specifikus konfigurációs szakaszt, és győződjön meg arról, hogy nem lettek kihagyva a lépések.
