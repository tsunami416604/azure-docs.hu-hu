---
title: "Az Azure File használata AKS |} Microsoft Docs"
description: "Azure-lemezeket használata AKS"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/17/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: bae60e7f78934deacac173767ca3013ce93cf9ad
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Az Azure Files használata Kubernetes

Tároló-alapú alkalmazások gyakran kell elérni, és egy külső adatmennyiség adatok megőrzéséhez. Az Azure files változtatás nélkül használhatók az külső adattárolót. Ez a cikk adatokat Azure fájlok használata az Azure Tárolószolgáltatásban Kubernetes kötetként.

Kubernetes köteteken további információkért lásd: [Kubernetes kötetek][kubernetes-volumes].

## <a name="create-an-azure-file-share"></a>Az Azure-fájlmegosztás létrehozása

Használata előtt egy Azure fájlmegosztás Kubernetes kötetként, létre kell hoznia egy Azure Storage-fiók és a fájlmegosztást. A következő parancsfájl segítségével ezeket a feladatokat. Jegyezze fel, vagy frissítse a paraméterek értékeit, néhány esetben van szükség, amikor a Kubernetes kötet létrehozásához.

```azurecli-interactive
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
```

## <a name="create-kubernetes-secret"></a>Kubernetes titkos kulcs létrehozása

Kubernetes kell a fájlmegosztás eléréséhez szükséges hitelesítő adatokat. Ezek a hitelesítő adatok vannak tárolva egy [Kubernetes titkos][kubernetes-secret], amely hivatkozik a Kubernetes pod létrehozásakor.

Egy Kubernetes titkos létrehozása esetén a titkos értékek base64 kódolásúnak kell lennie.

Első lépésként kódolja a tárfiók nevét. Szükség esetén cserélje le a `$AKS_PERS_STORAGE_ACCOUNT_NAME` az Azure storage-fiók nevével.

```azurecli-interactive
echo -n $AKS_PERS_STORAGE_ACCOUNT_NAME | base64
```

A következő kódolja a tárfiók kulcsára. Szükség esetén cserélje le a `$STORAGE_KEY` az Azure storage-fiók kulcs neve.

```azurecli-interactive
echo -n $STORAGE_KEY | base64
```

Hozzon létre egy fájlt `azure-secret.yml` és a következő YAM másolja. Frissítés a `azurestorageaccountname` és `azurestorageaccountkey` értékeket a base64 kódolású értékeket az előző lépésben beolvasott.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

Használja a [kubectl létrehozása] [ kubectl-create] parancsot a titkos kulcs létrehozásához.

```azurecli-interactive
kubectl create -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>Fájlmegosztás csatlakoztatása kötetként

A pod be az Azure-fájlok megosztás a kötetet a spec konfigurálásával lehet csatlakoztatni. Hozzon létre egy új fájlt `azure-files-pod.yml` a következő tartalommal. Frissítés `aksshare` az Azure-fájlok neve azonos.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
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
kubectl apply -f azure-files-pod.yml
```

Az Azure fájlmegosztás-e csatlakoztatva a most már rendelkezik egy futó tároló a `/mnt/azure` könyvtár. Láthatja, hogy a kötet csatlakoztatási vizsgálatakor ellenőrizze a pod keresztül `kubectl describe pod azure-files-pod`.

## <a name="next-steps"></a>Következő lépések

További tudnivalók Kubernetes kötetek Azure fájlokat használja.

> [!div class="nextstepaction"]
> [Azure-fájlok Kubernetes beépülő modul](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create