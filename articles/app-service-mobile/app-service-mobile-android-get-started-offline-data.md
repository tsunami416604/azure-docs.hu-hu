---
title: Offline szinkronizálás engedélyezése (Android)
description: Ismerje meg, hogyan használhatja az App Service Mobile Apps alkalmazást az offline adatok gyorsítótárazására és szinkronizálására az Android-alkalmazásban.
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c215105af5fe1ef8056b0d816cf2c2a6b96f2038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461623"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Offline szinkronizálás engedélyezése Android-mobilalkalmazásában
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag az Android-alapú Azure Mobile Apps offline szinkronizálási funkciójával foglalkozik. Az offline szinkronizálás lehetővé teszi&mdash;a végfelhasználók számára,&mdash;hogy akkor is kommunikáljanak a mobilalkalmazásokkal, hogy megtekintsék, hozzáadják vagy módosítsák az adatokat, még akkor is, ha nincs hálózati kapcsolat. A módosításokat a helyi adatbázis tárolja. Ha az eszköz újra online állapotba kerül, ezek a módosítások szinkronizálódnak a távoli háttérrendszerrel.

Ha ez az első élmény az Azure Mobile Apps szolgáltatással kapcsolatban, először töltse ki az [Android-alkalmazás létrehozása című oktatóanyagot.] Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, hozzá kell adnia az adatelérési bővítménycsomagokat a projekthez. A kiszolgálóbővítmény-csomagokról további információt [a .NET háttérkiszolgáló SDK azure mobile alkalmazásokhoz szolgáltatása című témakörben talál.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

Az offline szinkronizálási funkcióról az Offline data Sync in Azure Mobile Apps című témakörben olvashat [bővebben.]

## <a name="update-the-app-to-support-offline-sync"></a>Az alkalmazás frissítése az offline szinkronizálás támogatásához
Az offline szinkronizálás, akkor olvasni, és írni egy *szinkronizálási tábla* (az *IMobileServiceSyncTable* felület), amely része a **SQLite** adatbázis a készüléken.

Az eszköz és az Azure Mobile Services közötti módosítások leküldése és lekérése egy *szinkronizálási környezetet* *(MobileServiceClient.SyncContext)* használ, amelyet a helyi adatbázissal inicializál az adatok helyi tárolásához.

1. A `TodoActivity.java`alkalmazásban fűzzön megjegyzést `mToDoTable` a szinkronizálási tábla verziójának meglévő definíciójához, és ne fűzzön megjegyzést a megjegyzésekhez:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. A `onCreate` módszer, megjegyzést a meglévő `mToDoTable` inicializálása és uncomment ezt a meghatározást:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. A `refreshItemsFromTable` megjegyzés ki `results` a meghatározása, és uncomment ezt a meghatározást:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Megjegyzés ki a `refreshItemsFromMobileServiceTable`meghatározása .
5. A következő definíciójának megjegyzésének fésületlensedése: `refreshItemsFromMobileServiceTableSyncTable`
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. A következő definíciójának megjegyzésének fésületlensedése: `sync`
   
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
Ebben a szakaszban tesztelje a viselkedést a WiFi be, majd kapcsolja ki a WiFi egy offline forgatókönyv létrehozásához.

Amikor adatelemeket ad hozzá, azok a helyi SQLite tárolóban tárolódnak, de nem szinkronizálódnak a mobilszolgáltatással, amíg meg nem nyomja a **Frissítés** gombot. Más alkalmazások eltérő követelményeket támasztanak azzal kapcsolatban, hogy mikor kell szinkronizálni az adatokat, de bemutató céljából ez az oktatóanyag kifejezetten kéri azt.

Amikor megnyomja ezt a gombot, új háttérfeladat indul. Először lekéri az összes módosítást a helyi tároló szinkronizálási környezetben, majd lekéri az összes módosított adatokat az Azure-ból a helyi táblába.

### <a name="offline-testing"></a>Offline tesztelés
1. Helyezze a készüléket vagy *szimulátort Repülőgép módba.* Ez létrehoz egy kapcsolat nélküli forgatókönyvet.
2. Adjon hozzá néhány *Teendő* elemet, vagy jelöljen meg néhány elemet készként. Lépjen ki az eszközből vagy szimulátorból (vagy erőszakkal zárja be az alkalmazást), és indítsa újra. Ellenőrizze, hogy a módosítások megmaradnak-e az eszközön, mert azok a helyi SQLite tárolóban vannak tárolva.
3. Az Azure *TodoItem* tábla tartalmának megtekintése sql eszközzel, például *SQL Server Management Studio*eszközzel, vagy rest-ügyféllel, például *Fiddler* vagy *Postman*rendszerrel. Annak ellenőrzése, hogy *not* az új elemek nincsenek-e szinkronizálva a kiszolgálóval
   
       + Node.js háttérrendszer esetén nyissa meg az [Azure Portalt,](https://portal.azure.com/)és a mobilalkalmazás háttérrendszerében kattintson az **Egyszerű táblák** > **todoItem** elemre a `TodoItem` tábla tartalmának megtekintéséhez.
       + .NET háttérrendszer esetén tekintse meg a tábla tartalmát egy SQL eszközzel, például *az SQL Server Management Studio*eszközzel, vagy egy REST-ügyféllel, például a *Fiddler* vagy *a Postman*.
4. Kapcsolja be a WiFi-t a készülékben vagy szimulátorban. Ezután nyomja meg a **Frissítés** gombot.
5. Tekintse meg újra a TodoItem-adatokat az Azure Portalon. Az új és módosított TodoItems most megjelenik.

## <a name="additional-resources"></a>További források
* [Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]
* [Felhőalapú borító: Offline szinkronizálás az Azure Mobile Servicesben] \(megjegyzés: a videó mobilszolgáltatásokon található, de az offline szinkronizálás hasonló módon működik az Azure Mobile Apps alkalmazásokban\)

<!-- URLs. -->

[Kapcsolat nélküli adatszinkronizálás az Azure Mobile Apps megoldásban]: app-service-mobile-offline-data-sync.md

[Android-alkalmazás létrehozása]: app-service-mobile-android-get-started.md

[Felhőtakaró: Offline szinkronizálás az Azure mobilszolgáltatásokban]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

