---
title: Offline szinkronizálás engedélyezése az Azure Mobile App (Xamarin Android)
description: Ismerje meg, hogyan App Service Mobile Apps segítségével a Xamarin Android-alkalmazás cache és a szinkronizálási kapcsolat nélküli adatokat
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
ms.openlocfilehash: ca0eaf9e47b88bc0df8e7f050d8558c23d884f78
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52999306"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>A Xamarin.Android-mobilalkalmazás offline szinkronizálásának engedélyezése

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban a kapcsolat nélküli szinkronizálás – a szolgáltatás az Azure Mobile Apps Xamarin.Android a vezet be. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, hogy kommunikáljanak egy mobilalkalmazást – megtekintését, hozzáadását és módosítását adatokat –, akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban vannak tárolva.
Miután az eszköz újra online állapotba kerül, ezeket a módosításokat a távoli szolgáltatással vannak szinkronizálva.

Ebben az oktatóanyagban az oktatóanyag az ügyfélprojekt frissítése [A Xamarin Android-alkalmazás létrehozása] , az Azure Mobile Apps offline funkciók támogatásához. Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, hozzá kell adnia a data access bővítmény csomagokat a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

A kapcsolat nélküli szinkronizálás – szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="update-the-client-app-to-support-offline-features"></a>Offline funkciók támogatásához az ügyféloldali alkalmazás frissítése

Az Azure Mobile Apps offline funkciói lehetővé teszik helyi adatbázis használhatja, amikor egy offline állapotban van. Ezek a funkciók használata az alkalmazásban, inicializálni egy [SyncContext] a helyi tárolóba. Ezután hivatkozhat a tábla keresztül a [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet) felületet. Az eszközön a helyi tároló SQLite használatban van.

1. A Visual Studióban nyissa meg a NuGet-Csomagkezelőt, hogy végrehajtotta a projektben a [A Xamarin Android-alkalmazás létrehozása] oktatóanyag.  Keresse meg és telepítse a **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet-csomagot.
2. Nyissa meg a ToDoActivity.cs fájlt, és vonja vissza a `#define OFFLINE_SYNC_ENABLED` definíciója.
3. A Visual Studióban nyomja le az **F5** kulcs újraépítéséhez és az ügyfélalkalmazás futtatása. Az alkalmazás ugyanúgy működik, az offline szinkronizálás engedélyezése előtti is. A helyi adatbázis azonban most van feltöltve adatokkal, amelyek offline állapotban is használható.

## <a name="update-sync"></a>A háttérrendszer bontsa a kapcsolatot az alkalmazás frissítése

Ebben a szakaszban megszakítja a kapcsolatot, a Mobile Apps egy offline helyzet szimulálásához. Amikor hozzáadja az elemeket, a kivételkezelő kiderül, hogy, hogy az alkalmazás offline módban van. Ebben az állapotban új elemeket hozzáadja a helyi tároló, és a egy leküldéses csatlakoztatott állapotban végrehajtásakor a mobilalkalmazás háttérrendszere szinkronizált.

1. A megosztott projekt ToDoActivity.cs szerkesztheti. Módosítsa a **applicationURL** átirányítása egy URL-cím érvénytelen:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Kapcsolat nélküli viselkedése is bemutatják, Wi-Fi és az eszközön mobilhálózaton letiltása, vagy repülési üzemmód használatával.
2. Nyomja meg **F5** össze és futtathatja az alkalmazást. Figyelje meg, hogy a szinkronizálás nem sikerült a frissítés után is, az alkalmazás indításakor.
3. Adja meg az új elemeket, és figyelje meg, hogy leküldéses sikertelen [CancelledByNetworkError] állapotú minden alkalommal, amikor kattint **mentése**. Azonban az új teendőket szerepel a helyi tároló mindaddig, amíg azok lehet leküldeni a mobil-háttéralkalmazást.  Egy éles alkalmazásban Ha akkor tiltja le ezeket a kivételeket az ügyfélalkalmazás viselkedik, mintha továbbra is csatlakozik a mobil-háttéralkalmazást.
4. Zárja be az alkalmazást, és indítsa újra, hogy ellenőrizze, hogy a létrehozott új elemek, a helyi tárolóban tárolja.
5. (Nem kötelező) A Visual Studióban nyissa meg a **Server Explorer**. Keresse meg az adatbázist a **Azure**->**SQL-adatbázisok**. Kattintson a jobb gombbal az adatbázishoz, és válassza ki **nyissa meg az SQL Server Object Explorer**. Most megnyithatja az SQL database tábla és annak tartalmát. Győződjön meg arról, hogy a háttér adatbázis adatait nem változott.
6. (Nem kötelező) Egy REST-eszközre, például a Fiddler vagy Postman használatával lekérdezheti a mobil háttérszolgáltatásban, a GET-lekérdezésekkel formájában `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>A Mobile Apps-háttéralkalmazást újra az alkalmazás frissítése

Ebben a szakaszban kapcsolódjon újra az alkalmazást, hogy a mobil-háttéralkalmazást. Az alkalmazás első futtatásakor a `OnCreate` eseménykezelő-hívások `OnRefreshItemsSelected`. Ez a metódus meghívja `SyncAsync` szinkronizálni a háttér-adatbázissal a helyi tárolóhoz.

1. Nyissa meg a megosztott projekt ToDoActivity.cs, és a módosítás a visszaállítás a **applicationURL** tulajdonság.
2. Nyomja le az **F5** kulcs újbóli létrehozása, és futtassa az alkalmazást. Az alkalmazás szinkronizálja a helyi módosításokat az Azure Mobile Apps-háttéralkalmazás segítségével leküldéses és lekéréses műveletek során a `OnRefreshItemsSelected` metódus hajtja végre.
3. (Nem kötelező) Tekintse meg a frissített adatokat az SQL Server Object Explorer vagy egy REST-eszköz, például a Fiddler használatával. Figyelje meg az adatok szinkronizálása az Azure Mobile Apps háttér adatbázis és a helyi tároló között.
4. Az alkalmazásban kattintson a néhány elemet a helyi tároló végrehajtásához melletti jelölőnégyzetet.

   `CheckItem` hívások `SyncAsync` való szinkronizálás minden elem a Mobile Apps-háttéralkalmazást. `SyncAsync` meghívja a lekérést és. **Minden alkalommal, amikor egy lekéréses elleni egy táblát, amely az ügyfél által végrehajtott módosítások a végrehajtása, egy leküldéses mindig futtatja automatikusan**. Ez biztosítja, hogy a helyi tároló kapcsolatok mellett minden tábla konzisztens marad. Egy váratlan leküldéses ezt a viselkedést okozhat. Ezt a viselkedést a további információkért lásd: [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="review-the-client-sync-code"></a>Tekintse át az Ügyfélkód szinkronizálása

A Xamarin ügyfél-projektet az oktatóanyag befejezésekor letöltött [A Xamarin Android-alkalmazás létrehozása] már tartalmaz egy helyi SQLite-adatbázis segítségével kapcsolat nélküli szinkronizálás támogató kódot. Íme, mi már szerepel az oktatóprogram kódmintája rövid áttekintése. A szolgáltatás fogalmi áttekintése, lásd: [Offline adatszinkronizálás az Azure Mobile Appsban].

* Bármely table műveletek végrehajtása előtt inicializálni kell a helyi tárolóban. A helyi tároló adatbázis inicializálása során `ToDoActivity.OnCreate()` végrehajtja `ToDoActivity.InitLocalStoreAsync()`. Ez a módszer létrehoz egy helyi SQLite adatbázis használata a `MobileServiceSQLiteStore` az Azure Mobile Apps-ügyfél SDK által biztosított osztály.

    A `DefineTable` módszer létrehoz egy táblázatot a helyi tároló, amely megfelel a megadott típus mezőinek `ToDoItem` ebben az esetben. A típus nem kell megadnia a távoli adatbázisban lévő összes oszlopot. Oszlopok egy része tárolására is lehetőség.

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
            // For more details, see https://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* A `toDoTable` tagja `ToDoActivity` azonban a `IMobileServiceSyncTable` helyett írja be `IMobileServiceTable`. A IMobileServiceSyncTable összes létrehozása, olvasása, frissítése és törlési (CRUD) tábla műveletek a helyi tároló adatbázis irányítja.

    Eldöntheti, hogy amikor módosításokat meghívásával kerüljenek az Azure Mobile Apps-háttéralkalmazás `IMobileServiceSyncContext.PushAsync()`. A szinkronizálási környezetet segít nyomon követésével és módosítások leküldése minden táblát egy ügyfélalkalmazás mikor módosult a táblák közötti kapcsolatok megőrzése `PushAsync` nevezzük.

    A megadott kód meghívja `ToDoActivity.SyncAsync()` szinkronizálásra, amikor a todoitem lista frissül, vagy a todoitem hozzáadásakor vagy befejeződött. A kód szinkronizálások minden helyi módosítás után.

    A megadott kód, mind a távoli rögzíti `TodoItem` tábla lesznek lekérdezve, de egyben lehetővé teszi a rekordok szűrése a lekérdezés azonosítóját átadásával és lekérdezése `PushAsync`. További információkért lásd: a szakasz *növekményes szinkronizálás* a [Offline adatszinkronizálás az Azure Mobile Appsban].

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

* [Offline adatszinkronizálás az Azure Mobile Appsban]
* [Az Azure Mobile Apps .NET SDK-útmutató][8]

<!-- URLs. -->
[A Xamarin Android-alkalmazás létrehozása]: ./app-service-mobile-xamarin-android-get-started.md
[Offline adatszinkronizálás az Azure Mobile Appsban]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[A Xamarin Android-alkalmazás létrehozása]: app-service-mobile-xamarin-android-get-started.md
[Offline adatszinkronizálás az Azure Mobile Appsban]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: http://xamarin.com/download
[Xamarin extension]: http://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
