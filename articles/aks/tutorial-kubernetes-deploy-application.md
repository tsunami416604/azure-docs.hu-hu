---
title: "Kubernetes az Azure útmutató - alkalmazás központi telepítése"
description: "AKS oktatóanyag - alkalmazás központi telepítése"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1433fce61bc26a6aa0e4742195e623329eb5f0cc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="run-applications-in-azure-container-service-aks"></a>Alkalmazások futtatásához Azure tároló szolgáltatás (AKS)

Ebben az oktatóanyagban négy nyolc része, egy minta-alkalmazás központi telepítése egy Kubernetes fürtbe. Befejeződött a lépések az alábbiak:

> [!div class="checklist"]
> * Frissítse a jegyzékfájlt Kubernetes
> * Futtatja az alkalmazást Kubernetes
> * Az alkalmazás tesztelése

A következő útmutatókból az alkalmazás van méretezhető, frissítése és az Operations Management Suite a Kubernetes fürt figyelésére konfigurálni.

Ez az oktatóanyag azt feltételezi, hogy alapszinten megértse, Kubernetes fogalmak Kubernetes tekintse meg a részletes információkat a [Kubernetes dokumentáció][kubernetes-documentation].

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokat egy alkalmazás egy tároló lemezképpel csomagolása, ez a Rendszerkép feltöltése Azure tároló beállításjegyzék és Kubernetes fürt létrejött. 

Az oktatóanyag teljesítéséhez szüksége az előre létrehozott `azure-vote-all-in-one-redis.yml` Kubernetes manifest fájlt. Ez a fájl egy korábbi oktatóprogram letöltött alkalmazás forráskódja. Győződjön meg arról, hogy rendelkezik-e klónozták a tárházban, és hogy módosult-könyvtárak a klónozott tárház be.

Ha nem volna ezeket a lépéseket, és szeretné követéséhez, vissza [oktatóanyag 1 – létrehozás tároló képek][aks-tutorial-prepare-app].

## <a name="update-manifest-file"></a>A jegyzékfájl frissítése

Ebben az oktatóanyagban szereplő Azure tároló beállításjegyzék (ACR) tároló-lemezkép mentéséhez használatban van. Mielőtt futtatná az alkalmazást, a ACR bejelentkezési kiszolgálónév Kubernetes jegyzékfájl frissíteni kell.

A ACR bejelentkezési kiszolgáló nevét, a [az acr lista] [ az-acr-list] parancsot.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A jegyzékfájl lett egy bejelentkezési kiszolgálónevet az előre létrehozott `microsoft`. Szövegszerkesztőben nyissa meg a fájlt. Ebben a példában a fájl a Megnyitás a `vi`.

```console
vi azure-vote-all-in-one-redis.yml
```

Cserélje le `microsoft` az ACR bejelentkezési kiszolgáló nevével. Ez az érték megtalálható a sor **47** a jegyzékfájl.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

Mentse és zárja be a fájlt.

## <a name="deploy-application"></a>Alkalmazás központi telepítése

Használja a [kubectl létrehozása] [ kubectl-create] parancsot kell futtatni az alkalmazást. A parancs elemzi az Alkalmazásjegyzék-fájl, és a meghatározott Kubernetes objektumokat létrehozni.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yml
```

Kimenet:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Alkalmazás tesztelése

A [Kubernetes szolgáltatás] [ kubernetes-service] jön létre, amely mutatja, hogy az alkalmazás az internethez. A folyamat eltarthat néhány percig. 

Figyelemmel az előrehaladást, használja a [kubectl beolvasása szolgáltatás] [ kubectl-get] parancsot a `--watch` argumentum.

```azurecli
kubectl get service azure-vote-front --watch
```

Kezdetben az *azure-vote-front* szolgáltatás *EXTERNAL-IP* értéke *pending* állapotú.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Egyszer a *külső IP-* cím megváltozott *függőben lévő* való egy *IP-cím*, használjon `CTRL-C` kubectl figyelési megszakításához. 

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Az alkalmazás megtekintéséhez navigáljon a külső IP-címet.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure szavazattal alkalmazás egy Kubernetes fürthöz AKS lett telepítve. Befejeződött a következő feladatokból áll:  

> [!div class="checklist"]
> * Kubernetes fájlok letöltése
> * Az alkalmazás futtatásához Kubernetes
> * Az alkalmazás tesztelése

A következő oktatóanyag egy Kubernetes alkalmazási és az alapjául szolgáló Kubernetes infrastruktúra méretezésével kapcsolatos további továbblépés. 

> [!div class="nextstepaction"]
> [Skála Kubernetes alkalmazás- és infrastruktúra][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
