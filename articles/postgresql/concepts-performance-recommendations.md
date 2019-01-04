---
title: Teljesítménnyel kapcsolatos javaslatok az Azure Database for postgresql-hez
description: Ez a cikk ismerteti a teljesítménnyel kapcsolatos javaslatok egy kérheti le az Azure Database for postgresql-hez.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 1dedc08f27d1a483290dc61cce879290ca66592d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548101"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Teljesítménnyel kapcsolatos javaslatok az Azure Database for postgresql-hez

**A következőkre vonatkozik:** Azure Database for PostgreSQL 9.6 és 10

> [!IMPORTANT]
> Teljesítménnyel kapcsolatos javaslatok a nyilvános előzetes verzióban van.

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

