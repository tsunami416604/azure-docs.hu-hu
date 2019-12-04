---
title: Automatikus hangolás engedélyezése
description: Egyszerűen engedélyezheti a Azure SQL Database automatikus finomhangolását.
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
ms.openlocfilehash: bdd33d85ee0aac4808c343af088d4db1a0dc963e
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74767772"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Automatikus hangolás engedélyezése a lekérdezések figyeléséhez és a munkaterhelés teljesítményének növeléséhez

A Azure SQL Database egy automatikusan felügyelt adatszolgáltatás, amely folyamatosan figyeli a lekérdezéseket, és azonosítja a munkaterhelések teljesítményének növeléséhez végrehajtható műveletet. Áttekintheti az ajánlásokat, és manuálisan alkalmazhatja őket, vagy engedélyezheti Azure SQL Database a javítási műveletek automatikus alkalmazását – ezt az **Automatikus hangolási módot**nevezzük.

Az automatikus hangolás a kiszolgálón vagy az adatbázis szintjén engedélyezhető a [Azure Portalon](sql-database-automatic-tuning-enable.md#azure-portal), [REST API](sql-database-automatic-tuning-enable.md#rest-api) hívásokon és a [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) parancsokon keresztül.

> [!NOTE]
> Felügyelt példány esetén a FORCE_LAST_GOOD_PLAN támogatott beállítás csak a [T-SQL-](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) en keresztül konfigurálható. A jelen cikkben ismertetett, a portálon alapuló konfiguráció és az automatikus Indexelési beállítások nem vonatkoznak a felügyelt példányokra.

> [!NOTE]
> Az Automatikus hangolási beállítások az ARM (Azure Resource Manager) sablonnal történő konfigurálása jelenleg nem támogatott.

## <a name="enable-automatic-tuning-on-server"></a>Automatikus hangolás engedélyezése a kiszolgálón

A kiszolgáló szintjén dönthet úgy, hogy az "Azure Defaults" értékről örökli az Automatikus hangolási konfigurációt, vagy nem örökli a konfigurációt. Az Azure alapértelmezett beállításai FORCE_LAST_GOOD_PLAN engedélyezve vannak, CREATE_INDEX engedélyezve van, és a DROP_INDEX le van tiltva.

### <a name="azure-portal"></a>Azure Portal

Ha engedélyezni szeretné az automatikus hangolást Azure SQL Database logikai **kiszolgálón**, keresse meg a kiszolgálót a Azure Portal, majd válassza az **automatikus hangolás** lehetőséget a menüben.

![Kiszolgáló](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Vegye figyelembe, hogy az **DROP_INDEX** lehetőség jelenleg nem kompatibilis a partíciós váltást és az indexeket használó alkalmazásokkal, és ezeket az eseteket nem szabad engedélyezni. A nem használt indexek nem támogatottak a prémium és üzletileg kritikus szolgáltatási szinteken.
>

Válassza ki az engedélyezni kívánt Automatikus hangolási beállításokat, és kattintson az **alkalmaz**gombra.

A kiszolgálón az Automatikus hangolási beállítások a kiszolgálón lévő összes adatbázisra vonatkoznak. Alapértelmezés szerint az összes adatbázis örökli a konfigurációt a szülő kiszolgálóról, de ez felülbírálható és megadható minden egyes adatbázishoz.

### <a name="rest-api"></a>REST API

További információ a kiszolgálók automatikus hangolásának engedélyezéséről REST API használatáról SQL Server: az [automatikus Finomhangolás frissítése és a http-metódusok BEolvasása](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Automatikus hangolás engedélyezése egy adott adatbázison

A Azure SQL Database lehetővé teszi az egyes adatbázisok Automatikus hangolási konfigurációjának egyedi megadását. Az adatbázis szintjén dönthet úgy, hogy örökli az Automatikus hangolási konfigurációt a szülő kiszolgálóról, az "Azure Defaults" vagy a konfiguráció öröklése nélkül. Az Azure alapértelmezett beállításai FORCE_LAST_GOOD_PLAN engedélyezve, CREATE_INDEX engedélyezve van, és a DROP_INDEX le van tiltva.

> [!TIP]
> Az általános javaslat az Automatikus hangolási konfiguráció kezelése a **kiszolgáló szintjén** , így ugyanazokat a konfigurációs beállításokat lehet minden adatbázison automatikusan alkalmazni. Az automatikus hangolást csak akkor konfigurálja egy adott adatbázison, ha az adatbázisnak eltérő beállításokkal kell rendelkeznie, mint mások, mint a beállítások öröklése ugyanarról a kiszolgálóról.
>

### <a name="azure-portal"></a>Azure Portal

**Egyetlen adatbázis**automatikus finomhangolásának engedélyezéséhez navigáljon a Azure Portal adatbázisára, és válassza az **automatikus hangolás**lehetőséget.

Az egyes adatbázisokhoz külön konfigurálható az automatikus finomhangolási beállítások. Manuálisan is konfigurálhat egyéni Automatikus hangolási beállítást, vagy megadhatja, hogy egy beállítás örökli a beállításait a-kiszolgálótól.

![Adatbázis](./media/sql-database-automatic-tuning-enable/database.png)

Vegye figyelembe, hogy az DROP_INDEX lehetőség jelenleg nem kompatibilis a partíciós váltást és az indexeket használó alkalmazásokkal, és ezeket az eseteket nem szabad engedélyezni.

Miután kiválasztotta a kívánt konfigurációt, kattintson az **alkalmaz**gombra.

### <a name="rest-api"></a>REST API

További információ a REST API használatáról egyetlen adatbázis automatikus finomhangolásának engedélyezéséhez: [SQL Database Automatikus hangolási frissítés és http-metódusok BEolvasása](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Ha engedélyezni szeretné az automatikus finomhangolást egyetlen adatbázison a T-SQL használatával, kapcsolódjon az adatbázishoz, és hajtsa végre a következő lekérdezést:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Az automatikus Finomhangolás automatikus beállítása az Azure alapértelmezett beállításait fogja alkalmazni. Az öröklés beállításakor a rendszer az Automatikus hangolási konfigurációt örökli a fölérendelt kiszolgálótól. Az egyéni lehetőséget választva manuálisan kell konfigurálnia az automatikus hangolást.

Az egyes Automatikus hangolási beállítások T-SQL-kapcsolaton keresztüli konfigurálásához kapcsolódjon az adatbázishoz, és hajtsa végre a lekérdezést, például:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Ha az egyéni hangolási beállítást be értékre állítja, a felülbírálja az adatbázis által örökölt beállításokat, és engedélyezte a hangolási beállítást. Ha kikapcsolja a beállítást, az az adatbázis által örökölt beállításokat is felülbírálja, és letiltja a hangolási beállítást. Az Automatikus hangolási beállítás, amelynél az alapértelmezett érték meg van adva, a a kiszolgálói szintű beállításoktól örökli az Automatikus hangolási konfigurációt.  

> [!IMPORTANT]
> [Aktív földrajzi replikálás](sql-database-auto-failover-group.md)esetén az automatikus finomhangolást csak az elsődleges adatbázison kell konfigurálni. Automatikusan alkalmazott hangolási műveletek, például index létrehozása vagy törlése automatikusan replikálódnak a csak olvasható másodlagosra. Ha a csak olvasási jogosultsággal rendelkező T-SQL-n keresztül szeretné engedélyezni az automatikus hangolást, a rendszer hibát okoz, mivel a csak olvasási jogosultsággal rendelkező másodlagos konfiguráció nem támogatott.
>

További vmivel T-SQL-beállítások az automatikus hangolás konfigurálásához: [az adatbázis-beállítási beállítások módosítása (Transact-SQL) SQL Database kiszolgálóhoz](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>A rendszer letiltotta

Az automatikus hangolás az adatbázison végrehajtott összes műveletet figyeli, és bizonyos esetekben megállapíthatja, hogy az automatikus hangolás nem tud megfelelően működni az adatbázison. Ebben az esetben a rendszer letiltja a hangolási beállítást. A legtöbb esetben ez azért történik, mert a lekérdezési tár nincs engedélyezve, vagy csak olvasható állapotban van egy adott adatbázison.

## <a name="permissions"></a>Engedélyek

Mivel az automatikus hangolás az Azure-szolgáltatás, a használatához az Azure beépített RBAC-szerepköreit kell használnia. Az SQL-hitelesítés használata nem elegendő a funkció Azure Portalból való használatához.

Az automatikus hangolás használatához minimálisan szükséges engedély a felhasználónak az Azure beépített [SQL-adatbázis-közreműködői](../role-based-access-control/built-in-roles.md#sql-db-contributor) szerepköre. Érdemes lehet magasabb jogosultsági szerepköröket használni, például SQL Server közreműködő, közreműködő és tulajdonos.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>E-mail értesítések automatikus hangolásának konfigurálása

Lásd: az [e-mail értesítések automatikus finomhangolása](sql-database-automatic-tuning-email-notifications.md) útmutató.

## <a name="next-steps"></a>Következő lépések

* Az automatikus hangolással kapcsolatos további információkért olvassa el az automatikus finomhangolásról szóló [cikket](sql-database-automatic-tuning.md) , és azt, hogy miként segíthet a teljesítmény javításában.
* Tekintse meg a teljesítményre vonatkozó [javaslatokat](sql-database-advisor.md) a Azure SQL Database teljesítményével kapcsolatos javaslatok áttekintéséhez.
* A leggyakoribb lekérdezések teljesítményre gyakorolt hatásának megtekintéséhez tekintse meg a [lekérdezési teljesítmény](sql-database-query-performance.md) elemzését ismertető témakört.
