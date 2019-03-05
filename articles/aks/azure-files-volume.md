---
title: Több podok statikus kötetet létrehozni az Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan manuálisan hozzon létre egy kötetet az Azure Files a több egyidejű podok Azure Kubernetes Service (AKS) segítségével
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: f8558529df24c0aaede0c58744e17829ec0b5669
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337532"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Manuális létrehozásához és a egy kötet használata Azure-fájlmegosztást az Azure Kubernetes Service (AKS)

Tárolóalapú alkalmazások gyakran kell eléréséhez, és a egy külső adatmennyiség adatok megőrzése. Ha több podok kell az azonos tárolókötethez való egyidejű hozzáférés, az Azure Files használatával történő kapcsolódás használhatja a [Server Message Block (SMB) protokoll][smb-overview]. Ez a cikk bemutatja, hogyan manuálisan hozzon létre egy Azure-fájlmegosztást, és mellékelje egy pod az aks-ben.

A Kubernetes-köteteken további információkért lásd: [tárolási lehetőségek az aks-ben alkalmazásokhoz][concepts-storage].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel] [ aks-quickstart-cli] vagy [az Azure portal használatával][aks-quickstart-portal].

Emellett az Azure CLI 2.0.59 verziójára van szükség, vagy később telepített és konfigurált. Futtatás `az --version` a verzió megkereséséhez. Ha telepíteni vagy frissíteni, tekintse meg kell [Azure CLI telepítése][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Az Azure Files egy Kubernetes-kötetként használhatná, mielőtt létre kell hoznia egy Azure Storage-fiókot és a fájlmegosztást. A következő parancsokat, hozzon létre egy erőforráscsoportot *myAKSShare*, egy tárfiókot, és a egy nevű fájlmegosztást *aksshare*:

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
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Jegyezze fel a tárfiók nevét és a kulcs a parancsprogram kimenete végén látható. Ezekre az értékekre van szükség, az az alábbi lépések egyikét a Kubernetes-kötet létrehozásakor.

## <a name="create-a-kubernetes-secret"></a>A Kubernetes titkos kódjának létrehozása

Kubernetes van szüksége a fájlmegosztás az előző lépésben létrehozott eléréséhez szükséges hitelesítő adatokat. Ezek a hitelesítő adatok vannak tárolva egy [Kubernetes titkos][kubernetes-secret], amelyre hivatkozik egy Kubernetes-podok létrehozásakor.

Használja a `kubectl create secret` paranccsal hozza létre a titkos kulcsot. A következő példában létrehozunk egy megosztott nevű *azure-secret* tölti fel a *azurestorageaccountname* és *azurestorageaccountkey* az előző lépésben. Meglévő Azure storage-fiókot használ, adja meg a fiók nevét és kulcsát.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>A fájlmegosztás kötetként csatlakoztatása

Csatlakoztatása az Azure-fájlmegosztást be a pod, konfigurálja a tároló specifikációja a kötetet. Hozzon létre egy új fájlt `azure-files-pod.yaml` a következő tartalommal. Ha a fájlmegosztást vagy titkos kód neve neve módosította, frissítse a *megosztásnév* és *secretName*. Ha szükséges, frissítse a `mountPath`, azaz az elérési utat, ahol a fájlok megosztása a pod csatlakoztatva van.

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

Használja a `kubectl` paranccsal hozza létre a pod.

```console
kubectl apply -f azure-files-pod.yaml
```

Egy Azure-fájlmegosztási csatlakoztatásának helye a most már rendelkezik egy futó pod */mnt/azure*. Használhat `kubectl describe pod mypod` ellenőrizze, hogy a fájlmegosztás sikeresen csatlakoztatva van. A következő sűrített példához kimenetet jeleníti meg a kötet csatlakoztatva van a tárolóban:

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

Alapértelmezett *fileMode* és *dirMode* érték azonos a szerepkörtárolók Kubernetes-verzió az alábbi táblázatban leírtak szerint.

| version | érték |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| V1.8.6 vagy újabb | 0755 |
| v1.9.0 | 0700 |
| V1.9.1 vagy újabb | 0755 |

Ha használja a fürt verziójának megfelelő 1.8.5 vagy nagyobb és a tartós kötet objektum statikusan létrehozása csatlakoztatási lehetőségek meg kell adni a a *PersistentVolume* objektum.

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

Ha használja a fürt verziójának 1.8.0-as - 1.8.4-es verzióra, a biztonsági környezet adható a *felhasználó* értékre állítva *0*. A Pod biztonsági környezet további információkért lásd: [konfigurálása a biztonsági környezet][kubernetes-security-context].

## <a name="next-steps"></a>További lépések

További kapcsolódó ajánlott eljárások: [ajánlott eljárások a storage és az aks-ben biztonsági mentések][operator-best-practices-storage].

További információ az AKS-fürtök kezelése az Azure Files, lásd: a [Kubernetes beépülő modul az Azure Files számára][kubernetes-files].

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
