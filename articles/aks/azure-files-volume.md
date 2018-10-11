---
title: Több podok statikus kötetet létrehozni az Azure Kubernetes Service (AKS)
description: Ismerje meg, hogyan manuálisan hozzon létre egy kötetet az Azure Files a több egyidejű podok Azure Kubernetes Service (AKS) segítségével
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 1a8609dbf5fa1c1e7d5f4e35b081ecaa09994eb6
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068077"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Manuális létrehozásához és a egy kötet használata Azure-fájlmegosztást az Azure Kubernetes Service (AKS)

Tárolóalapú alkalmazások gyakran kell eléréséhez, és a egy külső adatmennyiség adatok megőrzése. Ha több podok kell az azonos tárolókötethez való egyidejű hozzáférés, az Azure Files használatával történő kapcsolódás használhatja a [Server Message Block (SMB) protokoll][smb-overview]. Ez a cikk bemutatja, hogyan manuálisan hozzon létre egy Azure-fájlmegosztást, és mellékelje egy pod az aks-ben.

A Kubernetes-köteteken további információkért lásd: [Kubernetes kötetek][kubernetes-volumes].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk azt feltételezi, hogy egy meglévő AKS-fürtöt. Ha egy AKS-fürtre van szüksége, tekintse meg az AKS gyors [az Azure CLI-vel] [ aks-quickstart-cli] vagy [az Azure portal használatával][aks-quickstart-portal].

Emellett az Azure CLI 2.0.46 verziójára van szükség, vagy később telepített és konfigurált. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Az Azure Files egy Kubernetes-kötetként használhatná, mielőtt létre kell hoznia egy Azure Storage-fiókot és a fájlmegosztást. A következő parancsfájl egy nevű erőforráscsoportot hoz létre *myAKSShare*, egy tárfiókot, és a egy nevű fájlmegosztást *aksshare*:

```azurecli
#!/bin/bash

# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
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

```azurecli-interactive
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
      Started:      Mon, 08 Oct 2018 19:28:34 +0000
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

## <a name="next-steps"></a>További lépések

További információ az AKS-fürtök kezelése az Azure Files, lásd: a [Kubernetes beépülő modul az Azure Files számára][kubernetes-files].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
