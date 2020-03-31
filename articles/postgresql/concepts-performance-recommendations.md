---
title: Teljesítményre vonatkozó javaslatok – Azure-adatbázis a PostgreSQL-hez – Egykiszolgálós
description: Ez a cikk ismerteti a teljesítményajánlás i Azure Database for PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768469"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Teljesítményjavaslatok az Azure Database for PostgreSQL -ban – Egykiszolgálós

**A következőkre vonatkozik:** Azure Database for PostgreSQL – Egykiszolgálós verzió9.6, 10, 11

A Teljesítményjavaslatok funkció elemzi az adatbázisokat, hogy személyre szabott javaslatokat hozzon létre a jobb teljesítmény érdekében. A javaslatok létrehozásához az elemzés különböző adatbázis-jellemzőket, köztük sémát vizsgál. Engedélyezze a [query store-t](concepts-query-store.md) a kiszolgálón a teljesítményjavaslatok szolgáltatás teljes kihasználásához. Bármely teljesítményajánlás végrehajtása után tesztelje a teljesítményt a módosítások hatásának kiértékeléséhez. 

## <a name="permissions"></a>Engedélyek
A Teljesítménnyel kapcsolatos javaslatok funkcióval futtatott elemzéshez **Tulajdonos** vagy **Közreműködő** jogosultság szükséges.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [Teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) funkció elemzi a szerveren futó számítási feladatokat a potenciálisan javítható teljesítményű indexek azonosításához.

A PostgreSQL-kiszolgáló azure portaloldalán található menüsor **intelligens teljesítmény** szakaszából származó **teljesítményjavaslatok** megnyitása.

![A Teljesítménnyel kapcsolatos javaslatok kezdőlapja](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Válassza **az Elemzés** lehetőséget, és válasszon egy adatbázist, amely megkezdi az elemzést. A számítási feladatoktól függően az elemzés több percet is igénybe vehet. Amikor az elemzés elkészült, a portálon megjelenik egy értesítés. Az elemzés az adatbázis alapos vizsgálatát végzi. Azt javasoljuk, hogy csúcsidőn kívüli időszakokban végezzen elemzést. 

A **Javaslatok** ablakban megjelenik a javaslatok listája, ha találhatók ilyenek.

![Teljesítményjavaslatok új lap](./media/concepts-performance-recommendations/performance-recommendations-result.png)

A javaslatok nem kerülnek automatikusan alkalmazásra. A javaslat alkalmazásához másolja a lekérdezés szövegét, és futtassa azt a választott ügyféltől. Ne felejtse el tesztelni és figyelni az ajánlás kiértékeléséhez. 

## <a name="recommendation-types"></a>Javaslattípusok

Jelenleg kétféle javaslat támogatott: *Index létrehozása* és *a Drop Index*.

### <a name="create-index-recommendations"></a>Indexjavaslatok létrehozása
*Indexjavaslatok létrehozása* azt javasolják, hogy új indexek gyorsítsák fel a munkaterhelés leggyakrabban futtatott vagy időigényes lekérdezéseit. Ehhez a javaslattípushoz engedélyezni kell a [Query Store szolgáltatást.](concepts-query-store.md) A Query Store összegyűjti a lekérdezési adatokat, és biztosítja a részletes lekérdezési futásidejű és gyakorisági statisztikákat, amelyeket az elemzés a javaslat hoz.

### <a name="drop-index-recommendations"></a>A Drop Index ajánlásai
A hiányzó indexek észlelése mellett az Azure Database for PostgreSQL elemzi a meglévő indexek teljesítményét. Ha egy index et ritkán vagy redundáns, az analizátor azt javasolja, hogy dobja el.

## <a name="considerations"></a>Megfontolandó szempontok
* A teljesítményjavaslatok nem érhetők el [az olvasási replikákhoz.](concepts-read-replicas.md)
## <a name="next-steps"></a>További lépések
- További információk az Azure Database for PostgreSQL [monitoringjához és finomhangolásához](concepts-monitoring.md).

