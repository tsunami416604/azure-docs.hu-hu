---
title: Állandó köteteket hozhat létre az Azure Kubernetes Service szolgáltatással
description: Ismerje meg, hogyan hozhat létre állandó kötetek podok Azure Kubernetes Service (AKS) az Azure-lemezek használatával
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: 129a39294d52a312c91fc6a4fd009d76e88b4ff7
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185222"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Állandó kötetek létrehozása az Azure-lemezek az Azure Kubernetes Service (AKS)

Tartós kötet egy Kubernetes-podok való használatra vett tárolási részét jelöli. Tartós kötet egy vagy több podok által használható, és hogy statikusan vagy dinamikusan bővítheti. A Kubernetes állandó köteteken további információkért lásd: [Kubernetes állandó kötetek][kubernetes-volumes]. Ez a cikk bemutatja, hogyan állandó kötetek használata Azure-lemezek az Azure Kubernetes Service (AKS)-fürtben.

> [!NOTE]
> Egy Azure-lemez csak csatlakoztathatók a *hozzáférési mód* típus *ReadWriteOnce*, amely lehetővé teszi az egyetlen AKS csomópont. Ha kellene megosztani egy tartós kötet több csomóponton, érdemes [Azure Files][azure-files-pvc].

## <a name="built-in-storage-classes"></a>A beépített storage osztályai

Tárolási osztály hogyan tárolási egység dinamikusan jön létre egy állandó mennyiségi meghatározására szolgál. A Kubernetes storage osztályai további információkért lásd: [Kubernetes Storage osztályai][kubernetes-storage-classes].

Minden egyes AKS-fürt tartalmazza a két előre létrehozott storage osztályai, is konfigurál az Azure-lemezek használata:

* A *alapértelmezett* tárolási osztály egy az Azure standard disk építi ki.
    * Standard szintű storage meghajtókra, és költséghatékony tárolási megoldás kínál a nagy teljesítményű ugyanakkor továbbra is. A standard lemezek ideális megoldást jelentenek költséghatékony fejlesztési és tesztelési számítási feladatokhoz.
* A *felügyelt prémium szintű* tárolási osztály egy prémium szintű Azure-lemez építi ki.
    * A prémium lemezek SSD-alapú, nagy teljesítményű, kis késleltetésű lemezek. Az éles számítási feladatokat futtató virtuális gépek esetén érdemes a használatuk mellett dönteni. Ha az AKS-csomópontok a fürtben használni a prémium szintű storage, válassza ki a *felügyelt prémium szintű* osztály.

Használja a [kubectl get sc] [ kubectl-get] paranccsal tekintheti meg az előre létrehozott storage osztályai. A következő példa bemutatja a storage osztályai és a egy AKS-fürtön belül elérhető előzetes létrehozása:

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Tartós kötet jogcímek megadott GiB-ban, de az Azure managed disks számlázása Termékváltozat által egy adott méretet. Ezen SKU-k és közé eső 32GiB S4 vagy P4 szintű lemezek 4TiB S50-es vagy P50 lemez. Az átviteli sebesség és IOPS-teljesítmény egy prémium szintű felügyelt lemez attól függ, a mind a Termékváltozat és a példány mérete az AKS-fürt csomópontja. További információkért lásd: [díjszabás és a felügyelt lemezek teljesítményének][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Tartós kötet jogcím létrehozása

Tartós kötet jogcím (PVC) automatikusan üzembe egy tárolási osztály alapján történik. Ebben az esetben egy PVC egyikét használhatja az előre létrehozott storage osztályai, hozzon létre egy standard vagy prémium szintű Azure felügyelt lemezt.

Hozzon létre egy fájlt `azure-premium.yaml`, és másolja a következő jegyzékfájlban. A jogcím-kérelmek nevű lemez `azure-managed-disk` , amely *5 GB-os* méretű *ReadWriteOnce* hozzáférést. A *felügyelt prémium szintű* tárolási osztály van megadva, a tárolási osztály.

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
> Hozzon létre egy lemezt, amely a standard szintű tárolást használ, használja a `storageClassName: default` helyett *felügyelt prémium szintű*.

Hozzon létre a tartós kötet jogcímet a [kubectl létrehozása] [ kubectl-create] parancsot, majd adja meg a *azure-premium.yaml* fájlt:

```
$ kubectl create -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Használja a tartós kötet

Ha a tartós kötet jogcím létrejött, és sikeresen üzembe lett helyezve a lemez lehet létrehozni egy pod a lemez elérésére. A következő jegyzékfájl hoz létre egy alapszintű NGINX-pod a tartós kötet jogcím nevű használó *azure managed disk* csatlakoztatása az Azure-lemez elérési útja a `/mnt/azure`.

Hozzon létre egy fájlt `azure-pvc-disk.yaml`, és másolja a következő jegyzékfájlban.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

A pod-létrehozása a [kubectl létrehozása] [ kubectl-create] parancsot, az alábbi példában látható módon:

```
$ kubectl create -f azure-pvc-disk.yaml

pod/mypod created
```

Az Azure csatlakoztatott lemezzel most már rendelkezik egy futó pod a `/mnt/azure` könyvtár. Ez a konfiguráció látható, amikor a pod-n keresztül vizsgálatával `kubectl describe pod mypod`, ahogyan az a következő sűrített példához:

```
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

Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Készítsen biztonsági másolatot egy tartós kötet

Biztonsági másolatot készíteni a tartós kötet adatait, hajtsa végre a kötet a felügyelt lemez pillanatképét. Ezután használhatja ezt a pillanatfelvételt a visszaállított lemez létrehozása és csatlakoztatása a podok, hogy az adatok visszaállítása.

Először kérje le a kötet nevét a `kubectl get pvc` parancsot, mint például a nevű permanens *azure managed disk*:

```
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

A kötet neve képezi a mögöttes Azure-lemez nevét. A lemez azonosítójának a lekérdezés [az Lemezlista] [ az-disk-list] , és adja meg a PVC kötet neve, az alábbi példában látható módon:

```
$ az disk list --query '[].id|[?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Pillanatkép lemez létrehozásához használja a Lemezazonosítót [az pillanatkép létrehozása][az-snapshot-create]. Az alábbi példa létrehoz egy pillanatképet nevű *pvcSnapshot* ugyanabban az erőforráscsoportban, az AKS-fürtöt a (*MC_myResourceGroup_myAKSCluster_eastus*). Ha pillanatképeket létrehozni, és az AKS-fürt nem rendelkezik hozzáféréssel az erőforrás-csoportok a lemezek visszaállítása engedély problémák merülhetnek fel.

```azurecli
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

A lemezen lévő adatok mennyiségétől függően a pillanatkép létrehozása néhány percig is eltarthat.

## <a name="restore-and-use-a-snapshot"></a>Visszaállítás és a egy pillanatkép használata

Állítsa vissza a lemezt, és a egy Kubernetes-podok használhatja, használja a pillanatkép forrásként lemez létrehozásakor [az lemez létrehozása][az-disk-create]. Ez a művelet megőrzi az eredeti erőforrás, ha szeretne hozzáférni az eredeti adatok pillanatképet. A következő példában létrehozunk egy nevű lemez *pvcRestored* a nevű pillanatkép *pvcSnapshot*:

```azurecli
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

A visszaállított lemez használata a pod, adja meg a lemez Azonosítóját a jegyzékfájlban. A lemez Azonosítójának lekéréséhez a [az disk show] [ az-disk-show] parancsot. Az alábbi példa lekéri a Lemezazonosítót az *pvcRestored* az előző lépésben létrehozott:

```azurecli
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Nevű pod jegyzék létrehozásához `azure-restored.yaml` , és adja meg a lemezt az előző lépésben lekért URI. A következő példában létrehozunk egy alapszintű NGINX-webkiszolgálót, és a visszaállított lemez kötet fürtkötetként */mnt/azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
    - name: myfrontendrestored
      image: nginx
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

A pod-létrehozása a [kubectl létrehozása] [ kubectl-create] parancsot, az alábbi példában látható módon:

```
$ kubectl create -f azure-restored.yaml

pod/mypodrestored created
```

Használhat `kubectl describe pod mypodrestored` a pod, például a következő sűrített példához, amely megjeleníti a rendszerkötet-információkat a részletek megtekintéséhez:

```
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

További tudnivalók a Kubernetes Azure-lemezek használatával állandó köteteket.

> [!div class="nextstepaction"]
> [Kubernetes-beépülő modul az Azure-lemezek][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
