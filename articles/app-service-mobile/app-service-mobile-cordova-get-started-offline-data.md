---
title: Offline szinkronizálás engedélyezése az Azure Mobile App (Cordova) |} A Microsoft Docs
description: Ismerje meg, hogyan App Service Mobile Apps segítségével a Cordova-alkalmazás cache és a szinkronizálási kapcsolat nélküli adatokat
documentationcenter: cordova
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 44c54b570a38eb1a3b9ca773893599d1d497dfa2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972150"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>A Cordova-mobilalkalmazás offline szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

Ebben az oktatóanyagban a kapcsolat nélküli szinkronizálás – a szolgáltatás az Azure Mobile Apps Cordova számára mutatja be. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, hogy kommunikáljanak a mobilalkalmazás&mdash;megtekintését, hozzáadását és módosítását adatok&mdash;akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban vannak tárolva.  Miután az eszköz újra online állapotba kerül, ezeket a módosításokat a távoli szolgáltatással vannak szinkronizálva.

Ebben az oktatóanyagban alapul a Cordova rövid megoldás hoz létre, amikor az oktatóanyag befejezése Mobile Apps-alkalmazáshoz [Az Apache Cordova – gyorsútmutató]. Ebben az oktatóanyagban a rövid útmutató megoldást, az Azure Mobile Apps offline funkciók hozzáadása, frissítése.  Azt is ki a kapcsolat nélküli tartozó kódot az alkalmazásban.

A kapcsolat nélküli szinkronizálás – szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [Offline adatszinkronizálás az Azure Mobile Appsban]. További információ az API-használat: a [API-dokumentáció](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Offline szinkronizálást adhat a gyors megoldás
A kapcsolat nélküli szinkronizálás – kód hozzá kell adni az alkalmazáshoz. Offline szinkronizálás szükséges a cordova-sqlite-storage beépülő modul, amely automatikusan bekerül az alkalmazáshoz, ha a projekt tartalmazza az Azure Mobile Apps beépülő modulját. A gyorsindítási projekt mindkét alábbi beépülő modulokat tartalmaz.

1. A Visual Studio Megoldáskezelőben nyissa meg az index.js, és cserélje le a következő kód

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

    Az előző kód hozzáadása a helyi tároló inicializálása, és adjon meg egy helyi táblát, amely megfelel az oszlopértékeket, használja az Azure háttér. (Nincs szükség minden oszlop értékeit tartalmazza az ebben a kódban.)  A `version` mező a mobil háttérszolgáltatásban által karbantartott és ütközésfeloldás szolgál.

    A szinkronizálási környezetet egy hivatkozást kap meghívásával **getSyncContext**. A szinkronizálási környezetet segít nyomon követésével és módosítások leküldése minden táblát egy ügyfélalkalmazás mikor módosult a táblák közötti kapcsolatok megőrzése `.push()` nevezzük.

3. Frissítse a Mobile Apps-alkalmazás URL-címe az alkalmazás URL-címet.

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

    A fenti kód inicializálja a szinkronizálási környezetet, és ezután meghív egy hivatkozást a helyi táblát beolvasni getSyncTable (helyett getTable).

    A kódot használ, az összes helyi adatbázis létrehozása, olvasása, frissítése és törlési (CRUD) tábla műveletek.

    Ez a minta egyszerű hibakezelési szinkronizálási ütközések hajt végre. Egy valódi alkalmazás szeretné kezelni a különféle hibák, például hálózati feltételek, a kiszolgáló ütközéseket és mások. Hitelesítésikód-példák, lásd: a [kapcsolat nélküli szinkronizálás – minta].

5. Ezután adja hozzá a függvény a tényleges szinkronizálás végrehajtásához.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Úgy dönt, hogy mikor változásainak leküldése a Mobile Apps-háttéralkalmazás meghívásával **syncContext.push()**. Például hívhatja **syncBackend** egy gomb eseménykezelőt a szinkronizálás gomb kötve.

## <a name="offline-sync-considerations"></a>Kapcsolat nélküli szinkronizálás – szempontok

A mintában a **leküldéses** metódusa **syncContext** csak az alkalmazás indításakor a visszahívási függvény bejelentkezési neve.  Egy valós alkalmazás esetében is teheti a szinkronizálási funkció manuálisan aktivált, vagy ha a hálózat állapota megváltozik.

Egy táblázaton, amely rendelkezik a függőben lévő lekérési végrehajtásakor helyi frissítések által nyomon követett kapcsolatban, hogy a pull művelet automatikusan eseményindítók egy leküldéses. Frissítésekor, felvétele és az ebben a példában szereplő elemek befejezése, akkor kihagyhatja az explicit **leküldéses** hívásokat indítani, mert elképzelhető, hogy a redundáns.

A megadott kód, a távoli todoItem tábla összes rekordot a rendszer megkérdezi, de azt is lehetővé teszi a rekordok szűrése a lekérdezés azonosítóját átadásával és lekérdezése **leküldéses**. További információkért lásd: a szakasz *növekményes szinkronizálás* a [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="optional-disable-authentication"></a>(Nem kötelező) Hitelesítés letiltása

Ha nem szeretné, állítsa be a hitelesítést, mielőtt tesztelési offline szinkronizálás, tegye megjegyzésbe a visszahívási függvény a bejelentkezéshez, de hagyja meg a kódot a uncommented a visszahívási függvény.  Megjegyzéseket ki a bejelentkezési sorok, miután a kódot a következőképpen:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Most az alkalmazás szinkronizál az Azure-beli háttéralkalmazásának az alkalmazás futtatásakor.

## <a name="run-the-client-app"></a>Az ügyfélalkalmazás futtatása
Offline szinkronizálás most már engedélyezve van futtathat az ügyfélalkalmazás legalább egyszer egyes platformokon a helyi tároló adatbázisának feltöltéséhez. Később szimulálása egy offline forgatókönyvet, és módosíthatja az adatokat, a helyi tároló, amikor az alkalmazás offline állapotban van.

## <a name="optional-test-the-sync-behavior"></a>(Nem kötelező) A szinkronizálási viselkedésének tesztelése
Ebben a szakaszban módosítsa az ügyfélprojekt szimulálása egy kapcsolat nélküli forgatókönyv biztosítani a háttérbeli érvénytelen az alkalmazás URL-címének használatával. Hozzáadásakor, vagy módosítsa az elemeket, ezeket a módosításokat a helyi tároló tartanak, de a rendszer nem szinkronizálja a háttérbeli adattárba mindaddig, amíg a kapcsolat helyreáll.

1. A megoldáskezelőben nyissa meg az index.js projektfájlt, és módosítsa az alkalmazás URL-Címének átirányítása egy URL-cím érvénytelen, például a következő kódot:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Index.html, frissítse a CSP `<meta>` elemet az azonos URL-cím érvénytelen.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Hozhat létre és futtassa az ügyfélalkalmazást, és figyelje meg, hogy kivétel kerül a konzolon, amikor az alkalmazás kísérel meg szinkronizálni a háttér-bejelentkezés után. Hozzáadhat új elemeket szerepel csak a helyi tároló mindaddig, amíg azok kerüljenek a mobil háttérszolgáltatás használatára. Az ügyfélalkalmazás úgy viselkedik, csatlakoztatva van a háttérrendszerhez.

4. Zárja be az alkalmazást, és indítsa újra, hogy ellenőrizze, hogy a létrehozott új elemek, a helyi tárolóban tárolja.

5. (Nem kötelező) A Visual Studio segítségével megtekintheti az Azure SQL Database adatbázistábla megtekintéséhez, hogy a háttér adatbázis adatait nem változott.

    A Visual Studióban nyissa meg a **Server Explorer**. Keresse meg az adatbázist a **Azure**->**SQL-adatbázisok**. Kattintson a jobb gombbal az adatbázishoz, és válassza ki **nyissa meg az SQL Server Object Explorer**. Most megnyithatja az SQL database tábla és annak tartalmát.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Nem kötelező) A mobil háttérszolgáltatásban való újracsatlakozás tesztelése

Ebben a szakaszban kapcsolódjon újra az alkalmazást, hogy a mobil háttérszolgáltatás használatára, amely szimulálja az alkalmazást, és az online állapot vissza hamarosan. Amikor bejelentkezik, adatok szinkronizálva van a mobil háttérszolgáltatás használatára.

1. Nyissa meg újra az index.js, és állítsa vissza az alkalmazás URL-címet.
2. Nyissa meg újra index.html, és javítsa ki az alkalmazás URL-címet a CSP `<meta>` elemet.
3. Építse újra, és futtassa az ügyfélalkalmazást. Az alkalmazást próbál meg bejelentkezés után a mobil-háttéralkalmazás szinkronizálni. Győződjön meg arról, hogy nincs kivétel jelentkezett-e a hibakeresési konzolt.
4. (Nem kötelező) Tekintse meg a frissített adatokat az SQL Server Object Explorer vagy egy REST-eszköz, például a Fiddler használatával. Figyelje meg, a háttérbeli adatbázis és a helyi tároló szinkronizálta az adatokat.

    Figyelje meg, hogy az adatbázis és a helyi tároló között szinkronizálta az adatokat, és tartalmazza az alkalmazás leválasztott hozzáadott elemek.

## <a name="additional-resources"></a>További források
* [Offline adatszinkronizálás az Azure Mobile Appsban]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>További lépések
* Kapcsolat nélküli szinkronizálás – szolgáltatások, például az ütközések feloldásának speciális tekintse át a [kapcsolat nélküli szinkronizálás – minta]
* A kapcsolat nélküli szinkronizálás – API-referencia a tekintse át a [API-dokumentáció](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Az Apache Cordova – gyorsútmutató]: app-service-mobile-cordova-get-started.md
[kapcsolat nélküli szinkronizálás – minta]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
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
