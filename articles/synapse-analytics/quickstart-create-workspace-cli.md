---
title: 'Rövid útmutató: szinapszis-munkaterület létrehozása az Azure CLI használatával'
description: Hozzon létre egy Azure szinapszis-munkaterületet az Azure CLI használatával az útmutató lépéseit követve.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: 46ab1df1b776bf8dc9d6d917e5394c3efeec0de4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659386"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Rövid útmutató: Azure szinapszis-munkaterület létrehozása az Azure CLI-vel

Az Azure CLI az Azure parancssori felülete, amely Azure-erőforrások kezelésére szolgál. Használhatja a böngészőjében az Azure Cloud Shell-lel. Vagy telepítheti macOS, Linux és Windows rendszeren, és futtathatja a parancssorból.

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy szinapszis-munkaterületet az Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Töltse le és telepítse a [jQ](https://stedolan.github.io/jq/download/)-t, amely egy egyszerű és rugalmas parancssori JSON-processzor
- [Azure Data Lake Storage Gen2 Storage-fiók](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > Az Azure szinapszis-munkaterületnek képesnek kell lennie olvasni és írni a kiválasztott ADLS Gen2 fiókot. Továbbá minden olyan Storage-fiókhoz, amelyet elsődleges Storage-fiókként csatol, a Storage-fiók [létrehozásához](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account) engedélyezni kell a **hierarchikus névteret** a Storage-fiók létrehozása lapon leírtak szerint. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Azure-beli szinapszis-munkaterület létrehozása az Azure CLI használatával

1. Adja meg a szükséges környezeti változókat az Azure szinapszis-munkaterület erőforrásainak létrehozásához.

    | Környezeti változó neve | Leírás |
    |---|---|---|
    |StorageAccountName| A meglévő ADLS Gen2 Storage-fiók neve.|
    |StorageAccountResourceGroup| A meglévő ADLS Gen2 Storage-fiók erőforráscsoport neve. |
    |FileShareName| A meglévő Storage-fájlrendszer neve.|
    |SynapseResourceGroup| Válassza ki az Azure szinapszis-erőforráscsoport új nevét. |
    |Régió| Válasszon egy Azure- [régiót](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
    |SynapseWorkspaceName| Válasszon egyedi nevet az új Azure szinapszis-munkaterülethez. |
    |SqlUser| Válasszon egy értéket egy új felhasználónévhez.|
    |SqlPassword| Válasszon biztonságos jelszót.|
    |||

2. Erőforráscsoport létrehozása tárolóként az Azure szinapszis-munkaterülethez:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```
3. A ADLS Gen 2 Storage-fiók kulcsának beolvasása:
    ```azurecli
    StorageAccountKey=$(az storage account keys list \
      --account-name $StorageAccountName \
      | jq -r '.[0] | .value')
    ```
4. A ADLS Gen 2 tárolási végpont URL-címének beolvasása:
    ```azurecli
    StorageEndpointUrl=$(az storage account show \
      --name $StorageAccountName \
      --resource-group $StorageAccountResourceGroup \
      | jq -r '.primaryEndpoints | .dfs')
    ```

5. Választható A ADLS Gen2 Storage-fiók kulcsa és végpontja mindig ellenőrizhető:
    ```azurecli
    echo "Storage Account Key: $StorageAccountKey"
    echo "Storage Endpoint URL: $StorageEndpointUrl"
    ```

6. Azure szinapszis-munkaterület létrehozása:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

7. Webes és fejlesztői URL-cím beszerzése az Azure szinapszis-munkaterülethez:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

8. Hozzon létre egy tűzfalszabály, amely lehetővé teszi az Azure szinapszis-munkaterület elérését a gépről:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

9. Nyissa meg a munkaterület eléréséhez a környezeti változóban tárolt Azure szinapszis-munkaterület webes URL-címét `WorkspaceWeb` :

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [![Azure szinapszis-munkaterület webes ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) felülete](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure szinapszis-munkaterület törléséhez kövesse az alábbi lépéseket.
> [!WARNING]
> Az Azure szinapszis-munkaterület törlésével a rendszer eltávolítja az elemzési motorokat és a tárolt SQL-készletek és munkaterület-metaadatok adatbázisában tárolt adatokat. A továbbiakban nem lesz lehetséges az SQL-vagy Apache Spark-végpontokhoz való kapcsolódás. Minden kódrészlet törölve lesz (lekérdezések, jegyzetfüzetek, feladatdefiníciók és folyamatok).
>
> A munkaterület törlése **nem** befolyásolja a munkaterülethez csatolt Data Lake Store Gen2 lévő adatműveleteket.

Ha törölni szeretné az Azure szinapszis munkaterületet, hajtsa végre a következő parancsot:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ezután [LÉTREHOZHAT SQL-készleteket](quickstart-create-sql-pool-studio.md) , vagy [létrehozhat Apache Spark készleteket](quickstart-create-apache-spark-pool-studio.md) az adatok elemzésének és vizsgálatának megkezdéséhez.
