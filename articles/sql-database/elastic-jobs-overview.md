---
title: Elastic Database feladatok (előzetes verzió)
description: Elastic Database feladatok (előzetes verzió) konfigurálása a Transact-SQL (T-SQL) parancsfájlok futtatásához egy vagy több Azure SQL Database-adatbázison keresztül
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: e5b07ac0e9421cbca034b17c573cab16641f49f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79214476"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Rugalmas feladatok létrehozása, konfigurálása és kezelése

Ebből a cikkből megtudhatja, hogyan hozhat létre, konfigurálhat és kezelhet rugalmas feladatokat.

Ha nem használt rugalmas feladatokat, [Ismerkedjen meg a Azure SQL Database feladat-automatizálási fogalmakkal](sql-database-job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Az ügynök létrehozása és konfigurálása

1. Hozzon létre vagy azonosítson egy üres, S0 vagy magasabb szintű SQL-adatbázist. Ez az adatbázis lesz felhasználva a *feladatok adatbázisa* számára a rugalmas feladatok ügynökének létrehozása során.
2. Hozzon létre egy rugalmas feladatot a [portálon](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) vagy a [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent)-lel.

   ![Rugalmas feladatok ügynökének létrehozása](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Feladatok létrehozása, futtatása és kezelése

1. Hozzon létre egy hitelesítő adatot a feladatok végrehajtásához a *feladatok adatbázisban* a [PowerShell](elastic-jobs-powershell.md) vagy a [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution)használatával.
2. Adja meg a célcsoportot (azokat az adatbázisokat, amelyeken a feladatot futtatni szeretné) a [PowerShell](elastic-jobs-powershell.md) vagy a [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers)használatával.
3. Hozzon létre feladatügynök-hitelesítő adatokat minden adatbázisban, ahol a feladat futni fog [(adja hozzá a felhasználót (vagy a szerepkört) a csoport minden adatbázisához)](sql-database-manage-logins.md). Példaként tekintse meg a [PowerShell-oktatóanyagot](elastic-jobs-powershell.md).
4. Hozzon létre egy feladatot a [PowerShell](elastic-jobs-powershell.md) vagy a [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases)használatával.
5. Adjon hozzá feladatlépéseket a [PowerShell](elastic-jobs-powershell.md) vagy a [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases) használatával.
6. Futtasson egy feladatot a [PowerShell](elastic-jobs-powershell.md#run-the-job) vagy a [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job)használatával.
7. A feladatok végrehajtási állapotának figyelése a portál, a [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) vagy a [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status)használatával.

   ![Portál](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>A feladatok futtatásához használt hitelesítő adatok

A feladatok [adatbázishoz kötődő hitelesítő adatokat](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) használnak a célcsoport által végrehajtáskor meghatározott adatbázisokhoz történő csatlakozáshoz. Ha egy célcsoport kiszolgálókat vagy készleteket tartalmaz, az adatbázishoz kötődő hitelesítő adatokat a rendszer a master adatbázishoz történő csatlakozáshoz használja az elérhető adatbázisok számbavétele céljából.

Lehetséges, hogy a feladatfuttatáshoz szükséges megfelelő hitelesítő adatok beállítása nem egyértelmű, ezért tartsa szem előtt a következőket:

- Az adatbázis-hatókörrel rendelkező hitelesítő adatokat létre kell hozni a *feladatok adatbázisában*.
- **Az összes célként megadott adatbázisnak [megfelelő engedélyekkel](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) kell rendelkeznie ahhoz, hogy a feladatok sikeresen befejeződik** (`jobuser` az alábbi ábrán).
- A hitelesítő adatok újra felhasználhatók a feladatok között, a hitelesítő adatok jelszava pedig titkosítva van, és a feladat objektumaihoz csak olvasási hozzáféréssel rendelkező felhasználók férhetnek hozzá.

Az alábbi kép segítséget nyújt a megfelelő hitelesítő adatok megértéséhez és beállításához. **Ne feledje létrehozni a felhasználót minden olyan adatbázisban (minden *célként megjelölt felhasználói adatbázisban*), ahol a feladatot futtatni szeretné**.

![Rugalmas feladatokhoz tartozó hitelesítő adatok](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Ajánlott biztonsági eljárások

Néhány megfontolandó ajánlott eljárás a rugalmas feladatokkal végzett munkához:

- Az API-k felhasználását korlátozza megbízható személyekre.
- A hitelesítő adatok a feladatlépés végrehajtásához szükséges minimális engedélyekkel rendelkezzenek. További információ: [Engedélyezés és engedélyek SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- A kiszolgáló és/vagy a készlet célcsoportjának használatakor a rendszer erősen javasolja, hogy hozzon létre egy külön hitelesítő adatokat, amelyekkel a főadatbázison megtekintheti vagy listázhatja azokat az adatbázisokat, amelyekkel a feladatok végrehajtása előtt kibonthatja a kiszolgáló (k) és/vagy a készlet (ek) adatbázis-listáját.

## <a name="agent-performance-capacity-and-limitations"></a>Az ügynök teljesítménye, kapacitása és korlátai

A rugalmas feladatok minimális számítási erőforrást használnak, amíg a hosszan futó feladatok befejezésére várakoznak.

Az adatbázisok célcsoportjának méretétől és az adott feladat kívánt végrehajtási idejétől (az egyidejű feldolgozók számától) függően az ügynök különböző mértékű számítási kapacitást és teljesítményt igényel a *feladat-adatbázis* részéről (több cél és több feladat esetén nagyobb számítási kapacitás szükséges).

Jelenleg az előzetes verzió 100 feladat egyidejű futtatására képes.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>A feladatok a céladatbázis teljesítményét csökkentő hatásának megakadályozása

Ha szeretné elkerülni, hogy az erőforrások túlterheltek legyenek egy rugalmas SQL-készlet adatbázisain történő feladatvégrehajtás közben, akkor a feladatok esetén konfigurálhatja, hogy azok egyszerre legfeljebb hány adatbázison futhatnak.

Állítsa be a feladat által futtatott egyidejű adatbázisok számát úgy, hogy `sp_add_jobstep` a tárolt eljárás `@max_parallelism` paraméterét a T-SQL- `Add-AzSqlElasticJobStep -MaxParallelism` ben vagy a PowerShellben állítja be.

## <a name="best-practices-for-creating-jobs"></a>Ajánlott eljárások feladatok létrehozásához

### <a name="idempotent-scripts"></a>Idempotens szkriptek
A feladatok T-SQL-szkriptjeinek [idempotensnek](https://en.wikipedia.org/wiki/Idempotence) kell lenniük. Az **idempotens** azt jelenti, hogy a sikeres szkript újrafuttatása esetén ugyanaz az eredmény várható. A szkript végrehajtása átmeneti hálózati problémák következtében meghiúsulhat. Ebben az esetben a feladat automatikusan újrapróbálkozik, és a megadott számú alkalommal ismételten futtatja a szkriptet, mielőtt feladná azt. Az idempotens szkriptek akkor is azonos eredménnyel járnak, ha már két (vagy több) alkalommal sikeresen futottak.

A legegyszerűbb módszer, ha ellenőrzi az objektum meglétét, mielőtt létrehozná.


```sql
IF NOT EXISTS (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Ehhez hasonlóan a szkriptnek sikeresen végrehajthatónak kell lennie az észlelt feltételek logikai tesztelése és azokkal ellentétes műveletek révén.



## <a name="next-steps"></a>További lépések

- [Rugalmas feladatok létrehozása és kezelése a PowerShell-lel](elastic-jobs-powershell.md)
- [Rugalmas feladatok létrehozása és kezelése a Transact-SQL (T-SQL) használatával](elastic-jobs-tsql.md)
