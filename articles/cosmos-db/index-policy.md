---
title: Az Azure Cosmos DB indexelési szabályzatok
description: Indexelő működésének megismerése az Azure Cosmos DB-hez. Ismerje meg, hogyan konfigurálja, és módosítsa az indexelési házirendet az Automatikus indexelés és jobb teljesítményt nyújt.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 6998db1679e67f8ac4bf7c81ea9373c66a9618ee
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278564"
---
# <a name="index-policy-in-azure-cosmos-db"></a>Az Azure Cosmos DB index házirend

Felülbírálhatja az alapértelmezett indexelési szabályzat egy Azure Cosmos-tárolón úgy konfigurálja a következő paraméterekkel:

* **Bevonhat vagy kizárhat elemek és elérési utak az indexből**: Zárja ki, vagy adott elemeket tartalmaznak az indexben, helyezze be vagy cserélje le a tárolóban lévő elemek. Akkor is is belefoglalhat vagy kizárhat meghatározott vlastnosti cest Pro hledání/indexelendő egyes tárolók között. Elérési utak tartalmazhat helyettesítő karakterek mintái, például *.

* **Index típusok konfigurálása**: Emellett tartomány indexelt elérési utak, hozzáadhat más típusú indexeket, például a térbeli.

* **Index módok konfigurálása**: Az indexelési házirendet egy tároló használatával konfigurálhatja az indexelő mód például *Consistent* vagy *None*.

## <a name="indexing-modes"></a>Az indexelő módok

Az Azure Cosmos DB kétféleképpen indexelési konfigurálható egy Azure Cosmos-tárolón keresztül indexelési házirendet:

* **Egységes**: Ha egy Azure Cosmos-tárolóhoz szabályzat úgy van beállítva *Consistent*, a lekérdezések egy adott tárolón hajtsa végre a megadott pont olvasási műveleteknél a konzisztencia szintjét (például erős, kötött elavulás, munkamenet és végleges). 

  Az index frissítése szinkron módon történik, az elemek frissítése során. Ha például beszúrás, cserélje le, frissítési és törlési műveletek egy elemre eredményez az index frissítése. Egységes indexelő támogatja az egységes lekérdezéseket tárolómappába, mely negatív hatással a lemezírás teljesítménye. Lemezírás teljesítménye csökkentése attól függ, a "az index tartalmazza útvonalak" és "konzisztenciaszinttől." Konzisztens az indexelő mód célja, hogy az index naprakész, és a frissítések, és azonnal-lekérdezések kiszolgálása érdekében.

* **Nincs**: Egy tároló, amely tartalmaz egy index mód nincs társítva indexszel rendelkezik. Ez általában akkor használatos, ha az Azure Cosmos-adatbázis egy kulcs-érték tárolóként szolgál, és csak az ID tulajdonság által elért elemek.

  > [!NOTE]
  > Mint az indexelési mód konfigurálása a *nincs* létező indexek elvetését a mellékhatása rendelkezik. Ezt a beállítást kell használnia, ha ID használatának megkövetelése a hozzáférési mintákat, vagy önhivatkozást csak.

Lekérdezés konzisztenciaszintek hasonló a normál olvasási műveletek karbantartása. Az Azure Cosmos database hibát ad vissza, ha lekérdezheti, ha a tároló, amelyben egy *nincs* indexelési módban. Vizsgálatok az explicit keresztül, a lekérdezéseket hajthat végre **x-ms-documentdb-enable-vizsgálat** fejléc a REST API-ban vagy a **EnableScanInQuery** beállítás kérése a .NET SDK használatával. Néhány lekérdezés hasonló szolgáltatásokat szabályoznak, ORDER BY záradék használata jelenleg nem támogatott **EnableScanInQuery**, mert azok megszabják, hogy a megfelelő index.

## <a name="modifying-the-indexing-policy"></a>Az indexelési szabályzat módosítása

Az Azure Cosmos DB-tároló az indexelési házirendet bármikor frissítheti. Az indexelési házirendet az Azure Cosmos-tárolókat a változás az alakzat az index módosítása vezethet. Ez a módosítás érinti a indexelhetők elérési utakat, a pontosság és az index magát a konzisztenciájú modellt. Indexelési szabályzat lényegében változását végrehajtásához egy új indexbe a régi index.

### <a name="index-transformations"></a>Index átalakítások

Az összes index átalakítások online hajtja végre. Az elemek indexelve a régi szabályzatonként hatékony működésének megzavarása nélkül megtesztelheti az írás rendelkezésre állása és a tárolóban kiosztott átviteli átalakításának az új házirend szerint. Konzisztenciájának olvasása és írása a REST API, SDK-k használatával, illetve használatával végrehajtott műveleteket tárolt eljárások és eseményindítók nincs hatással az index átalakítása során.

Indexelési házirend módosítása egy aszinkron művelet, és a művelet végrehajtásához szükséges elemek, a kiosztott átviteli sebesség és az elemek méretének összege számától függ. Amíg újraindexelését folyamatban van, a lekérdezés nesmí vracet minden egyező eredmény, ha a lekérdezések az éppen módosított index használatát, és a lekérdezések nem ad vissza hibát vagy hibákat. Amíg újraindexelését folyamatban van, a lekérdezések végül konzisztens, függetlenül az indexelési módjának saját konfigurációja. Az index után átalakítása befejeződik, továbbra is megtekintheti a konzisztens eredmények. Ez a felületek, például REST API, SDK-k vagy tárolt eljárások és triggerek által kiadott lekérdezésekre vonatkozik. Index átalakítási aszinkron módon történik a háttérben, az adott replika elérhető késztartalék források segítségével replikákon.

Az összes index átalakítások helyben történik. Az Azure Cosmos DB nem tart fenn az index két példányban. Ezért semmilyen további lemezterület szükséges vagy index átalakítás során a tárolókat igénybe vett.

Amikor módosítja az indexelési házirendet, a változások áthelyezése az új indexre a régi index lesznek alkalmazva, és elsősorban az indexelési mód konfigurációk alapul. Az indexelő mód konfigurációk más tulajdonságai, például/kizárt elérési utak, index típusú és a pontosság képest jelentős szerepet játszanak.

Ha a régi és új szabályzatok használata az indexelés **Consistent** indexelést, az Azure Cosmos database hajt végre egy online index-átalakítást. Egy másik indexelési házirend-módosítások, konzisztens az indexelő mód az átalakítás közben nem lehet alkalmazni. Ha áthelyezi a nincs mód az indexelés, az index azonnal megszakad. Nincs áthelyezése akkor hasznos, ha megszakítja a folyamatban lévő átalakulása és kezdés tiszta lappal egy másik indexelési házirendet.

## <a name="modifying-the-indexing-policy---examples"></a>Az indexelési szabályzat – példák módosítása

Az alábbiakban a leggyakoribb alkalmazási helyzetei Ha meg szeretné egy indexelési szabályzat frissítése:

* Ha azt szeretné, hogy a konzisztens eredmények van a normál működés során, hanem térhet vissza a **nincs** során tömeges adatokat importálja az indexelő mód.

* Ha azt szeretné, a jelenlegi Azure-Cosmos-tárolókat az indexelési szolgáltatások használatának megkezdéséhez. Például használja, a térinformatikai lekérdezések, ami megköveteli a térbeli index típusa vagy ORDER BY / tartomány lekérdezések, amelyek igényel a karakterlánc tartomány index ilyen karakterlánc.

* Ha szeretné manuálisan válassza ki a Tulajdonságok indexelni, és módosítsa őket arra, hogy alkalmazkodjanak a számítási feladatok idővel.

* Ha azt szeretné, finomhangolása az indexelési pontosság, a lekérdezési teljesítmény javításához vagy a felhasznált tárterület csökkentése érdekében.

## <a name="next-steps"></a>További lépések

További információ az indexelő az alábbi cikkeket:

* [Az indexelő áttekintése](index-overview.md)
* [Indextípusok](index-types.md)
* [Indexek elérési útjai](index-paths.md)
* [Indexelési házirend kezelése](how-to-manage-indexing-policy.md)
