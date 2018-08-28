---
title: Az Azure-on futó Kubernetes oktatóanyaga – Alkalmazás üzembe helyezése
description: Az Azure Kubernetes Service (AKS) ezen oktatóanyagában üzembe fog helyezni egy többtárolós alkalmazást a fürtön egy, az Azure Container Registryben tárolt egyéni rendszerkép használatával.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bf817f553250ead449ec0d5db3d33acc2eff23f3
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "41924795"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazások futtatása az Azure Kubernetes Service-ben (AKS)

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Ön hozz létre és helyezi üzembe saját alkalmazásait és szolgáltatásait a Kubernetes-fürtön, a rendelkezésre állást és a kapcsolatokat pedig a fürt kezeli. Ebben az oktatóanyagban, amely egy hétrészes sorozat negyedik része, egy alkalmazást helyezünk üzembe egy Kubernetes-fürtön. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kubernetes-jegyzékfájlok frissítése
> * Alkalmazás futtatása a Kubernetesben
> * Az alkalmazás tesztelése

Az ezt követő oktatóanyagokban horizontálisan felskálázzuk és frissítjük ezt az alkalmazást.

Ez az oktatóanyag feltételezi, hogy ismeri a Kubernetes alapvető fogalmait. A Kubernetesszel kapcsolatos részletes információkat a [Kubernetes dokumentációjában][kubernetes-documentation] találja.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt.

Az oktatóanyag teljesítéséhez szüksége lesz az előzőleg létrehozott `azure-vote-all-in-one-redis.yaml` Kubernetes-jegyzékfájlra. Ezt a fájlt az alkalmazás forráskódjával együtt egy korábbi oktatóanyagban letöltöttük. Bizonyosodjon meg róla, hogy az adattár klónja létrejött, és hogy a könyvtárakat átállította a klónozott adattárra. Ha ezeket a lépéseket még nem hajtotta végre, és szeretné követni az oktatóanyagot, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

Az oktatóanyag elvégzéséhez az Azure CLI 2.0.44-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="update-the-manifest-file"></a>A jegyzékfájl frissítése

Ezekben az oktatóanyagokban egy Azure Container Registry- (ACR-) példány tárolja a mintaalkalmazáshoz való tároló rendszerképét. Az alkalmazás üzembe helyezéséhez frissíteni kell a rendszerkép nevét a Kubernetes-jegyzékfájlban úgy, hogy magában foglalja az ACR bejelentkezési kiszolgálójának nevét.

Kérje le az ACR bejelentkezési kiszolgáló nevét az [az acr list][az-acr-list] paranccsal a következőképpen:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Az első oktatóanyagban klónozott, Git-adattárból származó mintajegyzékfájl a *microsoft* bejelentkezési kiszolgálónevet használja. Nyissa meg a jegyzékfájlt egy szövegszerkesztővel, például a `vi`-vel:

```console
vi azure-vote-all-in-one-redis.yaml
```

Helyettesítse be a *microsoft* nevet az Ön ACR bejelentkezési kiszolgálójának nevével. A rendszerkép neve a jegyzékfájl 47. sorában található. Az alábbi példa az alapértelmezett rendszerképnevet mutatja:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Adja meg saját ACR bejelentkezési kiszolgálójának nevét, hogy a jegyzékfájl a következő példához hasonlítson:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Mentse és zárja be a fájlt.

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése

Az alkalmazást a [kubectl apply][kubectl-apply] paranccsal helyezheti üzembe. A parancs elemzi jegyzékfájlt, és létrehozza a meghatározott Kubernetes-objektumokat. Adja meg a mintajegyzékfájlt a következő példa szerint:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

A Kubernetes-objektumok a fürtön belül jönnek létre, ahogy a következő példa is mutatja:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

A rendszer létrehoz egy [Kubernetes-szolgáltatást][kubernetes-service], amely közzéteszi az alkalmazást az interneten. Ez eltarthat pár percig. A folyamat állapotának monitorozásához használja a [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal:

```console
kubectl get service azure-vote-front --watch
```

Az *azure-vote-front* szolgáltatás *EXTERNAL-IP* értéke kezdetben *pending* állapotú, ahogy a következő példa is mutatja:

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Amikor az *EXTERNAL-IP* cím *pending* állapotról egy létező nyilvános IP-címre változik, a `CTRL-C` billentyűparanccsal állítsa le a kubectl figyelési folyamatát. A következő példa mutatja, hogy a nyilvános IP-cím most már hozzá van rendelve:

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Az alkalmazás működésének megtekintéséhez nyissa meg webböngészőjében a külső IP-címet.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/azure-vote.png)

Ha az alkalmazás nem töltődött be, ennek oka a rendszerkép-regisztrációs adatbázis engedélyezési problémája lehet. A tárolók állapotának megtekintéséhez használja a `kubectl get pods` parancsot. Ha a tárolók rendszerképei nem kérhetők le, lásd a [Container Registry-hozzáférés Kubernetes titkos kulcsokkal történő engedélyezésével](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret) foglalkozó témakört.

## <a name="next-steps"></a>További lépések

Az oktatóanyagban az Azure Vote alkalmazást egy AKS-beli Kubernetes-fürtön helyeztük üzembe. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Kubernetes-jegyzékfájlok frissítése
> * Alkalmazás futtatása a Kubernetesben
> * Az alkalmazás tesztelése

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan méretezhető egy Kubernetes-alkalmazás és az alapul szolgáló Kubernetes-infrastruktúra.

> [!div class="nextstepaction"]
> [Kubernetes-alkalmazás és -infrastruktúra méretezése][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
[azure-cli-install]: /cli/azure/install-azure-cli
