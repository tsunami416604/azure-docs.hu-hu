---
title: Az Azure használata az aks-sel
description: Azure-lemezek használata az aks-sel
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 0479e4d80b7490db170255d47ef3190bb744d2d8
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901493"
---
# <a name="volumes-with-azure-files"></a>Az Azure files-kötetek

Tárolóalapú alkalmazások gyakran kell eléréséhez, és a egy külső adatmennyiség adatok megőrzése. Az Azure files a külső adatok tárolását is használható. Ez a cikk részletesen, az Azure files használata az Azure Kubernetes Service-ben a Kubernetes-kötet.

A Kubernetes-köteteken további információkért lásd: [Kubernetes kötetek][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Mielőtt egy Azure-fájlmegosztás, egy Kubernetes-kötet, létre kell hoznia egy Azure Storage-fiókot és a fájlmegosztást. A következő szkriptet a feladatok végrehajtásához használható. Jegyezze fel, vagy frissítse a paraméterek értékeit, ezek közül néhányat a Kubernetes-kötet létrehozásakor van szükség.

```azurecli-interactive
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

## <a name="create-kubernetes-secret"></a>Kubernetes titkos kód létrehozása

Kubernetes van szüksége a fájlmegosztás eléréséhez szükséges hitelesítő adatokat. Ezek a hitelesítő adatok vannak tárolva egy [Kubernetes titkos][kubernetes-secret], amelyre hivatkozik egy Kubernetes-podok létrehozásakor.

A következő paranccsal hozzon létre a titkos kulcsot. Cserélje le `STORAGE_ACCOUNT_NAME` az a tárfiók nevét, és `STORAGE_ACCOUNT_KEY` az a tárfiók kulcsát.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>Fájlmegosztás csatlakoztatása kötetként

Csatlakoztassa az Azure-fájlmegosztást be a pod a kötetet a specifikációja konfigurálásával. Hozzon létre egy új fájlt `azure-files-pod.yaml` a következő tartalommal. Frissítés `aksshare` név, az Azure Files-megosztás.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
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

A kubectl használatával hozzon létre egy pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

A futó tárolót a csatlakoztatott Azure-fájlmegosztást most már a `/mnt/azure` könyvtár.  Láthatja, hogy a kötet csatlakoztatása, amikor be a pod-n keresztül `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>További lépések

További információ az Azure Files segítségével Kubernetes-köteteket.

> [!div class="nextstepaction"]
> [Kubernetes-beépülő modul az Azure Files számára][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
