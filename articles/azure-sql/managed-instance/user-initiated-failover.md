---
title: Feladatátvétel manuális kezdeményezése SQL felügyelt példányon
description: Ismerje meg, hogyan végezheti el az elsődleges és másodlagos replikák manuális feladatátvételét az Azure SQL felügyelt példányain.
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
ms.date: 08/12/2020
ms.openlocfilehash: e1a5cb4a5ce02954a14a6936ec14379701354a79
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191199"
---
# <a name="user-initiated-manual-failover-on-sql-managed-instance"></a>Felhasználó által kezdeményezett manuális feladatátvétel a felügyelt SQL-példányon

Ez a cikk azt ismerteti, hogyan lehet manuálisan feladatátvételt elvégezni az SQL felügyelt példányok általános célú (GP) és a üzletileg kritikus (BC) szolgáltatáshoz tartozó elsődleges csomópontok számára, és hogyan lehet manuálisan feladatátvételt elvégezni a csak a BC szolgáltatási szinten található másodlagos írásvédett replika csomóponton.

## <a name="when-to-use-manual-failover"></a>Mikor kell manuális feladatátvételt használni

A [magas rendelkezésre állás](../database/high-availability-sla.md) az SQL felügyelt példányok platformjának alapvető része, amely transzparens módon működik az adatbázis-alkalmazásokhoz. Az elsődlegestől a másodlagos csomópontok felé irányuló feladatátvételek csomópontok romlása vagy meghibásodása esetén, illetve a normál havi szoftverfrissítések esetében várhatóan előfordul minden olyan alkalmazás esetében, amely az SQL felügyelt példányát használja az Azure-ban.

A következő okok miatt érdemes lehet [manuális feladatátvételt](../database/high-availability-sla.md#testing-application-fault-resiliency) végrehajtani a FELÜGYELt SQL-példányon:
- Alkalmazás tesztelése feladatátvételi rugalmassághoz az éles környezetbe való üzembe helyezés előtt
- Végpontok közötti rendszerek tesztelése az automatikus feladatátvétel hibatűrése érdekében
- Annak tesztelése, hogyan befolyásolják a feladatátvétel a meglévő adatbázis-munkameneteket
- Annak ellenőrzése, hogy a feladatátvétel a hálózati késés változásai miatt megváltoztatja-e a végpontok közötti teljesítményt
- A lekérdezési teljesítmény romlása bizonyos esetekben a manuális feladatátvétel segít enyhíteni a teljesítménnyel kapcsolatos problémát.

> [!NOTE]
> Annak biztosítása, hogy az alkalmazások az éles környezetben történő üzembe helyezés előtt rugalmasak legyenek, segít csökkenteni az alkalmazás-meghibásodások kockázatát az éles környezetben, és az ügyfelek számára elérhetővé teszi az alkalmazások rendelkezésre állását.

## <a name="initiate-manual-failover-on-sql-managed-instance"></a>Manuális feladatátvétel kezdeményezése az SQL felügyelt példányán

### <a name="using-powershell"></a>A PowerShell használata

Az az. SQL minimális verziójának [v 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0)kell lennie. Vegye fontolóra [Azure Cloud Shell](../../cloud-shell/overview.md) használatát a Azure Portal, amely mindig elérhető a legújabb PowerShell-verzióval. 

Előfeltételként használja az alábbi PowerShell-szkriptet a szükséges Azure-modulok telepítéséhez. Továbbá válassza ki azt az előfizetést, amelyben a feladatátvételhez használni kívánt felügyelt példány található.

```powershell
$subscription = 'enter your subscription ID here'
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subscription
```

Használja az alábbi PowerShell-parancsot: [AzSqlInstanceFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlinstancefailover) a következő példával kezdeményezheti az elsődleges csomópont feladatátvételét, amely a BC és a GP szolgáltatási szintjére is érvényes.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName
```

A következő PS parancs használatával hajtson végre feladatátvételi olvasási másodlagos csomópontot, amely csak a BC szolgáltatási szintjére vonatkozik.

```powershell
$ResourceGroup = 'enter resource group of your MI'
$ManagedInstanceName = 'enter MI name'
Invoke-AzSqlInstanceFailover -ResourceGroupName $ResourceGroup -Name $ManagedInstanceName -ReadableSecondary
```

### <a name="using-cli"></a>A parancssori felület használata

Győződjön meg arról, hogy telepítve vannak a legújabb CLI-parancsfájlok.

Használja az az SQL mi feladatátvétel CLI parancsot az alábbi példával az elsődleges csomópont feladatátvételének elindításához, amely a BC és a GP szolgáltatási szintjére is érvényes.

```cli
az sql mi failover -g myresourcegroup -n myinstancename
```

A következő CLI-parancs használatával hajtson végre feladatátvételi olvasási másodlagos csomópontot, amely csak a BC szolgáltatási szintjére vonatkozik.

```cli
az sql mi failover -g myresourcegroup -n myinstancename --replica-type ReadableSecondary
```

### <a name="using-rest-api"></a>A REST API használata

A folyamatos tesztelési folyamat vagy az automatizált teljesítmény-elhárító használatával olyan speciális felhasználók számára, akik az SQL-felügyelt példányaik feladatátvételét szeretnék automatizálni, egy API-hívással végezheti el a feladatátvétel kezdeményezését. Lásd: [felügyelt példányok – feladatátvételi REST API](https://docs.microsoft.com/rest/api/sql/managed%20instances%20-%20failover/failover) a részletekért.

Ha REST API hívással szeretné elindítani a feladatátvételt, először az Ön által választott API-ügyféllel kell előállítania az Auth-tokent. A generált hitelesítési token az API-kérelem fejlécében található engedélyezési tulajdonságként van használatban, és kötelező megadni.

A következő kód a meghívni kívánt API URI-t szemlélteti:

```HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Sql/managedInstances/{managedInstanceName}/failover?api-version=2019-06-01-preview
```

A következő tulajdonságokat kell átadni az API-hívásban:

| **API-tulajdonság** | **Paraméter** |
| --- | --- |
| subscriptionId | Az előfizetés azonosítója, amelyre a felügyelt példány telepítve van |
| resourceGroupName | Felügyelt példányt tartalmazó erőforráscsoport |
| managedInstanceName | Felügyelt példány neve |
| replicaType | Választható (Elsődleges vagy ReadableSecondary). Ezek a paraméterek a feladatátvételi replika típusát jelölik: elsődleges vagy olvasható másodlagos. Ha nincs megadva, a rendszer alapértelmezés szerint az elsődleges replikán kezdeményezi a feladatátvételt. |
| api-verzió | A statikus értéknek és a jelenleginek "2019-06-01-Preview" értékűnek kell lennie |

Az API-válasz az alábbi kettő egyike lesz:

- 202 elfogadva
- Az 400-es kérelem hibáinak egyike.

A műveleti állapot nyomon követhető az API-válaszok áttekintésével a válasz fejlécekben. További információ: [aszinkron Azure-műveletek állapota](../../azure-resource-manager/management/async-operations.md).

## <a name="monitor-the-failover"></a>A feladatátvétel figyelése

A felhasználó által kezdeményezett manuális feladatátvétel előrehaladásának figyeléséhez futtassa az alábbi T-SQL-lekérdezést a kedvenc ügyfelén (például SSMS) az SQL felügyelt példányán. A rendszer beolvassa a rendszernézet sys. dm_hadr_fabric_replica_states és a példányon elérhető jelentések replikáit. A manuális feladatátvétel megkezdése után frissítse ugyanazt a lekérdezést.

```T-SQL
SELECT DISTINCT replication_endpoint_url, fabric_replica_role_desc FROM sys.dm_hadr_fabric_replica_states
```

A feladatátvétel megkezdése előtt a kimenet az alAlwaysOnon rendelkezésre állási csoport egy elsődleges és három formátumú másodlagos zónák tartalmazó jelenlegi elsődleges replikáját fogja jelezni a BC szolgáltatási szinten. A feladatátvétel végrehajtásakor a lekérdezés ismételt futtatásához meg kell adni az elsődleges csomópont változását.

Nem fogja tudni ugyanazt a kimenetet látni a GP szolgáltatási szintjével, mint a BC esetében. Ennek az az oka, hogy a GP szolgáltatási réteg csak egy csomóponton alapul. A GP szolgáltatási réteg T-SQL-lekérdezési kimenete csak egyetlen csomópontot fog megjeleníteni a feladatátvétel előtt és után. Az ügyfél kapcsolatának elvesztése a feladatátvétel során, amely általában egy perc alatt hosszan tart, a feladatátvétel végrehajtásának jelzése lesz.

> [!IMPORTANT]
> A felhasználó által kezdeményezett manuális feladatátvétel működési korlátai a következők:
> - Lehet, hogy egy (1) feladatátvétel a felügyelt példányon 30 percenként lett kezdeményezve.
> - BC-példányok esetében léteznie kell a feladatátvételi kérelem elfogadásához szükséges replikák Kvórumának.
> - BC-példányok esetében nem lehet megadni, hogy melyik olvasható másodlagos replika kezdeményezi a feladatátvételt.

## <a name="next-steps"></a>Következő lépések

- További információ a felügyelt példány magas rendelkezésre állásáról [Az Azure SQL felügyelt példányain](../database/high-availability-sla.md).
- Az áttekintést lásd: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
