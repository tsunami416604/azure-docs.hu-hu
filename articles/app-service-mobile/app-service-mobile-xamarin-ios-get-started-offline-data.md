---
title: Offline szinkronizálás engedélyezése az Azure Mobile-alkalmazáshoz (Xamarin iOS)
description: Megtudhatja, hogyan használhatja a App Service Mobile App-t a Xamarin iOS-alkalmazásban tárolt offline állapotok gyorsítótárazásához és szinkronizálásához
documentationcenter: xamarin
author: elamalani
manager: cfowler
editor: ''
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: fc4fd639f364a0d0852ed066d26eb0e3cf73e68c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388371"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Tegye lehetővé az offline szinkronizálást Xamarin.iOS-mobilalkalmazásában
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> A Visual Studio App Center támogatja a végpontok közötti, valamint az integrált szolgáltatásközpont és a mobilalkalmazás közötti fejlesztést. A fejlesztők **buildelési**, **tesztelési** és **elosztási** szolgáltatásokkal állíthatják be a folyamatos integrációval és szolgáltatásnyújtással kapcsolatos folyamatot. Az alkalmazás üzembe helyezése után a fejlesztők **elemzési** és **diagnosztikai** szolgáltatásokkal monitorozhatják az alkalmazás állapotát és használatát, illetve **leküldéses** szolgáltatással kommunikálhatnak a felhasználókkal. Emellett a fejlesztők a **Hitelesítés** szolgáltatással hitelesíthetik felhasználóikat, az **Adatok** szolgáltatással pedig megőrizhetik és szinkronizálhatják az alkalmazásadatokat a felhőben.
>
> Ha szeretné a felhőszolgáltatásokat a mobilalkalmazásba integrálni, regisztráljon az [App Centerbe](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag a Xamarin. iOS-hez készült Azure Mobile Apps offline szinkronizálási funkcióját mutatja be. Az offline szinkronizálás lehetővé teszi, hogy a végfelhasználók a mobil alkalmazásokkal is használhassák az adatmegjelenítést, hozzáadást vagy módosítást, még akkor is, ha nincs hálózati kapcsolat. A módosításokat a rendszer egy helyi adatbázisban tárolja. Miután az eszköz ismét online állapotba került, a módosítások szinkronizálva lesznek a távoli szolgáltatással.

Ebben az oktatóanyagban frissítse a Xamarin. iOS-alkalmazás projektjét, és [Xamarin iOS-alkalmazás létrehozása] az Azure Mobile apps offline szolgáltatásainak támogatásához. Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, hozzá kell adnia az adatelérési bővítmények csomagjait a projekthez. További információ a kiszolgálói bővítmények csomagjairól: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Az offline szinkronizálási szolgáltatással kapcsolatos további tudnivalókért tekintse meg az [Offline adatszinkronizálás az Azure Mobile Appsban]című témakört.

## <a name="update-the-client-app-to-support-offline-features"></a>Az ügyfélalkalmazás frissítése az offline funkciók támogatására
Az Azure Mobile App offline funkciói lehetővé teszik a helyi adatbázissal való interakciót, ha offline környezetben van. Ha az alkalmazásban szeretné használni ezeket a funkciókat, inicializáljon egy [SyncContext] egy helyi tárolóba. A [IMobileServiceSyncTable] felületen keresztül hivatkozhat a táblára. Az SQLite a helyi tárolóként van használatban az eszközön.

1. Nyissa meg a NuGet csomagkezelő eszközt a projektben, amelyet a [Xamarin iOS-alkalmazás létrehozása] oktatóanyagban végzett, majd keresse meg és telepítse a **Microsoft. Azure. Mobile. Client. SQLiteStore** NuGet csomagot.
2. Nyissa meg a QSTodoService.cs fájlt, és tegye meg a `#define OFFLINE_SYNC_ENABLED` definíciójának megjegyzését.
3. Hozza létre újra és futtassa az ügyfélalkalmazás alkalmazást. Az alkalmazás ugyanúgy működik, mint az offline szinkronizálás engedélyezése előtt. A helyi adatbázis azonban már fel van töltve olyan adatokkal, amelyek offline környezetben használhatók.

## <a name="update-sync"></a>Az alkalmazás frissítése a háttérből való leválasztáshoz
Ebben a szakaszban megszakítja a kapcsolatát a Mobile apps-háttérrel az offline állapot szimulálása érdekében. Adatelemek hozzáadásakor a kivétel kezelője jelzi, hogy az alkalmazás kapcsolat nélküli módban van. Ebben az állapotban a helyi tárolóban hozzáadott új elemek jelennek meg, és a rendszer szinkronizálja őket a Mobile apps-háttérrel, ha a leküldéses szolgáltatás a következő csatlakoztatott állapotban fut.

1. Szerkessze a QSToDoService.cs a megosztott projektben. Módosítsa úgy a **applicationURL** , hogy az érvénytelen URL-címre mutasson:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Az offline viselkedést is bemutathatja, ha letiltja a WiFi és a mobil hálózatok használatát az eszközön, vagy a repülőgép üzemmódot használja.
2. Hozza létre és futtassa az alkalmazást. Figyelje meg, hogy a szinkronizálás sikertelen volt a frissítéskor, amikor az alkalmazás elindul.
3. Adja meg az új elemeket, és figyelje meg, hogy a leküldés meghiúsul a [CancelledByNetworkError] állapottal, valahányszor a **Mentés**gombra kattint. Az új teendők azonban a helyi tárolóban léteznek, amíg a Mobile apps-háttérbe nem tudnak leküldeni őket.  Éles alkalmazásban, ha letiltja ezeket a kivételeket, az ügyfélalkalmazás úgy viselkedik, mintha továbbra is csatlakoztatva legyen a Mobile apps-háttérhöz.
4. Az alkalmazás bezárásával ellenőrizze, hogy a létrehozott új elemek megmaradnak-e a helyi tárolóban.
5. Választható Ha a Visual Studio telepítve van a számítógépen, nyissa meg a **Server Explorert**. Navigáljon az adatbázishoz az **Azure**-> **SQL-adatbázisokban**. Kattintson a jobb gombbal az adatbázisra, és válassza **a Megnyitás a SQL Server Object Explorerban**lehetőséget. Most megkeresheti az SQL Database-táblázatot és annak tartalmát. Győződjön meg arról, hogy a háttér-adatbázisban lévő összes érték nem módosult.
6. Választható Használjon REST-eszközt, mint például a Hegedűs vagy a Poster a mobil háttér lekérdezéséhez a GET lekérdezés használatával `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Az alkalmazás frissítése a Mobile apps-háttér újrakapcsolódásához
Ebben a szakaszban újra csatlakozik az alkalmazáshoz a Mobile apps-háttérrel. Ez szimulálja az alkalmazást offline állapotról online állapotra a Mobile App backend használatával.   Ha a hálózati kapcsolat kikapcsolásával szimulálta a hálózat törését, nincs szükség kód módosítására.
Kapcsolja be újra a hálózatot.  Az alkalmazás első futtatásakor a rendszer a `RefreshDataAsync` metódust hívja meg. Ezzel meghívja a `SyncAsync`t, hogy szinkronizálja a helyi tárolót a háttér-adatbázissal.

1. Nyissa meg a QSToDoService.cs a megosztott projektben, és állítsa be a **applicationURL** tulajdonság változását.
2. Hozza létre újra és futtassa az alkalmazást. Az alkalmazás a `OnRefreshItemsSelected` metódus végrehajtásakor leküldéses és lekéréses műveletekkel szinkronizálja a helyi módosításokat az Azure Mobile App backend használatával.
3. Választható A frissített adatok megtekintése SQL Server Object Explorer vagy REST-eszköz, például a Hegedűs használatával. Figyelje meg, hogy az adatokat szinkronizálták az Azure Mobile App backend-adatbázisa és a helyi tároló között.
4. Az alkalmazásban kattintson a jelölőnégyzetre néhány elem mellett, hogy elvégezze azokat a helyi tárolóban.

   `CompleteItemAsync` meghívja a `SyncAsync`, hogy szinkronizálja az egyes befejezett elemeket a Mobile apps-háttérrel. a `SyncAsync` leküldéses és lekéréses hívásokat kezdeményez.
   **Minden alkalommal, amikor egy lekérést hajt végre egy olyan táblán, amelyen az ügyfél módosításokat végzett, az ügyfél-szinkronizálási környezet leküldése mindig automatikusan lefut**. Az implicit leküldés biztosítja, hogy a helyi tárolóban lévő összes tábla és a kapcsolatok konzisztensek maradjanak. További információ erről a viselkedésről: [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="review-the-client-sync-code"></a>Az ügyfél-szinkronizálási kód áttekintése
A [Xamarin iOS-alkalmazás létrehozása] letöltött Xamarin-ügyfél-projekt már tartalmaz olyan kódot, amely támogatja az offline szinkronizálást egy helyi SQLite-adatbázis használatával. Íme egy rövid áttekintés arról, hogy mi szerepel az oktatóanyag kódjában. A szolgáltatás fogalmi áttekintését lásd: [Offline adatszinkronizálás az Azure Mobile Appsban].

* A tábla műveleteinek elvégzése előtt inicializálni kell a helyi tárolót. A helyi áruház adatbázisa inicializálva lesz, amikor a `QSTodoListViewController.ViewDidLoad()` végrehajtja `QSTodoService.InitializeStoreAsync()`. Ez a módszer létrehoz egy új helyi SQLite-adatbázist az Azure Mobile App Client SDK által biztosított `MobileServiceSQLiteStore` osztály használatával.

    A `DefineTable` metódus létrehoz egy táblát a helyi tárolóban, amely megfelel a megadott típus mezőinek, `ToDoItem` ebben az esetben. A típusnak nem kell tartalmaznia a távoli adatbázisban lévő összes oszlopot. Az oszlopok csak egy részhalmazát lehet tárolni.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* A `QSTodoService` `todoTable` tagjának `IMobileServiceSyncTable` típusa `IMobileServiceTable`helyett. A IMobileServiceSyncTable az összes létrehozási, olvasási, frissítési és törlési (szifilisz) tábla műveletet a helyi tároló adatbázisára irányítja.

    Ha ezeket a módosításokat a `IMobileServiceSyncContext.PushAsync()`meghívásával elküldi az Azure Mobile apps-háttérbe. A szinkronizálási környezet segít megőrizni a táblák kapcsolatait az összes olyan tábla változásainak nyomon követésével és kitolásával, amelyet az ügyfélalkalmazás a `PushAsync` hívásakor módosít.

    A megadott kód meghívja `QSTodoService.SyncAsync()` szinkronizálni a todoitem-lista frissítésekor, vagy egy todoitem hozzáadása vagy befejezése után. Az alkalmazás minden helyi módosítás után szinkronizál. Ha a lekéréses műveletet egy olyan táblán hajtja végre, amely a kontextusban függőben lévő helyi frissítésekkel rendelkezik, akkor a lekéréses művelet automatikusan elindítja a környezet leküldését.

    A megadott kódban a távoli `TodoItem` tábla összes rekordja le van kérdezve, de a rekordokat a lekérdezés azonosítójának és lekérdezésének átadásával is szűrheti `PushAsync`. További információ: *növekményes szinkronizálás* a [Offline adatszinkronizálás az Azure Mobile Appsban].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

## <a name="additional-resources"></a>További források
* [Offline adatszinkronizálás az Azure Mobile Appsban]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- Images -->

<!-- URLs. -->
[Xamarin iOS-alkalmazás létrehozása]: app-service-mobile-xamarin-ios-get-started.md
[Offline adatszinkronizálás az Azure Mobile Appsban]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
