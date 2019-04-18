---
title: Azure SQL rugalmas adatbázis-feladatok | Microsoft Docs
description: Transact-SQL (T-SQL) szkriptek futtatásához egy vagy több Azure SQL Database-adatbázisok több rugalmas adatbázis-feladatok konfigurálása
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srinia
ms.author: srinia
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 62efee57f3663f1dad0446da659de16d2800bf75
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264420"
---
# <a name="create-configure-and-manage-elastic-jobs"></a>Létrehozása, konfigurálása és kezelése rugalmas feladatokkal

Ebből a cikkből megtudhatja, létrehozása, konfigurálása és kezelése rugalmas feladatokkal. Ha még nem használta a rugalmas feladatok [tudjon meg többet az Azure SQL Database, a feladat automation fogalmak](sql-database-job-automation-overview.md).

## <a name="create-and-configure-the-agent"></a>Az ügynök létrehozása és konfigurálása

1. Hozzon létre vagy azonosítson egy üres, S0 vagy magasabb szintű SQL-adatbázist. Ezt az adatbázist fogja használni a *feladat adatbázis* rugalmas feladat ügynök létrehozása során.
2. Hozzon létre egy rugalmasfeladat-ügynököt a [portál](https://portal.azure.com/#create/Microsoft.SQLElasticJobAgent) vagy a [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent) használatával.

   ![Feladatügynök létrehozása](media/elastic-jobs-overview/create-elastic-job-agent.png)

## <a name="create-run-and-manage-jobs"></a>Feladatok létrehozása, futtatása és kezelése

1. Hozzon létre egy hitelesítő adatot a feladat végrehajtásához a *feladat-adatbázisban* a [PowerShell](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets) vagy a [T-SQL](elastic-jobs-tsql.md#create-a-credential-for-job-execution) használatával.
2. Határozza meg a célcsoportot (az adatbázisokat, amelyeken a feladatot futtatni szeretné) a [PowerShell](elastic-jobs-powershell.md#define-the-target-databases-you-want-to-run-the-job-against) vagy a [T-SQL](elastic-jobs-tsql.md#create-a-target-group-servers) használatával.
3. Hozzon létre feladatügynök-hitelesítő adatokat minden adatbázisban, ahol a feladat futni fog [(adja hozzá a felhasználót (vagy a szerepkört) a csoport minden adatbázisához)](sql-database-control-access.md). Példaként tekintse meg a [PowerShell-oktatóanyagot](elastic-jobs-powershell.md#create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets).
4. Hozzon létre egy feladatot a [PowerShell](elastic-jobs-powershell.md#create-a-job) vagy a [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases) használatával.
5. Adjon hozzá feladatlépéseket a [PowerShell](elastic-jobs-powershell.md#create-a-job-step) vagy a [T-SQL](elastic-jobs-tsql.md#deploy-new-schema-to-many-databases) használatával.
6. Futtasson egy feladatot a [PowerShell](elastic-jobs-powershell.md#run-the-job) vagy a [T-SQL](elastic-jobs-tsql.md#begin-ad-hoc-execution-of-a-job) használatával.
7. Monitorozza a feladatvégrehajtás állapotát a portál, a [PowerShell](elastic-jobs-powershell.md#monitor-status-of-job-executions) vagy a [T-SQL](elastic-jobs-tsql.md#monitor-job-execution-status) használatával.

   ![Portál](media/elastic-jobs-overview/elastic-job-executions-overview.png)

## <a name="credentials-for-running-jobs"></a>A feladatok futtatásához használt hitelesítő adatok

A feladatok [adatbázishoz kötődő hitelesítő adatokat](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) használnak a célcsoport által végrehajtáskor meghatározott adatbázisokhoz történő csatlakozáshoz. Ha egy célcsoport kiszolgálókat vagy készleteket tartalmaz, az adatbázishoz kötődő hitelesítő adatokat a rendszer a master adatbázishoz történő csatlakozáshoz használja az elérhető adatbázisok számbavétele céljából.

Lehetséges, hogy a feladatfuttatáshoz szükséges megfelelő hitelesítő adatok beállítása nem egyértelmű, ezért tartsa szem előtt a következőket:

- Az adatbázishoz kötődő hitelesítő adatokat a *feladat-adatbázisban* kell létrehozni.
- **Az összes céladatbázis rendelkeznie kell egy bejelentkezés [megfelelő engedélyekkel](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) sikeresen befejeződik a feladat** (`jobuser` az alábbi ábrán).
- Hitelesítő adatok felhasználhatók a feladatokat, és a hitelesítő adatok jelszavak titkosítva és védve legyen a feladat-objektumokhoz csak olvasási hozzáféréssel rendelkező felhasználók számára.

Az alábbi kép segítséget nyújt a megfelelő hitelesítő adatok megértéséhez és beállításához. **Ne feledje létrehozni a felhasználót minden olyan adatbázisban (minden *célként megjelölt felhasználói adatbázisban*), ahol a feladatot futtatni szeretné**.

![Rugalmas feladatokhoz tartozó hitelesítő adatok](media/elastic-jobs-overview/job-credentials.png)

## <a name="security-best-practices"></a>Ajánlott biztonsági eljárások

Néhány megfontolandó ajánlott eljárás a rugalmas feladatokkal végzett munkához:

- Az API-k felhasználását korlátozza megbízható személyekre.
- A hitelesítő adatok a feladatlépés végrehajtásához szükséges minimális engedélyekkel rendelkezzenek. További információkért lásd: [engedélyezési és az engedélyek SQL Server](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server).
- Egy kiszolgálón és/vagy a célként megadott csoportot készlettag használatakor, erősen ajánlott külön hitelesítő adatok létrehozása, amellyel bontsa ki a kiszolgáló (ko) és / vagy tárolókészleteit a feladat végrehajtása előtt az adatbázis listák adatbázisok megjelenítéséhez a master adatbázisban az jogosultsággal rendelkező.

## <a name="agent-performance-capacity-and-limitations"></a>Az ügynök teljesítménye, kapacitása és korlátai

A rugalmas feladatok minimális számítási erőforrást használnak, amíg a hosszan futó feladatok befejezésére várakoznak.

Az adatbázisok célcsoportjának méretétől és az adott feladat kívánt végrehajtási idejétől (az egyidejű feldolgozók számától) függően az ügynök különböző mértékű számítási kapacitást és teljesítményt igényel a *feladat-adatbázis* részéről (több cél és több feladat esetén nagyobb számítási kapacitás szükséges).

Jelenleg az előzetes verzió 100 feladat egyidejű futtatására képes.

### <a name="prevent-jobs-from-reducing-target-database-performance"></a>A feladatok a céladatbázis teljesítményét csökkentő hatásának megakadályozása

Ha szeretné elkerülni, hogy az erőforrások túlterheltek legyenek egy rugalmas SQL-készlet adatbázisain történő feladatvégrehajtás közben, akkor a feladatok esetén konfigurálhatja, hogy azok egyszerre legfeljebb hány adatbázison futhatnak.

Állítsa be a feladat fut, beállításával egyidejű adatbázisok száma a `sp_add_jobstep` tárolt eljárást a `@max_parallelism` paraméter a T-SQL, vagy `Add-AzSqlElasticJobStep -MaxParallelism` a PowerShellben.

## <a name="best-practices-for-creating-jobs"></a>Ajánlott eljárások feladatok létrehozásához

### <a name="idempotent-scripts"></a>Idempotens szkriptek
A feladatok T-SQL-szkriptjeinek [idempotensnek](https://en.wikipedia.org/wiki/Idempotence) kell lenniük. Az **idempotens** azt jelenti, hogy a sikeres szkript újrafuttatása esetén ugyanaz az eredmény várható. A szkript végrehajtása átmeneti hálózati problémák következtében meghiúsulhat. Ebben az esetben a feladat automatikusan újrapróbálkozik, és a megadott számú alkalommal ismételten futtatja a szkriptet, mielőtt feladná azt. Az idempotens szkriptek akkor is azonos eredménnyel járnak, ha már két (vagy több) alkalommal sikeresen futottak.

A legegyszerűbb módszer, ha ellenőrzi az objektum meglétét, mielőtt létrehozná.


```sql
IF NOT EXIST (some_object)
    -- Create the object
    -- If it exists, drop the object before recreating it.
```

Ehhez hasonlóan a szkriptnek sikeresen végrehajthatónak kell lennie az észlelt feltételek logikai tesztelése és azokkal ellentétes műveletek révén.



## <a name="next-steps"></a>További lépések

- [Rugalmas feladatok létrehozása és kezelése a PowerShell használatával](elastic-jobs-powershell.md)
- [Rugalmas feladatok létrehozása és kezelése a Transact-SQL (T-SQL) használatával](elastic-jobs-tsql.md)
