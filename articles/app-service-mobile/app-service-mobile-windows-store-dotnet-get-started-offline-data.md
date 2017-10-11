---
title: "Az univerzális Windows Platform (UWP-) alkalmazás, a Mobile Apps kapcsolat nélküli szinkronizálásának engedélyezése |} Microsoft Docs"
description: "Megtudhatja, hogyan használja a kapcsolat nélküli adatok gyorsítótár és a szinkronizálási Azure Mobile Apps az univerzális Windows Platform (UWP) alkalmazásban."
documentationcenter: windows
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 4b0a57c3bab688487eb9a50461b406e1a6e477c6
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Windows-alkalmazás kapcsolat nélküli szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan adhat offline támogatást egy univerzális Windows Platform (UWP) az Azure Mobile Apps-háttéralkalmazás segítségével. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, amellyel kommunikálni tud a mobilalkalmazások--megtekintését, hozzáadását és - adatok módosítását, akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban tárolódnak. Az eszköz újra online állapotba kerül, ha ezek a változások szinkronizálása megtörtént-e a távoli háttér.

Ebben az oktatóanyagban az UWP-alkalmazásprojektet a oktatóanyagot frissítése [Windows-alkalmazás létrehozása] az Azure Mobile Apps offline funkciók támogatásához. Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, hozzá kell adnia a hozzáférési adatok bővítménycsomagok a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

A kapcsolat nélküli szinkronizálás szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [az Azure Mobile Apps Offline adatszinkronizálás].

## <a name="requirements"></a>Követelmények
Ez az oktatóanyag szükséges a következő előfeltételeknek:

* A Visual Studio 2013 fut, a Windows 8.1 vagy újabb.
* Megvalósításának [Windows-alkalmazás létrehozása][windows-alkalmazás létrehozása].
* [Az Azure Mobile Services SQLite Store][sqlite store nuget]
* [Univerzális Windows Platform fejlesztési SQLite](http://www.sqlite.org/downloads)

## <a name="update-the-client-app-to-support-offline-features"></a>Frissítés az ügyfélalkalmazás offline funkciók támogatásához
Az Azure Mobile Apps offline funkciók lehetővé teszik egy helyi adatbázist kommunikál, amikor egy offline forgatókönyvben. Ezeket a szolgáltatásokat az alkalmazás használatához inicializálni egy [SyncContext] [ synccontext] a helyi tárolójába. A táblához majd hivatkozzon a [IMobileServiceSyncTable][IMobileServiceSyncTable] felületet. SQLite lesz a helyi tárolójába, az eszközön.

1. Telepítse a [SQLite futásidejű a univerzális Windows Platform](http://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. A Visual Studióban nyissa meg az UWP-alkalmazásprojektet, amelyek a NuGet-csomagkezelőjét a [Windows-alkalmazás létrehozása] oktatóanyag.
    Keresse meg és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-csomagot.
3. A Megoldáskezelőben kattintson a jobb gombbal **hivatkozások** > **hivatkozás hozzáadása...** >**Univerzális Windows** > **bővítmények**, engedélyeznie kell a mindkét **univerzális Windows Platform SQLite** és **a Visual C++ 2015 futtatókörnyezete Az univerzális Windows Platform alkalmazások**.

    ![SQLite UWP-hivatkozás hozzáadása][1]
4. Nyissa meg a MainPage.xaml.cs fájlban, és állítsa vissza a `#define OFFLINE_SYNC_ENABLED` definíciója.
5. A Visual Studio, nyomja le az **F5** kulcs újraépítéséhez és az ügyfélalkalmazás futtatása. Az alkalmazás működik, mint előtti engedélyezte a kapcsolat nélküli szinkronizálás. A helyi adatbázis azonban most van feltöltve adatokkal egy kapcsolat nélküli forgatókönyvben használható.

## <a name="update-sync"></a>A háttérbeli leválasztása alkalmazás frissítése
Ebben a szakaszban megszakítja a kapcsolatot egy kapcsolat nélküli helyzet szimulálása a Mobile Apps-háttéralkalmazás számára. Adatelemek hozzáadásakor a kivételkezelő megtudhatja, hogy az alkalmazás offline módban van. Ebben az állapotban lévő új elemek a helyi tárolóban levő adva, és során lesznek szinkronizálva a mobil-háttéralkalmazás számára leküldéses következő futtatás csatlakoztatott állapotban.

1. A megosztott projekt App.xaml.cs szerkesztése Inicializálása megjegyzésbe a **MobileServiceClient** és adja hozzá a következő sort, amely egy érvénytelen mobilalkalmazás URL-CÍMÉT használja:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Kapcsolat nélküli viselkedés bemutatják a Wi-Fi és az eszközön cellás hálózatok letiltásával vagy repülőgép üzemmódban is.
2. Nyomja le az **F5** össze és futtathatja az alkalmazást. Figyelje meg a szinkronizálás nem sikerült a frissítés, az alkalmazás indításakor.
3. Adja meg az új elemeket, és figyelje meg, hogy leküldéses sikertelen, és egy [CancelledByNetworkError] állapota minden egyes kattint **mentése**. Azonban az új todo elemeket szerepel a helyi tárolójába csak azokat a mobil-háttéralkalmazás továbbíthatja.  Egy éles alkalmazásban Ha Ön ne jelenjen meg többé az ilyen kivételek az ügyfélalkalmazás úgy viselkedik, mintha az továbbra is kapcsolódik a mobil-háttéralkalmazást.
4. Zárja be az alkalmazást, és indítsa újra, hogy ellenőrizze, hogy a helyi tárolójába őrződnek meg a létrehozott új elemeket.
5. (Választható) A Visual Studióban nyissa meg a **Server Explorer**. Keresse meg az adatbázist a **Azure**->**SQL-adatbázisok**. Kattintson a jobb gombbal az adatbázist, és válassza ki **nyissa meg az SQL Server Object Explorerben**. Most már megkeresheti az SQL-adatbázistáblában szereplő és annak tartalmát. Győződjön meg arról, hogy a háttér-adatbázis adatai nem módosult.
6. (Választható) Például a Fiddler vagy Postman REST eszköz segítségével lekérdezni a mobil-háttéralkalmazást, GET lekérdezéssel formájában `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>A Mobile Apps-háttéralkalmazás csatlakozzanak az alkalmazás frissítésére
Ebben a szakaszban az alkalmazásnak, hogy a mobil-háttéralkalmazás újra. A módosítások az alkalmazás egy hálózati újracsatlakozás szimulálása.

Az alkalmazás első futtatásakor a `OnNavigatedTo` eseménykezelő-hívások `InitLocalStoreAsync`. Ez a metódus meghívja a `SyncAsync` szinkronizálása a helyi tárolóhoz a háttér-adatbázissal. Az alkalmazás indításakor szinkronizálása kísérli meg.

1. Nyissa meg az App.xaml.cs a megosztott projektben, és állítsa vissza az előző inicializálása `MobileServiceClient` használatára a megfelelő mobilalkalmazás URL-CÍMÉT.
2. Nyomja meg a **F5** kulcs újraépítéséhez és az alkalmazás futtatásához. Az alkalmazás a helyi módosítások szinkronizál az Azure Mobile Apps-háttéralkalmazás segítségével eseménylekérési és eseményküldési műveletek során a `OnNavigatedTo` eseménykezelő hajt végre.
3. (Választható) Az SQL Server Object Explorer vagy egy REST-eszköz, például Fiddler használatával frissített adatok megtekintéséhez. Figyelje meg az adatokat az Azure Mobile Apps-háttéralkalmazás adatbázis és a helyi tárolójába között lett szinkronizálva.
4. Az alkalmazást, és kattintson a helyi tárolóban levő befejezéséhez néhány elem melletti jelölőnégyzet.

   `UpdateCheckedTodoItem`hívások `SyncAsync` a Mobile Apps-háttéralkalmazás a szinkronizálás minden befejeződött elemhez. `SyncAsync`meghívja a lekérést és a küldést. Azonban **egy táblát, amely az ügyfél által végrehajtott módosítások ellen lekérési hajtható végre, amikor egy leküldéses a rendszer mindig futtatja automatikusan**. Ez a viselkedés garantálja, hogy kapcsolatokat és a helyi tárolóban levő összes tábla azonban konzisztens marad. Ez a viselkedés egy váratlan leküldéses eredményezhet.  Ez a viselkedés további információkért lásd: [az Azure Mobile Apps Offline adatszinkronizálás].

## <a name="api-summary"></a>API-összefoglalót
A mobile services offline funkciók támogatásához használtuk az [IMobileServiceSyncTable] felületet, és inicializálva [MobileServiceClient.SyncContext] [ synccontext] rendelkező egy helyi SQLite-adatbázis. Kapcsolat nélküli módban, a Mobile Apps-alkalmazáshoz a szokásos CRUD műveletek dolgozhatnak, mintha az alkalmazás még mindig kapcsolódik, míg a műveletek a helyi tárolóban történik. A következő módszerek használhatók a helyi tárolójába szinkronizálása a kiszolgálóval:

* **[PushAsync]**  mivel ez a módszer egy tagja [IMobileServicesSyncContext], minden táblák között módosítások vannak leküldött háttérkiszolgálóra. A kiszolgáló csak a helyi módosításokkal bejegyzések kerülnek.
* **[PullAsync]**  lekérési elindult a egy [IMobileServiceSyncTable]. Ha a tábla észlelt változásokat, egy implicit leküldéses futtatásával győződjön meg arról, hogy kapcsolatokat és a helyi tárolóban levő összes tábla azonban konzisztens marad. A *pushOtherTables* e más táblák környezetében található egy implicit elküldte azokat a paraméterrel állítható be. A *lekérdezés* paraméter egy [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] vagy visszaadott adatok szűrésére OData-lekérdezési karakterlánc. A *queryId* paraméter a növekményes szinkronizálás azonosítására szolgál. További információkért lásd: [az Azure Mobile Apps Offline adatszinkronizálás](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]**  az alkalmazás kell hívásához Ez a módszer törli az elavult adatokat a helyi tárolóból. Használja a *kényszerítése* paramétert, ha még nem szinkronizált módosításokat kiürítése van szüksége.

Ezek a fogalmak kapcsolatos további információkért lásd: [az Azure Mobile Apps Offline adatszinkronizálás](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>További információ
Az alábbi témakörök nyújtanak további információt a Mobile Apps a kapcsolat nélküli szinkronizálás funkciója:

* [az Azure Mobile Apps Offline adatszinkronizálás]
* [Az Azure Mobile Apps .NET SDK útmutató][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[az Azure Mobile Apps Offline adatszinkronizálás]: app-service-mobile-offline-data-sync.md
[windows-alkalmazás létrehozása]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: http://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
