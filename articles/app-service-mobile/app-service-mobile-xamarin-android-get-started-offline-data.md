---
title: Offline szinkronizálás engedélyezése (Xamarin Android)
description: Ismerje meg, hogyan tárolhatja és szinkronizálhatja az offline adatokat a Xamarin Android alkalmazásban.
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4faab572cd77e20bb61d147ca6e58b25cd88ad9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458868"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Offline szinkronizálás engedélyezése a Xamarin.Android mobilalkalmazásban

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja a Xamarin.Android-alapú Azure Mobile Apps offline szinkronizálási funkcióját. Az offline szinkronizálás lehetővé teszi a végfelhasználók számára, hogy mobilalkalmazással kommunikáljanak – adatok megtekintése, hozzáadása vagy módosítása – még akkor is, ha nincs hálózati kapcsolat. A módosításokat a helyi adatbázis tárolja.
Ha az eszköz újra online állapotba kerül, ezek a módosítások szinkronizálódnak a távoli szolgáltatással.

Ebben az oktatóanyagban frissíti az ügyfélprojektet az oktatóanyagból [Xamarin Android-alkalmazás létrehozása] az Azure Mobile Apps offline funkcióinak támogatásához. Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, hozzá kell adnia az adatelérési bővítménycsomagokat a projekthez. A kiszolgálóbővítmény-csomagokról további információt [a .NET háttérkiszolgáló SDK azure mobile alkalmazásokhoz szolgáltatása című témakörben talál.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

Az offline szinkronizálási funkcióról az Offline data Sync in Azure Mobile Apps című témakörben olvashat [bővebben.]

## <a name="update-the-client-app-to-support-offline-features"></a>Az ügyfélalkalmazás frissítése az offline funkciók támogatásához

Az Azure Mobile App offline funkciói lehetővé teszik, hogy egy helyi adatbázissal kommunikáljon, amikor offline módban van. Ha használni szeretné ezeket a funkciókat az alkalmazásban, inicializálja a [SyncContext-t] egy helyi tárolóba. Ezután hivatkozzon a táblázatra az [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) felületen keresztül. Az SQLite a készülék helyi tárolója.

1. A Visual Studio-ban nyissa meg a NuGet csomagkezelőt a [Xamarin Android létrehozása alkalmazás] oktatóanyagában befejezett projektben.  Keresse meg és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet csomagot.
2. Nyissa meg a ToDoActivity.cs `#define OFFLINE_SYNC_ENABLED` fájlt, és ne fűzzön megjegyzést a definícióhoz.
3. A Visual Studio-ban nyomja le az **F5** billentyűt az ügyfélalkalmazás újraépítéséhez és futtatásához. Az alkalmazás ugyanúgy működik, mint az offline szinkronizálás engedélyezése előtt. A helyi adatbázis azonban most már feltöltve van az offline forgatókönyvben használható adatokkal.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Az alkalmazás frissítése a háttérrendszerről való leválasztáshoz

Ebben a szakaszban megszakítja a kapcsolatot a mobilalkalmazás-háttérrendszerével, hogy offline helyzetet szimuláljon. Adatelemek hozzáadásakor a kivételkezelő jelzi, hogy az alkalmazás offline módban van. Ebben az állapotban a helyi tárolóban hozzáadott új elemek, és a mobilalkalmazás-háttérkapcsolati kapcsolati kapcsolatesetén szinkronizálódnak.

1. A megosztott projektben ToDoActivity.cs szerkesztése. Módosítsa az **applicationURL-t** úgy, hogy érvénytelen URL-címre mutasson:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Az offline viselkedést a wifi és a mobilhálózatok letiltásával vagy repülőgép üzemmód használatával is bemutathatja.
2. Az alkalmazás létrehozásához és futtatásához nyomja le az **F5** billentyűt. Figyelje meg, hogy a szinkronizálás nem sikerült a frissítés, amikor az alkalmazás elindult.
3. Adjon meg új elemeket, és figyelje meg, hogy a leküldéses sikertelen [CanceldByNetworkError] állapottal minden alkalommal, amikor a **Mentés**gombra kattint. Az új teendők azonban a helyi tárolóban léteznek, amíg le nem lökhetők a mobilalkalmazás háttértartalékába.  Éles alkalmazásban, ha letiltja ezeket a kivételeket, az ügyfélalkalmazás úgy viselkedik, mintha még mindig a mobilalkalmazás-háttérrendszerhez csatlakozna.
4. Zárja be az alkalmazást, és indítsa újra, és ellenőrizze, hogy az új onnan létrehozott elemek megmaradnak-e a helyi tárolóban.
5. (Nem kötelező) A Visual Studio programban nyissa meg a **Server Explorer t**. Nyissa meg az adatbázist az **Azure**->**SQL-adatbázisokban.** Kattintson a jobb gombbal az adatbázisra, és válassza **a Megnyitás az SQL Server Object Explorer programban parancsot.** Most már tallózhat az SQL adatbázistábla és annak tartalmát. Ellenőrizze, hogy a háttéradatbázis ban lévő adatok nem változtak-e.
6. (Nem kötelező) Használjon REST eszközt, például a Hegedűs t vagy a Postman t `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`a mobil háttérrendszer lekérdezéséhez, az űrlapget lekérdezésével.

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Az alkalmazás frissítése a mobilalkalmazás-háttérrendszer újracsatlakoztatásához

Ebben a szakaszban csatlakoztassa újra az alkalmazást a mobilalkalmazás-háttér-háttér-. Az alkalmazás első futtatásakor az eseménykezelő meghívja a `OnCreate` . `OnRefreshItemsSelected` Ez a `SyncAsync` módszer a helyi tároló és a háttéradatbázis szinkronizálását kéri.

1. Nyissa meg ToDoActivity.cs a megosztott projektben, és változtassa vissza az **applicationURL** tulajdonság módosítását.
2. Nyomja le az **F5** billentyűt az alkalmazás újraépítéséhez és futtatásához. Az alkalmazás szinkronizálja a helyi módosításokat az Azure Mobile App `OnRefreshItemsSelected` háttér-leküldéses és lekéréses műveletek használatával, amikor a metódus végrehajtása.
3. (Nem kötelező) Tekintse meg a frissített adatokat az SQL Server Object Explorer vagy a REST eszköz, például a Fiddler segítségével. Figyelje meg, hogy az adatok szinkronizálva vannak az Azure Mobile App háttéradatbázisa és a helyi tároló között.
4. Az alkalmazásban kattintson néhány elem melletti jelölőnégyzetre, hogy kitöltse azokat a helyi áruházban.

   `CheckItem`az `SyncAsync` egyes befejezett elemek szinkronizálásához a mobilalkalmazás-háttérközponttal. `SyncAsync`a push és a pull hívásokat is meghívja. **Amikor lekéréset hajt végre egy olyan táblával szemben, amelyen az ügyfél módosításokat végzett, a leküldéses parancs mindig automatikusan végrehajtásra kerül.** Ez biztosítja, hogy a helyi tároló összes táblája a kapcsolatokkal együtt konzisztens maradjon. Ez a viselkedés váratlan leküldéset eredményezhet. A viselkedésről további információt az Offline adatszinkronizálás az Azure Mobile Apps alkalmazásban című [témakörben talál.]

## <a name="review-the-client-sync-code"></a>Az ügyfél szinkronizálási kódjának áttekintése

A Xamarin kliens projekt, amit letöltött, amikor befejezte a bemutató [Létrehozása Xamarin Android app] már tartalmaz kódot támogató offline szinkronizálás egy helyi SQLite adatbázis. Itt van egy rövid áttekintést, hogy mi már szerepel a bemutató kódot. A szolgáltatás fogalmi áttekintését az Offline adatok szinkronizálása az Azure Mobile Apps alkalmazásban című [témakörben találja.]

* Mielőtt bármilyen táblaműveletet végrehajtana, a helyi tárolót inicializálva kell végezni. A helyi tároló adatbázisa `ToDoActivity.OnCreate()` a `ToDoActivity.InitLocalStoreAsync()`végrehajtáskor inicializálódik. Ez a módszer egy helyi SQLite-adatbázist hoz létre az `MobileServiceSQLiteStore` Azure Mobile Apps ügyfél SDK által biztosított osztály használatával.

    A `DefineTable` metódus létrehoz egy táblát a helyi tárolóban, `ToDoItem` amely megfelel a megadott típusú mezőknek, ebben az esetben. A típusnak nem kell tartalmaznia a távoli adatbázisban lévő összes oszlopot. Lehetőség van az oszlopok egy részhalmazának tárolására.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see https://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* A `toDoTable` tag `ToDoActivity` a `IMobileServiceSyncTable` helyett a `IMobileServiceTable`típusú. Az IMobileServiceSyncTable az összes létrehozási, olvasási, frissítési és törlési (CRUD) táblaműveletet a helyi tároló adatbázisába irányítja.

    A hívással eldöntheti, hogy mikor kerül `IMobileServiceSyncContext.PushAsync()`leküldése siettetett az Azure Mobile App-háttérszolgáltatásra. A szinkronizálási környezet segít megőrizni a táblakapcsolatokat azáltal, hogy `PushAsync` nyomon követi és lenyomja az ügyfélalkalmazás által módosított összes tábla módosításait, amikor meghívják őket.

    A megadott `ToDoActivity.SyncAsync()` kód szinkronizálni, amikor a todoitem lista frissül, vagy egy todoitem hozzáadása vagy befejezése. A kód minden helyi módosítás után szinkronizálódik.

    A megadott kódban a távoli `TodoItem` tábla összes rekordja lekérdezésre kerül, de a rekordok szűrése is lehetséges a lekérdezésazonosító és a lekérdezés átadásával a rendszer beadásával. `PushAsync` További információt az Azure Mobile Apps *növekményes szinkronizálása* [az offline adatszinkronizálásban című szakaszban talál.]

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>További források

* [Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- URLs. -->
[Xamarin Android-alkalmazás létrehozása]: ./app-service-mobile-xamarin-android-get-started.md
[Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Xamarin Android-alkalmazás létrehozása]: app-service-mobile-xamarin-android-get-started.md
[Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext környezet]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
