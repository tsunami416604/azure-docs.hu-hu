---
title: Azure spot virtuális gépek üzembe helyezése a CLI használatával (előzetes verzió)
description: Megtudhatja, hogyan használhatja a CLI-t az Azure spot virtuális gépek üzembe helyezéséhez a költségek megtakarítása érdekében.
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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163092"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>Előzetes verzió: helyszíni virtuális gépek üzembe helyezése az Azure CLI-vel

Az [Azure helyszíni virtuális gépek](spot-vms.md) használata lehetővé teszi, hogy a felhasználatlan kapacitást jelentős költségmegtakarítással használja ki. Az Azure-infrastruktúra minden olyan időpontban kizárja a helyszíni virtuális gépeket, amikor az Azure-nak szüksége van a kapacitásra. Ezért a helyszíni virtuális gépek kiválóan alkalmasak olyan munkaterhelések kezelésére, amelyek kezelhetik a kötegelt feldolgozási feladatokat, a fejlesztési és tesztelési környezeteket, a nagy számítási feladatokat és egyebeket.

A helyszíni virtuális gépek díjszabása a régió és az SKU alapján változó. További információ: virtuális gépek díjszabása [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) és [Windows rendszerekhez](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Lehetősége van arra, hogy a virtuális gép számára óránként fizetendő maximális árat adja meg. A helyszíni virtuális gépek maximális díja az USA dollárban (USD) állítható be, akár 5 tizedesjegyet is igénybe vehet. A `0.98765`érték például egy óránként $0,98765 USD maximális díj. Ha a maximális árat `-1`értékre állítja, a virtuális gép ára nem lesz kizárva. A virtuális gép ára a jelenlegi díj vagy a standard virtuális gép díjszabása, amely soha nem kevesebb, amíg rendelkezésre áll a kapacitás és a kvóta. A maximális ár beállításával kapcsolatos további információkért lásd: [virtuális gépek – díjszabás](spot-vms.md#pricing).

A helyszíni virtuális gép Azure CLI-vel való létrehozásának folyamata ugyanaz, mint a gyors üzembe helyezési [cikkben](/azure/virtual-machines/linux/quick-create-cli). Csak adja hozzá a "--priority spot" paramétert, és adja meg a maximális árat vagy a `-1`.

> [!IMPORTANT]
> A helyszíni példányok jelenleg nyilvános előzetes verzióban érhetők el.
> Ez az előzetes verzió nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>



## <a name="install-azure-cli"></a>Telepítse az Azure CLI-t

A helyszíni virtuális gépek létrehozásához az Azure CLI 2.0.74 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következő parancsot: **az --version**. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

Jelentkezzen be az Azure-ba az [az login](/cli/azure/reference-index#az-login)használatával.

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Direktszínű virtuális gép létrehozása

Ebből a példából megtudhatja, hogyan helyezhet üzembe egy olyan linuxos helyszíni virtuális gépet, amelyet az ár alapján nem lehet kizárni. 

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

A virtuális gép létrehozása után a lekérdezéssel megtekintheti az erőforráscsoport összes virtuális gépe esetében a maximális számlázási árat.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**Következő lépések**

[Azure PowerShell](../windows/spot-powershell.md) vagy [sablon](spot-template.md)használatával is létrehozhat egy direkt virtuális gépet.

Ha hibát tapasztal, tekintse meg a [hibakódokat](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
