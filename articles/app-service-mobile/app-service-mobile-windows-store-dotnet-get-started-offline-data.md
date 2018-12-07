---
title: Offline szinkronizálás engedélyezése az univerzális Windows Platform (UWP) alkalmazás számára a Mobile Apps |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure-mobilalkalmazás offline adatok gyorsítótár és a szinkronizálási az univerzális Windows Platform (UWP) alkalmazásban.
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 69ee9e7101a2b7337e1e42ff5ae09954fbfd50b2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994927"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Windows-alkalmazás kapcsolat nélküli szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan adhat offline támogatást egy univerzális Windows Platform (UWP) egy Azure Mobile Apps-háttéralkalmazás segítségével. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, hogy kommunikáljanak egy mobilalkalmazást – megtekintését, hozzáadását és módosítását a data - még akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban vannak tárolva. Az eszköz újra online állapotba kerül, ha ezeket a módosításokat a távoli háttérrendszerrel vannak szinkronizálva.

Ebben az oktatóanyagban az UWP-alkalmazásprojektet az oktatóanyag frissítése [egy Windows-alkalmazás létrehozása] , az Azure Mobile Apps offline funkciók támogatásához. Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, hozzá kell adnia a data access bővítmény csomagokat a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

A kapcsolat nélküli szinkronizálás – szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="requirements"></a>Követelmények  
Ez az oktatóanyag a következő előfeltételek teljesülésére van szükség:

* A Visual Studio 2013-at futtató, Windows 8.1 vagy újabb.
* Megvalósításának [egy Windows-alkalmazás létrehozása][egy windows-alkalmazás létrehozása].
* [Az Azure Mobile Services SQLite Store][sqlite store nuget]
* [Univerzális Windows Platform fejlesztési SQLite](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Offline funkciók támogatásához az ügyféloldali alkalmazás frissítése
Az Azure Mobile Apps offline funkciói lehetővé teszik helyi adatbázis használhatja, amikor egy offline állapotban van. Ezek a funkciók használata az alkalmazásban, inicializálni egy [SyncContext] [ synccontext] a helyi tárolóba. Ezután hivatkozhat a tábla keresztül a [IMobileServiceSyncTable][IMobileServiceSyncTable] felületet. Az eszközön a helyi tároló SQLite használatban van.

1. Telepítse a [SQLite-modul a Universal Windows Platform](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. A Visual Studióban nyissa meg a NuGet-Csomagkezelő az UWP-alkalmazásprojektet, hogy végrehajtotta a [egy Windows-alkalmazás létrehozása] oktatóanyag.
    Keresse meg és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-csomagot.
3. A Megoldáskezelőben kattintson a jobb gombbal **hivatkozások** > **hivatkozás hozzáadása...** > **Univerzális Windows** > **bővítmények**, majd engedélyezze mindkét **SQLite univerzális Windows Platform** és **univerzális Windows Visual C++ 2015 Runtime Platformon futó**.

    ![Adja hozzá az SQLite UWP-referencia][1]
4. Nyissa meg a MainPage.xaml.cs fájlban, és állítsa vissza a `#define OFFLINE_SYNC_ENABLED` definíciója.
5. A Visual Studióban nyomja le az **F5** kulcs újraépítéséhez és az ügyfélalkalmazás futtatása. Az alkalmazás ugyanúgy működik, az offline szinkronizálás engedélyezése előtti is. A helyi adatbázis azonban most van feltöltve adatokkal, amelyek offline állapotban is használható.

## <a name="update-sync"></a>A háttérrendszer bontsa a kapcsolatot az alkalmazás frissítése
Ebben a szakaszban megszakítja a kapcsolatot, a Mobile Apps egy offline helyzet szimulálásához. Amikor hozzáadja az elemeket, a kivételkezelő kiderül, hogy, hogy az alkalmazás offline módban van. Ebben az állapotban új elemeket hozzáadja a helyi tároló, és során lesznek szinkronizálva a mobilalkalmazás háttérrendszere leküldéses tovább fut a csatlakoztatott állapotban.

1. A megosztott projekt App.xaml.cs szerkesztheti. Tegye megjegyzésbe inicializálása a **MobileServiceClient** , és adja hozzá a következő sort, amely egy érvénytelen a mobilalkalmazás URL-CÍMÉT használja:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Offline viselkedés bemutatják a Wi-Fi és az eszközön mobilhálózaton letiltásával vagy repülőgépes üzemmód használata is.
2. Nyomja meg **F5** össze és futtathatja az alkalmazást. Figyelje meg, hogy a szinkronizálás nem sikerült a frissítés után is, az alkalmazás indításakor.
3. Adja meg az új elemeket, és figyelje meg, hogy leküldéses meghiúsul, és egy [CancelledByNetworkError] állapota minden alkalommal, amikor kattint **mentése**. Azonban az új teendőket szerepel a helyi tároló mindaddig, amíg azok lehet leküldeni a mobil-háttéralkalmazást.  Egy éles alkalmazásban Ha akkor tiltja le ezeket a kivételeket az ügyfélalkalmazás viselkedik, mintha továbbra is csatlakozik a mobil-háttéralkalmazást.
4. Zárja be az alkalmazást, és indítsa újra, hogy ellenőrizze, hogy a létrehozott új elemek, a helyi tárolóban tárolja.
5. (Nem kötelező) A Visual Studióban nyissa meg a **Server Explorer**. Keresse meg az adatbázist a **Azure**->**SQL-adatbázisok**. Kattintson a jobb gombbal az adatbázishoz, és válassza ki **nyissa meg az SQL Server Object Explorer**. Most megnyithatja az SQL database tábla és annak tartalmát. Győződjön meg arról, hogy a háttér adatbázis adatait nem változott.
6. (Nem kötelező) Egy REST-eszközre, például a Fiddler vagy Postman használatával lekérdezheti a mobil háttérszolgáltatásban, a GET-lekérdezésekkel formájában `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>A Mobile Apps-háttéralkalmazást újra az alkalmazás frissítése
Ebben a szakaszban kapcsolódjon újra az alkalmazást, hogy a mobil-háttéralkalmazást. Ezek a változások szimulálása egy hálózati újracsatlakozás az alkalmazást.

Az alkalmazás első futtatásakor a `OnNavigatedTo` eseménykezelő-hívások `InitLocalStoreAsync`. Ez a metódus meghívja a `SyncAsync` szinkronizálni a háttér-adatbázissal a helyi tárolóhoz. Az alkalmazás megpróbál szinkronizálni az indításakor.

1. Nyissa meg az App.XAML.cs fájlban a megosztott projektben, és állítsa vissza az előző inicializálása `MobileServiceClient` használni a megfelelő mobilalkalmazás URL-CÍMÉT.
2. Nyomja le az **F5** kulcs újbóli létrehozása, és futtassa az alkalmazást. Az alkalmazás szinkronizálja a helyi módosításokat az Azure Mobile Apps-háttéralkalmazás segítségével leküldéses és lekéréses műveletek során a `OnNavigatedTo` eseménykezelő hajt végre.
3. (Nem kötelező) Tekintse meg a frissített adatokat az SQL Server Object Explorer vagy egy REST-eszköz, például a Fiddler használatával. Figyelje meg az adatok szinkronizálása az Azure Mobile Apps háttér adatbázis és a helyi tároló között.
4. Az alkalmazásban kattintson a néhány elemet a helyi tároló végrehajtásához melletti jelölőnégyzetet.

   `UpdateCheckedTodoItem` hívások `SyncAsync` való szinkronizálás minden elem a Mobile Apps-háttéralkalmazást. `SyncAsync` meghívja a lekérést és. Azonban **, amikor egy lekéréses elleni egy táblát, amely az ügyfél által végrehajtott módosítások a végrehajtása, egy leküldéses mindig futtatja automatikusan**. Ez a viselkedés garantálja, hogy a helyi tároló kapcsolatok mellett minden tábla konzisztens marad. Egy váratlan leküldéses ezt a viselkedést okozhat.  Ezt a viselkedést a további információkért lásd: [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="api-summary"></a>API összefoglaló
A mobilszolgáltatások offline funkcióinak támogatása érdekében használjuk a [IMobileServiceSyncTable] felületet, és inicializálva [MobileServiceClient.SyncContext] [ synccontext] együtt egy helyi SQLite-adatbázis. Ha offline állapotban van, a Mobile Apps-alkalmazáshoz a szokásos CRUD-műveletek dolgozhatnak, mintha az az alkalmazás továbbra is csatlakozik, miközben a műveleteket a rendszer a helyi tárolóban. A helyi tároló szinkronizálása a kiszolgálóval a következő módszerek használhatók:

* **[PushAsync]**  ezt a módszert a tagja, mert [IMobileServicesSyncContext], módosítások összes kerüljenek a háttérrendszerhez. A kiszolgáló csak a helyi változások rekordok kerülnek.
* **[PullAsync]**  lekérési környezetből indítható el egy [IMobileServiceSyncTable]. Ha nyomon követett módosításait a tábla, egy implicit leküldéses futtatásával győződjön meg arról, hogy a helyi tároló kapcsolatok mellett minden tábla konzisztens marad. A *pushOtherTables* e más táblák környezetében leküld egy implicit leküldéses a paraméterrel állítható be. A *lekérdezés* paraméter egy [IMobileServiceTableQuery<T> ] [ IMobileServiceTableQuery] vagy OData-lekérdezési karakterláncot a visszaadott adatok szűrésére. A *queryId* paraméter a növekményes szinkronizálás meghatározására szolgál. További információkért lásd: [Offline adatszinkronizálás az Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]**  rendszeres időközönként az alkalmazásnak meg kell hívnia ezt a metódust a helyi tároló elavult adatok törlése. Használja a *kényszerítése* paramétert, ha még nem szinkronizált változtatások kiürítése kell.

Ezek a fogalmak kapcsolatos további információkért lásd: [Offline adatszinkronizálás az Azure Mobile Apps](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>További információ
A következő témakörökben további háttér-információkat a kapcsolat nélküli szinkronizálás – a Mobile Apps szolgáltatásával:

* [Offline adatszinkronizálás az Azure Mobile Appsban]
* [Az Azure Mobile Apps .NET SDK-útmutató][8]

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
[Offline adatszinkronizálás az Azure Mobile Appsban]: app-service-mobile-offline-data-sync.md
[egy Windows-alkalmazás létrehozása]: app-service-mobile-windows-store-dotnet-get-started.md
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
