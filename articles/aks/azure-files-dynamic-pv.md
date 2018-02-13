---
title: "Az Azure File használata AKS"
description: "Azure-lemezeket használata AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/04/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ce37cfdd70f95822a912f6ea71b9e4a3f9a30a14
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Az Azure files állandó kötetek

Egy állandó kötet egy adat van kiépítve Kubernetes fürt használatra szánt tároló jelöli. Egy állandó kötet segítségével egy vagy több három munkaállomás-csoporttal, és dinamikusan vagy statikusan létesíthetők. Ez a dokumentum részletesen dinamikus kiépítése az Azure fájlmegosztások kötetként Kubernetes állandó AKS fürtben. 

Kubernetes állandó köteteken további információkért lásd: [Kubernetes állandó kötetek][kubernetes-volumes].

## <a name="prerequisites"></a>Előfeltételek

Létesítésekor dinamikusan az Azure fájlmegosztások Kubernetes kötetként, minden tárfiók használható, amíg a AKS fürtként ugyanabban az erőforráscsoportban szerepel. Szükség esetén hozzon létre egy tárfiókot a ugyanabban az erőforráscsoportban a AKS fürtként. 

A megfelelő erőforráscsoport azonosításához használja a [az listájának] [ az-group-list] parancsot.

```azurecli-interactive
az group list --output table
```

A következő egy példa a kimenetre jeleníti meg az erőforráscsoportot, egy AKS fürt mindkettőnek. Az erőforráscsoport nevét, például *MC_myAKSCluster_myAKSCluster_eastus* tartalmaz a AKS fürt erőforrásait, és ahol a tárfiókot kell létrehozni. 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Az erőforráscsoport azonosítása után hozzon létre a tárfiók a [az storage-fiók létrehozása] [ az-storage-account-create] parancsot.

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Tárolási osztály létrehozása

Tárolási osztály egy dinamikusan létrehozott állandó kötet konfigurálását meghatározására szolgál. A tárolási osztály objektum elemek, például az az Azure storage-fiók neve, a Termékváltozat és a terület vannak definiálva. Kubernetes tárolási osztályokon további információkért lásd: [Kubernetes tárolási osztályok][kubernetes-storage-classes].

Az alábbi példa meghatározza, hogy írja be a metódust a tárfiók `Standard_LRS` a a `eastus` régió is használható, ha a kért tárolási. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

Egy bizonyos tárolási fiók használatára a `storageAccount` paraméter használható.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>Állandó kötet jogcím létrehozása

Egy állandó kötet jogcímet a tárolási osztály objektum használatával dinamikusan létesítenek egy tárolási adat. Egy Azure-fájlok használata esetén az Azure fájlmegosztások a tárfiók kiválasztva, vagy a tárolási osztály objektumban megadott jön létre.

>  [!NOTE]
>   Ellenőrizze, hogy a megfelelő storage-fiók ugyanabban az erőforráscsoportban AKS fürterőforrások előre létrehozott lett. Ez az erőforráscsoport neve például van *MC_myAKSCluster_myAKSCluster_eastus*. Az állandó kötet jogcím kiépítése az Azure fájlmegosztás Ha nem érhető el a storage-fiókok nem. 

A következő jegyzékfájl segítségével hozzon létre egy állandó kötet jogcím `5GB` a méreténél `ReadWriteOnce` hozzáférést. A PVC hozzáférési módok további információkért lásd: [hozzáférési][access-modes].

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>Az állandó kötet használata

Ha az állandó kötet jogcím létrejött, és sikeresen kiépítette tárolására, hozhatja létre a pod elérni a kötetet. A következő jegyzékfájl létrehoz egy pod az állandó kötet jogcím használó `azurefile` : Azure fájlmegosztás csatlakoztatásához a `/var/www/html` elérési útja. 

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
        claimName: azurefile
```

## <a name="mount-options"></a>Csatlakoztatási beállítások

A fileMode és dirMode alapértékeket eltérő Kubernetes verzió a következő táblázatban ismertetett módon. 

| verzió: | érték |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 vagy újabb | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 vagy újabb | 0755 |

Ha verzió 1.8.5 fürt segítségével vagy nagyobb, csatlakozási beállítások adhatók meg a tárolási osztály objektum. A következő példa készletek `0777`. 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

Ha verzió 1.8.0 - 1.8.4, fürt segítségével a biztonsági környezet az adható meg a `runAsUser` értéke `0`. Pod biztonsági környezetben további információkért lásd: [konfigurálja a biztonsági környezet][kubernetes-security-context].

## <a name="next-steps"></a>További lépések

További tudnivalók Kubernetes állandó kötetek Azure fájlokat használja.

> [!div class="nextstepaction"]
> [Azure-fájlok Kubernetes beépülő modul][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create