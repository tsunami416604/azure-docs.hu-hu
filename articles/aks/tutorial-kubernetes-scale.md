---
title: "Az Azure útmutató – Scale Application Kubernetes"
description: "AKS oktatóanyag – Scale Application"
services: container-service
author: dlepow
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: ff8cf813f9c932f867413dbf7e76f949e0de2f26
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="scale-application-in-azure-container-service-aks"></a>Scale application Azure tároló szolgáltatás (AKS)

Ha korábban már követően az oktatóanyagok, egy működő Kubernetes fürt AKS rendelkezik, és telepítette, hogy a szavazati Azure app.

Ebben az oktatóanyagban öt részét nyolc, terjessze ki a három munkaállomás-csoporttal az alkalmazásban, majd próbálja pod automatikus skálázást. Is megismerheti, hogyan módosíthatja a fürt kapacitása munkaterhelések Azure virtuális gép csomópontok száma méretezése. Befejeződött a következő feladatokból áll:

> [!div class="checklist"]
> * A Kubernetes Azure csomópontok méretezése
> * Manuális skálázás Kubernetes három munkaállomás-csoporttal
> * Automatikus skálázás három munkaállomás-csoporttal fut az alkalmazás előtér konfigurálása

A következő útmutatókból az Azure szavazattal alkalmazás frissül, és az Operations Management Suite a Kubernetes fürt figyelésére konfigurálni.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagok egy tároló lemezképet, az Azure-tároló beállításjegyzék feltöltött lemezkép és a létrehozott Kubernetes fürt alkalmazás lett csomagolva. Az alkalmazás ezután futtatták a Kubernetes fürtön.

Ha nem volna ezeket a lépéseket, és szeretné követéséhez, térjen vissza a [oktatóanyag 1 – létrehozás tároló képek][aks-tutorial-prepare-app].

## <a name="scale-aks-nodes"></a>Skála AKS csomópontok

A parancsokkal az előző oktatóanyag Kubernetes fürt hozott létre, ha van egy csomópont. Ha azt tervezi, több vagy kevesebb tároló munkaterhelések a fürtön, manuálisan módosíthatja a csomópontok száma.

Az alábbi példa növeli a háromra a nevű Kubernetes fürtben lévő csomópontok száma *myK8sCluster*. A parancs néhány percet vesz igénybe.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myK8SCluster --node-count 3
```

Az eredmény az alábbihoz hasonlóan fog kinézni:

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myK8sCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="manually-scale-pods"></a>Manuálisan méretezhető, három munkaállomás-csoporttal

Így távolságban, az Azure szavazattal előtér- és a Redis-példány voltak telepítve, az egy replikához. Győződjön meg arról, hogy futtassa a [kubectl beolvasása] [ kubectl-get] parancsot.

```azurecli
kubectl get pods
```

Kimenet:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Manuálisan a három munkaállomás-csoporttal számának módosítása a `azure-vote-front` használó telepítés a [kubectl méretezési] [ kubectl-scale] parancsot. Ebben a példában az 5 szám növeli.

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

Futtatás [kubectl első három munkaállomás-csoporttal] [ kubectl-get] ellenőrzése, hogy Kubernetes hoz létre a három munkaállomás-csoporttal. Egy perc vagy tette, követően a további három munkaállomás-csoporttal használja:

```azurecli
kubectl get pods
```

Kimenet:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Automatikus skálázás három munkaállomás-csoporttal

Támogatja a Kubernetes [vízszintes pod automatikus skálázás] [ kubernetes-hpa] úgy, hogy a három munkaállomás-csoporttal attól függően, hogy a CPU-felhasználás telepítések száma vagy egyéb kiválaszthatja a metrikákat.

A autoscaler használatához a három munkaállomás-csoporttal CPU-kérelmek és meghatározott korlátozásokat kell rendelkeznie. Az a `azure-vote-front` a központi telepítés, az előtér-tároló kérelmek 0,25 Processzor 0,5 legfeljebb Processzor. A beállításokat a következőhöz hasonló:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Az alábbi példában a [kubectl automatikus skálázás] [ kubectl-autoscale] automatikus skálázásra parancsot a három munkaállomás-csoporttal száma a `azure-vote-front` központi telepítés. Itt CPU-felhasználás meghaladja az 50 %-át, ha a autoscaler növeli a három munkaállomás-csoporttal legfeljebb 10.


```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

A autoscaler állapotának megtekintéséhez futtassa a következő parancsot:

```azurecli
kubectl get hpa
```

Kimenet:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Néhány perc elteltével az Azure szavazattal alkalmazást, és minimális terhelése pod replikák száma csökken automatikusan 3.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban használt különböző méretezési szolgáltatásait a Kubernetes fürt. Feladatok kezelt tartalmazza:

> [!div class="checklist"]
> * Manuális skálázás Kubernetes három munkaállomás-csoporttal
> * Automatikus skálázás három munkaállomás-csoporttal fut az alkalmazás előtér konfigurálása
> * A Kubernetes Azure csomópontok méretezése

A következő oktatóanyag Kubernetes alkalmazás frissítésével kapcsolatos további továbblépés.

> [!div class="nextstepaction"]
> [Kubernetes tartozó alkalmazás frissítése][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
