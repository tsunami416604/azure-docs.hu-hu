---
title: Rendszercsomópont-készletek használata az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan hozhat létre és kezelhet rendszercsomópont-készleteket az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.openlocfilehash: 01dcd6b7b366b7a1ada581ec154409ee7598e7a6
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250838"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Rendszercsomópont-készletek kezelése az Azure Kubernetes szolgáltatásban (ak)

Az Azure Kubernetes szolgáltatásban (ak) az azonos konfiguráció csomópontjai a *csomópont-készletekbe*vannak csoportosítva. A csomópont-készletek az alkalmazásokat futtató mögöttes virtuális gépeket tartalmazzák. A rendszercsomópont-készletek és a felhasználói csomópontok készletei két különböző Node Pool-mód az AK-fürtökhöz. A rendszercsomópont-készletek a kritikus rendszerhüvelyek, például a CoreDNS és a tunnelfront üzemeltetésének elsődleges célját szolgálják ki. A felhasználói csomópontok készletei az alkalmazás-hüvelyek üzemeltetésének elsődleges céljaként szolgálnak. Az Application hüvelyek azonban a rendszercsomópont-készletekbe ütemezhetők, ha csak egy készletet szeretne használni az AK-fürtben. Minden AK-fürtnek legalább egy csomóponttal rendelkező rendszercsomópont-készletet kell tartalmaznia.

> [!Important]
> Ha az AK-fürthöz egyetlen rendszercsomópont-készletet futtat éles környezetben, javasoljuk, hogy legalább három csomópontot használjon a csomópont-készlethez.

## <a name="before-you-begin"></a>Előkészületek

* Szüksége lesz az Azure CLI 2.3.1-es vagy újabb verziójára, és konfigurálva van. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="limitations"></a>Korlátozások

A rendszercsomópont-készleteket támogató AK-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek.

* Tekintse [meg a kvótákat, a virtuális gépek méretére vonatkozó korlátozásokat és a régió elérhetőségét az Azure Kubernetes szolgáltatásban (ak)][quotas-skus-regions].
* Az AK-fürtöt virtuálisgép-méretezési csoportokkal kell felépíteni.
* A csomópontok készletének neve csak kisbetűket és kisbetűs karaktereket tartalmazhat. A Linux-csomópontok készletei esetében a hossznak 1 és 12 karakter közöttinek kell lennie. Windows-csomópontos készletek esetén a hossznak 1 és 6 karakter közöttinek kell lennie.
* A csomópont-készlet üzemmódjának beállításához a 2020-03-01-es vagy újabb API-verziót kell használni. Az 2020-03-01-nál régebbi API-verziókban létrehozott fürtök csak a felhasználói csomópontok készleteit tartalmazzák, de áttelepíthetők a rendszercsomópont-készletek tárolására a következő [frissítési készlet üzemmódjának lépéseit](#update-existing-cluster-system-and-user-node-pools)követve.
* A csomópont-készlet módja kötelező tulajdonság, és az ARM-sablonok vagy a közvetlen API-hívások használata esetén explicit módon kell megadni.

## <a name="system-and-user-node-pools"></a>Rendszer-és felhasználói csomópontok készletei

A rendszercsomópont-készlet csomópontjain a **kubernetes.Azure.com/Mode címke: System**. Minden AK-fürt legalább egy rendszercsomópont-készletet tartalmaz. A rendszercsomópont-készletek a következő korlátozásokkal rendelkeznek:

* A Rendszerkészletek osType Linux rendszernek kell lennie.
* A felhasználói csomópont-készletek osType lehet Linux vagy Windows.
* A rendszerkészleteknek legalább egy csomópontot tartalmazniuk kell, és a felhasználói csomópontok készletei nulla vagy több csomópontot is tartalmazhatnak.
* A rendszercsomópont-készletek legalább 2 vCPU és 4 GB memóriával rendelkező VM SKU-t igényelnek.
* A rendszercsomópont-készleteknek legalább 30 hüvelyt kell támogatniuk, a [hüvelyek minimális és maximális értékének képlete][maximum-pods]szerint.
* A helyszíni csomópont-készletek felhasználói csomópont-készleteket igényelnek.

A következő műveleteket hajthatja végre a Node Pools használatával:

* Módosítsa a rendszercsomópont-készletet felhasználói csomópont-készletként, ha rendelkezik egy másik rendszercsomópont-készlettel, hogy a helyére kerüljön az AK-fürtben.
* Felhasználói csomópont-készlet módosítása rendszercsomópont-készletként.
* Felhasználói csomópontok készletének törlése.
* A rendszercsomópont-készleteket törölheti, ha rendelkezik egy másik rendszercsomópont-készlettel, hogy az AK-fürtön legyen a helyük.
* Az AK-fürtök több rendszercsomópont-készlettel rendelkezhetnek, és legalább egy rendszercsomópont-készletet igényelnek.
* Ha módosítani kívánja a meglévő csomópont-készletek különböző változtathatatlan beállításait, létrehozhat új csomópont-készleteket a helyükre. Az egyik példa egy új csomópont-készlet hozzáadása új maxPods-beállítással, és a régi csomópont-készlet törlése.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Új AK-fürt létrehozása rendszercsomópont-készlettel

Amikor új AK-fürtöt hoz létre, a rendszer automatikusan létrehoz egy egyetlen csomóponttal rendelkező rendszercsomópont-készletet. A kezdeti csomópont-készlet alapértelmezett értéke rendszer típusú üzemmód. Amikor új csomópont-készleteket hoz létre az az AK nodepool Add paranccsal, ezek a csomópont-készletek a felhasználói csomópontok készletei, kivéve, ha explicit módon megadja a Mode paramétert.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* régióban.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Használja az [az aks create][az-aks-create] parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy olyan rendszerkészlettel, amely egy csomópontot tartalmaz. Az éles számítási feladatokhoz győződjön meg arról, hogy legalább három csomóponttal rendelkező rendszercsomópont-készleteket használ. A művelet végrehajtása több percet is igénybe vehet.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Rendszercsomópont-készlet hozzáadása meglévő AK-fürthöz

Egy vagy több rendszercsomópont-készletet is hozzáadhat meglévő AK-fürtökhöz. A következő parancs egy üzemmód típusú rendszer csomópont-készletét adja meg, amelynek alapértelmezett száma három csomópont.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>A Node-készlet részleteinek megjelenítése

A csomópont-készlet részleteit a következő paranccsal tekintheti meg.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

A System Node-készletekhez egy **rendszer** típusú mód van definiálva, és a felhasználói csomópontok készletei esetében **felhasználói** üzemmód van megadva.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Meglévő fürtrendszer-és felhasználói csomópont-készletek frissítése

> [!NOTE]
> A rendszercsomópont-készlet üzemmódjának beállításához a 2020-03-01-es vagy újabb API-verziót kell használni. A 2020-03-01-nál régebbi API-verziókban létrehozott fürtök csak a felhasználói csomópontok készleteit tartalmazzák. Ha a rendszercsomópont-készlet funkcióit és előnyeit a régebbi fürtökön szeretné fogadni, frissítse a meglévő Node-készletek üzemmódját az alábbi parancsokkal az Azure CLI legújabb verzióján.

A rendszer-és a felhasználói csomópont-készletek üzemmódját is megváltoztathatja. A rendszercsomópont-készleteket csak akkor módosíthatja felhasználói készletre, ha már létezik egy másik rendszercsomópont-készlet az AK-fürtön.

Ez a parancs egy rendszercsomópont-készletet módosít egy felhasználói csomópont-készletre.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Ez a parancs egy felhasználói csomópont-készletet módosít egy rendszercsomópont-készletre.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Rendszercsomópont-készlet törlése

> [!Note]
> Ha a rendszercsomópont-készleteket az AK-fürtökön a 2020-03-02-es API-verzió előtt szeretné használni, vegyen fel egy új rendszercsomópont-készletet, majd törölje az eredeti alapértelmezett csomópont-

Korábban nem lehetett törölni a rendszercsomópont-készletet, amely a kezdeti alapértelmezett Node-készlet volt egy AK-fürtben. Mostantól rugalmasan törölheti a fürt összes csomópont-készletét. Mivel az AK-fürtök legalább egy rendszercsomópont-készletet igényelnek, legalább két rendszercsomópont-készlettel kell rendelkeznie az AK-fürtön, mielőtt törölni lehetne őket.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan hozhat létre és kezelhet rendszercsomópont-készleteket egy AK-fürtben. További információ a több csomópontos készletek használatáról: [több csomópontos készlet használata][use-multiple-node-pools].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
