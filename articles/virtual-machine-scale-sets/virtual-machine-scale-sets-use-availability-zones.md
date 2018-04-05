---
title: Hozzon létre egy rendelkezésre állási zónák használó Azure méretezési |} Microsoft Docs
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
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: dee06eee045bc24c2864333a66a6d145a771b3ad
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones"></a>A rendelkezésre állási zónák használó virtuálisgép-méretezési csoport létrehozása
A datacenter-szintű meghibásodásával szembeni védelemhez a virtuálisgép-méretezési csoportok, a méretezési készlet rendelkezésre állásának zónák is létrehozhat. Azure-régiók, amely támogatja a rendelkezésre állási zónák legalább három különálló zónákra, mindegyiket a saját független a forrás-, hálózati és hűtési kapcsolja. További információkért lásd: [rendelkezésre állási zónák áttekintése](../availability-zones/az-overview.md).


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Single-zóna és a zónaredundáns méretezési csoportok
Amikor telepít egy virtuálisgép-méretezési csoport, dönthet úgy, egy rendelkezésre állási zóna régióban, illetve több zóna.

A skála egy ugyanabban a zónában, vezérelt melyik zónába e Virtuálisgép-példányok futnak, és a méretezési felügyelt és csak a zónán belül autoscales létrehozásakor. Zónaredundáns méretezési lehetővé teszi több zóna kiterjedő egyetlen méretezési készlet létrehozása. Virtuálisgép-példány létrehozásához szükségesek, alapértelmezés szerint ezek egyenletesen elosztását zónák között. Megzavarná a zónák telefonszámaira jöjjön létre, a méretezési készlet nem automatikusan horizontális felskálázás növelhető a kapacitása. Célszerű lehet a CPU és memória-használata alapján automatikus skálázási szabályok konfigurálása. Az automatikus skálázási szabályok lehetővé tenné a méretezési készletben válaszolni veszhetnek el, hogy egy zónát a Virtuálisgép-példány, a fennmaradó működési zónák új példányok kiterjesztése.

Rendelkezésre állási zónák használatára, a méretezési létre kell hozni egy [támogatott Azure-régiót](../availability-zones/az-overview.md#regions-that-support-availability-zones). Létrehozhat egy méretezési csoport, amely a rendelkezésre állási zónák használja az alábbi módszerek egyikével:

- [Azure Portal](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Az Azure Resource Manager-sablonok](#use-azure-resource-manager-templates)

## <a name="availability-considerations"></a>Rendelkezésre állási szempontok
API verziója 2017-12-01, kezdve a skála beállítani egy vagy több zónák telepítésekor, lehetősége van a "max terjednek" vagy "statikus 5 tartalék tartomány terjednek" központi telepítése. A maximális oszlik, a méretezési között osztja el a virtuális gépek között tetszőleges számú hiba tartományok lehető minden a zónán belül. Ez terjednek között lehet nagyobb, vagy kevesebb, mint öt fault zónánkénti tartományok. Másrészről a "statikus 5 tartalék tartomány terjednek", a méretezési között osztja el a virtuális gépek zónánkénti pontosan 5 tartalék tartományokban. A méretezési készlet nem található a foglalási kérelem teljesítéséhez zónánkénti 5 különböző tartalék tartományok, ha a kérelem sikertelen lesz.

**Javasoljuk, hogy a munkaterhelések többségéhez maximális terjednek történő telepítése** mert maximális terjednek biztosítja a legjobb a legtöbb esetben terjednek. Ha szüksége lesz elosztva a különböző hardver elkülönítési egységek replikák, ajánlott rendelkezésre állási zónák keresztül terjednek és okhoz belül az egyes zónák maximális terjednek. Vegye figyelembe, hogy a maximális oszlik, csak akkor jelenik meg egy tartalék tartomány, a méretezési csoport virtuális gép példányait tartalmazó nézetet, és a példány metaadatai között, függetlenül attól, hogy hány tartalék tartomány a virtuális gépek vannak ténylegesen elosztva; minden zóna terjednek implicit módon.

Maximális oszlik, állítsa "platformFaultDomainCount" 1. Statikus 5 tartalék tartomány oszlik, állítsa "platformFaultDomainCount" 5. Az API verzió 2017-12-01 "platformFaultDomainCount" alapértelmezés szerint az 1. egy- és zónát kereszt méretezési készlet. Jelenleg csak statikus 5 tartalék tartomány terjednek van támogatott regionális méretezési készlet.

Emellett egy méretezési telepítésekor lehetősége van történő telepítésének egyetlen [elhelyezési csoport](./virtual-machine-scale-sets-placement-groups.md) rendelkezésre állási zónánkénti, vagy az egyes zónák több (regionális méretezési csoportok kiválasztása, hogy egyetlen elhelyezési csoport rendelkezik a a régióban, vagy több régióban). A munkaterhelések többségéhez azt javasoljuk, több elhelyezési csoportot, amely lehetővé teszi nagyobb skálázási. Az API verzió 2017-12-01 skálázási készletekben alapértelmezett egy- és zónát kereszt méretezési csoportok több elhelyezési csoportjai, de azok az alapértelmezett területi méretezési csoportok egyetlen elhelyezési csoportjának.

>[!NOTE]
> Maximális terjednek használatakor több elhelyezési csoportot kell használnia.

Végezetül a méretezési készlet több zóna telepített, akkor is kiválasztható "legjobb elérhető zóna balance" vagy "strict zóna balance". A méretezési tekinteni "kiegyensúlyozott", ha a virtuális gépek minden egyes zónában számát a virtuális gépek méretezési készlet más zónákban számát egyikébe esik. A példány, a méretezési zónában 1, 3 virtuális gép zónában 2 és 3 virtuális gép zónában 3, 2 virtuális gép beállítása elosztott terhelésű minősül. Azonban a méretezési, állítsa be az 1 virtuális gép zónában 1, 2 és 3 virtuális gépek 3 zónában tekinthető zónában 3 virtuális gép tette. Akkor lehet, hogy a méretezési csoportban lévő virtuális gépek létrehozása sikeresen megtörtént, amíg a bővítményt a virtuális gépek sikertelen. Bővítmény hibákkal rendelkező virtuális gépeken továbbra is számítanak, ha egy méretezési kiegyensúlyozott meghatározásakor. Például egy méretezési beállítani 3 virtuális gép zónában 1, 3 virtuális gép zónában 2 és 3 virtuális gép zónában 3 elosztott terhelésű akkor is, ha az összes bővítmény zóna 1 nem tudott minősülnek, és az összes bővítmény zónákban sikeres volt, 2 és 3. A legjobb elérhető zóna egyensúlyt a méretezési megpróbálja vagy horizontális egyenleg megőrzésével. Azonban ha valamilyen okból ez nem lehetséges (például egy zóna leáll, így a méretezési készlet nem hozható létre egy új virtuális Gépet, hogy a zónában), majd a méretezési készlet lehetővé teszi a ideiglenes unbalance ahhoz, hogy a bejövő vagy kimenő sikeresen méretezni. Az ezt követő kísérletek bővített az a méretezési virtuális gépek hozzáadása további virtuális gépek kell a méretezési készletben egyenletesen eloszlik a zónákhoz. Ehhez hasonlóan skálán ezt követő kísérlet alatt, a méretezési eltávolítása virtuális gépek kevesebb virtuális gépek kell a méretezési készletben egyenletesen eloszlik a zónákhoz. "Szigorú zóna balance", a, másrészt a méretezési méretezése a bejövő vagy kimenő, ha ezzel kiegyensúlyozatlanság jellemzi okozna tett bármilyen kísérlet meghiúsul.

Legjobb elérhető zóna egyensúlyt, állítsa hamis (az alapértelmezett API verziója 2017-12-01) "zoneBalance". Szigorú zóna egyenleg, állítsa igaz "zoneBalance".

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
Zónaredundáns méretezési létrehozásához állítsa be, használhatja a *szabványos* SKU nyilvános IP cím és a terheléselosztó. A továbbfejlesztett redundancia érdekében a *szabványos* SKU hoz létre a zónaredundáns hálózati erőforrásokat. További információkért lásd: [Azure Load Balancer szabványos áttekintése](../load-balancer/load-balancer-standard-overview.md). 

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
Zónaredundáns méretezési létrehozásához állítsa be, használhatja a *szabványos* SKU nyilvános IP cím és a terheléselosztó. A továbbfejlesztett redundancia érdekében a *szabványos* SKU hoz létre a zónaredundáns hálózati erőforrásokat. További információkért lásd: [Azure Load Balancer szabványos áttekintése](../load-balancer/load-balancer-standard-overview.md).

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

A single-zóna skála teljes például és hálózati erőforrásokat, olvassa el [minta Resource Manager sablon](https://github.com/Azure/vm-scale-sets/blob/master/zones/singlezone.json)

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

Zónaredundáns méretezési kész példát és hálózati erőforrásokat, olvassa el [minta Resource Manager sablon](https://github.com/Azure/vm-scale-sets/blob/master/zones/multizone.json)


## <a name="next-steps"></a>További lépések
Most, hogy létrehozott egy rendelkezésre állási zóna terjedő skálán, áttekintheti, hogyan [központi telepítése az alkalmazások telepítése virtuális gépre méretezési készletek](virtual-machine-scale-sets-deploy-app.md) vagy [automatikus skálázás használata a virtuálisgép-méretezési csoportok](virtual-machine-scale-sets-autoscale-overview.md).
