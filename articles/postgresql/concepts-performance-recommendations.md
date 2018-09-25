---
title: Teljesítménnyel kapcsolatos javaslatok az Azure Database for postgresql-hez
description: Ez a cikk ismerteti a teljesítménnyel kapcsolatos javaslatok egy kérheti le az Azure Database for postgresql-hez.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 25ab2f735cfd4b0870729cb86992665fa8984580
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46976376"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Teljesítménnyel kapcsolatos javaslatok az Azure Database for postgresql-hez

**A következőkre vonatkozik:** Azure Database for PostgreSQL 9.6 és 10

> [!IMPORTANT]
> Teljesítménnyel kapcsolatos javaslatok a nyilvános előzetes verzióban van.

A teljesítménnyel kapcsolatos javaslatok funkció a felső indexeket hozhat létre az Azure Database for PostgreSQL-kiszolgáló a teljesítmény javítása, amely azonosítja. Index ajánlások előállításához, a szolgáltatás figyelembe veszi különféle adatbázis jellemzőit, beleértve a séma- és a számítási feladatok Query Store által jelentett módon. Után minden teljesítmény javaslat megvalósítása, ügyfelek kell teljesítménytesztelési ezeket a módosításokat hatásának vizsgálatában. 

## <a name="permissions"></a>Engedélyek
**Tulajdonos** vagy **közreműködői** elemzése a teljesítménnyel kapcsolatos javaslatok funkció használatával futtatásához szükséges engedéllyel.

## <a name="performance-recommendations"></a>Teljesítménnyel kapcsolatos javaslatok
A [teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) funkció elemzi a számítási feladatok indexek azonosíthatja a teljesítmény javítása a lehetséges a kiszolgáló között.

Nyissa meg **teljesítménnyel kapcsolatos javaslatok** származó a **támogatás + hibaelhárítás** a menüsávon a PostgreSQL-kiszolgáló az Azure portál oldalán szakaszában.

![Teljesítménnyel kapcsolatos javaslatok kezdőlap](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

Válassza ki **elemzés** , és válasszon egy adatbázist. Ennek hatására elkezdődik az elemzés. A számítási feladatok függően ez eltarthat néhány percig végrehajtásához. Ha elkészült az elemzés, a portálon értesítést lesz.

A **teljesítménnyel kapcsolatos javaslatok** ablakban megjelennek a javaslatok listája, ha bármelyik találhatók. Javaslat jelennek meg a megfelelő információ **adatbázis**, **tábla**, **oszlop**, és **Index mérete**.

![Teljesítmény javaslatok új lap](./media/concepts-performance-recommendations/performance-recommendations-result.png)

A javaslat megvalósítása, másolja a lekérdezés szövege, és futtathatja az ügyfél által választott.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [megfigyelés és finomhangolás](concepts-monitoring.md) az Azure Database for postgresql-hez.

