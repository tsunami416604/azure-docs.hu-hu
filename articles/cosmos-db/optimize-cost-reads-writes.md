---
title: Az olvasások és írások költségének optimalizálása az Azure Cosmos DB-ben
description: Ez a cikk bemutatja, hogyan csökkentheti az Azure Cosmos DB költségeit olvasási és írási műveletek végrehajtása során az adatokon.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 725876594a7e7c5f3b3a02802f487dc5fdfb64dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535935"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Olvasási és írási költség optimalizálása az Azure Cosmos DB-ben

Ez a cikk ismerteti, hogyan számítja ki az Azure Cosmos DB adatok olvasásához és írásához szükséges költségeket. Az olvasási műveletek közé tartoznak az elemek beolvasási műveletei, valamint az írási műveletek közé tartozik a beszúrási, csere-, törlés- és -upsert elemek.  

## <a name="cost-of-reads-and-writes"></a>Olvasási és írási költségek

Az Azure Cosmos DB garantálja a kiszámítható teljesítményt az átviteli teljesítmény és a késés egy kiépített átviteli modell használatával. A kiosztott átviteli szint másodpercenkénti [kérelemegységek](request-units.md) ben vagy RU/s-ban jelenik meg. A kérelemegység (RU) egy logikai absztrakció számítási erőforrások, például a CPU, memória, IO, stb, amelyek szükségesek a kérelem végrehajtásához. A kiosztott átviteli képesség (RUs) van félretéve, és a tároló vagy az adatbázis számára, hogy kiszámítható átviteli és késési. A kiépített átviteli teljesítmény lehetővé teszi, hogy az Azure Cosmos DB kiszámítható és konzisztens teljesítményt, garantált alacsony késést és magas rendelkezésre állást biztosítson bármilyen méretekben. A kérelemegységek a normalizált pénznemet jelölik, amely egyszerűsíti az alkalmazás igényeinek hány erőforrására vonatkozó érvelést. 

Nem kell gondolkodni a kérelem egységek közötti olvasási és írási. A kérelemegységek egységes pénznemmodellje hatékonyságot hoz létre, hogy felcserélhetően használja ugyanazt az átviteli kapacitást mind az olvasáshoz, mind az íráshoz. Az alábbi táblázat az 1 KB és 100 KB méretű cikkek beolvasásának és írási költségének költségét mutatja be.

|**Cikk mérete**  |**Egy olvasás költsége** |**Egy írás költsége**|
|---------|---------|---------|
|1 KB |1 KT |5 rus |
|100 KB |10 RU |50 rus |

Egy 1 KB méretű cikk olvasása egy VT-be kerül. Egy 1 KB-os cikk írása öt termékegységbe kerül. Az írási és írási költségek az alapértelmezett [munkamenet-konzisztenciaszint](consistency-levels.md)használataesetén alkalmazandók.  A javak körül a szempontok a következők: elemméret, tulajdonságszám, adatkonzisztencia, indexelt tulajdonságok, indexelés és lekérdezési minták.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Az írások és olvasások költségének optimalizálása

Írási műveletek végrehajtásakor elegendő kapacitást kell kiépítenie a szükséges írások másodpercenkénti számának támogatásához. Növelheti a kiosztott átviteli keresztül az SDK, portál, CLI használatával az írási műveletek végrehajtása előtt, és majd csökkentse az átviteli szintet az írási műveletek befejezése után. Az írási időszak átviteli értéke a megadott adatokhoz szükséges minimális átviteli, valamint a beszúrási számítási feladatokhoz szükséges átviteli, feltételezve, hogy más számítási feladatok nem futnak. 

Ha egyidejűleg más számítási feladatokat is futtat, például lekérdezést/olvasást/frissítést/törlést, hozzá kell adnia a műveletekhez szükséges további kérésegységeket is. Ha az írási műveletek díja korlátozott, testre szabhatja az újrapróbálkozási/visszalépési szabályzatot az Azure Cosmos DB SDK-k használatával. Például növelheti a terhelést, amíg egy kis sebességű kérelmek lesz sebesség-korlátozott. Ha a sebességkorlát bekövetkezik, az ügyfélalkalmazásnak vissza kell lépnie a megadott újrapróbálkozási időköz díjkorlátozó kérelmeiről. Az írások újrapróbálkozása előtt minimális időközt kell rendelkeznie az újrapróbálkozások között. Az újrapróbálkozási házirendek támogatása megtalálható az SQL .NET, Java, Node.js és Python SDK-kban, valamint a .NET Core SDK-k összes támogatott verziójában. 

Az Azure Data Factory használatával tömegesen is beilleszthet adatokat az Azure Cosmos DB-be, vagy bármely támogatott forrásadat-tárból adatokat másolhat az Azure [Cosmos](../data-factory/connector-azure-cosmos-db.md)DB-ba. Az Azure Data Factory natív módon integrálható az Azure Cosmos DB tömeges API-val, hogy a legjobb teljesítményt nyújtsa, amikor adatokat ír.

## <a name="next-steps"></a>További lépések

Ezután az alábbi cikkekkel többet tudhat meg a költségoptimalizálásról az Azure Cosmos DB-ben:

* További információ [az optimalizálásról fejlesztési és tesztelési célokra](optimize-dev-test.md)
* További információ [az Azure Cosmos DB-számlájának ismertetéséről](understand-your-bill.md)
* További információ az [átviteli költség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási költség optimalizálásáról](optimize-cost-storage.md)
* További információ [a lekérdezések költségének optimalizálásáról](optimize-cost-queries.md)
* További információ a több régióra kiterjedő [Azure Cosmos-fiókok költségeinek optimalizálásáról](optimize-cost-regions.md)
