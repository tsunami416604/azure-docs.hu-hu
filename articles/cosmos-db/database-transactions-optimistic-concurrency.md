---
title: Adatbázis-tranzakciók és az Azure Cosmos DB az optimista egyidejűség-vezérléssel
description: Ez a cikk ismerteti az adatbázis-tranzakciók és az Azure Cosmos DB az optimista egyidejűség-vezérléssel
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 568f47aacf39793d4c2da46798682abc002ca33b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59279508"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Tranzakciók és optimista egyidejűség vezérlése

Adatbázis-tranzakciók adjon meg egy biztonságos és kiszámítható programozási modell segítségével az adatok egyidejű módosításait foglalkozik. Hagyományos relációs adatbázisoktól, például az SQL Server lehetővé teszi, hogy a használatával, és/vagy a tárolt eljárások, eseményindítók üzleti logika írását küldje el a kiszolgálónak a végrehajtási közvetlenül az adatbázismotor belül. A hagyományos relációs adatbázisoktól szükségesek kezelésére két különböző programozási nyelvekhez programozási nyelvet, például a JavaScript, Python, a (nem tranzakciós) alkalmazás C#, Java és az egyéb és a tranzakciós programozási nyelvet () például a T-SQL), amely natív módon által végrehajtott az adatbázisban.

Az adatbázismotor, az Azure Cosmos DB támogatja a teljes ACID (atomitást, konzisztencia, elkülönítés, tartósság) megfelelő tranzakciót a pillanatkép-elkülönítés. Minden az adatbázis-műveletek a hatókörön belül egy adott tároló [logikai partíció](partition-data.md) tranzakciós szempontból tevékenységében az adatbázismotor által a partícióhoz tartozó replika futtatott. Ezek a műveletek is tartalmazza (a logikai partíció található egy vagy több elem frissítése) írási és olvasási műveletek. Az alábbi táblázatban látható, különböző műveleteket és transcation típusok:

| **Művelet**  | **Művelet típusa** | **Egyetlen vagy több elem tranzakció** |
|---------|---------|---------|
| (Nélkül előkészítő/utólagos eseményindító) beszúrása | Írás | Egyetlen elem tranzakció |
| (Egy eseményindítóval előkészítő/utólagos) beszúrása | Írási és olvasási | Több elem tranzakció |
| Cserélje le (nélkül előkészítő/utólagos eseményindító) | Írás | Egyetlen elem tranzakció |
| Cserélje le (előkészítő/utólagos eseményindító) | Írási és olvasási | Több elem tranzakció |
| Az Upsert (nélkül előkészítő/utólagos eseményindító) | Írás | Egyetlen elem tranzakció |
| Az Upsert (egy eseményindítóval előkészítő/utólagos) | Írási és olvasási | Több elem tranzakció |
| Törlés (nélkül előkészítő/utólagos eseményindító) | Írás | Egyetlen elem tranzakció |
| (Egy eseményindítóval előkészítő/utólagos) törlése | Írási és olvasási | Több elem tranzakció |
| Tárolt eljárás végrehajtása | Írási és olvasási | Több elem tranzakció |
| Rendszer által kezdeményezett egy merge eljárás végrehajtása | Írás | Több elem tranzakció |
| Rendszer által kezdeményezett végrehajtása egy elem lejáratának (TTL) alapján elemek törlése | Írás | Több elem tranzakció |
| Olvasás | Olvasás | Egyetlen elemből tranzakció |
| Módosítási hírcsatorna | Olvasás | Több elem tranzakció |
| Többoldalas olvasása | Olvasás | Több elem tranzakció |
| Többoldalas lekérdezések | Olvasás | Több elem tranzakció |
| Hajtsa végre a többoldalas lekérdezés részeként UDF | Olvasás | Több elem tranzakció |

## <a name="multi-item-transactions"></a>Több elem tranzakciók

Az Azure Cosmos DB lehetővé teszi, hogy írási [tárolt eljárások, előkészítő/utólagos eseményindítók, felhasználó által definiált-funkciókat (UDF)](stored-procedures-triggers-udfs.md) és egyesíti az egyes eljárások a JavaScript. Az Azure Cosmos DB natív módon támogatja a JavaScript végrehajtása az adatbázis motorjában. Tárolt eljárások regisztrálhatja, az előkészítő/utólagos eseményindítók, a felhasználói--függvények (UDF) és a egy tárolót, és később egyesítési eljárások végre őket tranzakciós szempontból az Azure Cosmos database engine belül. A JavaScript alkalmazáslogikát írása lehetővé teszi a természetes kifejezési módja átvitelvezérlés, változó hatókörkezeléséhez kapcsolódó, hozzárendelése és integrációs kivételkezelési primitívek belül az adatbázis-tranzakciók közvetlenül a JavaScript nyelven.

A JavaScript-alapú tárolt eljárások, eseményindítók, felhasználói függvényeket és egyesítés eljárások vannak tördelve a pillanatkép-elkülönítés egy környezeti ACID tranzakción belül minden elem a logikai partíción belül. A végrehajtása során a JavaScript program kivételt jelez, ha a teljes tranzakció megszakad, és helyzetben előzőre. Az eredményül kapott programozási modell használata egyszerű mégis hatékony. JavaScript-fejlesztőinek közben továbbra is használja a jól ismert nyelv utasításaival tartós programozási modell és a könyvtár primitívek első.

A JavaScript végrehajtására közvetlenül az adatbázismotor belül biztosít teljesítmény és a tároló elemek kapcsolatos művelet-végrehajtási adatbázis-tranzakciós végrehajtásához. Továbbá mivel az Azure Cosmos database engine natív módon támogatja a JSON és a JavaScript, nincs semmilyen impedanica-eltérés a típus rendszerek, alkalmazások és az adatbázis között.

## <a name="optimistic-concurrency-control"></a>Az optimista egyidejűség-vezérlés 

Az optimista egyidejűség-vezérlés lehetővé teszi, hogy megakadályozza az elveszett frissítések, és törli. A normál pesszimista zárolással az adatbázismotor, a logikai partíciót az elemet birtokló által üzemeltetett vetik egyidejű, ütköző művelet. Amikor két egyidejű művelet próbálja meg frissíteni a logikai partíció található elem a legújabb verzióra, az egyiket legyőzi, és a másik sikertelenek lesznek. Azonban próbál meg egyszerre az azonos elem frissítése egy vagy két művelet korábban rendelkezett olvasási egy régebbi elem értékét, az adatbázis nem tudja, ha korábban olvasott érték bármelyik vagy mindkettő az ütköző művelet volt valóban az elem a legújabb értékeket. Szerencsére a ebben a helyzetben a észlelhető a **optimista egyidejűség-vezérlés (OCC)** előtt látni a két művelet írja be a tranzakció határ az adatbázis motorjában. OCC felülírják a mások által elvégzett módosítások védi az adatokat. Azt is megakadályozza, hogy mások véletlenül a saját módosítások felülírása.

Egy elemet egyidejű frissítése az Azure Cosmos DB kommunikációs protokoll réteg által a OCC vannak kitéve. Az Azure Cosmos database biztosítja, hogy az elem frissítése (vagy törlése) ügyféloldali verziója ugyanaz, mint a verzió a cikk az Azure Cosmos-tárolóban. Ez garantálja, hogy az írási mások, és ez fordítva is igaz az írások véletlenül felülírásra védettek. Többfelhasználós környezetben az optimista egyidejűség-vezérlés megóvja a véletlen törlését, vagy helytelen verziójára figyelmeztet elem frissítése. Emiatt a elemek védve legyenek a infamous "elveszett update" vagy "elveszett törlés" problémákat.

Minden Azure-Cosmos-tárolóban tárolt elemnek egy rendszer által meghatározott `_etag` tulajdonság. Értékét a `_etag` automatikusan létrehozza és frissíti a kiszolgáló minden alkalommal, amikor az elem frissítése. `_etag` az ügyfél által megadott használható `if-match` kérelemfejlécből, hogy a kiszolgáló eldönteni, hogy egy elem feltételesen frissíthetők. Értékét a `if-match` fejléc értéke megegyezik a `_etag` a kiszolgálón, majd frissül az elemet. Ha az értéke a `if-match` kérelem fejléce már nem aktuális, a kiszolgáló elutasítja a műveletet egy "HTTP 412 sikertelen előfeltétel" válaszüzenet. Az ügyfél ezután is újra beolvasni az elem a kiszolgáló aktuális verziójának beszerzéséhez és nem írhatók felül a cikk a kiszolgáló a saját verzióját az elemet `_etag` az elem értékét. Emellett `_etag` együtt a `if-none-match` meghatározni, hogy szükség van-e egy adott erőforrás egy refetch fejléc. 

Az elem `_etag` érték változik, minden alkalommal, amikor az elem frissítése. A Csere elem műveletek `if-match` kell explicit módon megadni a kérelem beállítások részeként. Egy vonatkozó példáért tekintse meg a mintakód a [GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446). `_etag` értékeket implicit módon ellenőrzi csinált semmit a tárolt eljárás minden írásos elemet. Ha bármely ütközést észlel, a tárolt eljárás állítsa vissza a tranzakciót, és kivételt. Ezzel a módszerrel a tárolt eljárás minden vagy nincs írási szolgáltatásfrissítést érvényesek. Ez a jel, az alkalmazás újból alkalmazni a frissítéseket, és ismételje meg az eredeti ügyfél kérelmet.

## <a name="next-steps"></a>További lépések

További információ adatbázis-tranzakciók és az optimista egyidejűség-vezérléssel az alábbi cikkeket:

- [Az Azure Cosmos-adatbázisok, tárolók és elemek használata](databases-containers-items.md)
- [Konzisztenciaszint](consistency-levels.md)
- [Ütközés típusa és a névfeloldási házirend](conflict-resolution-policies.md)
- [Tárolt eljárások, eseményindítók és felhasználó által definiált függvények](stored-procedures-triggers-udfs.md)
