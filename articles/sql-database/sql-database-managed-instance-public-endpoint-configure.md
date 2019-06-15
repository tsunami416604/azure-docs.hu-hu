---
title: Konfigurálja a nyilvános végpont – Azure SQL Database felügyelt példány |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a felügyelt példány egy nyilvános végpontot
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: d3e68a5287e59c576f85491e6e5eba33fac080ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65465193"
---
# <a name="configure-public-endpoint-in-azure-sql-database-managed-instance"></a>Konfigurálja a nyilvános végpontot az Azure SQL Database felügyelt példány

Nyilvános végpontot egy [felügyelt példány](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) lehetővé teszi az adatokhoz való hozzáférést a felügyelt példány kívül a [virtuális hálózat](../virtual-network/virtual-networks-overview.md). Ön a több-bérlős Azure-szolgáltatásokhoz hasonlóan a Power bi-ban, az Azure App Service-ben vagy egy helyszíni hálózat, a felügyelt példány elérhetik. Felügyelt példány használatával a nyilvános végponthoz, nem kell egy VPN-, amely megkönnyíti a VPN átviteli problémák elkerülése érdekében használjon.

Ebből a cikkből megtudhatja, hogyan lehet:

> [!div class="checklist"]
> - A felügyelt példány az Azure Portalon nyilvános végpont engedélyezése
> - A PowerShell használatával a felügyelt példány nyilvános végpont engedélyezése
> - A felügyelt példány nyilvános végpont forgalom engedélyezésére a felügyelt példány hálózati biztonsági csoport konfigurálása
> - A felügyelt példány nyilvános végpontot kapcsolati karakterlánc beszerzése

## <a name="permissions"></a>Engedélyek

Felügyelt példány az adatok érzékenysége, mert engedélyezése a felügyelt példány nyilvános végpontot a konfiguráció szükséges egy kétlépéses folyamat. Ez a biztonsági intézkedés betartja feladatkörök (gyeptégla):

- Engedélyezése a felügyelt példány nyilvános végpontot kell tenni a felügyelt példány rendszergazda A felügyelt példány rendszergazdája találhatók **áttekintése** lapján az SQL felügyelt példány erőforrás.
- Lehetővé teszi a forgalom, amely a hálózati rendszergazda kell elvégezni a hálózati biztonsági csoport használatával További információkért lásd: [hálózati biztonságicsoport-engedélyeit](../virtual-network/manage-network-security-group.md#permissions).

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>A felügyelt példányhoz az Azure Portalon nyilvános végpont engedélyezése

1. Indítsa el az Azure Portalra <https://portal.azure.com/.>
1. Nyissa meg az erőforráscsoportot a következő felügyelt példányt, majd válassza ki a **SQL felügyelt példánya** , hogy szeretné-e a nyilvános végponthoz konfigurálása.
1. Az a **biztonsági** beállításaiban, válassza a **virtuális hálózati** fülre.
1. Válassza ki a virtuális hálózat konfigurációs lapja **engedélyezése** , majd a **mentése** ikonra kattintva frissítse a konfigurációt.

![mi-vnet-config.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>A felügyelt példányhoz PowerShell-lel nyilvános végpont engedélyezése

### <a name="enable-public-endpoint"></a>Nyilvános végpont engedélyezése

Futtassa a következő PowerShell-parancsokat. Cserélje le **előfizetés-azonosító** az előfizetés-azonosítóval. Továbbá cserélje le **rg-name** az erőforráscsoport, a felügyelt példány, és cserélje le a **mi-name** a felügyelt példány nevével.

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

### <a name="disable-public-endpoint"></a>Tiltsa le a nyilvános végponthoz

Tiltsa le a nyilvános végponthoz, PowerShell-lel, akkor hajtaná végre a következő parancsot (és is ne felejtse el gombra kattintva zárja be az NSG-t a bejövő port 3342, ha nincs konfigurálva):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Engedélyezze a nyilvános végponthoz forgalmat a hálózati biztonsági csoport

1. Ha a konfigurációs lapon a felügyelt példány még mindig nyitva van, lépjen a **áttekintése** fülre. Ellenkező esetben lépjen vissza a **SQL felügyelt példánya** erőforrás. Válassza ki a **virtuális hálózat/alhálózat** hivatkozást, amellyel a virtuális hálózati konfiguráció lapon.

    ![mi-overview.png](media/sql-database-managed-instance-public-endpoint-configure/mi-overview.png)

1. Válassza ki a **alhálózatok** a bal oldali konfigurációs panelen, a virtuális hálózat lapján, és jegyezze fel a **biztonsági csoport** a felügyelt példány.

    ![mi-vnet-subnet.png](media/sql-database-managed-instance-public-endpoint-configure/mi-vnet-subnet.png)

1. Lépjen vissza az erőforráscsoportot, amely tartalmazza a felügyelt példány. Megtekintheti a **hálózati biztonsági csoport** fentebb feltüntetett nevét. Válassza ki a nevét, a hálózati biztonsági csoport konfigurációs lap megnyitásához.

1. Válassza ki a **bejövő biztonsági szabályok** fülre, és **Hozzáadás** egy szabályt, amely magasabb prioritású, mint a **deny_all_inbound** szabály a következő beállításokkal: </br> </br>

    |Beállítás  |Ajánlott érték  |Leírás  |
    |---------|---------|---------|
    |**Forrás**     |Bármely IP-cím vagy -szolgáltatáscímke         |<ul><li>Az Azure-szolgáltatásokhoz hasonlóan a Power bi-ban válassza ki az Azure Cloud Service címke</li> <li>A számítógép vagy Azure virtuális Gépen használjon NAT IP-cím</li></ul> |
    |**Forrásporttartományok**     |*         |Hagyja üresen ezt a * (minden), a forrásportok rendszerint dinamikusan kiosztott és mint ilyen, előre nem látható |
    |**cél**     |Bármely         |Célállomása minden forgalom engedélyezésére a felügyelt példány alhálózatán történő elhagyása |
    |**Célporttartományok**     |3342         |Hatókör célport való 3342, amely a felügyelt példány nyilvános TDS-végpontja |
    |**Protocol (Protokoll)**     |TCP         |Felügyelt példány használja a TCP protokoll TDS |
    |**Művelet**     |Engedélyezés         |Felügyelt példány keresztül a nyilvános végponthoz beérkező forgalom engedélyezésére |
    |**prioritás**     |1300         |Győződjön meg arról, hogy ez a szabály, a magasabb prioritású a **deny_all_inbound** szabály |

    ![mi-nsg-rules.png](media/sql-database-managed-instance-public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > 3342 portot használja nyilvános végponthoz kapcsolatokat a felügyelt példány, és nem módosítható ezen a ponton.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>A felügyelt példány nyilvános végpontot kapcsolati karakterlánc beszerzése

1. Lépjen az SQL felügyelt példány konfigurációs oldalára, amely a nyilvános végpont engedélyezve van. Válassza ki a **kapcsolati karakterláncok** lapjára az **beállítások** konfigurációja.
1. Vegye figyelembe, hogy a nyilvános végpont gazdagépnevére is tartalmaz, a következő formátumban: < mi_name >. **nyilvános**. < dns_zone >. database.windows.net, hogy a port pedig a kapcsolathoz használt 3342.

    ![mi-public-endpoint-conn-string.png](media/sql-database-managed-instance-public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>További lépések

- Ismerje meg [Azure SQL Database felügyelt példány nyilvános végponttal rendelkező biztonságosan](sql-database-managed-instance-public-endpoint-securely.md).