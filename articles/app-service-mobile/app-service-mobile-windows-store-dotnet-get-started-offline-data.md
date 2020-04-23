---
title: Offline szinkronizálás engedélyezése a UWP-alkalmazáshoz
description: Ismerje meg, hogy miként lehet az Azure Mobile App használatával gyorsítótárazni és szinkronizálni a Univerzális Windows-platform (UWP) alkalmazás offline adatait.
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 236d4c199a13b02bcd82ae02657bbd35e45f729b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458817"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Windows-alkalmazás kapcsolat nélküli szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan adhat offline támogatást egy Univerzális Windows-platform-(UWP-) alkalmazáshoz egy Azure Mobile apps-háttér használatával. Az offline szinkronizálás lehetővé teszi, hogy a végfelhasználók a mobil alkalmazásokkal is használhassák az adatmegjelenítést, hozzáadást vagy módosítást, még akkor is, ha nincs hálózati kapcsolat. A módosításokat a rendszer egy helyi adatbázisban tárolja. Miután az eszköz ismét online állapotba került, a változások szinkronizálva lesznek a távoli háttérrel.

Ebben az oktatóanyagban frissíti a UWP alkalmazás projektjét a Windows- [alkalmazás létrehozása] az Azure Mobile apps offline szolgáltatásainak támogatásához című oktatóanyagban. Ha nem a letöltött gyors üzembe helyezési kiszolgáló projektet használja, hozzá kell adnia az adatelérési bővítmények csomagjait a projekthez. További információ a kiszolgálói bővítmények csomagjairól: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Az offline szinkronizálási szolgáltatással kapcsolatos további tudnivalókért tekintse meg az [Offline adatszinkronizálás az Azure Mobile Appsban]című témakört.

## <a name="requirements"></a>Követelmények  
Ehhez az oktatóanyaghoz a következő előfeltételek szükségesek:

* A Visual Studio 2013 Windows 8,1 vagy újabb rendszeren fut.
* Windows- [alkalmazás]létrehozásának befejezése[Windows-alkalmazás létrehozása].
* [Azure Mobile Services SQLite-tároló][sqlite store nuget]
* [SQLite Univerzális Windows-platform-fejlesztéshez](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Az ügyfélalkalmazás frissítése az offline funkciók támogatására
Az Azure Mobile App offline funkciói lehetővé teszik a helyi adatbázissal való interakciót, ha offline környezetben van. A funkciók az alkalmazásban való használatához inicializálnia kell egy [SyncContext][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable] felületet. Az SQLite a helyi tárolóként van használatban az eszközön.

1. Telepítse az [SQLite futtatókörnyezetet a univerzális Windows-platformhoz](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. A Visual Studióban nyissa meg a NuGet csomagkezelő alkalmazást a UWP alkalmazás-projekthez, amelyet a [create a Windows app] oktatóanyagban végzett.
    Keresse meg és telepítse a **Microsoft. Azure. Mobile. Client. SQLiteStore** NuGet csomagot.
3. Megoldáskezelőban kattintson a jobb gombbal **a hivatkozás** > **hozzáadása...** > **univerzális Windows** > - **bővítmények**lehetőségre, majd engedélyezze a **univerzális Windows-platform** és a **Visual C++ 2015 futtatókörnyezetet az univerzális Windows-platform alkalmazások esetében**.

    ![SQLite UWP-hivatkozás hozzáadása][1]
4. Nyissa meg a MainPage.xaml.cs fájlt, és `#define OFFLINE_SYNC_ENABLED` tegye meg a definíció megjegyzését.
5. A Visual Studióban nyomja le az **F5** billentyűt az újraépítéshez és az ügyfélalkalmazás futtatásához. Az alkalmazás ugyanúgy működik, mint az offline szinkronizálás engedélyezése előtt. A helyi adatbázis azonban már fel van töltve olyan adatokkal, amelyek offline környezetben használhatók.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Az alkalmazás frissítése a háttérből való leválasztáshoz
Ebben a szakaszban megszakítja a kapcsolatát a Mobile apps-háttérrel az offline állapot szimulálása érdekében. Adatelemek hozzáadásakor a kivétel kezelője jelzi, hogy az alkalmazás kapcsolat nélküli módban van. Ebben az állapotban a helyi tárolóban hozzáadott új elemek jelennek meg, és a rendszer szinkronizálja őket a Mobile apps-háttérrel, ha a leküldéses szolgáltatás a következő csatlakoztatott állapotban fut.

1. Szerkessze a App.xaml.cs a megosztott projektben. Tegye megjegyzésbe a **MobileServiceClient** inicializálását, és adja hozzá a következő sort, amely egy érvénytelen Mobile App URL-címet használ:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Az offline viselkedést is bemutathatja, ha letiltja a WiFi-t és a mobil hálózatokat az eszközön, vagy a repülőgép üzemmódot használja.
2. Az alkalmazás létrehozásához és futtatásához nyomja le az **F5** billentyűt. Figyelje meg, hogy a szinkronizálás sikertelen volt a frissítéskor, amikor az alkalmazás elindul.
3. Adja meg az új elemeket, és figyelje meg, hogy a leküldéses művelet minden alkalommal [CancelledByNetworkError] , amikor rákattint a **Mentés**gombra. Az új teendők azonban a helyi tárolóban léteznek, amíg a Mobile apps-háttérbe nem tudnak leküldeni őket.  Éles alkalmazásban, ha letiltja ezeket a kivételeket, az ügyfélalkalmazás úgy viselkedik, mintha továbbra is csatlakoztatva legyen a Mobile apps-háttérhöz.
4. Az alkalmazás bezárásával ellenőrizze, hogy a létrehozott új elemek megmaradnak-e a helyi tárolóban.
5. Választható A Visual Studióban nyissa meg a **Server Explorert**. Navigáljon az adatbázishoz az **Azure**->**SQL Database**-ben. Kattintson a jobb gombbal az adatbázisra, és válassza **a Megnyitás a SQL Server Object Explorerban**lehetőséget. Most megkeresheti az SQL Database-táblázatot és annak tartalmát. Győződjön meg arról, hogy a háttér-adatbázisban lévő összes érték nem módosult.
6. Választható Használjon REST-eszközt, mint például a Hegedűs vagy a Poster a mobil háttér lekérdezéséhez egy GET lekérdezéssel az `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`űrlapon.

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Az alkalmazás frissítése a Mobile apps-háttér újrakapcsolódásához
Ebben a szakaszban újra csatlakozik az alkalmazáshoz a Mobile apps-háttérrel. Ezek a változások szimulálják a hálózati újracsatlakoztatást az alkalmazáson.

Az alkalmazás első futtatásakor az eseménykezelő meghívja `OnNavigatedTo` `InitLocalStoreAsync`a következőt:. Ezzel a módszerrel meghívja `SyncAsync` a helyi tároló szinkronizálását a háttér-adatbázissal. Az alkalmazás az indításkor megkísérli a szinkronizálást.

1. Nyissa meg a App.xaml.cs a megosztott projektben, és adja meg a korábbi `MobileServiceClient` inicializálási megjegyzéseit, hogy a megfelelő a Mobile App URL-címét használja.
2. Az alkalmazás újraépítéséhez és futtatásához nyomja le az **F5** billentyűt. Az alkalmazás a helyi módosításokat az Azure Mobile apps-háttérrel szinkronizálja leküldéses és lekéréses műveletek használatával, amikor az eseménykezelő végrehajtja a `OnNavigatedTo` műveletet.
3. Választható A frissített adatok megtekintése SQL Server Object Explorer vagy REST-eszköz, például a Hegedűs használatával. Figyelje meg, hogy az adatokat szinkronizálták az Azure Mobile App backend-adatbázisa és a helyi tároló között.
4. Az alkalmazásban kattintson a jelölőnégyzetre néhány elem mellett, hogy elvégezze azokat a helyi tárolóban.

   `UpdateCheckedTodoItem`meghívja `SyncAsync` az egyes befejezett elemek szinkronizálását a Mobile apps-háttérrel. `SyncAsync`leküldéses és lekéréses hívásokat hív meg. Ha azonban a **lekérést egy olyan táblán hajtja végre, amelyen az ügyfél módosításokat végzett, a leküldéses művelet mindig automatikusan lefut**. Ez a viselkedés biztosítja, hogy a helyi tárolóban lévő összes tábla és a kapcsolatok konzisztensek maradjanak. Ez a viselkedés nem várt leküldést eredményezhet.  További információ erről a viselkedésről: [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="api-summary"></a>API – összefoglalás
A Mobile Services offline szolgáltatásainak támogatásához a [IMobileServiceSyncTable] felületet használtuk, és a [MobileServiceClient. SyncContext][synccontext] inicializálása egy helyi SQLite-adatbázissal. Offline üzemmódban a Mobile Apps normál szifilisz-műveletei ugyanúgy működnek, mint ha az alkalmazás továbbra is csatlakoztatva van, amíg a műveletek a helyi tárolón történnek. A következő módszerekkel lehet szinkronizálni a helyi tárolót a-kiszolgálóval:

* **[PushAsync]** Mivel ez a módszer a [IMobileServicesSyncContext]tagja, a rendszer az összes tábla változásait leküldi a háttérbe. Csak a helyi módosításokat tartalmazó rekordok lesznek elküldve a kiszolgálónak.
* **[PullAsync]** A rendszer lekérést indít el egy [IMobileServiceSyncTable]. Ha a táblázatban nyomon követett változások vannak, egy implicit leküldéses futtatással gondoskodhat arról, hogy a helyi tárolóban lévő összes tábla és a kapcsolatok konzisztensek maradjanak. A *pushOtherTables* paraméter azt szabályozza, hogy a kontextusban lévő más táblák egy implicit leküldéses üzenetben vannak-e leküldve. A *lekérdezési* paraméter egy [IMobileServiceTableQuery\<T>][IMobileServiceTableQuery] vagy OData lekérdezési karakterláncot használ a visszaadott adatértékek szűréséhez. A *queryId* paraméter a növekményes szinkronizálás definiálására szolgál. További információ: [Offline adatszinkronizálás az Azure Mobile Appsban](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** Az alkalmazásnak rendszeresen meg kell hívnia ezt a módszert a régi adatok helyi tárolóból való törléséhez. Akkor használja a *Force* paramétert, ha ki kell ürítenie a még nem szinkronizált módosításokat.

További információ ezekről a fogalmakról: [Offline adatszinkronizálás az Azure Mobile Appsban](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>További információ
A következő témakörök további háttér-információkat biztosítanak a Mobile Apps offline szinkronizálási funkciójával kapcsolatban:

* [Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]
* [Azure Mobile Apps .NET SDK HOWTO][8]

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
[Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]: app-service-mobile-offline-data-sync.md
[Windows-alkalmazás létrehozása]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
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
