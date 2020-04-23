---
title: Fejlesztés az Azure Kubernetes szolgáltatáson (AKS) a Helm segítségével
description: A Helm az AKS-sel és az Azure Container Registry használatával csomag, és futtatja az alkalmazástárolók egy fürtben.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 77627ab846999ea5ba42fde7a9c49b9cc7559fba
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873432"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>Rövid útmutató: Fejlesztés az Azure Kubernetes szolgáltatáson (AKS) a Helm

[A Helm][helm] egy nyílt forráskódú csomagolási eszköz, amely segít a Kubernetes-alkalmazások életciklusának telepítésében és kezelésében. Hasonló a Linux csomagkezelők, mint az *APT* és *a Yum,* Helm kezelésére használják Kubernetes diagramok, amelyek csomagok előre konfigurált Kubernetes erőforrások.

Ez a cikk bemutatja, hogyan használhatja a Helm egy alkalmazás csomagolásához és futtatásához az AKS-en. A Helm használatával meglévő alkalmazások telepítéséről a [Meglévő alkalmazások telepítése a Helm segítségével az AKS-ben][helm-existing]című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free).
* [Az Azure CLI telepítve van.](/cli/azure/install-azure-cli?view=azure-cli-latest)
* A Docker telepítve és konfigurálva. A Docker csomagokat biztosít, amelyekkel a Docker [Mac][docker-for-mac], [Windows][docker-for-windows] vagy [Linux][docker-for-linux] rendszereken konfigurálható.
* [Helm v3 telepítve][helm-install].

## <a name="create-an-azure-container-registry"></a>Azure Container Registry létrehozása
Helm használatával az alkalmazás a KS-fürtben, szüksége van egy Azure Container Registry a tárolórendszerképek tárolásához. Az alábbi példában [az acr create][az-acr-create] segítségével hozzon létre egy *MyHelmACR* nevű ACR-t a *MyResourceGroup* erőforráscsoportban az *alapszintű* termékváltozattal. Meg kell adnia a saját egyedi rendszerleíró nevét. A beállításjegyzék nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az *Alapszintű* termékváltozat költséghatékony, fejlesztési célú belépési pontként szolgál, és kiegyenlített tárolási kapacitást és teljesítményt biztosít.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

A kimenet a következő példához hasonló. Jegyezze fel az ACR *loginServer* értékét, mivel egy későbbi lépésben fogja használni. Az alábbi példában *myhelmacr.azurecr.io* a *MyHelmACR* *loginServer kiszolgálója.*

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

Az ACR-példány használatához először be kell jelentkeznie. A bejelentkezéshez használja az [acr bejelentkezési][az-acr-login] parancsot. Az alábbi példa egy *MyHelmACR*nevű ACR-be jelentkezik be.

```azurecli
az acr login --name MyHelmACR
```

A parancs a *Bejelentkezés sikeres* üzenetet adja vissza, ha befejeződött.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes-szolgáltatásfürt létrehozása

Hozzon létre egy AKS-fürtöt. Az alábbi parancs létrehoz egy AKS-fürt nevű MyAKS és csatolja MyHelmACR.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

Az AKS-fürtnek hozzáférésre van szüksége az ACR-hez a tárolórendszerképek lekérése és futtatása érdekében. A fenti parancs a *MyAKS-fürtnek* is hozzáférést biztosít a *MyHelmACR* ACR-hez.

## <a name="connect-to-your-aks-cluster"></a>Csatlakozás az AKS-fürthöz

Ahhoz, hogy csatlakozni tudjon a Kubernetes-fürthöz a helyi számítógépről, használja a Kubernetes [kubectl][kubectl] nevű parancssori ügyfelét.

Ha az Azure Cloud Shellt használja, a `kubectl` már telepítve van. Helyben is telepítheti az [az aks install-cli][] paranccsal:

```azurecli
az aks install-cli
```

Az [aks get-credentials][] paranccsal konfigurálható`kubectl` a Kubernetes-fürthöz való csatlakozásra. A következő példa a *MyResourceGroup*ban a MyResourceGroup ban *myaks* nevű AKS-fürt hitelesítő adatait kapja meg:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

Ez a rövid útmutató [egy példa Node.js alkalmazást használ az Azure Dev Spaces minttárházból.][example-nodejs] Klónozza az alkalmazást a GitHubról, és keresse meg a `dev-spaces/samples/nodejs/getting-started/webfrontend` könyvtárat.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Docker-fájl létrehozása

Hozzon létre egy új *Docker-fájlt* az alábbi használatával:

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>A mintaalkalmazás létrehozása és leküldése az ACR-be

A bejelentkezési kiszolgáló címének beszereznie az [acr list][az-acr-list] paranccsal és a *loginServer lekérdezésével:*

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A Docker használatával hozhat létre, címkézhet és lelökheti a mintaalkalmazás-tárolót az ACR-be:

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>A Helm-diagram létrehozása

Hozza létre a `helm create` Helm diagramot a paranccsal.

```console
helm create webfrontend
```

Végezze el a következő frissítéseket *a webfrontend/values.yaml webhelyen:*

* Váltás `image.repository` a következőre:`<acrLoginServer>/webfrontend`
* Váltás `service.type` a következőre:`LoadBalancer`

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

Frissítés `appVersion` `v1` a *webfrontend/Chart.yaml .* Példa:

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>Futtassa a Helm chart

A `helm create` parancs segítségével telepítse az alkalmazást a Helm diagram használatával.

```console
helm install webfrontend webfrontend/
```

A szolgáltatás néhány percet vesz igénybe a nyilvános IP-cím visszaadása. A folyamat figyeléséhez `kubectl get service` használja a parancsot a *figyelési* paraméterrel:

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

Keresse meg az alkalmazás terheléselosztóját a `<EXTERNAL-IP>` böngészőben a mintaalkalmazás megtekintéséhez.

## <a name="delete-the-cluster"></a>A fürt törlése

Ha a fürtre már nincs szükség, az [az csoport törlési][az-group-delete] parancsával távolítsa el az erőforráscsoportot, az AKS-fürtöt, a tároló beállításjegyzékét, az ott tárolt tárolólemezképeket és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket. Ha felügyelt identitást használt, az identitást a platform kezeli, és nem igényel eltávolítást.

## <a name="next-steps"></a>További lépések

A Helm használatával kapcsolatos további információkért tekintse meg a Helm dokumentációját.

> [!div class="nextstepaction"]
> [Helm dokumentáció][helm-documentation]

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
