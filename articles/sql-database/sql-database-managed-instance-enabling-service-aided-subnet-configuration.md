---
title: Szolgáltatásáltal támogatott alhálózati konfiguráció engedélyezése az Azure SQL Database felügyelt példányához
description: Szolgáltatásáltal támogatott alhálózati konfiguráció engedélyezése az Azure SQL Database felügyelt példányához
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.date: 03/12/2020
ms.openlocfilehash: efc2b8578651f68d052f227694f85348853e191f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533266"
---
# <a name="enabling-service-aided-subnet-configuration-for-azure-sql-database-managed-instance"></a>Szolgáltatásáltal támogatott alhálózati konfiguráció engedélyezése az Azure SQL Database felügyelt példányához
A szolgáltatásáltal támogatott alhálózati konfiguráció automatikus hálózati konfigurációkezelést biztosít a felügyelt példányokat tároló alhálózatok számára. A szolgáltatás által támogatott alhálózati konfiguráció felhasználó marad teljes mértékben ellenőrzése az adatokhoz való hozzáférés (TDS forgalom áramlása), míg a felügyelt példány felelősséget vállal a felügyeleti forgalom folyamatos áramlásának biztosításáért az SLA teljesítése érdekében.

Az automatikusan konfigurált hálózati biztonsági csoportok és útvonaltábla-szabályok láthatók az ügyfelek számára, és a _Microsoft.Sql-managedInstances_UseOnly__- előtaggal vannak elrendelve.

A szolgáltatás által támogatott konfiguráció automatikusan engedélyezve van, ha bekapcsolja az [erőforrás-szolgáltató alhálózati delegálását.](../virtual-network/subnet-delegation-overview.md) `Microsoft.Sql/managedInstances`

> [!IMPORTANT] 
> Az alhálózati delegálás bekapcsolása után nem tudta kikapcsolni, amíg el nem távolítja az utolsó virtuális fürtöt az alhálózatról. A virtuális fürt törlésével kapcsolatos további részletekért tekintse meg a következő [cikket.](sql-database-managed-instance-delete-virtual-cluster.md#delete-virtual-cluster-from-the-azure-portal)

> [!NOTE] 
> Mivel a szolgáltatás által támogatott alhálózati konfiguráció alapvető jellemzője az SLA karbantartásának, 2020. 2020. július 1-jén a felügyelt példányokat tartalmazó összes alhálózat automatikusan delegálva lesz a felügyelt példány erőforrás-szolgáltatószámára. 

## <a name="enabling-subnet-delegation-for-new-deployments"></a>Alhálózati delegálás engedélyezése új telepítéseknél
A felügyelt példány üres alhálózatba történő `Microsoft.Sql/managedInstances` telepítéséhez delegálnia kell az erőforrás-szolgáltatónak a következő [cikkben leírtak szerint.](../virtual-network/manage-subnet-delegation.md) _Kérjük, vegye figyelembe, hogy a hivatkozott cikk például erőforrás-szolgáltatót használ. `Microsoft.DBforPostgreSQL/serversv2` Ehelyett erőforrás-szolgáltatót `Microsoft.Sql/managedInstances` kell használnia._

## <a name="enabling-subnet-delegation-for-existing-deployments"></a>Alhálózati delegálás engedélyezése meglévő telepítéseknél

Annak érdekében, hogy lehetővé tegye az alhálózati delegálást a meglévő felügyelt példány telepítéséhez, meg kell találnia a virtuális hálózati alhálózatot, ahol el helyezik. 

Ennek megismeréséhez `Virtual network/subnet` ellenőrizze `Overview` a felügyelt példány portálpaneljén.

Alternatív megoldásként futtathatja a következő PowerShell-parancsokat, hogy ezt megismerhesse. Cserélje le **az előfizetés-azonosítót** az előfizetés-azonosítóra. Az **rg-name-t** a felügyelt példány erőforráscsoportjára is cserélje le, és cserélje le a **mi-name-t** a felügyelt példány nevére.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi.SubnetId
```

Miután megtalálta a felügyelt példány alhálózatát, delegálnia kell az erőforrás-szolgáltatónak `Microsoft.Sql/managedInstances` a következő cikkben leírtak [szerint.](../virtual-network/manage-subnet-delegation.md) _Kérjük, vegye figyelembe, hogy a hivatkozott cikk például erőforrás-szolgáltatót használ. `Microsoft.DBforPostgreSQL/serversv2` Ehelyett erőforrás-szolgáltatót `Microsoft.Sql/managedInstances` kell használnia._


> [!IMPORTANT]
> A szolgáltatás által támogatott konfiguráció engedélyezése nem okoz feladatátvételt vagy megszakítást az alhálózatban már meglévő felügyelt példányok kapcsolatában.
