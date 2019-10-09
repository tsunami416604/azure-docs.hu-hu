---
title: Offline szinkronizálás engedélyezése az Azure Mobile-alkalmazáshoz (Cordova) | Microsoft Docs
description: Megtudhatja, hogyan használhatja App Service Mobile App-t a Cordova-alkalmazás offline adatainak gyorsítótárazásához és szinkronizálásához
documentationcenter: cordova
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: dc1183e1557d634ab1880376a1347f43f33b329f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027501"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Offline szinkronizálás engedélyezése a Cordova Mobile-alkalmazáshoz
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center támogatja a teljes körű és integrált szolgáltatások központi használatát a Mobile apps fejlesztéséhez. A fejlesztők a szolgáltatások **kiépítését**, **tesztelését** és **terjesztését** használhatják a folyamatos integráció és a kézbesítési folyamat beállításához. Az alkalmazás üzembe helyezését követően a fejlesztők az **elemzési** és **diagnosztikai** szolgáltatások segítségével ellenőrizhetik az alkalmazás állapotát és használatát, és a **leküldéses** szolgáltatást használó felhasználókkal is elvégezhetik a felhasználókat. A fejlesztők **a hitelesítést a** felhasználók **és az adatszolgáltatások** hitelesítésére is használhatják a Felhőbeli alkalmazásadatok megőrzése és szinkronizálása érdekében.
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag a Cordova Azure Mobile Apps offline szinkronizálási funkcióját mutatja be. Az offline szinkronizálás lehetővé teszi, hogy a végfelhasználók a @ no__t-0viewing használatával, a @ no__t-1even hozzáadásával vagy módosításával kommunikálnak, ha nincs hálózati kapcsolat. A módosításokat a rendszer egy helyi adatbázisban tárolja.  Miután az eszköz ismét online állapotba került, a módosítások szinkronizálva lesznek a távoli szolgáltatással.

Ez az oktatóanyag az [Apache Cordova – gyors üzembe helyezés]során létrehozott Mobile apps Cordova rövid útmutató megoldásán alapul. Ebben az oktatóanyagban frissíti a gyors üzembe helyezési megoldást az Azure Mobile Apps offline szolgáltatásainak hozzáadásához.  Kiemeljük az alkalmazás offline-specifikus kódját is.

Az offline szinkronizálási szolgáltatással kapcsolatos további tudnivalókért tekintse meg az [Offline adatszinkronizálás az Azure Mobile Appsban]című témakört. Az API-használat részleteiért tekintse meg az [API dokumentációját](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Offline szinkronizálás hozzáadása a gyors üzembe helyezési megoldáshoz
Az offline szinkronizálási kódot hozzá kell adni az alkalmazáshoz. Az offline szinkronizáláshoz a Cordova-SQLite-Storage beépülő modul szükséges, amely automatikusan bekerül az alkalmazásba, amikor az Azure Mobile Apps beépülő modult belefoglalja a projektbe. A Gyorsindítás projekt mindkét beépülő modult tartalmazza.

1. A Visual Studio Megoldáskezelő nyissa meg az index. js fájlt, és cserélje le a következő kódot

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    Ezzel a kóddal:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Ezután cserélje le a következő kódot:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    Ezzel a kóddal:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    Az előző kód kiegészítései inicializálják a helyi tárolót, és meghatározhatnak egy helyi táblát, amely megfelel az Azure háttérbeli oszlop értékeinek. (A kódban nem kell szerepelnie az összes oszlop értékének.)  A `version` mezőt a mobil háttér tartja karban, és az ütközés feloldására szolgál.

    A **getSyncContext**meghívásával a szinkronizálási környezetre mutató hivatkozás fog megjelenni. A szinkronizálási környezet segít megőrizni a táblák kapcsolatait az összes olyan tábla változásainak nyomon követésével és a változtatások megadásával, amelyet az ügyfélalkalmazás a `.push()` hívásakor módosított.

3. Frissítse az alkalmazás URL-címét a Mobile App Application URL-címére.

4. Ezután cserélje le a következő kódot:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    Ezzel a kóddal:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    Az előző kód inicializálja a szinkronizálási környezetet, majd meghívja a getSyncTable (getTable helyett) a helyi táblára mutató hivatkozás beszerzéséhez.

    Ez a kód a helyi adatbázist használja az összes létrehozási, olvasási, frissítési és törlési (szifilisz-) tábla műveleteihez.

    Ez a minta egyszerű hibakezelés-kezelést végez a szinkronizálási ütközések esetében. Egy valós alkalmazás kezeli a különböző hibákat, például a hálózati körülményeket, a kiszolgálói ütközéseket és egyebeket. A kódokra vonatkozó példákért tekintse meg az [Offline szinkronizálási minta].

5. Ezután adja hozzá ezt a függvényt a tényleges szinkronizálás végrehajtásához.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    A **syncContext. push ()** metódus meghívásával eldöntheti, hogy mikor küldi el a módosításokat a Mobile apps-háttérbe. Például meghívhatja a **syncBackend** egy szinkronizálási gombhoz kötött gombos eseménykezelőben.

## <a name="offline-sync-considerations"></a>Offline szinkronizálási megfontolások

A mintában a **syncContext** **leküldéses** metódusát a rendszer csak az alkalmazás indításakor hívja meg a bejelentkezéshez a visszahívási függvényben.  Egy valós alkalmazásban azt is megteheti, hogy ezt a szinkronizálási funkciót manuálisan vagy a hálózati állapot megváltozásakor indítja el.

Amikor egy lekéréses műveletet hajt végre egy olyan táblán, amely függőben van a környezet által követett helyi frissítésekkel, a lekéréses művelet automatikusan elindít egy leküldéses műveletet. A mintában lévő elemek frissítésekor, hozzáadásakor és befejezésekor kihagyhatja a explicit **leküldéses** hívást, mivel az lehet redundáns.

A megadott kódban a távoli todoItem tábla összes rekordja le van kérdezve, de a rekordok szűrhetők a lekérdezési azonosító és a lekérdezés **leküldéséhez**. További információ: *növekményes szinkronizálás* a [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="optional-disable-authentication"></a>Választható Hitelesítés letiltása

Ha nem szeretné beállítani a hitelesítést az offline szinkronizálás tesztelése előtt, jegyezze fel a visszahívási függvényt a bejelentkezéshez, de hagyja meg a visszahívási függvényben a kód Megjegyzés nélküli beállítását.  A bejelentkezési sorok megjegyzése után a kód a következőt követi:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Az alkalmazás az alkalmazás futtatásakor szinkronizál az Azure-háttérrel.

## <a name="run-the-client-app"></a>Az ügyfélalkalmazás futtatása
Ha már engedélyezve van az offline szinkronizálás, az ügyfélalkalmazás legalább egyszer futtatható az egyes platformokon a helyi áruház adatbázisának feltöltéséhez. Később szimulálhat egy offline forgatókönyvet, és módosíthatja a helyi tárolóban lévő adatkapcsolatot, miközben az alkalmazás offline állapotban van.

## <a name="optional-test-the-sync-behavior"></a>Választható A szinkronizálási viselkedés tesztelése
Ebben a szakaszban az ügyfél-projektet úgy módosítja, hogy egy offline forgatókönyvet Szimuláljon, ha érvénytelen alkalmazás-URL-címet használ a háttérrendszer számára. Az adatelemek hozzáadásakor vagy módosításakor ezek a módosítások a helyi tárolóban vannak tárolva, de a rendszer nem szinkronizálja őket a háttér-adattárba, amíg újra nem jön létre.

1. A Megoldáskezelő nyissa meg az index. js projektfájlt, és módosítsa az alkalmazás URL-címét úgy, hogy az érvénytelen URL-címre mutasson, például a következő kóddal:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Az index. html fájlban frissítse a CSP `<meta>` elemet ugyanazzal az érvénytelen URL-címmel.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Hozza létre és futtassa az ügyfélalkalmazás alkalmazást, és figyelje meg, hogy a rendszer egy kivételt naplóz a konzolon, amikor az alkalmazás a bejelentkezést követően megpróbál szinkronizálni a háttérrel. A hozzáadott új elemek csak a helyi tárolóban léteznek, amíg el nem küldik őket a mobil háttérbe. Az ügyfélalkalmazás úgy viselkedik, mintha a háttérhöz csatlakozik.

4. Az alkalmazás bezárásával ellenőrizze, hogy a létrehozott új elemek megmaradnak-e a helyi tárolóban.

5. Választható A Visual Studióval megtekintheti a Azure SQL Database táblázatát, és megtekintheti, hogy a háttér-adatbázis adatai nem módosultak-e.

    A Visual Studióban nyissa meg a **Server Explorert**. Navigáljon az adatbázishoz az **Azure**->**SQL-adatbázisokban**. Kattintson a jobb gombbal az adatbázisra, és válassza **a Megnyitás a SQL Server Object Explorerban**lehetőséget. Most megkeresheti az SQL Database-táblázatot és annak tartalmát.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>Választható A mobil háttér-Újrakapcsolódás tesztelése

Ebben a szakaszban újra csatlakozik az alkalmazáshoz a mobil háttérrendszer számára, amely szimulálja az alkalmazást egy online állapotba. Amikor bejelentkezik, a rendszer szinkronizálja az adatait a mobil háttérrel.

1. Nyissa meg újra az index. js fájlt, és állítsa vissza az alkalmazás URL-címét.
2. Nyissa meg újra az index. html fájlt, és javítsa ki az alkalmazás URL-címét a CSP `<meta>` elemben.
3. Hozza létre újra és futtassa az ügyfélalkalmazás alkalmazást. A bejelentkezés után az alkalmazás megpróbál szinkronizálni a Mobile apps-háttérrel. Ellenőrizze, hogy a hibakeresési konzolon nincsenek-e naplózva kivételek.
4. Választható A frissített adatok megtekintése SQL Server Object Explorer vagy REST-eszköz, például a Hegedűs használatával. Figyelje meg, hogy az adatokat a háttér-adatbázis és a helyi tároló között szinkronizálták.

    Figyelje meg, hogy az adatok szinkronizálva lettek az adatbázis és a helyi tároló között, és tartalmazza azokat az elemeket, amelyeket az alkalmazás leválasztása közben adott meg.

## <a name="additional-resources"></a>További források
* [Offline adatszinkronizálás az Azure Mobile Appsban]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>További lépések
* Tekintse át a fejlettebb offline szinkronizálási funkciókat, például az ütközés feloldását az [Offline szinkronizálási minta] .
* Tekintse át a kapcsolat nélküli szinkronizálás API-referenciáját az [API dokumentációjában](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova – gyors üzembe helyezés]: app-service-mobile-cordova-get-started.md
[Offline szinkronizálási minta]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Offline adatszinkronizálás az Azure Mobile Appsban]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
