---
title: Az alkalmazás üzembe helyezése az Azure Service Fabric háló-sablonnal |} A Microsoft Docs
description: 'Útmutató: .NET Core-alkalmazás üzembe helyezése Service Fabric-háló egy sablonból az Azure CLI használatával.'
services: service-fabric-mesh
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
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 356e8019f9a4a64cb1c1c113d0f44990aa4e0f95
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054476"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>Service Fabric-háló-alkalmazás üzembe helyezése Service Fabric háló-sablon használatával
Ez a cikk bemutatja a .NET Core-alkalmazás Service Fabric-háló üzembe helyezése sablon használatával. Ha elkészült, rendelkezni kezelőfelületes szavazóalkalmazással, egy ASP.NET Core webes kezelőfelületes, amely a fürt egy háttérszolgáltatás menti a szavazati adatokat. Az előtér-DNS használatával oldja fel a címet a háttér-szolgáltatás.

## <a name="set-up-service-fabric-mesh-cli"></a>A Service Fabric Mesh parancssori felületének beállítása 
Ez a feladat végrehajtásához használhatja az Azure Cloud Shell vagy az Azure parancssori felület helyi telepítése. Az Azure Service Fabric Mesh CLI-bővítmény moduljának telepítéséhez kövesse ezeket az [útmutatásokat](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure-ba, és állítsa be az előfizetését.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot, amelyben az alkalmazást üzembe helyezheti. Egy meglévő erőforráscsoportot is használhat, és úgy kihagyhatja ezt a lépést. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése
Az alkalmazás létrehozása a resource csoport használatával a `deployment create` parancsot.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Az előző parancs telepíti a Windows alkalmazás használatával [mesh_rp.windows.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json). Ha szeretne egy Linux-alkalmazás üzembe helyezése, [mesh_rp.linux.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json). A Windows-tárolórendszerképek nagyobbak, mint a Linux-tárolórendszerképek, ezért több ideig tarthat az üzembe helyezésük.

Néhány perc alatt a parancs a kell visszaadnia:

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>Az alkalmazás megnyitása
Miután sikeresen üzembe helyezte az alkalmazást, a nyilvános IP-cím beszerzése a szolgáltatási végpont, és a egy böngészőben nyissa meg. Megjeleníti a következő weblapon. 

![Szavazóalkalmazás](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

Most szavazati lehetőséget hozzáadni az alkalmazáshoz, és szavazzon rajta vagy törlése a szavazati lehetőséget.

A telepítési parancs visszaadja a szolgáltatásvégpont nyilvános IP-címét. Igény szerint is lekérdezheti a hálózati erőforrás keresése a szolgáltatásvégpont nyilvános IP-címét. 

Ez az alkalmazás a hálózati erőforrás neve nem `VotingAppNetwork`, beolvassa a következő paranccsal kapcsolatos információkat. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>Az alkalmazás részleteinek ellenőrzése
Az alkalmazás állapotát az `app show` paranccsal ellenőrizheti. Az alkalmazás neve az üzembe helyezett alkalmazás "VotingApp", így beolvasni a részletek. 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>Az üzembe helyezett alkalmazások listázása
Az „app list” paranccsal lekérheti az előfizetéséhez üzembe helyezett alkalmazások listáját. 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az alkalmazás és a hozzá kapcsolt erőforrásokat, törölje az erőforráscsoportot, amely tartalmazza azokat. 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>További lépések
- A mintául szolgáló szavazóalkalmazás megtekintése [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp).
- Service Fabric-háló kapcsolatos további információkért olvassa el a [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md).


