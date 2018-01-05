---
title: "Hozzon létre egy rendelkezésre állási zónák (előzetes verzió) használó Azure méretezési |} Microsoft Docs"
description: "Útmutató az Azure virtuális gép méretezési készlet, amely a rendelkezésre állási zónák használata elleni kimaradások nagyobb redundancia létrehozása"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: iainfou
ms.openlocfilehash: 310fdc68d3eb662906053d2bc0c45e6cfa18d4da
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>A rendelkezésre állási zónák (előzetes verzió) használó virtuálisgép-méretezési csoport létrehozása
A datacenter-szintű meghibásodásával szembeni védelemhez a virtuálisgép-méretezési csoportok, létrehozhat egy rendelkezésre állási zóna terjedő skálán. Azure-régiók, amely támogatja a rendelkezésre állási zónák legalább három különálló zónákra, mindegyiket a saját független a forrás-, hálózati és hűtési kapcsolja. További információkért lásd: [rendelkezésre állási zónák áttekintése](../availability-zones/az-overview.md).

Rendelkezésre állási zónák használatára, a méretezési létre kell hozni egy [támogatott Azure-régiót](../availability-zones/az-overview.md#regions-that-support-availability-zones). Létrehozhat egy méretezési csoport, amely a rendelkezésre állási zónák használja az alábbi módszerek egyikével:

- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Az Azure Resource Manager-sablonok](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Az Azure Portal használata
A méretezési csoport egy rendelkezésre állási zóna használó létrehozásának folyamata megegyezik a részletes a [bevezető cikkben](virtual-machine-scale-sets-create-portal.md). Amikor kiválaszt egy támogatott Azure-régió, terjedő skálán, állítsa be az egyik rendelkezésre álló zónában, a következő példában látható módon hozhat létre:

![Hozzon létre egy méretezési egy rendelkezésre állási zóna beállítása](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

A méretezési és az azt támogató erőforrások, például az Azure terheléselosztó és a nyilvános IP-cím, az ugyanabban a zónában megadott jönnek létre.


## <a name="use-the-azure-cli-20"></a>Az Azure parancssori felület használatával 2.0
A méretezési csoport egy rendelkezésre állási zóna használó létrehozásának folyamata megegyezik a részletes a [bevezető cikkben](virtual-machine-scale-sets-create-cli.md). Rendelkezésre állási zónák használatára, a méretezés, a támogatott Azure-régiót állítsa be kell létrehoznia. Adja hozzá a `--zones` paramétert a [az vmss létrehozása](/cli/azure/vmss#az_vmss_create) parancsot, és adja meg, melyik zónába használni (például a zóna *1*, *2*, vagy *3*). Az alábbi példakód létrehozza a méretezési készletben elnevezett *myScaleSet* zónában *1*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```

Hozzon létre és konfigurálja a méretezési készlet a megadott zónában erőforrások és a virtuális gépek összes néhány percet vesz igénybe.


## <a name="use-azure-powershell"></a>Azure PowerShell használatával
A méretezési csoport egy rendelkezésre állási zóna használó létrehozásának folyamata megegyezik a részletes a [bevezető cikkben](virtual-machine-scale-sets-create-powershell.md). Rendelkezésre állási zónák használatára, a méretezés, a támogatott Azure-régiót állítsa be kell létrehoznia. Adja hozzá a `-Zone` paramétert a [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) parancsot, és adja meg, melyik zónába használja (például a zóna *1*, *2*, vagy *3*). Az alábbi példakód létrehozza a skála set config nevű *vmssConfig* a *USA keleti régiója 2* zóna *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

A tényleges méretezési csoport létrehozásához kövesse a további lépéseket a [bevezető cikkben](virtual-machine-scale-sets-create-powershell.md).


## <a name="use-azure-resource-manager-templates"></a>Használjon Azure Resource Manager-sablonokat
A méretezési csoport egy rendelkezésre állási zóna használó létrehozásának folyamata megegyezik az első lépések cikk részletes [Linux](virtual-machine-scale-sets-create-template-linux.md) vagy [Windows](virtual-machine-scale-sets-create-template-windows.md). Rendelkezésre állási zónák használatára, a méretezés, a támogatott Azure-régiót állítsa be kell létrehoznia. Adja hozzá a `zones` tulajdonságot a *Microsoft.Compute/virtualMachineScaleSets* erőforrás adja meg a sablont, és adja meg, melyik zónába használja (például a zóna *1*, *2*, vagy *3*). Az alábbi példa létrehoz egy Linux-skálázási beállítása a nevesített *myScaleSet* a *USA keleti régiója 2* zóna *1*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

A tényleges méretezési csoport létrehozásához kövesse a további lépéseket tartalmazza, mint az első lépések cikk részletes [Linux](virtual-machine-scale-sets-create-template-linux.md) vagy [Windows](virtual-machine-scale-sets-create-template-windows.md)


## <a name="next-steps"></a>További lépések
Most, hogy létrehozott egy rendelkezésre állási zóna terjedő skálán, áttekintheti, hogyan [központi telepítése az alkalmazások telepítése virtuális gépre méretezési készletek](virtual-machine-scale-sets-deploy-app.md) vagy [automatikus skálázás használata a virtuálisgép-méretezési csoportok](virtual-machine-scale-sets-autoscale-overview.md).
