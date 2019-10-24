---
title: A kérelmek egységének optimalizálása és a Azure Cosmos DB-lekérdezések futtatásának díja
description: Megtudhatja, hogyan értékelheti ki a lekérdezésekre vonatkozó kérések egységeit, és hogyan optimalizálhatja a lekérdezést a teljesítmény és a költség tekintetében.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 376c1a32a70951448b35a4c02022719229a3aad2
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753306"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>A lekérdezési díjak optimalizálása Azure Cosmos DB

A Azure Cosmos DB az adatbázis-műveletek gazdag készletét kínálja, beleértve a tárolóban lévő elemeken működő viszonyítási és hierarchikus lekérdezéseket. Az egyes műveletekhez kapcsolódó díjak a művelet végrehajtásához szükséges CPU, IO és memória alapján változnak. A hardveres erőforrások gondolkodása és kezelése helyett a kérések egysége (RU) egyetlen mértékként használható a különböző adatbázis-műveletek végrehajtásához szükséges erőforrások esetében. Ez a cikk azt ismerteti, hogyan értékelhető ki a lekérdezésre vonatkozó kérési egység, és hogyan optimalizálható a lekérdezés a teljesítmény és a költség tekintetében. 

A Azure Cosmos DB lekérdezéseit általában a leggyorsabb/leghatékonyabb értékről lassabb/kevésbé hatékonyra, a következőképpen kell megrendelni az átviteli sebesség tekintetében:  

* Művelet beolvasása egyetlen partíciós kulcs és egy elem kulcsa alapján.

* Lekérdezés szűrő záradékkal egyetlen partíciós kulcson belül.

* Lekérdezés egyenlőség vagy Range Filter záradék nélkül bármely tulajdonság esetében.

* Lekérdezés szűrő nélkül.

Az egy vagy több partícióból beolvasott lekérdezések nagyobb késéssel járnak, és nagyobb számú kérést használnak fel. Mivel minden partíció automatikus indexelést tartalmaz az összes tulajdonsághoz, a lekérdezés hatékonyan kiszolgálható az indexből. A párhuzamossági beállítások használatával több partíciót használó lekérdezéseket is készíthet. A particionálással és a partíciós kulcsokkal kapcsolatos további tudnivalókért lásd: [particionálás Azure Cosmos DBban](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Lekérdezési egységre vonatkozó kérések kiértékelése

Ha az Azure Cosmos-tárolókban tárolt néhány adattal, a lekérdezések létrehozásához és futtatásához a Azure Portal Adatkezelő is használhatja. A lekérdezések költségeit az adatkezelővel is lekérheti. Ezzel a módszerrel a rendszer által támogatott tipikus lekérdezésekkel és műveletekkel kapcsolatos tényleges költségek is megadhatók.

Az SDK-k segítségével programozott módon is lekérheti a lekérdezések költségeit. Az olyan műveletek terhelésének méréséhez, mint például a létrehozás, az Update vagy a DELETE, a REST API használatakor vizsgálja meg a `x-ms-request-charge` fejlécét. Ha a .NET-et vagy a Java SDK-t használja, a `RequestCharge` tulajdonság a kérések díjainak megfelelő tulajdonság, és ez a tulajdonság a ResourceResponse vagy a FeedResponse belül található.

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

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Egy lekérdezésre vonatkozó kérési egység díját befolyásoló tényezők

A lekérdezések kérelmi egységei számos tényezőtől függenek. Például a betöltött/visszaadott Azure Cosmos-elemek száma, az indexen végzett keresések száma, a lekérdezés fordítási ideje stb. részleteit. Azure Cosmos DB garantálja, hogy ugyanaz a lekérdezés, amikor ugyanazon az adattárban hajtja végre ugyanazt a kérést, még ismétlődő végrehajtás esetén is ugyanazokat a kérelmeket fogja használni. A lekérdezés-végrehajtási mérőszámokat használó lekérdezési profil jó ötlet a kérési egységek elköltésének módjára.  

Bizonyos esetekben előfordulhat, hogy egy 200-es és 429-as számú választ, valamint változó kérési egységeket fog látni a lekérdezések lapozható végrehajtása során, mert a lekérdezések az elérhető RUs alapján a lehető leggyorsabban futnak. Előfordulhat, hogy a lekérdezés végrehajtásának megszakítása több oldalra vagy oda, a kiszolgáló és az ügyfél között. Például az 10 000-es elemek több oldalként is visszaadhatók, az egyes díjak az adott laphoz végrehajtott számítás alapján. Ezen lapok összegzése esetén ugyanannyi RUst kell lekérnie, mint a teljes lekérdezéshez.  

## <a name="metrics-for-troubleshooting"></a>A hibaelhárítás metrikái

A lekérdezések által felhasznált teljesítmény és átviteli sebesség, valamint a felhasználó által definiált függvények (UDF-EK) többnyire a Function törzstől függenek. A legkönnyebben megtudhatja, hogy mennyi idő telik el a lekérdezés végrehajtásának az UDF-ben való elköltése és a felhasznált RUs száma. ehhez engedélyezze a lekérdezési metrikákat. Ha a .NET SDK-t használja, az SDK a következő lekérdezési mérőszámokat adja vissza:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Ajánlott eljárások a lekérdezések optimalizálásához 

Vegye figyelembe a következő ajánlott eljárásokat a lekérdezések a Cost szolgáltatáshoz való optimalizálásakor:

* **Több entitás típusának elhelyezése**

   Próbáljon meg több entitást is elhelyezni egyetlen vagy kevesebb tárolón belül. Ez a módszer nem csupán árképzési szempontból, hanem lekérdezés-végrehajtáshoz és tranzakcióhoz is biztosít előnyöket. A lekérdezések hatóköre egyetlen tárolóra terjed ki. a tárolt eljárások/eseményindítók használatával több rekordon keresztüli atomi tranzakciók hatóköre egyetlen tárolón belüli partíciós kulcsra terjed ki. Az azonos tárolóban lévő entitások közös elhelyezésével csökkentheti a hálózati kerek utak számát a rekordok közötti kapcsolatok feloldásához. Így növeli a végpontok közötti teljesítményt, lehetővé teszi, hogy az atomi tranzakciók több rekordon is elérhetők legyenek egy nagyobb adatkészletnél, és ennek eredményeképpen csökkennek a költségek. Ha egy vagy több típusú tárolóban több entitást is megnehezítik a forgatókönyv esetén, általában azért, mert egy meglévő alkalmazást telepít át, és nem kívánja végrehajtani a kód módosítását, érdemes megfontolnia a kiépítés elvégzését. átviteli sebesség az adatbázis szintjén.  

* **Az alacsonyabb kérelmek egységének mérése és finomhangolása/második használat**

   A lekérdezés bonyolultsága befolyásolja, hogy hány kérési egység (RUs) van használatban egy művelethez. A predikátumok száma, a predikátumok természete, a UDF száma és a forrásadatok mérete. Az összes tényező befolyásolja a lekérdezési műveletek költségeit. 

   A kérelem fejlécében visszaadott kérelem díja jelzi az adott lekérdezés költségeit. Ha például egy lekérdezés 1000 1 – KB elemet ad vissza, a művelet díja 1000. Mint ilyen, egy másodpercen belül a kiszolgáló csak két ilyen kérést értékel ki a későbbi kérelmek korlátozása előtt. További információ: a [kérelmek egységei](request-units.md) cikk és a kérési egység kalkulátora. 

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat talál a Azure Cosmos DB a Cost optimizationról:

* További információ az [Azure Cosmos díjszabásának működéséről](how-pricing-works.md)
* További információ a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a több régióból álló Azure Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)
* További információ a [Azure Cosmos db fenntartott kapacitásról](cosmos-db-reserved-capacity.md)

