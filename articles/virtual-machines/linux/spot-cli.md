---
title: Az Azure Spot virtuális gépek üzembe helyezéséhez használja a CLI-t
description: Ismerje meg, hogyan használhatja a CLI-t az Azure Spot virtuális gépek üzembe helyezéséhez a költségek csökkentése érdekében.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 3f341271c208cc56a704c836433c33af0129a4ac
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758362"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Azonnali virtuális gépek telepítése az Azure CLI használatával

Az [Azure Spot virtuális gépek](spot-vms.md) használatával jelentős költségmegtakarítást eredményezhet a kihasználatlan kapacitás kihasználása. Bármikor, amikor az Azure-nak szüksége van a kapacitás vissza, az Azure-infrastruktúra kilakoltatja spot virtuális gépek. Ezért a direkt virtuális gépek kiválóan szolgálnak olyan számítási feladatokhoz, amelyek kezelni tudják a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási számítási feladatokat és egyebeket.

A direkt virtuális gépek díjszabása változó, a régió és a termékváltozat alapján. További információ: VM-díjszabás [Linuxra](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windowsra.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 

Lehetősége van arra, hogy állítsa be a maximális árat, amelyet hajlandó fizetni, óránként, a virtuális gép. A direktvirtuális gép maximális ára usa dollárban (USD) állítható be, legfeljebb 5 tizedesjegy használatával. Az érték `0.98765`például óránként $0,98765 USD max ár. Ha a maximális árat `-1`állítja be, a virtuális gép nem lesz kizárva az ár alapján. A virtuális gép ára a Direktség aktuális ára vagy egy szabványos virtuális gép ára lesz, amely valaha is kevesebb, mindaddig, amíg van kapacitás és kvóta. A maximális ár beállításáról további információt a Azonnali virtuális gépek – árképzés című témakörben [talál.](spot-vms.md#pricing)

Az Azure CLI használatával a Direktussal létrehozott virtuális gép létrehozása megegyezik a [rövid útmutató cikkben részletezett eljárással.](/azure/virtual-machines/linux/quick-create-cli) Csak adja hozzá a "--priority Spot" `-1`paramétert, és adjon meg egy max árat vagy .


## <a name="install-azure-cli"></a>Telepítse az Azure CLI-t

Direktgépek létrehozásához az Azure CLI 2.0.74-es vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következő parancsot: **az --version**. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

Jelentkezzen be az Azure-ba [az az bejelentkezéssel.](/cli/azure/reference-index#az-login)

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Azonnali virtuális gép létrehozása

Ebben a példában bemutatja, hogyan telepíthet egy Linux spot virtuális gép, amely nem lesz kilakoltatva az ár alapján. 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

A virtuális gép létrehozása után lekérdezheti az erőforráscsoport összes virtuális gépének maximális számlázási árát.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**További lépések**

Azonnali virtuális gép is létrehozhat Azure [PowerShell](../windows/spot-powershell.md) vagy [sablon](spot-template.md)használatával.

Ha hibát észlel, olvassa el a [Hibakódok című témakört.](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
