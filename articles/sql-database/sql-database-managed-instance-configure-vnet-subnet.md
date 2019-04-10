---
title: Azure SQL Database felügyelt példánya a meglévő virtuális hálózat konfigurálása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálása egy meglévő virtuális hálózatot és alhálózatot, ahol telepítheti az Azure SQL Database felügyelt példányain.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: c4ff12f0c9adcb9943a6e2426eaf2740ba171e39
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358846"
---
# <a name="configure-an-existing-virtual-network-for-azure-sql-database-managed-instance"></a>Azure SQL Database felügyelt példánya a meglévő virtuális hálózat konfigurálása

Az Azure SQL Database felügyelt példányain telepíteni kell egy Azure-ban [virtuális hálózat](../virtual-network/virtual-networks-overview.md) és az alhálózat csak dedikált felügyelt példányok. Használhatja a meglévő virtuális hálózatot és alhálózatot, ha a következők szerint van konfigurálva a [felügyelt példány virtuális hálózati követelmények](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Ha valamelyik a következő esetekben érvényes, ellenőrizze, és a hálózat módosítása a leírt parancsfájl használatával:

- Egy új alhálózatot, amely még nincs konfigurálva van.
- Nem biztos, hogy az alhálózat teljesíti a [követelmények](sql-database-managed-instance-connectivity-architecture.md#network-requirements).
- Ellenőrizze, hogy az alhálózat továbbra is megfelel az szeretné a [hálózati követelmények](sql-database-managed-instance-connectivity-architecture.md#network-requirements) után végzett módosításokat.

> [!Note]
> Felügyelt példány csak az Azure Resource Manager-alapú üzemi modellel létrehozott virtuális hálózatokat hozhat létre. A klasszikus üzemi modellel létrehozott Azure virtuális hálózatok nem támogatottak. Az alábbi az irányelveket, az alhálózat méretének kiszámítása a [alhálózat méretét határozza meg a felügyelt példányok](sql-database-managed-instance-determine-size-vnet-subnet.md) cikk. Az alhálózat nem méretezhetők át, a belső erőforrások üzembe helyezése után.

## <a name="validate-and-modify-an-existing-virtual-network"></a>Érvényesíthetők és módosíthatók a meglévő virtuális hálózaton

Ha szeretne létrehozni egy meglévő alhálózaton belül felügyelt példányra, javasoljuk, hogy az alhálózat előkészítéséhez a következő PowerShell-parancsfájlt:

```powershell
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/prepare-subnet'

$parameters = @{
    subscriptionId = '<subscriptionId>'
    resourceGroupName = '<resourceGroupName>'
    virtualNetworkName = '<virtualNetworkName>'
    subnetName = '<subnetName>'
    }

Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/prepareSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

A parancsfájl előkészíti az alhálózat három lépésben:

1. Ellenőrzése: Azt ellenőrzi, a kiválasztott virtuális hálózat és alhálózat a felügyelt példány hálózati követelményei.
2. Erősítse meg: A felhasználó látható, amely szükség a felügyelt példány telepítésének előkészítéséhez az alhálózat módosításainak egy halmazát. Azt is beleegyezését kéri.
3. Készítse elő: Konfigurálja megfelelően a virtuális hálózatot és alhálózatot.

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány?](sql-database-managed-instance.md).
- Ez az oktatóanyag bemutatja, hogyan hozzon létre egy virtuális hálózatot, hozzon létre egy felügyelt példányt, és egy adatbázist egy adatbázis biztonsági másolatból, lásd: [hozzon létre egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén lásd: [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md).
