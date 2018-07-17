---
title: Az alkalmazás üzembe helyezése az Azure Service Fabric háló-sablonnal |} A Microsoft Docs
description: 'Útmutató: .NET Core-alkalmazás üzembe helyezése Service Fabric-háló egy sablonból az Azure CLI használatával.'
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceputal
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 11b3ad6bf83eebfc94ead6f8a730b8e6a6b8de2f
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076153"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>Service Fabric-háló-alkalmazás üzembe helyezése Service Fabric háló-sablon használatával
Ez a cikk bemutatja a .NET Core-alkalmazás Service Fabric-háló üzembe helyezése sablon használatával. Ha elkészült, rendelkezni kezelőfelületes szavazóalkalmazással, egy ASP.NET Core webes kezelőfelületes, amely a fürt egy háttérszolgáltatás menti a szavazati adatokat. Az előtér-DNS használatával oldja fel a címet a háttér-szolgáltatás.

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

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése
Az alkalmazás létrehozása a resource csoport használatával a `deployment create` parancsot.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Az előző parancs telepíti a Windows alkalmazás használatával [mesh_rp.windows.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json). Ha szeretne egy Linux-alkalmazás üzembe helyezése, [mesh_rp.linux.json sablon](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json). Windows-tárolórendszerképeket nagyobb, mint a Linuxos tárolólemezképek és központi telepítése több időt is igénybe vehet.

Néhány perc alatt a parancs a kell visszaadnia:

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>.` 

Ha például az IP-cím 13.68.129.22.

## <a name="open-the-application"></a>Nyissa meg az alkalmazás
Miután sikeresen üzembe helyezte az alkalmazást, a végpont a böngészőben (13.68.129.22, az előző példából) szolgáltatás kapcsolódni.  

![Szavazóalkalmazás](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

Most szavazati lehetőséget hozzáadni az alkalmazáshoz, és szavazzon rajta vagy törlése a szavazati lehetőséget.

## <a name="check-the-application-details"></a>Ellenőrizze az alkalmazás részletei
Ellenőrizheti az alkalmazás használatával az `app show` parancsot. Az alkalmazás neve az üzembe helyezett alkalmazás "VotingApp", így beolvasni a részletek. 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

Is lekérdezheti, ha a hálózati erőforrás keresése IP-címe a tárolóhoz, ahol az "az háló hálózati show" paranccsal, a szolgáltatás üzembe helyezése:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="list-the-deployed-applications"></a>Az üzembe helyezett alkalmazások listázása
Az "Alkalmazáslista" parancs segítségével az előfizetés telepített alkalmazások listáját. 

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


