---
title: "Azure Tárolószolgáltatás útmutató - alkalmazás központi telepítése"
description: "Azure Tárolószolgáltatás útmutató - alkalmazás központi telepítése"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 397bb22918d5b181692a42d0f4c2d87be086c534
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="run-applications-in-kubernetes"></a>A Kubernetes alkalmazások futtatásához

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Ebben az oktatóanyagban négy hét része, egy minta-alkalmazás központi telepítése egy Kubernetes fürtbe. Befejeződött a lépések az alábbiak:

> [!div class="checklist"]
> * Frissítse a jegyzékfájlt Kubernetes
> * Futtatja az alkalmazást Kubernetes
> * Az alkalmazás tesztelése

A következő útmutatókból az alkalmazás van méretezhető, frissítése és az Operations Management Suite a Kubernetes fürt figyelésére konfigurálni.

Ez az oktatóanyag azt feltételezi, hogy alapszinten megértse, Kubernetes fogalmak Kubernetes tekintse meg a részletes információkat a [Kubernetes dokumentáció](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokat egy alkalmazás egy tároló lemezképpel csomagolása, ez a Rendszerkép feltöltése Azure tároló beállításjegyzék és Kubernetes fürt létrejött. 

Az oktatóanyag teljesítéséhez szüksége az előre létrehozott `azure-vote-all-in-one-redis.yml` Kubernetes manifest fájlt. Ez a fájl egy korábbi oktatóprogram letöltött alkalmazás forráskódja. Győződjön meg arról, hogy rendelkezik-e klónozták a tárházban, és hogy módosult-könyvtárak a klónozott tárház be.

Ha nem volna ezeket a lépéseket, és szeretné követéséhez, vissza [oktatóanyag 1 – létrehozás tároló képek](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-manifest-file"></a>A jegyzékfájl frissítése

Ebben az oktatóanyagban szereplő Azure tároló beállításjegyzék (ACR) tároló-lemezkép mentéséhez használatban van. Mielőtt futtatná az alkalmazást, a ACR bejelentkezési kiszolgálónév Kubernetes jegyzékfájl frissíteni kell.

A ACR bejelentkezési kiszolgáló nevét, a [az acr lista](/cli/azure/acr#list) parancsot.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

A jegyzékfájl lett egy bejelentkezési kiszolgálónevet az előre létrehozott `microsoft`. Szövegszerkesztőben nyissa meg a fájlt. Ebben a példában a fájl a Megnyitás a `vi`.

```bash
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

Az alkalmazást a [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) paranccsal futtathatja. A parancs elemzi az Alkalmazásjegyzék-fájl, és a meghatározott Kubernetes objektumokat létrehozni.

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
```

Kimenet:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Alkalmazás tesztelése

A [Kubernetes szolgáltatás](https://kubernetes.io/docs/concepts/services-networking/service/) jön létre, amely mutatja, hogy az alkalmazás az internethez. A folyamat eltarthat néhány percig. 

A folyamat állapotának monitorozásához használja [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) parancsot a `--watch` argumentummal.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Kezdetben a **külső IP-** a a `azure-vote-front` szolgáltatás jelenik meg `pending`. Miután a külső IP-cím megváltozott `pending` való egy `IP address`, használja `CTRL-C` kubectl figyelési megszakításához.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Az alkalmazás megtekintéséhez navigáljon a külső IP-címet.

![Egy Azure-beli Kubernetes-fürt képe](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az Azure szavazattal alkalmazás Azure-tároló szolgáltatás Kubernetes fürthöz lett telepítve. Befejeződött a következő feladatokból áll:  

> [!div class="checklist"]
> * Kubernetes fájlok letöltése
> * Az alkalmazás futtatásához Kubernetes
> * Az alkalmazás tesztelése

A következő oktatóanyag egy Kubernetes alkalmazási és az alapjául szolgáló Kubernetes infrastruktúra méretezésével kapcsolatos további továbblépés. 

> [!div class="nextstepaction"]
> [Skála Kubernetes alkalmazás- és infrastruktúra](./container-service-tutorial-kubernetes-scale.md)