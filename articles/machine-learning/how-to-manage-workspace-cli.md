---
title: Munkaterületek létrehozása az Azure CLI-vel
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre új Azure Machine Learning-munkaterületet az Azure CLI használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 64c2e77ffc43ec98c13ce8c0cad002cdb2053241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296906"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Munkaterület létrehozása az Azure Machine Learning számára az Azure CLI-vel
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan hozhat létre egy Azure Machine Learning-munkaterületet az Azure CLI használatával. Az Azure CLI parancsokat biztosít az Azure-erőforrások kezeléséhez. A CLI gépi tanulási bővítménye parancsokat biztosít az Azure Machine Learning-erőforrások kal való munkához.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. Ha nem rendelkezik ilyen, próbálja ki az [Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

* A **helyi környezetből**származó CLI-parancsok használatához a helyi környezetből kell használnia a [CLI parancsot.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

    Ha az [Azure Cloud Shell,](https://azure.microsoft.com//features/cloud-shell/)a CLI érhető el a böngészőn keresztül, és él a felhőben.

## <a name="connect-the-cli-to-your-azure-subscription"></a>A CLI csatlakoztatása Azure-előfizetéséhez

> [!IMPORTANT]
> Ha az Azure Cloud Shell, kihagyhatja ezt a szakaszt. A felhőrendszerhéj automatikusan hitelesíti Önt az Azure-előfizetésébe bejelentkező fiók használatával.

Számos módon hitelesítheti magát az Azure-előfizetésa a CLI-ből. A legalapvetőbb az, hogy interaktívan hitelesíti a böngésző segítségével. Az interaktív hitelesítéshez nyisson meg egy parancssort vagy terminált, és használja a következő parancsot:

```azurecli-interactive
az login
```

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőt, és követnie kell a parancssorban található utasításokat. Az utasítások az [https://aka.ms/devicelogin](https://aka.ms/devicelogin) engedélyezési kód böngészését és megadását foglalják magukban.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

A hitelesítés egyéb módszereiről a [Bejelentkezés az Azure CLI-vel (Bejelentkezés az Azure CLI-vel) témakörben](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)található.

## <a name="install-the-machine-learning-extension"></a>A gépi tanulási bővítmény telepítése

A gépi tanulási bővítmény telepítéséhez használja a következő parancsot:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning-munkaterület a következő Azure-szolgáltatásokra vagy entitásokra támaszkodik:

> [!IMPORTANT]
> Ha nem ad meg egy meglévő Azure-szolgáltatást, a munkaterület létrehozása során automatikusan létrejön egy. Mindig meg kell adnia egy erőforráscsoportot.

| Szolgáltatás | Meglévő példány megadásának paramétere |
| ---- | ---- |
| **Azure-erőforráscsoport** | `-g <resource-group-name>`
| **Azure storage-fiók** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure container-beállításjegyzék** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure Machine Learning-munkaterületet egy erőforráscsoporton belül kell létrehozni. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy újat. __Új erőforráscsoport létrehozásához__használja a következő parancsot. Cserélje `<resource-group-name>` le az erőforráscsoporthoz használandó névvel. Cserélje `<location>` le az erőforráscsoporthoz használandó Azure-régióra:

> [!TIP]
> Ki kell választania egy régiót, ahol az Azure Machine Learning elérhető. További információt a [Régiónként elérhető Termékek](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)című témakörben talál.

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A parancs válasza hasonló a következő JSON-hoz:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Az erőforráscsoportokkal való munkáról az [az csoport című](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)témakörben talál további információt.

### <a name="automatically-create-required-resources"></a>A szükséges erőforrások automatikus létrehozása

Ha olyan új munkaterületet szeretne létrehozni, ahol a __szolgáltatások automatikusan létrejönnek,__ használja a következő parancsot:

> [!TIP]
> Az ebben a szakaszban található parancsok egy alapszintű kiadású munkaterületet hoznak létre. Vállalati munkaterület létrehozásához használja `--sku enterprise` a kapcsolót a `az ml workspace create` paranccsal. Az Azure Machine Learning-kiadásokról a [Mi az Azure Machine Learning](overview-what-is-azure-ml.md#sku)című témakörben talál további információt.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> A munkaterület neve nem i.

A parancs kimenete hasonló a következő JSON-hoz:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="use-existing-resources"></a>Meglévő erőforrások használata

Meglévő erőforrásokat használó munkaterület létrehozásához meg kell adnia az erőforrások azonosítóját. A következő parancsokkal szerezheti be a szolgáltatások azonosítójait:

> [!IMPORTANT]
> Nem kell megadnia az összes meglévő erőforrást. Megadhat egy vagy több. Például megadhat egy meglévő tárfiókot, és a munkaterület létrehozza a többi erőforrást.

+ **Azure storage-fiók:**`az storage account show --name <storage-account-name> --query "id"`

    A parancs válasza hasonló a következő szöveghez, és a tárfiók azonosítója:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure-alkalmazáselemzési adatok:**

    1. Telepítse az alkalmazáselemzési bővítményt:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Az alkalmazásbetekintési szolgáltatás azonosítójának beszerezni:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        A parancs válasza hasonló a következő szöveghez, és az alkalmazáselemzési szolgáltatás azonosítója:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault:**`az keyvault show --name <key-vault-name> --query "ID"`

    A parancs válasza hasonló a következő szöveghez, és a kulcstartó azonosítója:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container-beállításjegyzék:**`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    A parancs válasza hasonló a következő szöveghez, és a tárolóbeállításjegyzék azonosítója:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > A tároló beállításjegyzékben engedélyezni kell a [rendszergazdai fiók](/azure/container-registry/container-registry-authentication#admin-account) engedélyezve van, mielőtt egy Azure Machine Learning-munkaterülettel használható lenne.

Miután rendelkezik a munkaterülettel használni kívánt erőforrás(ok) azonosítóival, használja `az workspace create -w <workspace-name> -g <resource-group-name>` az alapparancsot, és adja hozzá a meglévő erőforrások paramétereit és azonosítóit. A következő parancs például létrehoz egy munkaterületet, amely egy meglévő tárolóbeállítás-jegyzéket használ:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

A parancs kimenete hasonló a következő JSON-hoz:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Munkaterületek listázása

Az Azure-előfizetés összes munkaterületének listázásához használja a következő parancsot:

```azurecli-interactive
az ml workspace list
```

A parancs kimenete hasonló a következő JSON-hoz:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

További információt az [az ml munkaterületi lista dokumentációjában](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) talál.

## <a name="get-workspace-information"></a>Munkaterületi információk beszerezni

Munkaterületről a következő paranccsal kaphat információt:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

A parancs kimenete hasonló a következő JSON-hoz:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

További információt az [az ml munkaterület bemutató](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) dokumentációjában talál.

## <a name="update-a-workspace"></a>Munkaterület frissítése

Munkaterület frissítéséhez használja a következő parancsot:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

A parancs kimenete hasonló a következő JSON-hoz:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

További információt az [az ml munkaterület frissítési](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) dokumentációjában talál.

## <a name="share-a-workspace-with-another-user"></a>Munkaterület megosztása másik felhasználóval

Ha egy munkaterületet meg szeretne osztani egy másik felhasználóval az előfizetésében, használja a következő parancsot:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

A szerepköralapú hozzáférés-vezérlésről (RBAC) az Azure Machine Learningszolgáltatással kapcsolatos további információkért [lásd: Felhasználók és szerepkörök kezelése.](how-to-assign-roles.md)

További információt az [az ml munkaterületmegosztási](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) dokumentációban talál.

## <a name="sync-keys-for-dependent-resources"></a>Függő erőforrások kulcsainak szinkronizálása

Ha módosítja a munkaterület által használt erőforrások egyikének hozzáférési kulcsait, az alábbi paranccsal szinkronizálhatja az új kulcsokat a munkaterülettel:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

A kulcsok módosításáról a [Tároló-hozzáférési kulcsok újragenerálása](how-to-change-storage-access-key.md)című témakörben talál további információt.

További információt az [az ml munkaterület szinkronizálási kulcsok](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) dokumentációjában talál.

## <a name="delete-a-workspace"></a>Munkaterület törlése

Ha a munkaterületet a következő parancs alkalmazásával szeretné törölni, miután már nincs rá szükség, használja a következő parancsot:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> A munkaterület törlése nem törli a munkaterület által használt alkalmazásbetekintést, tárfiókot, kulcstartót vagy tárolóbeállításjegyzéket.

Törölheti az erőforráscsoportot is, amely törli a munkaterületet és az összes többi Azure-erőforrást az erőforráscsoportban. Az erőforráscsoport törléséhez használja a következő parancsot:

```azurecli-interactive
az group delete -g <resource-group-name>
```

További információt az [az ml munkaterület törlési](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) dokumentációjában talál.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>A munkaterület áthelyezése

> [!WARNING]
> Az Azure Machine Learning-munkaterület áthelyezése egy másik előfizetésre, vagy a tulajdonában lévő előfizetés áthelyezése egy új bérlőre, nem támogatott. Ez hibákat okozhat.

### <a name="deleting-the-azure-container-registry"></a>Az Azure Container-beállításjegyzék törlése

Az Azure Machine Learning-munkaterület bizonyos műveletekhez az Azure Container Registry (ACR) szolgáltatást használja. Automatikusan létrehoz egy ACR-példányt, amikor először szüksége van rá.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>További lépések

Az Azure CLI-bővítmény gépi tanuláshoz további információkért tekintse meg az [az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) dokumentációt.
