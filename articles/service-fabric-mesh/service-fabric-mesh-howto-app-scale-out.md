---
title: Szolgáltatások méretezése egy Azure Service Fabric-háló alkalmazásban |} A Microsoft Docs
description: Ismerje meg, hogyan egymástól függetlenül méretezhető szolgáltatások futó Service Fabric tervezhetők, az Azure CLI használatával az alkalmazáson belül.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: c0350b767b65aee0c4611bb8fa6f635a651d33dc
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076149"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Service Fabric-háló futó alkalmazáson belüli szolgáltatások méretezése

Ez a cikk bemutatja, hogyan az alkalmazáson belül mikroszolgáltatások egymástól függetlenül skálázható. Ebben a példában látható objektumokat alkalmazás áll két mikroszolgáltatások; `web` és `worker`. 

A `web` szolgáltatás egy weblap, amelyen látható a böngészőben háromszögek az ASP.NET Core alkalmazás. A böngészőben megjelenik az egyes példányok esetében egy háromszög a `worker` szolgáltatás. 

A `worker` szolgáltatás háromszögre helyezi az előre meghatározott időközönként a tárhelyen lévő, és elküldi a háromszögre helyét `web` szolgáltatás. DNS használatával oldja meg a címét a `web` szolgáltatás.

## <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric háló parancssori felület beállítása 
Ez a feladat végrehajtásához használhatja az Azure Cloud Shell vagy az Azure parancssori felület helyi telepítése. Az Azure Service Fabric háló parancssori bővítmény modul telepítése a következő [utasításokat](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure-ba, és állítsa be az előfizetés.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot, amelybe az alkalmazás üzembe helyezése. Használjon egy meglévő erőforráscsoportot, és ezt a lépést kihagyhatja. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>Az alkalmazás egy feldolgozói szolgáltatás üzembe helyezése
Az alkalmazás létrehozása a resource csoport használatával a `deployment create` parancsot.

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Az előző parancs üzembe helyezi a Linux használatával [mesh_rp.base.linux.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Ha szeretne Windows-alkalmazás üzembe helyezése, [mesh_rp.base.windows.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Windows-tárolórendszerképeket nagyobb, mint a Linuxos tárolólemezképek és központi telepítése több időt is igénybe vehet.

Néhány perc alatt a parancs a kell visszaadnia:

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Nyissa meg az alkalmazás
Miután sikeresen üzembe helyezte az alkalmazást, a nyilvános IP-cím beszerzése a szolgáltatási végpont, és a egy böngészőben nyissa meg. Az a terület keresztül áthelyezése egy háromszög ekkor megjelenik egy weblap.

A telepítési parancs visszaadja a szolgáltatásvégpont nyilvános IP-címét. A hálózati erőforrás keresése a szolgáltatásvégpont nyilvános IP-címét is lekérdezheti.
 
Ez az alkalmazás a hálózati erőforrás neve nem `visualObjectsNetwork`, beolvassa a következő paranccsal kapcsolatos információkat. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Méretezési csoport `worker` szolgáltatás

Méretezési csoport a `worker` szolgáltatás a következő paranccsal három alkalmazáspéldányra. 

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Az előző parancs üzembe helyezi a Linux használatával [mesh_rp.scaleout.linux.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Ha szeretne Windows-alkalmazás üzembe helyezése, [mesh_rp.scaleout.windows.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Windows-tárolórendszerképeket nagyobb, mint a Linuxos tárolólemezképek és központi telepítése több időt is igénybe vehet.

Miután sikeresen üzembe helyezte az alkalmazást, a böngésző kell jeleníti meg egy weblap az a terület Mozgatott három háromszög.

## <a name="delete-the-resources"></a>Az erőforrások törlése

Erőforrásokkal való takarékoskodáshoz a korlátozott előzetes programjában hozzárendelni, gyakran törölje az erőforrásokat. Ebben a példában kapcsolódó erőforrások törléséhez törölje az erőforráscsoportot, amelyben üzembe lett helyezve.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>További lépések
- A vizuális objektumok mintaalkalmazás megtekintése [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Service Fabric Erőforrásmodell kapcsolatos további információkért lásd: [Service Fabric háló Erőforrásmodell](service-fabric-mesh-service-fabric-resources.md).
- Service Fabric-háló kapcsolatos további információkért olvassa el a [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md).