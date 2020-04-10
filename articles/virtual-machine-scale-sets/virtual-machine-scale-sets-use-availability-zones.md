---
title: Rendelkezésre állási zónákat használó Azure-méretezési csoport létrehozása
description: Megtudhatja, hogyan hozhat létre azure-beli virtuálisgép-méretezési csoportokat, amelyek rendelkezésre állási zónákat használnak a kimaradások elleni megnövekedett redundancia érdekében
author: ju-shim
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: jushiman
ms.openlocfilehash: c8795f46e47b2ab43898f6f436b9ee6026a22fa7
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011565"
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>Rendelkezésre állási zónákat használó virtuálisgép-méretezési csoport létrehozása

A virtuálisgép-méretezési csoportok adatközpont-szintű hibák elleni védelme érdekében létrehozhat egy méretezési csoportot a rendelkezésre állási zónák között. A rendelkezésre állási zónákat támogató Azure-régiók legalább három külön zónával rendelkeznek, amelyek mindegyike saját független áramforrással, hálózattal és hűtéssel rendelkezik. További információt [a Rendelkezésre állási zónák áttekintése című témakörben talál.](../availability-zones/az-overview.md)

## <a name="availability-considerations"></a>Rendelkezésre állási szempontok

Ha a *2017-12-01-es API-verziónak*köszönhetően egy méretezési csoportba egy vagy több zónába telepíti a méretezést, lehetősége van a "maximális szétosztás" vagy a "statikus 5 tartaléktartomány szétválasztása" segítségével. A maximális terjedés, a méretezési készlet osztja a virtuális gépek között a lehető legtöbb tartalék tartományok az egyes zónákon belül. Ez a terjedés zónanként ötnél több vagy kevesebb tartalék tartományban is lehet. A "statikus 5 tartalék tartomány szétterítése", a méretezési csoport osztja a virtuális gépek között pontosan öt tartalék tartományok zónánként. Ha a méretezési csoport nem talál öt különálló tartalék tartományt zónánként a foglalási kérelem teljesítéséhez, a kérés sikertelen lesz.

**Azt javasoljuk, üzembe helyezése maximális terjedésa a legtöbb számítási feladatok,** mivel ez a megközelítés biztosítja a legjobb terjedését a legtöbb esetben. Ha replikák kell elosztani a különböző hardverelkülönítési egységek, javasoljuk, hogy a rendelkezésre állási zónák között, és használja a maximális terjedési belül minden zónában.

A maximális terjedés, csak egy tartalék tartomány a méretezési csoport virtuálisgép-példány nézetben, és a példány metaadatok, függetlenül attól, hogy hány tartalék tartományok a virtuális gépek között elosztva. Az egyes zónákon belüli terjedés implicit.

A maximális szórás használatához állítsa *a platformFaultDomainCount* értéket *1-re.* A statikus öt tartaléktartomány terjedésének használatához állítsa *a platformFaultDomainCount-ot* *5-re*. A *2017-12-01*API-verzióban a *platformFaultDomainCount* alapértelmezés szerint *1* az egyzónás és a zónaközi méretezési csoportok esetében. Jelenleg csak a statikus öt tartalék tartomány terjedése támogatott regionális (nem zónaszintű) méretezési csoportok.

### <a name="placement-groups"></a>Elhelyezési csoportok

Méretezési csoport telepítésekor is lehetősége van arra, hogy egyetlen [elhelyezési csoporttal](./virtual-machine-scale-sets-placement-groups.md) üzembe helyezze rendelkezésre állási zónánként vagy zónánként több beállítással. A regionális (nem zónaszintű) méretezési csoportok esetében a választás az, hogy egyetlen elhelyezési csoport a régióban, vagy több a régióban. A legtöbb számítási feladatok hoz, azt javasoljuk, több elhelyezési csoportok, amely lehetővé teszi a nagyobb léptékű. A *2017-12-01-es API-verzióban*a méretezés alapértelmezés szerint több elhelyezési csoportra van beállítva az egyzónás és a zónaközi méretezési csoportokhoz, de alapértelmezés szerint a regionális (nem zónaszintű) méretezési csoportok egyetlen elhelyezési csoportjára vannak beállítva.

> [!NOTE]
> Ha maximális szórást használ, több elhelyezési csoportot kell használnia.

### <a name="zone-balancing"></a>Zóna kiegyensúlyozása

Végül a több zónában üzembe helyezett méretezési csoportok esetében lehetősége van a "legjobb erőkifejtési zóna egyensúlya" vagy a "szigorú zónaegyensúly" kiválasztására is. A méretezési készlet akkor minősül "kiegyensúlyozottnak", ha minden\\zónában azonos számú virtuális gép vagy + - 1 virtuális gép van az összes többi zónában a méretezési készlethez. Például:

- Az 1., 3-as zónában lévő 2 virtuális gépből és a 3-as zónában lévő 3 virtuális gépekből álló méretezési készlet kiegyensúlyozottnak minősül. Csak egy zóna egy másik virtuális gép száma, és ez csak 1 kevesebb, mint a többi zónában. 
- Az 1., a 2. Az 1-es zónában 2-vel kevesebb virtuális gép található, mint a 2.

Lehetséges, hogy a méretezési készletben lévő virtuális gépek sikeresen létrejöttek, de a virtuális gépek bővítményei nem telepíthetők. Ezek a bővítményhibákkal rendelkező virtuális gépek továbbra is számítanak, amikor meghatározzák, hogy egy méretezési készlet kiegyensúlyozott-e. Például egy méretezési csoport 3 virtuális géptel az 1., 3 virtuális gépek a 2.

A legjobb hancöttetési zónaegyensúly mellett a méretezési készlet megkísérli a be- és kiskálázást az egyensúly fenntartása mellett. Azonban ha valamilyen oknál fogva ez nem lehetséges (például ha egy zóna leáll, a méretezési csoport nem hozhat létre új virtuális gép ebben a zónában), a méretezési készlet lehetővé teszi az ideiglenes egyensúlyhiány sikeresen skálázható be- vagy ki. Későbbi horizontális felskálázási kísérletek esetén a méretezési készlet virtuális gépeket ad hozzá azokhoz a zónákhoz, amelyeknek több virtuális gépre van szükségük a méretezési készlet kiegyenlítéséhez. Hasonlóképpen a későbbi skálázási kísérletek, a méretezési készlet eltávolítja a virtuális gépeket a zónákból, amelyek kevesebb virtuális gépet igényelnek a méretezési készlet kiegyenlítendő. A "szigorú zónaegyensúly", a méretezési csoport nem minden olyan kísérletet, hogy a skála, vagy ki, ha ezzel okoz egyensúlytalanság.

A zóna legjobb egyensúlyának használatához állítsa *a zoneBalance* értéket *hamis*ra. Ez a beállítás az alapértelmezett az API *2017-12-01-es verziójában.* A szigorú zónaegyenleg használatához állítsa *a zoneBalance* értéket *true*értékre.

## <a name="single-zone-and-zone-redundant-scale-sets"></a>Egyzónás és zónaredundáns léptékkészletek

Virtuálisgép-méretezési csoport telepítésekor választhat, hogy egy adott régióban egyetlen rendelkezésre állási zónát vagy több zónát használ.When you deploy a virtual machine scale set, you can choose to use a single availability Zone in a region, or multiple zones.

Amikor egy méretezési csoportot hoz létre egy zónában, szabályozhatja, hogy melyik zóna minden virtuálisgép-példány fut, és a méretezési csoport kezelése és automatikus skálázása csak az adott zónán belül. A zónaredundáns méretezési csoport lehetővé teszi, hogy hozzon létre egy méretezési csoport, amely több zónára terjed ki. Virtuálisgép-példányok létrehozásakor alapértelmezés szerint egyenletesen vannak egyensúlyban a zónák között. Ha valamelyik zónában megszakítás történik, a méretezési készlet nem méretezhető ki automatikusan a kapacitás növelése érdekében. Ajánlott eljárás lenne az automatikus skálázási szabályok konfigurálása a PROCESSZOR vagy a memória használat alapján. Az automatikus skálázási szabályok lehetővé tenné, hogy a méretezési csoport reagáljon a virtuálisgép-példányok elvesztését az adott zónában a fennmaradó működési zónákúj példányok horizontális felskálázása.

A rendelkezésre állási zónák használatához a méretezési csoport hozlétre kell egy [támogatott Azure-régióban.](../availability-zones/az-overview.md#services-support-by-region) Létrehozhat egy méretezési csoportot, amely a rendelkezésre állási zónákat használja az alábbi módszerek egyikével:

- [Azure Portal](#use-the-azure-portal)
- Azure CLI
- [Azure PowerShell](#use-azure-powershell)
- [Azure Resource Manager-sablonok](#use-azure-resource-manager-templates)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Az elérhetőségi zónát használó méretezési csoport létrehozásának folyamata megegyezik az [első lépések ről szóló cikkben részletezett eljárással.](quick-create-portal.md) Ha kiválaszt egy támogatott Azure-régiót, létrehozhat egy méretezési csoportot egy vagy több elérhető zónában, ahogy az a következő példában látható:

![Méretezési csoport létrehozása egyetlen rendelkezésre állási zónában](media/virtual-machine-scale-sets-use-availability-zones/vmss-az-portal.png)

A méretezési és támogató erőforrások, például az Azure terheléselosztó és a nyilvános IP-cím, a megadott egyetlen zónában jönnek létre.

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Az elérhetőségi zónát használó méretezési csoport létrehozásának folyamata megegyezik az [első lépések ről szóló cikkben részletezett eljárással.](quick-create-cli.md) A rendelkezésre állási zónák használatához létre kell hoznia a méretezési csoportot egy támogatott Azure-régióban.

Adja `--zones` hozzá a paramétert az [az vmss create](/cli/azure/vmss) parancshoz, és adja meg, hogy melyik zónát használja (például *1,* *2*vagy *3*zóna). A következő példa létrehoz egy egyzónás méretezési készlet nevű *myScaleSet* az *1-* es zónában:

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

Az egyzónás méretezési és hálózati erőforrások teljes példáját ebben a [mintában található CLI-parancsfájl](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh)

### <a name="zone-redundant-scale-set"></a>Zónaredundáns méretezési készlet

Zónaredundáns méretezési csoport létrehozásához használjon *szabványos* termékváltozat nyilvános IP-címet és terheléselosztót. A fokozott redundancia érdekében a *standard* termékváltozat zónaredundáns hálózati erőforrásokat hoz létre. További információ: [Azure Load Balancer Standard overview](../load-balancer/load-balancer-standard-overview.md) and [Standard Load Balancer and Availability Zones](../load-balancer/load-balancer-standard-availability-zones.md).

Zónaredundáns méretezési csoport létrehozásához adjon `--zones` meg több zónát a paraméterrel. A következő példa létrehoz egy *myScaleSet* nevű zónaredundáns méretezési készletet *az 1,2,3*zónák között:

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

Néhány percet vesz igénybe az összes méretezési készlet erőforrás és virtuális gép létrehozása és konfigurálása a megadott zónában(s) . A zónaredundáns méretezési készlet és a hálózati erőforrások teljes példáját ebben a [mintában található CLI-parancsfájl](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh)

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

A rendelkezésre állási zónák használatához létre kell hoznia a méretezési csoportot egy támogatott Azure-régióban. Adja `-Zone` hozzá a paramétert a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) parancshoz, és adja meg, hogy melyik zónát használja (például *1,* *2*vagy *3*zóna ).

A következő példa létrehoz egy egyzónás méretezési készlet nevű *myScaleSet* *az USA keleti részén 2* zóna *1*. A rendszer automatikusan létrehozza az Azure-beli hálózati erőforrásokat a virtuális hálózathoz, a nyilvános IP-címhez és a terheléselosztóhoz. Amikor a rendszer erre kéri, adja meg használni kívánt rendszergazdai hitelesítő adatait a méretezési csoportban lévő virtuálisgép-példányokhoz:

```powershell
New-AzVmss `
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

### <a name="zone-redundant-scale-set"></a>Zónaredundáns méretezési készlet

Zónaredundáns méretezési csoport létrehozásához adjon `-Zone` meg több zónát a paraméterrel. A következő példa létrehoz egy *myScaleSet* nevű zónaredundáns méretezési készletet *az USA keleti részén 2* zóna *1, 2, 3*. A zónaredundáns Azure hálózati erőforrások virtuális hálózat, nyilvános IP-cím és terheléselosztó automatikusan létrejön. Amikor a rendszer erre kéri, adja meg használni kívánt rendszergazdai hitelesítő adatait a méretezési csoportban lévő virtuálisgép-példányokhoz:

```powershell
New-AzVmss `
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

Az elérhetőségi zónát használó méretezési csoport létrehozásának folyamata megegyezik a [Linux](quick-create-template-linux.md) vagy [Windows](quick-create-template-windows.md)első lépésekről szóló cikkében részletezett részletes. A rendelkezésre állási zónák használatához létre kell hoznia a méretezési csoportot egy támogatott Azure-régióban. Adja `zones` hozzá a tulajdonságot a sablonBan lévő *Microsoft.Compute/virtualMachineScaleSets* erőforrástípushoz, és adja meg, hogy melyik zónát használja (például *az 1.* zónát, *a 2-* vagy 3-as *zónát).*

A következő példa létrehoz egy Linux egyzónás méretezési készlet nevű *myScaleSet* *az USA keleti részén 2* zóna *1:*

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

Az egyzónás méretezési csoport és a hálózati erőforrások teljes példáját ebben a mintában az [Erőforrás-kezelő sablonban lehet megtekinteni.](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json)

### <a name="zone-redundant-scale-set"></a>Zónaredundáns méretezési készlet

Zónaredundáns méretezési csoport létrehozásához adjon `zones` meg több értéket a *Microsoft.Compute/virtualMachineScaleSets* erőforrástípus tulajdonságában. A következő példa létrehoz egy *myScaleSet* nevű zónaredundáns méretezési készletet *az USA keleti részén 2* zóna *1,2,3:*

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

Ha nyilvános IP-címet vagy terheléselosztót hoz létre, adja meg az *"sku": { "name": "Standard" }"* tulajdonságot zónaredundáns hálózati erőforrások létrehozásához. Emellett létre kell hoznia egy hálózati biztonsági csoportot és szabályokat a forgalom engedélyezéséhez. További információ: [Azure Load Balancer Standard overview](../load-balancer/load-balancer-standard-overview.md) and [Standard Load Balancer and Availability Zones](../load-balancer/load-balancer-standard-availability-zones.md).

A zónaredundáns méretezési csoport és a hálózati erőforrások teljes példáját ebben a mintában található [erőforrás-kezelő sablonban](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json)

## <a name="next-steps"></a>További lépések

Most, hogy létrehozott egy méretezési készletet egy rendelkezésre állási zónában, [megtudhatja, hogyan telepíthet alkalmazásokat virtuálisgép-méretezési csoportokra,](tutorial-install-apps-cli.md) vagy [használhatja az automatikus skálázást a virtuálisgép-méretezési készletekkel.](tutorial-autoscale-cli.md)
