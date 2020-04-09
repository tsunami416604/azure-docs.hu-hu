---
title: Rendszercsomópont-készletek használata az Azure Kubernetes-szolgáltatásban (AKS)
description: Ismerje meg, hogyan hozhat létre és kezelhet rendszercsomópont-készleteket az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 04/06/2020
ms.openlocfilehash: ef5400f19f68fd2da45776d220e17777f58e46e6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986316"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Rendszercsomópont-készletek kezelése az Azure Kubernetes-szolgáltatásban (AKS)

Az Azure Kubernetes-szolgáltatásban (AKS) az azonos konfigurációs csomópontok *csomópontkészletekbe*vannak csoportosítva. A csomópontkészletek tartalmazzák az alkalmazásokat futtató mögöttes virtuális gépeket. A rendszercsomópont-készletek és a felhasználói csomópontkészletek két különböző csomópontkészlet-mód az AKS-fürtökhöz. A rendszercsomópont-készletek olyan alapvető rendszerszolgáltatásokat üzemeltetnek, mint például a CoreDNS. A felhasználói csomópontkészletek, ahol az alkalmazás-specifikus podok. Minden AKS-fürtnek legalább egy legalább egy csomónkkal rendelkező rendszercsomópont-készletet kell tartalmaznia. Ha az AKS-fürthöz egyetlen rendszercsomópont-készletet futtat, azt javasoljuk, hogy legalább három csomópontot használjon a csomópontkészlethez. 

## <a name="before-you-begin"></a>Előkészületek

* Az Azure CLI 2.3.1-es vagy újabb verziójára van szükség telepítve és konfigurálva. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="limitations"></a>Korlátozások

A rendszercsomópont-készleteket támogató AKS-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek.

* Lásd: [Kvóták, virtuálisgép-méretkorlátozások és a régió elérhetősége az Azure Kubernetes szolgáltatásban (AKS).][quotas-skus-regions]
* Az AKS-fürt kell építeni a virtuális gép méretezési készletek a virtuális gép típusa.
* A csomópontkészlet neve csak kisalfanumerikus karaktereket tartalmazhat, és kisbetűvel kell kezdődnie. Linux csomópontkészletek esetén a hossznak 1 és 12 karakter között kell lennie. Windows-csomópontkészletek esetén a hossznak 1 és 6 karakter között kell lennie.

## <a name="system-and-user-node-pools"></a>Rendszer- és felhasználói csomópontkészletek

A rendszercsomópont-készletcsomópont-csomópontok címkéje **kubernetes.azure.com/mode: rendszer**. Minden AKS-fürt legalább egy rendszercsomópont-készletet tartalmaz. A rendszercsomópont-készletekre a következő korlátozások vonatkoznak:

* Az osType rendszerkészleteknek Linuxnak kell lenniük.
* Az osType felhasználói csomópontkészletek linuxos vagy Windows-típusúak lehetnek.
* A rendszerkészleteknek legalább egy csomópontot kell tartalmazniuk, és a felhasználói csomópontkészletek nulla vagy több csomópontot tartalmazhatnak.
* A rendszercsomópont-készletek hez legalább 2 vCPU-t és 4 GB memóriát igénylő virtuálisgép-termékváltozat szükséges.
* A rendszercsomópont-készleteknek legalább 30 podot kell támogatniuk a [podok minimális és maximális értékképletében leírtak szerint.][maximum-pods]
* A direkt csomópontkészletek felhasználói csomópontkészleteket igényelnek.

A következő műveleteket teheti meg csomópontkészletekkel:

* Módosítsa a rendszercsomópont-készletet felhasználói csomópontkészletté, feltéve, hogy egy másik rendszercsomópont-készlet telje meg az AKS-fürtben.
* Felhasználói csomópontkészlet rendszercsomópont-készletté történő módosítása.
* Felhasználói csomópontkészletek törlése.
* Törölheti a rendszercsomópont-készleteket, feltéve, hogy egy másik rendszercsomópont-készlet telje el az AKS-fürtben.
* Egy AKS-fürt több rendszercsomópont-készlettel is rendelkezhet, és legalább egy rendszercsomópont-készletet igényel.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Új AKS-fürt létrehozása rendszercsomópont-készlettel

Új AKS-fürt létrehozásakor automatikusan létrehoz egy egyetlen csomópontos rendszercsomópont-készletet. A kezdeti csomópontkészlet alapértelmezés szerint rendszertípusú. Amikor új csomópontkészleteket hoz létre az aks nodepool add, ezek a csomópontkészletek felhasználói csomópontkészletek, kivéve, ha kifejezetten megadja a mód paramétert.

A következő példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* régióban.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Használja az [az aks create][az-aks-create] parancsot egy AKS-fürt létrehozásához. A következő példa létrehoz egy *myAKSCluster* nevű fürtöt, amelyegy csomópontot tartalmazó rendszerkészlettel rendelkezik. Az éles számítási feladatok hoz győződjön meg arról, hogy legalább három csomóponton rendszercsomópont-készleteket használ. Ez a művelet több percig is eltarthat.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Rendszercsomópont-készlet hozzáadása meglévő AKS-fürthöz

Egy vagy több rendszercsomópont-készletet adhat hozzá a meglévő AKS-fürtökhöz. A következő parancs hozzáad egy módtípusú csomópontkészletet, amelynek alapértelmezett száma három csomópont.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>A csomópontkészlet részleteinek megjelenítése

A csomópontkészlet adatait a következő paranccsal ellenőrizheti.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

A rendszercsomópont-készletekhez **rendszertípusú** mód van definiálva, a felhasználói csomópontkészletekhez **pedig Felhasználó** mód.

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

## <a name="update-system-and-user-node-pools"></a>Rendszer- és felhasználói csomópontkészletek frissítése

A rendszer- és a felhasználói csomópontkészletek módjait is módosíthatja. A rendszercsomópont-készletet csak akkor módosíthatja felhasználói készletté, ha az AKS-fürtön már létezik egy másik rendszercsomópont-készlet.

Ez a parancs a rendszercsomópont-készletet felhasználói csomópontkészletté módosítja.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Ez a parancs egy felhasználói csomópontkészletet rendszercsomópont-készletté változtat.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Rendszercsomópont-készlet törlése

> [!Note]
> Ha az API 2020-03-02-es verziójá előtt szeretné használni a rendszercsomópont-készleteket az AKS-fürtökön, adjon hozzá egy új rendszercsomópont-készletet, majd törölje az eredeti alapértelmezett csomópontkészletet.

Korábban nem lehetett törölni a rendszercsomópont-készletet, amely az AKS-fürt kezdeti alapértelmezett csomópontkészlete volt. Most már rugalmasan törölheti a csomópontok készletét a fürtökből. Mivel az AKS-fürtöknek legalább egy rendszercsomópont-készletre van szükségük, legalább két rendszercsomópont-készlettel kell rendelkeznie az AKS-fürtön, mielőtt törölheti az egyiket.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan hozhat létre és kezelhet rendszercsomópont-készleteket egy AKS-fürtben. A több csomópontkészlet használatáról további információt a Több csomópontkészlet használata című [témakörben talál.][use-multiple-node-pools]

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
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: faq.md#why-cant-i-set-maxpods-below-30