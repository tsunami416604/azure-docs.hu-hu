---
title: Azure-on futó Kubernetes oktatóanyag – Alkalmazások skálázása
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan skálázhat csomópontokat és podokat a Kubernetesben, és hogyan valósíthatja meg a podok horizontális felskálázását.
services: container-service
ms.topic: tutorial
ms.date: 01/14/2019
ms.custom: mvc
ms.openlocfilehash: 5335cd2931fee98bb5fa59f0c2bb642a01c296f6
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593240"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazások skálázása az Azure Kubernetes Service-ben (AKS)

Ha követte az oktatóanyagokat, rendelkezik egy működő Kubernetes-fürttel az AK-ban, és üzembe helyezte a minta Azure-beli szavazási alkalmazást. Ebben az oktatóanyagban, amely egy hétrészes sorozat ötödik része, horizontálisan felskálázzuk az alkalmazás podjait, továbbá kipróbáljuk a podok automatikus méretezését. Emellett megismerjük, hogyan skálázható az Azure-beli virtuális gépek csomópontjainak száma a fürt kapacitásának beállításához a számítási feladatok futtatása érdekében. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A Kubernetes-csomópontok skálázása
> * Az alkalmazást futtató Kubernetes-podok manuális skálázása
> * Az alkalmazás kezelőfelületét futtató automatikus skálázású podok konfigurálása

A további oktatóanyagokban az Azure vote alkalmazás új verzióra frissül.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazás egy tároló-rendszerképbe van csomagolva. A rendszerkép fel lett töltve Azure Container Registryba, és létrehozott egy AK-fürtöt. Az alkalmazás ezután üzembe lett helyezve az AK-fürtön. Ha még nem tette meg ezeket a lépéseket, és követni szeretné a lépéseket, kezdje az [1. oktatóanyag – tároló lemezképek létrehozásával][aks-tutorial-prepare-app].

Ehhez az oktatóanyaghoz az Azure CLI 2.0.53 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="manually-scale-pods"></a>Podok manuális méretezése

Az előző oktatóanyagokban az Azure Vote előtérrendszerének és a Redis-példánynak a telepítésekor egyetlen replika jött létre. A fürtben található hüvelyek számának és állapotának megtekintéséhez használja a [kubectl Get][kubectl-get] parancsot a következő módon:

```console
kubectl get pods
```

A következő példa egy előtérbeli podot és egy háttérbeli podot mutat be:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Ha manuálisan szeretné módosítani a hüvelyek számát az *Azure-vote-előtérben* üzemelő példányban, használja a [kubectl Scale][kubectl-scale] parancsot. A következő példa *5*-re növeli az előtérbeli podok számát:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Futtassa újra a [kubectl][kubectl-get] , és ellenőrizze, hogy az AK létrehozza-e a további hüvelyeket. Hozzávetőleg egy perc elteltével az újabb podok elérhetők a fürtön:

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

A Kubernetes támogatja a [podok horizontális felskálázását][kubernetes-hpa] az üzemi környezetekben a podok számának a processzorhasználat vagy egyéb megadott metrikák alapján való módosítása érdekében. A [metrikai kiszolgáló][metrics-server] a Kubernetes erőforrás-felhasználás biztosítására szolgál, és a rendszer automatikusan üzembe helyezi az AK-fürtök 1,10-es és újabb verzióiban. Ha szeretné megtekinteni az AK-fürt verzióját, használja az az [AK show][az-aks-show] parancsot az alábbi példában látható módon:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> Ha az AK-fürt kevesebb, mint *1,10*, a metrikák kiszolgálója nem települ automatikusan. A telepítéséhez telepítse a `metrics-server` GitHub-tárházat, és telepítse a példaként szolgáló erőforrás-definíciókat. A YAML-definíciók tartalmának megtekintéséhez lásd: [metrikai kiszolgáló Kuberenetes 1.8 +][metrics-server-github].
> 
> ```console
> git clone https://github.com/kubernetes-incubator/metrics-server.git
> kubectl create -f metrics-server/deploy/1.8+/
> ```

Az automéretező használatához a hüvelyben és a hüvelyben lévő összes tárolónak meg kell határoznia a CPU-kérelmeket és-korlátozásokat. Az `azure-vote-front` üzemelő példányban az előtér-tároló már 0,25 CPU-t kér, 0,5 PROCESSZORral. Ezek az erőforrás-kérelmek és-korlátok az alábbi kódrészletben látható módon vannak meghatározva:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Az alábbi példa a [kubectl autoscale][kubectl-autoscale] paranccsal a hüvelyek számát az *Azure-vote-elülső* telepítésben használja. Ha az összes hüvely átlagos CPU-kihasználtsága meghaladja a kért használat 50%-át, az autoskálázás legfeljebb *10* példányra növeli a hüvelyt. Ezután legalább *3* példányt definiálnak a központi telepítéshez:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Azt is megteheti, hogy létrehoz egy jegyzékfájlt az autoskálázási viselkedés és az erőforrás-korlátok definiálásához. Az alábbi példa egy `azure-vote-hpa.yaml`nevű jegyzékfájlt mutat be.

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization


apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

Használja a `kubectl apply` az `azure-vote-hpa.yaml` jegyzékfájlban definiált autoskálázás alkalmazásához.

```
$ kubectl apply -f azure-vote-hpa.yaml
```

Az automatikus skálázó állapotának megtekintéséhez használja a `kubectl get hpa` parancsot a következőképpen:

```
$ kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Néhány perc elteltével az Azure Vote alkalmazás minimális terhelése mellett a podreplikák száma automatikusan háromra csökken. Ismét használhatja a `kubectl get pods` parancsot a szükségtelen podok eltávolításának megtekintéséhez.

## <a name="manually-scale-aks-nodes"></a>AKS-csomópontok manuális méretezése

Ha a Kubernetes-fürtöt az előző oktatóanyag parancsaival hozta létre, két csomóponttal rendelkezik. A csomópontok számát manuálisan módosíthatja, ha több vagy kevesebb, tárolókhoz kapcsolódó számítási feladatot tervez futtatni a fürtön.

A következő példában háromra növeljük a csomópontok számát a *myAKSCluster* nevű Kubernetes-fürtben. A parancs végrehajtása eltarthat néhány percig.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

Ha a fürt méretezése sikeresen megtörtént, a kimenet a következő példához hasonló:

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

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban különböző méretezési funkciókat használtunk a Kubernetes-fürtben. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az alkalmazást futtató Kubernetes-podok manuális skálázása
> * Az alkalmazás kezelőfelületét futtató automatikus skálázású podok konfigurálása
> * A Kubernetes-csomópontok manuális skálázása

A következő oktatóanyag azt mutatja be, hogyan frissítheti az alkalmazásokat a Kubernetesben.

> [!div class="nextstepaction"]
> [Alkalmazások frissítése a Kubernetesben][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://v1-13.docs.kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
