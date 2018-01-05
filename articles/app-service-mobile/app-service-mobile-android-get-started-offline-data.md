---
title: "Kapcsolat nélküli szinkronizálásának engedélyezése az Azure Mobile alkalmazások (Android)"
description: "App Service Mobile Apps az Android-alkalmazás gyorsítótárába, a szinkronizálási kapcsolat nélküli adatainak használata"
documentationcenter: android
author: conceptdev
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 152702bed0ea061c3cb86e2ff6f88bf204f9d243
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Androidos mobilalkalmazás kapcsolat nélküli szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag az Azure Mobile Apps a kapcsolat nélküli szinkronizálás szolgáltatása Android ismerteti. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára a mobilalkalmazás együttműködhet&mdash;megtekintését, hozzáadását és módosítását adatok&mdash;akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban tárolódnak. Az eszköz újra online állapotba kerül, ha ezek a változások szinkronizálása megtörtént-e a távoli háttér.

Ha ez az Azure Mobile Apps első élményét, először ki az oktatóanyag [Android-alkalmazás létrehozása]. Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, hozzá kell adnia a hozzáférési adatok bővítménycsomagok a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

A kapcsolat nélküli szinkronizálás szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [az Azure Mobile Apps Offline adatszinkronizálás].

## <a name="update-the-app-to-support-offline-sync"></a>Kapcsolat nélküli szinkronizálás támogatásához az alkalmazás frissítésére
Kapcsolat nélküli szinkronizálás, az olvasási és írási egy *szinkronizálási tábla* (használatával a *IMobileServiceSyncTable* interface), amely része egy **SQLite** adatbázis az eszközön.

Leküldéses és lekéréses módosítások között az eszköz és az Azure Mobile Services, használja a *szinkronizálási környezetet* (*MobileServiceClient.SyncContext*), amely a helyi adatbázisban tárolják a inicializálása helyi adatok.

1. A `TodoActivity.java`, a meglévő definíciót megjegyzésbe `mToDoTable` és állítsa vissza a szinkronizálási tábla verziója:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. Az a `onCreate` metódus, a meglévő inicializálása megjegyzésbe `mToDoTable` és törölje az ehhez a definícióhoz:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. A `refreshItemsFromTable` definíciója megjegyzésbe `results` és törölje az ehhez a definícióhoz:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Definíciója megjegyzésbe `refreshItemsFromMobileServiceTable`.
5. Állítsa vissza a definíciója `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Állítsa vissza a definíciója `sync`:
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Az alkalmazás tesztelése
Ebben a szakaszban a működését Wi-Fi tesztelje a, és kapcsolja ki a Wi-Fi egy kapcsolat nélküli forgatókönyv létrehozásához.

Adatelemek hozzáadásakor használatban a helyi tárolóból. SQLite, de nincs szinkronizálva a mobilszolgáltatást, amíg lenyomja az a **frissítése** gombra. Más alkalmazások előfordulhat, hogy amikor adatokat szinkronizálni kell különböző követelményeit, de bemutató céljára ebben az oktatóanyagban a felhasználónak explicit módon kérheti rendelkezik.

A gomb megnyomásakor új háttérfeladat kezdődik. Először a helyi tárolójába szinkronizálási környezetet, akkor minden ponttá módosult adatokat az Azure-ból a helyi táblába használatával végrehajtott valamennyi módosítást leküldéses értesítések.

### <a name="offline-testing"></a>Kapcsolat nélküli tesztelése
1. Helyezze el az eszköz vagy a szimulátor *repülési üzemmód*. Ezzel létrehoz egy kapcsolat nélküli forgatókönyv.
2. Adja hozzá az egyes *ToDo* elemeket, vagy be van jelölve befejezettként egyes elemek. Lépjen ki az eszköz vagy szimulátor (vagy a kényszerített zárja be az alkalmazást), és indítsa újra. Győződjön meg arról, hogy a módosítások maradnak az eszközön mert tárolják őket a helyi SQLite-tárolóban.
3. Az Azure tartalmának megtekintése *TodoItem* például a tábla vagy egy SQL eszközzel *SQL Server Management Studio*, vagy egy REST-ügyfél, például *Fiddler* vagy  *Postman*. Ellenőrizze, hogy rendelkezik-e az új elemek *nem* lett-e szinkronizálva a kiszolgálóhoz
   
       + Node.js-háttéralkalmazáshoz, látogasson el a [Azure-portálon](https://portal.azure.com/), és a háttérkiszolgáló kattintson a mobilalkalmazás **könnyen táblák** > **TodoItem** a tartalmánakmegtekintéséhez`TodoItem`tábla.
       + A .NET-háttérrendszer, tekintse meg a tábla tartalmát, vagy egy SQL eszközzel, mint *SQL Server Management Studio*, vagy egy REST-ügyfél, például *Fiddler* vagy *Postman*.
4. Kapcsolja be a Wi-Fi az eszköz vagy szimulátor. Nyomja meg a **frissítése** gombra.
5. A TodoItem adatainak megtekintéséhez újra az Azure-portálon. Az új és módosított TodoItems ekkor meg kell jelennie.

## <a name="additional-resources"></a>További források
* [az Azure Mobile Apps Offline adatszinkronizálás]
* [Felhő tartalma: Kapcsolat nélküli szinkronizálás az Azure Mobile Services] \(Megjegyzés: a videó megtalálható-e a Mobile Services, de a kapcsolat nélküli szinkronizálás az Azure Mobile Apps hasonló módon működik\)

<!-- URLs. -->

[az Azure Mobile Apps Offline adatszinkronizálás]: app-service-mobile-offline-data-sync.md

[Android-alkalmazás létrehozása]: app-service-mobile-android-get-started.md

[Felhő tartalma: Kapcsolat nélküli szinkronizálás az Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

