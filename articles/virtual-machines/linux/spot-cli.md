---
title: Az Azure Spot virtuális gépek üzembe helyezéséhez használja a CLI-t (előzetes verzió)
description: Ismerje meg, hogyan használhatja a CLI-t az Azure Spot virtuális gépek üzembe helyezéséhez a költségek csökkentése érdekében.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 110e935671ab1d640b2ff3dc26c203b262e999fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77163092"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Előzetes verzió: Azonnali virtuális gépek üzembe helyezése az Azure CLI használatával

Az [Azure Spot virtuális gépek](spot-vms.md) használatával jelentős költségmegtakarítást eredményezhet a kihasználatlan kapacitás kihasználása. Bármikor, amikor az Azure-nak szüksége van a kapacitás vissza, az Azure-infrastruktúra kilakoltatja spot virtuális gépek. Ezért a direkt virtuális gépek kiválóan szolgálnak olyan számítási feladatokhoz, amelyek kezelni tudják a megszakításokat, például a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási számítási feladatokat és egyebeket.

A direkt virtuális gépek díjszabása változó, a régió és a termékváltozat alapján. További információ: VM-díjszabás [Linuxra](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windowsra.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 

Lehetősége van arra, hogy állítsa be a maximális árat, amelyet hajlandó fizetni, óránként, a virtuális gép. A direktvirtuális gép maximális ára usa dollárban (USD) állítható be, legfeljebb 5 tizedesjegy használatával. Az érték `0.98765`például óránként $0,98765 USD max ár. Ha a maximális árat `-1`állítja be, a virtuális gép nem lesz kizárva az ár alapján. A virtuális gép ára a Direktség aktuális ára vagy egy szabványos virtuális gép ára lesz, amely valaha is kevesebb, mindaddig, amíg van kapacitás és kvóta. A maximális ár beállításáról további információt a Azonnali virtuális gépek – árképzés című témakörben [talál.](spot-vms.md#pricing)

Az Azure CLI használatával a Direktussal létrehozott virtuális gép létrehozása megegyezik a [rövid útmutató cikkben részletezett eljárással.](/azure/virtual-machines/linux/quick-create-cli) Csak adja hozzá a "--priority Spot" `-1`paramétert, és adjon meg egy max árat vagy .

> [!IMPORTANT]
> A direktpéldányok jelenleg nyilvános előzetes verzióban vannak.
> Ez az előzetes verzió éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>



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
