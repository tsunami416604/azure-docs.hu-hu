---
title: Statikus kötet létrehozása podok számára az Azure Kubernetes-szolgáltatásban (AKS)
description: Megtudhatja, hogyan hozhat létre manuálisan kötetet az Azure Kubernetes-szolgáltatás (AKS) podjával való használatra az Azure Kubernetes szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 17795ae696c0d710f099a5c21aa754fc925953ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047943"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Kötet manuális létrehozása és használata Azure-lemezekkel az Azure Kubernetes-szolgáltatásban (AKS)

A tárolóalapú alkalmazásoknak gyakran kell hozzáférniük és meg kell őrizniük az adatokat egy külső adatköteten. Ha egy pod nak szüksége van a tároláshoz való hozzáférésre, azure-lemezek használatával egy natív kötetet az alkalmazás használata. Ez a cikk bemutatja, hogyan hozhat létre manuálisan egy Azure-lemezt, és csatolja azt egy pod az AKS-ben.

> [!NOTE]
> Egy Azure-lemez egyszerre csak egyetlen podra csatlakoztatható. Ha egy állandó kötetet több podközött kell megosztania, használja az [Azure Files szolgáltatást.][azure-files-volume]

A Kubernetes-kötetekről az [AKS-ben lévő alkalmazások tárolási beállításai][concepts-storage]című témakörben talál további információt.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="create-an-azure-disk"></a>Azure-lemez létrehozása

Amikor létrehoz egy Azure-lemezt az AKS-hez, létrehozhatja a lemezerőforrást a csomóponterőforrás-csoportban. **node** Ez a megközelítés lehetővé teszi, hogy az AKS-fürt hozzáférjen és kezelje a lemezerőforrást. Ha ehelyett egy külön erőforráscsoportban hozza létre a lemezt, az Azure Kubernetes Service `Contributor` (AKS) egyszerű szolgáltatása a fürt szerepkört kell adnia a lemez erőforráscsoportjának. Azt is megteheti, hogy a rendszer hozzárendelt felügyelt identitás engedélyek helyett a szolgáltatás névsor. További információ: [Felügyelt identitások használata.](use-managed-identity.md)

Ebben a cikkben hozza létre a lemezt a csomópont erőforráscsoportban. Először az erőforráscsoport nevét az [az aks][az-aks-show] `--query nodeResourceGroup` show paranccsal kapja meg, és adja hozzá a lekérdezési paramétert. A következő példa a *myResourceGroup*erőforráscsoport nevű erőforráscsoport ban az AKS-fürt *myAKSCluster* nevű csomóponterőforrás-csoportját kapja:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Most hozzon létre egy lemezt az [az lemez létrehozása][az-disk-create] paranccsal. Adja meg az előző parancsban kapott csomóponterőforrás-csoport nevét, majd a lemezerőforrás nevét, például a *myAKSDisk*nevet. A következő példa létrehoz egy *20*GiB-lemezt, és a lemez azonosítóját a létrehozás után adja ki. Ha létre kell hoznia egy lemezt a Windows Server-tárolókhoz `--os-type windows` (jelenleg előzetes verzióban az AKS-ben), adja hozzá a paramétert a lemez megfelelő formázásához.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Az Azure-lemezek számlázása a termékváltozat egy adott méretre. Ezek a termékkiállítások az S4 vagy P4 lemezek 32GiB-jétől az S80 vagy P80 lemezekhez (előzetes verzióban) 32TiB-ig terjednek. A prémium szintű felügyelt lemez átviteli és IOPS-teljesítménye a termékváltozattól és az AKS-fürt csomópontjainak méretétől is függ. Lásd: [A felügyelt lemezek díjszabása és teljesítménye.][managed-disk-pricing-performance]

A lemezerőforrás-azonosító akkor jelenik meg, ha a parancs sikeresen befejeződött, ahogy az a következő példakimeneten is látható. Ez a lemezazonosító a lemez csatlakoztatására szolgál a következő lépésben.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Lemez csatlakoztatása kötetként

Az Azure-lemez csatlakoztatásához a pod, konfigurálja a kötetet `azure-disk-pod.yaml` a tárolóban spec. Hozzon létre egy új nevű fájlt a következő tartalommal. Frissítés `diskName` az előző lépésben létrehozott lemez nevével és `diskURI` a lemezlétrehozás parancs kimenetén látható lemezazonosítóval. Ha szükséges, `mountPath`frissítse a , amely az elérési utat, ahol az Azure-lemez csatlakoztatva van a pod. Windows Server-tárolók esetén (jelenleg előzetes verzióban az AKS-ben) adjon meg egy *mountPath-ot* a Windows elérési út konvenciója, például a *"D:"* használatával.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
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

A `kubectl` parancs segítségével hozza létre a pod.

```console
kubectl apply -f azure-disk-pod.yaml
```

Most már rendelkezik egy futó pod `/mnt/azure`egy Azure-lemez csatlakoztatva. A lemez `kubectl describe pod mypod` sikeres csatlakoztatásának ellenőrzéséhez használható. A következő kondenzált példa kimenet a tárolóba szerelt kötetet mutatja:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>További lépések

A kapcsolódó gyakorlati tanácsok értése: [Gyakorlati tanácsok a tároláshoz és a biztonsági mentések az AKS-ben.][operator-best-practices-storage]

Az AKS-fürtök Azure-lemezekkel való interakciójáról további információt az [Azure Disks Kubernetes beépülő moduljában talál.][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
