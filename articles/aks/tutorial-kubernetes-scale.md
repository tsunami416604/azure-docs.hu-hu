---
title: Azure-on futó Kubernetes oktatóanyag – Alkalmazások skálázása
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan skálázhat csomópontokat és podokat a Kubernetesben, és hogyan valósíthatja meg a podok horizontális felskálázását.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 5a942aa10f36df55ac232defa610102700e3995b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614201"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazások skálázása az Azure Kubernetes Service (AKS)

Ha követte az oktatóanyagokat, rendelkezik egy működő Kubernetes-fürttel az aks-ben és üzembe helyezte a mintául szolgáló Azure-szavazóalkalmazást. Ebben az oktatóanyagban, amely egy hétrészes sorozat ötödik része, horizontálisan felskálázzuk az alkalmazás podjait, továbbá kipróbáljuk a podok automatikus méretezését. Emellett megismerjük, hogyan skálázható az Azure-beli virtuális gépek csomópontjainak száma a fürt kapacitásának beállításához a számítási feladatok futtatása érdekében. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A Kubernetes-csomópontok skálázása
> * Az alkalmazást futtató Kubernetes-podok manuális skálázása
> * Az alkalmazás kezelőfelületét futtató automatikus skálázású podok konfigurálása

További oktatóanyagok az Azure Vote alkalmazást egy új verzióra frissül.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe. A rendszerképet feltöltöttük az Azure Container Registrybe, és létrehozott egy AKS-fürtöt. Az AKS-fürtöt, majd helyeztünk üzembe az alkalmazást. Ha még nem végezte el ezeket a lépéseket, és szeretné követni, kezdje [1. oktatóanyag – tárolórendszerképek létrehozása][aks-tutorial-prepare-app].

Ehhez az oktatóanyaghoz, hogy futtat-e az Azure CLI 2.0.53 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="manually-scale-pods"></a>Podok manuális méretezése

Az előző oktatóanyagokban az Azure Vote előtérrendszerének és a Redis-példánynak a telepítésekor egyetlen replika jött létre. Száma és a fürt podok állapotának megtekintéséhez használja a [kubectl get][kubectl-get] paranccsal a következőképpen:

```console
kubectl get pods
```

A következő példa egy előtérbeli podot és egy háttérbeli podot mutat be:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

A podok számát manuálisan módosíthatja a *azure-vote-front* központi telepítését, használatát a [kubectl scale][kubectl-scale] parancsot. A következő példa *5*-re növeli az előtérbeli podok számát:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Futtatás [kubectl get pods][kubectl-get] , hogy az AKS hoz létre az újabb podok megerősítése érdekében. Hozzávetőleg egy perc elteltével az újabb podok elérhetők a fürtön:

```console
$ kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Podok automatikus méretezése

Kubernetes támogatja [podok horizontális felskálázását][kubernetes-hpa] to adjust the number of pods in a deployment depending on CPU utilization or other select metrics. The [Metrics Server][metrics-server] kubernetes erőforrás-használatot biztosít és automatikusan telepítve van az AKS-fürtök 1.10 és újabb verziók. Az AKS-fürt verziójának megtekintéséhez használja a [az aks show][az-aks-show] parancsot, az alábbi példában látható módon:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion
```

Ha az AKS-fürt verziószáma *1.10*-esnél régebbi, telepítse a Metrics Servert, máskülönben hagyja ki ezt a lépést. Telepítse, klónozza a `metrics-server` GitHub-adattárat, és telepítse az Példa erőforrás-definíciókban. Ezek a YAML-definíciók tartalmának megtekintése: [Kuberenetes 1.8-as + metrikák kiszolgáló][metrics-server-github].

```console
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f metrics-server/deploy/1.8+/
```

Az automatikus méretező használatához a podok és a podokat megszünteti az összes tárolót processzorigényét és korlátait definiálva kell rendelkeznie. Az a `azure-vote-front` a központi telepítés, az előtér-tároló már kérelmek 0,25 CPU-használat, a felső korlát pedig 0,5 Processzor. Ezen erőforrás-kérelmek és korlátozások vannak meghatározva, a következő példa kódrészletben látható módon:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Az alábbi példában a [kubectl autoscale][kubectl-autoscale] paranccsal automatikusan skálázzuk a podok számát az *azure-vote-front* központi telepítés. CPU-felhasználás meghaladja az 50 %-át, ha automatikus méretező növeli a podok legfeljebb *10* példányok. Legalább *3* példányok majd van definiálva a központi telepítés:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Az automatikus skálázó állapotának megtekintéséhez használja a `kubectl get hpa` parancsot a következőképpen:

```
$ kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Néhány perc elteltével az Azure Vote alkalmazás minimális terhelése mellett a podreplikák száma automatikusan háromra csökken. Ismét használhatja a `kubectl get pods` parancsot a szükségtelen podok eltávolításának megtekintéséhez.

## <a name="manually-scale-aks-nodes"></a>AKS-csomópontok manuális méretezése

Ha a Kubernetes-fürtöt az előző oktatóanyagban szereplő parancsokkal hozta létre, egy csomóponttal rendelkezik. A csomópontok számát manuálisan módosíthatja, ha több vagy kevesebb, tárolókhoz kapcsolódó számítási feladatot tervez futtatni a fürtön.

A következő példában háromra növeljük a csomópontok számát a *myAKSCluster* nevű Kubernetes-fürtben. A parancs végrehajtása eltarthat néhány percig.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

Ha a fürt sikeresen van méretezve, a kimenet a következő példához hasonló:

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

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban különböző méretezési funkciókat használtunk a Kubernetes-fürtben. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az alkalmazást futtató Kubernetes-podok manuális skálázása
> * Az alkalmazás kezelőfelületét futtató automatikus skálázású podok konfigurálása
> * A Kubernetes-csomópontokon manuális méretezése

A következő oktatóanyag azt mutatja be, hogyan frissítheti az alkalmazásokat a Kubernetesben.

> [!div class="nextstepaction"]
> [Alkalmazások frissítése a Kubernetesben][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://v1-12.docs.kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
