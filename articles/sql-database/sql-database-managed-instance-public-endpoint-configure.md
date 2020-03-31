---
title: Nyilvános végpont - felügyelt példány konfigurálása
description: További információ a nyilvános végpont konfigurálásáról a felügyelt példányhoz
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: 1acd7d6a3b203997e3acd8d7959b1572e09845f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256158"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Nyilvános végpont konfigurálása felügyelt Azure SQL Database-példányban

A [felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) nyilvános végpontja lehetővé teszi az adatok elérését a felügyelt példányhoz a [virtuális hálózaton](../virtual-network/virtual-networks-overview.md)kívülről. A felügyelt példány több-bérlős Azure-szolgáltatásokból, például a Power BI-ból, az Azure App Service-ből vagy egy helyszíni hálózatból érhető el. A nyilvános végpont használatával egy felügyelt példány, nem kell használni a VPN, amely segíthet elkerülni a VPN átviteli problémák.

Ebben a cikkben megtudhatja, hogyan:

> [!div class="checklist"]
> - Nyilvános végpont engedélyezése a felügyelt példányhoz az Azure Portalon
> - Nyilvános végpont engedélyezése a felügyelt példányhoz a PowerShell használatával
> - A felügyelt példány hálózati biztonsági csoportjának konfigurálása a felügyelt példány nyilvános végpontjára irányuló forgalom engedélyezéséhez
> - A felügyelt példány nyilvános végpontkapcsolati karakterláncának beszerzése

## <a name="permissions"></a>Engedélyek

A felügyelt példányban lévő adatok érzékenysége miatt a felügyelt példány nyilvános végpontjának engedélyezéséhez kétlépéses folyamatra van szükség. Ez a biztonsági intézkedés betartja a feladatok elkülönítését (SoD):

- A felügyelt példány rendszergazdájának engedélyeznie kell a nyilvános végpontot egy felügyelt példányon. A felügyelt példány rendszergazdája megtalálható az SQL felügyelt példány erőforrás **áttekintése lapján.**
- A hálózati rendszergazda által elvégzendő hálózati biztonsági csoport használatával történő forgalom engedélyezése. További információt a [hálózati biztonsági csoport engedélyei című témakörben talál.](../virtual-network/manage-network-security-group.md#permissions)

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Nyilvános végpont engedélyezése felügyelt példányhoz az Azure Portalon

1. Az Azure Portal elindítása a<https://portal.azure.com/.>
1. Nyissa meg a felügyelt példányt tartalmazó erőforráscsoportot, és válassza ki azt az **SQL felügyelt példányt,** amelyen nyilvános végpontot szeretne konfigurálni.
1. A **Biztonsági** beállítások lapon válassza a **Virtuális hálózat** lapot.
1. A Virtuális hálózat konfigurációja lapon válassza az **Engedélyezés** lehetőséget, majd a **Mentés** ikont a konfiguráció frissítéséhez.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Nyilvános végpont engedélyezése egy felügyelt példányhoz a PowerShell használatával

### <a name="enable-public-endpoint"></a>Nyilvános végpont engedélyezése

Futtassa a következő PowerShell-parancsokat. Cserélje le **az előfizetés-azonosítót** az előfizetés-azonosítóra. Az **rg-name-t** a felügyelt példány erőforráscsoportjára is cserélje le, és cserélje le a **mi-name-t** a felügyelt példány nevére.

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

A nyilvános végpont letiltásához a PowerShell használatával hajtsa végre a következő parancsot (és ne felejtse el bezárni a 3342-es bejövő port NSG-jét, ha konfigurálva van):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Nyilvános végponti forgalom engedélyezése a hálózati biztonsági csoportban

1. Ha a felügyelt példány konfigurációs lapja még nyitva van, keresse meg az **SQL managed instance** **Áttekintés** lapot. Válassza ki a **virtuális hálózat/alhálózat** kapcsolatot, amely a Virtuális hálózat konfigurációja lapra vezet.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Válassza az **Alhálózatok** lapot a virtuális hálózat bal oldali konfigurációs ablaktábláján, és jegyezze fel a felügyelt példány **biztonsági csoportját.**

    ![mi-vnet-alhálózat.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Lépjen vissza a felügyelt példányt tartalmazó erőforráscsoporthoz. A fent említett **hálózati biztonsági csoport** névnek kell megtekintenie. Válassza ki a hálózati biztonsági csoport konfigurációs lapjára való beugráshoz kívánt nevet.

1. Válassza a **Bejövő biztonsági szabályok** lapot, és adjon **hozzá** egy olyan szabályt, amelynek prioritása magasabb, mint a **deny_all_inbound** szabálynak a következő beállításokkal: </br> </br>

    |Beállítás  |Ajánlott érték  |Leírás  |
    |---------|---------|---------|
    |**Forrás**     |Bármely IP-cím vagy szolgáltatáscímke         |<ul><li>Az Azure-szolgáltatások, például a Power BI esetében válassza ki az Azure Cloud Service Címkét</li> <li>A számítógép vagy az Azure virtuális gép, nat IP-cím használata</li></ul> |
    |**Forrásporttartományok**     |*         |Hagyja ezt a * (bármely) a forrás portok általában dinamikusan leosztott, és mint ilyen, kiszámíthatatlan |
    |**Cél**     |Bármelyik         |Ha a célhelyet bármelyikként hagyja, hogy a felügyelt példány alhálózatába irányuló forgalom |
    |**Célporttartományok**     |3342         |A célport hatóköre a 3342-es szintre, amely a felügyelt példány nyilvános TDS-végpontja |
    |**Protocol (Protokoll)**     |TCP         |A felügyelt példány TCP protokollt használ a TDS-hez |
    |**Művelet**     |Engedélyezés         |Bejövő forgalom engedélyezése felügyelt példányhoz a nyilvános végponton keresztül |
    |**Prioritás**     |1300         |Győződjön meg arról, hogy ez a szabály magasabb prioritású, mint a **deny_all_inbound** szabály |

    ![mi-nsg-szabályok.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > A 3342-es port nyilvános végponti kapcsolatokhoz használatos a felügyelt példányhoz, és ezen a ponton nem módosítható.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>A felügyelt példány nyilvános végpontkapcsolati karakterláncának beszerzése

1. Keresse meg a nyilvános végponthoz engedélyezett SQL felügyelt példánykonfigurációs lapot. Válassza a **Kapcsolati karakterláncok** lapot a **Beállítások** konfiguráció alatt.
1. Vegye figyelembe, hogy a nyilvános végpont állomásneve <mi_name> formátumban érkezik. **nyilvános**.<dns_zone>.database.windows.net, és hogy a kapcsolathoz használt port 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg [az Azure SQL Database felügyelt példányának biztonságos használatát a nyilvános végponttal.](sql-database-managed-instance-public-endpoint-securely.md)