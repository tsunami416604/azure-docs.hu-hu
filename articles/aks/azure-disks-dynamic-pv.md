---
title: Az Azure lemez használata AKS
description: Azure-lemezeket használata AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: iainfou
ms.openlocfilehash: ddac68b2a47fc830055b9dd5bd705802cc29c52f
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095926"
---
# <a name="persistent-volumes-with-azure-disks"></a>Az Azure-lemezeket állandó kötetek

Egy állandó kötet egy adat Kubernetes három munkaállomás-csoporttal való használatra van kiépítve tároló jelöli. Egy állandó kötet segítségével egy vagy több három munkaállomás-csoporttal, és dinamikusan vagy statikusan létesíthetők. Kubernetes állandó köteteken további információkért lásd: [Kubernetes állandó kötetek][kubernetes-volumes].

Ez a dokumentum adatokat állandó kötetek használata Azure-lemezeket Azure Kubernetes szolgáltatás (AKS)-fürtben lévő.

> [!NOTE]
> Egy Azure lemez csak a hozzáférési mód típusú ReadWriteOnce, amely lehetővé teszi az egyetlen AKS csomópont lehet csatlakoztatni. Ha kellene megosztani egy állandó köteten több csomópont között, fontolja meg [Azure fájlok][azure-files-pvc].

## <a name="built-in-storage-classes"></a>A beépített tárolókezelési osztályok

Tárolási osztály hogyan tárolási egységének dinamikusan létrejön egy állandó mennyiségi meghatározására szolgál. Kubernetes tárolási osztályokon további információkért lásd: [Kubernetes tárolási osztályok][kubernetes-storage-classes].

Minden egyes AKS fürt két előre létrehozott tárolókezelési osztályok tartalmaz, akkor is dolgozhat az Azure-lemezeket konfigurál. A `default` tárolási osztály látja el egy szabványos Azure lemezt. A `managed-premium` tárolási osztály látja el egy prémium szintű Azure lemez. Ha a fürt AKS csomópontja prémium szintű storage használatához jelölje be a `managed-premium` osztály.

Használja a [kubectl beolvasása sc] [ kubectl-get] parancs a tárolási előzetesen létrehozott osztályt.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Állandó kötet jogcímek GiB vannak megadva, de Azure felügyelt az adott méretű lemezek számlázása a Termékváltozat. Ezek termékváltozatok 32GiB S4 vagy P4 lemezek és közé 4TiB S50 vagy P50 lemezek. Továbbá, az átviteli sebesség és a prémium IOPS teljesítményének kezelt lemez függ a mind a Termékváltozaton és a példány mérete, a fürtben található csomópontok a AKS. Lásd: [árképzési és felügyelt lemezek teljesítményét][managed-disk-pricing-performance].

## <a name="create-persistent-volume-claim"></a>Állandó kötet jogcím létrehozása

Egy állandó kötet jogcímet (PVC) használt automatikusan tárolási osztály alapján. Ebben az esetben egy PVC egyikét használhatja a korábban létrehozott tárolókezelési osztályok létrehozása, a standard vagy prémium szintű Azure felügyelt lemezes.

Hozzon létre egy fájlt `azure-premium.yaml`, és másolja a következő jegyzékben.

Jegyezze fel, amely a `managed-premium` tárolási osztály a jegyzet szerepel, és a jogcímek kér egy lemezt `5GB` a méreténél `ReadWriteOnce` hozzáférést.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Az állandó kötet jogcímet létrehozása a [kubectl alkalmazása] [ kubectl-apply] parancsot.

```azurecli-interactive
kubectl apply -f azure-premium.yaml
```

## <a name="using-the-persistent-volume"></a>Az állandó kötet használata

Miután az állandó kötet jogcím létrejött, és a lemez sikeresen kiépítette, egy pod hozható létre a lemez hozzáférést. A következő jegyzékfájl létrehoz egy pod az állandó kötet jogcím használó `azure-managed-disk` csatlakoztassa a lemezt az Azure a `/mnt/azure` elérési útja.

Hozzon létre egy fájlt `azure-pvc-disk.yaml`, és másolja a következő jegyzékben.

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

Hozzon létre a fogyasztanak a [kubectl alkalmazása] [ kubectl-apply] parancsot.

```azurecli-interactive
kubectl apply -f azure-pvc-disk.yaml
```

Most már rendelkezik futó pod lemezt az Azure-e csatlakoztatva a `/mnt/azure` könyvtár. Ez a konfiguráció látható vizsgálatakor ellenőrizze a pod keresztül `kubectl describe pod mypod`.

## <a name="next-steps"></a>További lépések

További tudnivalók Kubernetes állandó kötetek Azure-lemezeket.

> [!div class="nextstepaction"]
> [Azure-lemezeket Kubernetes beépülő modul][azure-disk-volume]

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
[premium-storage]: ../virtual-machines/windows/premium-storage.md
