---
title: Állandó köteteket hozhat létre az Azure Kubernetes Service szolgáltatással
description: Ismerje meg, hogyan hozhat létre állandó kötetek podok Azure Kubernetes Service (AKS) az Azure-lemezek használatával
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/10/2018
ms.author: iainfou
ms.openlocfilehash: 14617b57f59c068aa015c9bfea9b4d18520b4152
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38473682"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Állandó kötetek létrehozása az Azure-lemezek az Azure Kubernetes Service (AKS)

Tartós kötet egy Kubernetes-podok való használatra vett tárolási részét jelöli. Tartós kötet egy vagy több podok által használható, és hogy statikusan vagy dinamikusan bővítheti. A Kubernetes állandó köteteken további információkért lásd: [Kubernetes állandó kötetek][kubernetes-volumes]. Ez a cikk bemutatja, hogyan állandó kötetek használata Azure-lemezek az Azure Kubernetes Service (AKS)-fürtben.

> [!NOTE]
> Egy Azure-lemez csak csatlakoztathatók a *hozzáférési mód* típus *ReadWriteOnce*, amely lehetővé teszi az egyetlen AKS csomópont. Ha kellene megosztani egy tartós kötet több csomóponton, érdemes [Azure Files][azure-files-pvc].

## <a name="built-in-storage-classes"></a>A beépített storage osztályai

Tárolási osztály hogyan tárolási egység dinamikusan jön létre egy állandó mennyiségi meghatározására szolgál. A Kubernetes storage osztályai további információkért lásd: [Kubernetes Storage osztályai][kubernetes-storage-classes].

Minden egyes AKS-fürt tartalmazza a két előre létrehozott storage osztályai, mindkettő konfigurálva az Azure-lemezek való együttműködésre. A *alapértelmezett* tárolási osztály egy az Azure standard disk építi ki. A *felügyelt prémium szintű* tárolási osztály egy prémium szintű Azure-lemez építi ki. Ha az AKS-csomópontok a fürtben használni a prémium szintű storage, válassza ki a *felügyelt prémium szintű* osztály.

Használja a [kubectl get sc] [ kubectl-get] paranccsal tekintheti meg az előre létrehozott storage osztályai. A következő példa bemutatja a storage osztályai és a egy AKS-fürtön belül elérhető előzetes létrehozása:

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Tartós kötet jogcímek megadott GiB-ban, de az Azure managed disks számlázása Termékváltozat által egy adott méretet. Ezen SKU-k és közé eső 32GiB S4 vagy P4 szintű lemezek 4TiB S50-es vagy P50 lemez. Ezenkívül az átviteli sebesség és IOPS-teljesítmény egy prémium szintű felügyelt lemez attól függ, a mind a Termékváltozat és a példány mérete az AKS-fürt csomópontja. További információkért lásd: [díjszabás és a felügyelt lemezek teljesítményének][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Tartós kötet jogcím létrehozása

Tartós kötet jogcím (PVC) automatikusan üzembe egy tárolási osztály alapján történik. Ebben az esetben egy PVC egyikét használhatja az előre létrehozott storage osztályai, hozzon létre egy standard vagy prémium szintű Azure felügyelt lemezt.

Hozzon létre egy fájlt `azure-premium.yaml`, és másolja a következő jegyzékfájlban.

Jegyezze fel, amely a *felügyelt prémium szintű* tárolási osztály van megadva a jegyzetet, és a egy lemezt a jogcím kér *5 GB-os* méretű *ReadWriteOnce* hozzáférést.

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

Hozzon létre a tartós kötet jogcímet a [a kubectl a alkalmazni] [ kubectl-apply] parancsot, majd adja meg a *azure-premium.yaml* fájlt:

```console
kubectl apply -f azure-premium.yaml
```

## <a name="use-the-persistent-volume"></a>Használja a tartós kötet

Miután létrejött a tartós kötet jogcímet, és sikeresen üzembe lett helyezve a lemez lehet létrehozni egy pod a lemez elérésére. A következő jegyzékfájl hoz létre, amely a tartós kötet jogcím podot *azure managed disk* csatlakoztatása az Azure-lemez található a `/mnt/azure` elérési útja.

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

A pod-létrehozása a [a kubectl a alkalmazni] [ kubectl-apply] parancsot.

```console
kubectl apply -f azure-pvc-disk.yaml
```

Az Azure csatlakoztatott lemezzel most már rendelkezik egy futó pod a `/mnt/azure` könyvtár. Ez a konfiguráció látható, amikor a pod-n keresztül vizsgálatával `kubectl describe pod mypod`.

## <a name="next-steps"></a>További lépések

További tudnivalók a Kubernetes Azure-lemezek használatával állandó köteteket.

> [!div class="nextstepaction"]
> [Kubernetes-beépülő modul az Azure-lemezek][azure-disk-volume]

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
