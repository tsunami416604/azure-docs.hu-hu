---
title: Azure DDoS Protection-csomag létrehozása és konfigurálása Azure PowerShell használatával
description: Megtudhatja, hogyan hozhat létre DDoS Protection-csomagot a Azure PowerShell használatával
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 49fa0c849a6b2eab0647922a711c50b4fe762584
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095662"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>Rövid útmutató: Azure DDoS Protection standard létrehozása és konfigurálása Azure PowerShell használatával

A Azure DDoS Protection standard használatának első lépései Azure PowerShell használatával. 

A DDoS Protection-csomag olyan virtuális hálózatokat határoz meg, amelyeken engedélyezve van a DDoS Protection standard, az előfizetések között. Beállíthat egy DDoS Protection-tervet a szervezet számára, és a virtuális hálózatokat több előfizetésből ugyanahhoz a csomaghoz kapcsolhatja. 

Ebben a rövid útmutatóban létrehoz egy DDoS Protection-tervet, és összekapcsolja azt egy virtuális hálózattal. 

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Helyileg telepített Azure PowerShell vagy Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>DDoS Protection terv létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Erőforráscsoport létrehozásához használja a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Ebben a példában az erőforráscsoport _MyResourceGroup_ nevet fogjuk használni, és az _USA keleti_ régióját használjuk:

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

Most hozzon létre egy _MyDdosProtectionPlan_ nevű DDoS Protection-csomagot:

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>A DDoS engedélyezése virtuális hálózathoz

### <a name="enable-ddos-for-a-new-virtual-network"></a>A DDoS engedélyezése új virtuális hálózathoz

A DDoS Protection a virtuális hálózatok létrehozásakor engedélyezhető. Ebben a példában a virtuális hálózati _MyVnet_ fogjuk elnevezni: 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>A DDoS engedélyezése meglévő virtuális hálózathoz

A DDoS elleni védelmi terv létrehozásakor hozzárendelhet egy meglévő virtuális hálózatot:

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>Ellenőrzés és tesztelés

Először olvassa el a DDoS Protection-terv részleteit:

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

Ellenőrizze, hogy a parancs visszaadja-e a DDoS Protection-terv helyes részleteit.

## <a name="clean-up-resources"></a>Az erőforrások felszabadítása

A következő oktatóanyagban megtarthatja az erőforrásait. Ha már nincs rá szükség, törölje a _MyResourceGroup_ erőforráscsoportot. Az erőforráscsoport törlésekor a DDoS elleni védelmi tervet és az ahhoz kapcsolódó összes erőforrást is törli. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

A DDoS-védelem letiltása virtuális hálózat esetén: 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

Ha törölni szeretné a DDoS Protection-csomagot, először el kell távolítania az összes virtuális hálózatot.

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan tekintheti meg és konfigurálhatja a DDoS elleni védelmi terv telemetria, folytassa az oktatóanyagokkal.

> [!div class="nextstepaction"]
> [DDoS Protection-telemetria megtekintése és konfigurálása](telemetry-monitoring-alerting.md)