---
title: Az Azure Signaler szolgáltatás egy példányának méretezése
description: Megtudhatja, hogyan méretezhető az Azure Signaler szolgáltatás példánya kapacitás hozzáadásához vagy csökkentéséhez Azure Portal vagy az Azure CLI-n keresztül.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: zhshang
ms.openlocfilehash: 0c4f91ee9cea5e8b13ecfedafffdc1715fc242c2
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464177"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Azure Signaler Service-példány skálázása
Ez a cikk bemutatja, hogyan méretezhető az Azure Signaler szolgáltatás példánya. Két forgatókönyv áll rendelkezésre a méretezéshez, a vertikális felskálázáshoz és a horizontális felskálázáshoz.

* Vertikális [felskálázás](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): több egység, kapcsolat, üzenet és egyebek is elérhetők. Vertikális felskálázást az árképzési szint Ingyenesről standard szintűre való módosításával végezheti el.
* Vertikális [felskálázás](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): növelje a jelző egységek számát. Akár 100 egységre is kibővíthető.

A skálázási beállítások alkalmazása eltarthat néhány percig. Nem igénylik a kód módosítását vagy a kiszolgálóalkalmazás újbóli üzembe helyezését.

További információ az egyéni szignáló szolgáltatás díjszabásáról és kapacitásáról: az [Azure signaler szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/signalr-service/).  

> [!NOTE]
> A Signaler szolgáltatás **ingyenes** szintjéről **standard** csomagra vált, vagy fordítva, a nyilvános szolgáltatás IP-címe módosul, és általában 3-60 percet vesz igénybe, hogy a teljes interneten keresztül propagálja a DNS-kiszolgálókra való változást. Előfordulhat, hogy a szolgáltatás nem érhető el, mielőtt frissül a DNS. Általában nem ajánlott túl gyakran módosítani az árképzési szintet.


## <a name="scale-on-azure-portal"></a>Méretezés Azure Portal

1. Nyissa meg az [Azure Portalt](https://portal.azure.com) a böngészőjében.

2. A Signaler szolgáltatás lapjának bal oldali menüjében válassza a **skála**lehetőséget.
   
3. Válassza ki az árképzési szintet, majd kattintson a **kiválasztás**elemre. A **standard** szinthez be kell állítania az egységek darabszámát.
   
    ![Méretezés a portálon](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Kattintson a **Save** (Mentés) gombra.

## <a name="scale-using-azure-cli"></a>Méretezés az Azure CLI használatával

Ez a szkript létrehoz egy új, az **ingyenes** szint és egy új erőforráscsoport, valamint a **standard** szinthez való skálázást jelző szolgáltatás-erőforrást. 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

Jegyezze fel az új erőforráscsoporthoz létrehozott tényleges nevet. Ezt az erőforráscsoport-nevet fogja használni, amikor törölni szeretné a csoport összes erőforrását.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>Árképzési szintek összehasonlítása

Részletes információkat, például az összes díjszabási csomaghoz tartozó üzeneteket és kapcsolatokat lásd: a [signaler szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/signalr-service/).

Az egyes szintek szolgáltatási korlátainak, kvótáinak és megkötéseinek táblázatát lásd: a [signaler szolgáltatás korlátai](../azure-subscription-service-limits.md#azure-signalr-service-limits).

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megismerte, hogyan méretezhető az egyetlen szignáló szolgáltatás példánya.

Több végpont is támogatott a méretezés, a horizontális skálázás és a régiók közötti környezetekben.

> [!div class="nextstepaction"]
> [a Signaler szolgáltatás méretezése több példánnyal](./signalr-howto-scale-multi-instances.md)
