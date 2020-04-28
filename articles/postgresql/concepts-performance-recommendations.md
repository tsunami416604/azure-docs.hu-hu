---
title: Teljesítményre vonatkozó javaslatok – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL-Single Server teljesítmény-javaslati szolgáltatását ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74768469"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Teljesítményre vonatkozó javaslatok Azure Database for PostgreSQL – egyetlen kiszolgálón

**A következőkre vonatkozik:** Azure Database for PostgreSQL – egykiszolgálós verzió: 9,6, 10, 11

A teljesítményre vonatkozó javaslatok szolgáltatás elemzi az adatbázisokat, hogy testreszabott javaslatokat hozzon létre a jobb teljesítmény érdekében. A javaslatok létrehozásához az elemzés különböző adatbázis-jellemzőket vizsgál, beleértve a sémát. Engedélyezze a [lekérdezési tárolót](concepts-query-store.md) a kiszolgálón a teljesítményre vonatkozó javaslatok szolgáltatás teljes körű kihasználásához. A teljesítményre vonatkozó javaslat megvalósítása után tesztelje a teljesítményt a változások hatásának kiértékeléséhez. 

## <a name="permissions"></a>Engedélyek
A Teljesítménnyel kapcsolatos javaslatok funkcióval futtatott elemzéshez **Tulajdonos** vagy **Közreműködő** jogosultság szükséges.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [Teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) funkció elemzi a szerveren futó számítási feladatokat a potenciálisan javítható teljesítményű indexek azonosításához.

Nyissa meg a **teljesítményre vonatkozó javaslatokat** a PostgreSQL-kiszolgáló Azure Portal lapjának menüsorának **intelligens teljesítmény** szakaszában.

![A Teljesítménnyel kapcsolatos javaslatok kezdőlapja](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Válassza **az elemzés lehetőséget, és válasszon** egy adatbázist, amely megkezdi az elemzést. A számítási feladattól függően a th elemzés több percet is igénybe vehet. Amikor az elemzés elkészült, a portálon megjelenik egy értesítés. Az elemzés az adatbázis mélyreható vizsgálatát végzi. Javasoljuk, hogy az elemzést az időszakon kívüli időszakok alatt végezze el. 

A **javaslatok** ablakban megjelennek a javaslatok listája, ha vannak ilyenek.

![Teljesítményre vonatkozó javaslatok – új oldal](./media/concepts-performance-recommendations/performance-recommendations-result.png)

A javaslatok nem lesznek automatikusan alkalmazva. A javaslat alkalmazásához másolja a lekérdezés szövegét, és futtassa azt a választott ügyfélről. A javaslat kiértékeléséhez ne felejtse el tesztelni és figyelni. 

## <a name="recommendation-types"></a>Javaslatok típusai

Jelenleg két típusú javaslat támogatott: index és *drop index* *létrehozása* .

### <a name="create-index-recommendations"></a>Tárgymutató-javaslatok létrehozása
Az indexelési javaslatok alapján új indexek *hozhatók létre* , amelyek felgyorsítják a számítási feladatok leggyakrabban futtatott vagy időigényes lekérdezéseit. Ehhez az ajánlási típushoz engedélyezni kell a [query Store](concepts-query-store.md) -t. A lekérdezési tároló gyűjti a lekérdezési adatokat, és megadja az elemzés által a javaslat végrehajtásához használt részletes lekérdezési futtatókörnyezetet és gyakorisági statisztikát.

### <a name="drop-index-recommendations"></a>Tárgymutató-javaslatok eldobása
A hiányzó indexek észlelése mellett Azure Database for PostgreSQL elemzi a meglévő indexek teljesítményét. Ha egy indexet ritkán használnak vagy redundánsak, az analizátor eldobását javasolja.

## <a name="considerations"></a>Megfontolandó szempontok
* A teljesítményre vonatkozó javaslatok [olvasási replikák](concepts-read-replicas.md)esetén nem érhetők el.
## <a name="next-steps"></a>További lépések
- További információk az Azure Database for PostgreSQL [monitoringjához és finomhangolásához](concepts-monitoring.md).

