---
title: Szolgáltatások méretezése egy Azure Service Fabric-háló alkalmazásban |} A Microsoft Docs
description: Ismerje meg, hogyan egymástól függetlenül méretezhető szolgáltatások futó Service Fabric tervezhetők, az Azure CLI használatával az alkalmazáson belül.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/08/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: e68bcd135c33c7fd83908b8fed0fd098a698fd36
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054067"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Service Fabric-háló futó alkalmazáson belüli szolgáltatások méretezése

Ez a cikk bemutatja, hogyan az alkalmazáson belül mikroszolgáltatások egymástól függetlenül skálázható. Ebben a példában a vizuális objektumok alkalmazás áll két mikroszolgáltatások: `web` és `worker`.

A `web` szolgáltatás egy weblap, amelyen látható a böngészőben háromszögek az ASP.NET Core alkalmazás. A böngészőben megjelenik az egyes példányok esetében egy háromszög a `worker` szolgáltatás.

A `worker` szolgáltatás háromszögre helyezi az előre meghatározott időközönként a tárhelyen lévő, és elküldi a háromszögre helyét `web` szolgáltatás. DNS használatával oldja meg a címét a `web` szolgáltatás.

## <a name="set-up-service-fabric-mesh-cli"></a>A Service Fabric Mesh parancssori felületének beállítása

Ez a feladat végrehajtásához használhatja az Azure Cloud Shell vagy az Azure parancssori felület helyi telepítése. Az Azure Service Fabric Mesh CLI-bővítmény moduljának telepítéséhez kövesse ezeket az [útmutatásokat](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba, és állítsa be az előfizetését.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot, amelyben az alkalmazást üzembe helyezheti.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>Az alkalmazás egy feldolgozói szolgáltatás üzembe helyezése

Az alkalmazás létrehozása a resource csoport használatával a `deployment create` parancsot.

Az alábbi példa helyez üzembe egy Linux alkalmazás használja a [mesh_rp.base.linux.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Windows-alkalmazás üzembe helyezése, használja a [[mesh_rp.base.windows.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). A Windows-tárolórendszerképek nagyobbak, mint a Linux-tárolórendszerképek, ezért több ideig tarthat az üzembe helyezésük.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Néhány perc alatt a parancs a kell visszaadnia:

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Az alkalmazás megnyitása

A telepítési parancs visszaadja a szolgáltatásvégpont nyilvános IP-címét. Miután sikeresen üzembe helyezte az alkalmazást, a nyilvános IP-cím beszerzése a szolgáltatási végpont, és a egy böngészőben nyissa meg. Egy mozgó háromszög weblap jelenik meg.

Ez az alkalmazás a hálózati erőforrás neve nem `visualObjectsNetwork`. Az alkalmazás például annak leírása, tartózkodási hely, erőforráscsoport, stb. További információ a következő paranccsal tekintheti meg:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Méretezési csoport `worker` szolgáltatás

Méretezési csoport a `worker` szolgáltatás a következő paranccsal három alkalmazáspéldányra. Az alábbi példa helyez üzembe egy Linux alkalmazás használja a [mesh_rp.scaleout.linux.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Windows-alkalmazás üzembe helyezése, használja a [mesh_rp.scaleout.windows.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Vegye figyelembe, hogy nagyobb tárolórendszerképek hosszabb időt vehet igénybe üzembe helyezéséhez.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```

Miután sikeresen üzembe helyezte az alkalmazást, a böngésző három mozgó háromszög weblap kell megjelenítenie.

## <a name="delete-the-resources"></a>Az erőforrások törlése

Gyakran törölje az erőforrásokat, amelyek nem használják az Azure-ban. Ebben a példában a kapcsolódó erőforrások törléséhez törölje az erőforráscsoportot, amelyben üzembe lett helyezve, (Ez töröl Mindent az erőforráscsoporthoz társított) a következő paranccsal:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>További lépések

- A vizuális objektumok mintaalkalmazás megtekintése [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Service Fabric Erőforrásmodell kapcsolatos további információkért lásd: [Service Fabric háló Erőforrásmodell](service-fabric-mesh-service-fabric-resources.md).
- Service Fabric-háló kapcsolatos további információkért olvassa el a [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md).