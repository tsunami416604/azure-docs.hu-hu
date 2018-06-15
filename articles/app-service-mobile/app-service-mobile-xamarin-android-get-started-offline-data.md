---
title: Kapcsolat nélküli szinkronizálásának engedélyezése a Azure Mobile Apps (Xamarin Android)
description: App Service Mobile Apps a Xamarin Android-alkalmazás gyorsítótárába, a szinkronizálási kapcsolat nélküli adatainak használata
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 5c6ff5ac909e2dc6918f85d39beb781952ee6dd0
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
ms.locfileid: "27593096"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>A mobil Xamarin.Android-alkalmazás kapcsolat nélküli szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag a kapcsolat nélküli szinkronizálás szolgáltatást, az Azure Mobile Apps Xamarin.Android a vezet be. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, amellyel kommunikálni tud a mobilalkalmazások--megtekintését, hozzáadását és módosítását adatokat –, akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban tárolódnak.
Az eszköz újra online állapotba kerül, ha ezek a változások szinkronizálása megtörtént-e a távoli szolgáltatás.

Ebben az oktatóanyagban az ügyfélprojekt az oktatóanyagot frissítése [Xamarin Android-alkalmazás létrehozása] az Azure Mobile Apps offline funkciók támogatásához. Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, hozzá kell adnia a hozzáférési adatok bővítménycsomagok a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

A kapcsolat nélküli szinkronizálás szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [az Azure Mobile Apps Offline adatszinkronizálás].

## <a name="update-the-client-app-to-support-offline-features"></a>Frissítés az ügyfélalkalmazás offline funkciók támogatásához
Az Azure Mobile Apps offline funkciók lehetővé teszik egy helyi adatbázist kommunikál, amikor egy offline forgatókönyvben. Ezeket a szolgáltatásokat az alkalmazás használatához inicializálni egy [SyncContext] a helyi tárolójába. A tábla a [IMobileServiceSyncTable] [IMobileServiceSyncTable] felületen majd hivatkoznia. SQLite lesz a helyi tárolójába, az eszközön.

1. A Visual Studio, nyissa meg a projekt, amelyek a NuGet-Csomagkezelőt a [Xamarin Android-alkalmazás létrehozása] oktatóanyag.  Keresse meg és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-csomagot.
2. Nyissa meg a ToDoActivity.cs fájlt, és állítsa vissza a `#define OFFLINE_SYNC_ENABLED` definíciója.
3. A Visual Studio, nyomja le az **F5** kulcs újraépítéséhez és az ügyfélalkalmazás futtatása. Az alkalmazás működik, mint előtti engedélyezte a kapcsolat nélküli szinkronizálás. A helyi adatbázis azonban most van feltöltve adatokkal egy kapcsolat nélküli forgatókönyvben használható.

## <a name="update-sync"></a>A háttérbeli leválasztása alkalmazás frissítése
Ebben a szakaszban megszakítja a kapcsolatot egy kapcsolat nélküli helyzet szimulálása a Mobile Apps-háttéralkalmazás számára. Adatelemek hozzáadásakor a kivételkezelő megtudhatja, hogy az alkalmazás offline módban van. Ebben az állapotban lévő új elemek hozzáadva a helyi tárolóban levő, és a mobil-háttéralkalmazás a leküldéses csatlakoztatott állapotban végrehajtásakor szinkronizálva.

1. Szerkessze a ToDoActivity.cs a megosztott projektben. Módosítsa a **applicationURL** mutasson az egy URL-cím érvénytelen:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Kapcsolat nélküli viselkedés igazolni Wi-Fi és cellás hálózatok, az eszköz letiltása, vagy repülőgép üzemmódban.
2. Nyomja le az **F5** össze és futtathatja az alkalmazást. Figyelje meg a szinkronizálás nem sikerült a frissítés, az alkalmazás indításakor.
3. Adja meg az új elemeket, és figyelje meg, hogy leküldéses sikertelen [CancelledByNetworkError] állapotú gombra kattintáskor **mentése**. Azonban az új todo elemeket szerepel a helyi tárolójába csak azokat a mobil-háttéralkalmazás továbbíthatja.  Egy éles alkalmazásban Ha Ön ne jelenjen meg többé az ilyen kivételek az ügyfélalkalmazás úgy viselkedik, mintha az továbbra is kapcsolódik a mobil-háttéralkalmazást.
4. Zárja be az alkalmazást, és indítsa újra, hogy ellenőrizze, hogy a helyi tárolójába őrződnek meg a létrehozott új elemeket.
5. (Választható) A Visual Studióban nyissa meg a **Server Explorer**. Keresse meg az adatbázist a **Azure**->**SQL-adatbázisok**. Kattintson a jobb gombbal az adatbázist, és válassza ki **nyissa meg az SQL Server Object Explorerben**. Most már megkeresheti az SQL-adatbázistáblában szereplő és annak tartalmát. Győződjön meg arról, hogy a háttér-adatbázis adatai nem módosult.
6. (Választható) Például a Fiddler vagy Postman REST eszköz segítségével lekérdezni a mobil-háttéralkalmazást, GET lekérdezéssel formájában `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>A Mobile Apps-háttéralkalmazás csatlakozzanak az alkalmazás frissítésére
Ebben a szakaszban az alkalmazásnak, hogy a mobil-háttéralkalmazás újra. Az alkalmazás első futtatásakor a `OnCreate` eseménykezelő-hívások `OnRefreshItemsSelected`. Ez a metódus meghívja `SyncAsync` szinkronizálása a helyi tárolóhoz a háttér-adatbázissal.

1. Nyissa meg a ToDoActivity.cs a projektet, és a módosítás a visszaállításához a **applicationURL** tulajdonság.
2. Nyomja meg a **F5** kulcs újraépítéséhez és az alkalmazás futtatásához. Az alkalmazás a helyi módosítások szinkronizál az Azure Mobile Apps-háttéralkalmazás segítségével eseménylekérési és eseményküldési műveletek során a `OnRefreshItemsSelected` metódus.
3. (Választható) Az SQL Server Object Explorer vagy egy REST-eszköz, például Fiddler használatával frissített adatok megtekintéséhez. Figyelje meg az adatokat az Azure Mobile Apps-háttéralkalmazás adatbázis és a helyi tárolójába között lett szinkronizálva.
4. Az alkalmazást, és kattintson a helyi tárolóban levő befejezéséhez néhány elem melletti jelölőnégyzet.

   `CheckItem`hívások `SyncAsync` a Mobile Apps-háttéralkalmazás a szinkronizálás minden befejeződött elemhez. `SyncAsync`meghívja a lekérést és a küldést. **Az ügyfél által végrehajtott módosítások táblákon lekérési hajtható végre, amikor egy leküldéses a rendszer mindig futtatja automatikusan**. Ez biztosítja, hogy kapcsolatokat és a helyi tárolóban levő összes tábla azonban konzisztens marad. Ez a viselkedés egy váratlan leküldéses eredményezhet. Ez a viselkedés további információkért lásd: [az Azure Mobile Apps Offline adatszinkronizálás].

## <a name="review-the-client-sync-code"></a>Tekintse át az Ügyfélkód szinkronizálása
Az oktatóanyag befejezésekor letöltött Xamarin ügyfélprojekt [Xamarin Android-alkalmazás létrehozása] már tartalmaz egy helyi SQLite-adatbázis használata a kapcsolat nélküli szinkronizálást támogató kódot. Ez az oktatóanyag kódban már tartalmának rövid áttekintést. A szolgáltatás elméleti áttekintését lásd: [az Azure Mobile Apps Offline adatszinkronizálás].

* Mielőtt bármely tábla művelet végrehajtható, a helyi tárolójába inicializálni kell. A helyi tároló adatbázis inicializálása során `ToDoActivity.OnCreate()` végrehajtja a `ToDoActivity.InitLocalStoreAsync()`. Ezzel a módszerrel hoz létre egy helyi SQLite adatbázis használata a `MobileServiceSQLiteStore` az Azure Mobile Apps-ügyfél SDK által meghatározott osztály.

    A `DefineTable` metódus táblázatot hoz létre a helyi tárolóban, amely megfelel a megadott típus mezőinek `ToDoItem` ebben az esetben. A típus nem kell megadnia a távoli adatbázisban lévő összes oszlopot. Akkor lehet tárolására oszlopok egy részét.

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
            // For more details, see http://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* A `toDoTable` tagja `ToDoActivity` felel meg a `IMobileServiceSyncTable` helyett típusú `IMobileServiceTable`. A IMobileServiceSyncTable összes létrehozása, olvasása, frissítése és Törlés (CRUD) tábla műveletek a helyi tároló adatbázis irányítja.

    Úgy dönt, amikor módosításokat az Azure Mobile Apps-háttéralkalmazás meghívásával leküldve vannak `IMobileServiceSyncContext.PushAsync()`. A szinkronizálási környezetet segíti követésével és kérdez le a módosításokat minden olyan táblát, egy ügyfélalkalmazás módosította, ha a tábla közötti megőrzése érdekében `PushAsync` nevezik.

    A megadott kód hívások `ToDoActivity.SyncAsync()` szinkronizálásra, amikor a todoitem listájának frissítését, vagy a todoitem hozzá, vagy befejeződött. A kód szinkronizálások minden helyi módosítás után.

    A megadott kód összes rögzít a távoli `TodoItem` megkérdezi a táblában, de a rekordok szűrése úgy, hogy a lekérdezés azonosítóját, és a lekérdezési lehetőség arra is `PushAsync`. További információkért tekintse meg a szakasz *növekményes szinkronizálás* a [az Azure Mobile Apps Offline adatszinkronizálás].

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
* [az Azure Mobile Apps Offline adatszinkronizálás]
* [Az Azure Mobile Apps .NET SDK útmutató][8]

<!-- URLs. -->
[Xamarin Android-alkalmazás létrehozása]: ../app-service-mobile-xamarin-android-get-started.md
[az Azure Mobile Apps Offline adatszinkronizálás]: ../app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Xamarin Android-alkalmazás létrehozása]: app-service-mobile-xamarin-android-get-started.md
[Offline adatszinkronizálás az Azure Mobile Appsban]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
