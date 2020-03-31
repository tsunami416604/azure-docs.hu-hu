---
title: AAD-identitás használata a webszolgáltatással
titleSuffix: Azure Machine Learning
description: AAD-identitás használata az Azure Kubernetes-szolgáltatás webszolgáltatásával a felhőbeli erőforrások eléréséhez a pontozás során.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122843"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Azure AD-identitás használata a gépi tanulási webszolgáltatással az Azure Kubernetes szolgáltatásban

Ebben az útmutatóban megtudhatja, hogyan rendelhet hozzá egy Azure Active Directory (AAD) identitást az Azure Kubernetes-szolgáltatás üzembe helyezett gépi tanulási modelljéhez. Az [AAD Pod Identity](https://github.com/Azure/aad-pod-identity) projekt lehetővé teszi az alkalmazások számára, hogy biztonságosan hozzáférjenek a felhőbeli erőforrásokhoz az AAD-vel egy [felügyelt identitás-](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) és Kubernetes-primitívek használatával. Ez lehetővé teszi, hogy a webszolgáltatás biztonságosan hozzáférjen az Azure-erőforrásokhoz anélkül, hogy hitelesítő adatokat kellene beágyaznia, vagy közvetlenül a parancsfájlon belül kell kezelnie a `score.py` jogkivonatokat. Ez a cikk ismerteti az Azure Kubernetes-szolgáltatás fürtjében az Azure Kubernetes-szolgáltatás fürtjében létrehozott és telepíthető Azure-identitás létrehozásának és telepítésének lépéseit, és az identitás hozzárendelését az üzembe helyezett webszolgáltatáshoz.

## <a name="prerequisites"></a>Előfeltételek

- A [Machine Learning szolgáltatás Azure CLI-bővítménye](reference-azure-machine-learning-cli.md), az Azure Machine Learning [SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)vagy az Azure Machine Learning Visual Studio Code [bővítmény.](tutorial-setup-vscode-extension.md)

- Hozzáférés az AKS-fürthöz a `kubectl` paranccsal. További információ: [Csatlakozás a fürthöz](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- Az AKS-fürtbe üzembe helyezett Azure Machine Learning webszolgáltatás.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Azure-identitás létrehozása és telepítése az AKS-fürtben

1. Annak megállapításához, hogy az AKS-fürt engedélyezve van-e az RBAC-on, használja a következő parancsot:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Ez a parancs `true` akkor adja vissza, ha az RBAC engedélyezve van. Ez az érték határozza meg a következő lépésben használandó parancsot.

1. Az [AAD Pod Identity](https://github.com/Azure/aad-pod-identity#getting-started) AKS-fürtben történő telepítéséhez használja az alábbi parancsok egyikét:

    * Ha az AKS-fürt **rbac engedélyezve** van, használja a következő parancsot:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Ha az AKS-fürtön **nincs engedélyezve az RBAC,** használja a következő parancsot:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        A parancs kimenete hasonló a következő szöveghez:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Hozzon létre egy Azure-identitást](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) az AAD Pod Identity projektlapon látható lépéseket követve.

1. [Telepítse az Azure Identity](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) az AAD Pod Identity projektlapján látható lépéseket követve.

1. [Telepítse az Azure Identity Binding](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) az AAD Pod Identity projektlapján látható lépéseket követve.

1. Ha az előző lépésben létrehozott Azure-identitás nem ugyanabban az erőforráscsoportban van, mint az AKS-fürt, kövesse az [Engedélyek beállítása a MIC-hez](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) az AAD Pod Identity projektlapban látható lépéseket követve.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Azure Identity hozzárendelése gépi tanulási webszolgáltatáshoz

A következő lépések az előző szakaszban létrehozott Azure Identity-t használják, és egy **választócímkén**keresztül rendelik hozzá az AKS-webszolgáltatáshoz.

Először azonosítsa a központi telepítés nevét és névterét az AKS-fürtben, amelyhez hozzá szeretné rendelni az Azure Identity-t. Ezt az információt a következő parancs futtatásával szerezheti be. A névterek kell az Azure Machine Learning munkaterület nevét, és a központi telepítési nevet kell a végpont neve, ahogy a portálon látható.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Adja hozzá az Azure Identity selector címkét a központi telepítéshez a központi telepítési specifikáció szerkesztésével. A választóértéknek az [Azure Identity Binding telepítése 5.](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Az Azure Identity-választó címke hozzáadásához szerkesztsd a központi telepítést. Lépjen a következő `/spec/template/metadata/labels`szakaszra a csoportban. Ekkor megjelenik az `isazuremlapp: “true”`értékek, például a . Adja hozzá az aad-pod-identity címkét, ahogy az alább látható.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

A címke helyes hozzáadásának ellenőrzéséhez futtassa a következő parancsot.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Az összes pod állapot megtekintéséhez futtassa a következő parancsot.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Miután a podok már működik, a webes szolgáltatások ehhez a központi telepítéshez most már képes elérni az Azure-erőforrások at az Azure Identity anélkül, hogy beágyazza a hitelesítő adatokat a kódot. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>Rendelje hozzá a megfelelő szerepköröket az Azure-identitáshoz

[Rendelje hozzá az Azure felügyelt identitását a megfelelő szerepkörökkel](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) más Azure-erőforrások eléréséhez. Győződjön meg arról, hogy a hozzárendelt szerepkörök a megfelelő **adatműveletekkel rendelkeznek.** Például a [Storage Blob Data Reader szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) olvasási engedélyekkel rendelkezik a storage blob, míg az általános olvasó [szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) nem.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Az Azure Identity használata a gépi tanulási webszolgáltatással

Telepítsen egy modellt az AKS-fürtre. A `score.py` parancsfájl tartalmazhat műveleteket, amelyek az Azure-erőforrások, amelyek az Azure Identity hozzáféréssel rendelkezik. Győződjön meg arról, hogy telepítette a szükséges ügyfélkönyvtár-függőségeket ahhoz az erőforráshoz, amelyhez hozzá szeretne férni. Az alábbiakban néhány példát, hogyan használhatja az Azure Identity-t a szolgáltatás különböző Azure-erőforrások eléréséhez.

### <a name="access-key-vault-from-your-web-service"></a>Hozzáférés a Key Vaulthoz a webszolgáltatásból

Ha az Azure-identitás olvasási hozzáférést adott egy titkos `score.py` kulcs egy **Key Vaultban,** a következő kód használatával érheti el.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

### <a name="access-blob-from-your-web-service"></a>Access Blob a webszolgáltatásból

Ha az Azure Identity olvasási hozzáférést adott a `score.py` **storage blobon**belüli adatokhoz, a következő kód használatával érheti el azokat.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>További lépések

* A Python Azure Identity-ügyfélkódtár használatával kapcsolatos további információkért tekintse meg a [GitHub-tárban.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python)
* A modellek Azure Kubernetes service-fürtökbe való üzembe helyezéséről részletes útmutatót a [útmutatóban](how-to-deploy-azure-kubernetes-service.md)talál.