---
title: A költségek és a RU/ek optimalizálása lekérdezések futtatásához az Azure Cosmos DB-ben
description: Ismerje meg, hogyan értékelheti ki a lekérdezés kérelemegység-díjait, és hogyan optimalizálhatja a lekérdezést a teljesítmény és a költség szempontjából.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: dd75ad4ed1024292868f113e474fe8b8b73679b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445133"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>A lekérdezési költségek optimalizálása az Azure Cosmos DB-ben

Az Azure Cosmos DB többféle olyan adatbázis-műveletet biztosít, amelyek a tárolókban lévő elemekre vonatkoznak, beleértve a relációs és hierarchikus lekérdezéseket is. Az egyes ilyen műveletekhez kapcsolódó költségek a művelet végrehajtásához szükséges CPU, IO és memória függvényében változnak. A hardveres erőforrások használata és kezelése helyett a kérelemegységet tekintheti a kérelmet kiszolgáló különböző adatbázis-műveletek erőforrásaira vonatkozó egyetlen mértékegységnek. Ez a cikk azt írja le, hogyan értékelhetők ki egy lekérdezés kérelemegység-költségei, és hogyan optimalizálható a lekérdezés a teljesítmény és a költség tekintetében. 

Az Azure Cosmos DB lekérdezései általában a leggyorsabb/leghatékonyabb tól a lassabbig/kevésbé hatékonyig vannak rendezve az átviteli érték tekintetében az alábbiak szerint:  

* Get művelet egyetlen partíciókulcson és elemkulcson.

* Egyetlen partíciókulcson belüli szűrőzáradékkal rendelkező lekérdezés.

* Lekérdezés egyenlőség vagy tartományszűrő záradék nélkül bármely tulajdonságon.

* Lekérdezés szűrők nélkül.

Lekérdezések, amelyek egy vagy több partícióadatait olvassa fel, nagyobb késést eredményeznek, és nagyobb számú kérelemegységet használnak fel. Mivel minden partíció automatikus indexelésaz összes tulajdonság, a lekérdezés hatékonyan kiszolgálható az indexből. A több partíciót használó lekérdezéseket gyorsabbá teheti a párhuzamossági beállítások kal. A particionálásról és a partíciókulcsokról az [Azure Cosmos DB particionálása.](partitioning-overview.md)

## <a name="evaluate-request-unit-charge-for-a-query"></a>Lekérdezés kérelemegység-költségének kiértékelése

Miután tárolt néhány adatot az Azure Cosmos-tárolókban, használhatja az Azure Portalon az Adatkezelő t a lekérdezések létrehozásához és futtatásához. A lekérdezések költségét az adatkezelő vel is megkaphatja. Ez a módszer a rendszer által támogatott tipikus lekérdezések és műveletek tényleges díjainak érzékelését teszi leadott.

A lekérdezések költségét programozott módon is lehetővé teheti az SDK-k használatával. Bármely művelet, például létrehozása, frissítése vagy törlése `x-ms-request-charge` a repapi használatakor a fejléc általános terhelésének mérésére. Ha a .NET vagy a Java SDK, a `RequestCharge` tulajdonság az egyenértékű tulajdonság a kérelem díját, és ez a tulajdonság a ResourceResponse vagy a FeedResponse.

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

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>A lekérdezés kérelemegység-díját befolyásoló tényezők

A lekérdezések kérési egységei számos tényezőtől függenek. Például az Azure Cosmos-elemek betöltve/visszaadott száma, az indexen lévő lekérdezések száma, a lekérdezés fordítási ideje stb. Az Azure Cosmos DB garantálja, hogy ugyanazt a lekérdezést, ha végre ugyanazon az adaton mindig ugyanolyan számú kérelem egységek még ismételt végrehajtások. A lekérdezés-végrehajtási metrikákat használó lekérdezési profil jó képet ad a kérelemegységek elköltéséről.  

Bizonyos esetekben előfordulhat, hogy egy 200 és 429 válaszsorozat, és a változó kérelem egységek egy lapozható lekérdezések végrehajtása, azaz azért, mert a lekérdezések a lehető leggyorsabban futnak a rendelkezésre álló válaszegységek alapján. Előfordulhat, hogy egy lekérdezés-végrehajtás több oldalra/oda-vissza utazásra tör be a kiszolgáló és az ügyfél között. Például 10 000 elem több oldalként is visszaküldhető, amelyek mindegyike az adott oldalon végrehajtott számítás alapján kerül felszámításra. Ha ezeken az oldalakon összegez, ugyanannyi rt-t kell kapnia, mint amennyit a teljes lekérdezéshez kapna.  

## <a name="metrics-for-troubleshooting"></a>Hibaelhárítási mutatók

A lekérdezések, a felhasználó által definiált függvények (UDF-ek) által felhasznált teljesítmény és átviteli teljesítmény nagyrészt a függvény törzsétől függ. A legegyszerűbb módja annak, hogy megtudja, hogy mennyi időt töltött a lekérdezés végrehajtása az UDF-ben, és a felhasznált felhasználói szolgáltatások száma, a lekérdezési metrikák engedélyezésével. Ha a .NET SDK-t használja, az SDK által visszaadott mintalekérdezési metrikák:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Gyakorlati tanácsok a lekérdezések költségoptimalizálásához 

A lekérdezések költségre vonatkozó optimalizálásakor vegye figyelembe az alábbi gyakorlati tanácsokat:

* **Több entitástípus közös áthelyezése**

   Próbáljon meg több entitástípust egyetlen vagy kisebb számú tárolón belül elhelyezni. Ez a módszer nem csak árképzési szempontból eredményez előnyöket, hanem lekérdezés-végrehajtás és tranzakciók esetén is. A lekérdezések hatóköre egyetlen tárolóba van hatóköre; és atomi tranzakciók több rekord on tárolt eljárások/eseményindítók hatóköre egy partíciókulcs egyetlen tárolón belül. Az entitások ugyanazon tárolón belüli közös elhelyezése csökkentheti a hálózati adatváltások számát a rekordok közötti kapcsolatok feloldásához. Így növeli a végpontok között a teljesítményt, lehetővé teszi az atomi tranzakciók több rekordot egy nagyobb adatkészlet, és ennek eredményeként csökkenti a költségeket. Ha több entitástípus egyetlen vagy kisebb számú tárolón belüli közös elhelyezése nehéz a forgatókönyv ben, általában azért, mert egy meglévő alkalmazást telepít át, és nem szeretne kódmódosításokat végrehajtani - érdemes megfontolnia a kiépítést az adatbázis szintjén.  

* **Kisebb kérési egységek/másodperchasználat mérése és hangolása**

   A lekérdezés összetettsége hatással van arra, hogy hány kérelemegység (RUs) használ egy művelethez. A predikátumok száma, a predikátumok jellege, az UDF-ek száma és a forrásadatkészlet mérete. Mindezek a tényezők befolyásolják a lekérdezési műveletek költségét. 

   A kérelem fejlécében visszaadott kérelemdíj egy adott lekérdezés költségét jelzi. Ha például egy lekérdezés 1000 1 KB-os cikket ad vissza, a művelet költsége 1000. Mint ilyen, egy másodpercen belül a kiszolgáló csak két ilyen kérést tart tiszteletben, mielőtt korlátozza a későbbi kérelmek et. További információt a [Kérelemegységek](request-units.md) és a kérelemegység-kalkulátor című témakörben talál. 

## <a name="next-steps"></a>További lépések

Ezután az alábbi cikkekkel többet tudhat meg a költségoptimalizálásról az Azure Cosmos DB-ben:

* További információ az [Azure Cosmos díjszabásának működéséről](how-pricing-works.md)
* További információ [az optimalizálásról fejlesztési és tesztelési célokra](optimize-dev-test.md)
* További információ [az Azure Cosmos DB-számlájának ismertetéséről](understand-your-bill.md)
* További információ az [átviteli költség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási költség optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási költségek optimalizálásáról](optimize-cost-reads-writes.md)
* További információ a több régióra kiterjedő [Azure Cosmos-fiókok költségeinek optimalizálásáról](optimize-cost-regions.md)
* További információ az [Azure Cosmos DB fenntartott kapacitásáról](cosmos-db-reserved-capacity.md)

