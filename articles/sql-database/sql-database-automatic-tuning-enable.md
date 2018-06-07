---
title: Az Azure SQL Database automatikus hangolása engedélyezése |} Microsoft Docs
description: Automatikus hangolása az Azure SQL adatbázis könnyen engedélyezheti.
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: vvasic
ms.openlocfilehash: d4d3b7f54c7393b57339ea149e8a79f97891dc20
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646031"
---
# <a name="enable-automatic-tuning"></a>Automatikus hangolás engedélyezése

Az Azure SQL Database az automatikusan kezelt adatok szolgáltatása, amely folyamatosan figyeli a lekérdezéseket, és azonosítja a végrehajtandó műveletet a számítási feladatok teljesítményének javításával végezheti el. Áttekintheti javaslatok és manuálisan alkalmazhatja azokat, vagy lehetővé teszik az Azure SQL Database automatikusan alkalmazza a javítási műveleteket – ez az úgynevezett **automatikus hangolási módot**. Az automatikus hangolással a kiszolgáló vagy az adatbázis szintjén engedélyezhető.

## <a name="enable-automatic-tuning-on-server"></a>Engedélyezze a kiszolgálón automatikus hangolása
A kiszolgáló szintjén választhat öröklik az "Azure alapértelmezett értéke" automatikus hangolási beállítás, vagy nem az, hogy örökölje a konfigurációt. Az Azure azok FORCE_LAST_GOOD_PLAN engedélyezve van, engedélyezve van a CREATE_INDEX és DROP_INDEX le van tiltva.

### <a name="azure-portal"></a>Azure Portal
Ahhoz, hogy az Azure SQL Database logikai automatikus hangolása **server**, keresse fel a kiszolgálót az Azure portálon, és válassza ki **automatikus hangolása** a menüben.

![Kiszolgáló](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Ne feledje, hogy **DROP_INDEX** beállítás jelenleg nem kompatibilis a partíció váltás és index mutatókat használó alkalmazások és ezekben az esetekben nem engedélyezhető.
>

Válassza ki a engedélyezése, és válassza ki az automatikus hangolási lehetőségeket **alkalmaz**.

Automatikus hangolási lehetőségeket a kiszolgálón lévő összes adatbázis is vonatkozik. Alapértelmezés szerint minden adatbázisok konfigurációs öröklése a fölérendelt kiszolgáló, de ez felül, és az egyes adatbázisok külön-külön megadva.

### <a name="rest-api"></a>REST API
[Ide kattintva további engedélyezéséről a REST API-n keresztül a kiszolgáló szintjén automatikus hangolása](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Egyedi adatbázis automatikus hangolása engedélyezése

Az Azure SQL Database lehetővé teszi az automatikus hangolási beállítás, az egyes adatbázisok külön-külön megadását. Az adatbázis szintjén választhat automatikus hangolási beállítás öröklése a fölérendelt kiszolgáló, "Azure alapértelmezett értéke", vagy nem az, hogy örökölje a konfigurációt. Az Azure alapértelmezés szerint engedélyezve van a FORCE_LAST_GOOD_PLAN, CREATE_INDEX engedélyezve van, és DROP_INDEX le van tiltva.

> [!NOTE]
> Az általános javasoljuk, hogy az automatikus hangolási beállítás, kezelése **kiszolgálószintű** , ugyanazokat a konfigurációs beállításokat alkalmazhassa az összes adatbázis automatikusan. Konfigurálja az automatikus hangolással a egyedi adatbázis csak akkor, ha szükséges, hogy az adatbázis különböző beállításokat, mint a többire beállításokat örököl ugyanarra a kiszolgálóra.
>

### <a name="azure-portal"></a>Azure Portal

Ahhoz, hogy az automatikus hangolással a **egyetlen adatbázis**, keresse meg az adatbázist, az Azure portálon, és válassza ki **automatikus hangolása**.

Egyes automatikus hangolási beállításainak külön konfigurálhatók az egyes adatbázisok. Az egyes automatikus hangolási beállítás, vagy adja meg, hogy egy beállítást beállításait örökli a kiszolgáló manuálisan.

![Adatbázis](./media/sql-database-automatic-tuning-enable/database.png)

Vegye figyelembe, hogy DROP_INDEX beállítás jelenleg nem kompatibilis a partíció váltás és index mutatókat használó alkalmazások és ezekben az esetekben nem engedélyezhető.

Miután kiválasztotta a kívánt konfiguráció, kattintson a **alkalmaz**.

### <a name="rest-api"></a>REST API
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

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Automatikus hangolási e-mail értesítések konfigurálása

Lásd: [automatikus e-mail értesítések beállítása](sql-database-automatic-tuning-email-notifications.md)

## <a name="next-steps"></a>További lépések
* Olvassa el a [automatikus hangolási cikk](sql-database-automatic-tuning.md) tudhat meg többet automatikus hangolása és szerepéről javítják a teljesítményt.
* Lásd: [teljesítmény javaslatok](sql-database-advisor.md) teljesítmény javaslatok az Azure SQL Database áttekintését.
* Lásd: [lekérdezési teljesítmény Insights](sql-database-query-performance.md) a teljesítményre gyakorolt hatását a leggyakoribb lekérdezések megtekintésének megismerése.
