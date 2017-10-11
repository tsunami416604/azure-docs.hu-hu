---
title: "Az iOS-mobilalkalmazások kapcsolat nélküli szinkronizálás engedélyezése |} Microsoft Docs"
description: "Ismerje meg az iOS-alkalmazások az Azure App Service mobile apps szolgáltatásban való gyorsítótár és a szinkronizálási kapcsolat nélküli használata."
documentationcenter: ios
author: ggailey777
manager: syntaxc4
editor: 
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 44c0d26b2d7d28322d436d4bda319d728c31a635
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Az iOS-mobilalkalmazások kapcsolat nélküli szinkronizálás engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, kapcsolat nélküli szinkronizálás a iOS rendszerhez készült Azure App Service Mobile Apps szolgáltatásának. A kapcsolat nélküli szinkronizálja végfelhasználókkal kezelheti a mobilalkalmazások megtekintését, vegye fel, és módosíthatják az adatokat, akkor is, ha nincs hálózati kapcsolat van. Változások a helyi adatbázisban tárolódnak. Miután az eszköz újra online állapotba kerül, a változások szinkronizálása megtörtént-e a távoli háttér.

Ha ez a Mobile Apps első élményét, először ki az oktatóanyag [iOS-alkalmazás létrehozása]. Ha nem használja a letöltött gyors üzembe helyezési kiszolgálóprojektet, hozzá kell adnia a adatelérési bővítménycsomagok a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

A kapcsolat nélküli szinkronizálás szolgáltatással kapcsolatos további tudnivalókért lásd: [Mobile Apps az Offline adatszinkronizálás].

## <a name="review-sync"></a>Tekintse át az Ügyfélkód szinkronizálása
A letöltött ügyfélprojekt a [iOS-alkalmazás létrehozása] oktatóanyag már kódot tartalmaz, amely támogatja a helyi adatok Core-alapú adatbázis kapcsolat nélküli szinkronizálás. Ez a szakasz foglalja az oktatóanyag kódot már tartalmát. A szolgáltatás elméleti áttekintését lásd: [Mobile Apps az Offline adatszinkronizálás].

Mobile Apps az offline adatszinkronizálás szolgáltatásával, végfelhasználók használhatják a helyi adatbázis akkor is, ha a hálózat nem érhető el. Ezeket a szolgáltatásokat az alkalmazás használatához inicializálni a szinkronizálási kontextusában `MSClient` , és a helyi tárolójába hivatkozik. Ezt követően a tábla használatával hivatkozik a **MSSyncTable** felületet.

A **QSTodoService.m** (Objective-C) vagy **ToDoTableViewController.swift** (Swift), figyelje meg, hogy a tag típusával **syncTable** van **MSSyncTable** . A szinkronizálási tábla felületet helyett használja a kapcsolat nélküli szinkronizálás **MSTable**. Szinkronizálási tábla használata esetén az összes művelet nyissa meg a helyi tárolójába, és csak a távoli háttérrendszerének integrációját explicit eseménylekérési és eseményküldési műveletek szinkronizálva.

 A szinkronizálási hivatkozás beszerzéséhez használja a **syncTableWithName** metódusa `MSClient`. Kapcsolat nélküli szinkronizálásának eltávolításához használja **tableWithName** helyette.

Mielőtt bármely tábla művelet végrehajtható, a helyi tárolójába inicializálni kell. A megfelelő kód itt látható:

* **Objective-C**. Az a **QSTodoService.init** módszert:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **SWIFT**. Az a **ToDoTableViewController.viewDidLoad** módszert:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Ez a módszer használatával hozza létre a helyi tárolójába a `MSCoreDataStore` felület, amely a Mobile Apps SDK biztosít. Azt is megteheti, megadhat egy másik helyi tároló alkalmazásával a `MSSyncContextDataSource` protokoll. Az is, az első paraméter **MSSyncContext** ütközés kezelő megadására szolgál. Mivel jelenleg átadott `nil`, azt lekérése sikertelen ütközés alapértelmezett ütközés kezelő.

Most tegyük a tényleges szinkronizálási művelet végrehajtására, és adatokat lekérni a távoli háttér:

* **Objective-C**. `syncData`először leküldéses értesítések a módosításokat, és ekkor meghívja a **pullData** a távoli háttérből adatok eléréséhez. Viszont a **pullData** metódus lekéri az új lekérdezés adatokat:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **SWIFT**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

Az Objective-C verzióban a `syncData`, első hívás **pushWithCompletion** sync-környezetében. Ez a módszer egy tagja `MSSyncContext` (és nem magára szinkronizálási táblázat), mert azt módosítások leküldéses értesítések összes táblák között. Csak azt jelzi, hogy helyi (CUD műveletek) révén valamilyen módon módosítva lett a kiszolgálón kerülnek. Ezután a segítő **pullData** neve, mely hívások **MSSyncTable.pullWithQuery** távoli adatok lekéréséhez és a helyi adatbázisban tárolja.

A Swift verziójában a leküldéses művelet volt nem feltétlenül szükséges, mert nincs nincs hívása **pushWithCompletion**. Ha a szinkronizálás a környezetben a következő táblázatban, amely egy leküldéses művelet függőben lévő módosításokat, lekéréses mindig kibocsát egy leküldéses először. Azonban ha több szinkronizálási tábla, célszerű explicit módon hívja az leküldéses annak biztosításához, hogy minden konzisztens kapcsolódó táblák között.

Az Objective-C és Swift verziók, használhatja a **pullWithQuery** adjon meg egy lekérdezést a rekordok szűrése a keresett metódust. Ebben a példában a lekérdezés lekéri a távoli minden rekordot `TodoItem` tábla.

A második paramétere **pullWithQuery** használt lekérdezés azonosító *növekményes szinkronizálás*. A növekményes szinkronizálás csak azt jelzi, hogy módosultak a rekord, a legutóbbi szinkronizálás óta lekéri `UpdatedAt` időbélyegzője (nevű `updatedAt` a helyi tárolóban levő.) A lekérdezés Azonosítóját, amely egyedi az alkalmazás minden logikai lekérdezés leíró karakterláncnak kell lennie. A növekményes szinkronizálás megadásának lehetőségét, adja át `nil` , a lekérdezés azonosítóját. Ezt a módszert potenciálisan nem hatékony, lehet, mert az összes rekord lévő összes lekéréses művelet lekérdezi.

Az Objective-C alkalmazás szinkronizálásának módosítása vagy hozzáadása adatokat, amikor egy felhasználó a frissítési kézmozdulat végez, és indítsa el.

A Swift app szinkronizálja, amikor a felhasználó hajt végre a frissítési hitelesítési mód, és indítsa el a.

Mivel az alkalmazás szinkronizálások mindig, amikor adatokat módosítva (Objective-C), vagy amikor az alkalmazás indítása (Objective-C és Swift), az alkalmazás azt feltételezi, hogy a felhasználó online. Egy későbbi szakasz ismerteti hogy frissíteni fogja az alkalmazást úgy, hogy a felhasználók akkor is, ha a kapcsolat nélküli szerkesztheti.

## <a name="review-core-data"></a>Tekintse át az alapvető adatokat az adatmodellbe
Az alapvető offline adattár használata esetén meg kell adnia adott táblákat és a mezőket az adatmodellben. A mintaalkalmazás már tartalmazza a megfelelő formátumban az adatmodellt. Ez a szakasz azt bízná ezek a táblázatok használatával megjelenítendő is.

Nyissa meg **QSDataModel.xcdatamodeld**. Négy táblázatokat--három, amelyeket az SDK-t használnak, a másik a tennivaló használt maguk elemei:
  * MS_TableOperations: Nyomon követi a kiszolgálóval való szinkronizálásra elemeket.
  * MS_TableOperationErrors: Követi nyomon, hogy olyan hibákat, amelyek a kapcsolat nélküli szinkronizálás során kerül sor.
  * MS_TableConfig: Nyomon követi az utolsó az idő az utolsó szinkronizálás művelet összes lekéréses műveletek frissítése.
  * TodoItem: Tárolja a Tennivalólista elemein. A rendszer oszlopok **createdAt**, **updatedAt**, és **verzió** választható rendszer tulajdonságai vannak.

> [!NOTE]
> A Mobile Apps SDK fenntartja az oszlop neve kezdődik "**``**". A rendszer oszlopok csakis ne használja ezt az előtagot. Ellenkező esetben a nevének módosítva lett a távoli háttér használatakor.
>
>

A kapcsolat nélküli szinkronizálás szolgáltatás használatakor a három rendszertáblák és a tábla megadása.

### <a name="system-tables"></a>Rendszertáblák

**MS_TableOperations**  

![MS_TableOperations tábla attribútumai][defining-core-data-tableoperations-entity]

| Attribútum | Típus |
| --- | --- |
| id | 64 egész szám |
| itemId | Karakterlánc |
| properties | Bináris adatok |
| Tábla | Karakterlánc |
| tableKind | 16 egész szám |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors tábla attribútumai][defining-core-data-tableoperationerrors-entity]

| Attribútum | Típus |
| --- | --- |
| id |Karakterlánc |
| OperationID azonosítójú |64 egész szám |
| properties |Bináris adatok |
| tableKind |16 egész szám |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Attribútum | Típus |
| --- | --- |
| id |Karakterlánc |
| kulcs |Karakterlánc |
| keyType |64 egész szám |
| Tábla |Karakterlánc |
| érték |Karakterlánc |

### <a name="data-table"></a>Adattábla

**TodoItem**

| Attribútum | Típus | Megjegyzés |
| --- | --- | --- |
| id | Karakterlánc, kötelezőként megjelölt |elsődleges távoli tárolóban levő kulccsal. |
| Végezze el | Logikai érték | Tennivalók elem mező |
| Szöveg |Karakterlánc |Tennivalók elem mező |
| createdAt | Dátum | (választható) Leképezve **createdAt** rendszer tulajdonság |
| updatedAt | Dátum | (választható) Leképezve **updatedAt** rendszer tulajdonság |
| Verzió | Karakterlánc | (választható) Ütközések, verzióra maps érzékeli |

## <a name="setup-sync"></a>Az alkalmazás szinkronizálási viselkedésének módosítása
Ebben a szakaszban módosítani az alkalmazásnak, hogy nincs szinkronban az alkalmazás indítása vagy beszúrása, és ha a elemek frissítését. A szinkronizált csak akkor, ha a frissítés kézmozdulat gombra történik.

**Objective-C**:

1. A **QSTodoListViewController.m**, módosítsa a **viewDidLoad** metódus hívása eltávolítása `[self refresh]` metódus végén. Az adatok a kiszolgálóval, az alkalmazás indítása most nincs szinkronizálva. Ehelyett azt szinkronizálva van a helyi tárolójába tartalmát.
2. A **QSTodoService.m**, módosítsa a meghatározása `addItem` , hogy azt nem szinkronizálása után a cikk csatlakoztatva van. Távolítsa el a `self syncData` letiltása, és cserélje le a következő:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Módosítsa a meghatározása `completeItem` amint azt korábban említettük. A Tiltás feloldásához a `self syncData` , és cserélje le a következőre:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**SWIFT**:

A `viewDidLoad`, a **ToDoTableViewController.swift**, az alkalmazás indítása a szinkronizálás befejeződése látható itt, két sort megjegyzésbe. A cikk írásának időpontjában a Swift teendőlista alkalmazás nem frissíti a szolgáltatás valaki ad hozzá, vagy egy elem befejeződött. Frissíti a szolgáltatást csak az alkalmazás indítása.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Az alkalmazás tesztelése
Ebben a szakaszban csatlakozhat egy érvénytelen URL-CÍMÉT egy kapcsolat nélküli forgatókönyv szimulálásához. Amikor adatelemek, azok van használatban a helyi alapvető adatok tárolására, de azok még nincs szinkronizálva a mobilalkalmazás háttérrendszer működésében.

1. Módosítsa a mobilalkalmazás URL-címet a **QSTodoService.m** egy érvénytelen URL-címet, és futtassa ismét az alkalmazást:

   **Objective-C**. A QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **SWIFT**. A ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Adja hozzá az egyes Tennivalólista elemein. Lépjen ki a szimulátor (vagy kényszerített zárja be az alkalmazást), majd indítsa újra. Győződjön meg arról, hogy a változtatások megmaradnak.

3. A távoli tartalmának megtekintése **TodoItem** tábla:
   * A Node.js háttérből, látogasson el a [Azure-portálon](https://portal.azure.com/) és kattintson a mobilalkalmazás háttér **könnyen táblák** > **TodoItem**.  
   * A .NET vissza az end, egy SQL-eszközt, például SQL Server Management Studio alkalmazást, vagy a többi ügyfél, például a Postman vagy a Fiddler használatával.  

4. Ellenőrizze, hogy rendelkezik-e az új elemek *nem* lett-e szinkronizálva a kiszolgálóval.

5. Az URL-CÍMÉT állítsa vissza a megfelelőre **QSTodoService.m**, és futtassa újra az alkalmazást.

6. Hajtsa végre a frissítési kézmozdulat modulba húzza le az elemeket.  
A folyamatban lévő léptető jelenik meg.

7. Nézet a **TodoItem** újra adatokat. Az új és módosított Tennivalólista elemein meg kell jelennie.

## <a name="summary"></a>Összefoglalás
A kapcsolat nélküli szinkronizálás szolgáltatást támogató használtuk az `MSSyncTable` felületet, és inicializálva `MSClient.syncContext` a helyi tárolójába. Ebben az esetben a helyi tárolójába lett egy alapvető adatok-alapú adatbázist.

A Core helyi tárolóban használatakor meg kell adnia a több táblákban a [javítsa ki a rendszer tulajdonságai](#review-core-data).

A normál létrehozása, olvasása, frissítése és törlése (CRUD) típusú műveletek mobilalkalmazások együttműködnek a szerint, ha az alkalmazás még a kapcsolat, de a műveletek fordulhat elő, a helyi tárolóban.

Ha a helyi tárolójába szinkronizálja azt a kiszolgálóval, használtuk az **MSSyncTable.pullWithQuery** metódust.

## <a name="additional-resources"></a>További források
* [Mobile Apps az Offline adatszinkronizálás]
* [Felhő tartalma: Kapcsolat nélküli szinkronizálás az Azure Mobile Services] \(a videó kapcsolatos Mobile Services, de a Mobile Apps kapcsolat nélküli szinkronizálás működésének hasonló módon.\)

<!-- URLs. -->


[iOS-alkalmazás létrehozása]: app-service-mobile-ios-get-started.md
[Mobile Apps az Offline adatszinkronizálás]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Felhő tartalma: Kapcsolat nélküli szinkronizálás az Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
