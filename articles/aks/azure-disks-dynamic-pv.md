---
title: Állandó kötet dinamikus létrehozása és használata azure-lemezekkel az Azure Kubernetes-szolgáltatásban (AKS)
description: Megtudhatja, hogyan hozhat létre dinamikusan állandó kötetet az Azure Kubernetes-szolgáltatásban (AKS) az Azure Kubernetes-szolgáltatásban
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 37fea36567866af69e832a1f7e3caff2a68477a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596963"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Állandó kötet dinamikus létrehozása és használata azure-lemezekkel az Azure Kubernetes-szolgáltatásban (AKS)

Az állandó kötet egy olyan tárolódarabot jelöl, amely a Kubernetes-podokkal való használatra lett kiépítve. Egy állandó kötetet egy vagy több podok, és dinamikusan vagy statikusan kiépített. Ez a cikk bemutatja, hogyan hozhat létre dinamikusan állandó köteteket az Azure-lemezekkel egyetlen pod egy Azure Kubernetes-fürt (AKS) fürtben.

> [!NOTE]
> Egy Azure-lemez csak akkor csatlakoztatható *az Access mód* típusa *ReadWriteOnce*, amely elérhetővé teszi, hogy csak egy pod az AKS-ben. Ha egy állandó kötetet több podközött kell megosztania, használja az [Azure Files szolgáltatást.][azure-files-pvc]

A Kubernetes-kötetekről az [AKS-ben lévő alkalmazások tárolási beállításai][concepts-storage]című témakörben talál további információt.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="built-in-storage-classes"></a>Beépített tárolási osztályok

A tárolási osztály segítségével határozható meg, hogy egy egység nyikorosan hogyan jön létre dinamikusan egy állandó kötettel. A Kubernetes-tárolóosztályokról további információt a [Kubernetes-tárolási osztályok című témakörben talál.][kubernetes-storage-classes]

Minden AKS-fürt két előre létrehozott tárolási osztályt tartalmaz, amelyek mindegyike azure-lemezekkel működik:

* Az *alapértelmezett* tárolási osztály egy szabványos Azure-lemezt tartalmaz.
    * A normál tárolást HDD-k biztosítják, és költséghatékony tárolást biztosít, miközben továbbra is teljesít. A standard lemezek ideális megoldást jelentenek költséghatékony fejlesztési és tesztelési számítási feladatokhoz.
* A *felügyelt prémium szintű* tárolási osztály egy prémium szintű Azure-lemezt biztosít.
    * A prémium lemezek SSD-alapú, nagy teljesítményű, kis késleltetésű lemezek. Az éles számítási feladatokat futtató virtuális gépek esetén érdemes a használatuk mellett dönteni. Ha a fürt AKS-csomópontjai prémium szintű tárhelyet használnak, válassza ki a *felügyelt prémium szintű* osztályt.
    
Ezek az alapértelmezett tárolási osztályok nem teszik lehetővé a kötet méretének frissítését a létrehozás után. Ennek a képességnek a engedélyezéséhez adja hozzá az *allowVolumeExpansion: true* sort az egyik alapértelmezett tárolási osztályhoz, vagy hozzon létre saját egyéni tárolási osztályt. A `kubectl edit sc` parancs segítségével szerkeszthet egy meglévő tárolási osztályt. A tárolási osztályokról és a saját létrehozása című témakörben további információt [az AKS-ben futó alkalmazások tárolási beállításai című témakörben talál.][storage-class-concepts]

Használja a [kubectl get sc][kubectl-get] parancsot az előre létrehozott tárolási osztályok megtekintéséhez. A következő példa az AKS-fürtön belül elérhető, létrehozás előtti tárolási osztályokat mutatja be:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Állandó kötet jogcímek vannak megadva a GiB, de az Azure által felügyelt lemezek által számlázott termékváltozat egy adott méretre. Ezek a termékkiállítások az S4 vagy P4 lemezek 32GiB-jétől az S80 vagy P80 lemezekhez (előzetes verzióban) 32TiB-ig terjednek. A prémium szintű felügyelt lemez átviteli és IOPS-teljesítménye a termékváltozattól és az AKS-fürt csomópontjainak méretétől is függ. További információt a [Felügyelt lemezek díjszabása és teljesítménye című témakörben talál.][managed-disk-pricing-performance]

## <a name="create-a-persistent-volume-claim"></a>Állandó kötetjogcím létrehozása

Az állandó kötetjogcím (PVC) segítségével automatikusan kiépítheti a tárolási osztályon alapuló tárhelyet. Ebben az esetben a PERPV-k az előre létrehozott tárolási osztályok egyikével hozhat létre egy szabványos vagy prémium szintű Azure felügyelt lemezt.

Hozzon létre `azure-premium.yaml`egy nevű fájlt, és másolja a következő jegyzékfájlba. A jogcím egy `azure-managed-disk` *5 GB* méretű, *ReadWriteOnce* hozzáféréssel rendelkező lemezt kér. A *felügyelt prémium szintű* tárolási osztály tárolási osztályként van megadva.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Ha olyan lemezt szeretne létrehozni, amely szabványos tárhelyet használ, a felügyelt prémium verzió helyett `storageClassName: default` *használja.*

Hozza létre az állandó kötetjogcímet a [kubectl apply][kubectl-apply] paranccsal, és adja meg az *azure-premium.yaml* fájlt:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Az állandó kötet használata

Az állandó kötetjogcím létrehozása és a lemez sikeres kiépítése után létrehozható egy pod a lemezhez való hozzáféréssel. A következő jegyzékfájl létrehoz egy alapvető NGINX pod, amely az *azure-felügyelt lemez* `/mnt/azure`nevű állandó kötetjogcím használatával csatlakoztatja az Azure-lemezt az elérési úton. Windows Server-tárolók esetén (jelenleg előzetes verzióban az AKS-ben) adjon meg egy *mountPath-ot* a Windows elérési út konvenciója, például a *"D:"* használatával.

Hozzon létre `azure-pvc-disk.yaml`egy nevű fájlt, és másolja a következő jegyzékfájlba.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Hozza létre a pod a [kubectl alkalmazni parancsot,][kubectl-apply] ahogy az a következő példában látható:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Most már rendelkezik egy futó pod az `/mnt/azure` Azure-lemez csatlakoztatva a címtárban. Ez a konfiguráció látható, amikor `kubectl describe pod mypod`ellenőrzi a pod keresztül, amint azt a következő tömörített példa:

```console
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Állandó kötet biztonsági másolatot kell kapnia

Az állandó kötetben lévő adatok biztonsági másolatot készíteni, pillanatképet készíthet a kötet felügyelt lemezéről. Ezután használhatja ezt a pillanatképet egy visszaállított lemez létrehozásához, és csatolja a podok az adatok visszaállításának eszközeként.

Először a kötet nevét `kubectl get pvc` a paranccsal, például a PVC nevű *azure-felügyelt lemez:*

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Ez a kötetnév képezi az alapul szolgáló Azure-lemeznevét. A lemezazonosító lekérdezése az [az lemezlistával,][az-disk-list] és adja meg a PVC-kötet nevét, ahogy az a következő példában látható:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

A lemezazonosító segítségével hozzon létre egy pillanatkép [lemezt az pillanatkép létrehozása .][az-snapshot-create] A következő példa egy *pvcSnapshot* nevű pillanatképet hoz létre ugyanabban az erőforráscsoportban, mint az AKS-fürt (*MC_myResourceGroup_myAKSCluster_eastus).* Engedélyproblémák merülhetnek fel, ha pillanatképeket hoz létre, és olyan erőforráscsoportokban hoz létre lemezeket, amelyekhez az AKS-fürt nem fér hozzá.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

A lemezen lévő adatok mennyiségétől függően eltarthat néhány percig a pillanatkép létrehozása.

## <a name="restore-and-use-a-snapshot"></a>Pillanatkép visszaállítása és használata

A lemez visszaállításához és kubernetes-podkal való használatához forrásként használja a pillanatképet, amikor lemezt hoz létre [az az lemez létrehozása esetén.][az-disk-create] Ez a művelet megőrzi az eredeti erőforrást, ha ezután hozzá kell férned az eredeti adatpillanatképhez. A következő példa egy *pvcRestored* nevű lemezt hoz létre a *pvcSnapshot*nevű pillanatképből:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Ha a visszaállított lemezt podtal szeretné használni, adja meg a lemez azonosítóját a jegyzékben. A lemezazonosító beszereznie az [az lemezshow][az-disk-show] parancsot. A következő példa az előző lépésben létrehozott *pvcRestored* lemezazonosítóját kapja:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Hozzon létre `azure-restored.yaml` egy pod manifest nevű, és adja meg az előző lépésben kapott lemez URI. A következő példa létrehoz egy egyszerű NGINX webkiszolgálót, amelynek a visszaállított lemeze */mnt/azure*kötetként van csatlakoztatva:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Hozza létre a pod a [kubectl alkalmazni parancsot,][kubectl-apply] ahogy az a következő példában látható:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

A pod `kubectl describe pod mypodrestored` részleteinek megtekintéséhez, például a következő tömörített példához, amely a kötetadatait jeleníti meg:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>További lépések

A kapcsolódó gyakorlati tanácsok értése: [Gyakorlati tanácsok a tároláshoz és a biztonsági mentések az AKS-ben.][operator-best-practices-storage]

További információ a Kubernetes állandó kötetek Azure-lemezek használatával.

> [!div class="nextstepaction"]
> [Kubernetes beépülő modul az Azure-lemezekhez][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
