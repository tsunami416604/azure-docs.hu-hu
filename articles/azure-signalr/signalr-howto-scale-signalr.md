---
title: Az Azure SignalR-szolgáltatás egy példányának méretezése
description: Ismerje meg, hogyan skálázhat egy Azure SignalR-szolgáltatáspéldányt a kapacitás hozzáadásához vagy csökkentéséhez az Azure Portalon vagy az Azure CLI-n keresztül.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659287"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Hogyan skálázható egy Azure SignalR-szolgáltatáspéldány?
Ez a cikk bemutatja, hogyan skálázhatja az Azure SignalR-szolgáltatás példányát. Két forgatókönyv van a skálázás, a horizontális felskálázás és a horizontális felskálázás.

* [Felskálázás:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Több egység, kapcsolat, üzenet és egyebek. A tarifacsomag ingyenesről standardra történő módosításával skálázható.
* [Horizontális felskálázás](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Növelje a SignalR egységek számát. Akár 100 egységre is méretezhető.

A méretezési beállítások alkalmazása néhány percet vesz igénybe. Ritka esetekben, ez eltarthat körülbelül 30 perc alatt alkalmazni. Nem követelik meg a kód módosítását vagy a kiszolgálóalkalmazás újratelepítését.

Az egyes SignalR-szolgáltatások díjszabásáról és kapacitásairól az [Azure SignalR service díjszabásának részletei című témakörben talál további információt.](https://azure.microsoft.com/pricing/details/signalr-service/)  

> [!NOTE]
> Ha a SignalR szolgáltatást **ingyenes** szintről **standard** szintre vagy fordítva módosítja, a közszolgálati IP-cím megváltozik, és általában 30–60 percet vesz igénybe a módosítás terjesztése a DNS-kiszolgálókon a teljes interneten. Előfordulhat, hogy a szolgáltatás nem érhető el a DNS frissítése előtt. Általában nem ajánlott túl gyakran módosítani a tarifacsomagot.


## <a name="scale-on-azure-portal"></a>Méretezés az Azure Portalon

1. Nyissa meg az [Azure Portalt](https://portal.azure.com) a böngészőjében.

2. A SignalR Szolgáltatás lapon a bal oldali menüben válassza a **Méretezés**lehetőséget.
   
3. Válassza ki a tarifacsomagot, majd kattintson a **Kijelölés gombra.** Állítsa be a **standard** szint egységszámát.
   
    ![Méretezés a portálon](./media/signalr-howto-scale/signalr-howto-scale.png)

4. Kattintson a **Mentés** gombra.

## <a name="scale-using-azure-cli"></a>Méretezés az Azure CLI használatával

Ez a parancsfájl létrehoz egy új SignalR Service erőforrás **t és** egy új erőforráscsoportot, és standard **szintre** méretezi. 

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

## <a name="compare-pricing-tiers"></a>Tarifacsomagok összehasonlítása

Részletes információkért, például az egyes tarifacsomagokhoz tartozó üzenetekről és kapcsolatokról a [SignalR service pricing details című témakörben talál](https://azure.microsoft.com/pricing/details/signalr-service/)részletes információt.

Az egyes rétegek szolgáltatáskorlátait, kvótáit és korlátait táblázatban a [SignalR szolgáltatás korlátok .](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits)

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan skálázhatja az egyes SignalR-szolgáltatáspéldányokat.

Több végpont is támogatott méretezés, horizontális és régiók közötti forgatókönyvek.

> [!div class="nextstepaction"]
> [a SignalR szolgáltatás méretezése több példánysal](./signalr-howto-scale-multi-instances.md)
