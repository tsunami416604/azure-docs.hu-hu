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
ms.date: 10/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6e88c590e11aa8d2f4ae17e8b5e164483f0a6820
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="using-azure-files-with-kubernetes"></a>Az Azure Files használata Kubernetes

Tároló-alapú alkalmazások gyakran kell elérni, és egy külső adatmennyiség adatok megőrzéséhez. Az Azure files változtatás nélkül használhatók az külső adattárolót. Ez a cikk adatokat Azure fájlok használata az Azure Tárolószolgáltatásban Kubernetes kötetként.

Kubernetes köteteken további információkért lásd: [Kubernetes kötetek][kubernetes-volumes].

## <a name="creating-a-file-share"></a>Fájlmegosztás létrehozása

Azure Tárolószolgáltatás egy meglévő Azure fájlmegosztás használható. Ha szeretne létrehozni egyet, válasszon a következő parancsokat.

Hozzon létre egy erőforráscsoportot az Azure File megosztás használatára vonatkozó a [az csoport létrehozása] [ az-group-create] parancsot. Az erőforráscsoport a tárfiók és a Kubernetes fürt ugyanabban a régióban kell lennie.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

Használja a [az storage-fiók létrehozása] [ az-storage-create] parancsot egy Azure Storage-fiók létrehozásához. A tárfiók nevének egyedinek kell lennie. Frissítse az értéket a `--name` argumentum egy egyedi érték.

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

Használja a [az tárolási fióklista kulcsok ] [ az-storage-key-list] parancs sikeresen lefut a kulcsot. Frissítse az értéket a `--account-name` argumentum egyedi fiók nevével.

Jegyezze fel az értékek egyik használatos a későbbi lépésekben.

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

Használja a [az storage-megosztás létrehozása] [ az-storage-share-create] parancs az Azure fájlmegosztás létrehozásához. Frissítés a `--account-key` a értékkel rendelkező gyűjti az utolsó lépésben.

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>Kubernetes titkos kulcs létrehozása

Kubernetes kell a fájlmegosztás eléréséhez szükséges hitelesítő adatokat. Ahelyett, hogy az Azure Storage-fiók nevét és minden pod kulcs tárolása, egyszer a tárolja egy [Kubernetes titkos] [ kubernetes-secret] és minden Azure fájlok kötet által hivatkozott. 

Egy Kubernetes titkos jegyzékben szereplő értékek base64 kódolásúnak kell lennie. Az alábbi parancsokkal kódolt értéket ad vissza.

Első lépésként kódolja a tárfiók nevét. Cserélje le `storage-account` az Azure storage-fiók nevével.

```azurecli-interactive
echo -n <storage-account> | base64
```

Ezt követően a fiók tárelérési kulcs szükséges. A következő parancsot a kódolt kulcs adja vissza. Cserélje le `storage-key` a korábbi lépésben gyűjtött kulccsal

```azurecli-interactive
echo -n <storage-key> | base64
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

Használja a [kubectl alkalmazása] [ kubectl-apply] parancsot a titkos kulcs létrehozásához.

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>Fájlmegosztás csatlakoztatása kötetként

A pod be az Azure-fájlok megosztás a kötetet a spec konfigurálásával lehet csatlakoztatni. Hozzon létre egy új fájlt `azure-files-pod.yml` a következő tartalommal. Frissítés `share-name` az Azure-fájlok neve azonos.

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
      shareName: <share-name>
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
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create