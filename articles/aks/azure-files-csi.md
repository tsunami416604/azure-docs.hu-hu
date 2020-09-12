---
title: A (z) Azure Files Azure Kubernetes Service-ben (ak) található Container Storage Interface (CSI) illesztőprogramok használata
description: Ismerje meg, hogyan használható a Container Storage Interface (CSI) illesztőprogramja Azure Fileshoz egy Azure Kubernetes-szolgáltatás (ak) fürtjében.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 018275b6db4c2d2d1059f35077f74a6f45ec3ba9
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422049"
---
# <a name="use-the-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>A Azure Files Container Storage Interface (CSI) illesztőprogramjainak használata az Azure Kubernetes Service-ben (ak) (előzetes verzió)
A Azure Files CSI-illesztőprogram egy, az AK által használt [CSI-specifikációnak](https://github.com/container-storage-interface/spec/blob/master/spec.md) megfelelő illesztőprogram a Azure Files-megosztások életciklusának kezeléséhez. 

A Container Storage Interface (CSI) egy olyan szabvány, amely tetszőleges blokk-és file Storage-rendszerek számára teszi elérhetővé a Kubernetes tárolt számítási feladatokat. A CSI használatával az Azure Kubernetes Service (ak) mostantól képes írni, üzembe helyezni és megismételni a beépülő modult a Kubernetes meglévő tárolási rendszereinek új vagy javító funkcióinak megadásával anélkül, hogy meg kellene érintenie az alapvető Kubernetes-kódot, és várnia kell a kiadási ciklusokra.

Ha a CSI-illesztőprogram támogatásával szeretne létrehozni egy AK-fürtöt, tekintse [meg a CSI-illesztőprogramok engedélyezése az Azure-lemezekhez és a Azure Files az AK](csi-storage-drivers.md)

>[!NOTE]
> A *"beépített illesztőprogramok"* kifejezés az alapszintű kubernetes-kód részét képező aktuális tároló-illesztőprogramokat és a beépülő modulokat tartalmazó új CSI-illesztőprogramokat tartalmazza.

## <a name="use-a-persistent-volume-pv-with-azure-files"></a>Állandó kötet (PV) használata Azure Files

Az [állandó kötetek](concepts-storage.md#persistent-volumes) a Kubernetes hüvelyekkel való használatra kiépített tárterületet jelölik. Egy állandó kötetet egy vagy több hüvely is használhat, és dinamikusan vagy statikusan kiépíthető. Ha több hüvelynek egyidejű hozzáférésre van szüksége ugyanahhoz a tárolási kötethez, akkor a Azure Files használatával csatlakozhat a [Server Message Block (SMB) protokollal][smb-overview]. Ebből a cikkből megtudhatja, hogyan hozhat létre dinamikusan egy Azure Files-megosztást egy Azure Kubernetes-szolgáltatási (ak-) fürtön több hüvely használatával. Statikus kiépítés esetén lásd: [a kötet manuális létrehozása és használata Azure Files megosztással](azure-files-volume.md).

A Kubernetes-kötetekkel kapcsolatos további információkért lásd: az [AK-beli alkalmazások tárolási beállításai][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-using-the-built-in-storage-classes"></a>Azure Files PVs dinamikus létrehozása a beépített tárolási osztályok használatával
A tárolási osztály segítségével határozható meg az Azure-fájlmegosztás létrehozása. A rendszer automatikusan létrehoz egy Storage-fiókot a [csomópont-erőforráscsoport][node-resource-group] számára, hogy az Azure-fájlmegosztás tárolására használja a Storage osztályt. Válasszon a következő [Azure Storage-redundancia][storage-skus] *skuName*:

* *Standard_LRS* standard, helyileg redundáns tárolás
* *Standard_GRS* – szabványos geo-redundáns tárolás
* *Standard_ZRS* – szabványos zóna redundáns tárolója
* *Standard_RAGRS* – szabványos olvasási hozzáférésű geo-redundáns tárolás
* *Premium_LRS* – prémium helyileg redundáns tárolás

> [!NOTE]
> Azure Files támogatja a Premium Storage-t, a prémium szintű fájlmegosztás minimális 100 GB.

Ha a Storage CSI-illesztőprogramokat az AK-on használja, 2 további beépített, `StorageClasses` amely a **Azure Files CSI Storage-illesztőprogramokat**használja. A további CSI-tárolási osztályok a fürttel együtt a fában alapértelmezett tárolási osztályok mellett jönnek létre.

- `azurefile-csi` -Az Azure standard Storage használatával létrehoz egy Azure-fájlmegosztást. 
- `azurefile-csi-premium` – Az Azure Premium Storage használatával létrehoz egy Azure-fájlmegosztást. 

A visszaigénylési házirend mindkét tárolási osztályban biztosítja, hogy az alapul szolgáló Azure-fájlmegosztás törölve legyen a megfelelő állandó kötet törlésekor. A tárolási osztályok azt is konfigurálja, hogy a fájlmegosztás bővíthető legyen, csak az állandó mennyiségi jogcímet kell módosítania az új mérettel.

A tárolási osztályok kihasználása érdekében hozzon létre egy [állandó mennyiségi jogcímet (PVC)](concepts-storage.md#persistent-volume-claims) és a megfelelő Pod-t, amely hivatkozik és kihasználja őket. A tárolási osztályok alapján a tárolók automatikus kiépítéséhez állandó mennyiségi jogcím (PVC) használatos. A PVC az előre létrehozott tárolási osztályok egyikét vagy egy felhasználó által definiált tárolási osztályt használhat Azure Files megosztás létrehozásához a kívánt SKU-hoz és mérethez. Ha létrehoz egy Pod-definíciót, a rendszer az állandó kötet jogcímet adja meg a kívánt tár igényléséhez.

Hozzon létre egy olyan [állandó mennyiségi jogcímet és Pod-t `outfile` , amely az aktuális dátumot](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) a [kubectl Apply][kubectl-apply] paranccsal kinyomtatja:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Ha a pod futó állapotban van, ellenőrizheti, hogy a fájlmegosztás megfelelően van-e csatlakoztatva az alábbi parancs futtatásával, és a kimenet ellenőrzése a következőt tartalmazza `outfile` : 

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Egyéni tárolási osztály létrehozása

Az alapértelmezett tárolási osztályok a leggyakoribb forgatókönyvek, de nem az összes. Bizonyos esetekben előfordulhat, hogy saját tárolási osztályt szeretne használni a saját paraméterekkel. Használja például a következő jegyzékfájlt a fájlmegosztás konfigurálásához `mountOptions` .

A *fileMode* és a *dirMode* alapértelmezett értéke *0777* a Kubernetes csatlakoztatott fájlmegosztás esetében. Megadhatja a különböző csatlakoztatási beállításokat a tárolási osztály objektumon.

Hozzon létre egy nevű fájlt `azure-file-sc.yaml` , és illessze be a következő példa jegyzékfájlt: 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Hozza létre a tárolási osztályt a [kubectl Apply][kubectl-apply] paranccsal:

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Az Azure Files CSI-illesztőprogram támogatja az állandó kötetek és a mögöttes fájlmegosztás [pillanatképének](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) létrehozását. 

Hozzon létre egy [mennyiségi pillanatkép-osztályt](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) a [kubectl Apply][kubectl-apply] paranccsal:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Hozzon létre egy [kötet-pillanatképet](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) az [oktatóanyag elején dinamikusan létrehozott](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes)PVC-ből `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Ellenőrizze, hogy a pillanatkép helyesen lett-e létrehozva:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume-pv"></a>Állandó kötet átméretezése (PV)

A PVC számára nagyobb kötetet igényelhet. Szerkessze a PVC objektumot, és válasszon nagyobb méretet. Ez a módosítás elindítja az alapul szolgáló kötet kiterjesztését, amely biztonsági másolatot készít a PersistentVolume. 

> [!NOTE] 
> Egy új PersistentVolume soha nem jön létre a jogcím kielégítése érdekében. Ehelyett a rendszer átméretezi egy meglévő kötetet.

Az AK-ban a beépített `azurefile-csi` tárolási osztály már támogatja a bővítést, így a [korábban a tárolási osztállyal létrehozott PVC-t használja fel](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes). A PVC kért egy 100Gi-fájlmegosztást, a futtatásával ellenőrizheti, hogy:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Bontsa ki a PVC-t a mező növelésével `spec.resources.requests.storage` :

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Győződjön meg arról, hogy mind a PVC, mind a hüvelyen belül található fájlrendszer megjeleníti az új méretet:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```

## <a name="windows-containers"></a>Windows-tárolók

Az Azure Files CSI-illesztőprogram a Windows-csomópontokat és-tárolókat is támogatja, ha Windows-tárolókat szeretne használni a Windows- [tárolók oktatóanyag](windows-container-cli.md) használatával Windows-csomópontok készletének hozzáadásához.

Ha már rendelkezik Windows-csomóponttal, használja ki a beépített tárolási osztályokat, `azurefile-csi` vagy hozzon létre egyéni fájlokat. Üzembe helyezhet egy olyan, [Windows-alapú állapot-nyilvántartó készletet](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) , amely időbélyegeket ment egy fájlba, ha `data.txt` az alábbi parancsot telepíti a [kubectl Apply][kubectl-apply] paranccsal:

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

A kötet tartalmának ellenőrzése a futtatásával:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Következő lépések

- Az Azure-lemezek CSI-illesztőprogramjának használatáról további információt az [Azure-lemezek használata a CSI-illesztőprogramokkal](azure-disk-csi.md)című témakörben talál.
- További információ a tárolással kapcsolatos ajánlott eljárásokról: [ajánlott eljárások a tároláshoz és a biztonsági mentésekhez az Azure Kubernetes szolgáltatásban (ak)][operator-best-practices-storage]


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md