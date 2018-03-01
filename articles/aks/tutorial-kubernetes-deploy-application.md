---
title: "Azure-on futó Kubernetes oktatóanyag – Alkalmazás üzembe helyezése"
description: "AKS oktatóanyag – alkalmazás üzembe helyezése"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4ee59782a0e40f9d09023a75862ab0f8415e6d12
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="run-applications-in-azure-container-service-aks"></a>Alkalmazások futtatása az Azure Container Service (AKS) szolgáltatásban

Ebben az oktatóanyagban, amely egy nyolcrészes sorozat negyedik része, egy alkalmazást helyezünk üzembe egy Kubernetes-fürtön. Ennek lépései az alábbiak:

> [!div class="checklist"]
> * Kubernetes-jegyzékfájlok frissítése
> * Alkalmazás futtatása a Kubernetesben
> * Az alkalmazás tesztelése

Az ezt követő oktatóanyagokban méretezzük és frissítjük majd az alkalmazást, és az Operations Management Suite szolgáltatást konfiguráljuk a Kubernetes-fürt monitorozására.

Ez az oktatóanyag feltételezi, hogy ismeri a Kubernetes alapvető fogalmait. A Kubernetesszel kapcsolatos részletes információkat a [Kubernetes dokumentációjában][kubernetes-documentation] találja.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt. 

Az oktatóanyag teljesítéséhez szüksége lesz az előzőleg létrehozott `azure-vote-all-in-one-redis.yaml` Kubernetes-jegyzékfájlra. Ezt a fájlt az alkalmazás forráskódjával együtt egy korábbi oktatóanyagban letöltöttük. Bizonyosodjon meg róla, hogy az adattár klónja létrejött, és hogy a könyvtárakat átállította a klónozott adattárra.

Ha ezeket a lépéseket még nem hajtotta végre, és szeretné követni az oktatóanyagot, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

## <a name="update-manifest-file"></a>A jegyzékfájl frissítése

Ebben az oktatóanyagban az Azure Container Registryt (ACR) tárolólemezkép tárolására használtuk. Mielőtt futtatná az alkalmazást, az ACR bejelentkezési kiszolgálójának nevét frissíteni kell a Kubernetes-jegyzékfájlban.

Kérje le az ACR bejelentkezési kiszolgáló nevét az [az acr list][az-acr-list] paranccsal.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A jegyzékfájlt előzőleg a `microsoft` bejelentkezési kiszolgálónévvel hoztuk létre. Nyissa meg a fájlt bármilyen szövegszerkesztőben. Jelen példában a fájlt a `vi` eszközzel nyitjuk meg.

```console
vi azure-vote-all-in-one-redis.yaml
```

Helyettesítse be a `microsoft` nevet az ACR bejelentkezési kiszolgálójának nevével. Ez az érték a jegyzékfájl **47**. sorában található.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Mentse és zárja be a fájlt.

## <a name="deploy-application"></a>Alkalmazás üzembe helyezése

Az alkalmazást a [kubectl create][kubectl-create] paranccsal futtathatja. A parancs elemzi jegyzékfájlt, és létrehozza a meghatározott Kubernetes-objektumokat.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yaml
```

Kimenet:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Alkalmazás tesztelése

A rendszer létrehoz egy [Kubernetes-szolgáltatást][kubernetes-service], amely közzéteszi az alkalmazást az interneten. Ez eltarthat pár percig. 

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```azurecli
kubectl get service azure-vote-front --watch
```

Kezdetben az *azure-vote-front* szolgáltatás *EXTERNAL-IP* értéke *pending* állapotú.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Miután az *EXTERNAL-IP* cím *pending* állapotról egy *IP-címre* változik, a `CTRL-C` billentyűparanccsal állítsa le a kubectl figyelési folyamatát. 

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Az alkalmazás megtekintéséhez navigáljon a külső IP-címhez.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/azure-vote.png)

Ha az alkalmazás nem töltődött be, ennek oka a rendszerkép-regisztrációs adatbázis engedélyezési problémája lehet.

Az alábbi lépéseket követve [engedélyezheti a hozzáférést a Kubernetes titkos kódján keresztül](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>További lépések

Az oktatóanyagban az Azure Vote alkalmazást egy AKS-beli Kubernetes-fürtön helyeztük üzembe. Az eddig végrehajtott feladatok a következők:  

> [!div class="checklist"]
> * Letöltöttük a Kubernetes-jegyzékfájlokat
> * Futtattuk az alkalmazást a Kubernetesben
> * Teszteltük az alkalmazást

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan méretezhető együtt egy Kubernetes-alkalmazás, és az alapul szolgáló Kubernetes-infrastruktúra. 

> [!div class="nextstepaction"]
> [Kubernetes-alkalmazás és -infrastruktúra méretezése][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
