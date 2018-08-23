---
title: Hozzon létre egy Azure méretezési csoportot, amely a rendelkezésre állási zónák |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Azure-beli virtuálisgép-méretezési csoportok, amelyek a rendelkezésre állási zónák a nagyobb redundancia leállások ellen
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: cynthn
ms.openlocfilehash: 7297633b5a8954eb39e0a40bfd45b02d3838a734
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054903"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>A rendelkezésre állási zónák használó virtuális gép méretezési csoport létrehozása

A virtuális gép méretezési csoportjai szembeni adatközpont-szintű leállások, létrehozhat egy méretezési csoport rendelkezésre állási zónák között. Az Azure-régiók rendelkezésre állási zónákat támogató legalább három különálló zónákra, amelyek mindegyike saját független forrás, hálózattal és hűtéssel. További információkért lásd: [a rendelkezésre állási zónákat áttekintő](../availability-zones/az-overview.md).

## <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Egy méretezési csoportot az API-verzióval kezdődően egy vagy több zónában történő telepítésekor *2017-12-01*, lehetősége van "maximális terjesztés" vagy "statikus 5 tartalék tartomány terjesztés" üzembe helyezhető. A maximális terjesztés, a méretezési, minden zónában a lehető számos tartalék tartományok osztja a virtuális gépek között. A terjesztés között lehet több vagy kevesebb mint öt tartalék tartományok zónánként. A "statikus 5 tartalék tartomány terjesztés", a méretezési csoportot a virtuális gépek zónánként pontosan 5 tartalék tartomány között osztja el. Ha a méretezési csoportban nem találja a foglalási kérelem teljesítéséhez zónánként öt különböző tartalék tartományban, a kérés nem teljesíthető.

**Javasoljuk, hogy a legtöbb maximális terjesztés**, mivel ez a megközelítés biztosítja, hogy a legtöbb esetben ajánlott terjedhetnek. Replikák különböző hardveregységre elkülönítési elosztva van szüksége, ha azt javasoljuk a rendelkezésre állási zónák között szét, és felhasználja az belül az egyes zónák maximális terjedhetnek.

A maximális terjesztés csak látni egy tartalék tartományt a méretezési csoport virtuális gép példányait tartalmazó nézetet és a példány metaadatok, függetlenül attól, hogy hány tartalék tartomány a virtuális gépek vannak elosztva. Implicit terjedhetnek egyes zónán belül.

Maximális terjesztés használatához állítsa *platformFaultDomainCount* való *1*. Statikus öt tartalék tartomány terjesztés használatához állítsa *platformFaultDomainCount* való *5*. API-verzióban *2017-12-01*, *platformFaultDomainCount* alapértelmezés szerint a *1* és a zónák közötti Egyzónás méretezési csoport állítja be. Jelenleg csak statikus öt tartalék tartomány terjesztés támogatott regionális méretezési csoportokhoz.

### <a name="placement-groups"></a>Elhelyezési csoportok

Amikor telepít egy méretezési csoportot, akkor is telepíteni egyetlen [elhelyezési csoport](./virtual-machine-scale-sets-placement-groups.md) rendelkezésre állási zónában egy vagy több zónánként. A regionális méretezési csoportokhoz el több régióban vagy egy csoport egyetlen elhelyezési csoport rendelkezik a régióban. A legtöbb számítási feladatokhoz javasoljuk, hogy több elhelyezési csoportra, amely lehetővé teszi nagyobb méretezést. API-verzióban *2017-12-01*, a méretezési csoportok alapértelmezett és a zónák közötti Egyzónás méretezési csoportok több elhelyezési csoporthoz, de a rendszer alapértelmezés szerint a regionális méretezési csoport egyetlen elhelyezési csoport.

> [!NOTE]
> Maximális terjesztés használja, ha több elhelyezési csoportot kell használnia.

### <a name="zone-balancing"></a>Terheléselosztás zóna

Végül a méretezési csoportok több zónában üzembe helyezett, akkor is "ajánlott beavatkozást zónák kiegyensúlyozásáról" vagy "szigorú zónák kiegyensúlyozásáról" lehetőséget. Egy méretezési csoportot számít az "elosztott terhelésű" Ha az egyes virtuális gépek azonos számú zóna vagy +\\– 1 virtuális gép a méretezési csoport más zónákban. Példa:

- Elosztott terhelésű zónában 1, 2 és 3 virtuális gépeket a 3. zóna számít zónában 3 virtuális gépek 2 virtuális gépet tartalmazó méretezési. Van egy másik virtuális gépek száma csak egy zónát pedig csak 1 kisebb, mint a más zónákban. 
- Egy méretezési 1 virtuális gép az 1. zóna, 2 és 3 virtuális gépeket a 3. zóna számít zónában 3 virtuális gépet kiegyensúlyozatlan. 1. zóna 2 zónák 2. és 3-nál kevesebb virtuális gépet tartalmaz.

Akkor lehet, hogy a méretezési csoportban lévő virtuális gépek létrehozása sikeresen megtörtént, de a bővítményt a virtuális gépek sikertelen üzembe helyezéséhez. Ezek a virtuális gépek-bővítményekkel kapcsolatos hibák továbbra is számításba vesszük, ha egy méretezési csoportot kiegyensúlyozott meghatározásakor. Például egy méretezési csoportot 3 virtuális gépeket az 1. zónába, a 2. zóna 3 virtuális gépet, és 3 virtuális gépeket a 3. zóna számít, még akkor is, ha az összes bővítmény nem sikerült az 1. zóna elosztott terhelésű, és az összes bővítmény zónákban sikeres volt, 2. és 3.

A legjobb zónák kiegyensúlyozásáról a méretezési csoport horizontális skálázás egyenleg fenntartásához tett kísérletek. Azonban ha valamilyen okból ez nem lehetséges (például ha egy zóna leáll, a méretezési csoportban nem hozható létre egy új virtuális Gépet a zónában), a méretezési csoportok segítségével ideiglenes egyenetlenségének sikerült horizontálisan le- illetve ki. Az ezt követő kísérletek horizontális felskálázás a méretezési hozzáadja virtuális gépeket, amelyekre szükség további virtuális gépeket a méretezési csoport kiegyensúlyozott zónák. Ehhez hasonlóan a skálázási ezt követő kísérletek, a méretezési távolít el virtuális gépek zónák, amelyekre szükség kevesebb virtuális gépeket a méretezési csoport lehet átgondolni. A "szigorú zónák kiegyensúlyozásáról" a méretezési horizontálisan le- illetve, ha ezzel kiegyensúlyozatlanság jellemzi okozna tett bármilyen kísérlet sikertelen lesz.

Legjobb zónák kiegyensúlyozásáról használatához állítsa *zoneBalance* való *hamis*. Ez a beállítás az alapértelmezett API-verzióban nem *2017-12-01*. A szigorú zónák kiegyensúlyozásáról használatához állítsa *zoneBalance* való *igaz*.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Egyzónás és zónaredundáns méretezési csoportok

Amikor telepít egy virtuálisgép-méretezési csoportot, válassza ki egy rendelkezésre állási zóna egy régióban, vagy több zónában.

Mikor hozzon létre egy méretezési csoportot egy egyetlen zónában, Ön szabályozza, melyik zónába összes Virtuálisgép-példányok futnak, és a méretezési felügyelt és csak a zónán belüli automatikus skálázást alkalmat. Zónaredundáns méretezési lehetővé teszi több zónában kiterjedő egy méretezési csoport létrehozása. Virtuálisgép-példányok létrehozásakor alapértelmezés szerint ezek egyenletesen között oszlanak el zónák. A zónák telefonszámaira megtörténik a működésében zavarokat tapasztalhat, egy méretezési csoportot nem automatikusan horizontális felskálázása kapacitás bővítéséhez. Célszerű lehet a CPU és memória kihasználtsága alapján automatikus skálázási szabályok konfigurálása. Az automatikus skálázási szabályok lehetővé tenné a méretezési csoport Virtuálisgép-példányok, hogy egy zónában egy elvesztését válaszolni a fennmaradó működési zónák új példányok horizontális felskálázásával.

Rendelkezésre állási zónák használatára, a méretezési csoportot kell létrehozni egy [az Azure-régióban támogatott](../availability-zones/az-overview.md#regions-that-support-availability-zones). Létrehozhat egy méretezési csoportot, amely a rendelkezésre állási zónák használja az alábbi módszerek egyikével:

- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Az Azure Resource Manager-sablonok](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Hozzon létre egy méretezési csoportot, amely egy rendelkezésre állási zónát használ, a folyamat megegyezik a részletes a [első lépések a cikk](quick-create-portal.md). Amikor kiválaszt egy támogatott Azure-régiót, létrehozhat egy méretezési csoport egy vagy több rendelkezésre álló zónákban, az alábbi példában látható módon:

![Hozzon létre egy méretezési csoportot egy egyetlen rendelkezésre állási zónában](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

A méretezési és a támogató erőforrások, például az Azure load balancer és a nyilvános IP-cím jönnek létre a megadott zónában.

## <a name="use-the-azure-cli-20"></a>Az Azure CLI 2.0 használata

Hozzon létre egy méretezési csoportot, amely egy rendelkezésre állási zónát használ, a folyamat megegyezik a részletes a [első lépések a cikk](quick-create-cli.md). A rendelkezésre állási zónák használatához létre kell hoznia a méretezési csoportban egy támogatott Azure-régióban.

Adja hozzá a `--zones` paramétert a [az vmss létrehozásához](/cli/azure/vmss#az_vmss_create) parancsot, majd adja meg, melyik zónába használata (például a zóna *1*, *2*, vagy *3*). A következő példában létrehozunk egy Egyzónás méretezési elnevezett *myScaleSet* zónában *1*:

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

Egy teljes példa – Egyzónás méretezési csoport és a hálózati erőforrásokhoz, olvassa el [a CLI-példaszkript](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.)

### <a name="zone-redundant-scale-set"></a>Zónaredundáns méretezési csoport

Zónaredundáns méretezési csoport létrehozásához állítsa be, használhatja a *Standard* Termékváltozat nyilvános IP cím és a load balancer. A továbbfejlesztett redundancia biztosítása érdekében a *Standard* Termékváltozat zónaredundáns hálózati erőforrást hoz létre. További információkért lásd: [áttekintése az Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md) és [Standard Load Balancer és rendelkezésre állási zónák](../load-balancer/load-balancer-standard-availability-zones.md).

Zónaredundáns méretezési csoport létrehozása, adja meg a több zónában a `--zones` paraméter. A következő példában létrehozunk egy zónaredundáns méretezési csoport nevű *myScaleSet* zónákban *1,2,3*:

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1 2 3
```

Hozzon létre, és konfigurálhatók a méretezési csoport erőforrásainak és virtuális gépeinek az Ön által megadott van(nak) néhány percet vesz igénybe. Egy teljes példát egy zónaredundáns méretezési csoport és a hálózati erőforrásokhoz, olvassa el [a CLI-példaszkript](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

A rendelkezésre állási zónák használatához létre kell hoznia a méretezési csoportban egy támogatott Azure-régióban. Adja hozzá a `-Zone` paramétert a [New-azurermvmssconfig parancsmaghoz](/powershell/module/azurerm.compute/new-azurermvmssconfig) parancsot, majd adja meg, melyik zónába használata (például a zóna *1*, *2*, vagy *3*).

A következő példában létrehozunk egy Egyzónás méretezési elnevezett *myScaleSet* a *USA keleti RÉGIÓJA 2* zóna *1*. A rendszer automatikusan létrehozza az Azure-beli hálózati erőforrásokat a virtuális hálózathoz, a nyilvános IP-címhez és a terheléselosztóhoz. Amikor a rendszer erre kéri, adja meg használni kívánt rendszergazdai hitelesítő adatait a méretezési csoportban lévő virtuálisgép-példányokhoz:

```powershell
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1"
```

### <a name="zone-redundant-scale-set"></a>Zónaredundáns méretezési csoport

Zónaredundáns méretezési csoport létrehozása, adja meg a több zónában a `-Zone` paraméter. A következő példában létrehozunk egy zónaredundáns méretezési csoport nevű *myScaleSet* között *USA keleti RÉGIÓJA 2* zónák *1, 2, 3*. A zónaredundáns Azure hálózati erőforrásainak virtuális hálózat, a nyilvános IP-cím és a load balancer automatikusan jönnek létre. Amikor a rendszer erre kéri, adja meg használni kívánt rendszergazdai hitelesítő adatait a méretezési csoportban lévő virtuálisgép-példányokhoz:

```powershell
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS2" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic" `
  -Zone "1", "2", "3"
```

## <a name="use-azure-resource-manager-templates"></a>Használjon Azure Resource Manager-sablonokat

Hozhat létre egy méretezési csoportot, amely a rendelkezésre állási zónában a folyamat megegyezik a az első lépéseket bemutató cikkben leírt módon [Linux](quick-create-template-linux.md) vagy [Windows](quick-create-template-windows.md). A rendelkezésre állási zónák használatához létre kell hoznia a méretezési csoportban egy támogatott Azure-régióban. Adja hozzá a `zones` tulajdonságot a *Microsoft.Compute/virtualMachineScaleSets* erőforrás írja be a sablonban, és adja meg, melyik zónába használata (például a zóna *1*, *2*, vagy *3*).

Az alábbi példa létrehoz egy Linux – Egyzónás méretezési csoport nevű *myScaleSet* a *USA keleti RÉGIÓJA 2* zóna *1*:

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

Egy teljes példa – Egyzónás méretezési csoport és a hálózati erőforrásokhoz, olvassa el [a mintául szolgáló Resource Manager-sablon](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zónaredundáns méretezési csoport

Zónaredundáns méretezési csoport létrehozása, adja meg a több érték a `zones` tulajdonsága a *Microsoft.Compute/virtualMachineScaleSets* erőforrástípus. A következő példában létrehozunk egy zónaredundáns méretezési csoport nevű *myScaleSet* között *USA keleti RÉGIÓJA 2* zónák *1,2,3*:

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

Ha a nyilvános IP-cím vagy egy terheléselosztót hoz létre, adja meg a *"sku": {"name": "Standard"} "* tulajdonság zónaredundáns hálózati erőforrások létrehozása. Is szeretne létrehozni egy hálózati biztonsági csoport és a szabályok minden forgalom engedélyezéséhez. További információkért lásd: [áttekintése az Azure Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md) és [Standard Load Balancer és rendelkezésre állási zónák](../load-balancer/load-balancer-standard-availability-zones.md).

Egy teljes példát egy zónaredundáns méretezési csoport és a hálózati erőforrásokhoz, olvassa el [a mintául szolgáló Resource Manager-sablon](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>További lépések

Most, hogy létrehozta a méretezési csoport egy rendelkezésre állási zónában, megtudhatja hogyan [alkalmazások központi telepítése a virtuális gépen méretezési csoportokban](tutorial-install-apps-cli.md) vagy [használja az automatikus méretezés a virtual machine scale sets](tutorial-autoscale-cli.md).
