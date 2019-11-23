---
title: Offline szinkronizálás engedélyezése az Azure Mobile-alkalmazáshoz (Xamarin. Forms) | Microsoft Docs
description: Ismerje meg, hogyan használható App Service Mobile App a Xamarin. Forms-alkalmazás offline állapotának gyorsítótárazására és szinkronizálására
documentationcenter: xamarin
author: elamalani
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 65bd619a95b102c4ad4be57f1661a89fbae43e22
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388424"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Xamarin.Forms-mobilalkalmazás offline szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné a felhőszolgáltatásokat a mobilalkalmazásba integrálni, regisztráljon az [App Centerbe](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag a Xamarin. Forms Azure Mobile Apps offline szinkronizálási funkcióját mutatja be. Az offline szinkronizálás lehetővé teszi, hogy a végfelhasználók a mobil alkalmazásokkal is használhassák az adatmegjelenítést, hozzáadást vagy módosítást, még akkor is, ha nincs hálózati kapcsolat. A módosításokat a rendszer egy helyi adatbázisban tárolja. Miután az eszköz ismét online állapotba került, a módosítások szinkronizálva lesznek a távoli szolgáltatással.

Ez az oktatóanyag a Xamarin. Forms gyors üzembe helyezési megoldásán alapul, amelyet a Mobile Apps következő oktatóanyagban hozhat létre: [Xamarin iOS-alkalmazás létrehozása]. A Xamarin. Forms gyors üzembe helyezési megoldás az offline szinkronizálást támogató kódot tartalmazza, amelyet csak engedélyezni kell. Ebben az oktatóanyagban frissíti a gyors üzembe helyezési megoldást az Azure Mobile Apps offline szolgáltatásainak bekapcsolásához. Kiemeljük az alkalmazás offline-specifikus kódját is. Ha nem a letöltött gyors üzembe helyezési megoldást használja, hozzá kell adnia az adatelérési bővítmények csomagjait a projekthez. További információ a kiszolgálói bővítmények csomagjairól: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps][1].

Az offline szinkronizálási szolgáltatással kapcsolatos további tudnivalókért tekintse meg az [Offline adatszinkronizálás az Azure Mobile Appsban][2]című témakört.

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Offline szinkronizálási funkciók engedélyezése a gyors üzembe helyezési megoldásban
Az offline szinkronizálási kódot a projekt tartalmazza előfeldolgozó irányelvek C# használatával. Ha az **OFFLINE\_szinkronizálási\_engedélyezve** szimbólum van megadva, a rendszer ezeket a programkód-útvonalakat tartalmazza a buildben. Windows-alkalmazások esetén az SQLite platformot is telepítenie kell.

1. A Visual Studióban kattintson a jobb gombbal a megoldásra > **NuGet-csomagok kezelése**a megoldáshoz..., majd keresse meg és telepítse a **Microsoft. Azure. Mobile. Client. SQLiteStore** NuGet-csomagot a megoldás összes projektje számára.
2. A Megoldáskezelőban nyissa meg a TodoItemManager.cs fájlt a projektből a **Portable** (név) nevű fájlból, amely a következő előfeldolgozói direktíva:

        #define OFFLINE_SYNC_ENABLED
3. Választható A Windows-eszközök támogatásához telepítse a következő SQLite Runtime-csomagok egyikét:

   * **Windows 8,1 futtatókörnyezet:** Telepítse [a Windows 8,1 rendszerhez készült SQLite][3]-t.
   * **Windows Phone-telefon 8,1:** [Az SQLite telepítése Windows Phone-telefon 8,1][4]-re.
   * **Univerzális Windows-platform** [Az SQLite telepítése univerzális univerzális Windows-rendszerre][5].

     Bár a gyors útmutató nem tartalmaz univerzális Windows-projektet, az univerzális Windows-platformot a Xamarin-űrlapok támogatják.
4. Választható Minden Windows-alkalmazás projektben kattintson a jobb gombbal a **hivatkozások** > **hivatkozás hozzáadása...** elemre, bontsa ki a **Windows** mappa > **bővítmények**csomópontot.
    Engedélyezze a megfelelő **SQLite** -t a Windows SDK-hoz, valamint a **Visual C++ 2013 Runtime for Windows** SDK-t.
    Az SQLite SDK-nevek kis mértékben eltérőek az egyes Windows platformokon.

## <a name="review-the-client-sync-code"></a>Az ügyfél-szinkronizálási kód áttekintése
Íme egy rövid áttekintés arról, hogy mi szerepel az oktatóanyag kódjában az `#if OFFLINE_SYNC_ENABLED`-irányelvekben. Az offline szinkronizálási funkció a TodoItemManager.cs Project-fájlban található a hordozható osztály könyvtára projektben. A szolgáltatás fogalmi áttekintését lásd: [Offline adatszinkronizálás az Azure Mobile Appsban][2].

* A tábla műveleteinek elvégzése előtt inicializálni kell a helyi tárolót. A helyi áruház adatbázisát a **TodoItemManager** osztály konstruktora inicializálja a következő kód használatával:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Ez a kód egy új helyi SQLite-adatbázist hoz létre a **MobileServiceSQLiteStore** osztály használatával.

    A **DefineTable** metódus létrehoz egy táblát a helyi tárolóban, amely megfelel a megadott típus mezőinek.  A típusnak nem kell tartalmaznia a távoli adatbázisban lévő összes oszlopot. Az oszlopok egy részhalmaza is tárolható.
* A **TodoItemManager** **todoTable** mezője a **IMobileServiceTable**helyett egy **IMobileServiceSyncTable** -típus. Ez az osztály a helyi adatbázist használja az összes létrehozási, olvasási, frissítési és törlési (szifilisz-) tábla műveleteihez. A **PushAsync** a **IMobileServiceSyncContext**való meghívásával eldöntheti, hogy mikor küldi el ezeket a módosításokat a Mobile apps-háttérbe. A szinkronizálási környezet segít megőrizni a táblák kapcsolatait az összes olyan tábla változásainak nyomon követésével és kitolásával, amelyet az ügyfélalkalmazás a **PushAsync** hívásakor módosított.

    A következő **SyncAsync** metódust kell meghívni a Mobile apps-háttérrel való szinkronizálásra:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Ez a minta az alapértelmezett szinkronizálási kezelővel egyszerű hibakezelés használatával működik. Egy valós alkalmazás kezeli a különböző hibákat, például a hálózati feltételeket és a kiszolgálói ütközéseket egy egyéni **IMobileServiceSyncHandler** implementáció használatával.

## <a name="offline-sync-considerations"></a>Offline szinkronizálási megfontolások
A mintában a **SyncAsync** metódus csak indításkor, illetve a szinkronizálás kérelmezése esetén hívható meg.  Egy Android vagy iOS rendszerű alkalmazásban való szinkronizálás kezdeményezéséhez le kell húzni az elemek listáját; Windows esetén használja a **Sync (szinkronizálás** ) gombot. A valós alkalmazásokban a szinkronizálási triggert is elvégezheti a hálózati állapot megváltozásakor.

Amikor egy lekéréses műveletet hajt végre egy olyan táblán, amely függőben van a környezet által követett helyi frissítésekkel, a lekéréses művelet automatikusan elindítja az előző környezet leküldését. A mintában lévő elemek frissítésekor, hozzáadásakor és befejezésekor kihagyhatja a explicit **PushAsync** hívást.

A megadott kódban a távoli TodoItem tábla összes rekordja le van kérdezve, de a rekordok szűrhetők a lekérdezési azonosító és a lekérdezés **PushAsync**történő átadásával is. További információ: *növekményes szinkronizálás* a [kapcsolat nélküli adatszinkronizálásban az Azure Mobile Appsban][2].

## <a name="run-the-client-app"></a>Az ügyfélalkalmazás futtatása
Ha már engedélyezve van az offline szinkronizálás, futtassa az ügyfélalkalmazás legalább egyszer az egyes platformokon a helyi áruház adatbázisának feltöltéséhez. Később szimulálhat egy offline forgatókönyvet, és módosíthatja a helyi tárolóban lévő adatkapcsolatot, miközben az alkalmazás offline állapotban van.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Az ügyfélalkalmazás szinkronizálási viselkedésének frissítése
Ebben a szakaszban módosítsa az ügyfél-projektet úgy, hogy egy offline forgatókönyvet Szimuláljon, ha érvénytelen alkalmazás-URL-címet használ a háttérrendszer számára. Azt is megteheti, hogy kikapcsolja a hálózati kapcsolatokat úgy, hogy áthelyezi az eszközt a "repülőgép üzemmódba".  Az adatelemek hozzáadásakor vagy módosításakor ezek a módosítások a helyi tárolóban maradnak, de a rendszer nem szinkronizálja őket a háttér-adattárba, amíg újra nem hozza létre a kapcsolódást.

1. A Megoldáskezelő nyissa meg a Constants.cs projektfájlt a **hordozható** projektből, és módosítsa a `ApplicationURL` értékét úgy, hogy az egy érvénytelen URL-címre mutasson:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Nyissa meg a TodoItemManager.cs fájlt a **hordozható** projektből, majd vegyen fel egy **fogást** az alap **kivétel** osztályhoz a **Try... Catch** blokk a **SyncAsync**. Ez a **Catch** blokk a kivételt jelző üzenetet írja a konzolba, a következőképpen:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Hozza létre és futtassa az ügyfélalkalmazás alkalmazást.  Adjon hozzá néhány új elemet. Figyelje meg, hogy a rendszer minden, a háttérrel való szinkronizálásra irányuló kísérlet során kivételt naplóz a konzolon. Ezek az új elemek csak a helyi tárolóban léteznek, amíg a mobil háttérbe nem lesznek leküldve. Az ügyfélalkalmazás úgy viselkedik, mintha csatlakozik a háttérhez, és támogatja az összes létrehozási, olvasási, frissítési, törlési (szifilisz) műveletet.
4. Az alkalmazás bezárásával ellenőrizze, hogy a létrehozott új elemek megmaradnak-e a helyi tárolóban.
5. Választható A Visual Studióval megtekintheti a Azure SQL Database táblázatát, és megtekintheti, hogy a háttér-adatbázis adatai nem módosultak-e.

    A Visual Studióban nyissa meg a **Server Explorert**. Navigáljon az adatbázishoz az **Azure**->**SQL-adatbázisokban**. Kattintson a jobb gombbal az adatbázisra, és válassza **a Megnyitás a SQL Server Object Explorerban**lehetőséget. Most megkeresheti az SQL Database-táblázatot és annak tartalmát.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Az ügyfélalkalmazás frissítése a mobil háttér újrakapcsolódásához
Ebben a szakaszban újra csatlakozik az alkalmazáshoz a mobil háttérrendszer számára, amely szimulálja az alkalmazást egy online állapotba. A frissítési kézmozdulat végrehajtásakor az adatok szinkronizálva vannak a mobil háttérrel.

1. Nyissa meg újra a Constants.cs. Javítsa ki a `applicationURL`, hogy a megfelelő URL-címre mutasson.
2. Hozza létre újra és futtassa az ügyfélalkalmazás alkalmazást. Az alkalmazás az indítás után megpróbál szinkronizálni a Mobile apps-háttérrel. Ellenőrizze, hogy a hibakeresési konzolon nincsenek-e naplózva kivételek.
3. Választható A frissített adatok megtekintése SQL Server Object Explorer vagy egy REST-eszköz, például a Hegedűs vagy a [Poster][6]használatával. Figyelje meg, hogy az adatokat a háttér-adatbázis és a helyi tároló között szinkronizálták.

    Figyelje meg, hogy az adatok szinkronizálva lettek az adatbázis és a helyi tároló között, és tartalmazza azokat az elemeket, amelyeket az alkalmazás leválasztása közben adott meg.

## <a name="additional-resources"></a>További források
* [Offline adatszinkronizálás az Azure Mobile Appsban][2]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
