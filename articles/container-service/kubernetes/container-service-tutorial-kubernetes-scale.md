---
title: (ELAVULT) Az Azure Container Service-oktatóanyag – alkalmazás skálázása
description: Azure Container Service-oktatóanyag – Alkalmazás skálázása
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b16682535e7311648e832286e1e876ee0e694712
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993076"
---
# <a name="deprecated-scale-kubernetes-pods-and-kubernetes-infrastructure"></a>(ELAVULT) Kubernetes-podok és -infrastruktúra méretezése

> [!TIP]
> Ebben az oktatóanyagban, amely használja az Azure Kubernetes Service-ben, lásd: a frissített verzió [oktatóanyag: alkalmazások skálázása az Azure Kubernetes Service (AKS)](../../aks/tutorial-kubernetes-scale.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Ha eddig követte az oktatóanyagokat, rendelkezik egy működő Kubernetes-fürttel az Azure Container Service-ben, és üzembe helyezte az Azure-szavazóalkalmazást. 

Ebben az oktatóanyagban, amely egy hétrészes sorozat ötödik része, horizontálisan felskálázzuk az alkalmazás podjait, továbbá kipróbáljuk a podok automatikus méretezését. Emellett megismerjük, hogyan skálázható az Azure-beli virtuális gépek ügynökcsomópontjainak száma a fürt kapacitásának beállításához a számítási feladatok futtatása érdekében. Az eddig végrehajtott feladatok a következők:

> [!div class="checklist"]
> * Kubernetes-podok manuális méretezése
> * Az alkalmazás előtérrendszerét futtató podok automatikus méretezésének konfigurálása
> * A Kubernetes Azure-ügynökcsomópontok méretezése

Az ezt követő oktatóanyagokban frissítjük az Azure Vote alkalmazást, és a Log Analyticst a Kubernetes-fürt monitorozására konfiguráljuk.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt. Az alkalmazást ezután a Kubernetes-fürtön futtattuk. 

Ha ezeket a lépéseket még nem hajtotta végre, de szeretne velünk tartani, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása](./container-service-tutorial-kubernetes-prepare-app.md) részhez. 

## <a name="manually-scale-pods"></a>Podok manuális méretezése

Eddig már telepítettük az Azure Vote előtérrendszerét és a Redis-példányokat, mindegyiket egyetlen replikával. Ennek az ellenőrzéséhez futtassa a [kubectl get](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot.

```azurecli-interactive
kubectl get pods
```

Kimenet:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Manuálisan módosítsa az `azure-vote-front` üzemelő példány podjainak számát a [kubectl scale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale) paranccsal. Példánkban 5-re növeljük a számot.

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

Futtassa a [kubectl get pods](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) parancsot annak ellenőrzéséhez, hogy a Kubernetes létrehozza-e a podokat. Hozzávetőleg egy perc elteltével az újabb podok is futnak:

```azurecli-interactive
kubectl get pods
```

Kimenet:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Podok automatikus méretezése

A Kubernetes támogatja a [podok horizontális felskálázását](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) az üzemi környezetekben a podok számának a processzorhasználat vagy egyéb megadott metrikák alapján való módosítása érdekében. 

Az automatikus méretező használatához definiálni kell a podok processzorigényét és korlátait. Az `azure-vote-front` üzemi környezetben az előtértároló processzorigénye 0,25, a felső korlát pedig 0,5. A beállítások a következőhöz hasonlóak:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Az alábbi példában a [kubectl autoscale](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale) paranccsal automatikusan skálázzuk a podok számát az `azure-vote-front` üzemi környezetben. Amennyiben a processzorhasználat itt meghaladja az 50%-ot, az automatikus méretező legfeljebb 10-ig növeli a podok számát.


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Az automatikus méretező állapotának megtekintéséhez futtassa az alábbi parancsot:

```azurecli-interactive
kubectl get hpa
```

Kimenet:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Néhány perc elteltével az Azure Vote alkalmazás minimális terhelése mellett a podreplikák száma automatikusan 3-ra csökken.

## <a name="scale-the-agents"></a>Az ügynökök méretezése

Ha a Kubernetes-fürtöt az előző oktatóanyagban lévő alapértelmezett parancsokkal hozta létre, a három ügynökcsomóponttal rendelkezik. Az ügynökök számát manuálisan módosíthatja, ha több vagy kevesebb, tárolókhoz kapcsolódó számítási feladatot tervez futtatni a fürtön. Futtassa az [az acs scale](/cli/azure/acs#az-acs-scale) parancsot, és adja meg az ügynökök számát a `--new-agent-count` paraméterrel.

A következő példában 4-re növeljük az ügynökcsomópontok számát a *myK8sCluster* nevű Kubernetes-fürtben. A parancs végrehajtása eltarthat néhány percig.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

A parancs kimenete az ügynökcsomópontok számát az `agentPoolProfiles:count` értékében adja meg:

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban különböző méretezési funkciókat használtunk a Kubernetes-fürtben. A következők feladatokat hajtottuk végre:

> [!div class="checklist"]
> * Kubernetes-podok manuális méretezése
> * Az alkalmazás előtérrendszerét futtató podok automatikus méretezésének konfigurálása
> * A Kubernetes Azure-ügynökcsomópontok méretezése

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan frissíthetők az alkalmazások a Kubernetesben.

> [!div class="nextstepaction"]
> [Alkalmazások frissítése a Kubernetesben](./container-service-tutorial-kubernetes-app-update.md)

