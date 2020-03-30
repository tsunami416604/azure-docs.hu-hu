---
title: Azure Spot virtuális gépeket használó méretezési csoport létrehozása (előzetes verzió)
description: Ismerje meg, hogyan hozhat létre Azure virtuálisgép-méretezési készleteket, amelyek azonnali virtuális gépek et használnak a költségek megtakarításához.
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 37e914fe6bafe9587be525faf3e01c897cdd8230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162684"
---
# <a name="preview-azure-spot-vms-for-virtual-machine-scale-sets"></a>Előzetes verzió: Azure Spot virtuális gépek virtuális gépméret-készletekhez 

Az Azure Spot méretezési csoportokon történő használatával jelentős költségmegtakarítást eredményezhet a kihasználatlan kapacitás kihasználása. Bármikor, amikor az Azure-nak szüksége van a kapacitás vissza, az Azure-infrastruktúra kilakoltatja spot példányok. Ezért a direkt példányok nagyszerűek olyan számítási feladatokhoz, amelyek kezelni tudják a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési/tesztelési környezeteket, a nagy számítási számítási feladatokat és egyebeket.

A rendelkezésre álló kapacitás mennyisége a mérettől, a régiótól, a napszaktól és egyebektől függően változhat. Direktszínpéldányok méretezési csoportokon történő üzembe helyezésekor az Azure csak akkor osztja le a példányt, ha van kapacitás, de ezekhez a példányokhoz nincs SLA. A direkt méretezési csoport egyetlen tartalék tartományban van telepítve, és nem nyújt magas rendelkezésre állási garanciát.

> [!IMPORTANT]
> A direktpéldányok jelenleg nyilvános előzetes verzióban vannak.
> Ez az előzetes verzió éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>

## <a name="pricing"></a>Díjszabás

A direktszínpéldányok díjszabása változó, a régió és a termékváltozat alapján. További információ: Árak [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) és [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


A változó árképzés, akkor lehetősége van arra, hogy állítsa be a maximális ár, USA dollárban (USD), akár 5 tizedesjegy. Az érték `0.98765`például óránként $0,98765 USD max ár. Ha a maximális árat `-1`állítja be, a példány nem lesz kizárva az ár alapján. A példány ára a Spot aktuális ára vagy egy szabványos példány ára lesz, amely valaha is kevesebb, mindaddig, amíg van kapacitás és kvóta.

## <a name="eviction-policy"></a>Kilakoltatási politika

Direktszínméretezési csoportok létrehozásakor a kilakoltatási házirendet a *Felszabadítás* (alapértelmezett) vagy a Törlés értékre *állíthatja.* 

A *Felszabadítási* házirend a kilakoltatott példányokat a leállított felszabadított állapotba helyezi, így újratelepítheti a kilakoltatott példányokat. Azonban nincs garancia arra, hogy az elosztás sikeres lesz. A felszabadított virtuális gépek beleszámítanak a méretezési csoport példánykvótájába, és az alapul szolgáló lemezekért díjat számítunk fel. 

Ha azt szeretné, hogy a direktméretezési csoport példányai törlődjenek a kilakoltatáskor, beállíthatja, hogy a kilakoltatási házirend *törölje.* A kilakoltatási házirend törlésre van állítva, új virtuális gépeket hozhat létre a méretezési csoport példányszám tulajdonságának növelésével. A kizárt virtuális gépek törlődnek az alapul szolgáló lemezekkel együtt, ezért a tárolásért nem kell fizetnie. A méretezési csoportok automatikus méretezési szolgáltatásával automatikusan megpróbálhatja és kompenzálhatja a kizárt virtuális gépeket, de nincs garancia arra, hogy a lefoglalás sikeres lesz. Javasoljuk, hogy csak akkor használja az automatikus méretezési funkciót direkt méretezési csoportokban, ha a kilakoltatási házirendet törlésre állítja, hogy elkerülje a lemezek költségét, és a kvótakorlátokat. 

A felhasználók engedélyezhetik, hogy az [Azure-ban megadott eseményeken](../virtual-machines/linux/scheduled-events.md)keresztül fogadjanak a virtuális gépen keresztüli értesítéseket. Ez értesíti, ha a virtuális gépek kivannak zárva, és 30 másodpercáll majd a feladatok befejezésére és a leállítási feladatok végrehajtására a kilakoltatás előtt. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Azonnali virtuális gépek üzembe helyezése méretezési csoportokban

Ha a virtuális gépeket méretezési csoportokra szeretné telepíteni, beállíthatja az új *prioritásjelzőt* *direkt.* A méretezési készletben lévő összes virtuális gép direkt lesz beállítva. Ha méretezési csoportot szeretne létrehozni a direkt virtuális gépeken, használja az alábbi módszerek egyikét:
- [Azure-portál](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager-sablonok](#resource-manager-templates)

## <a name="portal"></a>Portál

A direkt virtuális gépeket használó méretezési csoport létrehozásának folyamata megegyezik az [első lépések ről szóló cikkben részletezett eljárással.](quick-create-portal.md) Méretezési csoport telepítésekor beállíthatja a Direktszínjelzőt és a kilakoltatási házirendet: ![Méretezési csoport létrehozása direktvirtuális gépekkel](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

A direktvirtuális gépekkel rendelkező méretezési csoport létrehozásának folyamata megegyezik az [első lépések ről szóló cikkben részletezett.](quick-create-cli.md) Csak adja hozzá a "--Priority `--max-price`Spot", és adjunk hozzá . Ebben a példában `-1` `--max-price` használjuk, így a példány nem lesz kilakoltatva az ár alapján.

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

A direktvirtuális gépekkel rendelkező méretezési csoport létrehozásának folyamata megegyezik az [első lépések ről szóló cikkben részletezett.](quick-create-powershell.md)
Csak add "-Priority Spot", `-max-price` és a kínálat a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

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

A direkt virtuális gépeket használó méretezési csoport létrehozásának folyamata megegyezik a [Linux](quick-create-template-linux.md) vagy [Windows](quick-create-template-windows.md)első lépésekről szóló cikkében részletezett részletes. 

Direktszínsablon-telepítések esetén használja`"apiVersion": "2019-03-01"` vagy később. Adja `priority`hozzá `evictionPolicy` `billingProfile` a és `"virtualMachineProfile":` a tulajdonságokat a sablon szakaszához: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

Ha a példány takarása után `evictionPolicy` törölni `Delete`szeretné, módosítsa a paramétert a-ra.

## <a name="faq"></a>GYIK

**K:** Miután létrehozta, egy direkt példány ugyanaz, mint a szabványos példány?

**A.** Igen, kivéve, hogy nincs SLA a direkt virtuális gépekhez, és bármikor kilakoltathatók.


**K:** Mi a teendő, ha kilakoltatnak, de még mindig kapacitásra van szüksége?

**A.** Azt javasoljuk, hogy a direkt virtuális gépek helyett szabványos virtuális gépeket használjon, ha azonnal kapacitásra van szüksége.


**K:** Hogyan kezeli a kvóta a Spot?

**A.** A direktpéldányok és a szabványos példányok külön kvótakészletekkel rendelkeznek. A direkthelykvóta meg lesz osztva a virtuális gépek és a méretezési csoport példányai között. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**K:** Kérhetek további kvótát a Spot-ra?

**A.** Igen, a [szabványos kvótakérelem-folyamaton](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)keresztül elküldheti a direkt virtuális gépekre vonatkozó kvóta növelésére irányuló kérelmet.


**K:** Konvertálhatok meglévő léptékkészleteket direktméretezési csoportokká?

**A.** Nem, a `Spot` jelző beállítása csak létrehozáskor támogatott.


**K:** Ha alacsony `low` prioritású méretezési csoportokhoz használtam, `Spot` helyette el kell kezdenem használni?

**A.** Most mindkettő, `low` `Spot` és működni fog, de meg `Spot`kell kezdeni áttérés segítségével .


**K:** Létrehozhatok egy méretezési csoportot a normál virtuális gépek és a direkt virtuális gépek segítségével is?

**A.** Nem, a méretezési csoport nem támogathat egynél több prioritástípust.


**K:**  Használhatom az automatikus skálázást direktszín méretezési készletekkel?

**A.** Igen, beállíthatja az automatikus skálázási szabályokat a direkt méretezési készletén. Ha a virtuális gépek kivannak zárva, az automatikus skálázás megpróbálhat új direkt virtuális gépeket létrehozni. Ne feledje, akkor nem garantált ez a kapacitás mégis. 


**K:**  Az automatikus skálázás működik a kilakoltatási házirendek (felszabadítása és törlése)?

**A.** Javasoljuk, hogy állítsa be a kilakoltatási házirend törlését automatikus skálázás használata esetén. Ennek az az oka, hogy a felszabadított példányok beleszámítanak a méretezési csoport kapacitásszámába. Automatikus skálázás használatakor valószínűleg gyorsan eléri a célpéldányok számát a felszabadított, kilakoltatott példányok miatt. 


**K:** Milyen csatornák támogatják a spot virtuális gépeket?

**A.** Tekintse meg az alábbi táblázatot a virtuális gépek rendelkezésre állásáról.

<a name="channel"></a>

| Azure-csatornák               | Az Azure spot virtuális gépek elérhetősége       |
|------------------------------|-----------------------------------|
| Nagyvállalati Szerződés         | Igen                               |
| Használatalapú fizetés                | Igen                               |
| Felhőszolgáltató (CSP) | [Vegye fel a kapcsolatot partnerével](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Előnyök                     | Nem érhető el                     |
| Szponzorált                    | Nem érhető el                     |
| Ingyenes próbaverzió                   | Nem érhető el                     |


**K:** Hol tehetek fel kérdéseket?

**A.** Tudod felad és tag `azure-spot` a kérdést a [Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>További lépések
Most, hogy létrehozott egy méretezési készletet a direkt virtuális gépek, próbálja meg telepíteni [az automatikus méretezési sablon segítségével Spot.](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri)

Tekintse meg a [virtuális gép méretezési beállítás árképzési oldalon](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) az árképzésrészleteit.
