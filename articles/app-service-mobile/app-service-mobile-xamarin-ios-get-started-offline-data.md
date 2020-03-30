---
title: Offline szinkronizálás engedélyezése (Xamarin iOS)
description: Ismerje meg, hogyan tárolhatja és szinkronizálhatja az offline adatokat a Xamarin iOS alkalmazásban.
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3a5128f6918b22be2ff1ef6adf3e453b1f373ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461300"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Offline szinkronizálás engedélyezése a Xamarin.iOS mobilalkalmazásban
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja a Xamarin.iOS-hez készült Azure Mobile Apps offline szinkronizálási funkcióját. Az offline szinkronizálás lehetővé teszi a végfelhasználók számára, hogy mobilalkalmazással kommunikáljanak – adatok megtekintése, hozzáadása vagy módosítása – még akkor is, ha nincs hálózati kapcsolat. A módosításokat a helyi adatbázis tárolja. Ha az eszköz újra online állapotba kerül, ezek a módosítások szinkronizálódnak a távoli szolgáltatással.

Ebben az oktatóanyagban frissítse a Xamarin.iOS alkalmazásprojektet [a Xamarin iOS-alkalmazás létrehozása] szolgáltatásból az Azure Mobile Apps offline funkcióinak támogatásához. Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, hozzá kell adnia az adatelérési bővítménycsomagokat a projekthez. A kiszolgálóbővítmény-csomagokról további információt [a .NET háttérkiszolgáló SDK azure mobile alkalmazásokhoz szolgáltatása című témakörben talál.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

Az offline szinkronizálási funkcióról az Offline data Sync in Azure Mobile Apps című témakörben olvashat [bővebben.]

## <a name="update-the-client-app-to-support-offline-features"></a>Az ügyfélalkalmazás frissítése az offline funkciók támogatásához
Az Azure Mobile App offline funkciói lehetővé teszik, hogy egy helyi adatbázissal kommunikáljon, amikor offline módban van. Ha használni szeretné ezeket a funkciókat az alkalmazásban, inicializálja a [SyncContext-t] egy helyi tárolóba. Hivatkozzon a táblára az [IMobileServiceSyncTable] felületen keresztül. Az SQLite a készülék helyi tárolója.

1. Nyissa meg a NuGet csomagkezelőt a [Xamarin iOS létrehozása alkalmazás] oktatóanyagában, majd keresse meg és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet csomagot.
2. Nyissa meg a QSTodoService.cs `#define OFFLINE_SYNC_ENABLED` fájlt, és ne fűzzön megjegyzést a definícióhoz.
3. Építse újra és futtassa az ügyfélalkalmazást. Az alkalmazás ugyanúgy működik, mint az offline szinkronizálás engedélyezése előtt. A helyi adatbázis azonban most már feltöltve van az offline forgatókönyvben használható adatokkal.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Az alkalmazás frissítése a háttérrendszerről való leválasztáshoz
Ebben a szakaszban megszakítja a kapcsolatot a mobilalkalmazás-háttérrendszerével, hogy offline helyzetet szimuláljon. Adatelemek hozzáadásakor a kivételkezelő jelzi, hogy az alkalmazás offline módban van. Ebben az állapotban a helyi tárolóban hozzáadott új elemek et szinkronizálja a mobilalkalmazás háttér-tartalékszámítógépe, amikor a leküldéses következő futtatás a csatlakoztatott állapotban lesz.

1. A megosztott projektben QSToDoService.cs szerkesztése. Módosítsa az **applicationURL-t** úgy, hogy érvénytelen URL-címre mutasson:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Az offline viselkedést a wifi és a mobilhálózatok letiltásával vagy repülőgép üzemmód használatával is bemutathatja.
2. Hozza létre és futtassa az alkalmazást. Figyelje meg, hogy a szinkronizálás nem sikerült a frissítés, amikor az alkalmazás elindult.
3. Adjon meg új elemeket, és figyelje meg, hogy a leküldéses sikertelen [CanceldByNetworkError] állapottal minden alkalommal, amikor a **Mentés**gombra kattint. Az új teendők azonban a helyi tárolóban léteznek, amíg le nem lökhetők a mobilalkalmazás háttértartalékába.  Éles alkalmazásban, ha letiltja ezeket a kivételeket, az ügyfélalkalmazás úgy viselkedik, mintha még mindig a mobilalkalmazás-háttérrendszerhez csatlakozna.
4. Zárja be az alkalmazást, és indítsa újra, és ellenőrizze, hogy az új onnan létrehozott elemek megmaradnak-e a helyi tárolóban.
5. (Nem kötelező) Ha a Visual Studio telepítve van a számítógépen, nyissa meg a **Server Explorer programot.** Nyissa meg az adatbázist az **Azure**-> **SQL-adatbázisokban.** Kattintson a jobb gombbal az adatbázisra, és válassza **a Megnyitás az SQL Server Object Explorer programban parancsot.** Most már tallózhat az SQL adatbázistábla és annak tartalmát. Ellenőrizze, hogy a háttéradatbázis ban lévő adatok nem változtak-e.
6. (Nem kötelező) Használjon REST eszközt, például a Hegedűs t vagy a Postman t `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`a mobil háttérrendszer lekérdezéséhez, az űrlapget lekérdezésével.

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Az alkalmazás frissítése a mobilalkalmazás-háttérrendszer újracsatlakoztatásához
Ebben a szakaszban csatlakoztassa újra az alkalmazást a mobilalkalmazás-háttér-háttér-. Ez szimulálja, hogy az alkalmazás offline állapotból online állapotba lép a mobilalkalmazás-háttérrendszerrel.   Ha a hálózati kapcsolat kikapcsolásával szimulálta a hálózati törést, nincs szükség kódmódosításra.
Kapcsolja be újra a hálózatot.  Az alkalmazás első futtatásakor a `RefreshDataAsync` metódus neve meg van hívva. Ez viszont `SyncAsync` felhívja a helyi tároló szinkronizálását a háttéradatbázissal.

1. Nyissa meg a QSToDoService.cs a megosztott projektben, és változtassa vissza az **applicationURL** tulajdonság módosítását.
2. Építse újra és futtassa az alkalmazást. Az alkalmazás szinkronizálja a helyi módosításokat az Azure Mobile App `OnRefreshItemsSelected` háttér-leküldéses és lekéréses műveletek használatával, amikor a metódus végrehajtása.
3. (Nem kötelező) Tekintse meg a frissített adatokat az SQL Server Object Explorer vagy a REST eszköz, például a Fiddler segítségével. Figyelje meg, hogy az adatok szinkronizálva vannak az Azure Mobile App háttéradatbázisa és a helyi tároló között.
4. Az alkalmazásban kattintson néhány elem melletti jelölőnégyzetre, hogy kitöltse azokat a helyi áruházban.

   `CompleteItemAsync`az `SyncAsync` egyes befejezett elemek szinkronizálásához a mobilalkalmazás-háttérközponttal. `SyncAsync`a push és a pull hívásokat is meghívja.
   **Amikor lekéréset hajt végre egy olyan táblával szemben, amelyen az ügyfél módosításokat végzett, az ügyfél szinkronizálási környezetének leküldése sokkal automatikusan történik.** Az implicit leküldéses biztosítja, hogy a helyi tároló összes táblája a kapcsolatokkal együtt konzisztens maradjon. A viselkedésről további információt az Offline adatszinkronizálás az Azure Mobile Apps alkalmazásban című [témakörben talál.]

## <a name="review-the-client-sync-code"></a>Az ügyfél szinkronizálási kódjának áttekintése
A Xamarin kliens projekt, amit letöltött, amikor befejezte a bemutató [Létrehozása Xamarin iOS app] már tartalmaz kódot támogató offline szinkronizálás egy helyi SQLite adatbázis. Itt van egy rövid áttekintést, hogy mi már szerepel a bemutató kódot. A szolgáltatás fogalmi áttekintését az Offline adatok szinkronizálása az Azure Mobile Apps alkalmazásban című [témakörben találja.]

* Mielőtt bármilyen táblaműveletet végrehajtana, a helyi tárolót inicializálva kell végezni. A helyi tároló adatbázisa `QSTodoListViewController.ViewDidLoad()` a `QSTodoService.InitializeStoreAsync()`végrehajtáskor inicializálódik. Ez a módszer egy új helyi `MobileServiceSQLiteStore` SQLite-adatbázist hoz létre az Azure Mobile App-ügyfél SDK által biztosított osztály használatával.

    A `DefineTable` metódus létrehoz egy táblát a helyi tárolóban, `ToDoItem` amely megfelel a megadott típusú mezőknek, ebben az esetben. A típusnak nem kell tartalmaznia a távoli adatbázisban lévő összes oszlopot. Lehetőség van az oszlopok egy részhalmazának tárolására.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* A `todoTable` tag `QSTodoService` a `IMobileServiceSyncTable` helyett a `IMobileServiceTable`típusú. Az IMobileServiceSyncTable az összes létrehozási, olvasási, frissítési és törlési (CRUD) táblaműveletet a helyi tároló adatbázisába irányítja.

    A hívással dönti el, hogy mikor kerül `IMobileServiceSyncContext.PushAsync()`a módosítások az Azure Mobile App háttérszolgáltatásba való leküldéses. A szinkronizálási környezet segít megőrizni a táblakapcsolatokat azáltal, hogy `PushAsync` nyomon követi és lenyomja az ügyfélalkalmazás által módosított összes tábla módosításait, amikor meghívják őket.

    A megadott `QSTodoService.SyncAsync()` kód szinkronizálni, amikor a todoitem lista frissül, vagy egy todoitem hozzáadása vagy befejezése. Az alkalmazás minden helyi módosítás után szinkronizál. Ha egy lekéréses végrehajtása egy olyan táblához, amely a környezet által nyomon követett függőben lévő helyi frissítéseket hajt végre, akkor a lekéréses művelet automatikusan először környezeti leküldéses műveletet indít el.

    A megadott kódban a távoli `TodoItem` tábla összes rekordja lekérdezésre kerül, de a rekordok szűrése is lehetséges a lekérdezésazonosító és a lekérdezés átadásával a rendszer beadásával. `PushAsync` További információt az Azure Mobile Apps *növekményes szinkronizálása* [az offline adatszinkronizálásban című szakaszban talál.]

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
* [Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- Images -->

<!-- URLs. -->
[Xamarin iOS-alkalmazás létrehozása]: app-service-mobile-xamarin-ios-get-started.md
[Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]: app-service-mobile-offline-data-sync.md
[SyncContext környezet]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
