---
title: Alkalmazás üzembe helyezése a Service Fabric-háló egy privát tárolójegyzékben rendszerképet az Azure Service Fabric háló |} A Microsoft Docs
description: Megtudhatja, hogyan helyezhet üzembe egy alkalmazást egy privát tárolójegyzékben lemezkép a Service Fabric háló az Azure CLI használatával.
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
ms.date: 08/20/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: a2791c86512790f36477e562cb82718174df8dc3
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296590"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Alkalmazás üzembe helyezése Service Fabric-háló egy privát rendszerkép tárolóregisztrációs adatbázisból

Ez a cikk bemutatja, hogyan helyezhet üzembe egy Azure Service Fabric-háló alkalmazást, amely egy privát tárolójegyzékben rendszerképet használ.

## <a name="prerequisites"></a>Előfeltételek

### <a name="set-up-service-fabric-mesh-cli"></a>A Service Fabric Mesh parancssori felületének beállítása

Az Azure parancssori felület helyi telepítését használja a feladat végrehajtásához. Az Azure Service Fabric Mesh CLI-bővítmény moduljának telepítéséhez kövesse ezeket az [útmutatásokat](service-fabric-mesh-howto-setup-cli.md).

## <a name="install-docker"></a>A Docker telepítése

#### <a name="windows-10"></a>Windows 10

Töltse le, majd telepítse a [Docker Community Edition for Windows][download-docker] legújabb verzióját a Service Fabric Mesh által használt tárolóalapú Service Fabric-alkalmazások támogatásához.

Amikor a telepítés során a rendszer kéri, válassza a **Windows-tárolók használatát Linux-tárolók helyett**. 

Ha a Hyper-V nincs engedélyezve a számítógépen, a Docker telepítője fel fogja ajánlani az engedélyezését. Ehhez kattintson az **OK** gombra, ha a rendszer arra kéri.

#### <a name="windows-server-2016"></a>Windows Server 2016

Ha nincs engedélyezve a Hyper-V szerepkör, nyissa meg a PowerShellt rendszergazdaként, futtassa a következő parancsot a Hyper-V engedélyezéséhez, majd indítsa újra a számítógépet. További információt a [Docker Enterprise Edition for Windows Server][download-docker-server] kiadást ismertető szakaszban talál.

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Indítsa újra a gépet.

Nyissa meg a PowerShellt rendszergazdaként, majd futtassa a következő parancsokat a Docker telepítéséhez:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba, és állítsa be az aktív előfizetést.

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Tárolóregisztrációs adatbázis létrehozása és a egy rendszerkép leküldése

A következő lépések segítségével hozzon létre egy Azure Container Registry.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az alábbi parancs segítségével hozzon létre egy erőforráscsoportot *myResourceGroup* a a *eastus* helyét.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Hozzon létre egy Azure container registry (ACR) példányt a `az acr create` parancsot. A beállításjegyzék neve legyen, egyedi Azure-ban, és 5 – 50 alfanumerikus karaktereket tartalmazhat. A következő példában a neve *myContainerRegistry007* szolgál. Ha a hibát, amely a beállításjegyzék-név használatban van, válasszon másik nevet. Használja ezt a nevet mindenhol `<acrName>` jelenik meg ezeket az utasításokat.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

A beállításjegyzék létrehozását követően látni fogja a következőhöz hasonló kimenetet:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="push-the-image-to-azure-container-registry"></a>A rendszerkép leküldése az Azure Container Registrybe

Rendszerkép leküldése az Azure container registry (ACR), először szüksége van egy tárolórendszerképet. Ha még nem rendelkezik helyi rendszerképe sem, futtassa a következő parancsot a egy rendszerképet a Docker Hubból (szükség lehet a Docker használata Linux-rendszerképeket, kattintson a jobb gombbal a docker ikont, majd válassza a Váltás **váltson át a Linux-tárolók**).

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Mielőtt leküldhetné a rendszerképet a regisztrációs adatbázisba, fel kell címkéznie az ACR bejelentkezési kiszolgálójának teljes nevével.

Futtassa a következő parancsot a bejelentkezési kiszolgálójának teljes nevét az ACR-példány beolvasásához.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A bejelentkezési kiszolgálójának teljes nevét visszaadott lesz a továbbiakban: `<acrLoginServer>` Ez a cikk többi részében.

Most már címkézése a docker rendszerkép használata a [docker címke] [ docker-tag] parancsot. Az alábbi parancsban cserélje le a `<acrLoginServer>` a fenti parancs által jelentett a bejelentkezési kiszolgálójának nevét. Az alábbi példa címkék seabreeze/azure-háló-helloworld:1.1-alpine kép. Ha másik rendszerképet használ, helyettesítse be a következő parancsot a rendszerkép nevét.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/seabreeze/azure-mesh-helloworld:1.1-alpine
```

Például:`docker tag seabreeze/azure-mesh-helloworld:1.1-alpine myContainerRegistry007.azurecr.io/seabreeze/azure-mesh-helloworld:1.1-alpine`

Jelentkezzen be az Azure Container Registrybe.

```bash
az acr login -n <acrName>
```

Például:`az acr login -n myContainerRegistry007`

A rendszerkép leküldése az azure container registrybe, a következő paranccsal:

```bash
docker push <acrLoginServer>/seabreeze/azure-mesh-helloworld:1.1-alpine
```

Például:`docker push myContainerRegistry007.azurecr.io/seabreeze/azure-mesh-helloworld:1.1-alpine`

### <a name="list-container-images"></a>Tárolórendszerképek listázása

Az alábbi példa egy beállításjegyzék adattárait listázza. A következő példák azt feltételezik, hogy használ az azure-háló-helloworld:1.1-alpine kép. Ha másik rendszerképet használ, helyettesítse be a nevét, ahol az azure-háló-helloworld lemezképet használja.

```azurecli
az acr repository list --name <acrName> --output table
```
Például:`az acr repository list --name myContainerRegistry007 --output table`

Kimenet:

```bash
Result
-------------------------------
seabreeze/azure-mesh-helloworld
```

Az alábbi példa címkéket listázza a **azure-háló-helloworld** tárház.

```azurecli
az acr repository show-tags --name <acrName> --repository seabreeze/azure-mesh-helloworld --output table
```

Például:`az acr repository show-tags --name myContainerRegistry007 --repository seabreeze/azure-mesh-helloworld --output table`

Kimenet:

```bash
Result
--------
1.1-alpine
```

A fenti kimeneti megerősíti, hogy a jelenléte `azure-mesh-helloworld:1.1-alpine` a privát beállításjegyzékben.

## <a name="retrieve-credentials-for-the-registry"></a>A beállításjegyzék hitelesítő adatainak lekérése

> [!IMPORTANT]
> Az Azure container registry a rendszergazdai felhasználó engedélyezése nem ajánlott üzemi forgatókönyvek esetén. Csak azért alkalmazzuk itt, hogy ez a bemutató rövid. Éles forgatókönyvekben használjon egy [szolgáltatásnév](https://docs.microsoft.com/azure/container-registry/container-registry-auth-service-principal) éles forgatókönyvekben felhasználói és a rendszer hitelesítésre.

Annak érdekében, hogy a beállításjegyzékből létrehozott tárolópéldány üzembe helyezéséhez meg kell adnia hitelesítő adatait az üzembe helyezés során. Engedélyezze a rendszergazdai felhasználót a regisztrációs adatbázisban a következő parancsot:

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

Például:`az acr update --name myContainerRegistry007 --admin-enabled true`

A beállításjegyzék kiszolgálónév, felhasználónév és jelszó lekérése a következő parancsokat:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

Az előző parancs által megadott értékeket hivatkozott `<acrLoginServer>`, `<acrUserName>`, és `<acrPassword>` alatt.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az alkalmazás és a kapcsolódó erőforrások az alábbi paranccsal hozzon létre, és adja meg az előző lépésben a hitelesítő adatokat.

A `registry-password` egy biztonságos karakterláncot paraméter a sablonban. Nem jelennek az üzembe helyezési állapot és `az mesh service show` parancsokat.

Ha Bash-konzolt használ, futtassa a következőt:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}"
```

Ha PowerShell-konzolt használ, futtassa a következőt:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{'location': {'value': 'eastus'}, 'registry-server': {'value': '<acrLoginServer>'}, 'registry-username': {'value': '<acrUserName>'}, 'registry-password': {'value': '<acrPassword>'}}"
```

Néhány perc alatt kell megjelennie:

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Az alkalmazás megnyitása

Miután sikeresen üzembe helyezte az alkalmazást, a nyilvános IP-cím beszerzése a szolgáltatási végpont, és a egy böngészőben nyissa meg. Service Fabric-háló emblémával weblap jeleníti meg.

A telepítési parancs visszaadja a szolgáltatásvégpont nyilvános IP-címét. Igény szerint is lekérdezheti a hálózati erőforrás keresése a szolgáltatásvégpont nyilvános IP-címét. 
 
Ez az alkalmazás a hálózati erőforrás neve nem `helloWorldPrivateRegistryNetwork`, beolvassa a következő paranccsal kapcsolatos információkat. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Az erőforrások törlése

Gyakran törölje az erőforrásokat, amelyek nem használják az Azure-ban. Ebben a példában a kapcsolódó erőforrások törléséhez törölje az erőforráscsoportot, amelyben üzembe lett helyezve, (Ez töröl Mindent az erőforráscsoporthoz társított) a következő paranccsal:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

Ha ehhez a gyakorlathoz Linux-rendszerképeket, és lépjen vissza a Windows-lemezképek használata, kattintson a jobb gombbal a docker ikont, és válassza ki a Docker váltott **váltson át a Windows-tárolók**

## <a name="next-steps"></a>További lépések

- A Hello World mintaalkalmazás megtekintése [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Service Fabric Erőforrásmodell kapcsolatos további információkért lásd: [Service Fabric háló Erőforrásmodell](service-fabric-mesh-service-fabric-resources.md).
- Service Fabric-háló kapcsolatos további információkért olvassa el a [Service Fabric-háló áttekintése](service-fabric-mesh-overview.md).

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/