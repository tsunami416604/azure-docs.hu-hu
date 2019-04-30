---
title: Offline szinkronizálás engedélyezése az Azure Mobile App (Xamarin iOS)
description: Ismerje meg a Xamarin iOS-alkalmazását a cache és a szinkronizálási offline adatok használata App Service Mobile Apps
documentationcenter: xamarin
author: conceptdev
manager: cfowler
editor: ''
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 6a43ed0a50082cc37587752631c707bf9b5059ab
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097467"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Tegye lehetővé az offline szinkronizálást Xamarin.iOS-mobilalkalmazásában
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja a kapcsolat nélküli szinkronizálás – a szolgáltatás az Azure Mobile Apps xamarin.Ios. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, hogy kommunikáljanak egy mobilalkalmazást – megtekintését, hozzáadását és módosítását adatokat –, akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban vannak tárolva. Miután az eszköz újra online állapotba kerül, ezeket a módosításokat a távoli szolgáltatással vannak szinkronizálva.

Ebben az oktatóanyagban a Xamarin.iOS-alkalmazás projektet, a frissítés [Xamarin iOS-alkalmazás létrehozása] , az Azure Mobile Apps offline funkciók támogatásához. Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, hozzá kell adnia a data access bővítmény csomagokat a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

A kapcsolat nélküli szinkronizálás – szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="update-the-client-app-to-support-offline-features"></a>Offline funkciók támogatásához az ügyféloldali alkalmazás frissítése
Az Azure Mobile Apps offline funkciói lehetővé teszik helyi adatbázis használhatja, amikor egy offline állapotban van. Ezek a funkciók használata az alkalmazásban, inicializálja a [SyncContext] a helyi tárolóba. Hivatkozhat a tábla a [IMobileServiceSyncTable] felületén keresztül. Az eszközön a helyi tároló SQLite használatban van.

1. Nyissa meg a NuGet-Csomagkezelő a projekthez, hogy végrehajtotta a [Xamarin iOS-alkalmazás létrehozása] oktatóanyag, majd keresse meg és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-csomagot.
2. Nyissa meg a QSTodoService.cs fájlt, és vonja vissza a `#define OFFLINE_SYNC_ENABLED` definíciója.
3. Építse újra, és futtassa az ügyfélalkalmazást. Az alkalmazás ugyanúgy működik, az offline szinkronizálás engedélyezése előtti is. A helyi adatbázis azonban most van feltöltve adatokkal, amelyek offline állapotban is használható.

## <a name="update-sync"></a>A háttérrendszer bontsa a kapcsolatot az alkalmazás frissítése
Ebben a szakaszban megszakítja a kapcsolatot, a Mobile Apps egy offline helyzet szimulálásához. Amikor hozzáadja az elemeket, a kivételkezelő kiderül, hogy, hogy az alkalmazás offline módban van. Ebben az állapotban új elemeket hozzáadja a helyi tároló, és során lesznek szinkronizálva a mobilalkalmazás háttérrendszere leküldéses tovább fut a csatlakoztatott állapotban.

1. A megosztott projekt QSToDoService.cs szerkesztheti. Módosítsa a **applicationURL** átirányítása egy URL-cím érvénytelen:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Kapcsolat nélküli viselkedése is bemutatják, Wi-Fi és az eszközön mobilhálózaton letiltása, vagy repülési üzemmód használatával.
2. Hozza létre és futtassa az alkalmazást. Figyelje meg, hogy a szinkronizálás nem sikerült a frissítés után is, az alkalmazás indításakor.
3. Adja meg az új elemeket, és figyelje meg, hogy leküldéses sikertelen [CancelledByNetworkError] állapotú minden alkalommal, amikor kattint **mentése**. Azonban az új teendőket szerepel a helyi tároló mindaddig, amíg azok lehet leküldeni a mobil-háttéralkalmazást.  Egy éles alkalmazásban Ha akkor tiltja le ezeket a kivételeket az ügyfélalkalmazás viselkedik, mintha továbbra is csatlakozik a mobil-háttéralkalmazást.
4. Zárja be az alkalmazást, és indítsa újra, hogy ellenőrizze, hogy a létrehozott új elemek, a helyi tárolóban tárolja.
5. (Nem kötelező) Ha a számítógépen telepítve van a Visual Studio, nyissa meg a **Server Explorer**. Keresse meg az adatbázist a **Azure**-> **SQL-adatbázisok**. Kattintson a jobb gombbal az adatbázishoz, és válassza ki **nyissa meg az SQL Server Object Explorer**. Most megnyithatja az SQL database tábla és annak tartalmát. Győződjön meg arról, hogy a háttér adatbázis adatait nem változott.
6. (Nem kötelező) Egy REST-eszközre, például a Fiddler vagy Postman használatával lekérdezheti a mobil háttérszolgáltatásban, a GET-lekérdezésekkel formájában `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>A Mobile Apps-háttéralkalmazást újra az alkalmazás frissítése
Ebben a szakaszban kapcsolódjon újra az alkalmazást, hogy a mobil-háttéralkalmazást. Ez szimulálja az alkalmazás offline állapotban van áthelyezését a mobil-háttéralkalmazás az online állapotban.   Ha a hálózati törés a hálózati kapcsolat kikapcsolásával szimulált, kódmódosítás nélkül van szükség.
Kapcsolja a hálózatra újra.  Az alkalmazás első futtatásakor a `RefreshDataAsync` módszert hívja meg. Ezután meghívja `SyncAsync` szinkronizálni a háttér-adatbázissal a helyi tárolóhoz.

1. Nyissa meg a megosztott projekt QSToDoService.cs, és a módosítás a visszaállítás a **applicationURL** tulajdonság.
2. Építse újra, és futtassa az alkalmazást. Az alkalmazás szinkronizálja a helyi módosításokat az Azure Mobile Apps-háttéralkalmazás segítségével leküldéses és lekéréses műveletek során a `OnRefreshItemsSelected` metódus hajtja végre.
3. (Nem kötelező) Tekintse meg a frissített adatokat az SQL Server Object Explorer vagy egy REST-eszköz, például a Fiddler használatával. Figyelje meg az adatok szinkronizálása az Azure Mobile Apps háttér adatbázis és a helyi tároló között.
4. Az alkalmazásban kattintson a néhány elemet a helyi tároló végrehajtásához melletti jelölőnégyzetet.

   `CompleteItemAsync` hívások `SyncAsync` való szinkronizálás minden elem a Mobile Apps-háttéralkalmazást. `SyncAsync` meghívja a lekérést és.
   **Minden alkalommal, amikor egy lekéréses elleni egy táblát, amely az ügyfél által végrehajtott módosítások a végrehajtása, a Szinkronizáló ügyfél környezetfüggő leküldéses a rendszer mindig futtatja először automatikusan**. Az implicit leküldéses biztosítja, hogy a helyi tároló kapcsolatok mellett minden tábla konzisztens marad. Ezt a viselkedést a további információkért lásd: [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="review-the-client-sync-code"></a>Tekintse át az Ügyfélkód szinkronizálása
A Xamarin ügyfél-projektet az oktatóanyag befejezésekor letöltött [Xamarin iOS-alkalmazás létrehozása] már tartalmaz egy helyi SQLite-adatbázis segítségével kapcsolat nélküli szinkronizálás támogató kódot. Íme, mi már szerepel az oktatóprogram kódmintája rövid áttekintése. A szolgáltatás fogalmi áttekintése, lásd: [Offline adatszinkronizálás az Azure Mobile Appsban].

* Bármely table műveletek végrehajtása előtt inicializálni kell a helyi tárolóban. A helyi tároló adatbázis inicializálása során `QSTodoListViewController.ViewDidLoad()` végrehajtja `QSTodoService.InitializeStoreAsync()`. Ezzel a módszerrel hoz létre egy új helyi SQLite adatbázis használata a `MobileServiceSQLiteStore` az Azure Mobile Apps-ügyfél SDK által biztosított osztály.

    A `DefineTable` módszer létrehoz egy táblázatot a helyi tároló, amely megfelel a megadott típus mezőinek `ToDoItem` ebben az esetben. A típus nem kell megadnia a távoli adatbázisban lévő összes oszlopot. Oszlopok egy része tárolására is lehetőség.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* A `todoTable` tagja `QSTodoService` azonban a `IMobileServiceSyncTable` helyett írja be `IMobileServiceTable`. A IMobileServiceSyncTable összes létrehozása, olvasása, frissítése és törlési (CRUD) tábla műveletek a helyi tároló adatbázis irányítja.

    Úgy dönt, ha ezek a módosítások akkor lesznek leküldve a az Azure Mobile Apps-háttéralkalmazás meghívásával `IMobileServiceSyncContext.PushAsync()`. A szinkronizálási környezetet segít nyomon követésével és módosítások leküldése minden táblát egy ügyfélalkalmazás mikor módosult a táblák közötti kapcsolatok megőrzése `PushAsync` nevezzük.

    A megadott kód meghívja `QSTodoService.SyncAsync()` szinkronizálásra, amikor a todoitem lista frissül, vagy a todoitem hozzáadásakor vagy befejeződött. Az alkalmazás szinkronizálások minden helyi módosítás után. Lekérési van függőben lévő helyi frissítést nyomon követett táblákon hajtja végre a környezet által, a pull művelet először automatikusan aktiválása egy környezeti leküldéses is.

    A megadott kód, mind a távoli rögzíti `TodoItem` tábla lesznek lekérdezve, de egyben lehetővé teszi a rekordok szűrése a lekérdezés azonosítóját átadásával és lekérdezése `PushAsync`. További információkért lásd: a szakasz *növekményes szinkronizálás* a [Offline adatszinkronizálás az Azure Mobile Appsban].

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
* [Az Azure Mobile Apps .NET SDK-útmutató][8]

<!-- Images -->

<!-- URLs. -->
[Xamarin iOS-alkalmazás létrehozása]: app-service-mobile-xamarin-ios-get-started.md
[Offline adatszinkronizálás az Azure Mobile Appsban]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
