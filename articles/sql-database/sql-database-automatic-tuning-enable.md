---
title: Az Azure SQL Database automatikus finomhangolása engedélyezése |} A Microsoft Docs
description: Az automatikus hangolás az Azure SQL Database könnyen engedélyezheti.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: e5f1f2fe3745ae2817b73b2500ee90a59fc283e1
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607081"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Lekérdezések figyelése és a számítási feladatok teljesítményének javítása automatikus hangolás engedélyezése

Azure SQL Database egy olyan automatikusan felügyelt szolgáltatás, amely folyamatosan figyeli a lekérdezések és az eseményekből eredő műveleteket is végezhet a számítási feladat teljesítményét azonosítja. Áttekinthesse javaslatok, és manuálisan alkalmazhatja őket, vagy lehetővé teszik az Azure SQL Database automatikusan alkalmazza a javítási műveleteket – ez az úgynevezett **automatikus hangolási módja**.

Az automatikus hangolás a kiszolgáló vagy az adatbázis szintjén keresztül engedélyezhető a [az Azure portal](sql-database-automatic-tuning-enable.md#azure-portal), [REST API-val](sql-database-automatic-tuning-enable.md#rest-api) hívások és [T-SQL](sql-database-automatic-tuning-enable.md#t-sql) parancsokat.

## <a name="enable-automatic-tuning-on-server"></a>A kiszolgáló automatikus hangolás engedélyezése
A kiszolgáló szintjén örökli az automatikus hangolás konfigurációját az "Azure alapértelmezett értéke" vagy a konfiguráció nem öröklésére választhat. Azure alapértelmezett FORCE_LAST_GOOD_PLAN engedélyezve van, engedélyezve van a CREATE_INDEX és DROP_INDEX le van tiltva.

### <a name="azure-portal"></a>Azure Portal
Ahhoz, hogy az automatikus hangolás az Azure SQL Database logikai **kiszolgáló**, keresse meg a kiszolgálót az Azure Portalon, majd **az automatikus hangolás** menüjében.

![Kiszolgáló](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Vegye figyelembe, hogy **DROP_INDEX** lehetőség jelenleg nem kompatibilis alkalmazások használatával a partíció közötti váltás és index mutatókat, és ezekben az esetekben nem szabad engedélyezni.
>

Válassza ki szeretné engedélyezni, és válassza ki az automatikus finomhangolási beállítások **alkalmaz**.

Automatikus hangolási beállításokat a kiszolgálón a kiszolgáló összes adatbázisára vonatkoznak. Alapértelmezés szerint minden adatbázis konfigurációs öröklése a szülőkiszolgálótól, de ez felül, és az egyes adatbázisok egyenként meghatározott.

### <a name="rest-api"></a>REST API

Tudjon meg többet a REST API használatával engedélyezze az automatikus hangolás egy kiszolgálón, olvassa el [SQL Server automatikus finomhangolási frissítés és a GET HTTP metódust](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).


## <a name="enable-automatic-tuning-on-an-individual-database"></a>Az egyes adatbázisok az automatikus hangolás engedélyezése

Az Azure SQL Database lehetővé teszi, hogy egyesével adja meg az egyes adatbázisok az automatikus hangolás konfigurációját. Az adatbázisszintű választhat automatikus hangolási konfiguráció öröklése a szülőkiszolgálótól "Az Azure alapértelmezett értéke", vagy nem öröklik a konfigurációt. Az Azure alapértelmezés szerint engedélyezve van a FORCE_LAST_GOOD_PLAN, CREATE_INDEX engedélyezve van, és DROP_INDEX le van tiltva.

> [!NOTE]
> Az általános ajánlás az, hogy kezelése, automatikus hangolási konfiguráció **kiszolgálószintű** így ugyanazokat a konfigurációs beállításokat az összes adatbázis automatikusan alkalmazható. Konfigurálja az automatikus hangolás be az egyes adatbázisok csak akkor, ha szükséges, hogy az adatbázis, mint a többi különböző beállítások tartoznak beállítások szülőcsoporttól ugyanarra a kiszolgálóra.
>

### <a name="azure-portal"></a>Azure Portal

Az automatikus hangolás engedélyezése egy **önálló adatbázis**, keresse meg az adatbázist az Azure Portalon, és válassza ki **az automatikus hangolás**.

Egyes automatikus hangolási beállításokat külön-külön konfigurálhatók az egyes adatbázisok. Manuálisan konfigurálhatja az egyes automatikus hangolási beállítást, vagy adja meg, hogy lehetőség beállításait örökli a kiszolgáló.

![Adatbázis](./media/sql-database-automatic-tuning-enable/database.png)

Vegye figyelembe, hogy DROP_INDEX lehetőség jelenleg nem kompatibilis alkalmazások használatával a partíció közötti váltás és index mutatókat és ezekben az esetekben nem szabad engedélyezni.

Miután kiválasztotta a kívánt beállításait, kattintson a **alkalmaz**.

### <a name="rest-api"></a>REST API-val

Tudjon meg többet a REST API-t használó önálló adatbázis az automatikus hangolás engedélyezése című témakörben talál [SQL Database automatikus finomhangolási frissítés és a GET HTTP metódust](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Engedélyezze az automatikus hangolás az egy egyetlen T-SQL-adatbázis, csatlakozzon az adatbázishoz, és hajtsa végre a következő lekérdezést:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
   ```
   
Automatikus automatikus hangolási beállítás alkalmazása az Azure alapértelmezés szerint. ÖRÖKLÉSI beállítást az automatikus hangolás konfigurációját öröklik a szülő-kiszolgáló. EGYÉNI kiválasztása, kell manuálisan konfigurálnia az automatikus hangolás.

Konfigurálja a T-SQL használatával az egyes automatikus hangolási lehetőségeket, csatlakozzon az adatbázishoz, és hajtsa végre a lekérdezést, például a:

   ```T-SQL
   ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
   ```
   
A beállítási lehetőségek állítja be, örökölt adatbázis-beállítások felülbírálása, valamint engedélyezi a hangolási beállítást. OFF értékre, is hatálytalanítja örökölt adatbázis-beállítások és a hangolási beállítás letiltása. Automatikus hangolási beállítás, amelynek alapértelmezett van megadva, a konfiguráció örökli az automatikus hangolási beállítás adatbázisszintű.  

Keresés szélesebb eredményez a T-SQL-beállítások konfigurálása az automatikus hangolás, lásd: [az ALTER DATABASE beállítások megadása (Transact-SQL) az SQL Database logikai kiszolgáló](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>A rendszer által letiltva
Az automatikus hangolás által figyelt vesz igénybe az adatbázis összes műveletet, és bizonyos esetekben képes meghatározni, hogy az automatikus hangolás nem megfelelően működik az adatbázison. Ebben a helyzetben hangolási beállítás letiltja a rendszer. A legtöbb esetben ennek oka az, a Query Store nincs engedélyezve, vagy egy adott adatbázis csak olvasható állapotban van.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Automatikus hangolási e-mail-értesítések konfigurálása

Lásd: [automatikus hangolási e-mail-értesítések](sql-database-automatic-tuning-email-notifications.md) útmutató.

## <a name="next-steps"></a>További lépések
* Olvassa el a [automatikus finomhangolási cikk](sql-database-automatic-tuning.md) további információ az automatikus hangolás, és hogyan tekintheti meg a teljesítmény javítása.
* Lásd: [teljesítménnyel kapcsolatos javaslatok](sql-database-advisor.md) áttekintheti az Azure SQL Database teljesítménnyel kapcsolatos javaslatok.
* Lásd: [lekérdezési teljesítmény Elemzéseihez](sql-database-query-performance.md) , a teljesítményre gyakorolt hatás, a lekérdezések megtekintésének megismerése.
