---
title: Offline szinkronizálás engedélyezése az Azure Mobile alkalmazás (Android)
description: Ismerje meg, hogyan cache és a szinkronizálási offline adatok, az Android-alkalmazás az App Service Mobile Apps használatával
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
ms.openlocfilehash: a20c79acce8c9dc9051651a0473fd07b8e62f5de
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960345"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Az Android rendszerhez készült mobilalkalmazás offline szinkronizálásának engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja a kapcsolat nélküli szinkronizálás – a szolgáltatás az Azure Mobile Apps androidhoz. Kapcsolat nélküli szinkronizálás lehetővé teszi, hogy a végfelhasználók számára, hogy kommunikáljanak a mobilalkalmazás&mdash;megtekintését, hozzáadását és módosítását adatok&mdash;akkor is, ha nincs hálózati kapcsolat. Változások a helyi adatbázisban vannak tárolva. Az eszköz újra online állapotba kerül, ha ezeket a módosításokat a távoli háttérrendszerrel vannak szinkronizálva.

Ha ez az első élmény, az Azure Mobile Apps, az oktatóanyag első kell teljes [Android-alkalmazás létrehozása]. Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, hozzá kell adnia a data access bővítmény csomagokat a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

A kapcsolat nélküli szinkronizálás – szolgáltatással kapcsolatos további tudnivalókért lásd a témakör [Offline adatszinkronizálás az Azure Mobile Appsban].

## <a name="update-the-app-to-support-offline-sync"></a>Offline szinkronizálás támogatásához az alkalmazás frissítése
A kapcsolat nélküli szinkronizálás olvasási és írási az egy *szinkronizálási tábla* (használatával a *IMobileServiceSyncTable* interface), részét képezi, amely egy **SQLite** adatbázis az eszközön.

Lekéréses és küldéses az eszköz és az Azure Mobile Services között változik, használhat egy *szinkronizálási környezetet* (*MobileServiceClient.SyncContext*), amely a helyi adatbázis, tárolására a táblafiókhoz helyi adatok.

1. A `TodoActivity.java`, tegye megjegyzésbe a meglévő definíció a `mToDoTable` és vonja vissza a szinkronizálási tábla verziója:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. Az a `onCreate` metódus, tegye megjegyzésbe a meglévő inicializálása `mToDoTable` és vonja vissza a definíció:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. A `refreshItemsFromTable` definíciója megjegyzésbe `results` és vonja vissza a definíció:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Tegye megjegyzésbe definíciója `refreshItemsFromMobileServiceTable`.
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
Ebben a szakaszban tesztelni az alkalmazáson a Wi-Fi viselkedés, és kapcsolja ki a Wi-Fi-kapcsolat nélküli forgatókönyv létrehozásához.

Adatelemek hozzáadásakor meg azokat a helyi SQLite-tárolóban tárolt, de nincs szinkronizálva a mobilszolgáltatást, amíg lenyomja a **frissítése** gombra. Előfordulhat, hogy más alkalmazások különböző követelmények vonatkoznak, ha az adatok szinkronizálását igények tekintetében, de a bemutatóhoz ebben az oktatóanyagban igényelhetnek, a felhasználó rendelkezik.

Gomb megnyomásakor egy új háttérben futó feladat elindul. Először leküldi a helyi tároló használata a szinkronizálási környezetet, majd lekéri az összes módosított adatokat az Azure-ból a helyi táblába végzett összes módosítást.

### <a name="offline-testing"></a>Offline tesztelés
1. Helyezze az eszközt vagy szimulátort az *repülési üzemmód*. Ez létrehoz egy kapcsolat nélküli forgatókönyv.
2. Adjon hozzá néhány *ToDo* elemek vagy az egyes elemek befejezettként be van jelölve. Lépjen ki az eszközt vagy szimulátort (vagy a kényszerített zárja be az alkalmazást), majd indítsa újra. Győződjön meg arról, hogy a módosítások megőrizte az eszközön tartanak, mert a helyi SQLite-tároló.
3. Az Azure tartalmának megtekintése *TodoItem* például a tábla egy SQL-eszközt a *SQL Server Management Studio*, vagy a REST-ügyfél, például *Fiddler* vagy  *Postman*. Győződjön meg arról, hogy rendelkezik-e az új elemeket *nem* lett szinkronizálva a kiszolgálóhoz
   
       + Node.js-háttéralkalmazáshoz, nyissa meg a [az Azure portal](https://portal.azure.com/), és a háttérrendszer kattintson a Mobile Apps **könnyen kezelhető táblák** > **TodoItem** a tartalmánakmegtekintéséhez`TodoItem`tábla.
       + .NET-háttérrendszert, tekintse meg a tábla tartalmának, például egy SQL-lel vagy eszköz *SQL Server Management Studio*, vagy a REST-ügyfél, például *Fiddler* vagy *Postman*.
4. Wi-Fi kapcsolja be az eszközt vagy szimulátort. Nyomja meg a **frissítése** gombra.
5. Újra a TodoItem adatok megtekintése az Azure Portalon. Az új és módosított TodoItems meg kell jelennie.

## <a name="additional-resources"></a>További források
* [Offline adatszinkronizálás az Azure Mobile Appsban]
* [Cloud Cover: Offline szinkronizálás az Azure Mobile Services] \(Megjegyzés: a videó megtalálható-e a Mobile Services, de a kapcsolat nélküli szinkronizálás – az Azure Mobile Apps hasonló módon működik\)

<!-- URLs. -->

[Offline adatszinkronizálás az Azure Mobile Appsban]: app-service-mobile-offline-data-sync.md

[Android-alkalmazás létrehozása]: app-service-mobile-android-get-started.md

[Cloud Cover: Offline szinkronizálás az Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

