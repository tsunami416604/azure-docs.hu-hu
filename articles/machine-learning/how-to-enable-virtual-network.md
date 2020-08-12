---
title: Hálózati elkülönítés & adatvédelem
titleSuffix: Azure Machine Learning
description: Egy elszigetelt Azure-Virtual Network Azure Machine Learning használatával biztosíthatja a kísérletezést/képzést, valamint a következtetési/pontozási feladatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: 58f0a25ebeb7fb715cfba27d7482a031d1fe8c32
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121204"
---
# <a name="network-isolation-during-training-and-inference-with-private-endpoints-and-virtual-networks"></a>Hálózati elkülönítés a betanítás során, valamint a magánhálózati végpontokkal és a virtuális hálózatokkal kapcsolatos következtetések
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan védheti meg a gépi tanulási életciklusait Azure Machine Learning képzések és a feladatok Azure-Virtual Network (vnet) belüli elkülönítésével. A __virtuális hálózat__ biztonsági határként működik, és az Azure-erőforrásokat a nyilvános internetről különíti el. Egy Azure-beli virtuális hálózatot is csatlakoztathat a helyszíni hálózathoz. A hálózatok összekapcsolásával biztonságosan betaníthatja a modelleket, és elérheti az üzembe helyezett modelleket a következtetésekhez.

Azure Machine Learning munkaterület egy __privát végpont__használatával érhető el egy virtuális hálózatból. A privát végpont a virtuális hálózaton belüli magánhálózati IP-címek készlete, a munkaterülethez való hozzáférés pedig a virtuális hálózatra korlátozódik. A privát végpont segít csökkenteni az adatmennyiség kiszűrése kockázatát. Ha többet szeretne megtudni a privát végpontokról, tekintse meg az [Azure privát hivatkozását](/azure/private-link/private-link-overview) ismertető cikket.

> [!NOTE]
> A Mozilla Firefox használatakor problémák merülhetnek fel a munkaterületek privát végponton keresztüli elérésekor. Előfordulhat, hogy a probléma a DNS HTTPS-kapcsolaton keresztüli beállításával kapcsolatos a böngészőben. A probléma megkerüléséhez a Microsoft Edge vagy a Google Chrome használatát javasoljuk.

A Azure Machine Learning más Azure-szolgáltatásokra támaszkodik az adattárolási és számítási erőforrások (modellek betanítása és üzembe helyezése) esetén. Ezek az erőforrások a virtuális hálózaton belül is létrehozhatók. Használhatja például Azure Machine Learning számítást a modell betanításához, majd a modell üzembe helyezéséhez az Azure Kubernetes szolgáltatásban (ak). 

## <a name="prerequisites"></a>Előfeltételek

+ Egy Azure Machine Learning [munkaterület](how-to-manage-workspace.md).

+ Az [Azure Virtual Network szolgáltatás](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) és az [IP-hálózatkezelés](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)általános munkaismerete.

+ Meglévő virtuális hálózat és alhálózat a számítási erőforrásokkal való használathoz.

+ Ha erőforrásokat szeretne telepíteni egy virtuális hálózatba vagy alhálózatba, a felhasználói fióknak rendelkeznie kell a következő műveletekhez szükséges engedélyekkel az Azure szerepköralapú hozzáférés-vezérlés (RBAC) szolgáltatásban:

    - "Microsoft. Network/virtualNetworks/JOIN/Action" a virtuális hálózati erőforráson.
    - "Microsoft. Network/virtualNetworks/subnet/JOIN/Action" az alhálózati erőforráson.

    A hálózatkezeléssel való RBAC kapcsolatos további információkért tekintse meg a [hálózatkezelés beépített szerepkörei](/azure/role-based-access-control/built-in-roles#networking) című témakört.

## <a name="secure-your-workspace"></a>Munkaterület biztonságossá tétele

A Azure Machine Learning-munkaterület rendelkezhet __nyilvános végponttal__ vagy __privát végponttal__is. A nyilvános végpontok olyan IP-címek összessége, amelyek elérhetők a nyilvános interneten, míg a privát végpontok magánhálózati IP-címek készletét jelentik a virtuális hálózaton belül. 

Ha korlátozni szeretné a munkaterület hozzáférését úgy, hogy csak a magánhálózati IP-címeken keresztül történjen, használjon privát végpontot.

Mivel a munkaterület felé irányuló kommunikáció csak a virtuális hálózatról engedélyezett, a munkaterületet használó fejlesztési környezeteknek a virtuális hálózat tagjának kell lenniük. Például egy virtuális gép a virtuális hálózaton.

> [!IMPORTANT]
> A magánhálózati végpont nem befolyásolja az Azure Control Plant (felügyeleti műveletek), például a munkaterület törlését vagy a számítási erőforrások kezelését. Például létrehozhatja, frissítheti vagy törölheti a számítási célt. Ezeket a műveleteket a rendszer a szokásos módon, a nyilvános interneten hajtja végre.
>
> A magánhálózati végpont megakadályozza a munkaterülethez való hozzáférést a virtuális hálózaton kívülről.

Egy privát végponttal rendelkező erőforrások egyes kombinációi nagyvállalati kiadási munkaterületet igényelnek. A következő táblázat segítségével megismerheti, hogy milyen forgatókönyvek szükségesek a vállalati kiadáshoz:

| Forgatókönyv | Enterprise</br>Edition | Alapszintű</br>Edition |
| ----- |:-----:|:-----:| 
| Nincs virtuális hálózat vagy magánhálózati végpont | ✔ | ✔ |
| Privát végpont nélküli munkaterület. Egyéb erőforrások (a Azure Container Registry kivételével) egy virtuális hálózaton | ✔ | ✔ |
| Privát végpont nélküli munkaterület. Egyéb erőforrások privát végponttal | ✔ | |
| Munkaterület privát végponttal. Egyéb erőforrások (a Azure Container Registry kivételével) egy virtuális hálózaton | ✔ | ✔ |
| Munkaterület és bármely más, privát végponttal rendelkező erőforrás | ✔ | |
| Munkaterület privát végponttal. Egyéb források magánhálózati végpont vagy virtuális hálózat nélkül | ✔ | ✔ |
| Azure Container Registry virtuális hálózaton | ✔ | |
| Ügyfél által felügyelt kulcsok a munkaterülethez | ✔ | |

> [!WARNING]
> 
> Azure Machine Learning számítási példányok nem támogatottak olyan munkaterületeken, amelyeken engedélyezve van a magánhálózati végpont.
>
> A Azure Machine Learning nem támogatja olyan Azure Kubernetes-szolgáltatás használatát, amelyen engedélyezve van a magánhálózati végpont. Ehelyett használhatja az Azure Kubernetes szolgáltatást egy virtuális hálózaton. További információkért lásd: [Azure-beli Virtual Network biztonságossá tétele és következtetések](how-to-enable-virtual-network.md)elvégzése az Azure-on belül.

Ha többet szeretne megtudni az Azure-beli privát végpontokról, tekintse meg az [Azure privát hivatkozását](/azure/private-link/private-link-overview) ismertető cikket.

### <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Privát végpontot használó munkaterület létrehozása

A Azure Machine Learning SDK, a CLI, a Azure Resource Manager sablon vagy a Azure Portal használatával létrehozhat egy új munkaterületet egy privát végponttal.

__Követelmények__

* A magánhálózati végponttal használt virtuális hálózatnak le kell tiltania a hálózati házirendeket. További információ: [hálózati házirendek letiltása privát végpontok számára](/azure/private-link/disable-private-endpoint-network-policy).

__Korlátozások__

* Nem tud csatlakozni a munkaterülethez a nyilvános interneten keresztül, csak a virtuális hálózaton belülről.

* Ha több munkaterület jön létre privát végpontok használatával, és ugyanazt a magánhálózati DNS-zónát használják, a rendszer csak az első munkaterületet adja hozzá a magánhálózati DNS-zóna __virtuális hálózati kapcsolataihoz__ .

    A korlátozás megkerüléséhez manuálisan adja hozzá a virtuális hálózati kapcsolatot a további munkaterületekhez. További információ: [Mi az a virtuális hálózati kapcsolat](/azure/dns/private-dns-virtual-network-links).

__Konfigurálás__

További információ a munkaterület virtuális hálózat és privát végpont használatával történő létrehozásáról, valamint egyéb konfigurációs lehetőségekről:

* [Azure Resource Manager sablonnal létrehozhat egy munkaterületet Azure Machine Learninghoz](how-to-create-workspace-template.md).
* [Munkaterületek létrehozása a portálon](how-to-manage-workspace.md).
* [Munkaterületek létrehozása az Azure CLI-vel](how-to-manage-workspace-cli.md).
* A Python SDK használatához tekintse meg a [PrivateEndPointConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.private_endpoint.privateendpointconfig?view=azure-ml-py) és a [munkaterület. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) dokumentációját.

<a id="amlcompute"></a>

## <a name="machine-learning-studio"></a>Machine Learning Studio

__Követelmények__

* A Storage-fiókban lévő adateléréshez a Storage-fióknak ugyanabban a virtuális hálózatban kell lennie, mint a munkaterületnek.

* Ha az adatok virtuális hálózaton vannak tárolva, egy munkaterület által [felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md) kell használnia ahhoz, hogy a stúdió hozzáférjen az adatokhoz.

    > [!IMPORTANT]
    > Habár a legtöbb Studio a virtuális hálózaton tárolt adattal működik, az integrált jegyzetfüzetek __nem__. Az integrált jegyzetfüzetek nem támogatják a virtuális hálózatban lévő tárolók használatát. Ehelyett Jupyter jegyzetfüzeteket használhat egy számítási példányból. További információ: [számítási fürtök & példányok](#compute-instance) szakaszban.

    Ha nem ad meg Studio-hozzáférést, akkor `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` a következő műveletek nem lesznek elérhetők:

    * A Studióban tárolt előzetes verzió.
    * Jelenítse meg a tervezőben tárolt adatmegjelenítést.
    * AutoML-kísérlet küldése.
    * Címkéző projekt elindítása.

__Korlátozások__

* Azure Machine Learning Studio támogatja az adatok olvasását a virtuális hálózat következő adattár-típusaiból:

    * Azure-blob
    * 1. generációs Azure Data Lake Storage
    * 2. generációs Azure Data Lake Storage
    * Azure SQL Database

__Konfigurálás__

* Az adattárolók __konfigurálása felügyelt identitás használatára__ az adatok eléréséhez. Ezek a lépések hozzáadja a munkaterület felügyelt identitását __olvasóként__ a Storage szolgáltatáshoz az Azure erőforrás-alapú hozzáférés-vezérlés (RBAC) használatával. Az __olvasói__ hozzáférés lehetővé teszi, hogy a munkaterület beolvassa a tűzfal beállításait, és gondoskodjon arról, hogy a virtuális hálózat ne maradjon meg

    1. A Studióban __válassza az__adattárolók lehetőséget.

    1. Új adattár létrehozásához válassza az __+ új adattár__lehetőséget. Egy meglévő frissítéséhez válassza ki az adattárt, és válassza a __hitelesítő adatok frissítése__lehetőséget.

    1. Az adattár beállításainál válassza az __Igen__ lehetőséget a __Azure Machine learning szolgáltatás számára a munkaterület által felügyelt identitás használatával való elérésének engedélyezéséhez__.

    > [!NOTE]
    > A módosítások érvénybe léptetése akár 10 percet is igénybe vehet.

* Az __Azure Blob Storage__esetében a munkaterület által felügyelt identitást is hozzá kell adni [blob-adatolvasóként](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , hogy az adatok beolvashatók legyenek a blob Storage-ból.

* A tervező a munkaterülethez csatolt Storage-fiókot használja a kimenet alapértelmezett tárolásához. Megadhatja azonban azt is, hogy a kimenetet minden olyan adattárhoz tárolja, amelyhez hozzáfér. Ha a környezete virtuális hálózatokat használ, ezekkel a vezérlőelemekkel biztosíthatja, hogy az adatai biztonságosak és elérhetők maradjanak. Új alapértelmezett tároló beállítása egy folyamathoz:

    1. A folyamat piszkozata lapon válassza a **Beállítások fogaskerék ikont** a folyamat címe közelében.
    1. Válassza az **alapértelmezett adattár kiválasztása** bejegyzést.
    1. Új adattárt kell megadnia.

    Az alapértelmezett adattárat is felülbírálhatja egy modul alapján. Ez lehetővé teszi az egyes modulok tárolási helyének szabályozását.

    1. Válassza ki azt a modult, amelynek kimenetét meg kívánja adni.
    1. Bontsa ki a **kimeneti beállítások** szakaszt.
    1. Válassza az **alapértelmezett kimeneti beállítások felülbírálása**lehetőséget.
    1. Válassza a **kimeneti beállítások megadása**lehetőséget.
    1. Új adattárt kell megadnia.

* __Azure Data Lake Storage Gen2__használata esetén a RBAC és a POSIX stílusú hozzáférés-vezérlési listákat (ACL-eket) is használhatja a virtuális hálózaton belüli adatelérés szabályozására.

    A RBAC használatához adja hozzá a munkaterület felügyelt identitását a [blob-Adatolvasó](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) szerepkörhöz. További információ: [szerepköralapú hozzáférés-vezérlés](../storage/blobs/data-lake-storage-access-control.md#role-based-access-control).

    Az ACL-ek használatához a munkaterület által felügyelt identitás ugyanúgy rendelhető hozzá, mint bármely más biztonsági elv. További információ: hozzáférés- [vezérlési listák a fájlokon és könyvtárakon](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

* __Azure Data Lake Storage Gen1__ csak a POSIX stílusú hozzáférés-vezérlési listát támogatja. A munkaterület felügyelt identitásokhoz való hozzáférését ugyanúgy rendelheti hozzá az erőforrásokhoz, mint bármely más biztonsági elv. További információ: [hozzáférés-vezérlés Azure Data Lake Storage Gen1ban](../data-lake-store/data-lake-store-access-control.md).

* A felügyelt identitással __Azure SQL Database__ tárolt adatok eléréséhez létre kell hoznia egy olyan SQL-beli felhasználót, amely a felügyelt identitásra van leképezve. Ha további információt szeretne arról, hogyan hozhat létre egy felhasználót egy külső szolgáltatótól, tekintse meg az [Azure ad-identitásokhoz leképezett felhasználók létrehozása](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)című témakört.

    Miután létrehozta az SQL-T tartalmazó felhasználót, adja meg az engedélyt a [T-SQL parancs](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql)használatával.

* Ha egy virtuális hálózaton (például egy számítási példányon vagy virtuális gépen) __belül található erőforráshoz fér hozzá a studióhoz__ , engedélyeznie kell a kimenő forgalmat a virtuális hálózatról a studióhoz. 

    Ha például hálózati biztonsági csoportokat (NSG) használ a kimenő forgalom korlátozására, adjon hozzá egy szabályt a __AzureFrontDoor. frontend__nevű __szolgáltatási címkéhez__ .

## <a name="use-datastores-and-datasets"></a>Adattárolók és adatkészletek használata

> [!NOTE]
> Ez a szakasz az SDK-élmény adattár-és adatkészlet-használatát ismerteti. A Studióval kapcsolatos további információkért tekintse meg a [Machine learning Studio](#machine-learning-studio)című szakaszt.

__Korlátozások__

Alapértelmezés szerint a Azure Machine Learning az adatok érvényességét és a hitelesítő adatokat ellenőrzi, amikor az SDK használatával próbál hozzáférni az adatokhoz. Ha az adatai egy virtuális hálózat mögött vannak, Azure Machine Learning nem fér hozzá az adathoz, és sikertelen lesz az ellenőrzése. A probléma megkerüléséhez az adattárolók és adatkészletek létrehozásakor hagyja ki az érvényesítést.

* __Adattár__használatakor:

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

* __Adatkészlet__használatakor:

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

## <a name="azure-storage-account"></a>Azure Storage-fiók

> [!IMPORTANT]
> A virtuális hálózatban a Azure Machine Learning és a _nem alapértelmezett Storage-fiókok_ esetében is elhelyezheti az _alapértelmezett Storage-fiókot_ .

__Követelmények__

* A Storage-fióknak ugyanabban a virtuális hálózatban és alhálózatban kell lennie, mint a képzéshez vagy következtetéshez használt számítási példányok vagy fürtök.

__Konfigurálás__

A munkaterület által használt Azure Storage-fiók védelméhez engedélyezzen egy __privát végpontot__ vagy egy __szolgáltatási végpontot__ a virtuális hálózaton lévő Storage-fiókhoz.

* Ha a Storage-fiókot __privát végpont__használatára szeretné konfigurálni, tekintse meg a [privát végpontok használata](/azure/storage/common/storage-private-endpoints.md) című cikket.

* Ha a Storage-fiókot __szolgáltatási végpont__használatára szeretné konfigurálni, kövesse az alábbi lépéseket:

    1. Ha hozzá szeretné adni a Storage-fiókot a munkaterület által használt virtuális hálózathoz, használja az [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network) című cikk __virtuális hálózatról való hozzáférésének engedélyezése__ című szakaszának információit.
    1. Ha engedélyezni szeretné a Microsoft szolgáltatásainak elérését a virtuális hálózaton (például Azure Machine Learning), használja az [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](/azure/storage/common/storage-network-security#exceptions) című cikk __kivételek__ szakaszában található információkat.
    1. Ha a Azure Machine Learning SDK-val dolgozik, a fejlesztési környezetnek képesnek kell lennie csatlakozni az Azure Storage-fiókhoz. Ha a Storage-fiók egy virtuális hálózaton belül van, a tűzfalnak engedélyeznie kell a hozzáférést a fejlesztői környezet IP-címéről. A fejlesztési környezet IP-címének hozzáadásához használja az [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range) című cikk az __Internet IP-címtartomány megadása__ című szakaszában található információkat.

## <a name="azure-container-registry"></a>Azure Container Registry

__Követelmények__

* A Azure Machine Learning munkaterületének Enterprise Edition rendszernek kell lennie. A frissítéssel kapcsolatos további információkért lásd: [frissítés a Enterprise Edition](how-to-manage-workspace.md#upgrade)verzióra.
* A Azure Machine Learning munkaterület-régiónak [privát kapcsolaton keresztül engedélyezett régiónak](https://docs.microsoft.com/azure/private-link/private-link-overview#availability)kell lennie. 
* A Azure Container Registrynak prémium verziójúnak kell lennie. További információ a frissítésről: a [SKU módosítása](/azure/container-registry/container-registry-skus#changing-skus).
* A Azure Container Registrynak ugyanabban a virtuális hálózatban és alhálózatban kell lennie, mint a betanításhoz vagy következtetéshez használt Storage-fióknak és számítási céloknak.
* A Azure Machine Learning-munkaterületnek tartalmaznia kell egy [Azure Machine learning számítási fürtöt](how-to-set-up-training-targets.md#amlcompute).

__Korlátozások__

* Ha az ACR egy virtuális hálózat mögött van, Azure Machine Learning nem tud közvetlenül Docker-lemezképeket felépíteni. Ehelyett a rendszer a számítási fürtöt használja a rendszerképek létrehozásához.

__Konfigurálás__

1. A munkaterület Azure Container Registry nevének megkereséséhez használja az alábbi módszerek egyikét:

    __Azure Portal__

    A munkaterület Áttekintés szakaszában a __beállításjegyzék__ értéke a Azure Container Registryra hivatkozik.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry a munkaterülethez" border="true":::

    __Azure CLI__

    Ha [telepítette az Azure CLI Machine learning bővítményét](reference-azure-machine-learning-cli.md), a `az ml workspace show` parancs használatával jelenítheti meg a munkaterület adatait.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    A parancs a következőhöz hasonló értéket ad vissza: `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . A karakterlánc utolsó része a munkaterület Azure Container Registry neve.

1. A virtuális hálózathoz való hozzáférés korlátozásához kövesse a [hálózati hozzáférés konfigurálása a beállításjegyzékhez](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)című témakör lépéseit. A virtuális hálózat hozzáadásakor válassza ki a virtuális hálózatot és az alhálózatot a Azure Machine Learning erőforrásaihoz.

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

1. Ha engedélyezni szeretné, hogy a munkaterület kommunikáljon az ACR-példánnyal, alkalmazza a következő Azure Resource Manager sablont:

    > [!WARNING]
    > Ez a sablon lehetővé teszi a munkaterület privát végpontjának használatát, és egy vállalati munkaterületre módosítja azt. Ezek a módosítások nem vonhatók vissza.

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

## <a name="key-vault-instance"></a>Key Vault-példány 

__Követelmények__

__Korlátozások__

__Konfigurálás__ 

Ha Azure Machine Learning kísérletezési képességeket szeretne használni a virtuális hálózat mögötti Azure Key Vault, használja a [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](/azure/key-vault/general/network-security) című cikket.

> [!IMPORTANT]
> A cikk lépéseit követve használja ugyanazt a virtuális hálózatot, mint amelyet a kísérletezési számítási erőforrások használnak. Engedélyeznie kell __a megbízható Microsoft-szolgáltatások számára a tűzfal megkerülését__is.

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Számítási fürtök & példányok 

__Követelmények__

* A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint a Azure Machine Learning munkaterületnek.
* A számítási példányhoz vagy fürthöz megadott alhálózatnak elegendő, nem hozzárendelt IP-címmel kell rendelkeznie a célként kijelölt virtuális gépek számának kielégítéséhez. Ha az alhálózat nem rendelkezik elegendő nem hozzárendelt IP-címmel, a számítási fürt részlegesen le lesz foglalva.
* Ha a virtuális hálózat védelmét úgy tervezi, hogy korlátozza a forgalmat, néhány portot nyitva kell hagyni a számítási szolgáltatás számára.
* Ha egy virtuális hálózatban több számítási példányt vagy fürtöt szeretne létrehozni, előfordulhat, hogy egy vagy több erőforrásra vonatkozó kvóta-növelést kell kérnie.
* Ha a munkaterülethez tartozó Azure Storage-fiók (ok) is védett virtuális hálózatban, akkor a Azure Machine Learning számítási példánnyal vagy fürttel azonos virtuális hálózatban kell lenniük. 
* A számítási példányok Jupyter működéséhez győződjön meg arról, hogy a webes szoftvercsatorna-kommunikáció nincs letiltva.

__Korlátozások__

* A Machine Learning számítási példány vagy fürt automatikusan további hálózati erőforrásokat foglal le __a virtuális hálózatot tartalmazó erőforráscsoporthoz__. A szolgáltatás minden számítási példányhoz vagy fürthöz a következő erőforrásokat foglalja le:

    * Egy hálózati biztonsági csoport
    * Egy nyilvános IP-cím
    * Egy Load Balancer
    
    A __számítási fürtök__esetében ezeket az erőforrásokat a rendszer minden alkalommal törli (és újból létrehozza), amikor a fürt 0 csomópontra méretezi a méretezést.
    
    __Számítási példány__ esetében az erőforrások a példány törlését követően lesznek tárolva (a Leállítás nem távolítja el az erőforrásokat).

    Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) vonatkoznak.

__Konfigurálás__

* Machine Learning Compute-fürt létrehozásához kövesse az alábbi lépéseket:

    1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/), majd válassza ki az előfizetést és a munkaterületet.
    1. Válassza a bal oldali __számítás__ lehetőséget.
    1. Válassza ki a __betanítási fürtök__ lehetőséget a középpontban, majd válassza a elemet __+__ .
    1. Az __új betanítási fürt__ párbeszédpanelen bontsa ki a __Speciális beállítások__ szakaszt.
    1. Ha ezt a számítási erőforrást virtuális hálózat használatára szeretné konfigurálni, hajtsa végre a következő műveleteket a __virtuális hálózat konfigurálása__ szakaszban:

        1. Az __erőforráscsoport__ legördülő listában válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.
        1. A __virtuális hálózat__ legördülő listában válassza ki azt a virtuális hálózatot, amely az alhálózatot tartalmazza.
        1. Az __alhálózat__ legördülő listában válassza ki a használni kívánt alhálózatot.

    ![Machine Learning Compute virtuális hálózati beállításai](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

    Machine Learning Compute-fürtöt a Azure Machine Learning SDK használatával is létrehozhat. A következő kód egy új Machine Learning Compute fürtöt hoz létre `default` egy nevű virtuális hálózat alhálózatában `mynetwork` :

    ```python
    from azureml.core.compute import ComputeTarget, AmlCompute
    from azureml.core.compute_target import ComputeTargetException

    # The Azure virtual network name, subnet, and resource group
    vnet_name = 'mynetwork'
    subnet_name = 'default'
    vnet_resourcegroup_name = 'mygroup'

    # Choose a name for your CPU cluster
    cpu_cluster_name = "cpucluster"

    # Verify that cluster does not exist already
    try:
        cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
        print("Found existing cpucluster")
    except ComputeTargetException:
        print("Creating new cpucluster")

        # Specify the configuration for the new cluster
        compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            min_nodes=0,
                                                            max_nodes=4,
                                                            vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                            vnet_name=vnet_name,
                                                            subnet_name=subnet_name)

        # Create the cluster with the specified name and configuration
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

        # Wait for the cluster to be completed, show the output log
        cpu_cluster.wait_for_completion(show_output=True)
    ```

    A létrehozási folyamat befejeződése után a modellt egy kísérletben a fürt használatával kell betanítani. További információkért lásd: [számítási cél kiválasztása és használata képzéshez](how-to-set-up-training-targets.md).

    [!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

* Ha egy Azure számítási példányon jegyzetfüzeteket használ, gondoskodnia kell arról, hogy a jegyzetfüzet az adatokkal azonos virtuális hálózat és alhálózat mögötti számítási erőforráson fusson. 

    Konfigurálja úgy a számítási példányt, hogy ugyanabban a virtuális hálózatban legyen a létrehozás során a **Speciális beállítások**  >  **konfigurálása virtuális hálózat beállítása**alatt. Meglévő számítási példányt nem adhat hozzá virtuális hálózathoz.

* Ha a virtuális hálózat védelmét úgy tervezi, hogy korlátozza a nyilvános internetre irányuló hálózati forgalmat, engedélyeznie kell a bejövő kommunikációt a Azure Batch szolgáltatástól.

    A Batch szolgáltatás hálózati biztonsági csoportokat (NSG) helyez üzembe a virtuális gépekhez csatolt hálózati adapterek (NIC-EK) szintjén. Ezek az NSG-k automatikusan konfigurálnak bejövő és kimenő szabályokat a következő forgalom engedélyezéséhez:

    - Bejövő TCP-forgalom a 29876-es és a 29877-es portokon a __BatchNodeManagement__ __szolgáltatási címkéjén__ .

    - Választható Bejövő TCP-forgalom a 22-es porton a távoli hozzáférés engedélyezéséhez. Csak akkor használja ezt a portot, ha az SSH használatával szeretne csatlakozni a nyilvános IP-címen.

    - Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz.

    - Kimenő forgalom bármilyen porton keresztül az internetre.

    - A számítási példány bejövő TCP-forgalma a 44224-as porton a __AzureMachineLearning__ __szolgáltatási címkéjén__ .

    > [!IMPORTANT]
    > Körültekintően járjon el a bejövő vagy kimenő szabályok módosításakor és hozzáadásakor a Batch által konfigurált NSG-kben. Ha egy NSG blokkolja a számítási csomópontok felé irányuló kommunikációt, a számítási szolgáltatás nem használhatóra állítja a számítási csomópontok állapotát.
    >
    > Nem kell megadnia a NSG az alhálózat szintjén, mert a Azure Batch szolgáltatás konfigurálja a saját NSG. Ha azonban a Azure Machine Learning számítási feltételt tartalmazó alhálózat NSG vagy tűzfallal rendelkezik, akkor a korábban felsorolt forgalmat is engedélyeznie kell.

* Ha nem szeretné az alapértelmezett kimenő szabályokat használni, és korlátozni szeretné a virtuális hálózat kimenő hozzáférését, kövesse az alábbi lépéseket:

    1. A kimenő internetkapcsolat megtagadása a NSG szabályok használatával.
    1. __Számítási példány__ vagy __számítási fürt__esetén korlátozza a kimenő forgalmat a következő elemekre:
        - Azure Storage a __Storage. RegionName__ __szolgáltatási címkéjével__ . Ahol az `{RegionName}` egy Azure-régió neve.
        - Azure Container Registry a __AzureContainerRegistry. RegionName__ __szolgáltatási címkéje__ segítségével. Ahol az `{RegionName}` egy Azure-régió neve.
        - Azure Machine Learning a __AzureMachineLearning__ __szolgáltatási címkéjének__ használatával
        - Azure Resource Manager a __AzureResourceManager__ __szolgáltatási címkéjének__ használatával
        - Azure Active Directory a __AzureActiveDirectory__ __szolgáltatási címkéjének__ használatával

    > [!NOTE]
    > Ha azt tervezi, hogy a Microsoft által biztosított alapértelmezett Docker-rendszerképeket használja, és engedélyezi a felhasználó által felügyelt függőségeket, akkor a következő __szolgáltatási címkéket__is használnia kell:
    >
    > * __MicrosoftContainerRegistry__
    > * __AzureFrontDoor.FirstParty__
    >
    > Erre a konfigurációra akkor van szükség, ha az alábbi kódrészletekhez hasonló kóddal rendelkezik a betanítási szkriptek részeként:
    >
    > __RunConfig-képzés__
    > ```python
    > # create a new runconfig object
    > run_config = RunConfiguration()
    > 
    > # configure Docker 
    > run_config.environment.docker.enabled = True
    > # For GPU, use DEFAULT_GPU_IMAGE
    > run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
    > run_config.environment.python.user_managed_dependencies = True
    > ```
    >
    > __Kalkulátor-képzés__
    > ```python
    > est = Estimator(source_directory='.',
    >                 script_params=script_params,
    >                 compute_target='local',
    >                 entry_script='dummy_train.py',
    >                 user_managed=True)
    > run = exp.submit(est)
    > ```

* Ha [kényszerített bújtatást](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm) használ Azure Machine learning számítási feladatokkal, akkor engedélyeznie kell a kommunikációt a nyilvános internettel a számítási erőforrást tartalmazó alhálózatból. Ez a kommunikáció a feladatütemezés és az Azure Storage elérésére szolgál.

    Ez kétféleképpen valósítható meg:

    * [Virtual Network NAT](../virtual-network/nat-overview.md)használata. A NAT-átjáró kimenő internetkapcsolatot biztosít a virtuális hálózat egy vagy több alhálózatához. További információ: [virtuális hálózatok tervezése NAT Gateway-erőforrásokkal](../virtual-network/nat-gateway-resource.md).

    * Adja hozzá a [felhasználó által megadott útvonalakat (UDR)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) a számítási erőforrást tartalmazó alhálózathoz. Hozzon létre egy UDR minden olyan IP-címhez, amelyet a Azure Batch szolgáltatás használ azon a régióban, ahol az erőforrásai léteznek. Ezek a UDR lehetővé teszik a Batch szolgáltatás számára a feladatütemezés számítási csomópontjaival való kommunikációját. Adja hozzá azt a Azure Machine Learning-szolgáltatáshoz tartozó IP-címet is, ahol az erőforrások léteznek, mivel ez szükséges a számítási példányokhoz való hozzáféréshez. A Batch szolgáltatás és a Azure Machine Learning szolgáltatás IP-címeinek listájának megjelenítéséhez használja a következő módszerek egyikét:

        * Töltse le az [Azure IP-címtartományok és a szolgáltatás címkéit](https://www.microsoft.com/download/details.aspx?id=56519) , és keresse meg a és a fájlt `BatchNodeManagement.<region>` `AzureMachineLearning.<region>` , ahol `<region>` az az Azure-régió.

        * Az adatok letöltéséhez használja az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -t. Az alábbi példa letölti az IP-cím adatait, és kiszűri az USA 2. keleti régiójának információit:

            ```azurecli-interactive
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
            az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
            ```
        
        A UDR hozzáadásakor adja meg az útvonalat az egyes kapcsolódó batch IP-címek előtagjaként, és állítsa be a __következő ugrás típusát__ az __Internet__értékre. 

        Az Ön által meghatározott UDR kívül az Azure Storage-ba irányuló kimenő forgalmat a helyszíni hálózati berendezésen keresztül kell engedélyezni. A forgalom URL-címei a következő formákban találhatók: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` és `<account>.blob.core.windows.net` . 

        További információ: [Azure batch készlet létrehozása egy virtuális hálózaton](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

__Követelmények__

* Az Azure Kubernetes szolgáltatás (ak) példányának és az Azure-beli virtuális hálózatnak ugyanabban a régióban kell lennie. Ha a virtuális hálózatban a munkaterület által használt Azure Storage-fiók (oka) t védi, azoknak ugyanabban a virtuális hálózatban kell lenniük, mint az AK-példány.

* A fürt __IP-címzésének megtervezéséhez__ kövesse a [speciális hálózatkezelés konfigurálása az Azure Kubernetes szolgáltatásban (ak)](https://docs.microsoft.com/azure/aks/configure-azure-cni#prerequisites) című cikkben ismertetett előfeltételeket.

* Ha korlátozni szeretné a bejövő és kimenő kommunikációt az AK-fürtre, kövesse az [Azure Kubernetes szolgáltatásban található fürtcsomópontok kimenő forgalmának szabályozása](/azure/aks/limit-egress-traffic) című témakör útmutatását, és győződjön meg arról, hogy az AK-ról érkező _kimenő_ kommunikáció megfelelően van konfigurálva. A _bejövő_ kommunikációs követelményeket, ha vannak ilyenek, az alábbi konfigurációs szakaszban hívhatók meg.

__Korlátozások__

* Ha olyan Azure Kubernetes szolgáltatást szeretne használni, amelyen engedélyezve van a privát kapcsolat, csatolja azt a munkaterülethez. Nem hozhat létre olyan Azure Kubernetes Service-fürtöt, amely Azure Machine Learning (SDK, portál, CLI stb.) származó privát hivatkozással rendelkezik.

__Konfigurálás__

> [!IMPORTANT]
> Ez a szakasz több konfigurációt tartalmaz. Válassza ki az igényeinek leginkább megfelelőt.

* A __virtuális hálózat mögötti, nyilvános terheléselosztó használatával__:

    1. Hozzon létre vagy csatolja az AK-fürtöt. Ha új fürtöt __hoz létre__ , meg kell adnia a fürthöz használni kívánt virtuális hálózatot.
    
        Az alábbi példa bemutatja, hogyan hozhat létre egy új AK-fürtöt a Python SDK használatával:

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        Ha olyan meglévő AK-fürttel rendelkezik, amely már a virtuális hálózat mögött van, használja az [üzembe helyezés az Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) című cikkben található információkat.

    1. Győződjön meg arról, hogy a virtuális hálózatot vezérlő hálózati biztonsági csoport rendelkezik egy __bejövő__ biztonsági szabállyal, amely engedélyezi a pontozási végpontot, hogy a virtuális hálózaton kívülről is meghívható legyen.

* Ha __a virtuális hálózat mögött található AK-t szeretné használni a saját Load balancerrel__:

    1. Hozzon létre vagy csatolja az AK-fürtöt. Ha új fürtöt __hoz létre__ , meg kell adnia a fürthöz használni kívánt virtuális hálózatot.
    
        Az alábbi példa bemutatja, hogyan hozhat létre egy új AK-fürtöt a Python SDK használatával:

        ```python
        from azureml.core.compute import ComputeTarget, AksCompute

        # Create the compute configuration and set virtual network information
        config = AksCompute.provisioning_configuration(location="eastus2")
        config.vnet_resourcegroup_name = "mygroup"
        config.vnet_name = "mynetwork"
        config.subnet_name = "default"
        config.service_cidr = "10.0.0.0/16"
        config.dns_service_ip = "10.0.0.10"
        config.docker_bridge_cidr = "172.17.0.1/16"

        # Create the compute target
        aks_target = ComputeTarget.create(workspace=ws,
                                        name="myaks",
                                        provisioning_configuration=config)
        ```

        Ha olyan meglévő AK-fürttel rendelkezik, amely már a virtuális hálózat mögött van, használja az [üzembe helyezés az Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) című cikkben található információkat.

    1. Az alábbi Azure CLI-parancsokkal keresheti meg az egyszerű szolgáltatásnév vagy a felügyelt identitás AZONOSÍTÓját. Cserélje le a `<aks-cluster-name>` nevet a fürt nevére. A helyére írja be az `<resource-group-name>` _AK-fürtöt tartalmazó_erőforráscsoport nevét:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
        ``` 

        Ha a parancs egy értéket ad vissza `msi` , használja a következő parancsot a felügyelt identitás résztvevő-azonosítójának azonosításához:

        ```azurecli-interactive
        az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
        ```
    1. A virtuális hálózatot tartalmazó erőforráscsoport AZONOSÍTÓjának megkereséséhez használja a következő parancsot. Cserélje le a `<resource-group-name>` nevet a _virtuális hálózatot tartalmazó_erőforráscsoport nevére:

        ```azurecli-interactive
        az group show -n <resource-group-name> --query id
        ```

    1. Ha a szolgáltatásnevet vagy a felügyelt identitást hálózati közreműködőként szeretné felvenni, használja a következő parancsot. Cserélje le az értéket az `<SP-or-managed-identity>` egyszerű szolgáltatásnév vagy a felügyelt identitás számára visszaadott azonosítóra. Cserélje le a értéket a `<resource-group-id>` virtuális hálózatot tartalmazó erőforráscsoport által visszaadott azonosítóra:

        ```azurecli-interactive
        az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
        ```

    1. Ha az AK-fürtöt __privát terheléselosztó__használatára szeretné frissíteni, használja a Python SDK-t. A következő kódrészlet azt mutatja be, hogyan lehet frissíteni egy meglévő AK-fürtöt, amely hozzá lett adva vagy csatolva van a munkaterülethez:
    
        ```python
        import azureml.core
        from azureml.core.compute.aks import AksUpdateConfiguration
        from azureml.core.compute import AksCompute

        # ws = workspace object. Creation not shown in this snippet
        aks_target = AksCompute(ws,"myaks")

        # Change to the name of the subnet that contains AKS
        subnet_name = "default"
        # Update AKS configuration to use an internal load balancer
        update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
        aks_target.update(update_config)
        # Wait for the operation to complete
        aks_target.wait_for_completion(show_output = True)
        ```

* __AK csatolása privát végpont használatával__:

    1. Az alábbi Azure CLI-paranccsal lekérheti annak az alhálózatnak az __alhálózati azonosítóját__ , amelyet az AK-fürt használni fog. Például a virtuális hálózat alapértelmezett alhálózata:
    
        ```azurecli
        az network vnet show -g myresourcegroup -n myvnet --query subnets[].id
        ```
        
        Ez a parancs a virtuális hálózat alhálózatai azonosítóinak tömbjét adja vissza. A következő JSON egy példa egy olyan virtuális hálózatra, amely csak egy alhálózattal rendelkezik:

        ```json
        [
            "/subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/default"
        ]
        ```

        Ha a rendszer több azonosítót ad vissza, válassza ki azt, amelyet használni szeretne.

    1. Ha egy AK-fürthöz privát végpontot szeretne létrehozni, használja a [privát Azure Kubernetes-fürt létrehozása](/azure/aks/private-clusters#advanced-networking) című cikk __speciális hálózatkezelés__ szakaszának információit. A fürt létrehozásakor használja az előző parancs alhálózati AZONOSÍTÓját a (z `--vnet-subnet-id` ) paraméterrel.

    1. A fürt csatolásához használja az [üzembe helyezés az Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md#attach-an-existing-aks-cluster) -ben című cikket.

    > [!TIP]
    > Ha ezt a konfigurációt használja, és a kimenő kommunikációt az AK-fürtből korlátozza, nem kell engedélyeznie a kommunikációt a __1194__ -es vagy a __9000__-es porton. Az egyéb engedélyezett portok esetében tekintse meg az [Azure Kubernetes Service-beli fürtcsomópontok kimenő forgalmának szabályozása](/azure/aks/limit-egress-traffic) című témakör útmutatását.

## <a name="use-azure-container-instances-aci"></a>Azure Container Instances használata (ACI)

__Követelmények__

* A Azure Container Instances a modell telepítésekor dinamikusan jönnek létre. Annak engedélyezéséhez, hogy a Azure Machine Learning az ACI-t a virtuális hálózaton belül hozza létre, engedélyeznie kell az alhálózati __delegálást__ az üzemelő példány által használt alhálózathoz.

__Korlátozások__

* A virtuális hálózatnak ugyanabban az erőforráscsoporthoz kell tartoznia, mint a Azure Machine Learning-munkaterületnek.

* A munkaterülethez tartozó Azure Container Registry (ACR) nem lehet a virtuális hálózatban is.

__Konfigurálás__

Ha egy virtuális hálózatban szeretné használni az ACI-t a munkaterületére, kövesse az alábbi lépéseket:

1. Ha engedélyezni szeretné az alhálózati delegálást a virtuális hálózaton, használja az [alhálózati delegálás hozzáadása vagy eltávolítása](../virtual-network/manage-subnet-delegation.md) című cikk információit. A delegálást engedélyezheti virtuális hálózat létrehozásakor, vagy hozzáadhatja egy meglévő hálózathoz.

    > [!IMPORTANT]
    > A delegálás engedélyezésekor használja `Microsoft.ContainerInstance/containerGroups` a __meghatalmazott alhálózatot a szolgáltatás__ értékéhez.

2. Telepítse a modellt a [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-)használatával, használja a `vnet_name` és a `subnet_name` paramétereket. Állítsa be ezeket a paramétereket a virtuális hálózat nevére és az alhálózatra, ahol engedélyezte a delegálást.

## <a name="azure-databricks"></a>Azure Databricks

__Követelmények__

* A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint a Azure Machine Learning munkaterületnek.
* Ha a munkaterülethez tartozó Azure Storage-fiók (ok) is védett virtuális hálózatban, akkor a Azure Databricks-fürttel megegyező virtuális hálózatban kell lenniük.
* Az Azure Databricks által használt __databricks-__ és __databricks-__ alhálózatok mellett a virtuális hálózathoz létrehozott __alapértelmezett__ alhálózat is szükséges.

__Korlátozások__

__Konfigurálás__

A Azure Databricks virtuális hálózattal való használatáról a [Azure Databricks üzembe helyezése az Azure-Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html)című témakörben olvashat bővebben.

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtuális gép vagy HDInsight-fürt

__Követelmények__

* A Azure Machine Learning csak az Ubuntut futtató virtuális gépeket támogatja.
* Az SSH-portot engedélyezni kell a virtuális gépen vagy a HDInsight-fürtön.

__Korlátozások__

__Konfigurálás__

1. Hozzon létre egy virtuális GÉPET vagy HDInsight-fürtöt a Azure Portal vagy az Azure CLI használatával, és helyezze üzembe a fürtöt egy Azure-beli virtuális hálózaton. További információkért tekintse át a következő cikkeket:

    * [Azure Virtual Networks létrehozása és kezelése Linux rendszerű virtuális gépekhez](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)

    * [HDInsight kiterjesztése Azure Virtual Network használatával](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network)

1. A Linux-alapú Azure-Virtual Machineshoz automatikusan létrejön egy NSG. Ez a NSG bármely forrásból engedélyezi a 22-es port elérését. Ha korlátozni szeretné az SSH-porthoz való hozzáférést, engedélyeznie kell a hozzáférést a Azure Machine Learning. Az Azure ML-hez való hozzáférés megőrzése érdekében engedélyeznie __kell a hozzáférést__ a __AzureMachineLearning__egy __forrásoldali szolgáltatási címkével__ . A következő Azure CLI-parancsok például úgy módosítják az SSH-szabályt, hogy csak a Azure Machine Learning elérését engedélyezzék.

    ```azurecli
    # Get default SSH rule
    nsgrule=$(az network nsg rule list --resource-group myResourceGroup --nsg-name myNetworkSecurityGroup --query [0].name -o tsv)
    # Update network security group rule to limit SSH to source service.
    az network nsg rule update --resource-group myResourceGroup --nsg-name myNetworkSecurityGroupBackEnd \
    --name $nsgrule --protocol tcp --direction inbound --priority 100 \
    --source-address-prefix AzureMachineLearning --source-port-range '*' --destination-address-prefix '*' \
    --destination-port-range 22 --access allow
    ```

    További információ: az Azure Virtual Networks for Linux Virtual Machines című cikk [hálózati biztonsági csoportok létrehozása](/azure/virtual-machines/linux/tutorial-virtual-network#create-network-security-groups) szakasza.
    
    Tartsa meg a hálózati biztonsági csoport alapértelmezett kimenő szabályait. További információ: [biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)alapértelmezett biztonsági szabályai.

1. Csatlakoztassa a virtuális gépet vagy a HDInsight-fürtöt a Azure Machine Learning munkaterülethez. További információ: [számítási célok beállítása a modell betanításához](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>További lépések

* [Azure Machine learning munkaterület használata Azure Firewall mögött](how-to-access-azureml-behind-firewall.md).
* [Betanítási környezetek beállítása](how-to-set-up-training-targets.md)
* [Privát végpontok beállítása](how-to-configure-private-link.md)
* [A modellek üzembe helyezési helyének kiválasztása](how-to-deploy-and-where.md)
* [Webszolgáltatás biztonságossá tétele a TLS használatával Azure Machine Learning](how-to-secure-web-service.md)
