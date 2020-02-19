---
title: Offline szinkronizálás engedélyezése (Xamarin Android)
description: Megtudhatja, hogyan használhatja a App Service Mobile apps-t a Xamarin Android-alkalmazásban tárolt offline állapotok gyorsítótárazásához és szinkronizálásához.
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 4faab572cd77e20bb61d147ca6e58b25cd88ad9f
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77458868"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>A Xamarin. Android Mobile alkalmazás offline szinkronizálásának engedélyezése

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja az Azure Mobile Apps offline szinkronizálási funkcióját a Xamarin. Android rendszerhez. Az offline szinkronizálás lehetővé teszi, hogy a végfelhasználók a mobil alkalmazásokkal is használhassák az adatmegjelenítést, hozzáadást vagy módosítást, még akkor is, ha nincs hálózati kapcsolat. A módosításokat a rendszer egy helyi adatbázisban tárolja.
Miután az eszköz ismét online állapotba került, a módosítások szinkronizálva lesznek a távoli szolgáltatással.

Ebben az oktatóanyagban az Azure Mobile Apps offline funkcióinak támogatásához a [Xamarin Android-alkalmazás létrehozása] című oktatóanyagból frissíti az ügyfél projektjét. Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, hozzá kell adnia az adatelérési bővítmények csomagjait a projekthez. További információ a kiszolgálói bővítmények csomagjairól: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Az offline szinkronizálási szolgáltatással kapcsolatos további tudnivalókért tekintse meg az [Offline adatszinkronizálás az Azure Mobile Appsban]című témakört.

## <a name="update-the-client-app-to-support-offline-features"></a>Az ügyfélalkalmazás frissítése az offline funkciók támogatására

Az Azure Mobile App offline funkciói lehetővé teszik a helyi adatbázissal való interakciót, ha offline környezetben van. Ha ezeket a funkciókat az alkalmazásban szeretné használni, a [SyncContext] egy helyi tárolóba kell inicializálnia. Ezután a [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) felületen keresztül hivatkozhat a táblára. Az SQLite a helyi tárolóként van használatban az eszközön.

1. A Visual Studióban nyissa meg a NuGet csomagkezelő eszközt a projektben, amelyet a [Xamarin Android-alkalmazás létrehozása] oktatóanyagban végzett.  Keresse meg és telepítse a **Microsoft. Azure. Mobile. Client. SQLiteStore** NuGet csomagot.
2. Nyissa meg a ToDoActivity.cs fájlt, és tegye meg a `#define OFFLINE_SYNC_ENABLED` definíciójának megjegyzését.
3. A Visual Studióban nyomja le az **F5** billentyűt az újraépítéshez és az ügyfélalkalmazás futtatásához. Az alkalmazás ugyanúgy működik, mint az offline szinkronizálás engedélyezése előtt. A helyi adatbázis azonban már fel van töltve olyan adatokkal, amelyek offline környezetben használhatók.

## <a name="update-sync"></a>Az alkalmazás frissítése a háttérből való leválasztáshoz

Ebben a szakaszban megszakítja a kapcsolatát a Mobile apps-háttérrel az offline állapot szimulálása érdekében. Adatelemek hozzáadásakor a kivétel kezelője jelzi, hogy az alkalmazás kapcsolat nélküli módban van. Ebben az állapotban a helyi tárolóban hozzáadott új elemek jelennek meg, és a rendszer szinkronizálja őket a Mobile apps-háttérrel, ha egy leküldéses állapotot csatlakoztatott állapotban hajtanak végre.

1. Szerkessze a ToDoActivity.cs a megosztott projektben. Módosítsa úgy a **applicationURL** , hogy az érvénytelen URL-címre mutasson:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Az offline viselkedést is bemutathatja, ha letiltja a WiFi és a mobil hálózatok használatát az eszközön, vagy a repülőgép üzemmódot használja.
2. Az alkalmazás létrehozásához és futtatásához nyomja le az **F5** billentyűt. Figyelje meg, hogy a szinkronizálás sikertelen volt a frissítéskor, amikor az alkalmazás elindul.
3. Adja meg az új elemeket, és figyelje meg, hogy a leküldés meghiúsul a [CancelledByNetworkError] állapottal, valahányszor a **Mentés**gombra kattint. Az új teendők azonban a helyi tárolóban léteznek, amíg a Mobile apps-háttérbe nem tudnak leküldeni őket.  Éles alkalmazásban, ha letiltja ezeket a kivételeket, az ügyfélalkalmazás úgy viselkedik, mintha továbbra is csatlakoztatva legyen a Mobile apps-háttérhöz.
4. Az alkalmazás bezárásával ellenőrizze, hogy a létrehozott új elemek megmaradnak-e a helyi tárolóban.
5. Választható A Visual Studióban nyissa meg a **Server Explorert**. Navigáljon az adatbázishoz az **Azure**->**SQL-adatbázisokban**. Kattintson a jobb gombbal az adatbázisra, és válassza **a Megnyitás a SQL Server Object Explorerban**lehetőséget. Most megkeresheti az SQL Database-táblázatot és annak tartalmát. Győződjön meg arról, hogy a háttér-adatbázisban lévő összes érték nem módosult.
6. Választható Használjon REST-eszközt, mint például a Hegedűs vagy a Poster a mobil háttér lekérdezéséhez a GET lekérdezés használatával `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Az alkalmazás frissítése a Mobile apps-háttér újrakapcsolódásához

Ebben a szakaszban újra csatlakozik az alkalmazáshoz a Mobile apps-háttérrel. Az alkalmazás első futtatásakor a `OnCreate` eseménykezelő meghívja a `OnRefreshItemsSelected`. Ez a metódus meghívja a `SyncAsync`t, hogy szinkronizálja a helyi tárolót a háttér-adatbázissal.

1. Nyissa meg a ToDoActivity.cs a megosztott projektben, és állítsa be a **applicationURL** tulajdonság változását.
2. Az alkalmazás újraépítéséhez és futtatásához nyomja le az **F5** billentyűt. Az alkalmazás a `OnRefreshItemsSelected` metódus végrehajtásakor leküldéses és lekéréses műveletekkel szinkronizálja a helyi módosításokat az Azure Mobile App backend használatával.
3. Választható A frissített adatok megtekintése SQL Server Object Explorer vagy REST-eszköz, például a Hegedűs használatával. Figyelje meg, hogy az adatokat szinkronizálták az Azure Mobile App backend-adatbázisa és a helyi tároló között.
4. Az alkalmazásban kattintson a jelölőnégyzetre néhány elem mellett, hogy elvégezze azokat a helyi tárolóban.

   `CheckItem` meghívja a `SyncAsync`, hogy szinkronizálja az egyes befejezett elemeket a Mobile apps-háttérrel. a `SyncAsync` leküldéses és lekéréses hívásokat kezdeményez. Amikor lekéri a **lekérést egy olyan táblára, amelyen az ügyfél módosításokat hajtott végre, a leküldéses szolgáltatás mindig automatikusan lefut**. Ez biztosítja, hogy a helyi tárolóban lévő összes tábla és a kapcsolatok konzisztensek maradjanak. Ez a viselkedés nem várt leküldést eredményezhet. További információ erről a viselkedésről: [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="review-the-client-sync-code"></a>Az ügyfél-szinkronizálási kód áttekintése

A [Xamarin Android-alkalmazás létrehozása] letöltött Xamarin-ügyfél-projekt már tartalmaz olyan kódot, amely támogatja az offline szinkronizálást egy helyi SQLite-adatbázis használatával. Íme egy rövid áttekintés arról, hogy mi szerepel az oktatóanyag kódjában. A szolgáltatás fogalmi áttekintését lásd: [Offline adatszinkronizálás az Azure Mobile Appsban].

* A tábla műveleteinek elvégzése előtt inicializálni kell a helyi tárolót. A helyi áruház adatbázisa inicializálva lesz, amikor a `ToDoActivity.OnCreate()` végrehajtja `ToDoActivity.InitLocalStoreAsync()`. Ez a módszer egy helyi SQLite-adatbázist hoz létre az Azure Mobile Apps Client SDK által biztosított `MobileServiceSQLiteStore` osztály használatával.

    A `DefineTable` metódus létrehoz egy táblát a helyi tárolóban, amely megfelel a megadott típus mezőinek, `ToDoItem` ebben az esetben. A típusnak nem kell tartalmaznia a távoli adatbázisban lévő összes oszlopot. Az oszlopok csak egy részhalmazát lehet tárolni.

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
* A `ToDoActivity` `toDoTable` tagjának `IMobileServiceSyncTable` típusa `IMobileServiceTable`helyett. A IMobileServiceSyncTable az összes létrehozási, olvasási, frissítési és törlési (szifilisz) tábla műveletet a helyi tároló adatbázisára irányítja.

    Ha úgy dönt, hogy a módosításokat az Azure Mobile apps-háttérbe küldi, a `IMobileServiceSyncContext.PushAsync()`meghívásával. A szinkronizálási környezet segít megőrizni a táblák kapcsolatait az összes olyan tábla változásainak nyomon követésével és kitolásával, amelyet az ügyfélalkalmazás a `PushAsync` hívásakor módosít.

    A megadott kód meghívja `ToDoActivity.SyncAsync()` szinkronizálni a todoitem-lista frissítésekor, vagy egy todoitem hozzáadása vagy befejezése után. A kód minden helyi módosítás után szinkronizál.

    A megadott kódban a távoli `TodoItem` tábla összes rekordja le van kérdezve, de a rekordokat a lekérdezés azonosítójának és lekérdezésének átadásával is szűrheti `PushAsync`. További információ: *növekményes szinkronizálás* a [Offline adatszinkronizálás az Azure Mobile Appsban].

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

* [Offline adatszinkronizálás az Azure Mobile Appsban]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- URLs. -->
[Xamarin Android-alkalmazás létrehozása]: ./app-service-mobile-xamarin-android-get-started.md
[Offline adatszinkronizálás az Azure Mobile Appsban]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Xamarin Android-alkalmazás létrehozása]: app-service-mobile-xamarin-android-get-started.md
[Offline adatszinkronizálás az Azure Mobile Appsban]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
