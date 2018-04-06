---
title: Az Azure SQL Database automatikus hangolása engedélyezése |} Microsoft Docs
description: Automatikus hangolása az Azure SQL adatbázis könnyen engedélyezheti.
services: sql-database
author: veljko-msft
manager: drasumic
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 09/19/2016
ms.author: vvasic
ms.openlocfilehash: 2ea5c0e640ac7e635692dafac1f30f377aa79625
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="enable-automatic-tuning"></a>Automatikus hangolás engedélyezése

Az Azure SQL Database az automatikusan kezelt adatok szolgáltatása, amely folyamatosan figyeli a lekérdezéseket, és azonosítja a végrehajtandó műveletet a számítási feladatok teljesítményének javításával végezheti el. Áttekintheti javaslatok és manuálisan alkalmazhatja azokat, vagy lehetővé teszik az Azure SQL Database automatikusan alkalmazza a javítási műveleteket – ez az úgynevezett **automatikus hangolási módot**. Az automatikus hangolással a kiszolgáló vagy az adatbázis szintjén engedélyezhető.

## <a name="enable-automatic-tuning-on-server"></a>Engedélyezze a kiszolgálón automatikus hangolása
A kiszolgáló szintjén választhat öröklik az "Azure alapértelmezett értéke" automatikus hangolási beállítás, vagy nem az, hogy örökölje a konfigurációt. Az Azure alapértelmezett FORCE_LAST_GOOD_PLAN engedélyezve van, CREATE_INDEX engedélyezve van, és DROP_INDEX le van tiltva.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Automatikus hangolási e-mail értesítések konfigurálása

Lásd: [automatikus e-mail értesítések beállítása](sql-database-automatic-tuning-email-notifications.md)

### <a name="azure-portal"></a>Azure Portal
Engedélyezi az automatikus hangolással Azure SQL adatbázis-kiszolgálón, keresse fel a kiszolgálót az Azure portálon, és válassza ki **automatikus hangolása** a menüben. Válassza ki a engedélyezése, és válassza ki az automatikus hangolási lehetőségeket **alkalmaz**:

![Kiszolgáló](./media/sql-database-automatic-tuning-enable/server.png)

A kiszolgáló automatikus hangolási lehetőségeket a kiszolgálón lévő összes adatbázis is vonatkozik. Alapértelmezés szerint minden adatbázisok a konfigurációs öröklése a fölérendelt kiszolgáló, de ez felül, és az egyes adatbázisok külön-külön megadott.

### <a name="rest-api"></a>REST API
[Ide kattintva további engedélyezéséről a REST API-n keresztül a kiszolgáló szintjén automatikus hangolása](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Egyedi adatbázis automatikus hangolása engedélyezése

Az Azure SQL Database lehetővé teszi, hogy egyesével adja meg az automatikus hangolási beállítás az összes adatbázisra. Az adatbázis szintjén választhat automatikus hangolási beállítás öröklése a fölérendelt kiszolgáló, "Azure alapértelmezett értéke", vagy nem az, hogy örökölje a konfigurációt. Az Azure alapértelmezett FORCE_LAST_GOOD_PLAN engedélyezve van, CREATE_INDEX engedélyezve van, és DROP_INDEX le van tiltva.

> [!NOTE]
> Az általános javasoljuk, hogy ugyanazokat a konfigurációs beállításokat is az összes adatbázis automatikusan alkalmazza az automatikus hangolási beállítás kiszolgálói szinten kezelését. Konfigurálja automatikus hangolással a egyedi adatbázis Ha az adatbázis különböző, hogy mások ugyanazon a kiszolgálón.
>

### <a name="azure-portal"></a>Azure Portal

Egy önálló adatbázis automatikus hangolása engedélyezéséhez nyissa meg az adatbázis az Azure portálon, és majd, és válassza ki **automatikus hangolása**. Beállíthatja, hogy a beállítások öröklése a kiszolgálóról a beállítás kiválasztásával egyetlen adatbázis, vagy a konfigurációs adatbázis egyesével adja meg.

![Adatbázis](./media/sql-database-automatic-tuning-enable/database.png)

Miután kiválasztotta a megfelelő konfigurációs, kattintson a **alkalmaz**.

### <a name="rest-api"></a>REST API-n
[Ide kattintva további engedélyezéséről a REST API-n keresztül egy önálló adatbázis automatikus hangolása](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)

### <a name="t-sql"></a>T-SQL

Engedélyezze az automatikus hangolással a T-SQL használatával egy adatbázist, hogy kapcsolódni az adatbázishoz, és hajtsa végre a következő lekérdezés:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Automatikus hangolása automatikus beállítása érvényes Azure alapértelmezés szerint használt érték. ÖRÖKLÉSI értékre állítaná automatikus hangolási beállítás öröklik a szülő-kiszolgáló. Egyéni választ, akkor manuálisan konfigurálnia a automatikus hangolása.

Az egyes automatikus hangolási beállításához T-SQL használatával kapcsolódni az adatbázishoz, és végrehajtsák a lekérdezést, ez például:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
Az egyes hangolási beállítás beállítása ON értékre állítása, örökölt adatbázis-beállítások felülbírálása, valamint a hangolási beállítás engedélyezése. OFF értékre állítaná örökölt adatbázis-beállítások felülbírálása és az is a hangolási beállítás letiltása. Automatikus hangolási beállítás, amely alapértelmezett van megadva, a konfiguráció örökli az adatbázis automatikus hangolási beállítás szintjét.  

## <a name="disabled-by-the-system"></a>A rendszer le van tiltva
Az automatikus hangolással figyeli időt vesz igénybe, az adatbázis az összes műveletet, és egyes esetekben meg tudja határozni, hogy automatikus hangolása nem megfelelően működik az adatbázison. Ebben a helyzetben hangolási beállítás letiltja a rendszer. A legtöbb esetben ez akkor fordul elő, mert a Lekérdezéstár nincs engedélyezve, vagy egy adott adatbázis csak olvasható állapotban van.

## <a name="next-steps"></a>További lépések
* Olvassa el a [automatikus hangolási cikk](sql-database-automatic-tuning.md) tudhat meg többet automatikus hangolása és szerepéről javítják a teljesítményt.
* Lásd: [teljesítmény javaslatok](sql-database-advisor.md) teljesítmény javaslatok az Azure SQL Database áttekintését.
* Lásd: [lekérdezési teljesítmény Insights](sql-database-query-performance.md) a teljesítményre gyakorolt hatását a leggyakoribb lekérdezések megtekintésének megismerése.
