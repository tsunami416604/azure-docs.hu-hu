---
title: Kapcsolat nélküli szinkronizálás engedélyezése (Xamarin.Forms) | Microsoft dokumentumok
description: Ismerje meg, hogyan tárolhatja és szinkronizálhatja az Offline adatokat a Xamarin.Forms alkalmazásban.
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 57776073ef7e2760b308df22280faf1d65b8d104
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458851"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Offline szinkronizálás engedélyezése a Xamarin.Forms mobilalkalmazáshoz
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja az Azure Mobile Apps for Xamarin.Forms offline szinkronizálási funkcióját. Az offline szinkronizálás lehetővé teszi a végfelhasználók számára, hogy mobilalkalmazással kommunikáljanak – adatok megtekintése, hozzáadása vagy módosítása – még akkor is, ha nincs hálózati kapcsolat. A módosításokat a helyi adatbázis tárolja. Ha az eszköz újra online állapotba kerül, ezek a módosítások szinkronizálódnak a távoli szolgáltatással.

Ez az oktatóanyag a Xamarin.Forms mobilalkalmazásokhoz készült rövid útmutatóján alapul, amelyet az oktatóanyag befejezésekor hoz létre [Xamarin iOS-alkalmazás létrehozása]. A Xamarin.Forms gyorsútmutatója tartalmazza az offline szinkronizálást támogató kódot, amelyet csak engedélyezni kell. Ebben az oktatóanyagban frissíti a gyorsindítási megoldást az Azure Mobile Apps offline funkcióinak bekapcsolásához. Az alkalmazás offline-specifikus kódját is kiemeljük. Ha nem használja a letöltött gyorsindítási megoldást, hozzá kell adnia az adatelérési bővítménycsomagokat a projekthez. A kiszolgálóbővítmény-csomagokról további információt [a .NET háttérkiszolgáló SDK azure mobile alkalmazásokhoz szolgáltatása című témakörben talál.][1]

Az offline szinkronizálási funkcióról az Offline data Sync in Azure Mobile Apps című témakörben olvashat [bővebben.][2]

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Offline szinkronizálási funkció engedélyezése a gyorsindítási megoldásban
A kapcsolat nélküli szinkronizálási kód c# előfeldolgozó-utasításokkal szerepel a projektben. Az **OFFLINE\_\_SYNC ENABLED** szimbólum definiálásakor ezek a kódútvonalak szerepelnek a buildben. Windows-alkalmazások esetén telepítenie kell az SQLite platformot is.

1. A Visual Studio jobb gombjára kattintva kattintson a megoldásra > **NuGet csomagok kezelése a megoldáshoz...**, majd keresse meg és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet csomagot a megoldás összes projektjéhez.
2. A Megoldáskezelőben nyissa meg a TodoItemManager.cs fájlt a projektből a **Portable (Hordozható** osztálykönyvtár) projekt nevében, majd ne fűzzön megjegyzést a következő előfeldolgozó-direktívához:

        #define OFFLINE_SYNC_ENABLED
3. (Nem kötelező) A Windows-eszközök támogatásához telepítse az alábbi SQLite futásidejű csomagok egyikét:

   * **Windows 8.1 futásidejű:** Telepítse [az SQLite for Windows 8.1 rendszert.][3]
   * **Windows Phone 8.1:** Telepítse [az SQLite-ot A Windows Phone 8.1-re][4].
   * **Univerzális Windows-platform** Telepítse [az SQLite-ot az univerzális Windows Universal rendszerhez.][5]

     Bár a rövid útmutató nem tartalmaz Univerzális Windows-projektet, az Univerzális Windows platformot a Xamarin Forms támogatja.
4. (Nem kötelező) Minden Windows-alkalmazásprojektben kattintson a jobb gombbal **a Hivatkozások** > **hivatkozás hozzáadása...** elemre, **bontsa**ki a **Windows** mappa > Bővítmények .
    Engedélyezze a megfelelő **SQLite for Windows** SDK-t a **Visual C++ 2013 Runtime for Windows** SDK-val együtt.
    Az SQLite SDK nevek némileg eltérnek az egyes Windows platformoktól.

## <a name="review-the-client-sync-code"></a>Az ügyfél szinkronizálási kódjának áttekintése
Itt van egy rövid áttekintést, hogy mi `#if OFFLINE_SYNC_ENABLED` már szerepel a bemutató kódot az irányelvekben. A kapcsolat nélküli szinkronizálás funkció a Portable Class Library projekt TodoItemManager.cs projektfájljában található. A szolgáltatás fogalmi áttekintését az Offline adatok szinkronizálása az Azure Mobile Apps alkalmazásban című [témakörben találja.][2]

* Mielőtt bármilyen táblaműveletet végrehajtana, a helyi tárolót inicializálva kell végezni. A helyi tároló adatbázisa inicializálva van a **TodoItemManager** osztálykonstruktorban a következő kód használatával:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Ez a kód létrehoz egy új helyi SQLite adatbázist a **MobileServiceSQLiteStore** osztály használatával.

    A **DefineTable** metódus létrehoz egy táblát a helyi tárolóban, amely megfelel a megadott típusú mezőknek.  A típusnak nem kell tartalmaznia a távoli adatbázisban lévő összes oszlopot. Lehetőség van az oszlopok részhalmazának tárolására.
* A **TodoItemManager** **todoTable** mezője az **IMobileServiceTable**tábla helyett **IMobileServiceSyncTable** típusú. Ez az osztály a helyi adatbázist használja az összes létrehozási, olvasási, frissítési és törlési (CRUD) táblaművelethez. Az **IMobileServiceSyncContext** **pushasync** hívásával dönti el, hogy a módosítások mikor kerülnek a mobilalkalmazás-háttérszolgáltatásba. A szinkronizálási környezet segít megőrizni a táblakapcsolatokat azáltal, hogy nyomon követi és lenyomja az ügyfélalkalmazás által a **PushAsync** megküldésekor módosított összes tábla változásait.

    A következő **SyncAsync** metódus t hívják meg a mobilalkalmazás-háttér-háttér-lejátszási móddal való szinkronizáláshoz:

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

    Ez a minta egyszerű hibakezelést használ az alapértelmezett szinkronizáláskezelővel. Egy valódi alkalmazás kezeli a különböző hibákat, például a hálózati feltételeket és a kiszolgálóütközéseket egy egyéni **IMobileServiceSyncHandler** implementáció használatával.

## <a name="offline-sync-considerations"></a>Kapcsolat nélküli szinkronizálással kapcsolatos szempontok
A mintában a **SyncAsync** metódus csak indításkor és szinkronizálás kérésének időpontjában kerül meghívásra.  Ha szinkronizálást szeretne kezdeményezni egy Android vagy iOS-alkalmazásban, húzza le az elemek listájáról; windows os rendszerben használja a **Szinkronizálás** gombot. Egy valós alkalmazásban a szinkronizálási eseményindítót is elindíthatja, amikor a hálózati állapot megváltozik.

Ha egy lekéréses végrehajtása egy olyan táblához, amely a környezet által nyomon követett helyi frissítéseket hajt végre, a lekéréses művelet automatikusan elindítja az előző környezetleküldéses műveletet. A mintában szereplő elemek frissítése, hozzáadása és befejezése korkihagyhatja a explicit **PushAsync** hívást.

A megadott kódban a távoli TodoItem tábla összes rekordja lekérdeződik, de a rekordok szűrhetők is egy lekérdezésazonosító és egy lekérdezés átadásával a **PushAsync**számára. További információt az Azure Mobile Apps *növekményes szinkronizálása* [az offline adatszinkronizálásban című szakaszban talál.][2]

## <a name="run-the-client-app"></a>Az ügyfélalkalmazás futtatása
Ha az offline szinkronizálás most már engedélyezve van, futtassa az ügyfélalkalmazást legalább egyszer minden platformon a helyi tároló adatbázisának feltöltéséhez. Később szimuláljon egy offline forgatókönyvet, és módosítsa az adatokat a helyi tárolóban, amíg az alkalmazás offline állapotban van.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Az ügyfélalkalmazás szinkronizálási viselkedésének frissítése
Ebben a szakaszban módosítsa az ügyfélprojektet úgy, hogy egy offline forgatókönyvet szimuláljon egy érvénytelen alkalmazás URL-cím használatával a háttérrendszerhez. Másik lehetőségként kikapcsolhatja a hálózati kapcsolatokat, ha a készüléket "Repülőgép üzemmódba" helyezi.  Adatelemek hozzáadásakor vagy módosításakor ezek a módosítások a helyi tárolóban tárolódnak, de nem szinkronizálódnak a háttérrendszer-adattárba, amíg a kapcsolat helyre nem áll.

1. A Megoldáskezelőben nyissa meg a **Constants.cs** projektfájlt a `ApplicationURL` Portable projektből, és módosítsa az érvénytelen URL-címre való mutatás értékét:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Nyissa meg a TodoItemManager.cs fájlt a **Hordozható** projektből, majd adjon hozzá egy **fogást** az alap **Kivétel** osztályhoz a **próbálkozáshoz... catch** blokk **SyncAsync**. Ez **a fogási** blokk a kivételüzenetet írja a konzolnak, az alábbiak szerint:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Az ügyfélalkalmazás létrehozása és futtatása.  Adjon hozzá néhány új elemet. Figyelje meg, hogy egy kivétel van bejelentkezve a konzolminden szinkronizálási kísérlet a háttérrendszerrel. Ezek az új elemek csak a helyi tárolóban léteznek, amíg le nem lehet nyomni őket a mobil háttérprogramra. Az ügyfélalkalmazás úgy viselkedik, mintha a háttérrendszerhez csatlakozna, támogatva az összes létrehozási, olvasási, frissítési, törlési (CRUD) műveletet.
4. Zárja be az alkalmazást, és indítsa újra, és ellenőrizze, hogy az új onnan létrehozott elemek megmaradnak-e a helyi tárolóban.
5. (Nem kötelező) A Visual Studio segítségével tekintse meg az Azure SQL Database-tábla, hogy a háttér-adatbázis adatai nem változtak.

    A Visual Studio programban nyissa meg a **Server Explorer t**. Nyissa meg az adatbázist az **Azure**->**SQL-adatbázisokban.** Kattintson a jobb gombbal az adatbázisra, és válassza **a Megnyitás az SQL Server Object Explorer programban parancsot.** Most már tallózhat az SQL adatbázistábla és annak tartalmát.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Az ügyfélalkalmazás frissítése a mobilháttér-rendszer újracsatlakoztatásához
Ebben a szakaszban csatlakoztassa újra az alkalmazást a mobil háttérrendszerhez, amely szimulálja, hogy az alkalmazás online állapotba lép vissza. A frissítési kézmozdulat végrehajtásakor a rendszer szinkronizálja az adatokat a mobil háttérrendszerhez.

1. Nyissa meg újra Constants.cs. Javítsa `applicationURL` ki a megfelelő URL-címre mutat.
2. Építse újra és futtassa az ügyfélalkalmazást. Az alkalmazás az indítás után megkísérli a szinkronizálást a mobilalkalmazás háttérszámítógépével. Ellenőrizze, hogy nincs-e kivétel a hibakeresési konzolon.
3. (Nem kötelező) A frissített adatok megtekintése az SQL Server Object Explorer vagy a REST eszköz, például a Fiddler vagy [a Postman][6]segítségével. Figyelje meg, hogy az adatok szinkronizálva vannak a háttéradatbázis és a helyi tároló között.

    Figyelje meg, hogy az adatok szinkronizálva vannak az adatbázis és a helyi tároló között, és az alkalmazás leválasztása közben hozzáadott elemeket tartalmazzák.

## <a name="additional-resources"></a>További források
* [Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban][2]
* [Azure Mobile Apps .NET SDK HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
