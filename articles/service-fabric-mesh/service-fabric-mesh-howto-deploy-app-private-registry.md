---
title: Az Azure Service Fabric háló privát tárolójegyzékből származó alkalmazás üzembe helyezése |} A Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe egy alkalmazást, amely egy privát tárolójegyzékben használja a Service Fabric háló az Azure CLI használatával.
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
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: af92d3c6ea881d00ec687a5560bf4db35aa431c5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089486"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Alkalmazás üzembe helyezése Service Fabric-háló egy privát rendszerkép tárolóregisztrációs adatbázisból

Ez a cikk bemutatja, hogyan helyezhet üzembe egy Azure Service Fabric-háló alkalmazást, amely egy privát tárolójegyzékben rendszerképet használ.

## <a name="prerequisites"></a>Előfeltételek

### <a name="set-up-service-fabric-mesh-cli"></a>Service Fabric háló parancssori felület beállítása 
Ez a feladat végrehajtásához használhatja az Azure Cloud Shell vagy az Azure parancssori felület helyi telepítése. Az Azure Service Fabric háló parancssori bővítmény modul telepítése a következő [utasításokat](service-fabric-mesh-howto-setup-cli.md).

### <a name="install-docker"></a>A Docker telepítése

Telepítheti a Dockert a tárolóalapú Service Fabric-alkalmazások Service Fabric-háló által használt támogatásához.

### <a name="windows-10"></a>Windows 10

Töltse le és telepítse a legújabb [a Docker Community Edition for Windows][download-docker]. 

A telepítés során válasszon **helyett Linux-tárolók használata Windows-tárolók** Ha a rendszer kéri. Szüksége lesz, majd jelentkezzen ki, és jelentkezzen be újra. A bejelentkezés után vissza, ha korábban nem engedélyezte a Hyper-V, kérheti a Hyper-V engedélyezése. Engedélyezze a Hyper-V, és indítsa újra a számítógépet.

A számítógép újraindítása után a Docker felkéri, hogy engedélyezze a **tárolók** funkciót, az engedélyezéshez, és indítsa újra a számítógépet.

### <a name="windows-server-2016"></a>Windows Server 2016

A következő PowerShell-parancsok használatával telepítheti a Dockert. További információkért lásd: [Docker Enterprise Edition for Windows Server][download-docker-server].

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Indítsa újra a számítógépet.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba, és állítsa be az aktív előfizetést:

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Tárolóregisztrációs adatbázis létrehozása és a egy rendszerkép leküldése

Hozzon létre egy Azure Container Registry utasításait követve [egy privát Docker-tárolójegyzék létrehozása az Azure-ban az Azure CLI-vel](../container-registry/container-registry-get-started-azure-cli.md). Akár hajtsa végre a lépéseket a [jelentkezzen be az ACR-be](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr) . lépés. 

### <a name="push-image-to-acr"></a>Rendszerkép leküldése az ACR-be

Ahhoz, hogy rendszerképet tudjon küldeni egy Azure Container Registry tárolóregisztrációs adatbázisba, először szüksége van egy rendszerképre. Ha még nincs egy helyi rendszerképe sem, futtassa a következő parancsot egy meglévő rendszerkép lekéréshez a Docker Hubból.

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Mielőtt leküldhetné a rendszerképet a regisztrációs adatbázisba, fel kell címkéznie az ACR bejelentkezési kiszolgálójának teljes nevével. A következő paranccsal kérheti le az ACR-példány bejelentkezési kiszolgálójának teljes nevét.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Címkézze fel a rendszerképet a [docker tag][docker-tag] parancs használatával. Helyettesítse be az `<acrLoginServer>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>Tárolórendszerképek listázása

A következő példa egy regisztrációs adatbázisba adattárait listázza:

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```bash
Result
----------------
azure-mesh-helloworld
```

Az alábbi példa címkéket listázza a **azure-háló-helloworld** tárház.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

Kimenet:

```bash
Result
--------
1.1-alpine
```
A fenti kimeneti megerősíti, hogy a jelenléte `azure-mesh-helloworld:1.1-alpine` a privát beállításjegyzékben.

## <a name="retrieve-credentials-for-the-registry"></a>A beállításjegyzék hitelesítő adatainak lekérése

A beállításjegyzékből létrehozott tárolópéldány üzembe helyezéséhez, hitelesítő adatok a telepítés során meg kell adni. Engedélyezze a rendszergazdai felhasználót a regisztrációs adatbázisban a következő parancsot:

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

A beállításjegyzék kiszolgálónév, felhasználónév és jelszó lekérése a következő parancsokat:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

Az előző parancs által megadott értékeket hivatkozott `<acrLoginServer>`, `<acrUserName>`, és `<acrPassword>` az alábbi parancsban.


## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az alkalmazás és a kapcsolódó erőforrások az alábbi paranccsal hozzon létre, és adja meg az előző lépésben a hitelesítő adatokat.

A `registry-password` paraméter a sablon egy `securestring`. Nem jelennek az üzembe helyezési állapot és `az mesh service show` parancsokat. Győződjön meg arról, hogy ezt helyesen van megadva a következő parancsban.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}" 
```

Néhány perc alatt a parancs a kell visszaadnia:

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Nyissa meg az alkalmazás
Miután sikeresen üzembe helyezte az alkalmazást, a nyilvános IP-cím beszerzése a szolgáltatási végpont, és a egy böngészőben nyissa meg. Service Fabric-háló emblémával weblap jeleníti meg.

A telepítési parancs visszaadja a szolgáltatásvégpont nyilvános IP-címét. Igény szerint is lekérdezheti a hálózati erőforrás keresése a szolgáltatásvégpont nyilvános IP-címét. 
 
Ez az alkalmazás a hálózati erőforrás neve nem `helloWorldPrivateRegistryNetwork`, beolvassa a következő paranccsal kapcsolatos információkat. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Az erőforrások törlése

Erőforrásokkal való takarékoskodáshoz a korlátozott előzetes programjában hozzárendelni, gyakran törölje az erőforrásokat. Ebben a példában kapcsolódó erőforrások törléséhez törölje az erőforráscsoportot, amelyben üzembe lett helyezve.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>További lépések
- A Hello World mintaalkalmazás megtekintése [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Service Fabric Erőforrásmodell kapcsolatos további információkért lásd: [Service Fabric háló Erőforrásmodell](service-fabric-mesh-service-fabric-resources.md).
- Service Fabric-háló kapcsolatos további információkért olvassa el a [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md).

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/