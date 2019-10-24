---
title: Adatbázis-tranzakciók és optimista Egyidejűség-vezérlés a Azure Cosmos DBban
description: Ez a cikk az adatbázis-tranzakciókat és az optimista Egyidejűség-vezérlést ismerteti Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 4c263b32b7ededb9e5169e80a29806f322a3c849
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755167"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Tranzakciók és optimista egyidejűség vezérlése

Az adatbázis-tranzakciók biztonságos és kiszámítható programozási modellt biztosítanak, amely az adatokat egyidejűleg módosítja. A hagyományos, a SQL Serverhoz hasonló, az üzleti logikát a tárolt eljárásokkal és/vagy triggerekkel írhatja át, így közvetlenül az adatbázis-motoron keresztül küldheti el a kiszolgálót. A hagyományos kapcsolatok adatbázisaival két különböző programozási nyelvet kell kezelni a (nem tranzakciós) programozási nyelv (például JavaScript, Python, C#, Java stb.) és a tranzakciós programozási nyelv ( például T-SQL), amelyet az adatbázis natív módon hajt végre.

Azure Cosmos DB adatbázis-motorja támogatja a teljes sav (atomi, konzisztencia, elkülönítés, tartósság) megfelelő tranzakciókat a pillanatkép-elkülönítéssel. A tároló [logikai partíciójának](partition-data.md) hatókörén belüli összes adatbázis-művelet tranzakciós végrehajtást végez a partíció replikája által üzemeltetett adatbázis-motoron belül. Ezek a műveletek magukban foglalják az írást (a logikai partíción belüli egy vagy több elem frissítése) és az olvasási műveleteket. A következő táblázat a különböző művelet-és tranzakciótípusok szemlélteti:

| **Művelet**  | **Művelet típusa** | **Egy vagy több elem tranzakciója** |
|---------|---------|---------|
| Beszúrás (Pre/post trigger nélkül) | Írás | Egyetlen elem tranzakciója |
| Beszúrás (előzetes/post triggerrel) | Írás és olvasás | Több tételből álló tranzakció |
| Csere (Pre/post trigger nélkül) | Írás | Egyetlen elem tranzakciója |
| Lecserélés (egy előzetes/utáni triggerrel) | Írás és olvasás | Több tételből álló tranzakció |
| Upsert (Pre/post trigger nélkül) | Írás | Egyetlen elem tranzakciója |
| Upsert (Pre/post triggerrel) | Írás és olvasás | Több tételből álló tranzakció |
| Törlés (Pre/post trigger nélkül) | Írás | Egyetlen elem tranzakciója |
| Törlés (előzetes/post triggerrel) | Írás és olvasás | Több tételből álló tranzakció |
| Tárolt eljárás végrehajtása | Írás és olvasás | Több tételből álló tranzakció |
| Egyesítési eljárás rendszer által kezdeményezett végrehajtása | Írás | Több tételből álló tranzakció |
| Elemek törlésének rendszer által kezdeményezett végrehajtása egy elem lejárta (TTL) alapján | Írás | Több tételből álló tranzakció |
| Olvasás | Olvasás | Egy elem tranzakciója |
| Változáscsatorna | Olvasás | Több tételből álló tranzakció |
| Oldalszámozott olvasás | Olvasás | Több tételből álló tranzakció |
| Többoldalas lekérdezés | Olvasás | Több tételből álló tranzakció |
| Az UDF végrehajtása a többoldalas lekérdezés részeként | Olvasás | Több tételből álló tranzakció |

## <a name="multi-item-transactions"></a>Több tételből álló tranzakciók

A Azure Cosmos DB lehetővé teszi [tárolt eljárások írását, az eseményindítók előtti/utáni triggerek, a felhasználó által definiált függvények (UDF)](stored-procedures-triggers-udfs.md) és a JavaScript egyesítési eljárásainak írását. Azure Cosmos DB natív módon támogatja a JavaScript-végrehajtást az adatbázis-motorján belül. A tárolt eljárások, az eseményindítók, a felhasználó által definiált függvények (UDF-EK) és az egyesítési eljárások regisztrálása egy tárolón, később pedig tranzakciós műveleteket hajthat végre az Azure Cosmos adatbázis-motorján belül. Az alkalmazás logikájának a JavaScriptben való írása lehetővé teszi a vezérlési folyamat természetes kifejezését, a változók hatókörét, hozzárendelését és az adatbázis-tranzakciókban lévő primitívek kezelését a JavaScript nyelven közvetlenül.

A JavaScript-alapú tárolt eljárások, eseményindítók, UDF és egyesítési eljárások egy környezeti sav-tranzakción belül vannak becsomagolva, a logikai partíción belüli összes elem pillanatkép-elkülönítésével. Ha a JavaScript-program kivételt jelez, a végrehajtás során a rendszer megszakítja és visszaállítja a teljes tranzakciót. Az eredményül kapott programozási modell egyszerű, mégis hatékony. A JavaScript-fejlesztők tartós programozási modellt kapnak, miközben továbbra is az ismerős nyelvi konstrukciókat és a könyvtárbeli primitíveket használják.

A JavaScript közvetlen végrehajtása az adatbázismotor keretében az adatbázis-műveletek teljesítményének és tranzakciós végrehajtásának lehetővé tétele egy tároló elemein. Emellett mivel az Azure Cosmos adatbázismotor natív módon támogatja a JSON-t és a JavaScriptet, az alkalmazás és az adatbázis típusa nem felel meg az egyes alkalmazások és az adatbázisok típusának.

## <a name="optimistic-concurrency-control"></a>Optimista Egyidejűség-vezérlés 

Az optimista Egyidejűség-vezérlés lehetővé teszi az elveszett frissítések és törlések elkerülését. Az egyidejű, ütköző műveleteket a rendszer az adott összetevőt birtokló logikai partíció által üzemeltetett adatbázismotor rendszeres pesszimista zárolásának aláveti. Ha két egyidejű művelet kísérli meg egy elem legújabb verziójának frissítését egy logikai partíción belül, az egyiket a rendszer megnyeri, a másik pedig sikertelen lesz. Ha azonban egy vagy két művelet párhuzamosan frissíti ugyanazt az elemeket, korábban már beolvasta az elem egy régebbi értékét, akkor az adatbázis nem tudja, hogy a korábban olvasott érték vagy akár mindkét ütköző művelet valóban az elem legújabb értéke volt-e. Szerencsére ezt a helyzetet az **optimista Egyidejűség-vezérléssel (OCC)** lehet észlelni, mielőtt a két művelet a tranzakció határát adja meg az adatbázismotor belsejében. A OCC megvédi adatait a mások által végrehajtott, véletlenül felülírt módosításokkal szemben. Azt is megakadályozza, hogy mások véletlenül felülírják a saját módosításait.

Az elemek egyidejű frissítését Azure Cosmos DB kommunikációs protokoll rétege OCC alá. Az Azure Cosmos Database biztosítja, hogy a frissíteni kívánt elem (vagy törlés) ügyféloldali verziója ugyanaz, mint az Azure Cosmos-tárolóban lévő elem verziója. Ez garantálja, hogy az írásokat a mások írásai véletlenül felülírják, és fordítva. Többfelhasználós környezetben az optimista Egyidejűség-vezérlés megvédi az elemek hibás verziójának véletlen törlését vagy frissítését. Ennek megfelelően az elemek védve vannak a hírhedt "elveszett frissítés" vagy "elveszett Törlés" problémával szemben.

Az Azure Cosmos-tárolóban tárolt minden egyes elemmel rendelkezik egy rendszer által definiált `_etag` tulajdonsággal. Az `_etag` értékét a rendszer automatikusan hozza létre és frissíti a kiszolgáló minden alkalommal, amikor frissíti az elemeket. a `_etag` használható az ügyfél által megadott `if-match` kérelem fejlécével, hogy a kiszolgáló eldöntse, hogy lehet-e feltételesen frissíteni egy adott tételt. A `if-match` fejléc értéke megegyezik a kiszolgáló `_etag` értékével, az elem pedig frissül. Ha a `if-match`-kérelem fejlécének értéke már nem aktuális, a kiszolgáló elutasítja a műveletet "HTTP 412 előfeltételi hiba" üzenettel. Az ügyfél ezután újra beolvashatja az elemeket, hogy megszerezze az elem aktuális verzióját a kiszolgálón, vagy felülbírálja a kiszolgálón lévő elem verzióját az elemhez tartozó saját `_etag` értékkel. Emellett `_etag` is használható a `if-none-match` fejléctel annak megállapításához, hogy szükség van-e egy erőforrás visszahívására. 

Az elemek `_etag` értékének változása minden alkalommal megváltozik, amikor az adott tétel frissül. Az elemek cseréje művelet esetén a `if-match` explicit módon meg kell adni a kérési beállítások részeként. Példaként tekintse meg a mintakód a [githubban](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446)című témakört. `_etag` érték implicit módon van bejelölve a tárolt eljárás által érintett összes írásos elemnél. Ha ütközés észlelhető, a tárolt eljárás visszaállítja a tranzakciót, és kivételt vet fel. Ezzel a módszerrel a tárolt eljáráson belül az összes vagy a nem írt írást is alkalmazza a rendszer. Ez egy jel az alkalmazásnak a frissítések újraalkalmazására, majd próbálja megismételni az eredeti ügyfél-kérelmet.

## <a name="next-steps"></a>Következő lépések

További információ az adatbázis-tranzakciókkal és az optimista Egyidejűség-vezérléssel kapcsolatban a következő cikkekben található:

- [Azure Cosmos-adatbázisok,-tárolók és-elemek használata](databases-containers-items.md)
- [Konzisztencia szintjei](consistency-levels.md)
- [Ütközési típusok és megoldási szabályzatok](conflict-resolution-policies.md)
- [Tárolt eljárások, eseményindítók és felhasználó által definiált függvények](stored-procedures-triggers-udfs.md)
