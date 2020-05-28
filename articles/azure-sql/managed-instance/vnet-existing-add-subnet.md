---
title: Meglévő virtuális hálózat konfigurálása
titleSuffix: Azure SQL Managed Instance
description: Ez a cikk azt ismerteti, hogyan lehet konfigurálni egy meglévő virtuális hálózatot és alhálózatot, ahol telepítheti az Azure SQL felügyelt példányát.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: e41a91320ed3226cb211de5ba3f6f0eb17da555f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044303"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-managed-instance"></a>Meglévő virtuális hálózat konfigurálása az Azure SQL felügyelt példányaihoz
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányát egy Azure-beli [virtuális hálózaton](../../virtual-network/virtual-networks-overview.md) belül kell telepíteni, és csak az SQL felügyelt példányaihoz dedikált alhálózatot kell üzembe helyezni. Ha az [SQL felügyelt példány virtuális hálózati követelményeinek](connectivity-architecture-overview.md#network-requirements)megfelelően van konfigurálva, használhatja a meglévő virtuális hálózatot és alhálózatot.

Ha a következő esetek valamelyike vonatkozik Önre, a jelen cikkben ismertetett parancsfájllal ellenőrizheti és módosíthatja a hálózatot:

- Olyan új alhálózattal rendelkezik, amely még nincs konfigurálva.
- Nem biztos benne, hogy az alhálózat igazodik a [követelményekhez](connectivity-architecture-overview.md#network-requirements).
- Azt szeretné, hogy az alhálózat továbbra is megfeleljen a [hálózati követelményeknek](connectivity-architecture-overview.md#network-requirements) , miután végzett a módosításokat.

> [!Note]
> SQL felügyelt példányt csak a Azure Resource Manager üzemi modellel létrehozott virtuális hálózatokban lehet létrehozni. A klasszikus üzemi modellel létrehozott Azure-beli virtuális hálózatok nem támogatottak. Az alhálózat méretének kiszámítása a [felügyelt SQL-példányok alhálózat méretének meghatározása](vnet-subnet-determine-size.md) című cikkben található irányelvek alapján. Az alhálózatot nem lehet átméretezni az erőforrások belső telepítése után.
>
> Az SQL felügyelt példányának létrehozása után a példány vagy a VNet másik erőforráscsoporthoz vagy előfizetésbe való áthelyezése nem támogatott.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Meglévő virtuális hálózat ellenőrzése és módosítása

Ha egy meglévő alhálózaton belül szeretne SQL felügyelt példányt létrehozni, a következő PowerShell-parancsfájlt javasoljuk az alhálózat előkészítéséhez:

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

A szkript három lépésben készíti elő az alhálózatot:

1. Ellenőrzés: ellenőrzi a kiválasztott virtuális hálózatot és alhálózatot az SQL felügyelt példányok hálózati követelményeihez.
2. Confirm (megerősítés): megjeleníti a felhasználó által az SQL felügyelt példány központi telepítésének előkészítéséhez szükséges módosításokat. Emellett beleegyezik.
3. Előkészítés: helyesen konfigurálja a virtuális hálózatot és az alhálózatot.

## <a name="next-steps"></a>További lépések

- Az áttekintést lásd: [Mi az SQL felügyelt példány?](sql-managed-instance-paas-overview.md).
- A virtuális hálózatok létrehozásával, a felügyelt SQL-példányok létrehozásával és az adatbázis biztonsági másolatból való visszaállításával kapcsolatos oktatóanyagért tekintse meg [Az Azure SQL felügyelt példányának létrehozása](instance-create-quickstart.md)című témakört.
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](custom-dns-configure.md)című témakört.
