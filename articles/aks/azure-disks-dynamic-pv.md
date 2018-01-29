---
title: "Az Azure lemez használata AKS"
description: "Azure-lemezeket használata AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/25/2018
ms.author: nepeters
ms.openlocfilehash: e1f5b68d5d39dd846ebec525d1e83a6c0ef4971a
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="persistent-volumes-with-azure-disks---dynamic-provisioning"></a>Állandó kötetek lemezzel Azure - dinamikus kiosztás

Egy állandó kötet egy adat van kiépítve Kubernetes fürt használatra szánt tároló jelöli. Egy állandó kötet segítségével egy vagy több három munkaállomás-csoporttal, és dinamikusan vagy statikusan létesíthetők. Ez a dokumentum részletesen dinamikus kiosztás Azure-lemezek kötetként Kubernetes állandó AKS fürtben. 

Kubernetes állandó köteteken további információkért lásd: [Kubernetes állandó kötetek][kubernetes-volumes].

## <a name="built-in-storage-classes"></a>A beépített tárolókezelési osztályok

Tárolási osztály egy dinamikusan létrehozott állandó kötet konfigurálását meghatározására szolgál. Kubernetes tárolási osztályokon további információkért lásd: [Kubernetes tárolási osztályok][kubernetes-storage-classes].

Minden egyes AKS fürt két előre létrehozott tárolókezelési osztályok tartalmaz, akkor is dolgozhat az Azure-lemezeket konfigurál. Használja a `kubectl get storageclass` parancs ezeket.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

Ha a tárolási osztályok működik az igényeinek, nem kell hozzon létre egy újat.

## <a name="create-storage-class"></a>Tárolási osztály létrehozása

Ha azt szeretné, Azure-lemezeket beállított új tárolási osztály létrehozása, a következő minta jegyzékfájlját használatával végezhet. 

A `storageaccounttype` értékének `Standard_LRS` azt jelzi, hogy a szabványos lemez létrehozása. Ez az érték módosítható `Premium_LRS` létrehozásához egy [prémium szintű lemez][premium-storage]. A premium lemezek használatához a AKS csomópont virtuális gép rendelkeznie kell egy kompatibilis a premium lemezek mérete. Lásd: [Ez a dokumentum] [ premium-storage] kompatibilis méretek listáját.

```yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: azure-managed-disk
provisioner: kubernetes.io/azure-disk
parameters:
  kind: Managed
  storageaccounttype: Standard_LRS
```

## <a name="create-persistent-volume-claim"></a>Állandó kötet jogcím létrehozása

Egy állandó kötet jogcím tárolási osztály objektum segítségével dinamikusan létesítenek egy tárolási adat. Ha egy Azure lemezt használ, a lemez a AKS erőforrásként ugyanabban az erőforráscsoportban jön létre.

A példa jegyzékfájlban létrehoz egy állandó kötet jogcím használatával a `azure-managed-disk` tárolási osztály lemez `5GB` a méreténél `ReadWriteOnce` hozzáférést. A PVC hozzáférési módok további információkért lásd: [hozzáférési][access-modes].

> [!NOTE]
> Egy Azure lemez csak a hozzáférési mód típusú ReadWriteOnce, amely lehetővé teszi az egyetlen AKS csomópont lehet csatlakoztatni. Ha kellene megosztani egy állandó köteten több csomópont között, fontolja meg [Azure fájlok][azure-files-pvc]. 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>Az állandó kötet használata

Miután az állandó kötet jogcím létrejött, és a lemez sikeresen kiépítette, egy pod hozható létre a lemez hozzáférést. A következő jegyzékfájl létrehoz egy pod az állandó kötet jogcím használó `azure-managed-disk` csatlakoztassa a lemezt az Azure a `/var/www/html` elérési útja. 

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
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>További lépések

További tudnivalók Kubernetes állandó kötetek Azure-lemezeket.

> [!div class="nextstepaction"]
> [Azure-lemezeket Kubernetes beépülő modul][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md