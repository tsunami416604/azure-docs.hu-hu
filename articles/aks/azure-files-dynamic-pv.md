---
title: Az Azure File használata AKS
description: Azure-lemezeket használata AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: dbd1b171185bf49ed5e633153938d3e7ba81387d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="persistent-volumes-with-azure-files"></a>Az Azure files állandó kötetek

Egy állandó kötet egy adat van kiépítve Kubernetes fürt használatra szánt tároló jelöli. Egy állandó kötet segítségével egy vagy több három munkaállomás-csoporttal, és dinamikusan vagy statikusan létesíthetők. Ez a dokumentum részletesen dinamikus kiépítése az Azure fájlmegosztások kötetként Kubernetes állandó AKS fürtben.

Kubernetes állandó köteteken további információkért lásd: [Kubernetes állandó kötetek][kubernetes-volumes].

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Létesítésekor dinamikusan az Azure fájlmegosztások Kubernetes kötetként, minden tárfiók használható, amíg a AKS fürtként ugyanabban az erőforráscsoportban szerepel. Szükség esetén hozzon létre egy tárfiókot a ugyanabban az erőforráscsoportban a AKS fürtként.

A megfelelő erőforráscsoport azonosításához használja a [az listájának] [ az-group-list] parancsot.

```azurecli-interactive
az group list --output table
```

Egy hasonló nevű erőforráscsoport keres `MC_clustername_clustername_locaton`, ahol clustername a AKS fürt nevét, és helye az Azure-régió, ahol a fürt telepítve lett.

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Használja a [az storage-fiók létrehozása] [ az-storage-account-create] parancsot a tárfiók létrehozásához.

Ez a példa használ, frissítse `--resource-group` az erőforráscsoport nevét és `--name` az Ön által választott névre.

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>Tárolási osztály létrehozása

Tárolási osztály hogyan jön létre az Azure fájlmegosztások meghatározására szolgál. A megadott tárfiók a osztály adható meg. Ha egy tárfiókja nincs megadva, a `skuName` és `location` meg kell adni, és egyezés kiértékeli az összes tárfiók társított erőforráscsoportban.

Az Azure files storage osztályok Kubernetes további információkért lásd: [Kubernetes tárolási osztályok][kubernetes-storage-classes].

Hozzon létre egy fájlt `azure-file-sc.yaml` , és másolja a következő jegyzékben. Frissítés a `storageAccount` a céloldali tárfiók nevével.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

A tárolási osztályt létrehozni a [kubectl létrehozása] [ kubectl-create] parancsot.

```azurecli-interactive
kubectl create -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Állandó kötet jogcím létrehozása

Egy állandó kötet jogcímet (PVC) tárolóobjektum osztály használatával dinamikusan létesítenek az Azure fájlmegosztások.

A következő jegyzékfájl segítségével hozzon létre egy állandó kötet jogcím `5GB` a méreténél `ReadWriteOnce` hozzáférést.

Hozzon létre egy fájlt `azure-file-pvc.yaml` , és másolja a következő jegyzékben. Győződjön meg arról, hogy a `storageClassName` megegyezik az előző lépésben létrehozott tárolási osztály.

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

Az állandó kötet jogcímet létrehozása a [kubectl létrehozása] [ kubectl-create] parancsot.

```azurecli-interactive
kubectl create -f azure-file-pvc.yaml
```

Ezt követően a fájlmegosztás jön létre. Kubernetes titkos kulcs is hoz létre, amely tartalmazza a kapcsolati adatokat és hitelesítő adatait.

## <a name="using-the-persistent-volume"></a>Az állandó kötet használata

A következő jegyzékfájl létrehoz egy pod az állandó kötet jogcím használó `azurefile` : Azure fájlmegosztás csatlakoztatásához a `/mnt/azure` elérési útja.

Hozzon létre egy fájlt `azure-pvc-files.yaml`, és másolja a következő jegyzékben. Győződjön meg arról, hogy a `claimName` megegyezik az előző lépésben létrehozott PVC.

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
        claimName: azurefile
```

Hozzon létre a fogyasztanak a [kubectl létrehozása] [ kubectl-create] parancsot.

```azurecli-interactive
kubectl create -f azure-pvc-files.yaml
```

Most már rendelkezik futó pod lemezt az Azure-e csatlakoztatva a `/mnt/azure` könyvtár. Ez a konfiguráció látható vizsgálatakor ellenőrizze a pod keresztül `kubectl describe pod mypod`.

## <a name="mount-options"></a>Csatlakoztatási beállítások

A fileMode és dirMode alapértékeket eltérő Kubernetes verzió a következő táblázatban ismertetett módon.

| verzió: | érték |
| ---- | ---- |
| V1.6.x, v1.7.x | 0777 |
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
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
