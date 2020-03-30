---
title: Adatbázis-tranzakciók és optimista egyidejűség-szabályozás az Azure Cosmos DB-ben
description: Ez a cikk az Azure Cosmos DB adatbázis-tranzakcióit és optimista egyidejűség-szabályozását ismerteti
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: d453bb4071c4a6972e01b8f7e90375181caf6d01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806524"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Tranzakciók és optimista egyidejűség vezérlése

Az adatbázis-tranzakciók biztonságos és kiszámítható programozási modellt biztosítanak az adatok egyidejű változásainak kezeléséhez. A hagyományos relációs adatbázisok, például az SQL Server, lehetővé teszik az üzleti logika írását tárolt eljárások és/vagy eseményindítók használatával, és közvetlenül az adatbázis-motoron belül történő végrehajtásra küldik a kiszolgálónak. A hagyományos relációs adatbázisok esetében két különböző programozási nyelvet kell kezelnie a (nem tranzakciós) alkalmazásprogramozási nyelv, például a JavaScript, python, C#, Java stb.

Az Azure Cosmos DB adatbázis-motorja támogatja a teljes ACID (Atomicity, Consistency, Isolation, Tartósság) kompatibilis tranzakciókat pillanatkép-elkülönítéssel. A tároló [logikai partíciójának](partition-data.md) hatókörén belüli összes adatbázis-művelet tranzakciós végrehajtása a partíció replikája által üzemeltetett adatbázis-motoron belül történik. Ezek a műveletek írási (egy vagy több elem frissítése a logikai partíción belül) és olvasási műveleteket is tartalmaznak. Az alábbi táblázat a különböző műveleteket és tranzakciótípusokat mutatja be:

| **Művelet**  | **Művelettípus** | **Egy vagy többtételes tranzakció** |
|---------|---------|---------|
| Beszúrás (elő-/bejegyzésjelző nélkül) | Írás | Egytételes tranzakció |
| Beszúrás (elő-/bejegyzésjelzővel) | Írás és olvasás | Többtételes tranzakció |
| Csere (elő-/bejegyzésjelző nélkül) | Írás | Egytételes tranzakció |
| Csere (elő-/bejegyzésjelzőre) | Írás és olvasás | Többtételes tranzakció |
| Upsert (elő-/bejegyzésjelző nélkül) | Írás | Egytételes tranzakció |
| Upsert (elő-/bejegyzésjelzővel) | Írás és olvasás | Többtételes tranzakció |
| Törlés (elő-/bejegyzési eseményindító nélkül) | Írás | Egytételes tranzakció |
| Törlés (elő-/bejegyzési eseményindítóval) | Írás és olvasás | Többtételes tranzakció |
| Tárolt eljárás végrehajtása | Írás és olvasás | Többtételes tranzakció |
| A rendszer által kezdeményezett egyesítési eljárás végrehajtása | Írás | Többtételes tranzakció |
| A rendszer kezdeményezte az elemek törlésének végrehajtását egy elem lejárata (TTL) alapján | Írás | Többtételes tranzakció |
| Olvasás | Olvasás | Egytételes tranzakció |
| Változáscsatorna | Olvasás | Többtételes tranzakció |
| Törrelapozott olvasás | Olvasás | Többtételes tranzakció |
| Tördelt lekérdezés | Olvasás | Többtételes tranzakció |
| UDF végrehajtása a tördelt lekérdezés részeként | Olvasás | Többtételes tranzakció |

## <a name="multi-item-transactions"></a>Többtételes tranzakciók

Az Azure Cosmos DB lehetővé teszi [a tárolt eljárások írását, az eseményindítók előtti/közzétételét, a felhasználó által definiált függvényeket (UDF-ek)](stored-procedures-triggers-udfs.md) és a JavaScript-eljárások egyesítését. Az Azure Cosmos DB natívmódon támogatja a JavaScript-végrehajtást az adatbázis-motorjában. Regisztrálhatja a tárolt eljárásokat, elő-/postai eseményindítókat, felhasználó által definiált függvényeket (UDF- et) és egyesítheti az eljárásokat egy tárolón, majd később tranzakciós módon végrehajthatja azokat az Azure Cosmos adatbázis-motorján belül. Az alkalmazáslogika javascriptben történő írása lehetővé teszi a vezérlési folyamat természetes kifejezését, a változók hatókörét, hozzárendelését és a kivételkezelési primitívek integrálását az adatbázis-tranzakciókon belül közvetlenül a JavaScript-nyelven.

A JavaScript-alapú tárolt eljárások, eseményindítók, UDF-ek és egyesítési eljárások vannak csomagolva egy környezeti ACID tranzakció pillanatkép elkülönítése az összes elem a logikai partíción belül. Végrehajtása során, ha a JavaScript program kivételt tesz, a teljes tranzakció megszakad, és visszalesz állítva. Az így kapott programozási modell egyszerű, mégis erős. A JavaScript-fejlesztők tartós programozási modellt kapnak, miközben továbbra is használják a jól ismert nyelvi konstrukciókat és a könyvtárprimitíveket.

A JavaScript közvetlenül az adatbázis-motoron belül történő végrehajtásának lehetősége biztosítja az adatbázis-műveletek teljesítményét és tranzakciós végrehajtását a tároló elemeivel szemben. Továbbá, mivel az Azure Cosmos adatbázis-motor natívan támogatja a JSON és a JavaScript, nincs impedancia eltérés az alkalmazás típusrendszerei és az adatbázis között.

## <a name="optimistic-concurrency-control"></a>Optimista egyidejűség-szabályozás

Az optimista egyidejűség-vezérlés lehetővé teszi az elveszett frissítések és törlések megelőzését. Az egyidejű, ütköző műveleteket az elemet birtokló logikai partíció által üzemeltetett adatbázis-motor rendszeres pesszimista zárolásának veti alá. Ha két egyidejű művelet megpróbálja frissíteni egy elem legújabb verzióját egy logikai partíción belül, az egyik nyer, a másik pedig sikertelen lesz. Ha azonban egy vagy két művelet, amely egyidejűleg frissíti ugyanazt az elemet, korábban az elem régebbi értékét olvasta, az adatbázis nem tudja, hogy az előzőleg olvasott érték vagy az ütköző műveletek valóban az elem legutóbbi értéke volt-e. Szerencsére ez a helyzet észlelhető az **optimista egyidejűség-vezérlés (OCC)** használatával, mielőtt a két művelet beengedné a tranzakcióhatárba az adatbázis-motoron belül. Az OCC megvédi az adatokat attól, hogy véletlenül felülírják a mások által végrehajtott módosításokat. Azt is megakadályozza, hogy mások véletlenül felülírják a saját módosításait.

Egy elem egyidejű frissítéseit az Azure Cosmos DB kommunikációs protokollrétege az OCC-nek veti alá. Az Azure Cosmos-adatbázis biztosítja, hogy a frissítendő (vagy törlési) elem ügyféloldali verziója megegyezik az azure Cosmos-tárolóban lévő elem verziójával. Ez garantálja, hogy az írások védettek amások írásai által véletlenül felülírva, és fordítva. Többfelhasználós környezetben az optimista egyidejűség-vezérlés megvédi Önt az elem nem megfelelő verziójának véletlen törlésétől vagy frissítésétől. Mint ilyen, elemek van megvéd ellen a hírhedt " elveszett korszerűsíteni" vagy " elveszett töröl" probléma.

Az Azure Cosmos-tárolóban tárolt minden elem `_etag` rendelkezik egy rendszer által definiált tulajdonsággal. Az elem `_etag` minden frissítésekor a kiszolgáló automatikusan létrehozza és frissíti az érték értékét. `_etag`használható az ügyfél által `if-match` megadott kérelemfejléccel, hogy a kiszolgáló eldönthesse, hogy egy elem feltételesen frissíthető-e. A `if-match` fejléc értéke megegyezik a `_etag` kiszolgálón lévő értékével, majd frissül az elem. Ha a `if-match` kérelemfejléc értéke már nem aktuális, a kiszolgáló "HTTP 412 Előfeltétel hiba" válaszüzenettel utasítja el a műveletet. Az ügyfél ezután újra lehívhatja az elemet, hogy megszerezze az elem aktuális verzióját a kiszolgálón, vagy felülírhatja az elem verzióját a kiszolgálón a saját `_etag` értékével. Ezenkívül `_etag` a `if-none-match` fejléccel együtt meghatározhatja, hogy szükség van-e egy erőforrás újbóli betöltésére.

Az elem `_etag` értéke minden alkalommal megváltozik, amikor az elem frissül. A cserecikk-műveletek `if-match` esetében explicit módon kell kifejezni a kérelem beállításainak részeként. Például tekintse meg a mintakódot a [GitHubon.](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L578-L674) `_etag`a rendszer implicit módon ellenőrzi a tárolt eljárás által megérintett összes írott elemet. Ha bármilyen ütközést észlel, a tárolt eljárás visszaállítja a tranzakciót, és kivételt okoz. Ezzel a módszerrel vagy a tárolt eljáráson belüli összes írási vagy egyáltalán nem imént kerül alkalmazásra. Ez egy jelzés az alkalmazás nak a frissítések ismételt alkalmazására, és próbálja meg újra az eredeti ügyfélkérelmet.

## <a name="next-steps"></a>További lépések

Az adatbázis-tranzakciókról és az optimista egyidejűség-szabályozásról az alábbi cikkekben olvashat bővebben:

- [Az Azure Cosmos-adatbázisok, -tárolók és -elemek](databases-containers-items.md)
- [Konzisztenciaszintek](consistency-levels.md)
- [Ütközéstípusok és -feloldási szabályzatok](conflict-resolution-policies.md)
- [Tárolt eljárások, eseményindítók és felhasználó által definiált függvények](stored-procedures-triggers-udfs.md)
