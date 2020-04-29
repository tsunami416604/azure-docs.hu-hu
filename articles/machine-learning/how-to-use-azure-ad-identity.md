---
title: HRE-identitás használata a webszolgáltatáshoz
titleSuffix: Azure Machine Learning
description: A HRE-identitást az Azure Kubernetes szolgáltatásban található webszolgáltatáshoz használva érheti el a Felhőbeli erőforrásokat a pontozás során.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77122843"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Azure AD-identitás használata az Azure Kubernetes Service-ben a Machine learning webszolgáltatással

Ebben az útmutatóban megtudhatja, hogyan rendelhet hozzá egy Azure Active Directory (HRE) identitást az üzembe helyezett Machine learning-modellhez az Azure Kubernetes szolgáltatásban. A [HRE Pod Identity](https://github.com/Azure/aad-pod-identity) projekt lehetővé teszi az alkalmazások számára, hogy biztonságosan hozzáférjenek a HRE a [felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) -és Kubernetes primitívek használatával. Ez lehetővé teszi a webszolgáltatás számára az Azure-erőforrások biztonságos elérését anélkül, hogy hitelesítő adatokat kellene beágyaznia vagy a `score.py` tokeneket közvetlenül a szkriptben kezelnie. Ez a cikk bemutatja, hogyan hozhat létre és telepíthet Azure-identitást az Azure Kubernetes Service-fürtben, és hogyan rendelheti hozzá az identitást az üzembe helyezett webszolgáltatáshoz.

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure CLI-bővítmény a Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), a [pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-hoz vagy a [Visual Studio Code](tutorial-setup-vscode-extension.md)-hoz készült Azure Machine learning.

- Hozzáférés az AK-fürthöz a `kubectl` parancs használatával. További információ: [Kapcsolódás a fürthöz](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)

- Egy Azure Machine Learning webszolgáltatás üzembe helyezése az AK-fürtön.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Azure-identitás létrehozása és telepítése az AK-fürtben

1. A következő paranccsal megállapíthatja, hogy a RBAC engedélyezve van-e az AK-fürtön:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Ez a parancs egy értéket ad `true` vissza, ha a RBAC engedélyezve van. Ez az érték határozza meg a következő lépésben használandó parancsot.

1. Ha az [HRE Pod-identitást](https://github.com/Azure/aad-pod-identity#getting-started) az AK-fürtön szeretné telepíteni, használja a következő parancsok egyikét:

    * Ha az AK-fürtön engedélyezve van a **RBAC** , használja a következő parancsot:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Ha az AK-fürthöz **nincs engedélyezve a RBAC**, használja a következő parancsot:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        A parancs kimenete az alábbi szöveghez hasonló:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Hozzon létre egy Azure-identitást](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) az HRE Pod Identity Project oldalon látható lépések követésével.

1. [Telepítse az Azure-identitást](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) az HRE Pod Identity Project oldalon látható lépések követésével.

1. [Telepítse az Azure Identity-kötést](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) a HRE Pod Identity Project oldalon látható lépések követésével.

1. Ha az előző lépésben létrehozott Azure-identitás nem ugyanabban az erőforráscsoporthoz van, mint az AK-fürt, kövesse az HRE Pod Identity Project oldalon látható lépéseket követve a [MIC engedélyeinek beállítása](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) című témakört.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Azure-identitás kiosztása a Machine learning-webszolgáltatáshoz

A következő lépések az előző szakaszban létrehozott Azure-identitást használják, és a **választó címkén**keresztül rendelheti hozzá az AK-webszolgáltatáshoz.

Először azonosítsa az üzemelő példány nevét és névterét az AK-fürtben, amelyhez hozzá szeretné rendelni az Azure-identitást. Ezt az információt a következő parancs futtatásával kérheti le. A névtereknek a Azure Machine Learning-munkaterület nevével kell rendelkezniük, és a telepítési névnek a-portálon láthatónak kell lennie a végpont neveként.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Az üzembe helyezési specifikáció szerkesztésével adja hozzá az Azure Identity választó címkét a központi telepítéshez. A választó értékének az [Azure Identity-kötés telepítése](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)5. lépésében megadott értéknek kell lennie.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Szerkessze az üzembe helyezést az Azure Identity választó címkéjének hozzáadásához. Lépjen a következő szakaszra `/spec/template/metadata/labels`. A `isazuremlapp: “true”`következő értékeket kell megjelennie:. Adja hozzá az alább látható HRE-Pod-Identity címkét.

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

A címke megfelelő hozzáadásának ellenőrzéséhez futtassa a következő parancsot.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Az összes Pod állapot megjelenítéséhez futtassa a következő parancsot.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Miután a hüvely működik, az üzembe helyezéshez használt webszolgáltatások mostantól hozzáférhetnek az Azure-identitáshoz az Azure-identitáson keresztül anélkül, hogy be kellene ágyaznia a hitelesítő adatokat a kódban. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>A megfelelő szerepkörök kiosztása az Azure-identitáshoz

[Rendelje hozzá az Azure felügyelt identitását megfelelő szerepkörökkel](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) más Azure-erőforrások eléréséhez. Győződjön meg arról, hogy a hozzárendelt szerepkörök megfelelő **Adatműveletekkel**rendelkeznek. Előfordulhat például, hogy a [Storage blob-Adatolvasó szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) olvasási engedéllyel rendelkezik a Storage-blobhoz, amíg az általános [olvasó szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) nem lesz.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Az Azure Identity használata a Machine learning webszolgáltatással

Modell üzembe helyezése az AK-fürtön. A `score.py` szkript olyan műveleteket tartalmazhat, amelyek az Azure-identitáshoz hozzáférő Azure-erőforrásokra mutatnak. Győződjön meg arról, hogy telepítette a szükséges ügyféloldali függvénytár-függőségeket ahhoz az erőforráshoz, amelyhez hozzáférni próbál. Az alábbiakban néhány példát láthat arra, hogyan használhatja Azure-identitását különböző Azure-erőforrások elérésére a szolgáltatásból.

### <a name="access-key-vault-from-your-web-service"></a>Hozzáférés Key Vault a webszolgáltatásból

Ha az Azure Identity olvasási hozzáférést kapott egy **Key Vaultban**található titkos kulcshoz, az `score.py` a következő kód használatával férhet hozzá.

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

### <a name="access-blob-from-your-web-service"></a>A blob elérése a webszolgáltatásból

Ha az Azure Identity olvasási hozzáférést kapott a **Storage-blobon**belüli adatokhoz, az `score.py` a következő kód használatával férhet hozzá.

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

* A Python Azure Identity ügyféloldali kódtár használatával kapcsolatos további információkért tekintse meg a [tárházat](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) a githubon.
* A modellek Azure Kubernetes Service-fürtökön való üzembe helyezésével kapcsolatos részletes útmutatóért lásd: [útmutató](how-to-deploy-azure-kubernetes-service.md).