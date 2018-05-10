---
title: Az Azure File használata AKS
description: Azure-lemezeket használata AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e0e62d37fbcc8f296deaee8e0f6658c0f72321ad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="volumes-with-azure-files"></a>Az Azure files kötetek

Tároló-alapú alkalmazások gyakran kell elérni, és egy külső adatmennyiség adatok megőrzéséhez. Az Azure files változtatás nélkül használhatók az külső adattárolót. A cikk adatokat az Azure files Kubernetes kötetként az Azure Kubernetes szolgáltatás használatával.

Kubernetes köteteken további információkért lásd: [Kubernetes kötetek][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Használata előtt egy Azure fájlmegosztás Kubernetes kötetként, létre kell hoznia egy Azure Storage-fiók és a fájlmegosztást. A következő parancsfájl segítségével ezeket a feladatokat. Jegyezze fel, vagy frissítse a paraméterek értékeit, néhány esetben van szükség, amikor a Kubernetes kötet létrehozásához.

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
echo Storgae account key: $STORAGE_KEY
```

## <a name="create-kubernetes-secret"></a>Kubernetes titkos kulcs létrehozása

Kubernetes kell a fájlmegosztás eléréséhez szükséges hitelesítő adatokat. Ezek a hitelesítő adatok vannak tárolva egy [Kubernetes titkos][kubernetes-secret], amely hivatkozik a Kubernetes pod létrehozásakor.

Az alábbi parancs segítségével hozzon létre a titkos kulcsot. Cserélje le `STORAGE_ACCOUNT_NAME` rendelkező a tárfiók nevére, és `STORAGE_ACCOUNT_KEY` a tárolási kulccsal.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>Fájlmegosztás csatlakoztatása kötetként

A kötet konfigurálása a saját egyedi csatlakoztassa azokat a pod az Azure-fájlok megosztást. Hozzon létre egy új fájlt `azure-files-pod.yaml` a következő tartalommal. Frissítés `aksshare` az Azure-fájlok neve azonos.

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

Kubectl segítségével hozzon létre egy pod.

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

Az Azure fájlmegosztás-e csatlakoztatva a most már rendelkezik egy futó tároló a `/mnt/azure` könyvtár.  Láthatja, hogy a kötet csatlakoztatási vizsgálatakor ellenőrizze a pod keresztül `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>További lépések

További tudnivalók Kubernetes kötetek Azure fájlokat használja.

> [!div class="nextstepaction"]
> [Azure-fájlok Kubernetes beépülő modul][kubernetes-files]

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
