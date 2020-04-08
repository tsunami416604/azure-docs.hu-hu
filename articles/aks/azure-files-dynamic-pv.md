---
title: Az Azure Files megosztásdinamikus létrehozása
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre dinamikusan állandó kötetet az Azure Files szolgáltatással több egyidejű podtal az Azure Kubernetes szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 09/12/2019
ms.openlocfilehash: 59b773cd4608187fedb24358eac57715e1c271ea
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803534"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Állandó kötet dinamikus létrehozása és használata az Azure Kubernetes-szolgáltatásban (AKS) az Azure Files szolgáltatással

Az állandó kötet egy olyan tárolódarabot jelöl, amely a Kubernetes-podokkal való használatra lett kiépítve. Egy állandó kötetet egy vagy több podok, és dinamikusan vagy statikusan kiépített. Ha több podnak egyidejűleg kell hozzáférnie ugyanahhoz a tárolókötethez, az Azure Files segítségével csatlakozhat a [Kiszolgálói üzenetblokk (SMB) protokoll][smb-overview]használatával. Ez a cikk bemutatja, hogyan hozhat létre dinamikusan egy Azure Files-megosztást több pod oka egy Azure Kubernetes-fürt (AKS) fürtben.

A Kubernetes-kötetekről az [AKS-ben lévő alkalmazások tárolási beállításai][concepts-storage]című témakörben talál további információt.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="create-a-storage-class"></a>Tárolási osztály létrehozása

Egy tárolási osztály segítségével határozza meg, hogyan jön létre az Azure-fájlmegosztás. A tárfiók automatikusan létrejön a [csomópont erőforráscsoport][node-resource-group] ban használható a tárolási osztály az Azure-fájlmegosztások tárolására. Válasszon a következő [Azure storage redundancia][storage-skus] *skuName:*

* *Standard_LRS* – szabványos helyiredundáns tárolás (LRS)
* *Standard_GRS* - szabványos georedundáns tárolás (GRS)
* *Standard_ZRS* - szabványos zónaredundáns tárolás (ZRS)
* *Standard_RAGRS* - szabványos olvasási hozzáférésű georedundáns tárolás (RA-GRS)
* *Premium_LRS* - prémium szintű helyiredundáns tárolás (LRS)
* *Premium_ZRS* - prémium szintű zóna redundáns tárolás (GRS)

> [!NOTE]
> Az Azure Files támogatja a prémium szintű tárhelyet a Kubernetes 1.13-as vagy újabb verziót futtató AKS-fürtökben, a minimális prémium szintű fájlmegosztás 100 GB

A Kubernetes Azure Files tárolási osztályairól a [Kubernetes storage classes][kubernetes-storage-classes]című témakörben talál további információt.

Hozzon létre `azure-file-sc.yaml` egy elnevezett fájlt, és másolja a következő példajegyzékbe. A *mountOptions csatlakoztatásáról*további információt a [Csatlakoztatási beállítások][mount-options] című szakaszban talál.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

Hozza létre a tárolási osztályt a [kubectl apply][kubectl-apply] paranccsal:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Állandó kötetjogcím létrehozása

Az állandó kötetjogcím (PVC) a tárolási osztály objektumot használja az Azure-fájlmegosztás dinamikus kiépítéséhez. A következő YAML segítségével hozhat létre egy állandó kötet jogcím *5 GB* méretű *ReadWriteMany* hozzáféréssel. A hozzáférési módokról további információt a [Kubernetes állandó kötetdokumentációjában][access-modes] talál.

Most hozzon `azure-file-pvc.yaml` létre egy nevű fájlt, és másolja a következő YAML. Győződjön meg arról, hogy a *storageClassName* megegyezik az utolsó lépésben létrehozott tárolási osztálysal:

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

> [!NOTE]
> Ha a *Premium_LRS* termékváltozatot használja a tárolási osztályhoz, a *minimális tárolási* értéknek *100Gi-nak*kell lennie.

Hozza létre az állandó kötetjogcímet a [kubectl apply][kubectl-apply] paranccsal:

```console
kubectl apply -f azure-file-pvc.yaml
```

Miután elkészült, létrejön a fájlmegosztás. Kubernetes titkos kulcsot is létrehoznak, amely tartalmazza a kapcsolat adatait és a hitelesítő adatokat. Használhatja a [kubectl get parancsot,][kubectl-get] hogy megtekinthesse az állapotát a PVC:

```console
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Az állandó kötet használata

A következő YAML létrehoz egy podot, amely az állandó *kötetjogcím-azurefile-t* használja az Azure-fájlmegosztás csatlakoztatásához a */mnt/azure* elérési úton. Windows Server-tárolók esetén (jelenleg előzetes verzióban az AKS-ben) adjon meg egy *mountPath-ot* a Windows elérési út konvenciója, például a *"D:"* használatával.

Hozzon létre `azure-pvc-files.yaml`egy nevű fájlt, és másolja a következő YAML-fájlba. Győződjön meg arról, hogy a *jogcímnév* megegyezik az utolsó lépésben létrehozott PVC-vel.

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
        claimName: azurefile
```

Hozza létre a pod a [kubectl alkalmazni][kubectl-apply] parancsot.

```console
kubectl apply -f azure-pvc-files.yaml
```

Most már rendelkezik egy futó pod az Azure Files share csatlakoztatva a */mnt/azure* könyvtárba. Ez a konfiguráció látható, ha `kubectl describe pod mypod`ellenőrzi a pod keresztül. A következő kondenzált példa kimenet a tárolóba szerelt kötetet mutatja:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Csatlakoztatási beállítások

A *fileMode* és *dirMode* alapértelmezett értéke *0777* a Kubernetes 1.13.0-s és újabb verzióinál. Ha dinamikusan hozza létre az állandó kötetet egy tárolási osztállyal, a tárolási osztály objektumon csatlakoztatási beállítások adhatók meg. A következő példa *0777-et*állít be:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>További lépések

A kapcsolódó gyakorlati tanácsok értése: [Gyakorlati tanácsok a tároláshoz és a biztonsági mentések az AKS-ben.][operator-best-practices-storage]

További információ a Kubernetes állandó kötetekről az Azure Files használatával.

> [!div class="nextstepaction"]
> [Kubernetes beépülő modul az Azure Files-hoz][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
