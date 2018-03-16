---
title: "Azure-lemezeket használata AKS"
description: "Azure-lemezeket használata AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a2f46aba80ad47335b7cd9b5e8d615c1d895cccb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="volumes-with-azure-disks"></a>Az Azure-lemezeket kötetek

Tároló-alapú alkalmazások gyakran kell elérni, és egy külső adatmennyiség adatok megőrzéséhez. Azure-lemezeket a külső adattár használható. Ez a cikk adatokat Azure lemezzel Azure tároló szolgáltatás (AKS)-fürtben lévő Kubernetes kötetként.

Kubernetes köteteken további információkért lásd: [Kubernetes kötetek][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Az Azure lemez létrehozása

Az Azure csatlakoztatása előtt kezelt lemez Kubernetes kötetként, a lemez már léteznie kell a AKS fürterőforrások ugyanabban az erőforráscsoportban. Ez az erőforráscsoport megkereséséhez használja a [az listájának] [ az-group-list] parancsot.

```azurecli-interactive
az group list --output table
```

Egy hasonló nevű erőforráscsoport keres `MC_clustername_clustername_locaton`, ahol clustername a AKS fürt nevét, és helye az Azure-régió, ahol a fürt telepítve lett.

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Használja a [az lemez létrehozása] [ az-disk-create] parancsot lemezt az Azure létrehozásához. 

Ez a példa használ, frissítse `--resource-group` az erőforráscsoport nevét és `--name` az Ön által választott névre.

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

A lemez létrehozása, a következőhöz hasonló kimenetnek kell megjelennie. Ez az érték szolgál, amikor a lemezt egy Kubernetes pod Lemezazonosító.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Csatlakoztassa a lemezt kötetként

A kötet konfigurálása a tároló spec csatlakoztassa azokat a pod lemezt az Azure. 

Hozzon létre egy új fájlt `azure-disk-pod.yaml` a következő tartalommal. Frissítés `diskName` nevű az újonnan létrehozott lemezt, és `diskURI` lemez azonosítóval. Továbbá jegyezze fel az a `mountPath`, amelynél a lemezt az Azure csatlakoztatva van a fogyasztanak elérési útja.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Használjon kubectl fogyasztanak létrehozásához.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Egy Azure-csatlakoztatni lemezzel most már rendelkezik egy futó pod a `/mnt/azure`.

## <a name="next-steps"></a>További lépések

További tudnivalók az Azure-lemezeket Kubernetes kötetek.

> [!div class="nextstepaction"]
> [Azure-lemezeket Kubernetes beépülő modul][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
