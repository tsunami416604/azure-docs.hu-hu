---
title: Az Azure-on futó Kubernetes oktatóanyaga – Alkalmazás üzembe helyezése
description: Az Azure Kubernetes Service (AKS) ezen oktatóanyagában üzembe fog helyezni egy többtárolós alkalmazást a fürtön egy, az Azure Container Registryben tárolt egyéni rendszerkép használatával.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: 42437130d30a405af47289ae16d9851fb506a598
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756271"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazások futtatása az Azure Kubernetes Service (AKS)

A Kubernetes tárolóalapú alkalmazásokhoz kínál elosztott platformot. Ön hozz létre és helyezi üzembe saját alkalmazásait és szolgáltatásait a Kubernetes-fürtön, a rendelkezésre állást és a kapcsolatokat pedig a fürt kezeli. Ebben az oktatóanyagban, amely egy hétrészes sorozat negyedik része, egy alkalmazást helyezünk üzembe egy Kubernetes-fürtön. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kubernetes-jegyzékfájlok frissítése
> * Alkalmazás futtatása a Kubernetesben
> * Az alkalmazás tesztelése

Ezt az alkalmazást a további oktatóanyagokban horizontálisan felskálázott és frissítése.

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információkért lásd: [Kubernetes alapvető fogalmait Azure Kubernetes Service (AKS)][kubernetes-concepts].

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt.

Az oktatóanyag teljesítéséhez szüksége lesz az előzőleg létrehozott `azure-vote-all-in-one-redis.yaml` Kubernetes-jegyzékfájlra. Ezt a fájlt az alkalmazás forráskódjával együtt egy korábbi oktatóanyagban letöltöttük. Ellenőrizze, hogy Ön már az adattár klónja létrejött, valamint, hogy Ön könyvtárakat átállította a klónozott adattárra. Ha még nem végezte el ezeket a lépéseket, és szeretné követni, kezdje [1. oktatóanyag – tárolórendszerképek létrehozása][aks-tutorial-prepare-app].

Ehhez az oktatóanyaghoz, hogy futtat-e az Azure CLI 2.0.53 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="update-the-manifest-file"></a>A jegyzékfájl frissítése

Ezekben az oktatóanyagokban egy Azure Container Registry- (ACR-) példány tárolja a mintaalkalmazáshoz való tároló rendszerképét. Az alkalmazás üzembe helyezéséhez frissíteni kell a rendszerkép nevét a Kubernetes-jegyzékfájlban úgy, hogy magában foglalja az ACR bejelentkezési kiszolgálójának nevét.

Kérje le az ACR bejelentkezési kiszolgáló nevét az [az acr list][az-acr-list] paranccsal a következőképpen:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Az első oktatóanyagban klónozott, Git-adattárból származó mintajegyzékfájl a *microsoft* bejelentkezési kiszolgálónevet használja. Győződjön meg arról, hogy használja-e a klónozott *azure-szavazóalkalmazás – alkalmazás-redis* könyvtárat, majd nyissa meg a jegyzékfájl-fájlt egy szövegszerkesztőben, például `vi`:

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

Mentse és zárja be a fájlt. A `vi`, használjon `:wq`.

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése

Az alkalmazást a [kubectl apply][kubectl-apply] paranccsal helyezheti üzembe. A parancs elemzi jegyzékfájlt, és létrehozza a meghatározott Kubernetes-objektumokat. Adja meg a mintajegyzékfájlt a következő példa szerint:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Az alábbi példa kimenetében látható, az AKS-fürtöt a sikeresen létrehozott erőforrásokat:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor egy Kubernetes-szolgáltatást mutatja meg az alkalmazás előtér az internethez. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```console
kubectl get service azure-vote-front --watch
```

Kezdetben a *EXTERNAL-IP* számára a *azure-vote-front* szolgáltatás jelenik meg, mint *függőben lévő*:

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Ha a *EXTERNAL-IP* -cím módosul a *függőben lévő* egy tényleges nyilvános IP-címet használja `CTRL-C` leállítani a `kubectl` tekintse meg a folyamatot. Az alábbi példa kimenetében látható, érvényes nyilvános IP-címet rendel hozzá a szolgáltatáshoz:

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Az alkalmazás működésének megtekintéséhez nyissa meg a szolgáltatás külső IP-címét egy webböngészőben:

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/azure-vote.png)

Ha az alkalmazás nem töltődött be, a regisztrációs adatbázisba egy engedélyezési problémája miatt lehet. A tárolók állapotának megtekintéséhez használja a `kubectl get pods` parancsot. Ha nem a tárolórendszerképeket le kell kérnie, [hozzáférést ad a Container Registry és a egy Kubernetes titkos](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy mintaalkalmazást az Azure vote az aks-ben a Kubernetes-fürtön helyeztünk üzembe. Megismerte, hogyan végezheti el az alábbi műveleteket:

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
