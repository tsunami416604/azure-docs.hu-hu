---
title: Lekérdezések futtatása az Azure Cosmos DB kérelemegység és költségek optimalizálása
description: Megtudhatja, hogyan kérelem egységekre vonatkozó díjakon egy lekérdezés kiértékeléséhez és optimalizálásához a lekérdezési teljesítmény és költség tekintetében.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: e6814224827aac0da9c6faf5108ecf585bae7c35
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57445381"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Az Azure Cosmos DB lekérdezési költségek optimalizálása

Az Azure Cosmos DB az adatbázis-műveletek, beleértve a relációs és hierarchikus lekérdezéseket, amely egy tárolóban található elem a gazdag tárházát kínálja. A műveletekhez kapcsolódó költségek a CPU, IO és a művelet végrehajtásához szükséges alapján változik. Szem előtt tartva és hardver-erőforrások kezelése, helyett felfogható kérelemegység (RU) egyetlen mérték a kérés kiszolgálása különböző adatbázis-műveletek végrehajtásához szükséges erőforrásokhoz. Ez a cikk bemutatja, hogyan kérelem egységekre vonatkozó díjakon egy lekérdezés kiértékeléséhez és optimalizálásához a lekérdezési teljesítmény és költség tekintetében. 

Az Azure Cosmos DB lekérdezések általában rendezése a leggyorsabb és a legtöbb hatékony lassabb/kevésbé hatékony, átviteli sebesség szempontjából, a következőképpen történik:  

* A GET műveletet egy önálló partíciókulcsokon és elemkulcs.

* Lekérdezés egy szűrési záradékot, amelyik egyetlen partíciókulcsot belül.

* A lekérdezés egy egyenlőség vagy tartomány szűrőfeltételt bármely vlastnost nélkül.

* A lekérdezés szűrők nélkül.

Lekérdezések, amelyek adatokat olvasni az egy vagy több partíció nagyobb késést merülnek fel, és megnövelt számú kérelemegység felhasználását. Mivel mindegyik partíció rendelkezik az összes tulajdonság automatikus indexelést, a lekérdezés az indexből hatékonyan is kiszolgálható. Lekérdezések, amelyek több partíciót gyorsabban a párhuzamossági beállítások használatával teheti meg. Particionálás és a partíciókulcsok kapcsolatos további információkért lásd: [az Azure Cosmos DB particionálási](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Kérelem egységek használata után a lekérdezés kiértékelése

Miután az Azure Cosmos-tárolókban tárolt adatokat, használhatja az adatkezelő az Azure Portalon hozhatnak létre, és a lekérdezések futtatásához. A lekérdezések költsége az adatkezelő segítségével is beszerezheti. Ez a metódus kap megismerje a tipikus lekérdezések és műveletek, amely támogatja a rendszer a tényleges költségek.

A lekérdezések költségét programozott módon is lekérése az SDK-k használatával. Mérhető bármilyen műveletet járó többletterhelést például létrehozása, frissítése vagy törlése vizsgálja meg a `x-ms-request-charge` fejléc REST API használata esetén. Ha a .net vagy a Java SDK-t használ a `RequestCharge` tulajdonsága a egyenértékű tulajdonságot a kérelem díja lekérése, és ez a tulajdonság nem található a ResourceResponse vagy FeedResponse.

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>A lekérdezés díja kérelemegység befolyásoló tényezők

Lekérdezések kérelemegységekről is számos tényezőtől függ. Például az Azure Cosmos-elemek száma betöltött/ad vissza, időt szemben az indexet, a lekérdezés fordítása keresések száma stb. részleteket. Az Azure Cosmos DB garantálja, hogy ugyanabból a lekérdezés végrehajtásakor ugyanazokat az adatokat bármikor felhasználhatja a azonos számú kérelemegység ismétlési végrehajtások mellett is. A lekérdezés-végrehajtási mérőszámokkal lekérdezés profilt biztosít, a kérelemegységek fordított hogyan érdemes.  

Bizonyos esetekben 200 429 válaszokat, és a lekérdezések, lapozható végrehajtása, mert a lekérdezéseket a lehető legnagyobb a rendelkezésre álló kérelemegység alapján fog futni a változó kérelemegység sorozatát jelenhet meg. A lekérdezés végrehajtása több oldal felosztása/kiszolgáló és ügyfél közötti lelassítja kerekíteni jelenhet meg. Például 10 000 elemek visszaadott több oldalon, egyes díját a számítás, az oldal hajtott végre. Ezek az oldalak közötti, sum, kell kap RUs azonos számú, a teljes lekérdezés számíthat.  

## <a name="metrics-for-troubleshooting"></a>Hibaelhárítás mérőszámok

A teljesítmény és a leginkább lekérdezések, felhasználó által definiált függvények (UDF-EK) által felhasznált átviteli sebesség attól függ, a függvény törzsében. Ismerje meg, mennyi idő a lekérdezés végrehajtása van a az UDF-ben és a fogyasztott, számát, a legegyszerűbb módja, a lekérdezés mérőszámainak engedélyezésével. Ha a .net SDK-t használ, az alábbiakban az SDK által visszaadott lekérdezés mintametrikák:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>Ajánlott eljárások a Cost-lekérdezések optimalizálása 

Ha költség a lekérdezések optimalizálását, vegye figyelembe az alábbi ajánlott eljárásokat:

* **Több entitástípusok elhelyezése**

   Próbálja meg tárolók egyetlen vagy kisebb számú belül több entitástípusok elhelyezése. Ez a módszer alapján előnyeit, nem csak a díjszabás szempontjából, hanem a lekérdezés végrehajtása és a tranzakciókért. Lekérdezések hatóköre egyetlen tároló; és tárolt eljárások és eseményindítók használatával több rekord keresztül elemi tranzakciókat is meghatározhat egy partíciókulcsot, egy egyetlen tárolóból. Közös elhelyezése az entitások ugyanabban a tárolóban is kevesebb hálózati kapcsolatok feloldásához a rekordok közötti adatváltások. Ezért azt növeli a végpontok közötti teljesítmény, nagyobb adatkészletek több rekord keresztül lehetővé teszi, hogy elemi tranzakciókat és eredményeképpen kínál a költségek csökkentésére. Ha a közös elhelyezése a tárolók egyetlen vagy kisebb számú belül több entitástípusok számára nehézséget jelent a forgatókönyv általában, mert egy meglévő alkalmazást telepít át, és nem szeretné, hogy a kódváltozások - majd érdemes, kiépítés átviteli sebesség az adatbázis szintjén.  

* **Mérheti és finomhangolási feladat alacsonyabb kérelem kérelemegység/s használat**

   A lekérdezés összetettségétől hatással van egy művelet felhasznált kérelemegység (RU) számát. A predikátum UDF-EK számát és méretét a forrás adatkészlet jellegét, predikátumok számát. Ezek a tényezők befolyásolhatják a lekérdezési műveletek költségét. 

   Kérelem díja a kérelem fejlécében visszaadott azt jelzi, hogy egy adott lekérdezésre költségét. Például ha egy lekérdezést 1000 1 KB-os elemeket adja vissza, a költség, a művelet: 1000. Emiatt a belül egy második, a kiszolgáló figyelembe veszi előtt sebességével későbbi kérelmeket csak két ilyen kérelmeket. További információkért lásd: [kérelemegységek](request-units.md) cikk és a kérés egység számológépet. 

## <a name="next-steps"></a>További lépések

Ezután folytassa további tudnivalók a költségek optimalizálása az Azure Cosmos DB az alábbi cikkeket:

* Tudjon meg többet [Azure Cosmos-hogyan díjszabásának főbb jellemzői](how-pricing-works.md)
* Tudjon meg többet [optimalizálása fejlesztéshez és teszteléshez](optimize-dev-test.md)
* Tudjon meg többet [az Azure Cosmos DB-elszámolások ismertetése](understand-your-bill.md)
* Tudjon meg többet [átviteli költségek optimalizálása](optimize-cost-throughput.md)
* Tudjon meg többet [tárolási költségek optimalizálása](optimize-cost-storage.md)
* Tudjon meg többet [olvasási és írási a költségek optimalizálása](optimize-cost-reads-writes.md)
* Tudjon meg többet [többrégiós Azure Cosmos-fiókok költségeinek optimalizálása](optimize-cost-regions.md)
* Tudjon meg többet [Azure Cosmos DB lefoglalt kapacitás](cosmos-db-reserved-capacity.md)

