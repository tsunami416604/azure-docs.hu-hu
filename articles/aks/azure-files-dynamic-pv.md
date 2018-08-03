---
title: Az Azure használata az aks-sel
description: Azure-lemezek használata az aks-sel
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bac80e354a4d629bfbfc8207b9fed7c69c765839
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429267"
---
# <a name="persistent-volumes-with-azure-files"></a>Az Azure-fájlok állandó kötetek

Tartós kötet olyan tárolási megoldás, amely létre lett hozva egy Kubernetes-fürt használatát. Tartós kötet segítségével egy vagy több podok és statikusan vagy dinamikusan létrehozhatók. Ez a dokumentum részletesen **dinamikus létrehozása** az Azure-fájlmegosztás kötetként állandó.

További információk a Kubernetes szolgáltatásban állandó kötetek, statikus létrehozását, beleértve: [Kubernetes állandó kötetek][kubernetes-volumes].

## <a name="create-storage-account"></a>Storage-fiók létrehozása

Amikor dinamikusan hoz létre egy Azure-fájlmegosztást, egy Kubernetes-kötet, minden olyan storage-fiók is használható, mindaddig, amíg az AKS van **csomópont** erőforráscsoportot. Ez az a a `MC_` előtagot, amely hozta létre az AKS-fürt erőforrásainak üzembe helyezése. Az erőforráscsoport nevét az első a [az resource show] [ az-resource-show] parancsot.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Használja a [az tárfiók létrehozása] [ az-storage-account-create] parancsot a tárfiók létrehozásához.

Frissítés `--resource-group` az előző lépésben gyűjtött az erőforráscsoport nevére és `--name` egy Ön által választott nevére.

```azurecli-interactive
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

> Az Azure Files jelenleg csak a standard szintű storage együttműködve. Ha premium storage szolgáltatást használja, nem fogja tudni a kötet kiépítése.

## <a name="create-storage-class"></a>Tárolási osztály létrehozása

Tárolási osztály hogyan jön létre egy Azure-fájlmegosztás meghatározására szolgál. Egy adott tárfiókhoz a osztály adható meg. Ha nincs megadva tárfiók, egy `skuName` és `location` meg kell adni, és a társított erőforráscsoportokhoz összes storage-fiókok értékeli ki az egyezést.

Az Azure files szolgáltatáshoz a Kubernetes storage osztályai további információkért lásd: [Kubernetes Storage osztályai][kubernetes-storage-classes].

Hozzon létre egy fájlt `azure-file-sc.yaml` , és másolja a következő jegyzékfájlban. Frissítés a `storageAccount` a célként megadott tárfiók nevére. További információ [csatlakoztatási beállítások] szakaszban `mountOptions`.

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

Hozzon létre a storage osztályt a [a kubectl a alkalmazni] [ kubectl-apply] parancsot.

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>Tartós kötet jogcímszabály létrehozása

Tartós kötet jogcím (PVC) a tárolási osztály objektum használatával dinamikusan telepíthet az Azure-fájlmegosztások.

A következő yaml-kódot segítségével hozzon létre egy tartós kötet jogcím `5GB` méretűnek és `ReadWriteMany` hozzáférést. Hozzáférési mód további információkért lásd: a [Kubernetes tartós kötet] [ access-modes] dokumentációját.

Hozzon létre egy fájlt `azure-file-pvc.yaml` másolja be a következő yaml-kódot. Győződjön meg arról, hogy a `storageClassName` megegyezik az előző lépésben létrehozott storage osztály.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Hozzon létre a tartós kötet jogcímet a [a kubectl a alkalmazni] [ kubectl-apply] parancsot.

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

Ha befejeződött, a fájlmegosztást hoz létre. Kubernetes titkos kulcs is létrehoz, amely tartalmazza a kapcsolati adatokat és hitelesítő adatokat.

## <a name="using-the-persistent-volume"></a>Tartós kötet

A következő yaml-kódot hoz létre, amely a tartós kötet jogcím podot `azurefile` , az Azure-fájlmegosztást, csatlakoztathatja a `/mnt/azure` elérési útja.

Hozzon létre egy fájlt `azure-pvc-files.yaml`, és másolja be a következő yaml-kódot. Győződjön meg arról, hogy a `claimName` megegyezik a virtuális az előző lépésben létrehozott kapcsolat.

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

A pod-létrehozása a [a kubectl a alkalmazni] [ kubectl-apply] parancsot.

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
```

Az Azure csatlakoztatott lemezzel most már rendelkezik egy futó pod a `/mnt/azure` könyvtár. Ez a konfiguráció látható, amikor a pod-n keresztül vizsgálatával `kubectl describe pod mypod`.

## <a name="mount-options"></a>Csatlakoztatási beállítások

Alapértelmezett fileMode és dirMode értékek eltérnek a Kubernetes-verzió az alábbi táblázatban leírtak szerint.

| verzió: | érték |
| ---- | ---- |
| V1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 vagy újabb | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 vagy újabb | 0755 |

Ha használja a fürt verziójának megfelelő 1.8.5 vagy nagyobb, és dinamikusan hozza létre a persistant kötet egy olyan tárolási osztállyal, csatlakoztatási beállítások is megadhatók a tárolási osztály objektum. A következő példa készletek `0777`.

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

Ha használja a fürt verziójának megfelelő 1.8.5 vagy nagyobb és a persistant kötet objektum statikusan létrehozása csatlakoztatási lehetőségek meg kell adni a a `PersistentVolume` objektum. persistant kötetek statikusan létrehozásával kapcsolatos további információkért lásd: [statikus állandó kötetek][pv-static].

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  ```

Ha használja a fürt verziójának 1.8.0-as - 1.8.4-es verzióra, a biztonsági környezet adható a `runAsUser` értéke `0`. A Pod biztonsági környezet további információkért lásd: [konfigurálása a biztonsági környezet][kubernetes-security-context].

## <a name="next-steps"></a>További lépések

További tudnivalók a Kubernetes Azure Files használatával állandó köteteket.

> [!div class="nextstepaction"]
> [Kubernetes-beépülő modul az Azure Files számára][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
