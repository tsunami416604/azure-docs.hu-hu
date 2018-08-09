---
title: Rövid útmutató – Privát Docker regisztrációs adatbázis létrehozása az Azure-ban az Azure CLI-vel
description: Az útmutató azt ismerteti, hogyan hozhat létre egy privát Docker regisztrációs adatbázist az Azure CLI-vel.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: marsma
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 78dc9eceba11ce07deb7fe0d10df1fea9cd74a75
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426122"
---
# <a name="quickstart-create-a-container-registry-using-the-azure-cli"></a>Rövid útmutató: Tárolóregisztrációs adatbázis létrehozása az Azure CLI-vel

Az Azure Container Registry egy felügyelt Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez az útmutató a következőket ismerteti: Azure Container Registry-példány létrehozása az Azure CLI-vel, tárolórendszerkép továbbítása a regisztrációs adatbázisba, végül a tároló üzembe helyezése a regisztrációs adatbázisból az Azure Container Instances (ACI) szolgáltatásban.

A rövid útmutatóhoz az Azure CLI 2.0.27-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli].

A Dockert is telepítenie kell helyileg. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [Mac][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ebben a rövid útmutatóban egy *Alapszintű* regisztrációs adatbázist hoz létre. Az Azure Container Registry több különböző termékváltozatban érhető el, amelyek rövid leírása az alábbi táblázatban található. Bővebb részletekért lásd a [Container Registry termékváltozatait][container-registry-skus] ismertető cikket.

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Hozzon létre egy ACR-példányt az [az acr create][az-acr-create] paranccsal.

A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában a *myContainerRegistry007* nevet használjuk. Ezt cserélje le egy egyedi értékre.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

A tárolóregisztrációs adatbázis létrehozásakor a kimenet a következő példához hasonló:

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

A rövid útmutató hátralevő részében az `<acrName>` elem helyettesíti a tárolóregisztrációs adatbázis nevét.

## <a name="log-in-to-acr"></a>Jelentkezzen be az ACR-be

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Ehhez használja az [az acr login][az-acr-login] parancsot.

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` üzenetet adja vissza, ha befejeződött.

## <a name="push-image-to-acr"></a>Rendszerkép leküldése az ACR-be

Ahhoz, hogy rendszerképet tudjon küldeni egy Azure Container Registry tárolóregisztrációs adatbázisba, először szüksége van egy rendszerképre. Ha még nincs egy helyi rendszerképe sem, futtassa a következő parancsot egy meglévő rendszerkép lekéréshez a Docker Hubból.

```bash
docker pull microsoft/aci-helloworld
```

Mielőtt leküldhetné a rendszerképet a regisztrációs adatbázisba, fel kell címkéznie az ACR bejelentkezési kiszolgálójának teljes nevével. A következő paranccsal kérheti le az ACR-példány bejelentkezési kiszolgálójának teljes nevét.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Címkézze fel a rendszerképet a [docker tag][docker-tag] parancs használatával. Helyettesítse be az `<acrLoginServer>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Végül a [docker push][docker-push] paranccsal küldje le a rendszerképet az ACR-példányba. Helyettesítse be az `<acrLoginServer>` helyére az ACR-példány bejelentkezési kiszolgálójának nevét.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Tárolórendszerképek listázása

A következő példa egy regisztrációs adatbázisba adattárait listázza:

```azurecli
az acr repository list --name <acrName> --output table
```

Kimenet:

```bash
Result
----------------
aci-helloworld
```

A következő példa az **aci-helloworld** adattárban lévő címkéket sorolja fel.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Kimenet:

```bash
Result
--------
v1
```

## <a name="deploy-image-to-aci"></a>Rendszerkép üzembe helyezése az ACI szolgáltatásban

Tárolópéldánynak a létrehozott regisztrációs adatbázisból történő üzembe helyezéséhez meg kell adnia a regisztrációs adatbázis hitelesítő adatait az üzembe helyezéskor. Éles forgatókönyvekben használjon [szolgáltatásnevet][container-registry-auth-aci] a tárolóregisztrációs adatbázis eléréséhez, a rövid útmutató tömörségének megőrzéséhez azonban engedélyezze a rendszergazdai felhasználót a regisztrációs adatbázisban az alábbi paranccsal:

```azurecli
az acr update --name <acrName> --admin-enabled true
```

Miután engedélyezte a rendszergazdát, a felhasználónév megegyezik a regisztrációs adatbázis nevével, a jelszót pedig az alábbi paranccsal kérheti le:

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

A tárolórendszerkép 1 processzormaggal és 1 GB memóriával történő üzembe helyezéséhez futtassa az alábbi parancsot. Cserélje le az `<acrName>`, `<acrLoginServer>` és `<acrPassword>` helyőrzőt az előző parancsokból beszerzett értékekre.

```azurecli
az container create --resource-group myResourceGroup --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Az Azure Resource Manager kezdeti válaszát kell megkapnia a tároló részleteivel. A tároló állapotának monitorozásához és annak ellenőrzéséhez, hogy mikor fut, ismételje meg az [az container show][az-container-show] parancsot. Ez kevesebb mint egy percet vesz igénybe.

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query instanceView.state
```

## <a name="view-the-application"></a>Az alkalmazás megtekintése

Ha az üzembe helyezés az ACI szolgáltatásban sikeresen megtörtént, az [az container show][az-container-show] paranccsal kérje le a tároló teljes tartománynevét:

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query ipAddress.fqdn
```

Példa a kimenetre: `"aci-demo.eastus.azurecontainer.io"`

A futó alkalmazás megtekintéséhez nyissa meg a nyilvános IP-címet kedvenc böngészőjében.

![A Hello World alkalmazás a böngészőben][aci-app-browser]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, az [az group delete][az-group-delete] paranccsal eltávolítható az erőforráscsoport, az ACR-példány és az összes tárolórendszerkép.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry-példányt az Azure CLI segítségével, továbbított egy tárolórendszerképet a regisztrációs adatbázisba, és futtatta annak egy példányát az Azure Container Instances használatával. Folytassa az Azure Container Instances oktatóanyagával, amelyben alaposabban megismerheti az ACI szolgáltatást.

> [!div class="nextstepaction"]
> [Az Azure Container Instances oktatóanyaga][container-instances-tutorial-prepare-app]

<!-- IMAGES> -->
[aci-app-browser]: ../container-instances/media/container-instances-quickstart/aci-app-browser.png


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[az-container-show]: /cli/azure/container#az-container-show
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
[container-registry-auth-aci]: container-registry-auth-aci.md