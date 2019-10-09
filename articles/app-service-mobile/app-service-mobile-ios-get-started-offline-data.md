---
title: Az iOS Mobile apps szolgáltatással való offline szinkronizálás engedélyezése | Microsoft Docs
description: Megtudhatja, hogyan használhatók a Azure App Service Mobile apps az offline adatkapcsolatok iOS-alkalmazásokban való gyorsítótárazásához és szinkronizálásához.
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 0c96442de5b8eea2ec969c48e6a815b6ae78b5c4
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027279"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Az iOS Mobile apps szolgáltatással való offline szinkronizálás engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center támogatja a teljes körű és integrált szolgáltatások központi használatát a Mobile apps fejlesztéséhez. A fejlesztők a szolgáltatások **kiépítését**, **tesztelését** és **terjesztését** használhatják a folyamatos integráció és a kézbesítési folyamat beállításához. Az alkalmazás üzembe helyezését követően a fejlesztők az **elemzési** és **diagnosztikai** szolgáltatások segítségével ellenőrizhetik az alkalmazás állapotát és használatát, és a **leküldéses** szolgáltatást használó felhasználókkal is elvégezhetik a felhasználókat. A fejlesztők **a hitelesítést a** felhasználók **és az adatszolgáltatások** hitelesítésére is használhatják a Felhőbeli alkalmazásadatok megőrzése és szinkronizálása érdekében.
> Ha szeretné integrálni a Cloud Servicest a mobil alkalmazásban, regisztráljon App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) még ma.

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag az iOS rendszerhez készült Azure App Service Mobile Apps szolgáltatásával folytatott offline szinkronizálást ismerteti. A kapcsolat nélküli szinkronizálással a végfelhasználók akkor is kezelhetik a mobil alkalmazásokat, ha nem rendelkeznek hálózati kapcsolattal. A módosításokat a rendszer egy helyi adatbázisban tárolja. Miután az eszköz ismét online állapotba került, a módosítások szinkronizálva lesznek a távoli háttérrel.

Ha ez az első élmény a Mobile Apps, először el kell végeznie az oktatóanyag [IOS-alkalmazás létrehozása]. Ha nem a letöltött gyors indítású kiszolgálói projektet használja, hozzá kell adnia az adatelérési bővítmények csomagjait a projekthez. További információ a kiszolgálói bővítmények csomagjairól: [Az Azure-hoz készült .net backend Server SDK használata Mobile apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Az offline szinkronizálási szolgáltatással kapcsolatos további tudnivalókért tekintse meg [Offline adatszinkronizálás a Mobile Appsban]című témakört.

## <a name="review-sync"></a>Az ügyfél-szinkronizálási kód áttekintése
Az [iOS-alkalmazás létrehozása] oktatóanyaghoz letöltött ügyfél-projekt már tartalmaz olyan kódot, amely támogatja az offline szinkronizálást egy helyi alapszintű adatalapú adatbázis használatával. Ez a szakasz összefoglalja, hogy mi is szerepel az oktatóanyag kódjában. A szolgáltatás fogalmi áttekintését lásd: [Offline adatszinkronizálás a Mobile Appsban].

A Mobile Apps offline adatszinkronizálási funkciója segítségével a végfelhasználók akkor is kezelhetik a helyi adatbázisokat, ha a hálózat nem érhető el. Ha szeretné használni ezeket a szolgáltatásokat az alkalmazásban, inicializálja `MSClient` szinkronizálási környezetét, és hivatkozzon a helyi tárolóra. Ezután a **MSSyncTable** felületen keresztül hivatkozhat a táblára.

A **QSTodoService. m** (Objective-C) vagy a **ToDoTableViewController. Swift** (Swift) esetében figyelje meg, hogy a **syncTable** tag típusa **MSSyncTable**. Az offline szinkronizálás a **MSTable**helyett ezt a szinkronizálási tábla felületet használja. Egy szinkronizálási tábla használatakor az összes művelet a helyi tárolóra kerül, és a rendszer csak a távoli háttérrel szinkronizálja az explicit leküldéses és lekéréses műveletekkel.

 Egy szinkronizálási táblára mutató hivatkozáshoz használja a **syncTableWithName** metódust `MSClient` értékre. Az offline szinkronizálási funkciók eltávolításához használja helyette a **tableWithName** .

A tábla műveleteinek elvégzése előtt inicializálni kell a helyi tárolót. A megfelelő kód a következő:

* **Objective-C**. A **QSTodoService. init** metódusban:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**. A **ToDoTableViewController. viewDidLoad** metódusban:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Ez a metódus helyi tárolót hoz létre a `MSCoreDataStore` illesztőfelület használatával, amelyet a Mobile Apps SDK biztosít. Másik lehetőségként a `MSSyncContextDataSource` protokoll megvalósításával más helyi tárolót is megadhat. A **MSSyncContext** első paramétere is az ütköző kezelő megadására szolgál. Mivel a `nil` értéket adta meg, a rendszer lekéri az alapértelmezett ütköző kezelőt, amely bármilyen ütközés esetén sikertelen lesz.

Most végezzük el a tényleges szinkronizálási műveletet, és az adatok lekérése a távoli háttérből:

* **Objective-C**. @no__t – 0 – először leküldi az új módosításokat, majd meghívja a **pullData** -t, hogy lekérje az adatok távoli háttérből való lekérését. A **pullData** metódus pedig új, a lekérdezésnek megfelelő adatértékeket kap:

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
* **Swift**:
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

A Objective-C verzióban `syncData` esetében először a **pushWithCompletion** hívja meg a szinkronizálási kontextusban. Ez a módszer `MSSyncContext` (és nem maga a szinkronizálási tábla) tagja, mert az összes táblában leküldi a módosításokat. A kiszolgálónak csak olyan rekordokat kell elküldeni, amelyek valamilyen módon helyileg (CUD-műveleteken keresztül) vannak módosítva. Ezután meghívja a segítő **pullData** , amely a **MSSyncTable. pullWithQuery** hívásával kéri le a távoli adatlekérdezéseket, és a helyi adatbázisban tárolja azt.

A Swift verzióban, mivel a leküldéses művelet nem volt feltétlenül szükséges, a **pushWithCompletion**nem hívható meg. Ha a leküldéses műveletet végző tábla szinkronizálási kontextusában függőben lévő módosítások vannak, a lekéréses művelet mindig leküldéses műveletet hajt végre. Ha azonban egynél több szinkronizálási táblázattal rendelkezik, a lehető legjobb megoldás a leküldéses utasítás meghívása, hogy minden a kapcsolódó táblák között konzisztens legyen.

A Objective-C és a Swift verzióban egyaránt használhatja a **pullWithQuery** metódust a lekérdezni kívánt rekordok szűrésére szolgáló lekérdezés megadásához. Ebben a példában a lekérdezés a távoli `TodoItem` tábla összes rekordját lekérdezi.

A **pullWithQuery** második paramétere a *növekményes szinkronizáláshoz*használt lekérdezési azonosító. A növekményes szinkronizálás csak azokat a rekordokat kérdezi le, amelyek a legutóbbi szinkronizálás óta módosultak, a rekord @no__t – 0 időbélyegző használatával (a helyi tárolóban `updatedAt` néven). A lekérdezés AZONOSÍTÓjának olyan leíró sztringnek kell lennie, amely egyedi az alkalmazás minden logikai lekérdezéséhez. Ha ki szeretné kapcsolni a növekményes szinkronizálást, adja át a `nil` értéket a lekérdezési AZONOSÍTÓként. Ez a megközelítés potenciálisan nem hatékony lehet, mert az összes rekordot lekéri minden lekérési művelethez.

A Objective-C alkalmazás szinkronizálja az adatok módosításakor vagy hozzáadásakor, amikor egy felhasználó végrehajtja a frissítési kézmozdulatot, és elindul.

A Swift-alkalmazás szinkronizál, amikor a felhasználó végrehajtja a frissítési kézmozdulatot és indításkor.

Mivel az alkalmazás szinkronizálja az adatmódosítást (Objective-C), vagy amikor az alkalmazás elindul (Objective-C és Swift), az alkalmazás feltételezi, hogy a felhasználó online állapotú. Egy későbbi szakaszban frissíteni fogja az alkalmazást, hogy a felhasználók akkor is szerkeszthetnek, ha offline állapotban vannak.

## <a name="review-core-data"></a>Az alapszintű adatmodell áttekintése
Ha az alapadatok offline tárolóját használja, meg kell határoznia az adatmodellben meghatározott táblákat és mezőket. A mintául szolgáló alkalmazás már tartalmaz egy megfelelő formátumú adatmodellt. Ebben a szakaszban bemutatjuk, hogyan használják a táblázatokat.

Nyissa meg a **QSDataModel. xcdatamodeld**. Négy tábla van definiálva – három, amelyet az SDK használ, és amelyek a tennivalók számára is használatosak:
  * MS_TableOperations: A kiszolgálóval szinkronizálandó elemek nyomon követése.
  * MS_TableOperationErrors: Az offline szinkronizálás során megjelenő hibák nyomon követése.
  * MS_TableConfig: A legutóbbi szinkronizálási művelet utolsó frissítésének időpontját követi az összes lekéréses művelet esetében.
  * TodoItem A tennivaló elemeket tárolja. A rendszeroszlopok **createdAt**, **updatedAt**és **verziószáma** opcionális rendszertulajdonság.

> [!NOTE]
> A Mobile Apps SDK fenntartja a " **``** " kezdetű oszlop nevét. Ne használja ezt az előtagot a rendszeroszlopok kivételével. Ellenkező esetben az oszlopnevek a távoli háttér használatakor módosulnak.
>
>

Ha az offline szinkronizálás funkciót használja, adja meg a három rendszertáblát és az adattáblát.

### <a name="system-tables"></a>Rendszertáblák

**MS_TableOperations**  

![MS_TableOperations táblázat attribútumai][defining-core-data-tableoperations-entity]

| Attribútum | Type |
| --- | --- |
| id | Integer 64 |
| elemazonosító | Sztring |
| properties | Binary Data |
| table | Sztring |
| tableKind | Integer 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors táblázat attribútumai][defining-core-data-tableoperationerrors-entity]

| Attribútum | Type |
| --- | --- |
| id |Sztring |
| operationId |Integer 64 |
| properties |Binary Data |
| tableKind |Integer 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Attribútum | Type |
| --- | --- |
| id |Sztring |
| key |Sztring |
| keyType |Integer 64 |
| table |Sztring |
| value |Sztring |

### <a name="data-table"></a>Adattábla

**TodoItem**

| Attribútum | Type | Megjegyzés |
| --- | --- | --- |
| id | Karakterlánc, megjelölve kötelező |Elsődleges kulcs a távoli tárolóban |
| teljes | Logikai | Teendő mező |
| text |Sztring |Teendő mező |
| CreatedAt | Date | választható A **createdAt** System tulajdonságának leképezése |
| updatedAt | Date | választható A **updatedAt** System tulajdonságának leképezése |
| version | Sztring | választható Ütközések észlelésére használatos, leképezve a verzióra |

## <a name="setup-sync"></a>Az alkalmazás szinkronizálási viselkedésének módosítása
Ebben a szakaszban úgy módosítja az alkalmazást, hogy az ne legyen szinkronizálva az alkalmazás indításakor, vagy amikor elemeket szúr be és frissít. Csak a refresh kézmozdulat gomb elvégzése után szinkronizál.

**Objective-C**:

1. A **QSTodoListViewController. m**-ben módosítsa a **viewDidLoad** metódust, hogy eltávolítsa a metódus végén található `[self refresh]`-es hívást. A rendszer most nem szinkronizálja az adatkiszolgálót az alkalmazás indításakor. Ehelyett szinkronizálva van a helyi tároló tartalmával.
2. A **QSTodoService. m**-ben módosítsa `addItem` definícióját úgy, hogy az az elem beszúrása után ne legyen szinkronban. Távolítsa el a `self syncData` blokkot, és cserélje le a következőre:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Módosítsa `completeItem` definícióját, ahogy azt korábban említettük. Távolítsa el a `self syncData` blokkot, és cserélje le a következőre:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

@No__t – 0, a **ToDoTableViewController. Swift**esetében tegye megjegyzésbe az itt látható két sort, hogy leállítsa a szinkronizálást az alkalmazás indításakor. Az írás időpontjában a Swift Todo alkalmazás nem frissíti a szolgáltatást, amikor valaki hozzáadja vagy befejezi az adott elemeket. A szolgáltatás csak az alkalmazás indításakor frissül.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Az alkalmazás tesztelése
Ebben a szakaszban érvénytelen URL-címhez csatlakozik a kapcsolat nélküli forgatókönyv szimulálása érdekében. Adatelemek hozzáadásakor a rendszer a helyi alapadattárban tárolja őket, de nem szinkronizálja őket a Mobile-App háttérrel.

1. Módosítsa a Mobile-App URL-címét a **QSTodoService. m** -ben egy érvénytelen URL-címre, majd futtassa újra az alkalmazást:

   **Objective-C**. A QSTodoService. m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**. A ToDoTableViewController. Swift-ben:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Adjon hozzá néhány teendőt. Zárja be a szimulátort (vagy zárja be kényszerítve az alkalmazást), majd indítsa újra. Ellenőrizze, hogy a módosítások megmaradnak-e.

3. A távoli **TodoItem** tábla tartalmának megtekintése:
   * A Node. js háttérrendszer esetében nyissa meg a [Azure Portal](https://portal.azure.com/) , és a Mobile-App háttér területen kattintson az **Easy Tables** > **TodoItem**elemre.  
   * A .NET háttérrendszer esetében használjon egy SQL-eszközt, például SQL Server Management Studio vagy egy REST-ügyfelet, például a Hegedűs vagy a Poster.  

4. Ellenőrizze, hogy az új elemek *nem* lettek-e szinkronizálva a kiszolgálóval.

5. Módosítsa az URL-címet a megfelelő értékre a **QSTodoService. m**-ben, majd futtassa újból az alkalmazást.

6. A frissítési kézmozdulat végrehajtásához húzza le az elemek listáját.  
Megjelenik egy folyamatjelző.

7. Tekintse meg ismét a **TodoItem** -adatbázisokat. Ekkor meg kell jelennie az új és a módosított teendő elemek megjelenítésének.

## <a name="summary"></a>Összegzés
Az offline szinkronizálási funkció támogatásához a `MSSyncTable` felületet használtuk, és a `MSClient.syncContext` inicializálása helyi tárolóval. Ebben az esetben a helyi tároló egy alapszintű adatalapú adatbázis volt.

Ha alapszintű adattárat használ, több táblát kell megadnia a [megfelelő rendszer-tulajdonságokkal](#review-core-data).

A Mobile apps normál létrehozási, olvasási, frissítési és törlési (szifilisz) műveletei ugyanúgy működnek, mint ha az alkalmazás továbbra is csatlakoztatva van, de az összes művelet a helyi tárolón történik.

Amikor szinkronizálta a helyi tárolót a-kiszolgálóval, a **MSSyncTable. pullWithQuery** metódust használtuk.

## <a name="additional-resources"></a>További források
* [Offline adatszinkronizálás a Mobile Appsban]
* @no__t – 0Cloud fedél: Offline szinkronizálás az Azure-ban Mobile Services @ no__t-0 \(a videó a Mobile Services, de Mobile Apps offline szinkronizálás hasonló módon működik. \)

<!-- URLs. -->


[IOS-alkalmazás létrehozása]: app-service-mobile-ios-get-started.md
[Offline adatszinkronizálás a Mobile Appsban]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

@no__t – 0Cloud fedél: Offline szinkronizálás az Azure-ban Mobile Services @ no__t-0
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
