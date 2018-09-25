---
title: Az Azure Cosmos DB több főkiszolgálós ütközésének feloldása
description: Ez a cikk ismerteti a ütközés kategóriák és a ütközés feloldása módok például a Wins-utolsó-írója (LWW), egyéni – a felhasználó által megadott eljárás, egyéni – az Azure Comsos DB több főkiszolgálós aszinkron.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: d2f9c5dc8decfbe613d9fd3be2c37b45f1e4f2ea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987628"
---
# <a name="multi-master-conflict-resolution-in-azure-cosmos-db"></a>Az Azure Cosmos DB több főkiszolgálós ütközésének feloldása 

Az Azure Cosmos DB több főkiszolgálós biztosít globális ütközés feloldása módok, esetén konzisztens minden régióban, ahol a rendszer replikálja.

## <a name="conflict-categories"></a>Ütközés kategóriák

Az Azure Cosmos DB-adatai használatakor előforduló ütközések három kategóriába sorolhatók:

* **Ütközések beszúrása:** az ilyen típusú ütközés történik, ha az alkalmazás (például azonosítója) azonos egyedi indexszel rendelkező két vagy több dokumentum beszúrása a két vagy több régióban egyszerre. Ebben az esetben kezdetben az összes írási művelet sikerülhet, de úgy dönt, az ütközésfeloldási házirend alapján, az eredeti azonosító csak egy dokumentum számára fontos.

* **Cserélje le az ütközéseket:** az ilyen típusú ütközés történik, ha az alkalmazás egyszerre két vagy több régióban, az egyetlen dokumentum frissíti.

* **Törlés ütközések:** az ilyen típusú ütközés történik, ha az alkalmazás egy dokumentumot töröl egy adott régióban, és frissíti az egy vagy több régióban. 

## <a name="conflict-resolution-modes"></a>Ütközés feloldása módok

Nincsenek Azure Cosmos DB által kínált három ütközés feloldása módot. Ütközés feloldása módok gyűjteményekhez vannak meghatározva.

> [!NOTE]
> Az SQL API-t felhasználók három különböző ütközés feloldása mód közül választhat. Minden más API-modellek (MongoDB, Cassandra, diagram és táblázat) ütközések feloldása Wins-utolsó-író segítségével.

### <a name="last-writer-wins-lww"></a>Wins-utolsó-írója (LWW)

Wins-utolsó-író alapértelmezett ütközés feloldása módban. Ebben a módban ütközések elhárulnak átadott tulajdonság a dokumentum egy numerikus érték alapján.

A következő kódrészletet, amelyek a Wins-utolsó-író ütközésfeloldási házirend konfigurálása, ha a .net SDK használatával. A "ConflictResolutionPath" az elérési utat a tulajdonságot, amelyet szeretné feloldani az ütközést az határozza meg. Ebben a példában `/userDefinedId` az ütközés feloldása elérési utat, és a dokumentum a legnagyobb `userDefinedId` érték mindig legyőzi az ütközést. Regisztrálni a Wins-utolsó-író feloldási mód, kiépíteni a gyűjteményt a ConflictResolutionPolicy az, ahogy az alábbi.

```csharp
DocumentCollection lwwCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
   Id = "myCollection", 
   ConflictResolutionPolicy = new ConflictResolutionPolicy
   {
      Mode = ConflictResolutionMode.LastWriterWins,
      ConflictResolutionPath = "/userDefinedId"
   }
});
```

 Utolsó-író – Wins-hozzárendeléshez módot alkalmazza különböző adatkategóriák ütköző, az alábbiak szerint:

* **Beszúrási és frissítési ütközés:** két vagy több dokumentum ütköznek az insert vagy csereműveletek, a dokumentum, amely tartalmazza a legnagyobb értéket az ütközés feloldása elérési út lesz a győztes (azaz userDefinedId). Ha több dokumentumot ugyanazt az ütközés feloldása elérési numerikus értéket, a kiválasztott nyertest nem determinisztikus. Azonban minden régió egyetlen győztes lesz szerveződik.

* **Ütközés törlése:** törlés ütközések vesz részt, ha a törlés minden esetben wins keresztül más attól függetlenül, az ütközés feloldása elérési út értékét cserélje le ütközés.

### <a name="custom--user-defined-procedure"></a>Egyéni – a felhasználó által meghatározott eljárást

Ebben a módban a felhasználói vezérlők ütközést névfeloldási regisztrációja egy felhasználó definiált eljárás (UDP), a gyűjtemény. Az UDP egy adott aláírással rendelkezik. Ha ezt a beállítást, de nem sikerült regisztrálni a UDP, vagy ha az UDP futásidőben kivételt jelez, a hírcsatorna, ahol azok feloldható külön-külön ütközések írt ütközéseket.

Egyéni – felhasználó által meghatározott eljárást ütközés feloldása módban, regisztrálja kiépíteni a gyűjteményt a ConflictResolutionPolicy az, ahogy az alábbi.

```csharp
DocumentCollection udpCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
  Id = "myCollection",
  ConflictResolutionPolicy = new ConflictResolutionPolicy
  {
     Mode = ConflictResolutionMode.Custom,
     ConflictResolutionProcedure = UriFactory.CreateStoredProcedureUri("myDatabase","myCollection","myUdpStoredProcedure").ToString()
  }
});
```

Ezután adja hozzá a felhasználó által meghatározott eljárást a gyűjtemény alább látható módon.

```csharp
StoredProcedure myUdpStoredProc = new StoredProcedure
{
   Id = "myUdpStoredProcedure",
   Body = myUdpStoredProcText
};
await myClient.UpsertStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("myDatabase", "myCollection"), myUdpStoredProc);
```

A tárolt eljárás regisztrálva a gyűjtemény egy speciális aláírással rendelkezik. Ebben a példában az alábbi, az UDP használ egy tulajdonság `UserDefinedId` ütközések feloldásához. A dokumentum a legnagyobb értékű wins az ütközést.

```javascript
function myUdpStoredProcedure(incomingDocument, existingDocument, isDeleteConflict, conflictingDocuments){
    var collection = getContext().getCollection();

    if (!incomingDocument) {
        if (existingDocument) {

            collection.deleteDocument(existingDocument._self, {}, function(err, responseOptions) {
                if (err) throw err;
            });
        }
    } else if (isDeleteConflict) {
        // delete always wins.
    } else {
        var documentToUse = incomingDocument;

        if (existingDocument) {
            if (documentToUse.userDefinedId < existingDocument.userDefinedId) {
                documentToUse = existingDocument;
            }
        }

        var i;
        for (i = 0; i < conflictingDocuments.length; i++) {
            if (documentToUse.userDefinedId < conflictingDocuments[i].userDefinedId) {
                documentToUse = conflictingDocuments[i];
            }
        }

        tryDelete(conflictingDocuments, incomingDocument, existingDocument, documentToUse);
    }

    function tryDelete(documents, incoming, existing, documentToInsert) {
        if (documents.length > 0) {
            collection.deleteDocument(documents[0]._self, {}, function(err, responseOptions) {
                if (err) throw err;

                documents.shift();
                tryDelete(documents, incoming, existing, documentToInsert);
            });
        } else if (existing) {
            collection.replaceDocument(existing._self, documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        } else {
            collection.createDocument(collection.getSelfLink(), documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        }
    }
}

```

Az eljárás négy paraméterekkel rendelkezik:

* **incomingDocument:** határozza meg a dokumentum ütköző verzióját. Az ütközés az insert, replace vagy -törlés ütközést lehet. A törlési ütközés lép fel ez lesz egy delete-rendszerképet (törlésjelző) nincs tartalom.

* **existingDocument:** adja meg a véglegesített verzióba, a dokumentum, azonos "relatív" érték, mint a incomingDocument. Ez a paraméter értéke null a Beszúrás, és törölje az ütközést.

* **isDeleteConflict:** logikai jelzőt, amely azt jelzi, ha a bejövő dokumentum ütközik egy korábban törölt dokumentumot. Ha ez a paraméter értéke true, existingDocument is null értékű.

* **conflictingDocuments:** az adatbázisban, amelyek ütköznek az azonosító oszlop vagy bármely egyéb egyedi index mezőire incomingDocument adja meg az összes dokumentum véglegesített verzióba gyűjteménye. Ezeket a dokumentumokat kell "relatív" eltérő érték a incomingDocument képest.

A felhasználó által meghatározott eljárást a Cosmos DB partíciókulcs teljes hozzáféréssel rendelkezik, és az ütközések feloldása store műveleteket hajthat végre. Ha a felhasználó által meghatározott eljárást nem véglegesíteni az ütköző verzió, a rendszer eldobja az ütközést, és a existingDocument véglegesített marad. Ha a felhasználó által meghatározott eljárást meghibásodik, vagy nem létezik, Azure Cosmos DB lesz az összes adja hozzá az ütközést a csak olvasható ütközések hírcsatorna, ahol fel lehessen dolgozni aszinkron módon, ahogyan az a [aszinkron ütközés feloldása mód](). 

### <a name="custom--asynchronous"></a>Egyéni – aszinkron  

Ebben a módban az Azure Cosmos DB összes ütközésnél (insert, replace és törlése) kizárja a véglegesítése, és regisztrálja őket az alkalmazás a felhasználó által a késleltetett feloldásához hírcsatorna a csak olvasható ütközéseket. Az alkalmazás ütközésfeloldás aszinkron módon végrehajtani, és használja minden olyan logikai vagy bármely külső forrásból, alkalmazás vagy szolgáltatás az ütközés feloldásához tekintse meg.

Egyéni – aszinkron ütközés feloldása módban, regisztrálja kiépíteni a gyűjteményt a ConflictResolutionPolicy az, ahogy az alábbi.

```csharp
DocumentCollection manualCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
    Id = "myCollection",
    ConflictResolutionPolicy = new ConflictResolutionPolicy
    {
        Mode = ConflictResolutionMode.Custom
    }
});
```

Olvassa el, és az ütközések hírcsatornában ütközéseket feldolgozásához, hajtja végre az alábbi kódot. Az ütközések hírcsatorna-ban tárolt adatok hozzáadása a bizonyos tárolási költségeket. Ezért azt javasoljuk, hogy az adatcsatorna feldolgozása után ütközések tárolt adatokat törli.

```csharp
FeedResponse<Conflict> response = await myClient.ReadConflictFeedAsync(myCollectionUri);
  foreach (Conflict conflict in response)
  {
      switch(conflict.OperationKind)
      {
         case OperationKind.Create:
         //Process Insert Conflicts.
         …
         case OperationKind.Replace:
         //Process Replace Conflicts
         …
         case OperationKind.Delete:
         //Process Delete Conflicts
         …
      }
  //Optionally delete the conflict after processed
  await myClient.DeleteConflictAsync(conflict.SelfLink);
  }
```

> [!NOTE]
> Az ütközések hírcsatorna nem tartalmaz egy figyelőt, hogy az alárendelt feldolgozáshoz, például a Cosmos DB-ben a változáscsatorna értesítések küldéséhez. Hogy lekérdezik a ütközések hírcsatorna, és határozza meg, hogy megtalálhatók-e ütközések a logikát alkalmazzák.

## <a name="code-samples"></a>Kódminták

Az alábbiakban felsorolt az API-k ütközésfeloldás alkalmazását bemutató mintaalkalmazások. Minden mintához ütközik egy tárolón belül hoz létre, és majd bemutatja, hogyan ütközések feloldása minden támogatott ütközés feloldása üzemmódban.

|API-modell  | SDK |Sample |
|---------|---------|---------|
|SQL      | .NET    |[Azure-cosmos-DB-SQL-DotNet-multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-sql-dotnet-multi-master)  |
|MongoDB  | .NET    |[Azure-cosmos-DB-mongodb-DotNet-multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-multi-master)   |
|Tábla    | .NET    |[Azure-cosmos-DB-TABLE-DotNet-multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-multi-master)       |
|SQL      | Csomópont    |[az Azure-cosmos-js/samples/MultiRegionWrite /](https://github.com/Azure/azure-cosmos-js/tree/master/samples/MultiRegionWrite)  |
|SQL      | Java    |[Azure-cosmos-DB-SQL-Java-multi-Master](https://github.com/Azure-Samples/azure-cosmos-db-sql-java-multi-master)  |

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett a ütközés kategóriákat és ütközés feloldása mód az Azure Cosmos DB. Ezután tekintse meg az alábbi forrásanyagokat:

* [Használja a MongoDB-ügyfelekből több főkiszolgálós](multi-master-oss-nosql.md)
