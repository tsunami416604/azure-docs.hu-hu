---
title: "Kapcsolat nélküli szinkronizálásának engedélyezése a Azure Mobile Apps (Xamarin iOS)"
description: "App Service Mobile Apps a Xamarin iOS-alkalmazás gyorsítótárába, a szinkronizálási kapcsolat nélküli adatainak használata"
documentationcenter: xamarin
author: ggailey777
manager: cfowler
editor: 
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: b5878d8a2e18cf08b6e9074ecf40cd732624f0c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Tegye lehetővé az offline szinkronizálást Xamarin.iOS-mobilalkalmazásában
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag a kapcsolat nélküli szinkronizálás szolgáltatást, az Azure Mobile Apps xamarin.IOS vezet be. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, amellyel kommunikálni tud a mobilalkalmazások--megtekintését, hozzáadását és módosítását adatokat –, akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban tárolódnak. Az eszköz újra online állapotba kerül, ha ezek a változások szinkronizálása megtörtént-e a távoli szolgáltatás.

Ebben az oktatóanyagban frissítse a Xamarin.iOS app projektet a [Xamarin iOS-alkalmazás létrehozása] az Azure Mobile Apps offline funkciók támogatásához. Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, hozzá kell adnia a hozzáférési adatok bővítménycsomagok a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

A kapcsolat nélküli szinkronizálás szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [az Azure Mobile Apps Offline adatszinkronizálás].

## <a name="update-the-client-app-to-support-offline-features"></a>Frissítés az ügyfélalkalmazás offline funkciók támogatásához
Az Azure Mobile Apps offline funkciók lehetővé teszik egy helyi adatbázist kommunikál, amikor egy offline forgatókönyvben. Ezeket a szolgáltatásokat az alkalmazás használatához inicializálni egy [SyncContext] a helyi tárolójába. A [IMobileServiceSyncTable] felületen a táblájára hivatkozhat. SQLite lesz a helyi tárolójába, az eszközön.

1. Nyissa meg a NuGet-Csomagkezelőt a projektben az elvégzett a [Xamarin iOS-alkalmazás létrehozása] oktatóanyag, majd keresse meg és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-csomagot.
2. Nyissa meg a QSTodoService.cs fájlt, és állítsa vissza a `#define OFFLINE_SYNC_ENABLED` definíciója.
3. Építse újra, és futtassa az ügyfél-alkalmazást. Az alkalmazás működik, mint előtti engedélyezte a kapcsolat nélküli szinkronizálás. A helyi adatbázis azonban most van feltöltve adatokkal egy kapcsolat nélküli forgatókönyvben használható.

## <a name="update-sync"></a>A háttérbeli leválasztása alkalmazás frissítése
Ebben a szakaszban megszakítja a kapcsolatot egy kapcsolat nélküli helyzet szimulálása a Mobile Apps-háttéralkalmazás számára. Adatelemek hozzáadásakor a kivételkezelő megtudhatja, hogy az alkalmazás offline módban van. Ebben az állapotban lévő új elemek a helyi tárolóban levő adva, és során lesznek szinkronizálva a mobil-háttéralkalmazás számára leküldéses következő futtatás csatlakoztatott állapotban.

1. Szerkessze a QSToDoService.cs a megosztott projektben. Módosítsa a **applicationURL** mutasson az egy URL-cím érvénytelen:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Kapcsolat nélküli viselkedés igazolni Wi-Fi és cellás hálózatok, az eszköz letiltása, vagy repülőgép üzemmódban.
2. Hozza létre, és futtassa az alkalmazást. Figyelje meg a szinkronizálás nem sikerült a frissítés, az alkalmazás indításakor.
3. Adja meg az új elemeket, és figyelje meg, hogy leküldéses sikertelen [CancelledByNetworkError] állapotú gombra kattintáskor **mentése**. Azonban az új todo elemeket szerepel a helyi tárolójába csak azokat a mobil-háttéralkalmazás továbbíthatja.  Egy éles alkalmazásban Ha Ön ne jelenjen meg többé az ilyen kivételek az ügyfélalkalmazás úgy viselkedik, mintha az továbbra is kapcsolódik a mobil-háttéralkalmazást.
4. Zárja be az alkalmazást, és indítsa újra, hogy ellenőrizze, hogy a helyi tárolójába őrződnek meg a létrehozott új elemeket.
5. (Választható) Ha a számítógépen telepített Visual Studio, nyissa meg a **Server Explorer**. Keresse meg az adatbázist a **Azure**-> **SQL-adatbázisok**. Kattintson a jobb gombbal az adatbázist, és válassza ki **nyissa meg az SQL Server Object Explorerben**. Most már megkeresheti az SQL-adatbázistáblában szereplő és annak tartalmát. Győződjön meg arról, hogy a háttér-adatbázis adatai nem módosult.
6. (Választható) Például a Fiddler vagy Postman REST eszköz segítségével lekérdezni a mobil-háttéralkalmazást, GET lekérdezéssel formájában `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>A Mobile Apps-háttéralkalmazás csatlakozzanak az alkalmazás frissítésére
Ebben a szakaszban az alkalmazásnak, hogy a mobil-háttéralkalmazás újra. Ez szimulálja az alkalmazást, az offline állapotú áthelyezését a mobil-háttéralkalmazás az online állapotban.   A hálózati törés szimulálja kikapcsolása a hálózati kapcsolatot, ha nincs kód módosítására van szükség.
Kapcsolja a hálózatra be újból.  Az alkalmazás első futtatásakor a `RefreshDataAsync` metódust. Ezután meghívja `SyncAsync` szinkronizálása a helyi tárolóhoz a háttér-adatbázissal.

1. Nyissa meg a QSToDoService.cs a projektet, és a módosítás a visszaállításához a **applicationURL** tulajdonság.
2. Építse újra, és futtassa az alkalmazást. Az alkalmazás a helyi módosítások szinkronizál az Azure Mobile Apps-háttéralkalmazás segítségével eseménylekérési és eseményküldési műveletek során a `OnRefreshItemsSelected` metódus.
3. (Választható) Az SQL Server Object Explorer vagy egy REST-eszköz, például Fiddler használatával frissített adatok megtekintéséhez. Figyelje meg az adatokat az Azure Mobile Apps-háttéralkalmazás adatbázis és a helyi tárolójába között lett szinkronizálva.
4. Az alkalmazást, és kattintson a helyi tárolóban levő befejezéséhez néhány elem melletti jelölőnégyzet.

   `CompleteItemAsync`hívások `SyncAsync` a Mobile Apps-háttéralkalmazás a szinkronizálás minden befejeződött elemhez. `SyncAsync`meghívja a lekérést és a küldést.
   **Az ügyfél által végrehajtott módosítások táblákon lekérési hajtható végre, amikor egy leküldéses ügyfél-szinkronizálás környezetében a rendszer mindig futtatja először automatikusan**. Az implicit leküldéses biztosítja a kapcsolatokat és a helyi tárolóban levő összes tábla azonban konzisztens marad. Ez a viselkedés további információkért lásd: [az Azure Mobile Apps Offline adatszinkronizálás].

## <a name="review-the-client-sync-code"></a>Tekintse át az Ügyfélkód szinkronizálása
Az oktatóanyag befejezésekor letöltött Xamarin ügyfélprojekt [Xamarin iOS-alkalmazás létrehozása] már tartalmaz egy helyi SQLite-adatbázis használata a kapcsolat nélküli szinkronizálást támogató kódot. Ez az oktatóanyag kódban már tartalmának rövid áttekintést. A szolgáltatás elméleti áttekintését lásd: [az Azure Mobile Apps Offline adatszinkronizálás].

* Mielőtt bármely tábla művelet végrehajtható, a helyi tárolójába inicializálni kell. A helyi tároló adatbázis inicializálása során `QSTodoListViewController.ViewDidLoad()` végrehajtja a `QSTodoService.InitializeStoreAsync()`. Ezzel a módszerrel hoz létre egy új helyi SQLite adatbázis használata a `MobileServiceSQLiteStore` az Azure Mobile Apps-ügyfél SDK által meghatározott osztály.

    A `DefineTable` metódus táblázatot hoz létre a helyi tárolóban, amely megfelel a megadott típus mezőinek `ToDoItem` ebben az esetben. A típus nem kell megadnia a távoli adatbázisban lévő összes oszlopot. Akkor lehet tárolására oszlopok egy részét.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* A `todoTable` tagja `QSTodoService` felel meg a `IMobileServiceSyncTable` helyett típusú `IMobileServiceTable`. A IMobileServiceSyncTable összes létrehozása, olvasása, frissítése és Törlés (CRUD) tábla műveletek a helyi tároló adatbázis irányítja.

    Úgy dönt, ha ezeket a módosításokat az Azure Mobile Apps-háttéralkalmazás meghívásával leküldve vannak `IMobileServiceSyncContext.PushAsync()`. A szinkronizálási környezetet segíti követésével és kérdez le a módosításokat minden olyan táblát, egy ügyfélalkalmazás módosította, ha a tábla közötti megőrzése érdekében `PushAsync` nevezik.

    A megadott kód hívások `QSTodoService.SyncAsync()` szinkronizálásra, amikor a todoitem listájának frissítését, vagy a todoitem hozzá, vagy befejeződött. Az alkalmazás szinkronizálások minden helyi módosítás után. Ha egy lekéréses van függőben lévő helyi frissítést nyomon követett táblákon a környezetben futtatja, lekéréses művelet automatikusan indít környezetben leküldéses először.

    A megadott kód összes rögzít a távoli `TodoItem` megkérdezi a táblában, de a rekordok szűrése úgy, hogy a lekérdezés azonosítóját, és a lekérdezési lehetőség arra is `PushAsync`. További információkért tekintse meg a szakasz *növekményes szinkronizálás* a [az Azure Mobile Apps Offline adatszinkronizálás].

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
* [az Azure Mobile Apps Offline adatszinkronizálás]
* [Az Azure Mobile Apps .NET SDK útmutató][8]

<!-- Images -->

<!-- URLs. -->
[Xamarin iOS-alkalmazás létrehozása]: app-service-mobile-xamarin-ios-get-started.md
[az Azure Mobile Apps Offline adatszinkronizálás]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
