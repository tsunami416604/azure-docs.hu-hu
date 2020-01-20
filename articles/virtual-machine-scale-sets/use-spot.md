---
title: Azure spot virtuális gépeket használó méretezési csoport létrehozása (előzetes verzió)
description: Megtudhatja, hogyan hozhat létre helyszíni virtuális gépeket használó Azure virtuálisgép-méretezési csoportokat a költségek megtakarítása érdekében.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: cynthn
ms.openlocfilehash: 4f434afdd02d15f98e005b44f5563847f4c5847d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278205"
---
# <a name="preview-azure-spot-vms-for-virtual-machine-scale-sets"></a>Előzetes verzió: Azure-beli helyszíni virtuális gépek a virtuálisgép-méretezési csoportokhoz 

Az Azure spot on Scale sets használatával jelentős költségmegtakarítást érhet el a fel nem használt kapacitás kihasználása érdekében. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni példányokat, amikor az Azure-nak vissza kell használnia a kapacitást. Ezért a helyszíni példányok kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A rendelkezésre álló kapacitás mennyisége a mérettől, a régiótól, a napszaktól és egyebektől függően változhat. Ha a méretezési csoportokban a direktszínes példányokat helyezi üzembe, az Azure csak akkor osztja ki a példányt, ha rendelkezésre áll kapacitás, de ezekhez a példányokhoz nem tartozik SLA. A direktszín-méretezési csoport egyetlen tartalék tartományban van telepítve, és nem biztosít magas rendelkezésre állású garanciákat.

> [!IMPORTANT]
> A helyszíni példányok jelenleg nyilvános előzetes verzióban érhetők el.
> Ez az előzetes verzió nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> A nyilvános előzetes verzió korai részében a direktszínes példányok fix áron lesznek, így nem kerül sor ár-alapú kizárásra.

## <a name="pricing"></a>Díjszabás

A helyszíni példányok díjszabása a régió és az SKU alapján változó. További információ: a [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) és a [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)díjszabása. 


A változó díjszabással maximális árat állíthat be az USA dollárban (USD), legfeljebb 5 tizedesjegyet használva. A `0.98765`érték például egy óránként $0,98765 USD maximális díj. Ha a maximális árat `-1`értékre állítja, a példány ára alapján nem lesz kizárva. A példány díja a helyszíni aktuális díj, vagy egy standard példány díjszabása, amely soha nem kevesebb, ha rendelkezésre áll kapacitás és kvóta.

## <a name="eviction-policy"></a>Kizárási szabályzat

A direktszínes méretezési csoportok létrehozásakor beállíthatja a kizárási házirendet a *felszabadításhoz* (alapértelmezett) vagy a *törléshez*. 

A *felszabadított* házirend áthelyezi a kizárt példányokat a leállított, lefoglalt állapotba, ami lehetővé teszi a kizárt példányok újratelepítését. Azonban nem garantálható, hogy a foglalás sikeres lesz. A delefoglalt virtuális gépek a méretezési csoport példánya kvótájának számítanak, és a mögöttes lemezek után díjat számítunk fel. 

Ha szeretné, hogy a direktszín-méretezési csoport példányai törölve legyenek a kizárásakor, beállíthatja a *törlési*szabályzatot. A törlési házirend beállításával új virtuális gépeket hozhat létre a méretezési csoport példányainak száma tulajdonság növelésével. A kizárt virtuális gépeket a rendszer a mögöttes lemezekkel együtt törli, ezért a tárterületért nem számítunk fel díjat. A méretezési csoportok automatikus skálázási funkciójának használatával automatikusan kipróbálhatja és kompenzálhatja a kizárt virtuális gépeket, azonban nem garantálható, hogy a foglalás sikeres lesz. Javasoljuk, hogy csak az automatikus méretezési funkciót használja a direktszínes méretezési csoportokon, ha a törlési szabályzatot törölni szeretné, hogy elkerülje a lemezek költségeit és a kvóták korlátait. 

A felhasználók eldönthetik, hogy a virtuális gép értesítéseit az [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md)használatával kapják meg. Ez értesíti Önt, ha a virtuális gépek ki vannak zárva, és 30 másodpercen belül befejezi az összes feladatot, és leállítási feladatokat hajt végre a kizárás előtt. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Direktszínű virtuális gépek üzembe helyezése méretezési csoportokban

A helyszíni virtuális gépek méretezési csoportokon történő üzembe helyezéséhez beállíthatja az új *prioritás* jelzőt a *helyszínen*. A méretezési csoport összes virtuális gépe a következőre lesz beállítva:. A helyszíni virtuális gépekkel rendelkező méretezési csoport létrehozásához használja az alábbi módszerek egyikét:
- [Azure Portal](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager sablonok](#resource-manager-templates)

## <a name="portal"></a>Portál

A helyszíni virtuális gépeket használó méretezési csoport létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-portal.md)részletezett eljárással. Méretezési csoport telepítésekor beállíthatja a direktszín jelölőt és a kizárási házirendet: ![helyszíni virtuális gépekkel rendelkező méretezési csoport létrehozása](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure parancssori felület (CLI)

A helyszíni virtuális gépekkel rendelkező méretezési csoport létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-cli.md)részletezett eljárással. Csak adja hozzá a "--priority spot" helyet, és adja hozzá a `--max-price`. Ebben a példában a `--max-price` `-1` használjuk, így a példány árát a rendszer nem zárja ki.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

A helyszíni virtuális gépekkel rendelkező méretezési csoport létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-powershell.md)részletezett eljárással.
Csak adja hozzá a "-priority spot" lehetőséget, és adjon meg egy `-max-price` a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Erőforrás-kezelői sablonok

A helyszíni virtuális gépeket használó méretezési csoport létrehozásának folyamata megegyezik a [Linux](quick-create-template-linux.md) vagy [Windows rendszerhez](quick-create-template-windows.md)készült első lépéseket ismertető cikkben leírtakkal. Adja hozzá a "priority" tulajdonságot a sablonban található *Microsoft. számítási/virtualMachineScaleSets/virtualMachineProfile* erőforrástípus számára, és adja meg a *spot* értéket. Ügyeljen arra, hogy a *2019-03-01* API vagy újabb verzióját használja. 

A törlési házirend beállításához adja hozzá a "evictionPolicy" paramétert, és állítsa a *törlésre*.

A következő példa létrehoz egy *myScaleSet* nevű Linux-méretezési készletet az *USA nyugati középső*régiójában, amely *törli a kiürítési* csoportba tartozó virtuális gépeket:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2019-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Spot",
       "evictionPolicy": "delete",
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
## <a name="faq"></a>Gyakori kérdések

**K:** A létrehozása után a a standard példány megegyeznek?

**A:** Igen, a helyszíni virtuális gépekhez nem biztosítunk SLA-t, és a szolgáltatás bármikor kizárható.


**K:** Mi a teendő, ha kizárja, de továbbra is kapacitásra van szüksége?

**A:** Javasoljuk, hogy a virtuális gépek helyett használjon szabványos virtuális gépeket, ha a kapacitásra azonnal szükség van.


**K:** Hogyan történik a kvóta kezelése a helyszínen?

**A:** A helyszíni példányok és a standard példányok külön kvótával rendelkeznek. A helyszíni kvóta a virtuális gépek és a méretezési csoport példányai között lesz megosztva. További információk: [Az Azure-előfizetésekre és -szolgáltatásokra vonatkozó korlátozások, kvóták és megkötések](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**K:** Igényelhetek további kvótát a helyszínen?

**A:** Igen, elküldheti a kérést, hogy növelje a helyszíni virtuális gépek kvótáját a [normál kvóta-kérési folyamaton](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)keresztül.


**K:** Válthatok-e meglévő méretezési csoportokat a méretezési csoportok számára?

**A:** Nem, a `Spot` jelző beállítása csak a létrehozási időszakban támogatott.


**K:** Ha alacsony prioritású méretezési csoportokhoz használok `low`, érdemes inkább `Spot` használni?

**A:** Egyelőre a `low` és az `Spot` is működni fog, de a `Spot`használatával kell elkezdeni a váltást.


**K:** Létrehozhatok a normál virtuális gépekkel és a helyszíni virtuális gépekkel is rendelkező méretezési csoportokat?

**A:** Nem, a méretezési csoport legfeljebb egy prioritási típust támogat.


**K:**  Használhatom az autoskálázást a helyszíni méretezési csoportokkal?

**A:** Igen, beállíthatja az automatikus skálázási szabályokat a helyszíni méretezési csoporton. Ha a virtuális gépek ki vannak zárva, az autoscale segítségével új helyszíni virtuális gépeket hozhat létre. Ne feledje, hogy ez a kapacitás azonban nem garantált. 


**K:**  Működik az autoskálázás a kizárási házirendekkel (felszabadítás és törlés)?

**A:** Azt javasoljuk, hogy állítsa be a kizárási házirendet a törléshez az autoscale használatakor. Ennek az az oka, hogy a lefoglalt példányok száma a méretezési csoport kapacitásának száma alapján történik. Az autoscale használatakor a megcélzott példányok száma gyorsan elvégezhető a delefoglalt, kizárt példányok miatt. 


**K:** Milyen csatornák támogatják a helyszíni virtuális gépeket?

**A:** Tekintse meg az alábbi táblázatot a helyszíni virtuális gépek rendelkezésre állásához.

<a name="channel"></a>

| Azure-csatornák               | Azure helyszíni virtuális gépek rendelkezésre állása       |
|------------------------------|-----------------------------------|
| Nagyvállalati szerződés         | Igen                               |
| Használatalapú fizetés                | Igen                               |
| Felhőalapú szolgáltató (CSP) | [Kapcsolatfelvétel a partnerrel](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Előnyök                     | Nincs                     |
| Szponzorált                    | Nincs                     |
| Free Trial (Ingyenes próba)                   | Nincs                     |


**K:** Hol tehetek közzé kérdéseket?

**A:** A kérdéseit közzéteheti és címkézheti `azure-spot` a [Q & a](https://docs.microsoft.com/answers/topics/azure-spot.html)-ben. 

## <a name="next-steps"></a>Következő lépések
Most, hogy helyszíni virtuális gépekkel létrehozott egy méretezési készletet, próbálja meg üzembe helyezni az [automatikus skálázási sablont a Spot használatával](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

A díjszabással kapcsolatos részletekért tekintse meg a [virtuálisgép-méretezési csoport díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) .
