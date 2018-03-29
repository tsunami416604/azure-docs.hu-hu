---
title: Hozzon létre egy rendelkezésre állási zónák (előzetes verzió) használó Azure méretezési |} Microsoft Docs
description: Útmutató az Azure virtuális gép méretezési készlet, amely a rendelkezésre állási zónák használata elleni kimaradások nagyobb redundancia létrehozása
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 8b497af8bc7e3060e184dd6a029b23ccb2d2bbfb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>A rendelkezésre állási zónák (előzetes verzió) használó virtuálisgép-méretezési csoport létrehozása
A datacenter-szintű meghibásodásával szembeni védelemhez a virtuálisgép-méretezési csoportok, a méretezési készlet rendelkezésre állásának zónák is létrehozhat. Azure-régiók, amely támogatja a rendelkezésre állási zónák legalább három különálló zónákra, mindegyiket a saját független a forrás-, hálózati és hűtési kapcsolja. További információkért lásd: [rendelkezésre állási zónák áttekintése](../availability-zones/az-overview.md).

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Single-zóna és a zónaredundáns méretezési csoportok
Amikor telepít egy virtuálisgép-méretezési csoport, dönthet úgy, egy rendelkezésre állási zóna régióban, illetve több zóna.

A skála egy ugyanabban a zónában, vezérelt melyik zónába e Virtuálisgép-példányok futnak, és a méretezési felügyelt és csak a zónán belül autoscales létrehozásakor. Zónaredundáns méretezési lehetővé teszi több zóna kiterjedő egyetlen méretezési készlet létrehozása. Virtuálisgép-példány létrehozásához szükségesek, alapértelmezés szerint ezek egyenletesen elosztását zónák között. Megzavarná a zónák telefonszámaira jöjjön létre, a méretezési készlet nem automatikusan horizontális felskálázás növelhető a kapacitása. Célszerű lehet a CPU és memória-használata alapján automatikus skálázási szabályok konfigurálása. Az automatikus skálázási szabályok lehetővé tenné a méretezési készletben válaszolni veszhetnek el, hogy egy zónát a Virtuálisgép-példány, a fennmaradó működési zónák új példányok kiterjesztése.

Rendelkezésre állási zónák használatára, a méretezési létre kell hozni egy [támogatott Azure-régiót](../availability-zones/az-overview.md#regions-that-support-availability-zones). Szükség [tekintse meg a rendelkezésre állási zónák regisztrálható](http://aka.ms/azenroll). Létrehozhat egy méretezési csoport, amely a rendelkezésre állási zónák használja az alábbi módszerek egyikével:

- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Az Azure Resource Manager-sablonok](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Az Azure Portal használata
A méretezési csoport egy rendelkezésre állási zóna használó létrehozásának folyamata megegyezik a részletes a [bevezető cikkben](quick-create-portal.md). Győződjön meg arról, hogy rendelkezik [tekintse meg a rendelkezésre állási zónák regisztrálva](http://aka.ms/azenroll). Amikor kiválaszt egy támogatott Azure-régió, terjedő skálán, állítsa be az egyik rendelkezésre álló zónában, a következő példában látható módon hozhat létre:

![Hozzon létre egy méretezési egy rendelkezésre állási zóna beállítása](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

A méretezési és az azt támogató erőforrások, például az Azure terheléselosztó és a nyilvános IP-cím, az ugyanabban a zónában megadott jönnek létre.


## <a name="use-the-azure-cli-20"></a>Az Azure parancssori felület használatával 2.0
A méretezési csoport egy rendelkezésre állási zóna használó létrehozásának folyamata megegyezik a részletes a [bevezető cikkben](quick-create-cli.md). Rendelkezésre állási zónák használatára kell létrehozni a méretezési a támogatott Azure-régiót, és rendelkezik [tekintse meg a rendelkezésre állási zónák regisztrálva](http://aka.ms/azenroll).

Adja hozzá a `--zones` paramétert a [az vmss létrehozása](/cli/azure/vmss#az_vmss_create) parancsot, és adja meg, melyik zónába használni (például a zóna *1*, *2*, vagy *3*). Az alábbi példa létrehoz egy nevesített beállítása single-zóna méretezési *myScaleSet* zónában *1*:

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
A single-zóna skála teljes például és hálózati erőforrásokat, olvassa el [a parancsfájlpéldát parancssori felület](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)

### <a name="zone-redundant-scale-set"></a>Zónaredundáns méretezési csoport
Zónaredundáns méretezési létrehozásához állítsa be, használhatja a *szabványos* SKU nyilvános IP cím és a terheléselosztó. A továbbfejlesztett redundancia érdekében a *szabványos* SKU hoz létre a zónaredundáns hálózati erőforrásokat. További információkért lásd: [Azure Load Balancer szabványos áttekintése](../load-balancer/load-balancer-standard-overview.md). Az Ön által létrehozott zónaredundáns méretezési először állítsa be, vagy belső terheléselosztót, el kell végeznie a következő ezeket az előnézeti funkciókat a fiókot regisztrálni.

1. A fiókjának a zónaredundáns méretezési és hálózati szolgáltatásokat a register [az szolgáltatás regisztrálása](/cli/azure/feature#az_feature_register) az alábbiak szerint:

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. A regisztrációt a szolgáltatások néhány percig is eltarthat. A művelet állapotát ellenőrizheti [az szolgáltatás megjelenítése](/cli/azure/feature#az_feature_show):

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    A következő példa bemutatja a megfelelő állapotot szolgáltatás *regisztrált*:
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. Ha mindkét a zónaredundáns skála állítsa be, és a hálózati erőforrások jelentés *regisztrált*, regisztrálja újra a *számítási* és *hálózati* a szolgáltatók [az szolgáltató regisztrálása](/cli/azure/provider#az_provider_register) az alábbiak szerint:

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

Zónaredundáns méretezési készlet létrehozásához adjon meg több zónákat a `--zones` paraméter. Az alábbi példakód létrehozza a zónaredundáns méretezési készletben elnevezett *myScaleSet* keresztül zónák *1,2,3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

Hozza létre és konfigurálja a skála megadott a zóna, amely megadja az erőforrások és a virtuális gépek összes néhány percet vesz igénybe. Zónaredundáns méretezési kész példát és hálózati erőforrásokat, olvassa el [a parancsfájlpéldát parancssori felület](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)


## <a name="use-azure-powershell"></a>Azure PowerShell használatával
A méretezési csoport egy rendelkezésre állási zóna használó létrehozásának folyamata megegyezik a részletes a [bevezető cikkben](quick-create-powershell.md). Rendelkezésre állási zónák használatára kell létrehozni a méretezési a támogatott Azure-régiót, és rendelkezik [tekintse meg a rendelkezésre állási zónák regisztrálva](http://aka.ms/azenroll). Adja hozzá a `-Zone` paramétert a [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) parancsot, és adja meg, melyik zónába használja (például a zóna *1*, *2*, vagy *3*). 

Az alábbi példakód létrehozza a single-zóna méretezési set config nevű *vmssConfig* a *USA keleti régiója 2* zóna *1*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

A single-zóna skála teljes például és hálózati erőforrásokat, olvassa el [a PowerShell-parancsfájlpélda](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1)

### <a name="zone-redundant-scale-set"></a>Zónaredundáns méretezési csoport
Zónaredundáns méretezési létrehozásához állítsa be, használhatja a *szabványos* SKU nyilvános IP cím és a terheléselosztó. A továbbfejlesztett redundancia érdekében a *szabványos* SKU hoz létre a zónaredundáns hálózati erőforrásokat. További információkért lásd: [Azure Load Balancer szabványos áttekintése](../load-balancer/load-balancer-standard-overview.md). Az Ön által létrehozott zónaredundáns méretezési először állítsa be, vagy belső terheléselosztót, el kell végeznie a következő ezeket az előnézeti funkciókat a fiókot regisztrálni.

1. A fiókjának a zónaredundáns méretezési és hálózati szolgáltatásokat a register [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) az alábbiak szerint:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. A regisztrációt a szolgáltatások néhány percig is eltarthat. A művelet állapotát ellenőrizheti [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature):

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    A következő példa bemutatja a megfelelő állapotot szolgáltatás *regisztrált*:
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. Ha mindkét a zónaredundáns skála állítsa be, és a hálózati erőforrások jelentés *regisztrált*, regisztrálja újra a *számítási* és *hálózati* a szolgáltatók [ Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) az alábbiak szerint:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

Zónaredundáns méretezési készlet létrehozásához adjon meg több zónákat a `-Zone` paraméter. Az alábbi példakód létrehozza a zónaredundáns méretezési set config nevű *myScaleSet* keresztül *USA keleti régiója 2* zónák *1, 2, 3*:

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

Ha létrehoz egy nyilvános IP-cím [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) vagy a terheléselosztó [New-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer), adja meg a *- SKU "Standard"* létrehozása zónaredundáns hálózati erőforrásokhoz. Emellett a hálózati biztonsági csoport és a szabályok a forgalmat a létrehozásához szükséges. További információkért lásd: [Azure Load Balancer szabványos áttekintése](../load-balancer/load-balancer-standard-overview.md).

Zónaredundáns méretezési kész példát és hálózati erőforrásokat, olvassa el [a PowerShell-parancsfájlpélda](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1)


## <a name="use-azure-resource-manager-templates"></a>Használjon Azure Resource Manager-sablonokat
A méretezési csoport egy rendelkezésre állási zóna használó létrehozásának folyamata megegyezik az első lépések cikk részletes [Linux](quick-create-template-linux.md) vagy [Windows](quick-create-template-windows.md). Rendelkezésre állási zónák használatára kell létrehozni a méretezési a támogatott Azure-régiót, és rendelkezik [tekintse meg a rendelkezésre állási zónák regisztrálva](http://aka.ms/azenroll). Adja hozzá a `zones` tulajdonságot a *Microsoft.Compute/virtualMachineScaleSets* erőforrás adja meg a sablont, és adja meg, melyik zónába használja (például a zóna *1*, *2*, vagy *3*).

Az alábbi példa létrehoz egy Linux single-zóna méretezési beállítása a nevesített *myScaleSet* a *USA keleti régiója 2* zóna *1*:

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

A single-zóna skála teljes például és hálózati erőforrásokat, olvassa el [minta Resource Manager sablon](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zónaredundáns méretezési csoport
Zónaredundáns méretezési készlet létrehozásához adjon meg több értéket a `zones` tulajdonságát a *Microsoft.Compute/virtualMachineScaleSets* erőforrástípus. Az alábbi példakód létrehozza a zónaredundáns méretezési készletben elnevezett *myScaleSet* keresztül *USA keleti régiója 2* zónák *1,2,3*:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Ha létrehoz egy nyilvános IP-címet vagy egy adott terheléselosztóhoz, adja meg a *"sku": {"name": "Standard"} "* tulajdonság zónaredundáns hálózati erőforrások létrehozásához. Emellett a hálózati biztonsági csoport és a szabályok a forgalmat a létrehozásához szükséges. További információkért lásd: [Azure Load Balancer szabványos áttekintése](../load-balancer/load-balancer-standard-overview.md).

Zónaredundáns méretezési kész példát és hálózati erőforrásokat, olvassa el [minta Resource Manager sablon](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)


## <a name="next-steps"></a>További lépések
Most, hogy létrehozott egy rendelkezésre állási zóna terjedő skálán, áttekintheti, hogyan [központi telepítése az alkalmazások telepítése virtuális gépre méretezési készletek](virtual-machine-scale-sets-deploy-app.md) vagy [automatikus skálázás használata a virtuálisgép-méretezési csoportok](virtual-machine-scale-sets-autoscale-overview.md).
