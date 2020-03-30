---
title: Meglévő virtuális hálózat konfigurálása felügyelt példányhoz
description: Ez a cikk ismerteti, hogyan konfigurálhat egy meglévő virtuális hálózat és alhálózat, ahol az Azure SQL Database felügyelt példány telepítheti.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 50b832baa9253f47b5f10980ae1764c9425ed4d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476949"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Meglévő virtuális hálózat konfigurálása felügyelt Azure SQL Database-példányhoz

Az Azure SQL Database felügyelt példánya kell telepíteni egy Azure [virtuális hálózaton,](../virtual-network/virtual-networks-overview.md) és az alhálózat dedikált felügyelt példányok csak. A meglévő virtuális hálózatot és alhálózatot akkor használhatja, ha az a [felügyelt példány virtuális hálózati követelményeinek megfelelően](sql-database-managed-instance-connectivity-architecture.md#network-requirements)van konfigurálva.

Ha az alábbi esetek valamelyike vonatkozik Önre, a jelen cikkben ismertetett parancsfájl segítségével érvényesítheti és módosíthatja a hálózatot:

- Van egy új alhálózat, amely még mindig nincs konfigurálva.
- Nem biztos benne, hogy az alhálózat megfelel a [követelményeknek.](sql-database-managed-instance-connectivity-architecture.md#network-requirements)
- A módosítások után ellenőrizni szeretné, hogy az alhálózat továbbra is megfelel-e a [hálózati követelményeknek.](sql-database-managed-instance-connectivity-architecture.md#network-requirements)

> [!Note]
> Felügyelt példányt csak az Azure Resource Manager telepítési modellen keresztül létrehozott virtuális hálózatokban hozhat létre. A klasszikus üzembe helyezési modellen keresztül létrehozott Azure virtuális hálózatok nem támogatottak. Az alhálózat méretét a [Felügyelt példányok alhálózata méretének meghatározása](sql-database-managed-instance-determine-size-vnet-subnet.md) című cikkben található irányelvek követve számítsa ki. Az alhálózat nem méretezhető át, miután telepítette a belső erőforrásokat.
>
> Felügyelt példány létrehozása után a felügyelt példány vagy virtuális hálózat áthelyezése egy másik erőforráscsoportba vagy előfizetésbe nem támogatott.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Meglévő virtuális hálózat ellenőrzése és módosítása

Ha egy meglévő alhálózaton belül szeretne felügyelt példányt létrehozni, javasoljuk a következő PowerShell-parancsfájlt az alhálózat előkészítéséhez:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

A parancsfájl három lépésben készíti elő az alhálózatot:

1. Ellenőrzés: Ellenőrzi a kiválasztott virtuális hálózatot és alhálózatot a felügyelt példány hálózati követelményeihez.
2. Megerősítés: A felhasználó nak egy olyan módosítást jelenít meg, amelyet el kell készíteni e-jével a felügyelt példány központi telepítéséhez. A beleegyezést is kéri.
3. Előkészítés: Megfelelően konfigurálja a virtuális hálózatot és az alhálózatot.

## <a name="next-steps"></a>További lépések

- Az áttekintést a [Mi a felügyelt példány?](sql-database-managed-instance.md)
- A virtuális hálózat létrehozásáról, felügyelt példány létrehozásáról és az adatbázis adatbázis-biztonsági mentésből történő visszaállításáról az [Azure SQL Database Managed Instance létrehozása című](sql-database-managed-instance-get-started.md)témakörben található.
- A DNS-sel kapcsolatos problémákról az [Egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md)című témakörben van.
