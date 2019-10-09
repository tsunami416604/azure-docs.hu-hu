---
title: Offline szinkronizálás engedélyezése az Azure Mobile alkalmazásban (Android)
description: Megtudhatja, hogyan használhatja a App Service Mobile Appst az offline adatkapcsolatok gyorsítótárazásához és szinkronizálásához az Android-alkalmazásokban
documentationcenter: android
author: elamalani
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 0180e100432ac34b876af04ad99c9a5d189455c3
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025461"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Az Android Mobile-alkalmazás offline szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center támogatja a teljes körű és integrált szolgáltatások központi használatát a Mobile apps fejlesztéséhez. A fejlesztők a szolgáltatások **kiépítését**, **tesztelését** és **terjesztését** használhatják a folyamatos integráció és a kézbesítési folyamat beállításához. Az alkalmazás üzembe helyezését követően a fejlesztők az **elemzési** és **diagnosztikai** szolgáltatások segítségével ellenőrizhetik az alkalmazás állapotát és használatát, és a **leküldéses** szolgáltatást használó felhasználókkal is elvégezhetik a felhasználókat. A fejlesztők **a hitelesítést a** felhasználók **és az adatszolgáltatások** hitelesítésére is használhatják a Felhőbeli alkalmazásadatok megőrzése és szinkronizálása érdekében.
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon App Center [app Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag az Azure Mobile Apps Android rendszerhez készült offline szinkronizálási funkcióját ismerteti. Az offline szinkronizálás lehetővé teszi, hogy a végfelhasználók a @ no__t-0viewing használatával, a @ no__t-1even hozzáadásával vagy módosításával kommunikálnak, ha nincs hálózati kapcsolat. A módosításokat a rendszer egy helyi adatbázisban tárolja. Miután az eszköz ismét online állapotba került, a változások szinkronizálva lesznek a távoli háttérrel.

Ha első alkalommal használja az Azure Mobile Apps-t, először el kell végeznie az [Android-alkalmazás létrehozása]című oktatóanyagot. Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, hozzá kell adnia az adatelérési bővítmények csomagjait a projekthez. További információ a kiszolgálói bővítmények csomagjairól: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Az offline szinkronizálási szolgáltatással kapcsolatos további tudnivalókért tekintse meg az [Offline adatszinkronizálás az Azure Mobile Appsban]című témakört.

## <a name="update-the-app-to-support-offline-sync"></a>Az alkalmazás frissítése az offline szinkronizálás támogatásához
Ha offline szinkronizálást használ, a rendszer egy, az eszközön található **SQLite** -adatbázis részét képező *szinkronizálási táblából* olvas és ír egy *IMobileServiceSyncTable* felületet.

Az eszköz és az Azure Mobile Services közötti változások leküldéséhez és lekéréséhez *szinkronizálási környezetet* (*MobileServiceClient. SyncContext*) kell használnia, amelyet a helyi adatbázissal kell inicializálni, hogy helyileg tárolja az adattárolást.

1. @No__t – 0 esetében tegye megjegyzésbe a `mToDoTable` meglévő definícióját, és a szinkronizálási táblázat verziójának megjegyzését:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. A `onCreate` metódusban tegye megjegyzésbe a `mToDoTable` meglévő inicializálását, és tegye meg a következő definíció megjegyzését:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. A `refreshItemsFromTable` megjegyzésében írja be `results` definícióját, és írja be a következő definíciót:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. A `refreshItemsFromMobileServiceTable` definíciójának megjegyzése.
5. @No__t-0 definíciójának megjegyzése:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. @No__t-0 definíciójának megjegyzése:
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Az alkalmazás tesztelése
Ebben a szakaszban a Wi-Fi-vel tesztelheti a működését, majd kikapcsolhatja a WiFi-t egy offline forgatókönyv létrehozásához.

Az adatelemek hozzáadásakor a rendszer a helyi SQLite-tárolóban tárolja őket, de addig nem szinkronizálja a Mobile Service-t, amíg meg nem nyomja a **frissítés** gombot. Más alkalmazások eltérő követelményekkel rendelkezhetnek, ha az adatokat szinkronizálni kell, de a bemutató szempontjából ez az oktatóanyag kifejezetten kéri a felhasználót.

Ha megnyomja ezt a gombot, elindul egy új háttérbeli feladat. Először a helyi tárolóban végzett összes változást leküldi a szinkronizálási környezettel, majd lekéri az Azure-ból származó összes megváltozott információt a helyi táblára.

### <a name="offline-testing"></a>Offline tesztelés
1. Helyezze az eszközt vagy a szimulátort a *repülőgép módba*. Ez egy offline forgatókönyvet hoz létre.
2. Vegyen fel néhány *teendőt* , vagy adjon meg néhány elemet befejezettként. Zárja be az eszközt vagy a szimulátort (vagy kényszerítse az alkalmazás bezárását), majd indítsa újra. Győződjön meg arról, hogy a módosítások megmaradtak az eszközön, mert a helyi SQLite-tárolóban vannak tárolva.
3. Tekintse meg az Azure *TodoItem* tábla tartalmát egy olyan SQL-eszközzel, mint például a *SQL Server Management Studio*vagy egy Rest-ügyfél, például a *Hegedűs* vagy a *Poster*. Annak ellenőrzése, hogy az új elemek *nem* lettek-e szinkronizálva a kiszolgálóval
   
       + A Node. js-háttérrendszer esetében lépjen a [Azure Portalra](https://portal.azure.com/), és a Mobile App backend-ben kattintson az **Easy Tables** > **TodoItem** elemre a `TodoItem` tábla tartalmának megtekintéséhez.
       + .NET-háttér esetében tekintse meg a táblázat tartalmát egy olyan SQL-eszközzel, mint például a *SQL Server Management Studio*vagy egy Rest-ügyfél, például a *Hegedűs* vagy a *Poster*.
4. Kapcsolja be a Wi-Fi-t az eszközön vagy a szimulátorban. Ezután kattintson a **frissítés** gombra.
5. Tekintse meg ismét a TodoItem az Azure Portal. Ekkor meg kell jelennie az új és módosított TodoItems.

## <a name="additional-resources"></a>További források
* [Offline adatszinkronizálás az Azure Mobile Appsban]
* @no__t – 0Cloud fedél: Offline szinkronizálás az Azure-ban Mobile Services @ no__t-0 \(note: a videó Mobile Services, de az offline szinkronizálás hasonló módon működik az Azure Mobile Apps @ no__t-2

<!-- URLs. -->

[Offline adatszinkronizálás az Azure Mobile Appsban]: app-service-mobile-offline-data-sync.md

[Android-alkalmazás létrehozása]: app-service-mobile-android-get-started.md

@no__t – 0Cloud fedél: Offline szinkronizálás az Azure-ban Mobile Services @ no__t-0
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

