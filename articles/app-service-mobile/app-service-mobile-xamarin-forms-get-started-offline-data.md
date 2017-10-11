---
title: "A Azure Mobile Apps (Xamarin.Forms) kapcsolat nélküli szinkronizálásának engedélyezése |} Microsoft Docs"
description: "App Service Mobile Apps a Xamarin.Forms-alkalmazás gyorsítótárába, a szinkronizálási kapcsolat nélküli adatainak használata"
documentationcenter: xamarin
author: ggailey777
manager: yochayk
editor: 
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: glenga
ms.openlocfilehash: f2bed0a7124517319cc82405c4ab6b4d79aacfe1
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Xamarin.Forms-mobilalkalmazás offline szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag a kapcsolat nélküli szinkronizálás szolgáltatást, az Azure Mobile Apps xamarin.Forms vezet be. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, amellyel kommunikálni tud a mobilalkalmazások--megtekintését, hozzáadását és módosítását adatokat –, akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban tárolódnak. Az eszköz újra online állapotba kerül, ha ezek a változások szinkronizálása megtörtént-e a távoli szolgáltatás.

Ez az oktatóanyag a Xamarin.Forms-gyors üzembe helyezés megoldás alapján hoz létre, amikor befejezte az oktatóanyag [Xamarin iOS-alkalmazás létrehozása] Mobile Apps-alkalmazáshoz. A gyors üzembe helyezés megoldást a Xamarin.Forms kapcsolat nélküli szinkronizálás, amelyen engedélyezhető, hogy támogatásához kódját tartalmazza. Ebben az oktatóanyagban az Azure Mobile Apps offline funkciók bekapcsolása a gyors üzembe helyezés megoldás frissítése. Azt is kiemelése a kapcsolat nélküli tartozó kódot az alkalmazásban. Ha nem használja a letöltött gyorsútmutató-megoldás, hozzá kell adnia a hozzáférési adatok bővítménycsomagok a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a][1].

A kapcsolat nélküli szinkronizálás szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [az Azure Mobile Apps Offline adatszinkronizálás][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>A gyors üzembe helyezés megoldásban kapcsolat nélküli szinkronizálásának engedélyezése
A kapcsolat nélküli szinkronizálás kód szerepel a projekt C# előfeldolgozó irányelvek segítségével. Ha a **OFFLINE\_SZINKRONIZÁLÁSI\_engedélyezve** szimbólum van megadva, a kód elérési utak a build szerepelnek. Windows-alkalmazások esetén is telepítenie kell az SQLite platform.

1. A Visual Studióban, kattintson a jobb gombbal a megoldás > **NuGet-csomagok kezelése megoldáshoz...** , majd keresse meg, és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-csomagot az összes projektet a megoldásban.
2. A Solution Explorerben nyissa meg a TodoItemManager.cs fájlt a projektből az **hordozható** a neve, amely hordozható Class Library projektet, majd állítsa vissza a következő előfeldolgozó irányelv:

        #define OFFLINE_SYNC_ENABLED
3. (Választható) Windows-eszközök támogatása érdekében telepítse a következő SQLite futtatókörnyezetek egyike:

   * **Windows 8.1 futásidejű:** telepítése [a Windows 8.1 SQLite][3].
   * **Windows Phone 8.1:** telepítése [a Windows Phone 8.1 SQLite][4].
   * **Az univerzális Windows Platform** telepítése [az univerzális Windows Universal az SQLite][5].

     Bár a gyors üzembe helyezés nem tartalmaz egy univerzális Windows-projektet, a univerzális Windows platform Xamarin Forms használata támogatott.
4. (Választható) Minden Windows-alkalmazás projekt, kattintson a jobb egérgombbal **hivatkozások** > **hivatkozás hozzáadása...** , bontsa ki a **Windows** mappa > **bővítmények**.
    Engedélyezze a megfelelő **SQLite for Windows** SDK, valamint a **Visual C++ 2013 futásidejű Windows** SDK.
    Az SQLite SDK nevek függően eltérőek az egyes Windows-platformra.

## <a name="review-the-client-sync-code"></a>Tekintse át az Ügyfélkód szinkronizálása
Ez az oktatóanyag kódot már tartalmát rövid áttekintést a `#if OFFLINE_SYNC_ENABLED` irányelvek. A kapcsolat nélküli szinkronizálásának a hordozható osztály Hordozhatóosztálytár-projektjének TodoItemManager.cs projekt fájlban van. A szolgáltatás elméleti áttekintését lásd: [az Azure Mobile Apps Offline adatszinkronizálás][2].

* Mielőtt bármely tábla művelet végrehajtható, a helyi tárolójába inicializálni kell. A helyi adatbázis a inicializálva van a **TodoItemManager** osztálykonstruktor az alábbi kód használatával:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Ez a kód létrehoz egy új helyi SQLite adatbázis használata a **MobileServiceSQLiteStore** osztály.

    A **DefineTable** metódus táblázatot hoz létre a helyi tárolóban, amely megfelel a megadott típusú mezőket.  A típus nem kell megadnia a távoli adatbázisban lévő összes oszlopot. Akkor lehet az oszlopok olyan részhalmazán tárolásához.
* A **todoTable** mezőjét **TodoItemManager** van egy **IMobileServiceSyncTable** helyett típusú **IMobileServiceTable**. A osztály használja az összes helyi adatbázis létrehozása, olvasása, frissítése és törlése (CRUD) tábla műveletek. Úgy dönt, ha ezeket a módosításokat a Mobile Apps-háttéralkalmazás meghívásával leküldve vannak **PushAsync** a a **IMobileServiceSyncContext**. A szinkronizálási környezetet segíti követésével és kérdez le a módosításokat minden olyan táblát, egy ügyfélalkalmazás módosította, ha a tábla közötti megőrzése érdekében **PushAsync** nevezik.

    A következő **SyncAsync** metódust a Mobile Apps-háttéralkalmazás szinkronizálni:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    A példa az alapértelmezett szinkronizálási leírójú egyszerű hibakezelési használja. Egy valós alkalmazás egyéni használatával szeretné kezelni a különféle hibák például hálózati feltételek és a kiszolgáló ütközések **IMobileServiceSyncHandler** végrehajtására.

## <a name="offline-sync-considerations"></a>Kapcsolat nélküli szinkronizálás kapcsolatos szempontok
A példában a **SyncAsync** metódus csak meghívta az indítási és amikor a szinkronizálás van szükség.  Az Android vagy iOS alkalmazások a szinkronizálást kezdeményezni, húzza le a elemek listájában; a Windows, használja a **szinkronizálási** gombra. Egy valós alkalmazás is, sikerült a szinkronizálási eseményindító, ha a hálózati állapota megváltozik.

Elleni táblának függőben lévő lekérési végrehajtásakor helyi frissítések követik kapcsolatban, hogy a pull művelet automatikusan eseményindítók egy előző környezetben leküldéses. Frissítéskor, hozzáadását, és ebben a példában szereplő elemek befejezése, akkor kihagyhatja a explicit **PushAsync** hívható meg.

A megadott kód a távoli TodoItem tábla minden rekordot a rendszer megkérdezi, de a rekordok szűrése úgy, hogy a lekérdezés azonosítóját, és a lekérdezési lehetőség arra is **PushAsync**. További információkért tekintse meg a szakasz *növekményes szinkronizálás* a [az Azure Mobile Apps Offline adatszinkronizálás][2].

## <a name="run-the-client-app"></a>Az ügyfél-alkalmazás futtatása
A kapcsolat nélküli szinkronizálás most engedélyezve van, az ügyfélalkalmazás legalább egyszer futtatni a helyi tárolójába adatbázisának feltöltése minden platformon. Később egy kapcsolat nélküli forgatókönyv szimulálása, és módosíthatja az adatokat, a helyi tárolóban levő, amikor az alkalmazás offline állapotban.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Frissítés az ügyfélalkalmazás szinkronizálási viselkedése
Ebben a szakaszban módosítsa az ügyfél-projektet egy kapcsolat nélküli forgatókönyv szimulálása a háttérkiszolgáló URL-címének érvénytelen az alkalmazás használatával. Azt is megteheti de ki is kapcsolhatja hálózati kapcsolatok az eszköz áthelyezésével "Repülőgép üzemmódban."  Hozzáadásakor, vagy módosítsa az elemeket, ezeket a módosításokat a helyi tárolóban levő tartani, de nincs szinkronizálva a háttér-tárolót, amíg a kapcsolat helyreáll.

1. A Solution Explorerben nyissa meg a Constants.cs projektfájlt a a **hordozható** projektre, és módosítsa az értéket a `ApplicationURL` mutasson az egy URL-cím érvénytelen:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Nyissa meg a TodoItemManager.cs a fájl a **hordozható** projektre, majd adja hozzá egy **catch** az alap **kivétel** az osztály a **próbálja... catch** letiltása **SyncAsync**. Ez **catch** blokk ír a kivételre vonatkozó üzenet a konzolt, az alábbiak szerint:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Hozza létre, és futtassa az ügyfél-alkalmazást.  Vegyen fel egy új elemeket. Figyelje meg, hogy egy kivételt a konzol minden kísérlet a háttér-vel való szinkronizálásának van bejelentkezve. Ezek az új elemek csak a helyi tárolóban levő létezik, csak azokat a mobil-háttéralkalmazást továbbíthatja. Az ügyfélalkalmazás úgy viselkedik, mintha a háttér, támogató, az összes létrehozása, olvasása, frissítés, Törlés (CRUD) operations van csatlakoztatva.
4. Zárja be az alkalmazást, és indítsa újra, hogy ellenőrizze, hogy a helyi tárolójába őrződnek meg a létrehozott új elemeket.
5. (Választható) Visual Studio használatával az Azure SQL Database tábla, hogy a háttér-adatbázis adatai nem változott meg.

    A Visual Studióban nyissa meg a **Server Explorer**. Keresse meg az adatbázist a **Azure**->**SQL-adatbázisok**. Kattintson a jobb gombbal az adatbázist, és válassza ki **nyissa meg az SQL Server Object Explorerben**. Most már megkeresheti az SQL-adatbázistáblában szereplő és annak tartalmát.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Ha újból csatlakozni a mobil-háttéralkalmazást ügyfél alkalmazás frissítése
Ebben a szakaszban csatlakozzon újra az alkalmazásnak, hogy a mobil-háttéralkalmazást, az alkalmazáshoz, és az online állapot vissza hamarosan szimulálja. A frissítési kézmozdulat végrehajtásakor adatok szinkronizálva van a mobil-háttéralkalmazást.

1. Nyissa meg újra a Constants.cs. Javítsa ki a `applicationURL` úgy, hogy a helyes URL-címet mutasson.
2. Építse újra, és futtassa az ügyfél-alkalmazást. Az alkalmazás megpróbálja szinkronizálni a mobil-háttéralkalmazás indítását követően. Győződjön meg arról, hogy nem tartalmaznak kivételeket a hibakereső konzol vannak bejelentkezve.
3. (Választható) Az SQL Server Object Explorer vagy egy REST-eszköz, például Fiddler használatával frissített adatok megtekintéséhez vagy [Postman][6]. Figyelje meg, az adatok szinkronizálása a háttérbeli adatbázis és a helyi tárolójába.

    Figyelje meg, az adatok az adatbázis és a helyi tárolójába lett szinkronizálva, és az alkalmazás forráshoz hozzáadott elemeket tartalmazza.

## <a name="additional-resources"></a>További források
* [Az Azure Mobile Apps az offline adatszinkronizálás][2]
* [Az Azure Mobile Apps .NET SDK útmutató][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: http://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: http://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: http://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: http://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
