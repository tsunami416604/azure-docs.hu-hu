---
title: Offline szinkronizálás engedélyezése (iOS)
description: Ismerje meg, hogyan használhatja az Azure App Service mobilalkalmazásait az offline adatok gyorsítótárazására és szinkronizálására az iOS-alkalmazásokban.
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: d943213814b999f101a541abb0195a9fdd5a7423
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459174"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Offline szinkronizálás engedélyezése az iOS mobilalkalmazásokkal
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag az iOS-es Azure App Service mobilalkalmazás-szolgáltatásával való offline szinkronizálást ismerteti. Az offline szinkronizálással a végfelhasználók akkor is kommunikálhatnak a mobilalkalmazással, hogy megtekinthessék, hozzáadhassák vagy módosíthassák az adatokat, még akkor is, ha nincs hálózati kapcsolatuk. A módosításokat a helyi adatbázis tárolja. Miután az eszköz újra online állapotba került, a módosítások szinkronizálódnak a távoli háttérrendszerrel.

Ha ez az első élménya a mobilalkalmazásokkal kapcsolatban, először töltse ki az [iOS-alkalmazás létrehozása]című oktatóanyagot. Ha nem használja a letöltött gyorsindítási kiszolgálóprojektet, hozzá kell adnia az adatelérési bővítménycsomagokat a projekthez. A kiszolgálóbővítmény-csomagokról további információt [a .NET háttérkiszolgáló SDK azure mobile alkalmazásokhoz szolgáltatása című témakörben talál.](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

Az offline szinkronizálási funkcióról az [Offline adatszinkronizálás a mobilalkalmazásokban]című témakörben olvashat bővebben.

## <a name="review-the-client-sync-code"></a><a name="review-sync"></a>Az ügyfél szinkronizálási kódjának áttekintése
Az [iOS-alkalmazás oktatóanyagának] létrehozása során letöltött ügyfélprojekt már tartalmaz olyan kódot, amely támogatja az offline szinkronizálást egy helyi core data-alapú adatbázis használatával. Ez a szakasz összefoglalja, hogy mi már szerepel a bemutató kódot. A szolgáltatás fogalmi áttekintését az [Offline adatszinkronizálás a mobilalkalmazásokban]című témakörben találja.

A mobilalkalmazások offline adatszinkronizálási funkciójának használatával a végfelhasználók akkor is kezelhetik a helyi adatbázist, ha a hálózat nem érhető el. Ha használni szeretné ezeket a funkciókat az `MSClient` alkalmazásban, inicializálja a helyi áruház szinkronizálási környezetét, és hivatkozik rá. Ezután az **MSSyncTable** felületen keresztül hivatkozhat a táblára.

A **QSTodoService.m** (Objective-C) vagy **A ToDoTableViewController.swift** (Swift) mezőben figyelje meg, hogy a tag **syncTable** típusa **MSSyncTable**. A kapcsolat nélküli szinkronizálás ezt a szinkronizálási táblafelületet használja az **MSTable**helyett. Szinkronizálási tábla használatesetén az összes művelet a helyi tárolóba kerül, és csak a távoli háttérrendszerrel lesz szinkronizálva explicit leküldéses és lekéréses műveletekkel.

 Ha egy szinkronizálási táblára szeretne hivatkozni, használja `MSClient`a **syncTableWithName** metódust a alkalmazásban. A kapcsolat nélküli szinkronizálási funkció eltávolításához használja inkább **a tableWithName** parancsot.

Mielőtt bármilyen táblaműveletet végrehajtana, a helyi tárolót inicializálva kell végezni. Itt van a vonatkozó kód:

* **C célkitűzés.** A **QSTodoService.init** metódusban:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift, de nem tudom, mit**mondja A **ToDoTableViewController.viewDidLoad** metódusban:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Ez a módszer létrehoz egy `MSCoreDataStore` helyi tárolót a mobilalkalmazások SDK által biztosított felület használatával. Másik lehetőségként a `MSSyncContextDataSource` protokoll megvalósításával másik helyi tárolót is megadhat. Emellett az **MSSyncContext** első paramétere ütközéskezelő megadására is szolgál. Mivel már `nil`át , megkapjuk az alapértelmezett ütközéskezelő, amely nem minden ütközés.

Most hajtsuk végre a tényleges szinkronizálási műveletet, és szerezzünk adatokat a távoli háttérrendszerről:

* **C célkitűzés.** `syncData`először lekéri az új módosításokat, majd meghívja a **pullData-t,** hogy adatokat kapjon a távoli háttérrendszerből. A **pullData** metódus viszont olyan új adatokat kap, amelyek megfelelnek a lekérdezésnek:

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
* **Gyors**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc. via the MSSyncContextDelegate
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

Az Objective-C verzióban `syncData`a alkalmazásban először hívjuk meg a **pushWithCompletion-t** a szinkronizálási környezetben. Ez a módszer `MSSyncContext` tagja (és nem maga a szinkronizálási tábla), mert leküldéses változások az összes tábla között. A rendszer csak azokat a rekordokat küldi el a kiszolgálónak, amelyek valamilyen módon helyileg (CUD-műveletek révén) lettek módosítva. Ezután a helper **pullData** hívják, amely meghívja **MSSyncTable.pullWithQuery** letölteni a távoli adatokat, és tárolja a helyi adatbázisban.

A Swift verzióban, mivel a leküldéses művelet nem volt feltétlenül szükséges, nincs hívás **pushWithCompletion**. Ha vannak olyan módosítások függőben lévő a szinkronizálási környezetben a tábla, amely egy leküldéses műveletet, lekéréses mindig kiadja a leküldéses először. Ha azonban egynél több szinkronizálási táblával rendelkezik, a legjobb, ha explicit módon hívja meg a leküldéses leküldéses leküldéses, győződjön meg arról, hogy minden konzisztens a kapcsolódó táblák között.

Az Objective-C és a Swift verzióban a **pullWithQuery** metódussal is megadhat egy lekérdezést a beolvasni kívánt rekordok szűréséhez. Ebben a példában a lekérdezés a `TodoItem` távoli tábla összes rekordját beolvassa.

A **pullWithQuery** második paramétere egy lekérdezésazonosító, amely a *növekményes szinkronizáláshoz*használatos. A növekményes szinkronizálás csak azokat a rekordokat olvassa be, amelyeket az utolsó szinkronizálás óta módosítottak a rekord időbélyegzőjével `UpdatedAt` (amelyet a helyi tárolóban hívtak `updatedAt` meg).) A lekérdezésazonosítónak olyan leíró karakterláncnak kell lennie, amely az alkalmazás minden logikai lekérdezéséhez egyedi. A növekményes `nil` szinkronizálás letiltásához adja át a lekérdezésazonosítót. Ez a megközelítés potenciálisan nem hatékony, mert lekéri az egyes lekéréses műveletek összes rekordját.

Az Objective-C alkalmazás szinkronizál, amikor módosítja vagy hozzáadja az adatokat, amikor a felhasználó végrehajtja a frissítési kézmozdulatot, és indításkor.

A Swift alkalmazás akkor szinkronizál, amikor a felhasználó végrehajtja a frissítési kézmozdulatot és elindítja.

Mivel az alkalmazás szinkronizálja az adatok módosítását (C célkitűzés) vagy az alkalmazás indításakor (C célkitűzés és Swift), az alkalmazás feltételezi, hogy a felhasználó online állapotban van. Egy későbbi szakaszban frissíteni fogja az alkalmazást, hogy a felhasználók akkor is szerkeszthessék őket, ha offline állapotban vannak.

## <a name="review-the-core-data-model"></a><a name="review-core-data"></a>Az alapadat-modell áttekintése
Az Alapadatok kapcsolat nélküli tároló használatakor meg kell határoznia az adatmodellben meghatározott táblákat és mezőket. A mintaalkalmazás már tartalmaz egy megfelelő formátumú adatmodellt. Ebben a részben végighaladunk ezeken a táblázatokon, hogy megmutassuk, hogyan használják őket.

Nyissa meg **a QSDataModel.xcdatamodeld parancsot.** Négy tábla van definiálva - három, amelyeket az SDK használ, és egy, amelyet maguk a tennivalók használnak:
  * MS_TableOperations: Nyomon követi azokat az elemeket, amelyeket szinkronizálni kell a kiszolgálóval.
  * MS_TableOperationErrors: Nyomon követi az offline szinkronizálás során előforduló hibákat.
  * MS_TableConfig: Az összes lekéréses művelet utolsó szinkronizálási műveletének utolsó frissítési idejét követi nyomon.
  * TodoItem: Tárolja a tennivalókat. A **létrehozott rendszeroszlopokAt**, **updatedAt**és **verzió** választható rendszertulajdonságok.

> [!NOTE]
> A Mobile Apps SDK olyan oszlopneveket foglal le, amelyek " "-val**``** kezdődnek. Ne használja ezt az előtagot más, mint rendszeroszlopokkal. Ellenkező esetben az oszlopnevek módosulnak a távoli háttérrendszer használatakor.
>
>

Az offline szinkronizálási szolgáltatás használatakor adja meg a három rendszertáblát és az adattáblát.

### <a name="system-tables"></a>Rendszertáblák

**MS_TableOperations**  

![MS_TableOperations tábla attribútumai][defining-core-data-tableoperations-entity]

| Attribútum | Típus |
| --- | --- |
| id | Egész 64 |
| Itemid | Sztring |
| properties | Bináris adatok |
| tábla | Sztring |
| tableKind | Egész 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors tábla attribútumai][defining-core-data-tableoperationerrors-entity]

| Attribútum | Típus |
| --- | --- |
| id |Sztring |
| operationId |Egész 64 |
| properties |Bináris adatok |
| tableKind |Egész 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Attribútum | Típus |
| --- | --- |
| id |Sztring |
| kulcs |Sztring |
| kulcstípusa |Egész 64 |
| tábla |Sztring |
| érték |Sztring |

### <a name="data-table"></a>Adattábla

**TodoItem (Címelem)**

| Attribútum | Típus | Megjegyzés |
| --- | --- | --- |
| id | Kötelezőként megjelölt karakterlánc |Elsődleges kulcs a távoli tárolóban |
| Teljes | Logikai | Tennivaló cikk mezője |
| szöveg |Sztring |Tennivaló cikk mezője |
| createdAt | Dátum | (nem kötelező) Térképek a **createdAt** rendszertulajdonsághoz |
| frissítve | Dátum | (nem kötelező) Térképek a **updatedAt** rendszertulajdonsághoz |
| version | Sztring | (nem kötelező) Az ütközések észlelésére szolgál, verzióra leképezve |

## <a name="change-the-sync-behavior-of-the-app"></a><a name="setup-sync"></a>Az alkalmazás szinkronizálási viselkedésének módosítása
Ebben a szakaszban úgy módosíthatja az alkalmazást, hogy ne szinkronizálódjon az alkalmazás indításakor, illetve az elemek beszúrásakor és frissítésekor. Csak akkor szinkronizál, ha a frissítési kézmozdulat gombot végrehajtja.

**C célkitűzés**:

1. A **QSTodoListViewController.m,** változtassa meg a **viewDidLoad** metódust, hogy `[self refresh]` távolítsa el a hívást a végén a metódus. Most az adatok nincsenek szinkronizálva a kiszolgálóval az alkalmazás indításakor. Ehelyett a helyi tároló tartalmával van szinkronizálva.
2. A **QSTodoService.m**fájlban `addItem` módosítsa a definícióját úgy, hogy az ne szinkronizálódjon az elem beszúrása után. Távolítsa `self syncData` el a blokkot, és cserélje ki a következőkre:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Módosítsa a `completeItem` korábban említett definícióját. Távolítsa el `self syncData` a blokkot, és cserélje ki a következőkre:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Gyors**:

A `viewDidLoad`, a **ToDoTableViewController.swift**, megjegyzést ki a két sor itt látható, hogy hagyja abba a szinkronizálást az alkalmazás indításakor. Az írás időpontjában a Swift Todo alkalmazás nem frissíti a szolgáltatást, ha valaki hozzáad vagy kivégez egy elemet. Csak az alkalmazás indításakor frissíti a szolgáltatást.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-the-app"></a><a name="test-app"></a>Az alkalmazás tesztelése
Ebben a szakaszban egy érvénytelen URL-címhez csatlakozik egy offline forgatókönyv szimulálásához. Amikor adatelemeket ad hozzá, azok a helyi alapadat-tárolóban tárolódnak, de nincsenek szinkronizálva a mobilalkalmazás háttérrendszerével.

1. Módosítsa a mobilalkalmazás URL-címét a **QSTodoService.m-ben** érvénytelen URL-re, és futtassa újra az alkalmazást:

   **C célkitűzés.** A Következő ben: QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift, de nem tudom, mit**mondja A ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Adjon hozzá néhány tennivalót. Lépjen ki a szimulátorból (vagy zárja be erőszakkal az alkalmazást), majd indítsa újra. Ellenőrizze, hogy a módosítások továbbra is fennállnak-e.

3. A távoli **TodoItem** tábla tartalmának megtekintése:
   * Node.js háttértartalék esetén nyissa meg az [Azure Portalt,](https://portal.azure.com/) és a mobilalkalmazás háttér-tartalékában kattintson **az Egyszerű táblák** > **teendőgombra.**  
   * A .NET háttérrendszerhez használjon SQL-eszközt, például az SQL Server Management Studio vagy a REST-ügyfelet, például a Fiddler vagy a Postman alkalmazást.  

4. Ellenőrizze, hogy az *not* új elemek nincsenek-e szinkronizálva a kiszolgálóval.

5. Módosítsa vissza az URL-címet a megfelelő url-címre a **QSTodoService.m-ben,** és futtassa újra az alkalmazást.

6. Hajtsa végre a frissítési kézmozdulatot az elemek listájának lehúzásával.  
Megjelenik egy folyamatjelző.It is progress spinner is displayed.

7. Tekintse meg újra a **TodoItem** adatokat. Az új és módosított teendők most megjelennek.

## <a name="summary"></a>Összefoglalás
Az offline szinkronizálási funkció támogatásához a `MSSyncTable` `MSClient.syncContext` felületet használtuk, és egy helyi üzlettel inicializáltuk. Ebben az esetben a helyi tároló egy core data-alapú adatbázis volt.

Core Data helyi tároló használata esetén több táblát kell definiálnia a [megfelelő rendszertulajdonságokkal.](#review-core-data)

A mobilalkalmazások normál létrehozási, olvasási, frissítési és törlési (CRUD) műveletei úgy működnek, mintha az alkalmazás még mindig csatlakoztatva lenne, de az összes művelet a helyi tárolón keresztül történik.

Amikor szinkronizálta a helyi áruház a szerver, használtuk a **MSSyncTable.pullWithQuery** módszer.

## <a name="additional-resources"></a>További források
* [Offline adatszinkronizálás mobilalkalmazásokban]
* [Felhőborító: Offline szinkronizálás az Azure Mobile Services] \(ben A videó a mobilszolgáltatásokról szól, de a mobilalkalmazások offline szinkronizálása hasonló módon működik.\)

<!-- URLs. -->


[iOS-alkalmazás létrehozása]: app-service-mobile-ios-get-started.md
[Offline adatszinkronizálás mobilalkalmazásokban]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Felhőtakaró: Offline szinkronizálás az Azure mobilszolgáltatásokban]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
