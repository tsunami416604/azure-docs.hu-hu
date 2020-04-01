---
title: Az Azure-on futó Kubernetes oktatóanyaga – Alkalmazás üzembe helyezése
description: Az Azure Kubernetes Service (AKS) ezen oktatóanyagában üzembe fog helyezni egy többtárolós alkalmazást a fürtön egy, az Azure Container Registryben tárolt egyéni rendszerkép használatával.
services: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.custom: mvc
ms.openlocfilehash: 3b614fcb6692f35884af2fc4e19210267ab8ab04
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77593274"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazások futtatása az Azure Kubernetes Service-ben (AKS)

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Ön hozz létre és helyezi üzembe saját alkalmazásait és szolgáltatásait a Kubernetes-fürtön, a rendelkezésre állást és a kapcsolatokat pedig a fürt kezeli. Ebben az oktatóanyagban, amely egy hétrészes sorozat negyedik része, egy alkalmazást helyezünk üzembe egy Kubernetes-fürtön. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kubernetes-jegyzékfájl frissítése
> * Alkalmazás futtatása a Kubernetesben
> * Az alkalmazás tesztelése

További oktatóanyagok, ez az alkalmazás kicsinyített és frissített.

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Kubernetes alapfogalmak az Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt.

Az oktatóanyag teljesítéséhez szüksége lesz az előzőleg létrehozott `azure-vote-all-in-one-redis.yaml` Kubernetes-jegyzékfájlra. Ezt a fájlt az alkalmazás forráskódjával együtt egy korábbi oktatóanyagban letöltöttük. Ellenőrizze, hogy klónozta-e a tárta, és hogy a könyvtárakat a klónozott tárműtérré módosította-e. Ha még nem végezte el ezeket a lépéseket, és szeretné követni a mentén, kezdje [az 1.][aks-tutorial-prepare-app]

Ez az oktatóanyag megköveteli, hogy az Azure CLI 2.0.53-as vagy újabb verzióját futtassa. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="update-the-manifest-file"></a>A jegyzékfájl frissítése

Ezekben az oktatóanyagokban egy Azure Container Registry- (ACR-) példány tárolja a mintaalkalmazáshoz való tároló rendszerképét. Az alkalmazás üzembe helyezéséhez frissíteni kell a rendszerkép nevét a Kubernetes-jegyzékfájlban úgy, hogy magában foglalja az ACR bejelentkezési kiszolgálójának nevét.

Kérje le az ACR bejelentkezési kiszolgáló nevét az [az acr list][az-acr-list] paranccsal a következőképpen:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Az első oktatóanyagban klónozott, Git-adattárból származó mintajegyzékfájl a *microsoft* bejelentkezési kiszolgálónevet használja. Győződjön meg arról, hogy a klónozott *azure-voting-app-redis* könyvtárban van, majd `vi`nyissa meg a jegyzékfájlt egy szövegszerkesztővel, például:

```console
vi azure-vote-all-in-one-redis.yaml
```

Helyettesítse be a *microsoft* nevet az Ön ACR bejelentkezési kiszolgálójának nevével. A kép neve a jegyzékfájl 51. Az alábbi példa az alapértelmezett rendszerképnevet mutatja:

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

Mentse és zárja be a fájlt. A `vi`alkalmazásban `:wq`használja a használatát.

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése

Az alkalmazást a [kubectl apply][kubectl-apply] paranccsal helyezheti üzembe. A parancs elemzi jegyzékfájlt, és létrehozza a meghatározott Kubernetes-objektumokat. Adja meg a mintajegyzékfájlt a következő példa szerint:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

A következő példa kimenetaz AKS-fürtben sikeresen létrehozott erőforrásokat mutatja be:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Amikor az alkalmazás fut, a Kubernetes szolgáltatás elérhetővé teszi az alkalmazás előtér-az interneten. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```console
kubectl get service azure-vote-front --watch
```

Kezdetben az *ex-IP* az *azure-vote-front* szolgáltatás jelenik *meg függőben :*

```
azure-vote-front   LoadBalancer   10.0.34.242   <pending>     80:30676/TCP   5s
```

Amikor az *EXTERNAL-IP-cím* *függőben lévőről* tényleges `CTRL-C` nyilvános `kubectl` IP-címre változik, az órafolyamat leállításához használja. A következő példa kimenete a szolgáltatáshoz rendelt érvényes nyilvános IP-címet mutatja:

```
azure-vote-front   LoadBalancer   10.0.34.242   52.179.23.131   80:30676/TCP   67s
```

Az alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére:

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/azure-vote.png)

Ha az alkalmazás nem töltődik be, annak oka lehet a lemezkép-beállításjegyzék engedélyezési problémája. A tárolók állapotának megtekintéséhez használja a `kubectl get pods` parancsot. Ha a tárolórendszerképek nem hívhatók le, olvassa el a [Hitelesítés az Azure Kubernetes-szolgáltatás ból az Azure Container Registry hitelesítése.](cluster-container-registry-integration.md)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy minta Azure szavazási alkalmazás telepítve van egy Kubernetes-fürt az AKS-ben. Megismerte, hogyan végezheti el az alábbi műveleteket:

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

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
