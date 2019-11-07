---
title: Nyilvános végpont konfigurálása – felügyelt példány Azure SQL Database
description: Megtudhatja, hogyan konfigurálhat nyilvános végpontot felügyelt példányhoz
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 6f953e4c549619a30564bdb061e98761474174c3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687959"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Nyilvános végpont konfigurálása Azure SQL Database felügyelt példányban

A [felügyelt példányok](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) nyilvános végpontja lehetővé teszi a felügyelt példányhoz való adathozzáférést a [virtuális hálózaton](../virtual-network/virtual-networks-overview.md)kívülről. Felügyelt példányát a több-bérlős Azure-szolgáltatásokból (például Power BI, Azure App Service vagy helyszíni hálózatból) érheti el. A felügyelt példányok nyilvános végpontjának használatával nem szükséges VPN-t használnia, ami segít elkerülni a VPN átviteli sebességét.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

> [!div class="checklist"]
> - Nyilvános végpont engedélyezése a felügyelt példányhoz a Azure Portal
> - Nyilvános végpont engedélyezése a felügyelt példány számára a PowerShell használatával
> - Felügyelt példány hálózati biztonsági csoportjának konfigurálása a felügyelt példány nyilvános végpontjának forgalmának engedélyezéséhez
> - Felügyelt példány nyilvános végpontjának összekapcsolási karakterláncának beszerzése

## <a name="permissions"></a>Engedélyek

A felügyelt példányban lévő adatérzékenység miatt a felügyelt példány nyilvános végpontjának engedélyezéséhez kétlépéses folyamat szükséges. Ez a biztonsági mérték a feladatok elkülönítését (SoD) követi:

- A felügyelt példányok nyilvános végpontjának engedélyezését a felügyelt példány rendszergazdájának kell elvégeznie. A felügyelt példány rendszergazdája a felügyelt SQL-példány erőforrásának **Áttekintés** lapján található.
- Olyan hálózati biztonsági csoporttal történő adatforgalom engedélyezése, amelyet egy hálózati rendszergazdának kell elvégeznie. További információ: [hálózati biztonsági csoport engedélyei](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Felügyelt példányok nyilvános végpontjának engedélyezése a Azure Portalban

1. A Azure Portal elindítása <https://portal.azure.com/.>
1. Nyissa meg az erőforráscsoportot a felügyelt példánnyal, és válassza ki azt az **SQL felügyelt példányt** , amelyen a nyilvános végpontot konfigurálni kívánja.
1. A **biztonsági** beállítások lapon válassza a **virtuális hálózat** lapot.
1. A virtuális hálózat konfigurációja lapon válassza az **Engedélyezés** , majd a **Mentés** ikont a konfiguráció frissítéséhez.

![Mi-vnet-config. png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Felügyelt példány nyilvános végpontjának engedélyezése a PowerShell használatával

### <a name="enable-public-endpoint"></a>Nyilvános végpont engedélyezése

Futtassa a következő PowerShell-parancsokat. Cserélje le az **előfizetés-azonosítót** az előfizetés-azonosítójával. Továbbá cserélje le a **RG nevet** a felügyelt példányhoz tartozó erőforráscsoporthoz, és cserélje le a **mi-Name nevet** a felügyelt példány nevére.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Nyilvános végpont letiltása

Ha a nyilvános végpontot a PowerShell használatával szeretné letiltani, hajtsa végre a következő parancsot (és ne feledkezzen meg a 3342 bejövő port NSG bezárásáról is, ha konfigurálva van):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Nyilvános végponti forgalom engedélyezése a hálózati biztonsági csoportban

1. Ha a felügyelt példány konfigurációs lapja továbbra is nyitva van, lépjen az **Áttekintés** lapra. Máskülönben lépjen vissza az **SQL felügyelt példány** -erőforráshoz. Válassza ki a **virtuális hálózat/alhálózat** hivatkozást, amely a virtuális hálózat konfigurációja lapra kerül.

    ![Mi-Overview. png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Válassza az **alhálózatok** fület a virtuális hálózat bal oldali konfigurációs paneljén, és jegyezze fel a felügyelt példány **biztonsági csoportját** .

    ![Mi-vnet-subnet. png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Lépjen vissza a felügyelt példányt tartalmazó erőforráscsoporthoz. Ekkor meg kell jelennie a **hálózati biztonsági csoport** fent említett nevének. Válassza ki a hálózati biztonsági csoport konfigurációs lapjára felvenni kívánt nevet.

1. Válassza a **bejövő biztonsági szabályok** fület, és **adjon hozzá** egy olyan szabályt, amely magasabb prioritással rendelkezik, mint a **deny_all_inbound** szabály a következő beállításokkal: </br> </br>

    |Beállítás  |Ajánlott érték  |Leírás  |
    |---------|---------|---------|
    |**Forrás**     |Bármely IP-cím vagy szolgáltatás címkéje         |<ul><li>Az Azure-szolgáltatások, például a Power BI esetében válassza az Azure Cloud Service címkét</li> <li>A számítógép vagy az Azure-beli virtuális gép esetében használja a NAT IP-címet</li></ul> |
    |**Forrásport-tartományok**     |*         |Hagyja ezt a lehetőséget * (bármely), mivel a forrás portjai általában dinamikusan vannak lefoglalva, és mint ilyen, kiszámíthatatlan |
    |**Cél**     |Bármelyik         |A célhely kihagyása a felügyelt példány alhálózatára való adatforgalom engedélyezéséhez |
    |**Célport tartományai**     |3342         |Hatóköri célport a 3342-re, amely a felügyelt példány nyilvános TDS-végpontja |
    |**Protocol (Protokoll)**     |TCP         |A felügyelt példány a TDS protokollhoz TCP protokollt használ. |
    |**Művelet**     |Engedélyezés         |Felügyelt példány bejövő forgalmának engedélyezése a nyilvános végponton keresztül |
    |**Priority (Prioritás)**     |1300         |Győződjön meg arról, hogy ez a szabály magasabb prioritású, mint a **deny_all_inbound** szabály |

    ![Mi-NSG-rules. png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Az 3342-es port a felügyelt példányhoz tartozó nyilvános végponti kapcsolatokhoz használatos, és ezen a ponton nem módosítható.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>A felügyelt példány nyilvános végpontjának összekapcsolási karakterláncának beszerzése

1. Navigáljon az SQL felügyelt példányának konfiguráció lapjára, amelyen engedélyezve van a nyilvános végpont. Válassza a **kapcsolatok karakterláncok** lapot a **Beállítások** konfiguráció alatt.
1. Vegye figyelembe, hogy a nyilvános végpont állomásneve < mi_name > formátumban jön. **nyilvános**. < dns_zone >. database. Windows. net és a csatlakozáshoz használt port 3342.

    ![Mi-Public-Endpoint-Conn-string. png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>További lépések

- További információ a [Azure SQL Database felügyelt példány biztonságos használatáról nyilvános végponttal](sql-database-managed-instance-public-endpoint-securely.md).