---
title: Azure-fájlok megosztásának manuális létrehozása
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre manuálisan kötetet az Azure Files szolgáltatással több egyidejű podtal való használatra az Azure Kubernetes-szolgáltatásban (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 412b7158ea366eefb1c3e9c1d2586d54c316aa6c
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803449"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Kötet manuális létrehozása és használata az Azure Files megosztásával az Azure Kubernetes szolgáltatásban (AKS)

A tárolóalapú alkalmazásoknak gyakran kell hozzáférniük és meg kell őrizniük az adatokat egy külső adatköteten. Ha több podnak egyidejűleg kell hozzáférnie ugyanahhoz a tárolókötethez, az Azure Files segítségével csatlakozhat a [Kiszolgálói üzenetblokk (SMB) protokoll][smb-overview]használatával. Ez a cikk bemutatja, hogyan hozhat létre manuálisan egy Azure Files-megosztást, és csatolhatja azt egy pod hoz az AKS-ben.

A Kubernetes-kötetekről az [AKS-ben lévő alkalmazások tárolási beállításai][concepts-storage]című témakörben talál további információt.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Az Azure CLI 2.0.59-es vagy újabb verziójára is szüksége van telepítve és konfigurálva. Futtassa `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni kell, olvassa el [az Azure CLI telepítése][install-azure-cli]című témakört.

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Az Azure Files Kubernetes-kötetként való használata előtt létre kell hoznia egy Azure Storage-fiókot és a fájlmegosztást. A következő parancsok létrehoznak egy *myAKSShare*nevű erőforráscsoportot , egy tárfiókot és egy *aksshare*nevű Fájlok megosztást:

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Jegyezze fel a tárfiók nevét és a parancsfájl kimenetének végén látható kulcsot. Ezekre az értékekre akkor van szükség, ha a Következő lépések egyikében hozza létre a Kubernetes-kötetet.

## <a name="create-a-kubernetes-secret"></a>Kubernetes-titok létrehozása

Kubernetes hitelesítő adatokat kell elérni az előző lépésben létrehozott fájlmegosztáseléréséhez. Ezek a hitelesítő adatok egy [Kubernetes titkos adattárában][kubernetes-secret]tárolódnak, amelyre a Kubernetes-pod létrehozásakor hivatkoznak.

A `kubectl create secret` paranccsal hozd létre a titkot. A következő példa létrehoz egy megosztott *nevű azure-secret,* és feltölti az *azurestorageaccountname* és *azurestorageaccountkey* az előző lépésből. Meglévő Azure-tárfiók használatához adja meg a fiók nevét és kulcsát.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>A fájlmegosztás csatlakoztatása kötetként

Az Azure Files share csatlakoztatásához a pod, konfigurálja a kötetet `azure-files-pod.yaml` a tárolóban spec. Hozzon létre egy új nevű fájlt a következő tartalommal. Ha módosította a Fájlok megosztásvagy titkos név nevét, frissítse a *shareName* és *secretName nevet.* Ha szükséges, `mountPath`frissítse a , amely az elérési utat, ahol a fájlok megosztása van csatlakoztatva a pod. Windows Server-tárolók esetén (jelenleg előzetes verzióban az AKS-ben) adjon meg egy *mountPath-ot* a Windows elérési út konvenciója, például a *"D:"* használatával.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

A `kubectl` parancs segítségével hozza létre a pod.

```console
kubectl apply -f azure-files-pod.yaml
```

Most már rendelkezik egy futó pod egy Azure Files megosztás csatlakoztatva */mnt/azure.* A megosztás `kubectl describe pod mypod` sikeres csatlakoztatásának ellenőrzéséhez használható. A következő kondenzált példa kimenet a tárolóba szerelt kötetet mutatja:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-options"></a>Csatlakoztatási beállítások

A *fileMode* és *dirMode* alapértelmezett értéke *0755* a Kubernetes 1.9.1-es és újabb verzióinál. Ha egy fürt et használ a Kubernetes 1.8.5-ös vagy újabb verziójával, és statikusan hozza létre az állandó kötetobjektumet, a csatlakoztatási beállításokat meg kell adni az *PersistentVolume* objektumon. A következő példa *0777-et*állít be:

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
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Ha az 1.8.0-1.8.4-es verzióból álló fürtöt használja, biztonsági környezet et lehet megadni úgy, hogy a *runAsUser* értéke *0.* A Pod biztonsági környezetről a [Biztonsági környezet konfigurálása][kubernetes-security-context]című témakörben talál további információt.

A csatlakoztatási beállítások frissítéséhez hozzon létre egy *azurefile-mount-options-pv.yaml* fájlt *persistentvolume .* Például:

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
  storageClassName: azurefile
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Hozzon létre egy *azurefile-mount-options-pvc.yaml* fájlt egy *PersistentVolumeClaim* használatával, amely a *PersistentVolume parancsot*használja. Például:

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

A `kubectl` parancsok segítségével hozza létre a *PersistentVolume* és *persistentVolumeClaim*.

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Ellenőrizze, hogy a *PersistentVolumeClaim* létrejött-e, és a *PersistentVolume kötethez van-e kötve.*

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Frissítse a tároló specifikációját a *PersistentVolumeClaim-re* való hivatkozáshoz, és frissítse a podot. Például:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>További lépések

A kapcsolódó gyakorlati tanácsok értése: [Gyakorlati tanácsok a tároláshoz és a biztonsági mentések az AKS-ben.][operator-best-practices-storage]

Az Azure Files [kubernetes beépülő moduljában][kubernetes-files]további információt az Azure Files Kubers-fürtökkel kapcsolatos együttműködéséről talál.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
