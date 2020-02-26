---
title: Állandó kötet létrehozása és használata Azure-lemezekkel az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan hozhat létre dinamikusan állandó kötetet Azure-lemezekkel az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 37fea36567866af69e832a1f7e3caff2a68477a9
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596963"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Állandó kötet létrehozása és használata Azure-lemezekkel az Azure Kubernetes szolgáltatásban (ak)

Az állandó kötet a Kubernetes hüvelyekkel való használatra kiépített tárterületet jelöli. Egy állandó kötetet egy vagy több hüvely is használhat, és dinamikusan vagy statikusan kiépíthető. Ebből a cikkből megtudhatja, hogyan hozhat létre dinamikusan állandó köteteket az Azure-lemezekkel az Azure Kubernetes Service-(ak-) fürtben található egyetlen Pod használatával.

> [!NOTE]
> Az Azure-lemezeket csak a ReadWriteOnce *hozzáférési móddal* lehetcsatlakoztatni, így csak egyetlen Pod-ban érhető el az AK-ban. Ha egy állandó kötetet több hüvelyben kell megosztania, használja a [Azure Files][azure-files-pvc].

A Kubernetes-kötetekkel kapcsolatos további információkért lásd: az [AK-beli alkalmazások tárolási beállításai][concepts-storage].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió megkereséséhez futtassa a `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg az [Azure CLI telepítését][install-azure-cli]ismertető témakört.

## <a name="built-in-storage-classes"></a>Beépített tárolási osztályok

A tárolási osztály segítségével határozható meg, hogy egy adott tárolási egység hogyan legyen dinamikusan létrehozva állandó kötettel. További információ a Kubernetes tárolásával kapcsolatban: [Kubernetes Storage classs][kubernetes-storage-classes].

Mindegyik AK-fürt két, előre létrehozott tárolási osztályt tartalmaz, amelyek az Azure-lemezekkel való együttműködésre vannak konfigurálva:

* Az *alapértelmezett* tárolási osztály szabványos Azure-lemezt foglal le.
    * A standard szintű tárterületet a HDD-k végzik, és költséghatékony tárolást biztosít, miközben még folyamatban van. A standard lemezek ideális megoldást jelentenek költséghatékony fejlesztési és tesztelési számítási feladatokhoz.
* A *felügyelt Premium* Storage osztály egy prémium szintű Azure-lemezt foglal le.
    * A prémium lemezek SSD-alapú, nagy teljesítményű, kis késleltetésű lemezek. Az éles számítási feladatokat futtató virtuális gépek esetén érdemes a használatuk mellett dönteni. Ha a fürt AK-csomópontjai a Premium Storage-t használják, válassza a *felügyelt prémium* osztályt.
    
Ezek az alapértelmezett tárolási osztályok nem teszik lehetővé a kötet méretének módosítását a létrehozásuk után. Ha engedélyezni szeretné ezt a képességet, adja hozzá a *allowVolumeExpansion: true* sort az egyik alapértelmezett tárolási osztályhoz, vagy hozzon létre saját egyéni tárolási osztályt. A `kubectl edit sc` parancs használatával szerkesztheti a meglévő tárolási osztályokat. A Storage-osztályokkal és a saját létrehozásával kapcsolatos további információkért lásd: [az AK-beli alkalmazások tárolási lehetőségei][storage-class-concepts].

Az előre létrehozott tárolási osztályok megjelenítéséhez használja a [kubectl Get SC][kubectl-get] parancsot. Az alábbi példa egy AK-fürtön belül elérhető, előre létrehozott tárolási osztályokat mutatja be:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Az állandó mennyiségi jogcímek a GiB-ban vannak megadva, de az Azure Managed Disks szolgáltatás egy adott méretű SKU-ra van kiszámlázva. Ezek az SKU-32GiB az S4 vagy a P4 lemezeken a S80-vagy P80-lemezek 32TiB (előzetes verzió). A prémium szintű felügyelt lemez átviteli sebessége és IOPS teljesítménye az AK-fürtben található csomópontok SKU-jának és példányának méretétől függ. További információ: [Managed Disks díjszabása és teljesítménye][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Állandó kötet jogcímek létrehozása

A tárolási osztályok alapján a tárolók automatikus kiépítéséhez állandó mennyiségi jogcím (PVC) használatos. Ebben az esetben a PVC az előre létrehozott tárolási osztályok egyikét használja egy standard vagy prémium szintű Azure Managed Disk létrehozásához.

Hozzon létre egy `azure-premium.yaml`nevű fájlt, és másolja a következő jegyzékbe. A jogcím egy `azure-managed-disk` nevű lemezt kér, amely a *ReadWriteOnce* -hozzáféréssel rendelkező *5 GB* méretű. A *felügyelt Premium* Storage osztály a tárolási osztályként van megadva.

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
> A standard szintű tárolót használó lemezek létrehozásához használja a `storageClassName: default`t a *felügyelt prémium*helyett.

Hozza létre az állandó kötet jogcímet a [kubectl Apply][kubectl-apply] paranccsal, és adja meg az *Azure-Premium. YAML* fájlt:

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Az állandó kötet használata

Miután létrehozta az állandó kötet jogcímet, és a lemez sikeresen kiépítve, a lemezhez hozzáféréssel rendelkező Pod hozható létre. A következő jegyzékfájl létrehoz egy alapszintű NGINX Pod-t, amely az *Azure-Managed-Disk* nevű állandó mennyiségi jogcímet használja az Azure-lemez csatlakoztatásához a Path `/mnt/azure`. A Windows Server-tárolók esetében (jelenleg előzetes verzióban) a Windows PATH Convention, például a *'d: "* *mountPath* használatával adható meg.

Hozzon létre egy `azure-pvc-disk.yaml`nevű fájlt, és másolja a következő jegyzékbe.

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

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, ahogy az az alábbi példában is látható:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Most már rendelkezik egy futó Pod lemezzel, amely a `/mnt/azure` könyvtárban van csatlakoztatva. Ez a konfiguráció megtekinthető a pod `kubectl describe pod mypod`-on keresztüli vizsgálatakor, ahogy az az alábbi tömörített példában is látható:

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

## <a name="back-up-a-persistent-volume"></a>Állandó kötet biztonsági mentése

Az állandó köteten lévő adatok biztonsági mentéséhez készítsen pillanatképet a kötet felügyelt lemezéről. Ezt a pillanatképet használhatja a visszaállított lemez létrehozásához és a hüvelyekhez való csatolásához az adatok visszaállításához.

Először szerezze be a kötet nevét a `kubectl get pvc` paranccsal, például az *Azure által felügyelt lemez*nevű PVC esetében:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Ez a kötet neve az alapul szolgáló Azure-lemez nevét képezi. Kérdezze le a lemez AZONOSÍTÓját az [az Disk List][az-disk-list] paranccsal, és adja meg a PVC-kötet nevét az alábbi példában látható módon:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

A lemez azonosítójával hozzon létre egy pillanatkép-lemezt az [az Snapshot Create][az-snapshot-create]paranccsal. A következő példa létrehoz egy *pvcSnapshot* nevű pillanatképet ugyanabban az erőforráscsoporthoz, mint az AK-fürtöt (*MC_myResourceGroup_myAKSCluster_eastus*). Ha pillanatképeket hoz létre, és helyreállítja a lemezeket azokon az erőforráscsoportok között, amelyekhez az AK-fürt nem fér hozzá, akkor az engedélyek problémákba ütközhet.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

A lemezen lévő adatok mennyiségétől függően a pillanatkép létrehozása néhány percet is igénybe vehet.

## <a name="restore-and-use-a-snapshot"></a>Pillanatkép visszaállítása és használata

A lemez visszaállításához és a Kubernetes-Pod használatával történő használatához használja a pillanatképet forrásként, amikor létrehoz egy lemezt az [az Disk Create][az-disk-create]paranccsal. Ez a művelet megőrzi az eredeti erőforrást, ha ezt követően el kell érnie az eredeti adatpillanatképet. A következő példa létrehoz egy *pvcRestored* nevű lemezt a *pvcSnapshot*nevű pillanatképből:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

A helyreállított lemez Pod-vel való használatához határozza meg a lemez AZONOSÍTÓját a jegyzékfájlban. Szerezze be a lemez AZONOSÍTÓját az az [Disk show][az-disk-show] paranccsal. Az alábbi példa lekéri az előző lépésben létrehozott *pvcRestored* tartozó lemez azonosítóját:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Hozzon létre egy `azure-restored.yaml` nevű Pod manifest nevet, és határozza meg az előző lépésben beszerzett lemez URI azonosítóját. Az alábbi példa egy alapszintű NGINX-webkiszolgálót hoz létre, amelynek a visszaállított lemeze kötetként van csatlakoztatva a */mnt/Azure*-ben:

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

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal, ahogy az az alábbi példában is látható:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Az `kubectl describe pod mypodrestored` használatával megtekintheti a pod részleteit, például a következő, a kötetre vonatkozó információkat bemutató tömörített példát:

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

## <a name="next-steps"></a>Következő lépések

A kapcsolódó ajánlott eljárásokért lásd: [ajánlott eljárások a tároláshoz és a biztonsági mentéshez az AK-ban][operator-best-practices-storage].

További információ az állandó kötetek Kubernetes az Azure-lemezek használatával.

> [!div class="nextstepaction"]
> [Kubernetes beépülő modul Azure-lemezekhez][azure-disk-volume]

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
