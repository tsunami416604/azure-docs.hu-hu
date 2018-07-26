---
title: Azure-on futó Kubernetes oktatóanyag – Alkalmazások skálázása
description: AKS-oktatóanyag – Alkalmazások skálázása
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 22f7f9aee791d315300ffdc4dc9f708a80a5baf7
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39127410"
---
# <a name="tutorial-scale-application-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazások méretezése az Azure Kubernetes Service-ben (AKS)

Ha eddig követte az oktatóanyagokat, rendelkezik egy működő Kubernetes-fürttel az AKS-ben, és üzembe helyezte az Azure-szavazóalkalmazást.

Ebben az oktatóanyagban, amely egy hétrészes sorozat ötödik része, horizontálisan felskálázzuk az alkalmazás podjait, továbbá kipróbáljuk a podok automatikus méretezését. Emellett megismerjük, hogyan skálázható az Azure-beli virtuális gépek csomópontjainak száma a fürt kapacitásának beállításához a számítási feladatok futtatása érdekében. Az eddig végrehajtott feladatok a következők:

> [!div class="checklist"]
> * A Kubernetes Azure-csomópontok skálázása
> * Kubernetes-podok manuális méretezése
> * Az alkalmazás előtérrendszerét futtató podok automatikus méretezésének konfigurálása

Az ezt követő oktatóanyagokban az Azure Vote alkalmazást egy új verzióra frissítjük.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt. Az alkalmazást ezután a Kubernetes-fürtön futtattuk.

Ha ezeket a lépéseket még nem hajtotta végre, de szeretne velünk tartani, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

## <a name="scale-aks-nodes"></a>AKS-csomópontok skálázása

Ha a Kubernetes-fürtöt az előző oktatóanyagban szereplő parancsokkal hozta létre, egy csomóponttal rendelkezik. A csomópontok számát manuálisan módosíthatja, ha több vagy kevesebb, tárolókhoz kapcsolódó számítási feladatot tervez futtatni a fürtön.

A következő példában háromra növeljük a csomópontok számát a *myAKSCluster* nevű Kubernetes-fürtben. A parancs végrehajtása eltarthat néhány percig.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myAKSCluster --node-count 3
```

Az eredmény az alábbihoz hasonlóan fog kinézni:

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="manually-scale-pods"></a>Podok manuális méretezése

Eddig már telepítettük az Azure Vote előtérrendszerét és a Redis-példányokat, mindegyiket egyetlen replikával. Ennek ellenőrzéséhez futtassa a [kubectl get][kubectl-get] parancsot.

```azurecli
kubectl get pods
```

Kimenet:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Manuálisan módosítsa az `azure-vote-front` üzemelő példány podjainak számát a [kubectl scale][kubectl-scale] paranccsal. Példánkban 5-re növeljük a számot.

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

Futtassa a [kubectl get pods][kubectl-get] parancsot annak ellenőrzéséhez, hogy a Kubernetes létrehozza-e a podokat. Hozzávetőleg egy perc elteltével az újabb podok is futnak:

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

## <a name="autoscale-pods"></a>Podok automatikus méretezése

A Kubernetes támogatja a [podok horizontális felskálázását][kubernetes-hpa] a környezetekben a podok számának a processzorhasználat vagy egyéb megadott metrikák alapján való módosítása érdekében. A [Metrics Server][metrics-server] erőforrás-használatot biztosít a Kubernetes számára. A Metrics Server üzeme helyezéséhez klónozza a(z) `metrics-server` GitHub-adattárat, és telepítse az erőforrás-definíciók példáit. Ezeknek a YAML-definícióknak a tartalmát a [Metrics Server for Kuberenetes 1.8+][metrics-server-github] cikkben találja.

```console
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f metrics-server/deploy/1.8+/
```

Az automatikus méretező használatához definiálni kell a podok processzorigényét és korlátait. Az `azure-vote-front` üzemi környezetben az előtértároló processzorigénye 0,25, a felső korlát pedig 0,5. A beállítások a következőhöz hasonlóak:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Az alábbi példában a [kubectl autoscale][kubectl-autoscale] paranccsal automatikusan skálázzuk a podok számát az `azure-vote-front` környezetben. Amennyiben a processzorhasználat itt meghaladja az 50%-ot, az automatikus méretező legfeljebb 10-ig növeli a podok számát.

```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Az automatikus méretező állapotának megtekintéséhez futtassa az alábbi parancsot:

```azurecli
kubectl get hpa
```

Kimenet:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Néhány perc elteltével az Azure Vote alkalmazás minimális terhelése mellett a podreplikák száma automatikusan 3-ra csökken.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban különböző méretezési funkciókat használtunk a Kubernetes-fürtben. A következők feladatokat hajtottuk végre:

> [!div class="checklist"]
> * Kubernetes-podok manuális méretezése
> * Az alkalmazás előtérrendszerét futtató podok automatikus méretezésének konfigurálása
> * A Kubernetes Azure-csomópontok skálázása

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan frissíthetők az alkalmazások a Kubernetesben.

> [!div class="nextstepaction"]
> [Alkalmazások frissítése a Kubernetesben][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
