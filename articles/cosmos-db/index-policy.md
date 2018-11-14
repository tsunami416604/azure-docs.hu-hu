---
title: Indexelési szabályzatok az Azure Cosmos DB |} A Microsoft Docs
description: Indexelő működésének megismerése az Azure Cosmos DB-hez. Ismerje meg, hogyan konfigurálja, és módosítsa az indexelési házirendet az Automatikus indexelés és jobb teljesítményt nyújt.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: ffb70ce8c26b7774e90801271c55cd8a80906c90
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51629137"
---
# <a name="indexing-policy-in-azure-cosmos-db"></a>Az Azure Cosmos DB indexelési házirend

Felülbírálhatja az alapértelmezett indexelési szabályzat egy Azure Cosmos-tárolón úgy konfigurálja a következő paraméterekkel:

* **Bevonhat vagy kizárhat elemek és elérési utak az indexből**: kizárása, vagy adott elemeket tartalmaznak az indexben, helyezze be vagy cserélje le a tárolóban lévő elemek. Akkor is is belefoglalhat vagy kizárhat meghatározott vlastnosti cest Pro hledání/indexelendő egyes tárolók között. Elérési utak tartalmazhat helyettesítő karakterek mintái, például *.

* **Index típusok konfigurálása**: emellett tartomány indexelt elérési utak, adja hozzá az indexek más típusú például térbeli.

* **Index módok konfigurálása**: egy tárolót az indexelési házirendet használatával konfigurálhatja az indexelő mód például *Consistent* vagy *None*.

## <a name="indexing-modes"></a>Az indexelő módok 

Az Azure Cosmos DB két lehetőséget támogat az indexelő konfigurálható egy Azure Cosmos-tárolón. A következő két indexelési mód az indexelési szabályzat konfigurálhatja: 

* **Egységes**: egy Azure Cosmos-tárolóhoz szabályzat Consistent úgy van beállítva, ha a lekérdezések egy adott tárolón hajtsa végre a megadott pont olvasási műveleteknél a konzisztencia szintjét (például erős, kötött elavulás, munkamenet és végleges). 

  Az index frissítése szinkron módon történik, az elemek frissítése során. Ha például beszúrás, cserélje le, frissítési és törlési műveletek egy elemre eredményez az index frissítése. Egységes indexelő támogatja az egységes lekérdezéseket tárolómappába, mely negatív hatással a lemezírás teljesítménye. Lemezírás teljesítménye csökkentése attól függ, a "elérési út tartalmazza az indexelés", és a "konzisztenciaszint." Indexelő mód CONSISTENT írható gyorsan szolgál, és azonnal számítási feladatok lekérdezése.

* **Nincs**: egy tároló, amely tartalmaz egy index mód nincs társítva indexszel rendelkezik. Ez általában akkor használatos, ha az Azure Cosmos-adatbázis egy kulcs-érték tárolóként szolgál, és csak az ID tulajdonság által elért elemek.

  > [!NOTE]
  > A létező indexek elvetését mellékhatása az indexelési mód konfigurálása egy sem rendelkezik. Ezt a beállítást kell használnia, ha ID használatának megkövetelése a hozzáférési mintákat, vagy önhivatkozást csak.

Lekérdezés konzisztenciaszintek hasonló a normál olvasási műveletek karbantartása. Az Azure Cosmos database hibát ad vissza, ha lekérdezheti, ha a tároló, amelyben nincs mód az indexelés. Vizsgálatok az explicit keresztül, a lekérdezéseket hajthat végre **x-ms-documentdb-enable-vizsgálat** fejléc a REST API-ban vagy a **EnableScanInQuery** beállítás kérése a .NET SDK használatával. Néhány lekérdezés hasonló szolgáltatásokat szabályoznak, ORDER BY záradék használata jelenleg nem támogatott **EnableScanInQuery**, mert azok megszabják, hogy a megfelelő index.

## <a name="modifying-the-indexing-policy"></a>Az indexelési szabályzat módosítása

Az Azure Cosmos DB-tároló az indexelési házirendet bármikor frissítheti. Indexelési szabályzat az Azure Cosmos-tárolókat a változás az alakzat az index módosítása vezethet. Ez a módosítás érinti a indexelhetők elérési utakat, a pontosság és az index magát a konzisztenciájú modellt. Indexelési szabályzat lényegében változását végrehajtásához egy új indexbe a régi index.

### <a name="index-transformations"></a>Index átalakítások

Az összes index átalakítások online hajtja végre. Az elemek indexelve a régi szabályzatonként hatékony működésének megzavarása nélkül megtesztelheti az írás rendelkezésre állása, vagy a tároló kiosztott átviteli átalakításának az új házirend szerint. Konzisztenciájának olvasása és írása, a REST API, SDK-k használatával, vagy a tárolt eljárásokkal és eseményindítókkal végrehajtott műveleteket az index átalakítás nem lesz hatással.

Indexelési házirend módosítása egy aszinkron művelet, és a művelet végrehajtásához szükséges elemek, az átviteli sebesség kiosztott részéért és az elemek méretének számától függ. Amíg folyamatban van az újraindexelés, a lekérdezés nesmí vracet minden egyező eredmény, ha a lekérdezések az éppen módosított index használatát, és a lekérdezések nem ad vissza hibát vagy hibákat. Amíg folyamatban van az újraindexelés, a lekérdezések végül konzisztens, függetlenül az indexelési módjának saját konfigurációja. Az index után átalakítása befejeződik, továbbra is megtekintheti a konzisztens eredmények. Ez a felületek, például REST API, SDK-k vagy tárolt eljárások és triggerek által kiadott lekérdezésekre vonatkozik. Index átalakítási aszinkron módon történik a háttérben a replikákon egy adott replika elérhető késztartalék források segítségével.

Az összes index átalakítások helyben történik. Az Azure Cosmos DB nem tart fenn az index két példányban. Ezért semmilyen további lemezterület szükséges vagy index átalakítás során a tárolókat igénybe vett.

Amikor módosítja az indexelési házirendet, módosítások áthelyezése az új indexre a régi index elsősorban az indexelési mód konfigurációk alapul. Az indexelő mód konfigurációk más tulajdonságai, például/kizárt elérési utak, index típusú és a pontosság képest jelentős szerepet játszanak.

Ha a régi és új szabályzatok használata az indexelés **Consistent** indexelést, az Azure Cosmos database hajt végre egy online index-átalakítást. Egy másik indexelési házirend-módosítások, konzisztens az indexelő mód az átalakítás közben nem lehet alkalmazni. Ha áthelyezi a nincs mód az indexelés, az index azonnal megszakad. Nincs áthelyezése akkor hasznos, ha megszakítja a folyamatban lévő átalakulása és kezdés tiszta lappal egy másik indexelési házirendet.

Index átalakításkor sikeresen befejeződik, ellenőrizze, hogy a tároló rendelkezik elegendő lemezterület. Ha a tároló eléri a tárolási vonatkozó kvótát, az index átalakítás fel van függesztve. Index átalakítás automatikusan folytatódik, ha tárterület érhető el, például bizonyos elemek törlésekor.

## <a name="modifying-the-indexing-policy---examples"></a>Az indexelési szabályzat – példák módosítása

A leggyakoribb alkalmazási helyzetei, ahol frissíti az indexelési házirendet a következők:

* Ha szeretné-e konzisztens eredmények van a normál működés során, de térhet vissza a **nincs** során tömeges adatokat importálja az indexelő mód.

* Ha azt szeretné, a jelenlegi Azure-Cosmos-tárolókat az indexelési szolgáltatások használatának megkezdéséhez. Például használja, a térinformatikai lekérdezések, ami megköveteli a térbeli index típusa vagy ORDER BY / tartomány lekérdezések, amelyek igényel a karakterlánc tartomány index ilyen karakterlánc.

* Ha szeretné manuálisan válassza ki a Tulajdonságok indexelni, és módosítsa őket arra, hogy alkalmazkodjanak a számítási feladatok idővel.

* Ha azt szeretné, finomhangolása az indexelési pontosság, a lekérdezési teljesítmény javításához vagy a felhasznált tárterület csökkentése érdekében.

## <a name="next-steps"></a>További lépések

További információ az indexelő az alábbi cikkeket:

* [Az indexelő áttekintése](index-overview.md)
* [Index típusa](index-types.md)
* [Index elérési utak](index-paths.md)
* [Indexelési házirend kezelése](how-to-manage-indexing-policy.md)