---
title: Fejlesztés az Azure Kubernetes szolgáltatásban (ak) a Helmtal
description: Az alkalmazás-tárolók fürtben való kicsomagolásához és futtatásához használja az AK-t és a Azure Container Registry-t.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 1f67605918e093e9ab28aa88be777d27acd831ef
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "82169568"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Gyors útmutató: fejlesztés az Azure Kubernetes Service-ben (ak) a Helmtal

A [Helm][helm] egy nyílt forráskódú csomagolási eszköz, amely segítséget nyújt a Kubernetes-alkalmazások életciklusának telepítéséhez és kezeléséhez. A Linux-csomagkezelő, például az *apt* és a *yum*hasonlóan a Helm a Kubernetes-diagramok kezelésére szolgál, amelyek előre konfigurált Kubernetes-erőforrások csomagjai.

Ebből a cikkből megtudhatja, hogyan használhatja a Helm csomagot és futtathat egy alkalmazást az AK-ban. Egy meglévő alkalmazás Helm használatával történő telepítésével kapcsolatos további információkért lásd: [meglévő alkalmazások telepítése az AK-ban Helm-ben][helm-existing].

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* Az [Azure CLI telepítve van](/cli/azure/install-azure-cli?view=azure-cli-latest).
* A Docker telepítése és konfigurálása megtörtént. A Docker csomagokat biztosít, amelyekkel a Docker [Mac][docker-for-mac], [Windows][docker-for-windows] vagy [Linux][docker-for-linux] rendszereken konfigurálható.
* A [Helm v3 telepítve van][helm-install].

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása
Ha a Helm használatával szeretné futtatni az alkalmazást az AK-fürtben, szüksége lesz egy Azure Container Registry a tároló lemezképének tárolására. Az alábbi példa az [az ACR Create][az-acr-create] paranccsal hoz létre egy *MyHelmACR* nevű ACR-t a *MyResourceGroup* -erőforráscsoporthoz az *alapszintű* SKU használatával. Adja meg a saját egyedi regisztrációs nevét. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az *Alapszintű* termékváltozat költséghatékony, fejlesztési célú belépési pontként szolgál, és kiegyenlített tárolási kapacitást és teljesítményt biztosít.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

A kimenet a következő példához hasonló. Jegyezze fel az ACR *lekéréséhez* értékét, mivel azt egy későbbi lépésben fogja használni. Az alábbi példában a *myhelmacr.azurecr.IO* a *myhelmacr* *lekéréséhez* .

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
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

Az ACR-példány használatához először be kell jelentkeznie. A bejelentkezéshez használja az az [ACR login][az-acr-login] parancsot. Az alábbi példa egy *MyHelmACR*nevű ACR-be fog bejelentkezni.

```azurecli
az acr login --name MyHelmACR
```

A parancs a *Bejelentkezés sikeres* üzenetet adja vissza, ha befejeződött.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service-fürt létrehozása

Hozzon létre egy AK-fürtöt. Az alábbi parancs létrehoz egy MyAKS nevű AK-fürtöt, és csatolja a MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Az AK-fürtnek hozzá kell férnie az ACR-hez a tároló lemezképének lekéréséhez és futtatásához. A fenti parancs azt is megadja, hogy a *MyAKS* -fürt hozzáférjen a *MyHelmACR* ACR-hez.

## <a name="connect-to-your-aks-cluster"></a>Kapcsolódás az AK-fürthöz

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz a helyi számítógépről, használja a Kubernetes [kubectl][kubectl] nevű parancssori ügyfelét.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

Az [aks get-credentials][] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. A következő példa a *MyAKS* nevű AK-fürt hitelesítő adatait kéri le a *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

Ez a rövid útmutató [egy példa Node. js-alkalmazást használ az Azure dev Spaces minta adattárból][example-nodejs]. Klónozott az alkalmazást a GitHubról, és navigáljon a `dev-spaces/samples/nodejs/getting-started/webfrontend` címtárhoz.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Docker létrehozása

Hozzon létre egy új *Docker* -fájlt a következő használatával:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>A minta alkalmazás létrehozása és leküldése az ACR-be

Szerezze be a bejelentkezési kiszolgáló nevét az az [ACR List][az-acr-list] paranccsal, és kérdezze le a *lekéréséhez*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A Docker használatával felépítheti, címkézheti és leküldheti a minta alkalmazás-tárolóját az ACR-be:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>A Helm-diagram létrehozása

A Helm-diagramot a `helm create` parancs használatával hozhatja ki.

```console
helm create webfrontend
```

Végezze el a következő frissítéseket a *webfrontend/Values. YAML*:

* Módosítás `image.repository` ide`<acrLoginServer>/webfrontend`
* Módosítás `service.type` ide`LoadBalancer`

Például:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: <acrLoginServer>/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

Frissítés `appVersion` a `v1` következőhöz: *webfrontend/chart. YAML*. Példa:

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>A Helm-diagram futtatása

Az `helm install` parancs használatával telepítse az alkalmazást a Helm-diagram használatával.

```console
helm install webfrontend webfrontend/
```

Néhány percet vesz igénybe, hogy a szolgáltatás egy nyilvános IP-címet ad vissza. A folyamat figyeléséhez használja az `kubectl get service` parancsot a *Watch* paraméterrel:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Nyissa meg az alkalmazás terheléselosztó eszközét egy böngészőben a `<EXTERNAL-IP>` használatával, és tekintse meg a minta alkalmazást.

## <a name="delete-the-cluster"></a>A fürt törlése

Ha a fürtre már nincs szükség, az az [Group delete][az-group-delete] paranccsal távolítsa el az erőforráscsoportot, az AK-fürtöt, a tároló-beállításjegyzéket, az ott tárolt tároló-lemezképeket és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket. Felügyelt identitás használata esetén az identitást a platform felügyeli, és nem szükséges az eltávolítás.

## <a name="next-steps"></a>További lépések

A Helm használatával kapcsolatos további információkért tekintse meg a Helm dokumentációját.

> [!div class="nextstepaction"]
> [A Helm dokumentációja][helm-documentation]

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
