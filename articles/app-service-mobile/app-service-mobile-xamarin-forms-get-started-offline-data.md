---
title: Offline szinkronizálás engedélyezése az Azure Mobile App (Xamarin.Forms) |} A Microsoft Docs
description: Ismerje meg az App Service Mobile Apps Xamarin.Forms-alkalmazását a cache és a szinkronizálási offline adatok használata
documentationcenter: xamarin
author: conceptdev
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: crdun
ms.openlocfilehash: 506c59ca24aeafbac59b1508bb78142051302765
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001837"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Xamarin.Forms-mobilalkalmazás offline szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja a kapcsolat nélküli szinkronizálás – a szolgáltatás az Azure Mobile Apps a xamarin.Forms-hoz. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, hogy kommunikáljanak egy mobilalkalmazást – megtekintését, hozzáadását és módosítását adatokat –, akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban vannak tárolva. Miután az eszköz újra online állapotba kerül, ezeket a módosításokat a távoli szolgáltatással vannak szinkronizálva.

Ebben az oktatóanyagban a Xamarin.Forms-megoldás a rövid útmutató alapján hoz létre, amikor befejezte az oktatóanyag [Create a Xamarin iOS-alkalmazás] Mobile Apps-alkalmazáshoz. A rövid útmutató megoldást a Xamarin.Forms támogatja az offline szinkronizálás, amely csupán engedélyeznie kell a kódot tartalmaz. Ebben az oktatóanyagban a rövid útmutató megoldást, az Azure Mobile Apps offline funkciók bekapcsolása frissítése. Azt is ki a kapcsolat nélküli tartozó kódot az alkalmazásban. Ha nem használja a letöltött gyorsútmutató-megoldás, hozzá kell adnia az adatok hozzáférési bővítmény csomagokat a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a][1].

A kapcsolat nélküli szinkronizálás – szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [Offline adatszinkronizálás az Azure Mobile Apps][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>A rövid útmutató megoldásban offline szinkronizálásának engedélyezése
A kapcsolat nélküli szinkronizálás – kódot tartalmazza a projekt használatával C# elő-feldolgozói irányelveknek. Ha a **OFFLINE\_SZINKRONIZÁLÁSI\_engedélyezve** szimbólum van definiálva, a kód az elérési utak a build szerepelnek. Windows-alkalmazások esetén is telepítenie kell az SQLite-platform.

1. A Visual Studióban kattintson a jobb gombbal a megoldás > **NuGet-csomagok kezelése megoldáshoz...** , majd keresse meg, és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** a megoldásban az összes projekt NuGet-csomagot.
2. A Megoldáskezelőben nyissa meg a TodoItemManager.cs fájlt a projektből az **hordozható** a neve, amely hordozható osztálytár projektet, majd állítsa vissza a következő elő-feldolgozói irányelv:

        #define OFFLINE_SYNC_ENABLED
3. (Nem kötelező) Támogatja a Windows-eszközöket, telepítse az SQLite következő futtatókörnyezetek közül:

   * **Windows 8.1-modul:** telepítése [for Windows 8.1 SQLite][3].
   * **Windows Phone 8.1:** telepítése [a Windows Phone 8.1-es SQLite][4].
   * **Univerzális Windows Platform** telepítése [az univerzális Windows Universal az SQLite][5].

     Bár ez a rövid útmutató nem tartalmaz egy univerzális Windows-projektet, a Universal Windows platform Xamarin Forms eszközkészlethez használata támogatott.
4. (Nem kötelező) Az egyes Windows-projektben kattintson a jobb gombbal **hivatkozások** > **hivatkozás hozzáadása...** , bontsa ki a **Windows** mappa > **bővítmények**.
    Engedélyezze a megfelelő **SQLite for Windows** az SDK a **Visual C++ 2013 modul a Windows** SDK-t.
    Az SQLite SDK nevei kissé eltérő lehet az egyes Windows-platform.

## <a name="review-the-client-sync-code"></a>Tekintse át az Ügyfélkód szinkronizálása
Íme, mi oktatóanyag kódot már szerepel egy rövid áttekintést a `#if OFFLINE_SYNC_ENABLED` irányelveknek. A kapcsolat nélküli szinkronizálás – funkciója a hordozható osztálytár projektek TodoItemManager.cs projekt fájlban. A szolgáltatás fogalmi áttekintése, lásd: [Offline adatszinkronizálás az Azure Mobile Apps][2].

* Bármely table műveletek végrehajtása előtt inicializálni kell a helyi tárolóban. A helyi tároló adatbázis inicializálása a **TodoItemManager** osztálykonstruktor az alábbi kód használatával:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Ez a kód létrehoz egy új helyi SQLite adatbázis használata a **MobileServiceSQLiteStore** osztály.

    A **DefineTable** metódus a helyi tároló, amely megfelel a megadott típus mezőinek egy táblát hoz létre.  A típus nem kell megadnia a távoli adatbázisban lévő összes oszlopot. Oszlopok tárolására is lehetőség.
* A **todoTable** mező **TodoItemManager** van egy **IMobileServiceSyncTable** helyett írja be **IMobileServiceTable**. A osztályt használ az összes helyi adatbázis létrehozása, olvasása, frissítése és törlési (CRUD) tábla műveletek. Ha ezek a módosítások meghívásával kerüljenek a Mobile Apps-háttéralkalmazás mellett dönt **PushAsync** a a **IMobileServiceSyncContext**. A szinkronizálási környezetet segít nyomon követésével és módosítások leküldése minden táblát egy ügyfélalkalmazás mikor módosult a táblák közötti kapcsolatok megőrzése **PushAsync** nevezzük.

    A következő **SyncAsync** módszert hívja meg a Mobile Apps-háttéralkalmazás szinkronizálni:

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

    Ez a minta az alapértelmezett szinkronizálási kezelővel rendelkező egyszerű hibakezelési használja. Egy valódi alkalmazás egyéni használatával szeretné kezelni a különféle hibák, például hálózati feltételek és a kiszolgáló ütközések **IMobileServiceSyncHandler** végrehajtására.

## <a name="offline-sync-considerations"></a>Kapcsolat nélküli szinkronizálás – szempontok
A mintában a **SyncAsync** módszert csak hívja meg az indítási és mikor van szükség a szinkronizálást.  Egy Android vagy iOS alkalmazásban szinkronizálást kezdeményezni, kérje le az elemek listájában; a Windows, használja a **szinkronizálási** gombra. Egy valós alkalmazás esetében meg is elhelyezheti a szinkronizálási eseményindító hálózati állapot.

Egy táblázaton, amely rendelkezik a függőben lévő lekérési végrehajtásakor helyi frissítések által nyomon követett kapcsolatban, hogy a pull művelet automatikusan eseményindítók egy előző környezet leküldéses. Frissítésekor, felvétele és az ebben a példában szereplő elemek befejezése, akkor kihagyhatja az explicit **PushAsync** hívja.

A megadott kód, a távoli TodoItem tábla összes rekordot a rendszer megkérdezi, de azt is lehetővé teszi a rekordok szűrése a lekérdezés azonosítóját átadásával és lekérdezése **PushAsync**. További információkért lásd: a szakasz *növekményes szinkronizálás* a [Offline adatszinkronizálás az Azure Mobile Apps][2].

## <a name="run-the-client-app"></a>Az ügyfélalkalmazás futtatása
A kapcsolat nélküli szinkronizálás most már engedélyezve van, az ügyfélalkalmazás legalább egyszer futtatott egyes platformokon a helyi tároló adatbázisának feltöltéséhez. Később szimulálása egy offline forgatókönyvet, és módosíthatja az adatokat, a helyi tároló, amikor az alkalmazás offline állapotban van.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Az ügyfélalkalmazás szinkronizálási viselkedésének módosítása
Ebben a szakaszban módosítsa az ügyfélprojekt szimulálása egy kapcsolat nélküli forgatókönyv biztosítani a háttérbeli érvénytelen az alkalmazás URL-címének használatával. Másik megoldásként kikapcsolhatja a hálózati kapcsolatok helyezi át az eszköz "Repülőgépes üzemmódból."  Hozzáadásakor, vagy módosítsa az elemeket, ezek a változások a helyi tárolóban tárolt, de nincs szinkronizálva a háttérbeli adattárba, amíg a kapcsolat helyreáll.

1. A megoldáskezelőben nyissa meg a Constants.cs projektfájlt a a **hordozható** projektre, és módosítsa az értéket a `ApplicationURL` átirányítása egy URL-cím érvénytelen:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Nyissa meg a TodoItemManager.cs fájlt a **hordozható** projektre, majd adjon hozzá egy **catch** az alap **kivétel** osztályt az a **próbálja... catch**blokkoló **SyncAsync**. Ez **catch** blokk ír a kivételre vonatkozó üzenet a konzolt, a következő:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Hozhat létre, és futtassa az ügyfélalkalmazást.  Adjon hozzá néhány új elemeket. Figyelje meg, hogy a konzol minden kísérlet szinkronizálni a háttér-kivétel kerül. Ezek az új elemek szerepel csak a helyi tároló mindaddig, amíg azokat a mobil háttérrendszer lehet leküldeni. Az ügyfélalkalmazás úgy viselkedik, háttér, támogató, minden létrehozási, olvasási, frissítési, törlési (CRUD) műveleteket van csatlakoztatva.
4. Zárja be az alkalmazást, és indítsa újra, hogy ellenőrizze, hogy a létrehozott új elemek, a helyi tárolóban tárolja.
5. (Nem kötelező) A Visual Studio segítségével megtekintheti az Azure SQL Database adatbázistábla megtekintéséhez, hogy a háttér adatbázis adatait nem változott.

    A Visual Studióban nyissa meg a **Server Explorer**. Keresse meg az adatbázist a **Azure**->**SQL-adatbázisok**. Kattintson a jobb gombbal az adatbázishoz, és válassza ki **nyissa meg az SQL Server Object Explorer**. Most megnyithatja az SQL database tábla és annak tartalmát.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>A mobil háttérszolgáltatásban újracsatlakoztatása ügyfélalkalmazás frissítése
Ebben a szakaszban kapcsolódjon újra az alkalmazást, hogy a mobil háttérszolgáltatás használatára, amely szimulálja az alkalmazást, és az online állapot vissza hamarosan. A frissítés kézmozdulat hajt végre, amikor adatok szinkronizálva van a mobil háttérszolgáltatás használatára.

1. Nyissa meg újra Constants.cs. Javítsa ki a `applicationURL` , a helyes URL-címre mutasson.
2. Építse újra, és futtassa az ügyfélalkalmazást. Az alkalmazás próbál szinkronizálni a mobil-háttéralkalmazás elindítása után. Győződjön meg arról, hogy nincs kivétel jelentkezett-e a hibakeresési konzolt.
3. (Nem kötelező) Az SQL Server Object Explorer vagy egy REST-eszköz, például a Fiddler használatával frissített adatok megtekintéséhez vagy [Postman][6]. Figyelje meg, a háttérbeli adatbázis és a helyi tároló szinkronizálta az adatokat.

    Figyelje meg, hogy az adatbázis és a helyi tároló között szinkronizálta az adatokat, és tartalmazza az alkalmazás leválasztott hozzáadott elemek.

## <a name="additional-resources"></a>További források
* [Offline adatszinkronizálás az Azure Mobile Appsban][2]
* [Az Azure Mobile Apps .NET SDK-útmutató][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
