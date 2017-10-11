---
title: "A Azure Mobile Apps (Cordova) kapcsolat nélküli szinkronizálásának engedélyezése |} Microsoft Docs"
description: "App Service Mobile Apps a Cordova-alkalmazás gyorsítótárába, a szinkronizálási kapcsolat nélküli adatainak használata"
documentationcenter: cordova
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: glenga
ms.openlocfilehash: 45e80ca672dfdb6defc6e5c1aac3d29f5479125c
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>A mobil Cordova-alkalmazás kapcsolat nélküli szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Ez az oktatóanyag a kapcsolat nélküli szinkronizálás szolgáltatást, az Azure Mobile Apps a Cordova vezet be. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára a mobilalkalmazás együttműködhet&mdash;megtekintését, hozzáadását és módosítását adatok&mdash;akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban tárolódnak.  Az eszköz újra online állapotba kerül, ha ezek a változások szinkronizálása megtörtént-e a távoli szolgáltatás.

Ez az oktatóanyag alapul a Cordova gyors üzembe helyezés megoldást hoz létre, ha az oktatóanyag befejezése Mobile Apps-alkalmazáshoz [Apache Cordova gyors üzembe helyezési]. Ebben az oktatóanyagban az Azure Mobile Apps offline funkciók hozzáadása a gyors üzembe helyezés megoldás frissítése.  Azt is kiemelése a kapcsolat nélküli tartozó kódot az alkalmazásban.

A kapcsolat nélküli szinkronizálás szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [az Azure Mobile Apps Offline adatszinkronizálás]. API-használati részletekért lásd: a [API-JÁNAK dokumentációja](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Kapcsolat nélküli szinkronizálás hozzáadása a gyors üzembe helyezés megoldáshoz
A kapcsolat nélküli szinkronizálás kódot kell felvenni az alkalmazás. Kapcsolat nélküli szinkronizálás a cordova-sqlite-tároló beépülő modul, amely automatikusan bekerül az alkalmazáshoz, ha a projekt tartalmazza az Azure Mobile Apps beépülő modul szükséges. A Gyorsútmutató-projekt tartalmazza a beépülő modulok mindegyikét.

1. A Visual Studio Solution Explorerben nyissa meg a index.js, és cserélje le a következő kódot

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

    Az előző kód kiegészítésekkel inicializálása a helyi tárolójába, és adjon meg egy helyi táblát, amely az oszlopértékeket használt megfelel az Azure-ban háttér. (Nincs szükség minden oszlopértékeket foglalandó ezt a kódot.)  A `version` mező által a mobil-háttéralkalmazást, és ütközésfeloldás közben használható.

    A szinkronizálási környezetet hivatkozást kap meghívásával **getSyncContext**. A szinkronizálási környezetet segíti követésével és kérdez le a módosításokat minden olyan táblát, egy ügyfélalkalmazás módosította, ha a tábla közötti megőrzése érdekében `.push()` nevezik.

3. Frissítse az alkalmazás URL-címet a mobilalkalmazás alkalmazás URL-CÍMÉT.

4. Ezután cserélje le ezt a kódot:

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

    Az előzőekben látható kód inicializálja a szinkronizálási környezetet, és ekkor meghívja a getSyncTable (helyett getTable) történő hivatkozás a helyi táblába.

    A kódot használja az összes helyi adatbázis létrehozása, olvasása, frissítése és törlése (CRUD) tábla műveletek.

    Ez a minta egyszerű hibakezelési szinkronizálási ütközések hajt végre. Egy valós alkalmazás hálózati feltételek mellett, a kiszolgáló ütközések és mások például a különféle hibák szeretné kezelni. Kód példákért lásd a [kapcsolat nélküli szinkronizálás minta].

5. Ezután adja hozzá ezt a funkciót a tényleges szinkronizálás végrehajtásához.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Úgy dönt, hogy mikor kell a Mobile Apps-háttéralkalmazás meghívásával változásainak leküldése **syncContext.push()**. Például hívhatja **syncBackend** a szinkronizálás gomb kötve egy gomb eseménykezelő.

## <a name="offline-sync-considerations"></a>Kapcsolat nélküli szinkronizálás kapcsolatos szempontok

A példában a **leküldéses** metódusában **syncContext** alkalmazás következő indításakor a visszahívási függvény bejelentkezési azonosító csak nevezik.  Egy valós alkalmazás is teheti a szinkronizálási funkció manuálisan elindítva, vagy ha hálózati állapota megváltozik.

Elleni táblának függőben lévő lekérési végrehajtásakor helyi frissítések követi nyomon a környezetben, hogy a pull művelet automatikusan eseményindítók leküldéses. Frissítéskor, hozzáadását, és ebben a példában szereplő elemek befejezése, akkor kihagyhatja a explicit **leküldéses** hívható, mert elképzelhető, hogy a redundáns.

A megadott kód, a rendszer megkérdezi a a távoli todoItem tábla összes adatát, de a rekordok szűrése úgy, hogy a lekérdezés azonosítóját, és a lekérdezési lehetőség arra is **leküldéses**. További információkért tekintse meg a szakasz *növekményes szinkronizálás* a [az Azure Mobile Apps Offline adatszinkronizálás].

## <a name="optional-disable-authentication"></a>(Választható) Hitelesítés letiltása

Ha nem szeretné tesztelési kapcsolat nélküli szinkronizálás előtt hitelesítés beállítása, a visszahívási függvény bejelentkezési azonosítóhoz megjegyzéssé, de hagyja meg a kódot a visszahívási függvény uncommented.  Után fűzött megjegyzések ki a bejelentkezési sorok, a kódot a következőképpen:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Most az alkalmazás szinkronizál az Azure-háttérrendszernek az alkalmazás futtatásakor.

## <a name="run-the-client-app"></a>Az ügyfél-alkalmazás futtatása
A kapcsolat nélküli szinkronizálás most engedélyezve van futtathatja az ügyfélalkalmazás legalább egyszer a helyi tárolójába adatbázisának feltöltése minden platformon. Később egy kapcsolat nélküli forgatókönyv szimulálása, és módosíthatja az adatokat, a helyi tárolóban levő, amikor az alkalmazás offline állapotban.

## <a name="optional-test-the-sync-behavior"></a>(Választható) A szinkronizálás viselkedés tesztelése
Ebben a szakaszban módosítsa az ügyfél-projektet egy kapcsolat nélküli forgatókönyv szimulálása a háttérkiszolgáló URL-címének érvénytelen az alkalmazás használatával. Amikor hozzá, vagy módosítsa az elemeket, ezeket a módosításokat a helyi tárolóban levő tranzakció kimenetelére várva, de nem lett szinkronizálva a háttérrendszer adattárolóhoz mindaddig, amíg a kapcsolat helyreáll.

1. A Solution Explorerben nyissa meg a index.js projektfájlt, és módosítsa az alkalmazás URL-címet mutasson az egy URL-cím érvénytelen, például a következő kódot:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Index.html, frissítse a kriptográfiai Szolgáltató `<meta>` az azonos érvénytelen URL-CÍMMEL rendelkező elemet.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Hozza létre, és futtassa az ügyfél-alkalmazást, és figyelje meg, hogy egy kivételt a konzol kerül, amikor az alkalmazás kísérel meg szinkronizálni a háttéralkalmazással való bejelentkezés után. Hozzáadhat új elemek csak a helyi tárolóban levő létezik, csak azokat a mobil-háttéralkalmazást vannak leküldve. Az ügyfélalkalmazás úgy viselkedik, mintha a háttérrendszer van csatlakoztatva.

4. Zárja be az alkalmazást, és indítsa újra, hogy ellenőrizze, hogy a helyi tárolójába őrződnek meg a létrehozott új elemeket.

5. (Választható) Visual Studio használatával az Azure SQL Database tábla, hogy a háttér-adatbázis adatai nem változott meg.

    A Visual Studióban nyissa meg a **Server Explorer**. Keresse meg az adatbázist a **Azure**->**SQL-adatbázisok**. Kattintson a jobb gombbal az adatbázist, és válassza ki **nyissa meg az SQL Server Object Explorerben**. Most már megkeresheti az SQL-adatbázistáblában szereplő és annak tartalmát.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Választható) Az újracsatlakozás a mobil-háttéralkalmazást történő tesztelése

Ebben a szakaszban az alkalmazásnak, hogy a mobil-háttéralkalmazást, az alkalmazáshoz, és az online állapot vissza hamarosan szimulálja újra. Jelentkezzen be, amikor adatok szinkronizálva van a mobil-háttéralkalmazást.

1. Nyissa meg újra a index.js, és állítsa vissza az alkalmazás URL-címet.
2. Nyissa meg újra a index.html, és javítsa ki az alkalmazás URL-címet a kriptográfiai Szolgáltató `<meta>` elemet.
3. Építse újra, és futtassa az ügyfél-alkalmazást. Az alkalmazás megpróbálja szinkronizálni a mobil-háttéralkalmazás bejelentkezés után. Győződjön meg arról, hogy nem tartalmaznak kivételeket a hibakereső konzol vannak bejelentkezve.
4. (Választható) Az SQL Server Object Explorer vagy egy REST-eszköz, például Fiddler használatával frissített adatok megtekintéséhez. Figyelje meg, az adatok szinkronizálása a háttérbeli adatbázis és a helyi tárolójába.

    Figyelje meg, az adatok az adatbázis és a helyi tárolójába lett szinkronizálva, és az alkalmazás forráshoz hozzáadott elemeket tartalmazza.

## <a name="additional-resources"></a>További források
* [az Azure Mobile Apps Offline adatszinkronizálás]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>Következő lépések
* Tekintse át a fejlettebb, kapcsolat nélküli szinkronizálás szolgáltatások, például az ütközések feloldása a [kapcsolat nélküli szinkronizálás minta]
* Tekintse át a kapcsolat nélküli szinkronizálás API-hivatkozás a [API-JÁNAK dokumentációja](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova gyors üzembe helyezési]: app-service-mobile-cordova-get-started.md
[kapcsolat nélküli szinkronizálás minta]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[az Azure Mobile Apps Offline adatszinkronizálás]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
