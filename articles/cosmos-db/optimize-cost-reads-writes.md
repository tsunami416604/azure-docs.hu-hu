---
title: Költségeinek optimalizálása beolvassa, és az Azure Cosmos DB-ben
description: Ez a cikk azt ismerteti, azt ismerteti, hogyan lehet Azure Cosmos DB végrehajtásakor olvasási és írási műveletek az adatokon.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 0cb40e4cbbfccaaf60e53010603e1d547e45dab9
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263810"
---
# <a name="optimize-the-cost-required-to-read-and-write-data-from-azure-cosmos-db"></a>A szükséges adatok olvasását és írását az Azure Cosmos DB költségek optimalizálása

Ez a cikk bemutatja, hogyan kerül kiszámításra a költsége az Azure Cosmos DB az adatok írását és olvasását. Olvasási műveletnek számítanak többek között a cikkek a get műveletek, és az írási műveletek közé tartozik az insert, replace, delete és upsert elemek.  

## <a name="cost-of-reads-and-writes"></a>Olvasási és írási költség

Az Azure Cosmos DB garantálja, hogy a kiszámítható teljesítmény szempontjából teljesítménye és adatelérési sebessége a kiosztott átviteli sebesség modell használatával. Az átviteli sebesség kiosztott részéért, hogy jelölt [kérelemegység](request-units.md) másodpercenként vagy RU/s. Egy ilyen kérelemegység (RU) egy logikai absztrakciós a számítási erőforrások, például a Processzor, memória, IO, stb. a kérés végrehajtásához szükséges. A kiosztott átviteli sebesség (RU) elkülönített és dedikált a tároló vagy adatbázist biztosít, kiszámítható teljesítménye és adatelérési sebessége. Kiosztott átviteli sebesség lehetővé teszi, hogy az Azure Cosmos DB garantáltan alacsony késéssel és magas rendelkezésre állás bármilyen méretben kiszámítható és következetes teljesítményt biztosít. Kérelemegység képviseli, amely leegyszerűsíti az indoklást hány erőforrásokról egy alkalmazásnak kell normalizált pénznem. 

Nem kell állításoknak sokoldalúbbá kérelemegység olvasási és írási között. Kérelemegység egységes pénznem modelljét növelhető a hatékonyság az olvasásokat és az írásokat az azonos átviteli kapacitás felcserélhető használandó hoz létre. Az alábbi táblázat olvasások költségeit mutatja, és írás tekintetében RU/s a elemek 1 KB-os és 100 KB-nál.

|**Elem mérete**  |**Egy olvasási költsége** |**Egy írási költsége**|
|---------|---------|---------|
|1 KB |1 RU |5 Kérelemegységet |
|100 KB |10 RU |50 kérelemegység |

Egy elem, amely 1 KB méretű költségek egy RU olvasása. Egy elem, amely 1 KB-os írása költségek öt fenntartott egységek. Az alapértelmezett munkamenet használata esetén alkalmazandók, az olvasási és írási költségek [konzisztenciaszint](consistency-levels.md).  A fenntartott egységek körül szempontok közé tartoznak: mérete, a tulajdonság száma, az adatkonzisztencia, a indexelt tulajdonságok, indexelést, elemet, és minták lekérdezése.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>1 millió normalizált költsége olvasási és írási

Kiépítés 1000 RU/s a rendszer lefordítja arra 3.6-os millió RU/óra és fogja terhelni az órát 0,08 (az USA és Európa). Egy 1 KB-os elem esetében 3.6-os millió olvasási vagy írási 0,72 millió hajthat végre (Ez az érték a következő képlettel: `3.6 million RU / 5`) a létesített átviteli sebesség révén óránként. Egy millió olvasási és írási normalizálva, a költség lenne $0,022 1 millió olvasási (Ez az érték a következő képlettel: $0.08/3.6 millió) és 1 millió írást a 0.111 $ (ezt az értéket a következő képlettel: $0.08/0.72 millió).

## <a name="number-of-regions-and-the-request-units-cost"></a>Régiók és a költségek kérelemegységek számát

Az írások költsége attól függetlenül, az Azure Cosmos-fiókjához társított régiók számának állandó. Más szóval egy 1 KB-os írási ára a fiókhoz társított régiók számának független öt fenntartott egységek. Nincs egy nem triviális replikál, elfogadása és minden régióban az adatreplikációs forgalmat feldolgozó töltött erőforrás-mennyiséggel. Többrégiós költségek optimalizálása kapcsolatos részletekért lásd: [többrégiós Cosmos-fiókok költségeinek optimalizálása](optimize-cost-regions.md) cikk.

## <a name="optimize-the-cost-of-writes-and-reads"></a>Írási és olvasási optimalizálása

Írási műveletek elvégzésekor, támogatásához szükséges másodpercenként írások száma elegendő kapacitással kell kiépítenie. A kiosztott átviteli sebesség SDK,-val növelheti az írási műveletek végrehajtása előtt a CLI-portálon, és csökkentse az átviteli sebességet, az írási műveletek után. Az átviteli sebességet, az írási időszak, a minimális átviteli sebesség az adott adat szükséges, valamint a Beszúrás munkaterhelés feltéve nincs más számítási feladatok futnak a szükséges átviteli sebességtől. 

Ha egyéb számítási feladatokat egy időben futnak, például lekérdezési/olvasása/frissítése/törlése, hozzá kell adnia az ezekhez a műveletekhez szükséges túl kérelemegységeket. Ha az írási műveletek sebessége korlátozott, testre szabható az újrapróbálkozási/leállítási házirendet az Azure Cosmos DB SDK-k használatával. Növelheti például a terhelés, mindaddig, amíg egy kis méretű kérések másodpercenkénti száma lekérdezi sebessége korlátozott. Sebességkorlát-történik, ha az ügyfélalkalmazás készítsen a sebességhatárolt található a megadott újrapróbálkozási időköz kérelmeket. Mielőtt újra próbálkozna írást, az újrapróbálkozások közötti időkülönbség mennyisége minimális kell rendelkeznie. Újrapróbálkozási házirend támogatási SQL .NET, Java, Node.js, és a Python SDK-k és a támogatott verziók a .NET Core SDK-k tartalmazza. 

Tömeges beszúrás adatokat az Azure Cosmos DB-be vagy is másolása az adatok bármely támogatott forrásadattárból az Azure Cosmos DB használatával [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Az Azure Data Factory natív módon integrálható az Azure Cosmos DB tömeges API ír adatokat a legjobb teljesítmény érdekében szolgáltatni.

## <a name="next-steps"></a>További lépések

Ezután folytassa további tudnivalók a költségek optimalizálása az Azure Cosmos DB az alábbi cikkeket:

* Tudjon meg többet [optimalizálása fejlesztéshez és teszteléshez](optimize-dev-test.md)
* Tudjon meg többet [az Azure Cosmos DB-elszámolások ismertetése](understand-your-bill.md)
* Tudjon meg többet [átviteli költségek optimalizálása](optimize-cost-throughput.md)
* Tudjon meg többet [tárolási költségek optimalizálása](optimize-cost-storage.md)
* Tudjon meg többet [lekérdezések költségeinek optimalizálása](optimize-cost-queries.md)
* Tudjon meg többet [többrégiós Azure Cosmos-fiókok költségeinek optimalizálása](optimize-cost-regions.md)
