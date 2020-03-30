---
title: Automatikus hangolás engedélyezése
description: Az Azure SQL Database automatikus hangolását egyszerűen engedélyezheti.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: eed839c277156046ff9b7d97c6e87636a0822889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299328"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Automatikus hangolás engedélyezése a lekérdezések figyeléséhez és a számítási feladatok teljesítményének javításához

Az Azure SQL Database egy automatikusan felügyelt adatszolgáltatás, amely folyamatosan figyeli a lekérdezéseket, és azonosítja a számítási feladatok teljesítményének javítása érdekében végrehajtható műveletet. Áttekintheti a javaslatokat, és manuálisan alkalmazhatja őket, vagy lehetővé teheti, hogy az Azure SQL Database automatikusan alkalmazza a korrekciós műveleteket – ezt **automatikus hangolási módnak nevezzük.**

Az automatikus hangolás engedélyezhető a kiszolgálón vagy az adatbázis szintjén az [Azure Portalon,](sql-database-automatic-tuning-enable.md#azure-portal)a [REST API-hívásokon](sql-database-automatic-tuning-enable.md#rest-api) és a [T-SQL-parancsokon](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) keresztül.

> [!NOTE]
> A felügyelt példány esetében a támogatott FORCE_LAST_GOOD_PLAN csak [T-SQL-en](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) keresztül konfigurálható. A portálalapú konfigurációs és az ebben a cikkben ismertetett automatikus indexhangolási beállítások nem vonatkoznak a felügyelt példányra.

> [!NOTE]
> Az automatikus hangolási beállítások konfigurálása az ARM (Azure Resource Manager) sablonon keresztül jelenleg nem támogatott.

## <a name="enable-automatic-tuning-on-server"></a>Automatikus hangolás engedélyezése a kiszolgálón

A kiszolgáló szintjén választhat, hogy örökli az automatikus hangolási konfiguráció "Azure defaults", vagy nem örökli a konfigurációt. Az Azure alapértelmezései FORCE_LAST_GOOD_PLAN engedélyezve van, CREATE_INDEX engedélyezve van, és DROP_INDEX le van tiltva.

> [!IMPORTANT]
> Márciustól az automatikus hangoláshoz 2020-ig az Azure alapértelmezett módosításai a következőképpen lépnek érvénybe:
>
> - Az új Azure-beállítások FORCE_LAST_GOOD_PLAN = engedélyezve lesznek, CREATE_INDEX = letiltva és DROP_INDEX = letiltva.
> - Az automatikus hangolási beállításokkal nem rendelkező meglévő kiszolgálók automatikusan konfigurálva lesznek az új Azure-alapértelmezett értékek örökléséhez. Ez minden olyan ügyfélre vonatkozik, akik nek jelenleg nincs kiszolgálóbeállítása az automatikus hangoláshoz, meghatározatlan állapotban.
> - Az új onnan létrehozott új kiszolgálók automatikusan úgy lesznek konfigurálva, hogy örököljék az új Azure-alapértelmezett értékeket (ellentétben a korábbiakkal, amikor az automatikus hangolási konfiguráció nem definiált állapotban volt az új kiszolgáló létrehozásakor).

### <a name="azure-portal"></a>Azure portál

Az Azure SQL Database logikai **kiszolgálóautomatikus**hangolásának engedélyezéséhez keresse meg a kiszolgálót az Azure Portalon, és válassza az **Automatikus hangolás** parancsot a menüben.

![Kiszolgáló](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Kérjük, vegye figyelembe, hogy **DROP_INDEX** lehetőség jelenleg nem kompatibilis a partíciókapcsolást és indextippeket használó alkalmazásokkal, és ezekben az esetekben nem engedélyezhető. A nem használt indexek eldobása nem támogatott a prémium és az üzleti legkritikusabb szolgáltatási szinteken.
>

Adja meg az engedélyezni kívánt automatikus hangolási beállításokat, és válassza **az Alkalmaz**lehetőséget.

A kiszolgáló automatikus hangolási beállításai a kiszolgáló összes adatbázisára érvényesek. Alapértelmezés szerint minden adatbázis örökli a konfigurációt a szülőkiszolgálótól, de ez felülbírálható és minden egyes adatbázishoz külön-külön megadható.

### <a name="rest-api"></a>REST API

További információ a REST API használatával az automatikus hangolás kiszolgálón való engedélyezéséről az [SQL Server automatikus hangolási update és get HTTP metódusai című témakörben](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)talál további információ.

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Automatikus hangolás engedélyezése egy adott adatbázison

Az Azure SQL Database lehetővé teszi, hogy külön-külön adja meg az egyes adatbázisok automatikus hangolási konfigurációját. Az adatbázis szintjén választhat, hogy örökli az automatikus hangolási konfiguráció a szülő kiszolgáló, "Azure defaults", vagy nem örökli a konfigurációt. Az Azure defaults van beállítva, hogy FORCE_LAST_GOOD_PLAN engedélyezve van, CREATE_INDEX engedélyezve van, és DROP_INDEX le van tiltva.

> [!TIP]
> Az általános javaslat az automatikus hangolási konfiguráció **kiszolgálói szinten** történő kezelése, hogy minden adatbázisban automatikusan ugyanazok at lehessen alkalmazni. Csak akkor konfigurálja az automatikus hangolást egy adott adatbázison, ha az adatbázis beállításai eltérőek, mint a többi, ugyanattól a kiszolgálótól öröklő beállításoknak.
>

### <a name="azure-portal"></a>Azure portál

Ha engedélyezni szeretné **az**automatikus hangolást egyetlen adatbázison, keresse meg az adatbázist az Azure Portalon, és válassza az **Automatikus hangolás**lehetőséget.

Az egyes automatikus hangolási beállítások minden adatbázishoz külön konfigurálhatók. Manuálisan konfigurálhat egy egyedi automatikus hangolási beállítást, vagy megadhatja, hogy egy beállítás örökölje a beállításokat a kiszolgálótól.

![Adatbázis](./media/sql-database-automatic-tuning-enable/database.png)

Kérjük, vegye figyelembe, hogy DROP_INDEX lehetőség jelenleg nem kompatibilis a partíciókapcsolást és indextippeket használó alkalmazásokkal, és ezekben az esetekben nem engedélyezhető.

Miután kiválasztotta a kívánt konfigurációt, kattintson az **Alkalmaz gombra.**

### <a name="rest-api"></a>Rest API

További információ a REST API használatával az automatikus hangolás engedélyezéséről egyetlen adatbázisban, lásd: [SQL Database Automatic tuning UPDATE és GET HTTP methods](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Ha engedélyezni szeretné az automatikus hangolást egyetlen adatbázison a T-SQL-en keresztül, csatlakozzon az adatbázishoz, és hajtsa végre a következő lekérdezést:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Az automatikus hangolás auto beállításával az Azure Defaults lesz. Ha az ÖRÖKLÉS beállításra állítja, az automatikus hangolási konfiguráció a szülőkiszolgálótól öröklődik. A CUSTOM lehetőséget választva manuálisan kell konfigurálnia az automatikus hangolást.

Ha egyéni automatikus hangolási beállításokat szeretne konfigurálni a T-SQL-en keresztül, csatlakozzon az adatbázishoz, és hajtsa végre a lekérdezést, például ezt:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Ha az egyes hangolási beállítást BE-re állítja, felülír minden olyan beállítást, amelyet az adatbázis örökölt, és engedélyezi a hangolási beállítást. Ha ki van állítva, az adatbázis által örökölt beállításokat is felülírja, és letiltja a hangolási beállítást. Az automatikus hangolási beállítás, amelyhez az DEFAULT van megadva, örökli az automatikus hangolási konfigurációt a kiszolgálószint-beállításoktól.  

> [!IMPORTANT]
> [Aktív georeplikáció](sql-database-auto-failover-group.md)esetén az automatikus hangolást csak az elsődleges adatbázisban kell konfigurálni. Automatikusan alkalmazott hangolási műveletek, például index létrehozása vagy törlése automatikusan replikálódik az írásvédett másodlagos. Az automatikus hangolás t-SQL-en keresztüli engedélyezésének megkísérlése az írásvédett másodlagos on a hibát eredményez, mivel az írásvédett másodlagos eltérő hangolási konfigurációval rendelkezik.
>

További abut T-SQL beállítások az Automatikus hangolás konfigurálásához, [lásd: ALTER DATABASE SET Options (Transact-SQL) for SQL Database server](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>A rendszer letiltotta

Az automatikus hangolás figyeli az adatbázison végrehajtott összes műveletet, és bizonyos esetekben megállapíthatja, hogy az automatikus hangolás nem működik megfelelően az adatbázison. Ebben az esetben a rendszer letiltja a hangolási beállítást. A legtöbb esetben ez azért történik, mert a Query Store nincs engedélyezve, vagy csak olvasható állapotban van egy adott adatbázisban.

## <a name="permissions"></a>Engedélyek

Mivel az automatikus hangolás az Azure-funkció, annak használatához az Azure beépített RBAC szerepköröket kell használnia. Az SQL-hitelesítés használata csak nem lesz elegendő az Azure Portal szolgáltatás használatához.

Az automatikus hangolás használatához a minimálisan szükséges engedélyt adni a felhasználó nak az Azure beépített [SQL DB közreműködői](../role-based-access-control/built-in-roles.md#sql-db-contributor) szerepkör. Magasabb jogosultsági szerepkörök et is használhat, például sql server közreműködő, közreműködő és tulajdonos.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Automatikus hangolási e-mail értesítések konfigurálása

Lásd az [automatikus hangolási e-mail értesítések](sql-database-automatic-tuning-email-notifications.md) útmutatóját.

## <a name="next-steps"></a>További lépések

* Az [Automatikus hangolás című cikkben](sql-database-automatic-tuning.md) többet is megtudhat az automatikus hangolásról és ateljesítmény javításáról.
* Az Azure SQL Database teljesítményre vonatkozó javaslatainak áttekintését [a Teljesítményre vonatkozó javaslatok](sql-database-advisor.md) című témakörben találja.
* A [Query Performance Insights](sql-database-query-performance.md) című témakörben olvashat a leggyakoribb lekérdezések teljesítményre gyakorolt hatásának megtekintéséről.
