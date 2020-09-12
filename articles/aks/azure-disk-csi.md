---
title: Az Azure Kubernetes Service-ben (ak) lévő Azure-lemezek tároló-illesztőprogramjainak használata
description: Megtudhatja, hogyan használhatja az Azure-lemezek Container Storage Interface-(CSI-) illesztőprogramjait az Azure Kubernetes Service (ak) fürtben.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 4b5ccd2712a95f5f020daa0161f1b5908a38a62e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422054"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Az Azure Disk Container Storage Interface (CSI) illesztőprogramjainak használata az Azure Kubernetes szolgáltatásban (ak) (előzetes verzió)
Az Azure Disk CSI-illesztőprogram egy olyan [CSI-specifikációnak](https://github.com/container-storage-interface/spec/blob/master/spec.md) megfelelő illesztőprogram, amelyet az AK az Azure-lemezek életciklusának kezelésére használ. 

A Container Storage Interface (CSI) egy olyan szabvány, amely tetszőleges blokk-és file Storage-rendszerek számára teszi elérhetővé a Kubernetes tárolt számítási feladatokat. A CSI segítségével az Azure Kubernetes Service (ak) segítségével írhat, telepíthet és megismételheti a beépülő modulokat a Kubernetes meglévő tárolási rendszereinek új vagy javító lehetőségeit anélkül, hogy az alapvető Kubernetes-kódot kellene érintenie, és várnia kell a kiadási ciklusokra.

Ha a CSI-illesztőprogram támogatásával szeretne létrehozni egy AK-fürtöt, tekintse [meg a CSI-illesztőprogramok engedélyezése az Azure-lemezekhez és a Azure Files az AK](csi-storage-drivers.md)

>[!NOTE]
> A *"beépített illesztőprogramok"* kifejezés az alapszintű kubernetes-kód részét képező aktuális tároló-illesztőprogramokat és a beépülő modulokat tartalmazó új CSI-illesztőprogramokat tartalmazza.

## <a name="use-csi-persistent-volumes-pv-with-azure-disks"></a>A CSI állandó kötetei (PV) használata az Azure-lemezekkel 

Az [állandó kötetek](concepts-storage.md#persistent-volumes) a Kubernetes hüvelyekkel való használatra kiépített tárterületet jelölik. Egy állandó kötetet egy vagy több hüvely is használhat, és dinamikusan vagy statikusan kiépíthető. Ebből a cikkből megtudhatja, hogyan hozhat létre dinamikusan állandó köteteket az Azure-lemezekkel az Azure Kubernetes Service-(ak-) fürtben található egyetlen Pod használatával. Statikus kiépítés esetén lásd: [kötetek manuális létrehozása és használata az Azure Disks](azure-disk-volume.md)szolgáltatással.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

A Kubernetes-kötetekkel kapcsolatos további információkért lásd: az [AK-beli alkalmazások tárolási beállításai][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes"></a>Azure Disk PVs dinamikus létrehozása a beépített tárolási osztályok használatával

A tárolási osztály segítségével határozható meg, hogy egy adott tárolási egység hogyan legyen dinamikusan létrehozva állandó kötettel. További információ a Kubernetes tárolásával kapcsolatban: [Kubernetes Storage classs][kubernetes-storage-classes]. Ha a Storage CSI-illesztőprogramokat az AK-on használja, `StorageClasses` az **Azure Disk CSI-tároló illesztőprogramjainak**kihasználása 2 további beépített. A további CSI-tárolási osztályok a fürttel együtt a fában alapértelmezett tárolási osztályok mellett jönnek létre.

>[!NOTE]
> A *"beépített illesztőprogramok"* azon aktuális tároló-illesztőprogramok, amelyek az alapvető kubernetes-kód részét képezik, és a beépülő modulhoz tartozó CSI-illesztőprogramok.

- `managed-csi` -Az Azure StandardSSD helyileg redundáns tárolást (LRS) használ egy felügyelt lemez létrehozásához.
- `managed-csi-premium` -Az Azure Premium helyileg redundáns tárolást (LRS) használja a felügyelt lemez létrehozásához. 

A visszaigénylési házirend mindkét tárolási osztályban biztosítja, hogy az alapul szolgáló Azure-lemez törölve legyen a megfelelő állandó kötet törlésekor. A tárolási osztályok azt is konfigurálja, hogy az állandó kötetek bővíthetők legyenek, csak az állandó mennyiségi jogcímet kell módosítania az új mérettel.

A tárolási osztályok kihasználása érdekében létre kell hoznia egy [állandó mennyiségi jogcímet (PVC)](concepts-storage.md#persistent-volume-claims) és a megfelelő Pod-t, amely hivatkozik és hasznosítja őket. A tárolási osztályok alapján a tárolók automatikus kiépítéséhez állandó mennyiségi jogcím (PVC) használatos. A PVC az előre létrehozott tárolási osztályok egyikét vagy egy felhasználó által definiált tárolási osztályt használhat egy Azure által felügyelt lemez létrehozásához a kívánt SKU-hoz és mérethez. Ha létrehoz egy Pod-definíciót, a rendszer az állandó kötet jogcímet adja meg a kívánt tár igényléséhez.

Hozzon létre egy példát a pod és a megfelelő állandó mennyiségi jogcímet a [kubectl Apply][kubectl-apply] paranccsal:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Ha a pod futó állapotban van, hozzon létre egy nevű új fájlt `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Most ellenőrizheti, hogy a lemez megfelelően van-e csatlakoztatva az alábbi parancs futtatásával, és ellenőrzi, hogy látható-e a `test.txt` fájl a kimenetben: 

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Egyéni tárolási osztály létrehozása

Az alapértelmezett tárolási osztályok a leggyakoribb forgatókönyvek, de nem az összes. Bizonyos esetekben előfordulhat, hogy saját tárolási osztályt szeretne használni a saját paraméterekkel. A megmutassa van egy olyan forgatókönyv, amelyben érdemes lehet módosítani a következőt: `volumeBindingMode` . 

Az alapértelmezett tárolási osztályok olyan `volumeBindingMode: Immediate` garanciát használnak, amely azonnal megtörténik a PersistentVolumeClaim létrehozása után. Azokban az esetekben, amikor a csomópont-készletek topológia korlátozottak, például Availability Zones használatával, az állandó kötetek a pod ütemezési követelményeinek ismerete nélkül lesznek kötve vagy kiépítve (ebben az esetben egy adott zónában kell lennie).

Ennek a forgatókönyvnek a megoldásához használhatja a t `volumeBindingMode: WaitForFirstConsumer` , amely késlelteti a PersistentVolume kötését és üzembe helyezését egészen addig, amíg a PersistentVolumeClaim-t használó Pod-t nem hozza létre. Így a PV a pod ütemezési megkötései által meghatározott rendelkezésre állási zónában (vagy más topológiában) lesz kiépítve. 

Hozzon létre egy nevű fájlt `sc-azuredisk-csi-waitforfirstconsumer.yaml` , és illessze be az alábbi jegyzékfájlt.
A tárolási osztály ugyanaz, mint a `managed-csi` Storage osztály, de más `volumeBindingMode` . 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Hozza létre a tárolási osztályt a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a `sc-azuredisk-csi-waitforfirstconsumer.yaml` fájlt:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Mennyiségi Pillanatképek

Az Azure Disk CSI-illesztőprogram támogatja [az állandó kötetek pillanatképének](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)létrehozását. Ennek a funkciónak a részeként az illesztőprogram a paraméterben megadott értéktől függően *teljes* vagy [ *növekményes* pillanatképeket](../virtual-machines/windows/disks-incremental-snapshots.md) is végrehajthat `incremental` (alapértelmezés szerint igaz). 

Az összes paraméterrel kapcsolatos részletekért lásd a [mennyiségi Pillanatképek osztályának paramétereit](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Kötet pillanatképének létrehozása

A képesség megmutassa hozzon létre egy [mennyiségi pillanatkép-osztályt](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) a [kubectl Apply][kubectl-apply] paranccsal:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Most hozzon létre egy [kötet-pillanatképet](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) az [oktatóanyag elején dinamikusan létrehozott](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes)PVC-ből `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Győződjön meg arról, hogy a pillanatkép helyesen lett létrehozva:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Új PVC létrehozása kötet-pillanatkép alapján

Kötet-pillanatkép alapján létrehozhat egy új PVC-t. Használja az előző lépésben létrehozott pillanatképet, és hozzon létre egy [új PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) -t és egy [új Pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) -t a használathoz.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Végül győződjön meg róla, hogy ez ugyanaz a PVC, amelyet a tartalom ellenőrzése előtt hozott létre.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

A várt módon továbbra is láthatjuk a korábban létrehozott `test.txt` fájlt.

## <a name="clone-volumes"></a>Kötetek klónozása

A klónozott kötetek egy meglévő Kubernetes-kötet duplikálása vannak meghatározva. További információ a Kubernetes található kötetek klónozásáról: a kötetek [klónozásának](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)fogalmi dokumentációja.

Az Azure-lemezek CSI-illesztőprogramja támogatja a kötetek klónozását. A bemutatóhoz hozzon létre egy [klónozott kötetet](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) a [korábban létrehozott](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes) `azuredisk-pvc` és [egy új Pod használatával](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).



```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Most már megvizsgálhatja a klónozott kötet tartalmát az alábbi parancs futtatásával, és megerősíti, hogy a létrehozott fájl továbbra is látható `test.txt` .

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume-pv"></a>Állandó kötet átméretezése (PV)

Ehelyett nagyobb kötetet igényelhet egy PVC számára. Szerkessze a PVC objektumot, és válasszon nagyobb méretet. Ez a módosítás elindítja az alapul szolgáló kötet kiterjesztését, amely biztonsági másolatot készít a PersistentVolume. 

> [!NOTE] 
> Egy új PersistentVolume soha nem jön létre a jogcím kielégítése érdekében. Ehelyett a rendszer átméretezi egy meglévő kötetet.

Az AK-ban a beépített `managed-csi` tárolási osztály már lehetővé teszi a bővítést, így a [korábban a tárolási osztállyal létrehozott PVC-t](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes)használja ki. A PVC 10Gi állandó kötetet kért, és a futtatásával ellenőrizheti, hogy:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```
> [!IMPORTANT]
> Az Azure Disk CSI-illesztőprogram jelenleg csak a hüvelyek által társított (és a kötet nem egy adott csomóponthoz csatlakoztatott) átméretezését támogatja.

Így törölheti a korábban létrehozott Pod-t:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Bontsa ki a PVC-t a `spec.resources.requests.storage` mező növelésével:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Erősítse meg, hogy a kötet már nagyobb:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE] 
> A PVC nem tükrözi az új méretet, amíg hozzá nem kapcsolódik a pod.

Hozzunk létre egy új Pod-t:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

Végül pedig erősítse meg a PVC méretét és a pod-on belül: 
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

<!--- ## Shared disk

[Azure shared disks](../virtual-machines/windows/disks-shared.md) is an Azure managed disks feature that enables attaching an Azure disk to agent nodes simultaneously. Attaching a managed disk to multiple agent nodes allows you, for example, to deploy new or migrate existing clustered applications to Azure.

> [!IMPORTANT] Currently, only raw block device (`volumeMode: Block`) is supported by the Azure disk CSI driver. Applications should manage the coordination and control of writes, reads, locks, caches, mounts and fencing on the shared disk which is exposed as raw block device.

Let's create file called `shared-disk.yaml` by copying the below that contains the shared disk storage class and PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Create the storage class with the [kubectl apply][kubectl-apply] command and specify your `shared-disk.yaml` file:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Now let's create a file called `deployment-shared.yml` by copying the below:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: nginx
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Create the deployment with the [kubectl apply][kubectl-apply] command and specify your `deployment-shared.yml` file:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Finally, let's check the block device inside the pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp bash
root@deployment-sharedisk-7454978bc6-xh7jp:/# dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.0502999 s, 2.1 GB/s
```
-->

## <a name="windows-containers"></a>Windows-tárolók

Az Azure Disk CSI-illesztőprogram a Windows-csomópontokat és-tárolókat is támogatja. Ha Windows-tárolókat szeretne használni, kövesse a [Windows-tárolók oktatóanyagot](windows-container-cli.md) a Windows-csomópontok hozzáadásához.

Ha már rendelkezik Windows-csomóponttal, most már használhatja a beépített tárolási osztályokat, például a következőt: `managed-csi` . Üzembe helyezhet egy olyan, [Windows-alapú állapot-nyilvántartó készletet](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) , amely időbélyegeket ment egy fájlba, ha `data.txt` az alábbi parancsot telepíti a [kubectl Apply][kubectl-apply] paranccsal:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

A kötet tartalmát mostantól a (z) parancs futtatásával is ellenőrizheti:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Következő lépések

- A CSI-illesztőprogram Azure-fájlokhoz való használatáról további információt az [Azure-fájlok használata a CSI-illesztőprogramokkal](azure-files-csi.md)című témakörben talál.
- További információ a tárolással kapcsolatos ajánlott eljárásokról: [ajánlott eljárások a tároláshoz és a biztonsági mentésekhez az Azure Kubernetes szolgáltatásban (ak)][operator-best-practices-storage]


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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register