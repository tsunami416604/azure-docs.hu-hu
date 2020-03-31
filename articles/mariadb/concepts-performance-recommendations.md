---
title: Teljesítményre vonatkozó javaslatok – Azure-adatbázis a MariaDB-hez
description: Ez a cikk a Teljesítményajánlás funkciót ismerteti a MariaDB Azure Database szolgáltatásában
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 838a4123bd5007f987f27674862409445967a2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528098"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Teljesítménnyel kapcsolatos javaslatok az Azure Database for MariaDB-ben

**A következőkre vonatkozik:** Azure-adatbázis a MariaDB 10.2-hez

A Teljesítményjavaslatok szolgáltatás elemzi az adatbázisokat, és személyre szabott javaslatokat hoz létre a jobb teljesítmény érdekében. A javaslatok létrehozásához az elemzés különböző adatbázis-jellemzőket, köztük sémát vizsgál. Engedélyezze a [query store-t](concepts-query-store.md) a kiszolgálón a teljesítményjavaslatok szolgáltatás teljes kihasználásához. Ha a teljesítményséma ki van kapcsolva, a Query Store bekapcsolása lehetővé teszi, hogy performance_schema és a szolgáltatáshoz szükséges teljesítményséma-eszközök egy részhalmazát. Bármely teljesítményajánlás végrehajtása után tesztelje a teljesítményt a módosítások hatásának kiértékeléséhez.

## <a name="permissions"></a>Engedélyek

A Teljesítménnyel kapcsolatos javaslatok funkcióval futtatott elemzéshez **Tulajdonos** vagy **Közreműködő** jogosultság szükséges.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok

A [Teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) funkció elemzi a szerveren futó számítási feladatokat a potenciálisan javítható teljesítményű indexek azonosításához.

A MariaDB-kiszolgáló azure portallapján található menüsor **intelligens teljesítmény** szakaszából származó **teljesítményjavaslatok** megnyitása.

![A Teljesítménnyel kapcsolatos javaslatok kezdőlapja](./media/concepts-performance-recommendations/performance-recommendations-page.png)

Válassza **az Elemzés** lehetőséget, és válasszon egy adatbázist, amely megkezdi az elemzést. A számítási feladatoktól függően az elemzés több percet is igénybe vehet. Amikor az elemzés elkészült, a portálon megjelenik egy értesítés. Az elemzés az adatbázis alapos vizsgálatát végzi. Azt javasoljuk, hogy csúcsidőn kívüli időszakokban végezzen elemzést.

A **Javaslatok** ablakban megjelenik a javaslatok listája, ha bármelyik megtalálható, és a kapcsolódó lekérdezésazonosító, amely létrehozta ezt a javaslatot. A lekérdezésazonosító segítségével a [mysql.query_store](concepts-query-store.md#mysqlquery_store) nézetben többet megtudhat a lekérdezésről.

![Teljesítményjavaslatok új lap](./media/concepts-performance-recommendations/performance-recommendations-result.png)

A javaslatok nem kerülnek automatikusan alkalmazásra. A javaslat alkalmazásához másolja a lekérdezés szövegét, és futtassa azt a választott ügyféltől. Ne felejtse el tesztelni és figyelni az ajánlás kiértékeléséhez.

## <a name="recommendation-types"></a>Javaslattípusok

Jelenleg csak *index létrehozása* javaslatok támogatottak.

### <a name="create-index-recommendations"></a>Indexjavaslatok létrehozása

*Indexjavaslatok létrehozása* azt javasolják, hogy új indexek gyorsítsák fel a munkaterhelés leggyakrabban futtatott vagy időigényes lekérdezéseit. Ehhez a javaslattípushoz engedélyezni kell a [Query Store szolgáltatást.](concepts-query-store.md) A Query Store összegyűjti a lekérdezési adatokat, és biztosítja a részletes lekérdezési futásidejű és gyakorisági statisztikákat, amelyeket az elemzés a javaslat hoz.

## <a name="next-steps"></a>További lépések

- További információ a [Figyelésről és a Hangolásról](concepts-monitoring.md) a MariaDB Azure Database szolgáltatásában.