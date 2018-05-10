---
title: Az Azure lemez használata AKS
description: Azure-lemezeket használata AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: c9f3c851507c1120670f513479bc448fed656141
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
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

## <a name="create-persistent-volume-claim"></a>Állandó kötet jogcím létrehozása

Egy állandó kötet jogcímet (PVC) használt automatikusan tárolási osztály alapján. Ebben az esetben egy PVC egyikét használhatja a korábban létrehozott tárolókezelési osztályok létrehozása, a standard vagy prémium szintű Azure felügyelt lemezes.

Hozzon létre egy fájlt `azure-premimum.yaml`, és másolja a következő jegyzékben.

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

Az állandó kötet jogcímet létrehozása a [kubectl létrehozása] [ kubectl-create] parancsot.

```azurecli-interactive
kubectl create -f azure-premimum.yaml
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

Hozzon létre a fogyasztanak a [kubectl létrehozása] [ kubectl-create] parancsot.

```azurecli-interactive
kubectl create -f azure-pvc-disk.yaml
```

Most már rendelkezik futó pod lemezt az Azure-e csatlakoztatva a `/mnt/azure` könyvtár. Ez a konfiguráció látható vizsgálatakor ellenőrizze a pod keresztül `kubectl describe pod mypod`.

## <a name="next-steps"></a>További lépések

További tudnivalók Kubernetes állandó kötetek Azure-lemezeket.

> [!div class="nextstepaction"]
> [Azure-lemezeket Kubernetes beépülő modul][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md