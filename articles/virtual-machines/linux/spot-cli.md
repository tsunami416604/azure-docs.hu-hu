---
title: Azure helyszíni virtuális gépek üzembe helyezése a CLI használatával
description: Megtudhatja, hogyan használhatja a CLI-t az Azure spot virtuális gépek üzembe helyezéséhez a költségek megtakarítása érdekében.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 165a2c6221c8d3c14f71134deef962d0859bb438
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816711"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Helyszíni virtuális gépek üzembe helyezése az Azure CLI használatával

Az [Azure helyszíni virtuális gépek](spot-vms.md) használata lehetővé teszi, hogy a felhasználatlan kapacitást jelentős költségmegtakarítással használja ki. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni virtuális gépeket, amikor az Azure-nak szüksége van a kapacitásra. Ezért a helyszíni virtuális gépek kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A helyszíni virtuális gépek díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Lehetősége van arra, hogy a virtuális gép számára óránként fizetendő maximális árat adja meg. A helyszíni virtuális gépek maximális díja az USA dollárban (USD) állítható be, akár 5 tizedesjegyet is igénybe vehet. Az érték például a `0.98765` maximális díj $0,98765 USD/óra. Ha a maximális árat állítja be `-1` , a virtuális gép ára nem kerül kizárásra. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta. A maximális ár beállításával kapcsolatos további információkért lásd: [virtuális gépek – díjszabás](../spot-vms.md#pricing).

A helyszíni virtuális gép Azure CLI-vel való létrehozásának folyamata ugyanaz, mint a gyors üzembe helyezési [cikkben](./quick-create-cli.md). Csak adja hozzá a "--priority spot" paramétert, állítsa be a `--eviction-policy` (z) felszabadítását (ez az alapértelmezett érték) `Delete` , és adja meg a maximális árat vagy a értéket `-1` . 


## <a name="install-azure-cli"></a>Telepítse az Azure CLI-t

A helyszíni virtuális gépek létrehozásához az Azure CLI 2.0.74 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következő parancsot: **az --version**. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

Jelentkezzen be az Azure-ba az [az login](/cli/azure/reference-index#az-login)használatával.

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Direktszínű virtuális gép létrehozása

Ebből a példából megtudhatja, hogyan helyezhet üzembe egy olyan linuxos helyszíni virtuális gépet, amelyet az ár alapján nem lehet kizárni. A kizárási szabályzat úgy van beállítva, hogy felszabadítsa a virtuális gépet, hogy később újra lehessen indítani. Ha törölni szeretné a virtuális gépet és a mögöttes lemezt a virtuális gép kizárásakor, állítsa a következőre: `--eviction-policy` `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



A virtuális gép létrehozása után a lekérdezéssel megtekintheti az erőforráscsoport összes virtuális gépe esetében a maximális számlázási árat.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Következő lépések**

[Azure PowerShell](../windows/spot-powershell.md), [portál](../windows/spot-portal.md)vagy [sablon](spot-template.md)használatával is létrehozhat egy helyszíni virtuális gépet.

Ha hibát tapasztal, tekintse meg a [hibakódokat](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
