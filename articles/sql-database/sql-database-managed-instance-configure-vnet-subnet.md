---
title: Az Azure SQL Database felügyelt példányain meglévő virtuális hálózat/alhálózat konfigurálása |} A Microsoft Docs
description: Ez a témakör ismerteti, hogyan lehet meglévő virtuális hálózaton (VNet) konfigurálása és alhálózatot, ahol telepítheti az Azure SQL Database felügyelt példányain.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 1718177a0902bc7049eb6986e5a1d128eeb3f233
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040958"
---
# <a name="configure-an-existing-vnet-for-azure-sql-database-managed-instance"></a>Egy meglévő virtuális hálózat konfigurálása az Azure SQL Database felügyelt példánya

Az Azure SQL Database felügyelt példányain telepíteni kell egy Azure-ban [virtuális hálózat (VNet)](../virtual-network/virtual-networks-overview.md) és az alhálózat csak dedikált felügyelt példányok. A meglévő virtuális hálózatot és alhálózatot is használhat, ha a következők szerint van konfigurálva a [felügyelt példány a virtuális hálózat követelményei](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

Egy új alhálózatot, amely még nincs konfigurálva van, ha nem biztos a alhálózat teljesíti a [követelmények](sql-database-managed-instance-connectivity-architecture.md#network-requirements), vagy ellenőrizni kívánt van az alhálózat továbbra is megfeleljenek a [hálózati követelmények](sql-database-managed-instance-connectivity-architecture.md#network-requirements) után néhány módosítást végzett, akkor is érvényesíthetők és módosíthatók a hálózat, az ebben a szakaszban ismertetett parancsfájl használatával.

  > [!Note]
  > Felügyelt példány csak a Resource Manager virtuális hálózatot hozhat létre. Klasszikus üzemi modell használatával üzembe helyezett Azure virtuális hálózatok nem lesznek suported. Győződjön meg arról, hogy az alábbi az irányelveket, az alhálózat méretét kiszámítása a [alhálózat méretét határozza meg a felügyelt példányok](sql-database-managed-instance-determine-size-vnet-subnet.md) cikkre, mert az alhálózat nem méretezhetők át belül az erőforrásokat üzembe helyez.

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

Alhálózat előkészítési három egyszerű lépésben történik:

1. Ellenőrzése – a kiválasztott virtuális hálózat és alhálózat érvényesíti a felügyelt példány hálózati követelményeiben.
2. Megerősítése – felhasználó látható, amely kell végzett készíti elő az alhálózat a felügyelt példány üzembe helyezési és beleegyezését kéri módosításainak egy halmazát.
3. Készítse elő – a virtuális hálózatot és alhálózatot megfelelően van beállítva.

## <a name="next-steps"></a>További lépések

- Áttekintéséhez lásd: [mit jelent a felügyelt példány](sql-database-managed-instance.md)
- Hozzon létre egy Vnetet, létrehoz egy felügyelt példányt, és egy adatbázist egy adatbázis biztonsági másolatból történő bemutató oktatóanyagot, tekintse meg a [létrehozása egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-get-started.md).
- DNS-problémák esetén lásd: [egy egyéni DNS konfigurálása](sql-database-managed-instance-custom-dns.md).
