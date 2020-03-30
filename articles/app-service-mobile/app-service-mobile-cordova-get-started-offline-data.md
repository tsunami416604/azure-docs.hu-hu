---
title: Offline szinkronizálás engedélyezése (Cordova)
description: Ismerje meg, hogyan használhatja az App Service Mobile App segítségével az offline adatok gyorsítótárazására és szinkronizálására a Cordova alkalmazásban.
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 5a2d5ec8da5c1a317039e656f6df884a10efe7c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459418"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Offline szinkronizálás engedélyezése a Cordova mobilalkalmazáshoz
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja az Azure Mobile Apps for Cordova offline szinkronizálási funkcióját. Az offline szinkronizálás lehetővé teszi&mdash;a végfelhasználók számára,&mdash;hogy akkor is kommunikáljanak a mobilalkalmazásokkal, hogy megtekintsék, hozzáadják vagy módosítsák az adatokat, még akkor is, ha nincs hálózati kapcsolat. A módosításokat a helyi adatbázis tárolja.  Ha az eszköz újra online állapotba kerül, ezek a módosítások szinkronizálódnak a távoli szolgáltatással.

Ez az oktatóanyag a Cordova mobilalkalmazásokhoz készült rövid útmutatóján alapul, amelyet az [Apache Cordova gyorsindítás]befejezésekor hoz létre. Ebben az oktatóanyagban frissíti a gyorsindítási megoldást az Azure Mobile Apps offline funkcióinak hozzáadásához.  Az alkalmazás offline-specifikus kódját is kiemeljük.

Az offline szinkronizálási funkcióról az Offline data Sync in Azure Mobile Apps című témakörben olvashat [bővebben.] Az API-használat részleteiről az [API dokumentációjában](https://azure.github.io/azure-mobile-apps-js-client)olvashat.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Kapcsolat nélküli szinkronizálás hozzáadása a gyorsútmutató megoldáshoz
Az offline szinkronizálási kódot hozzá kell adni az alkalmazáshoz. Az offline szinkronizáláshoz szükség van a cordova-sqlite-storage beépülő modulra, amely automatikusan hozzáadódik az alkalmazáshoz, amikor az Azure Mobile Apps beépülő modul szerepel a projektben. A rövid útmutató projekt mindkét bővítményt tartalmazza.

1. A Visual Studio Solution Explorer programban nyissa meg az index.js programot, és cserélje le a következő kódot

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    ezzel a kóddal:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Ezután cserélje ki a következő kódot:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    ezzel a kóddal:

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

    Az előző kódkiegészítések inicializálják a helyi tárolót, és meghatároznak egy helyi táblát, amely megfelel az Azure háttérrendszerében használt oszlopértékeknek. (Nem kell az összes oszlopértéket belefoglalnia ebbe a kódba.)  A `version` mezőt a mobil háttérkapcsolat tartja karban, és ütközésfeloldásra szolgál.

    A **getSyncContext**metódus meghívásával hivatkozást kap a szinkronizálási környezetre. A szinkronizálási környezet segít megőrizni a táblakapcsolatokat azáltal, hogy `.push()` nyomon követi és lenyomja az ügyfélalkalmazás által módosított összes tábla módosításait, amikor meghívják őket.

3. Frissítse az alkalmazás URL-címét a mobilalkalmazás URL-címére.

4. Ezután cserélje le ezt a kódot:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    ezzel a kóddal:

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

    Az előző kód inicializálja a szinkronizálási környezetet, majd meghívja a getSyncTable-t (a getTable helyett), hogy hivatkozást kapjon a helyi táblára.

    Ez a kód a helyi adatbázist használja az összes létrehozási, olvasási, frissítési és törlési (CRUD) táblaművelethez.

    Ez a minta egyszerű hibakezelést hajt végre szinkronizálási ütközések esetén. Egy valódi alkalmazás kezelné a különböző hibákat, például a hálózati feltételeket, a kiszolgálóütközéseket és másokat. A kódpéldákat az [offline szinkronizálási mintában talál.]

5. Ezután adja hozzá ezt a függvényt a tényleges szinkronizálás végrehajtásához.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    A **syncContext.push()** metódus hívásával dönti el, hogy mikor kell lelökni a mobilalkalmazás-háttérkapcsolat módosításait. Meghívhat például **syncBackend** metódust egy szinkronizálási gombhoz kötött gomb eseménykezelőjében.

## <a name="offline-sync-considerations"></a>Kapcsolat nélküli szinkronizálással kapcsolatos szempontok

A mintában a **syncContext** **leküldéses** módja csak az alkalmazás indításakor a visszahívási függvény bejelentkezési.  Egy valós alkalmazásban manuálisan vagy a hálózati állapot változásakor is aktiválhatja ezt a szinkronizálási funkciót.

Ha egy lekéréses művelet végrehajtása egy olyan táblával szemben történik, amelynek környezete által nyomon követett helyi frissítések vannak, a lekéréses művelet automatikusan leküldéses műveletet indít el. A mintában szereplő elemek frissítése, hozzáadása és befejezése kor kihagyhatja az explicit **leküldéses** hívást, mivel az felesleges lehet.

A megadott kódban a távoli todoItem tábla összes rekordja lekérdeződik, de a rekordok szűrhetők is egy lekérdezésazonosító átadásával és a **leküldéses**lekérdezéssel. További információt az Azure Mobile Apps *növekményes szinkronizálása* [az offline adatszinkronizálásban című szakaszban talál.]

## <a name="optional-disable-authentication"></a>(Nem kötelező) Hitelesítés letiltása

Ha nem szeretné beállítani a hitelesítést az offline szinkronizálás tesztelése előtt, fűzze hozzá a bejelentkezésvisszahívási függvényt, de hagyja a kódot a visszahívási függvényben megjegyzés nélkül.  Miután kommentálta a bejelentkezési sorokat, a kód a következőképpen módosul:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Most az alkalmazás szinkronizálja az Azure-háttérszolgáltatás, amikor az alkalmazás futtatásakor.

## <a name="run-the-client-app"></a>Az ügyfélalkalmazás futtatása
Ha az offline szinkronizálás most már engedélyezve van, az ügyfélalkalmazást minden platformon legalább egyszer futtathatja a helyi tároló adatbázisának feltöltéséhez. Később szimuláljon egy offline forgatókönyvet, és módosítsa az adatokat a helyi tárolóban, amíg az alkalmazás offline állapotban van.

## <a name="optional-test-the-sync-behavior"></a>(Nem kötelező) A szinkronizálási viselkedés tesztelése
Ebben a szakaszban módosítja az ügyfélprojektet, hogy egy offline forgatókönyvet szimuláljon egy érvénytelen alkalmazás URL-cím használatával a háttérrendszerhez. Adatelemek hozzáadásakor vagy módosításakor ezek a módosítások a helyi tárolóban tárolódnak, de a rendszer nem szinkronizálja őket a háttéradattárba, amíg a kapcsolat helyre nem áll.

1. A Megoldáskezelőben nyissa meg az index.js projektfájlt, és módosítsa úgy az alkalmazás URL-címét, hogy érvénytelen URL-címre mutasson, például a következő kódra:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Az index.html fájlban `<meta>` frissítse a CSP-elemet ugyanazzal az érvénytelen URL-címmel.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Az ügyfélalkalmazás létrehozása és futtatása, és figyelje meg, hogy egy kivétel van bejelentkezve a konzolon, amikor az alkalmazás a bejelentkezés után megpróbál szinkronizálni a háttérrendszerrel. A hozzáadott új elemek csak a helyi tárolóban léteznek, amíg a mobil háttérprogramra nem kerülnek. Az ügyfélalkalmazás úgy viselkedik, mintha a háttérrendszerhez csatlakozna.

4. Zárja be az alkalmazást, és indítsa újra, és ellenőrizze, hogy az új onnan létrehozott elemek megmaradnak-e a helyi tárolóban.

5. (Nem kötelező) A Visual Studio segítségével tekintse meg az Azure SQL Database-tábla, hogy a háttér-adatbázis adatai nem változtak.

    A Visual Studio programban nyissa meg a **Server Explorer t**. Nyissa meg az adatbázist az **Azure**->**SQL-adatbázisokban.** Kattintson a jobb gombbal az adatbázisra, és válassza **a Megnyitás az SQL Server Object Explorer programban parancsot.** Most már tallózhat az SQL adatbázistábla és annak tartalmát.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Nem kötelező) A mobil háttérrendszerhez való újracsatlakozás tesztelése

Ebben a szakaszban újra csatlakoztatja az alkalmazást a mobil háttérrendszerhez, amely szimulálja, hogy az alkalmazás online állapotba lép vissza. Bejelentkezéskor az adatok szinkronizálódnak a mobil háttérrendszerhez.

1. Nyissa meg újra az index.js-t, és állítsa vissza az alkalmazás URL-címét.
2. Nyissa meg újra az index.html fájlt, `<meta>` és javítsa ki az alkalmazás URL-címét a kriptaelemben.
3. Építse újra és futtassa az ügyfélalkalmazást. Az alkalmazás a bejelentkezés után megkísérli a szinkronizálást a mobilalkalmazás-háttérrendszerrel. Ellenőrizze, hogy nincs-e kivétel a hibakeresési konzolon.
4. (Nem kötelező) Tekintse meg a frissített adatokat az SQL Server Object Explorer vagy a REST eszköz, például a Fiddler segítségével. Figyelje meg, hogy az adatok szinkronizálva vannak a háttéradatbázis és a helyi tároló között.

    Figyelje meg, hogy az adatok szinkronizálva vannak az adatbázis és a helyi tároló között, és az alkalmazás leválasztása közben hozzáadott elemeket tartalmazzák.

## <a name="additional-resources"></a>További források
* [Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>További lépések
* Tekintse át a kapcsolat nélküli szinkronizálás fejlettebb funkcióit, például az ütközésfeloldást a [kapcsolat nélküli szinkronizálási mintában]
* Tekintse át az offline szinkronizálási API-hivatkozást az [API dokumentációjában.](https://azure.github.io/azure-mobile-apps-js-client)

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova gyorsindítás]: app-service-mobile-cordova-get-started.md
[kapcsolat nélküli szinkronizálási minta]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
