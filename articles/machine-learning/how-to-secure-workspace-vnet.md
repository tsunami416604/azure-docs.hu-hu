---
title: Azure Machine Learning munkaterület biztonságossá tétele virtuális hálózatokkal
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning munkaterület és a kapcsolódó erőforrások védelméhez használjon elkülönített Azure-Virtual Network.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, tracking-python
ms.openlocfilehash: e718ed13cfd67092b50b42584d861a2bcf5dacc5
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664556"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Azure Machine Learning munkaterület biztonságossá tétele virtuális hálózatokkal
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan védheti meg egy Azure Machine Learning munkaterületet és a hozzá tartozó erőforrásokat egy virtuális hálózatban.


Ez a cikk egy öt részes sorozat második része, amely végigvezeti egy Azure Machine Learning munkafolyamat biztonságossá tételének lépésein. Javasoljuk, hogy először olvassa el az első [részt: VNet – áttekintés](how-to-network-security-overview.md) az általános architektúra megismeréséhez. 

Tekintse meg a sorozat egyéb cikkeit:

[1. VNet – áttekintés](how-to-network-security-overview.md)  >  **2. A**  >  [3. munkaterület védelme Gondoskodjon a 4. képzési környezet védelméről](how-to-secure-training-vnet.md)  >  [. Gondoskodjon az 5. következtetési környezet védelméről](how-to-secure-inferencing-vnet.md)  >  [. A Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)

Ebből a cikkből megtudhatja, hogyan engedélyezheti a következő munkaterület-erőforrásokat egy virtuális hálózaton:
> [!div class="checklist"]
> - Azure Machine Learning-munkaterület
> - Azure Storage-fiókok
> - Adattárolók és adatkészletek Azure Machine Learning
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Előfeltételek

+ Olvassa el a [hálózati biztonság áttekintése című](how-to-network-security-overview.md) cikket a gyakori virtuális hálózati forgatókönyvek és az általános virtuális hálózati architektúra megismeréséhez.

+ Meglévő virtuális hálózat és alhálózat a számítási erőforrásokkal való használatra.

+ Ha erőforrásokat szeretne telepíteni egy virtuális hálózatba vagy alhálózatba, a felhasználói fióknak rendelkeznie kell a következő műveletekhez szükséges engedélyekkel az Azure szerepköralapú hozzáférés-vezérlés (RBAC) szolgáltatásban:

    - "Microsoft. Network/virtualNetworks/JOIN/Action" a virtuális hálózati erőforráson.
    - "Microsoft. Network/virtualNetworks/subnet/JOIN/Action" az alhálózati erőforráson.

    A hálózatkezeléssel való RBAC kapcsolatos további információkért tekintse meg a [hálózatkezelés beépített szerepkörei](/azure/role-based-access-control/built-in-roles#networking) című témakört.


## <a name="secure-the-workspace-with-private-endpoint"></a>Munkaterület védelme privát végponttal

Az Azure Private link segítségével privát végponton keresztül csatlakozhat a munkaterülethez. A magánhálózati végpont a virtuális hálózaton belüli magánhálózati IP-címek készlete. Ezután korlátozhatja a hozzáférést a munkaterülethez, hogy csak a magánhálózati IP-címeken keresztül történjen. A privát hivatkozás segít csökkenteni az adatkiszűrése kockázatát.

A privát kapcsolati munkaterület beállításával kapcsolatos további információkért lásd: [privát hivatkozás konfigurálása](how-to-configure-private-link.md).


## <a name="secure-azure-storage-accounts"></a>Azure Storage-fiókok biztonságossá tétele

Ebből a szakaszból megtudhatja, hogyan védheti meg az Azure Storage-fiókot a szolgáltatási végpontok használatával. A privát végpontokat azonban használhatja az Azure Storage biztonságossá tételéhez is. További információ: [privát végpontok használata az Azure Storage](../storage/common/storage-private-endpoints.md)-hoz.

> [!IMPORTANT]
> Az _alapértelmezett Storage-fiókot_ a virtuális hálózatban lévő Azure Machine learning vagy _nem alapértelmezett tárolási fiókok_ számára is elhelyezheti.
>
> A munkaterület létrehozásakor a rendszer automatikusan kiépíti az alapértelmezett Storage-fiókot.
>
> A nem alapértelmezett tárolási fiókok esetében a `storage_account` [ `Workspace.create()` függvény](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) paramétere lehetővé teszi egyéni Storage-fiók megadását az Azure erőforrás-azonosító alapján.

Ha egy virtuális hálózatban lévő munkaterülethez Azure Storage-fiókot szeretne használni, kövesse az alábbi lépéseket:

1. A Azure Portal lépjen a munkaterületen használni kívánt tárolási szolgáltatáshoz.

   [![Az Azure Machine Learning munkaterülethez csatolt tárterület](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. A Storage-szolgáltatásfiók lapon válassza a __tűzfalak és virtuális hálózatok__lehetőséget.

   ![A Azure Portal Azure Storage lapjának "tűzfalak és virtuális hálózatok" területén](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. A __tűzfalak és virtuális hálózatok__ oldalon hajtsa végre a következő műveleteket:
    1. Válassza a __Kijelölt hálózatok__ lehetőséget.
    1. A __virtuális hálózatok__területen válassza a __meglévő virtuális hálózati kapcsolat hozzáadása__ elemet. Ez a művelet hozzáadja azt a virtuális hálózatot, ahol a számítás található (lásd: 1. lépés).

        > [!IMPORTANT]
        > A Storage-fióknak ugyanabban a virtuális hálózatban és alhálózatban kell lennie, mint a képzéshez vagy következtetéshez használt számítási példányok vagy fürtök.

    1. Jelölje be a __megbízható Microsoft-szolgáltatások számára a Storage-fiók elérésének engedélyezése__ jelölőnégyzetet.

    > [!IMPORTANT]
    > Ha a Azure Machine Learning SDK-val dolgozik, a fejlesztési környezetnek képesnek kell lennie csatlakozni az Azure Storage-fiókhoz. Ha a Storage-fiók egy virtuális hálózaton belül van, a tűzfalnak engedélyeznie kell a hozzáférést a fejlesztői környezet IP-címéről.
    >
    > A Storage-fiókhoz való hozzáférés engedélyezéséhez keresse fel a Storage-fiókhoz tartozó __tűzfalakat és virtuális hálózatokat__ a *fejlesztői ügyfél webböngészőjéből*. Ezután használja az __ügyfél IP-címének hozzáadása__ jelölőnégyzetet az ügyfél IP-címének a __címtartományból__való hozzáadásához. A __címtartomány__ mező használatával manuálisan is megadhatja a fejlesztési környezet IP-címét. Miután hozzáadta az ügyfél IP-címét, az SDK-val elérheti a Storage-fiókot.

   [![A Azure Portal tűzfalak és virtuális hálózatok panelje](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-datastores-and-datasets"></a>Biztonságos adattárolók és adatkészletek

Ebből a szakaszból megtudhatja, hogyan használhatja az adattárt és az adatkészletet a virtuális hálózat SDK-felületének használatával. A Studio-felülettel kapcsolatos további információkért lásd: [Azure Machine learning Studio használata virtuális hálózaton](how-to-enable-studio-virtual-network.md).

Ha az SDK-val szeretné elérni az adatelérést, az egyes szolgáltatásokhoz szükséges hitelesítési módszert kell használnia, amelyet az adatai tárolnak. Ha például regisztrál egy adattárt Azure Data Lake Store Gen2 eléréséhez, akkor továbbra is az [Azure Storage-szolgáltatásokhoz való kapcsolódáshoz](how-to-access-data.md#azure-data-lake-storage-generation-2)szükséges egyszerű szolgáltatásnevet kell használnia.

### <a name="disable-data-validation"></a>Adatérvényesítés letiltása

Alapértelmezés szerint a Azure Machine Learning az adatok érvényességét és a hitelesítő adatokat ellenőrzi, amikor az SDK használatával próbál hozzáférni az adatokhoz. Ha az adatközpont egy virtuális hálózat mögött található, Azure Machine Learning nem tudja befejezni ezeket az ellenőrzéseket. Ennek elkerüléséhez létre kell hoznia az érvényesítést kihagyó adattárolókat és adatkészleteket.

### <a name="use-datastores"></a>Adattárolók használata

 Azure Data Lake Store Gen1 és Azure Data Lake Store a Gen2 alapértelmezés szerint kihagyja az érvényesítést, így nincs szükség további műveletre. A következő szolgáltatások esetében azonban hasonló szintaxist használhat az adattár-érvényesítés kihagyása érdekében:

- Azure Blob Storage
- Azure-fájlmegosztás
- PostgreSQL
- Azure SQL Database

A következő mintakód egy új Azure Blob-adattárat és-készletet hoz létre `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Adatkészletek használata

Az adatkészlet-ellenőrzés kihagyásának szintaxisa hasonló a következő adatkészletek típusaihoz:
- Tagolt fájl
- JSON 
- Parquet
- SQL
- Fájl

A következő kód egy új JSON-adatkészletet és-készletet hoz létre `validate=False` .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Biztonságos Azure Key Vault

Azure Machine Learning egy társított Key Vault-példányt használ a következő hitelesítő adatok tárolásához:
* A társított Storage-fiókhoz tartozó kapcsolatok karakterlánca
* Az Azure Container repository példányainak jelszavai
* Adattárakhoz való kapcsolódási karakterláncok

Ha Azure Machine Learning kísérletezési képességeket szeretne használni a virtuális hálózat mögötti Azure Key Vault, kövesse az alábbi lépéseket:

1. Lépjen a munkaterülethez társított Key Vault.

1. A __Key Vault__ oldalon, a bal oldali ablaktáblán válassza a __hálózatkezelés__lehetőséget.

1. A __tűzfalak és virtuális hálózatok__ lapon végezze el a következő műveleteket:
    1. __A hozzáférés engedélyezése lehetőségnél__válassza a __privát végpont és a kiválasztott hálózatok__elemet.
    1. A __virtuális hálózatok__területen válassza a __meglévő virtuális hálózatok hozzáadása__ lehetőséget annak a virtuális hálózatnak a hozzáadásához, ahol a kísérletezési számítás található.
    1. A __megbízható Microsoft-szolgáltatások a tűzfal megkerülésének engedélyezése__területen válassza az __Igen__lehetőséget.

   [![A Key Vault panel "tűzfalak és virtuális hálózatok" szakasza](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Azure Container Registry engedélyezése (ACR)

Ha Azure Container Registryt szeretne használni egy virtuális hálózaton belül, meg kell felelnie a következő követelményeknek:

* A Azure Machine Learning munkaterületének Enterprise Edition rendszernek kell lennie. A frissítéssel kapcsolatos további információkért lásd: [frissítés a Enterprise Edition](how-to-manage-workspace.md#upgrade)verzióra.

* A Azure Container Registrynak prémium verziójúnak kell lennie. További információ a frissítésről: a [SKU módosítása](/azure/container-registry/container-registry-skus#changing-skus).

* A Azure Container Registrynak ugyanabban a virtuális hálózatban és alhálózatban kell lennie, mint a betanításhoz vagy következtetéshez használt Storage-fióknak és számítási céloknak.

* A Azure Machine Learning-munkaterületnek tartalmaznia kell egy [Azure Machine learning számítási fürtöt](how-to-create-attach-compute-sdk.md#amlcompute).

    Ha az ACR egy virtuális hálózat mögött van, Azure Machine Learning nem tud közvetlenül Docker-lemezképeket felépíteni. Ehelyett a rendszer a számítási fürtöt használja a rendszerképek létrehozásához.

A követelmények teljesítése után a következő lépésekkel engedélyezheti a Azure Container Registry.

1. Keresse meg a munkaterülethez tartozó Azure Container Registry nevét az alábbi módszerek egyikének használatával:

    __Azure Portal__

    A munkaterület Áttekintés szakaszában a __beállításjegyzék__ értéke a Azure Container Registryra hivatkozik.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry a munkaterülethez" border="true":::

    __Azure CLI__

    Ha [telepítette az Azure CLI Machine learning bővítményét](reference-azure-machine-learning-cli.md), a `az ml workspace show` parancs használatával jelenítheti meg a munkaterület adatait.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    A parancs a következőhöz hasonló értéket ad vissza: `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . A karakterlánc utolsó része a munkaterület Azure Container Registry neve.

1. Korlátozza a virtuális hálózathoz való hozzáférést a [hálózati hozzáférés konfigurálása a beállításjegyzékben](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)című témakör lépéseit követve. A virtuális hálózat hozzáadásakor válassza ki a virtuális hálózatot és az alhálózatot a Azure Machine Learning erőforrásaihoz.

1. A Azure Machine Learning Python SDK használatával konfigurálhat egy számítási fürtöt a Docker-rendszerképek létrehozásához. A következő kódrészlet bemutatja, hogyan teheti meg ezt:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    ```

    > [!IMPORTANT]
    > A Storage-fióknak, a számítási fürtnek és a Azure Container Registrynek a virtuális hálózat azonos alhálózatán kell lennie.
    
    További információkért tekintse meg a [frissítési ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) metódus-referenciát.

1. Alkalmazza a következő Azure Resource Manager sablont. Ez a sablon lehetővé teszi, hogy a munkaterület kommunikáljon az ACR-szel.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "keyVaultArmId": {
        "type": "string"
        },
        "workspaceName": {
        "type": "string"
        },
        "containerRegistryArmId": {
        "type": "string"
        },
        "applicationInsightsArmId": {
        "type": "string"
        },
        "storageAccountArmId": {
        "type": "string"
        },
        "location": {
        "type": "string"
        }
    },
    "resources": [
        {
        "type": "Microsoft.MachineLearningServices/workspaces",
        "apiVersion": "2019-11-01",
        "name": "[parameters('workspaceName')]",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "sku": {
            "tier": "enterprise",
            "name": "enterprise"
        },
        "properties": {
            "sharedPrivateLinkResources":
    [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
            "keyVault": "[parameters('keyVaultArmId')]",
            "containerRegistry": "[parameters('containerRegistryArmId')]",
            "applicationInsights": "[parameters('applicationInsightsArmId')]",
            "storageAccount": "[parameters('storageAccountArmId')]"
        }
        }
    ]
    }
    ```

## <a name="next-steps"></a>Következő lépések

Ez a cikk egy négy részből álló virtuális hálózati sorozat első része. A virtuális hálózatok biztonságossá tételéhez tekintse meg a cikkek további részeit:

* [1. rész: a Virtual Network áttekintése](how-to-network-security-overview.md)
* [3. rész: a képzési környezet biztonságossá tétele](how-to-secure-training-vnet.md)
* [4. rész: a következtetési környezet biztonságossá tétele](how-to-secure-inferencing-vnet.md)
* [5. rész: a Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)