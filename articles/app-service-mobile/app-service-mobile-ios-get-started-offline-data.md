---
title: IOS-mobilalkalmazásokban offline szinkronizálás engedélyezése |} A Microsoft Docs
description: Ismerje meg a mobilalkalmazások az Azure App Service-ben a cache és a szinkronizálási offline adatok használata az iOS-alkalmazások.
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: bc0afcf1ac7d9e7a777d850e1b6df7b915837f3a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956874"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>IOS-mobilalkalmazásokban offline szinkronizálás engedélyezése
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogy a kapcsolat nélküli szinkronizálás iOS-hez készült Azure App Service Mobile Apps szolgáltatással. A kapcsolat nélküli szinkronizálja végfelhasználók használhatják a megtekintése, hozzáadása és módosíthassanak adatokat, akkor is, ha nincs hálózati kapcsolat rendelkeznek egy mobilalkalmazást. Változások a helyi adatbázisban vannak tárolva. Miután az eszköz ismét online, a rendszer szinkronizálja a módosításokat, a távoli háttérrendszerrel.

Ha ez az első élmény, a Mobile Apps, az oktatóanyag első kell teljes [iOS-alkalmazás létrehozása]. Ha nem használja a letöltött gyorsútmutató-kiszolgálói projektet, hozzá kell adnia az adat-hozzáférési bővítmény csomagokat a projekthez. Kiszolgáló bővítménycsomagok kapcsolatos további információkért lásd: [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

A kapcsolat nélküli szinkronizálás – szolgáltatással kapcsolatos további tudnivalókért lásd: [Offline Adatszinkronizálás a mobilalkalmazásokban].

## <a name="review-sync"></a>Tekintse át az Ügyfélkód szinkronizálása
Az ügyfélprojekt a letöltött a [iOS-alkalmazás létrehozása] oktatóanyag már tartalmazza a kódot, amely támogatja a helyi adatok Core-alapú adatbázis kapcsolat nélküli szinkronizálás. Ez a szakasz foglalja össze, amit már szerepel az oktatóprogram kódmintája. A szolgáltatás fogalmi áttekintése, lásd: [Offline Adatszinkronizálás a mobilalkalmazásokban].

A Mobile Apps az offline adatszinkronizálás szolgáltatásával, végfelhasználók használhatják egy helyi adatbázist akkor is, ha a hálózat nem érhető el. Ezek a funkciók használata az alkalmazásban, a szinkronizálási kontextusában inicializálása `MSClient` és a egy helyi tárolóban hivatkozhat. A tábla keresztül hivatkozik, majd a **MSSyncTable** felületet.

A **QSTodoService.m** (Objective-C) vagy **ToDoTableViewController.swift** (Swift), vegye figyelembe, hogy a tag típusával **syncTable** van **MSSyncTable** . Kapcsolat nélküli szinkronizálás – a szinkronizálás tábla felületet helyett használja a **MSTable**. Egy szinkronizálási tábla használata esetén minden művelet nyissa meg a helyi tárolóban, és csak a távoli háttér explicit leküldési és lekérési műveletek szinkronizálva.

 A szinkronizálás hivatkozás lekéréséhez használja a **syncTableWithName** metódust `MSClient`. A kapcsolat nélküli szinkronizálásának eltávolításához használja **tableWithName** helyette.

Bármely table műveletek végrehajtása előtt inicializálni kell a helyi tárolóban. Itt látható a megfelelő kódot:

* **Objective-C**. Az a **QSTodoService.init** módszer:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **SWIFT**. Az a **ToDoTableViewController.viewDidLoad** módszer:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Ez a módszer használatával egy helyi tárolóban hozza létre a `MSCoreDataStore` felület, amely a Mobile Apps SDK-t biztosít. Azt is megteheti, megadhat egy másik helyi tároló alkalmazásával a `MSSyncContextDataSource` protokoll. Az is, az első paraméterként **MSSyncContext** ütközés kezelő meghatározására szolgál. Mivel tudjuk megfeleltek `nil`, az ütközés meghiúsul, az ütközés alapértelmezett kezelő kapunk.

Most tegyük a tényleges szinkronizálási művelet hajtható végre, és adatokat kérhet le a távoli háttér:

* **Objective-C**. `syncData` először leküldi az új módosításokat, és ekkor meghívja a **pullData** adatokat kíván gyűjteni a távoli háttérrendszere. Ezután a **pullData** metódus lekéri a lekérdezésnek megfelelő új adatok:

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

Az Objective-C verzióban a `syncData`, első hívás **pushWithCompletion** a szinkronizálási környezetben. Ez a módszer tagja `MSSyncContext` (és nem magát szinkronizálási táblázatot), mert az összes leküldi a módosításokat. A kiszolgáló csak a helyi (CUD műveletek) keresztül valamilyen módon módosítva lett rekordok kerülnek. Ezután a segítő **pullData** nevezzük, amely **MSSyncTable.pullWithQuery** távoli adatok lekéréséhez és a helyi adatbázisban tárolja.

A Swift nyelvű a leküldéses művelet nem feltétlenül szükséges, mert nincs nincs hívása **pushWithCompletion**. Ha a szinkronizálás a környezetben a tábla, amely egy leküldéses művelet függőben lévő módosításokat, lekéréses mindig kiad egy leküldéses először. Azonban ha több szinkronizálási táblából, célszerű explicit módon hívja a leküldéses annak biztosításához, hogy minden konzisztens kapcsolódó táblák között.

Az Objective-C és a Swift verziók, is használhatja a **pullWithQuery** metódus megadhat egy lekérdezést a rekordok szűrésére szeretné beolvasni. Ebben a példában a lekérdezés lekérdezi a távoli összes rekordja `TodoItem` tábla.

A második paramétere **pullWithQuery** használt lekérdezés azonosító *növekményes szinkronizálás*. A növekményes szinkronizálás csak azok a rekordok használatával a rekord, a legutóbbi szinkronizálás óta módosított lekéri `UpdatedAt` időbélyeg (nevű `updatedAt` a helyi tároló.) A lekérdezés Azonosítóját, amely egyedi az alkalmazás minden egyes logikai lekérdezés leíró karakterláncnak kell lennie. Tilthatják le a növekményes szinkronizálás, át kell adnia `nil` , a lekérdezés azonosítóját. Ez a megközelítés potenciálisan nem elég hatékony, lehet, mivel ez lekéri az összes rekordot az egyes lekéréses műveletek.

Az Objective-C-alkalmazást szinkronizálja, ha módosítja vagy hozzá adatokat, amikor egy felhasználó a frissítés kézmozdulat végez, és indítsa el a.

A Swift-alkalmazást szinkronizálja, amikor a felhasználó hajt végre a frissítési kézmozdulat és az indításkor.

Mivel az alkalmazás szinkronizálja, amikor az adatok módosítva (Objective-C), vagy minden alkalommal, amikor az alkalmazás elindul (Objective-C és Swift), az alkalmazás azt feltételezi, hogy a felhasználó online állapotban. Egy későbbi szakasz ismerteti hogy frissíteni fogja az alkalmazást úgy, hogy a felhasználók szerkeszthetik, akkor is, ha a kapcsolat nélküli.

## <a name="review-core-data"></a>Tekintse át a Core Data model
A legfontosabb adatainak helyi tároló használata esetén meg kell határoznia egyes táblákat és mezőket az adatmodellben. A mintaalkalmazás már tartalmaz egy modellt a szükséges formátumban. Ez a szakasz ismerteti azokat a lépéseket megjelenítéséhez a használatukat, ezeket a táblázatokat.

Nyissa meg **QSDataModel.xcdatamodeld**. A négy tábla meghatározott--három, amelyeket az SDK-t használ, a másik használható a teendő elemek maguk:
  * MS_TableOperations: Nyomon követi a kiszolgáló szinkronizálni kell igénylő elemeket.
  * MS_TableOperationErrors: Kapcsolat nélküli szinkronizálás során esetlegesen előforduló hibák nyomon követi.
  * MS_TableConfig: Nyomon követi az utolsó az idő az utolsó szinkronizálási művelet a pull művelet frissítése.
  * TodoItem: Tárolja a Tennivalólista elemein. A rendszer oszlopok **createdAt**, **updatedAt**, és **verzió** nem kötelező tulajdonságai vannak.

> [!NOTE]
> A Mobile Apps SDK fenntartja az oszlopnevek kezdődő "**``**". A rendszer oszlopok csakis ne használja ezt az előtagot. Ellenkező esetben az oszlopnevek módosítják a távoli háttérrendszer használata esetén.
>
>

A kapcsolat nélküli szinkronizálás – a szolgáltatás használatakor a rendszer három táblákat és az adattábla megadása.

### <a name="system-tables"></a>Rendszertáblák

**MS_TableOperations**  

![MS_TableOperations tábla attribútumai][defining-core-data-tableoperations-entity]

| Attribútum | Típus |
| --- | --- |
| id | Egész 64 |
| Elemazonosító | Karakterlánc |
| properties | A bináris adatok |
| tábla | Karakterlánc |
| tableKind | Egész 16 |


**MS_TableOperationErrors**

 ![MS_TableOperationErrors tábla attribútumai][defining-core-data-tableoperationerrors-entity]

| Attribútum | Típus |
| --- | --- |
| id |Karakterlánc |
| operationId |Egész 64 |
| properties |A bináris adatok |
| tableKind |Egész 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Attribútum | Típus |
| --- | --- |
| id |Karakterlánc |
| kulcs |Karakterlánc |
| KeyType |Egész 64 |
| tábla |Karakterlánc |
| érték |Karakterlánc |

### <a name="data-table"></a>Adattábla

**TodoItem**

| Attribútum | Típus | Megjegyzés |
| --- | --- | --- |
| id | Karakterlánc, kötelezőként megjelölt |Távoli tároló az elsődleges kulcs |
| Hajtsa végre | Logikai | TEENDŐ elem mező |
| szöveg |Karakterlánc |TEENDŐ elem mező |
| createdAt | Dátum | (nem kötelező) A Maps **createdAt** rendszertulajdonság |
| updatedAt | Dátum | (nem kötelező) A Maps **updatedAt** rendszertulajdonság |
| version | Karakterlánc | (nem kötelező) Ütközések, a maps-verzióra észleléséhez használt |

## <a name="setup-sync"></a>Az alkalmazás a szinkronizálási viselkedésének módosítása
Ebben a szakaszban módosítja az alkalmazást úgy, hogy nem szinkronizálja az alkalmazás indítása, vagy ha insert és elemek frissítése. A szinkronizált csak akkor, ha a frissítés kézmozdulat gomb történik.

**Objective-C**:

1. A **QSTodoListViewController.m**, módosítsa a **viewDidLoad** metódus hívása eltávolítása `[self refresh]` a metódus végén. Az adatok most már nincs szinkronizálva az alkalmazás indítása a kiszolgálóval. Ehelyett azt szinkronizálva van a helyi tároló tartalmát.
2. A **QSTodoService.m**, definíciójának módosítása `addItem` úgy, hogy azt követően kerül beillesztésre oda, az elem nem szinkronizálja. Távolítsa el a `self syncData` letiltása, és cserélje le a következő:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Definíciójának módosítása `completeItem` amint azt korábban említettük. A Tiltás a `self syncData` és cserélje le a következő:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**SWIFT**:

A `viewDidLoad`, a **ToDoTableViewController.swift**, tegye megjegyzésbe a két sor látható itt, az alkalmazás indítása a szinkronizálás leállítása. A cikk írásának időpontjában a Swift Teendőkezelő alkalmazás nem frissíti a szolgáltatás Ha valaki hozzáad, vagy egy elem befejeződik. Frissíti a szolgáltatást csak az alkalmazás indítása.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Az alkalmazás tesztelése
Ebben a szakaszban csatlakoztatja egy kapcsolat nélküli forgatókönyv szimulálása érvénytelen URL-címre. Adatelemek hozzáadásakor azok éppen tartott a helyi alapvető adatok tárolására, de nincs a mobilalkalmazás-háttérrendszer van szinkronizálva.

1. Módosítsa a mobilalkalmazás-URL-címet a **QSTodoService.m** egy érvénytelen URL-címet, és futtassa ismét az alkalmazást:

   **Objective-C**. A QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **SWIFT**. A ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Adjon hozzá néhány teendők. Lépjen ki a szimulátor (vagy kényszerített módon zárja be az alkalmazást), majd indítsa újra. Győződjön meg arról, hogy a változtatások megmaradnak.

3. A távoli tartalmának megtekintése **TodoItem** tábla:
   * Node.js háttérrendszer látogasson el a [az Azure portal](https://portal.azure.com/) és kattintson a mobilalkalmazás-háttérrendszer **könnyen kezelhető táblák** > **TodoItem**.  
   * Vissza .NET-end, egy SQL-eszközt, például az SQL Server Management Studio vagy a REST-ügyfél, például a Fiddler vagy Postman használatával.  

4. Győződjön meg arról, hogy rendelkezik-e az új elemeket *nem* lett szinkronizálva a kiszolgálóval.

5. Az URL-CÍMÉT állítsa vissza a megfelelőt a **QSTodoService.m**, és futtassa újra az alkalmazást.

6. A frissítés kézmozdulat végez le elemek listájának lekérése.  
Egy folyamat folyamatjelző jelenik meg.

7. Nézet a **TodoItem** újra adatokat. Az új és módosított teendők meg kell jelennie.

## <a name="summary"></a>Összegzés
A kapcsolat nélküli szinkronizálás – a szolgáltatás támogatása érdekében használjuk a `MSSyncTable` felületet, és inicializálva `MSClient.syncContext` a helyi tárolóból. Ebben az esetben a helyi tároló volt a legfontosabb adatainak-alapú adatbázis.

A legfontosabb adatainak helyi tároló használata esetén meg kell adnia a több táblát a [javítsa ki a rendszer tulajdonságai](#review-core-data).

A normál létrehozását, olvasását, frissíthet és törölhet (CRUD) műveleteket mobilalkalmazások együttműködnek a szerint, ha az alkalmazás továbbra is csatlakozik, de minden műveletet a helyi tárolóban történik.

A helyi tároló szinkronizálja azt a kiszolgálón, amikor használtuk a **MSSyncTable.pullWithQuery** metódust.

## <a name="additional-resources"></a>További források
* [Offline Adatszinkronizálás a mobilalkalmazásokban]
* [Cloud Cover: Offline szinkronizálás az Azure Mobile Services] \(a videó tudnivalók a mobilszolgáltatások, de a Mobile Apps kapcsolat nélküli szinkronizálás működésének hasonló módon.\)

<!-- URLs. -->


[IOS-alkalmazás létrehozása]: app-service-mobile-ios-get-started.md
[Offline Adatszinkronizálás a mobilalkalmazásokban]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Offline szinkronizálás az Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
