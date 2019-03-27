---
title: Teljesítménnyel kapcsolatos javaslatok az Azure Database for postgresql-hez
description: Ez a cikk ismerteti a teljesítmény javaslat funkció az Azure Database for postgresql-hez.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: d8edbc2847c06e95e658a1324f2e85f1758e60be
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487940"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Teljesítménnyel kapcsolatos javaslatok az Azure Database for postgresql-hez

**A következőkre vonatkozik:** Azure Database for PostgreSQL 9.6 és 10

A teljesítménnyel kapcsolatos javaslatok funkció a felső indexeket hozhat létre az Azure Database for PostgreSQL-kiszolgáló a teljesítmény javítása, amely azonosítja. Index ajánlások előállításához, a szolgáltatás figyelembe veszi különféle adatbázis jellemzőit, beleértve a séma- és a számítási feladatok Query Store által jelentett módon. Után minden teljesítmény javaslat megvalósítása, ügyfelek kell teljesítménytesztelési ezeket a módosításokat hatásának vizsgálatában. 

## <a name="permissions"></a>Engedélyek
A Teljesítménnyel kapcsolatos javaslatok funkcióval futtatott elemzéshez **Tulajdonos** vagy **Közreműködő** jogosultság szükséges.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [Teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) funkció elemzi a szerveren futó számítási feladatokat a potenciálisan javítható teljesítményű indexek azonosításához.

Nyissa meg a PostgreSQL-kiszolgálója Azure portáloldalán lévő menüsáv **Támogatás és hibaelhárítás** részén lévő **Teljesítménnyel kapcsolatos javaslatok** menüpontot.

![A Teljesítménnyel kapcsolatos javaslatok kezdőlapja](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Jelölje ki az **Elemzés** funkciót, és válasszon egy adatbázist. Ez elindítja az elemzést. A számítási feladatok függően ez eltarthat néhány percig végrehajtásához. Amikor az elemzés elkészült, a portálon megjelenik egy értesítés.

A **Teljesítménnyel kapcsolatos javaslatok** ablakban megjelenik a javaslatok listája (ha vannak). A javaslat információt jelenít meg a releváns **adatbázisról**, **tábláról**, **oszlopról** és **indexméretről**.

![Teljesítmény javaslatok új lap](./media/concepts-performance-recommendations/performance-recommendations-result.png)

A javaslat megvalósításához másolja ki a lekérdezés szövegét, és futtassa azt a választott ügyfélről.

## <a name="next-steps"></a>További lépések
- További információk az Azure Database for PostgreSQL [monitoringjához és finomhangolásához](concepts-monitoring.md).

