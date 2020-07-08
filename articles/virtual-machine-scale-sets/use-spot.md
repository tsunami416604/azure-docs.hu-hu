---
title: Azure spot virtuális gépeket használó méretezési csoport létrehozása
description: Megtudhatja, hogyan hozhat létre helyszíni virtuális gépeket használó Azure virtuálisgép-méretezési csoportokat a költségek megtakarítása érdekében.
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: spot
ms.date: 03/25/2020
ms.reviewer: jagaveer
ms.custom: jagaveer
ms.openlocfilehash: 756e0d62927b67d26ae75af90c64facfe9c92d31
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84310563"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>Azure spot virtuális gépek virtuálisgép-méretezési csoportokhoz 

Az Azure spot on Scale sets használatával jelentős költségmegtakarítást érhet el a fel nem használt kapacitás kihasználása érdekében. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni példányokat, amikor az Azure-nak vissza kell használnia a kapacitást. Ezért a helyszíni példányok kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A rendelkezésre álló kapacitás mennyisége a mérettől, a régiótól, a napszaktól és egyebektől függően változhat. Ha a méretezési csoportokban a direktszínes példányokat helyezi üzembe, az Azure csak akkor osztja ki a példányt, ha rendelkezésre áll kapacitás, de ezekhez a példányokhoz nem tartozik SLA. A direktszín-méretezési csoport egyetlen tartalék tartományban van telepítve, és nem biztosít magas rendelkezésre állású garanciákat.


## <a name="pricing"></a>Díjszabás

A helyszíni példányok díjszabása a régió és az SKU alapján változó. További információ: a [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) és a [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)díjszabása. 


A változó díjszabással maximális árat állíthat be az USA dollárban (USD), legfeljebb 5 tizedesjegyet használva. Az érték például a `0.98765` maximális díj $0,98765 USD/óra. Ha a maximális árat állítja be `-1` , a példány árát a rendszer nem fogja kizárni. A példány díja a helyszíni aktuális díj, vagy egy standard példány díjszabása, amely soha nem kevesebb, ha rendelkezésre áll kapacitás és kvóta.

## <a name="eviction-policy"></a>Kizárási szabályzat

A direktszínes méretezési csoportok létrehozásakor beállíthatja a kizárási házirendet a *felszabadításhoz* (alapértelmezett) vagy a *törléshez*. 

A *felszabadított* házirend áthelyezi a kizárt példányokat a leállított, lefoglalt állapotba, ami lehetővé teszi a kizárt példányok újratelepítését. Azonban nem garantálható, hogy a foglalás sikeres lesz. A delefoglalt virtuális gépek a méretezési csoport példánya kvótájának számítanak, és a mögöttes lemezek után díjat számítunk fel. 

Ha szeretné, hogy a direktszín-méretezési csoport példányai törölve legyenek a kizárásakor, beállíthatja a *törlési*szabályzatot. A törlési házirend beállításával új virtuális gépeket hozhat létre a méretezési csoport példányainak száma tulajdonság növelésével. A kizárt virtuális gépeket a rendszer a mögöttes lemezekkel együtt törli, ezért a tárterületért nem számítunk fel díjat. A méretezési csoportok automatikus skálázási funkciójának használatával automatikusan kipróbálhatja és kompenzálhatja a kizárt virtuális gépeket, azonban nem garantálható, hogy a foglalás sikeres lesz. Javasoljuk, hogy csak az automatikus méretezési funkciót használja a direktszínes méretezési csoportokon, ha a törlési szabályzatot törölni szeretné, hogy elkerülje a lemezek költségeit és a kvóták korlátait. 

A felhasználók eldönthetik, hogy a virtuális gép értesítéseit az [Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md)használatával kapják meg. Ez értesíti Önt, ha a virtuális gépek ki vannak zárva, és 30 másodpercen belül befejezi az összes feladatot, és leállítási feladatokat hajt végre a kizárás előtt. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Direktszínű virtuális gépek üzembe helyezése méretezési csoportokban

A helyszíni virtuális gépek méretezési csoportokon történő üzembe helyezéséhez beállíthatja az új *prioritás* jelzőt a *helyszínen*. A méretezési csoport összes virtuális gépe a következőre lesz beállítva:. A helyszíni virtuális gépekkel rendelkező méretezési csoport létrehozásához használja az alábbi módszerek egyikét:
- [Azure Portalra](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-sablonok](#resource-manager-templates)

## <a name="portal"></a>Portál

A helyszíni virtuális gépeket használó méretezési csoport létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-portal.md)részletezett eljárással. Méretezési csoport telepítésekor beállíthatja, hogy a direktszín jelzőt és a kizárási házirendet: ![ hozzon létre egy méretezési készletet a helyszíni virtuális gépekkel.](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

A helyszíni virtuális gépekkel rendelkező méretezési csoport létrehozásának folyamata megegyezik az [első lépéseket ismertető cikkben](quick-create-cli.md)részletezett eljárással. Csak adja hozzá a "--priority spot"-t, és adja hozzá a következőt: `--max-price` . Ebben a példában `-1` a (z) `--max-price` rendszert használjuk, így a példány árát nem lehet kizárni.

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
Csak adja hozzá a "-priority spot" lehetőséget, és adjon meg egy- `-max-price` t a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

A helyszíni virtuális gépeket használó méretezési csoport létrehozásának folyamata megegyezik a [Linux](quick-create-template-linux.md) vagy [Windows rendszerhez](quick-create-template-windows.md)készült első lépéseket ismertető cikkben leírtakkal. 

A helyszíni sablonok üzembe helyezéséhez használja a `"apiVersion": "2019-03-01"` vagy a újabb verziót. Adja hozzá `priority` a `evictionPolicy` és a tulajdonságokat a `billingProfile` `"virtualMachineProfile":` sablon szakaszához: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Ha törölni szeretné a példányt a kizárása után, módosítsa a paramétert a következőre: `evictionPolicy` `Delete` .

## <a name="faq"></a>GYIK

**K:** A létrehozása után a a standard példány megegyeznek?

**A:** Igen, a helyszíni virtuális gépekhez nem biztosítunk SLA-t, és a szolgáltatás bármikor kizárható.


**K:** Mi a teendő, ha kizárja, de továbbra is kapacitásra van szüksége?

**A:** Javasoljuk, hogy a virtuális gépek helyett használjon szabványos virtuális gépeket, ha a kapacitásra azonnal szükség van.


**K:** Hogyan történik a kvóta kezelése a helyszínen?

**A:** A helyszíni példányok és a standard példányok külön kvótával rendelkeznek. A helyszíni kvóta a virtuális gépek és a méretezési csoport példányai között lesz megosztva. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**K:** Igényelhetek további kvótát a helyszínen?

**A:** Igen, elküldheti a kérést, hogy növelje a helyszíni virtuális gépek kvótáját a [normál kvóta-kérési folyamaton](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)keresztül.


**K:** Válthatok-e meglévő méretezési csoportokat a méretezési csoportok számára?

**A:** Nem, a `Spot` jelző beállítása csak a létrehozási időszakban támogatott.


**K:** Ha `low` alacsony prioritású méretezési csoportokhoz használok, érdemes inkább a használatot használni `Spot` ?

**A:** Egyelőre mind a, mind a `low` `Spot` működni fog, de a használatára való áttérést kell kezdenie `Spot` .


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
| Nagyvállalati Szerződés         | Yes                               |
| Használatalapú fizetés                | Yes                               |
| Felhőalapú szolgáltató (CSP) | [Kapcsolatfelvétel a partnerrel](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Előnyök                     | Nem érhető el                     |
| Szponzorált                    | Yes                               |
| Ingyenes próbaverzió                   | Nem érhető el                     |


**K:** Hol tehetek közzé kérdéseket?

**A:** A kérdését a következő címen teheti közzé és címkézheti `azure-spot` : [Q&a](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>További lépések

A díjszabással kapcsolatos részletekért tekintse meg a [virtuálisgép-méretezési csoport díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) .
