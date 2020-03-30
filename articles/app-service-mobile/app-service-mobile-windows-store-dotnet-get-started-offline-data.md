---
title: Offline szinkronizálás engedélyezése az UWP-alkalmazásban
description: Megtudhatja, hogy miként tárolhat és szinkronizálhat offline adatokat az Univerzális Windows-platform (UWP) alkalmazásban az Azure Mobile App használatával.
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
Ez az oktatóanyag bemutatja, hogyan adhat hozzá offline támogatást egy univerzális Windows-platform (UWP) alkalmazáshoz egy Azure Mobile App háttérrendszer használatával. Az offline szinkronizálás lehetővé teszi a végfelhasználók számára, hogy mobilalkalmazással kommunikáljanak – adatokat tekinthetnek meg, nem adnak hozzá vagy módosíthatnak – még akkor is, ha nincs hálózati kapcsolat. A módosításokat a helyi adatbázis tárolja. Ha az eszköz újra online állapotba kerül, ezek a módosítások szinkronizálódnak a távoli háttérrendszerrel.

Ebben az oktatóanyagban frissíti az UWP-alkalmazásprojektet az Azure Mobile Apps offline funkcióinak támogatásához [létrehozott Windows-alkalmazás létrehozása] című oktatóanyagból. Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, hozzá kell adnia az adatelérési bővítménycsomagokat a projekthez. A kiszolgálóbővítmény-csomagokról további információt [a .NET háttérkiszolgáló SDK azure mobile alkalmazásokhoz szolgáltatása című témakörben talál.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

Az offline szinkronizálási funkcióról az Offline data Sync in Azure Mobile Apps című témakörben olvashat [bővebben.]

## <a name="requirements"></a>Követelmények  
Ez az oktatóanyag a következő előfeltételeket igényli:

* A Visual Studio 2013 Windows 8.1-es vagy újabb verzión fut.
* A [Windows-alkalmazás létrehozása][Windows-alkalmazás létrehozása befejezése].
* [Azure Mobile Services SQLite áruház][sqlite store nuget]
* [SQLite univerzális Windows platformfejlesztéshez](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Az ügyfélalkalmazás frissítése az offline funkciók támogatásához
Az Azure Mobile App offline funkciói lehetővé teszik, hogy egy helyi adatbázissal kommunikáljon, amikor offline módban van. Ha használni szeretné ezeket a funkciókat az alkalmazásban, inicializálja a [SyncContext][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable] felületet. Az SQLite a készülék helyi tárolója.

1. Telepítse az [SQLite futásidejű t az univerzális Windows platformhoz.](https://sqlite.org/2016/sqlite-uwp-3120200.vsix)
2. A Visual Studióban nyissa meg a NuGet csomagkezelőt a Windows létrehozása [alkalmazás] oktatóanyagában elvégzett UWP-alkalmazásprojekthez.
    Keresse meg és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet csomagot.
3. A Megoldáskezelőben kattintson a jobb gombbal **a Hivatkozások** > **hivatkozás hozzáadása...** > Univerzális **Windows-bővítmények** **Universal Windows** > elemre, majd engedélyezze az **SQLite for Universal Windows Platform** és a Visual **C++ 2015 Futtatókörnyezet universal Windows Platform alkalmazásokhoz című alkalmazást**is.

    ![SQLite UWP-hivatkozás hozzáadása][1]
4. Nyissa meg a MainPage.xaml.cs `#define OFFLINE_SYNC_ENABLED` fájlt, és ne fűzzön megjegyzést a definícióhoz.
5. A Visual Studio-ban nyomja le az **F5** billentyűt az ügyfélalkalmazás újraépítéséhez és futtatásához. Az alkalmazás ugyanúgy működik, mint az offline szinkronizálás engedélyezése előtt. A helyi adatbázis azonban most már feltöltve van az offline forgatókönyvben használható adatokkal.

## <a name="update-the-app-to-disconnect-from-the-backend"></a><a name="update-sync"></a>Az alkalmazás frissítése a háttérrendszerről való leválasztáshoz
Ebben a szakaszban megszakítja a kapcsolatot a mobilalkalmazás-háttérrendszerével, hogy offline helyzetet szimuláljon. Adatelemek hozzáadásakor a kivételkezelő jelzi, hogy az alkalmazás offline módban van. Ebben az állapotban a helyi tárolóban hozzáadott új elemek et szinkronizálja a mobilalkalmazás háttér-tartalékszámítógépe, amikor a leküldéses következő futtatás a csatlakoztatott állapotban lesz.

1. A megosztott projektben App.xaml.cs szerkeszt. Fűzzön megjegyzést a **MobileServiceClient** inicializálásához, és adja hozzá a következő sort, amely érvénytelen mobilalkalmazás-URL-címet használ:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Az offline viselkedést a wifi és a mobilhálózatok letiltásával vagy a repülőgép üzemmód használatával is demonstrálhatja.
2. Az alkalmazás létrehozásához és futtatásához nyomja le az **F5** billentyűt. Figyelje meg, hogy a szinkronizálás nem sikerült a frissítés, amikor az alkalmazás elindult.
3. Adjon meg új elemeket, és figyelje meg, hogy a leküldéses sikertelen [a CanceldByNetworkError] állapot minden alkalommal, amikor a **Mentés gombra**kattint. Az új teendők azonban a helyi tárolóban léteznek, amíg le nem lökhetők a mobilalkalmazás háttértartalékába.  Éles alkalmazásban, ha letiltja ezeket a kivételeket, az ügyfélalkalmazás úgy viselkedik, mintha még mindig a mobilalkalmazás-háttérrendszerhez csatlakozna.
4. Zárja be az alkalmazást, és indítsa újra, és ellenőrizze, hogy az új onnan létrehozott elemek megmaradnak-e a helyi tárolóban.
5. (Nem kötelező) A Visual Studio programban nyissa meg a **Server Explorer t**. Nyissa meg az adatbázist az **Azure**->**SQL-adatbázisokban.** Kattintson a jobb gombbal az adatbázisra, és válassza **a Megnyitás az SQL Server Object Explorer programban parancsot.** Most már tallózhat az SQL adatbázistábla és annak tartalmát. Ellenőrizze, hogy a háttéradatbázis ban lévő adatok nem változtak-e.
6. (Nem kötelező) Használjon REST eszközt, például a Hegedűs t vagy a Postman t `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`a mobil háttérrendszer lekérdezéséhez, az űrlapget lekérdezésével.

## <a name="update-the-app-to-reconnect-your-mobile-app-backend"></a><a name="update-online-app"></a>Az alkalmazás frissítése a mobilalkalmazás-háttérrendszer újracsatlakoztatásához
Ebben a szakaszban újra csatlakoztatja az alkalmazást a mobilalkalmazás-háttérhez. Ezek a módosítások hálózati újracsatlakozást szimulálnak az alkalmazásban.

Az alkalmazás első futtatásakor az eseménykezelő meghívja a `OnNavigatedTo` . `InitLocalStoreAsync` Ez a módszer `SyncAsync` viszont felhívja a helyi tároló és a háttéradatbázis szinkronizálását. Az alkalmazás indításkor megkísérli a szinkronizálást.

1. Nyissa meg App.xaml.cs a megosztott projektben, és `MobileServiceClient` a mobilalkalmazás HELYES URL-címének használatához ne fűzzön megjegyzést a korábbi inicializáláshoz.
2. Nyomja le az **F5** billentyűt az alkalmazás újraépítéséhez és futtatásához. Az alkalmazás szinkronizálja a helyi módosításokat az Azure Mobile App `OnNavigatedTo` háttér-leküldéses és lekéréses műveletek használatával, amikor az eseménykezelő végrehajtása.
3. (Nem kötelező) Tekintse meg a frissített adatokat az SQL Server Object Explorer vagy a REST eszköz, például a Fiddler segítségével. Figyelje meg, hogy az adatok szinkronizálva vannak az Azure Mobile App háttéradatbázisa és a helyi tároló között.
4. Az alkalmazásban kattintson néhány elem melletti jelölőnégyzetre, hogy kitöltse azokat a helyi áruházban.

   `UpdateCheckedTodoItem`az `SyncAsync` egyes befejezett elemek szinkronizálásához a mobilalkalmazás-háttérközponttal. `SyncAsync`a push és a pull hívásokat is meghívja. Amikor azonban **lekéréset hajt végre egy olyan táblával szemben, amelyen az ügyfél módosításokat végzett, a leküldéses parancs mindig automatikusan végrehajtásra kerül.** Ez a viselkedés biztosítja, hogy a helyi tároló összes táblája a kapcsolatokkal együtt konzisztens maradjon. Ez a viselkedés váratlan leküldéset eredményezhet.  A viselkedésről további információt az Offline adatszinkronizálás az Azure Mobile Apps alkalmazásban című [témakörben talál.]

## <a name="api-summary"></a>API – összefoglalás
A mobilszolgáltatások offline funkcióinak támogatásához az [IMobileServiceSyncTable] felületet használtuk, és inicializáltuk a [MobileServiceClient.SyncContext-t][synccontext] egy helyi SQLite adatbázissal. Kapcsolat nélküli módban a mobilalkalmazások normál CRUD-műveletei úgy működnek, mintha az alkalmazás továbbra is csatlakoztatva lenne, miközben a műveletek a helyi áruházon keresztül történnek. A helyi tároló és a kiszolgáló szinkronizálására a következő módszerek et alkalmazzák:

* **[PushAsync]** Mivel ez a módszer az [IMobileServicesSyncContext]tagja, az összes tábla módosításai a háttérterületre kerülnek. A rendszer csak a helyi módosításokkal rendelkező rekordokat küldi el a kiszolgálónak.
* **[PullAsync]** A lekéréset egy [IMobileServiceSyncTable táblából]indítják el. Ha a tábla nyomon követett módosításokat hajt végre, implicit leküldéses futtatása győződjön meg arról, hogy a helyi tároló összes táblája és a kapcsolatok konzisztensek maradnak. A pushOtherTables paraméter azt szabályozza, hogy a környezetben lévő többi tábla implicit leküldéses leküldéses.The *pushOtherTables* parameter controls whether other tables in the context are pushed in implicit push. A *lekérdezési* paraméter egy [\<IMobileServiceTableQuery T>][IMobileServiceTableQuery] vagy OData lekérdezési karakterláncot vesz igénybe a visszaadott adatok szűréséhez. A *queryId* paraméter a növekményes szinkronizálás meghatározására szolgál. További információt az Offline adatszinkronizálás az Azure Mobile Apps alkalmazásban című [témakörben talál.](app-service-mobile-offline-data-sync.md#how-sync-works)
* **[PurgeAsync]** Az alkalmazásnak rendszeresen meg kell hívnia ezt a metódust, hogy megtisztítsa az elavult adatokat a helyi tárolóból. Használja a *force* paramétert, ha meg kell tisztítania a még nem szinkronizált módosításokat.

Ezekről a fogalmakról további információt az Offline adatszinkronizálás az Azure Mobile Apps alkalmazásban című [témakörben talál.](app-service-mobile-offline-data-sync.md#how-sync-works)

## <a name="more-info"></a>További információ
Az alábbi témakörök további háttérinformációkat tartalmaznak a mobilalkalmazások offline szinkronizálási funkciójáról:

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
[windows os alkalmazás létrehozása]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSzinkronizálható]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[iMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CanceldByNetworkError (CanceldByNetworkError)]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
