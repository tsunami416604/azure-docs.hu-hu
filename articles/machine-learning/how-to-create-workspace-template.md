---
title: Munkaterület létrehozása az Azure Resource Manager sablonnal
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre egy új Azure Machine Learning-munkaterületet egy Azure Resource Manager-sablon használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: 9403cc05ed5b31f3b76c16c4232506e2ddc5da2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402907"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Az Azure Resource Manager-sablon használata munkaterület létrehozásához az Azure Machine Learning számára

Ebben a cikkben az Azure Resource Manager-sablonok használatával számos lehetőséget olvashat az Azure Machine Learning-munkaterület létrehozásához. Az Erőforrás-kezelő sablonokkal egyszerűen hozhat létre erőforrásokat egyetlen, összehangolt műveletként. A sablon egy JSON-dokumentum, amely meghatározza a központi telepítéshez szükséges erőforrásokat. Telepítési paramétereket is megadhat. A paraméterek a sablon használatakor a bemeneti értékek megadására szolgálnak.

További információ: [Alkalmazás telepítése az Azure Resource Manager sablonnal](../azure-resource-manager/templates/deploy-powershell.md)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. Ha nem rendelkezik ilyen, próbálja ki az [Azure Machine Learning ingyenes vagy fizetős verzióját.](https://aka.ms/AMLFree)

* A CLI-ből származó sablon használatához az [Azure PowerShellre](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) vagy az [Azure CLI-re](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)van szükség.

## <a name="resource-manager-template"></a>Resource Manager-sablon

A következő Resource Manager-sablon használható az Azure Machine Learning-munkaterület és a kapcsolódó Azure-erőforrások létrehozásához:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Ez a sablon a következő Azure-szolgáltatásokat hozza létre:

* Azure erőforráscsoport
* Azure Storage-tárfiók neve
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-munkaterület

Az erőforráscsoport a szolgáltatásokat tároló tároló. A különböző szolgáltatásokra az Azure Machine Learning-munkaterület szükséges.

A példasablon nak két paramétere van:

* Az a **hely,** ahol az erőforráscsoport és a szolgáltatások létrejönnek.

    A sablon a legtöbb erőforráshoz kiválasztott helyet fogja használni. A kivétel az Application Insights szolgáltatás, amely nem érhető el az összes helyen, amely a többi szolgáltatás. Ha olyan helyet választ, ahol az nem érhető el, a szolgáltatás az USA déli középső részén jön létre.

* A **munkaterület neve**, amely az Azure Machine Learning-munkaterület rövid neve.

    > [!NOTE]
    > A munkaterület neve nem i.

    A többi szolgáltatás nevét véletlenszerűen hozza létre a rendszer.

> [!TIP]
> Míg a dokumentumhoz társított sablon létrehoz egy új Azure Container Registry, is létrehozhat egy új munkaterületet anélkül, hogy egy tároló beállításjegyzék létrehozása. A rendszer akkor jön létre, ha olyan műveletet hajt végre, amelyhez tárolóbeállításjegyzékszükséges. Például egy modell betanítása vagy üzembe helyezése.
>
> Egy meglévő tároló beállításjegyzék- vagy tárfiók az Azure Resource Manager sablonban is hivatkozhat, ahelyett, hogy újat hozna létre.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

A sablonokról további információt az alábbi cikkekben talál:

* [Az Azure Resource Manager sablonjainak szerzője](../azure-resource-manager/templates/template-syntax.md)
* [Alkalmazás üzembe helyezése Azure Resource Manager-sablonokkal](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Speciális sablon

A következő példasablon bemutatja, hogyan hozhat létre munkaterületet három beállítással:

* A munkaterület magas bizalmassági beállításainak engedélyezése
* Titkosítás engedélyezése a munkaterületen
* Meglévő Azure Key Vault ot használ

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "southcentralus",
        "southeastasia",
        "westcentralus",
        "westeurope",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred to as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "hbi_workspace":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with the customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault Resource Manager ID."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault key uri."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2018-02-14",
      "name": "[variables('keyVaultName')]",
      "location": "[parameters('location')]",
      "properties": {
        "tenantId": "[variables('tenantId')]",
        "sku": {
          "name": "standard",
          "family": "A"
        },
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2015-05-01",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2017-10-01",
      "name": "[variables('containerRegistryName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "adminUserEnabled": true
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]",
        "[resourceId('Microsoft.ContainerRegistry/registries', variables('containerRegistryName'))]"
      ],
      "identity": {
        "type": "systemAssigned"
      },
      "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
      },
      "properties": {
        "friendlyName": "[parameters('workspaceName')]",
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbi_workspace": "[parameters('hbi_workspace')]"
      }
    }
  ]
}
```

A Key Vault azonosítójának és a sablon hoz szükséges kulcs URI-jának lekért, használhatja az Azure CLI-t. A következő parancs egy példa az Azure CLI használatával a Key Vault erőforrás-azonosító és URI bekéselésére:

```azurecli-interactive
az keyvault show --name mykeyvault --resource-group myresourcegroup --query "[id, properties.vaultUri]"
```

Ez a parancs a következő szöveghez hasonló értéket ad vissza. Az első érték az azonosító, a második pedig az URI:

```text
[
  "/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault",
  "https://mykeyvault.vault.azure.net/"
]
```

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Kövesse az [Erőforrások telepítése egyéni sablonból](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)című részében leírt lépéseket. Amikor megérkezik a __Sablon szerkesztése__ képernyőre, illessze be a sablont ebből a dokumentumból.
1. A sablon használatához válassza a __Mentés__ gombot. Adja meg a következő információkat, és fogadja el a felsorolt feltételeket:

   * Előfizetés: Válassza ki az Azure-előfizetést, amelyet ezekhez az erőforrásokhoz szeretne használni.
   * Erőforráscsoport: Jelöljön ki vagy hozzon létre egy erőforráscsoportot a szolgáltatások tárolására.
   * Munkaterület neve: Az Azure Machine Learning-munkaterület létrehozandó neve. A munkaterület nevének 3 és 33 karakter között kell lennie. Csak alfanumerikus karaktereket és '-'-t tartalmazhat.
   * Hely: Válassza ki azt a helyet, ahol az erőforrások létrejönnek.

További információt az [Erőforrások telepítése egyéni sablonból című](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)témakörben talál.

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

Ez a példa feltételezi, hogy a sablont az aktuális könyvtárban megnevezett `azuredeploy.json` fájlba mentette:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

További információ: [Erőforrások üzembe helyezése Erőforrás-kezelő sablonokkal és Az Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) és [a Private Resource Manager-sablon SAS-jogkivonattal és Az Azure PowerShell használatával](../azure-resource-manager/templates/secure-template-with-sas-token.md)című témakörben olvashat.

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Ez a példa feltételezi, hogy a sablont az aktuális könyvtárban megnevezett `azuredeploy.json` fájlba mentette:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

További információ: [Erőforrások üzembe helyezése Erőforrás-kezelő sablonokkal és Az Azure CLI](../azure-resource-manager/templates/deploy-cli.md) és [a Private Resource Manager sablon telepítése SAS-jogkivonattal és Az Azure CLI szolgáltatással](../azure-resource-manager/templates/secure-template-with-sas-token.md)című témakörben olvashat.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Az Azure Key Vault hozzáférési szabályzata és az Azure Resource Manager-sablonok

Ha egy Azure Resource Manager-sablon használatával hozza létre a munkaterületet és a kapcsolódó erőforrásokat (beleértve az Azure Key Vaultot is), többször is. Például a sablon használata többször ugyanazt a paramétereket egy folyamatos integrációs és üzembe helyezési folyamat részeként.

A sablonokon keresztüli legtöbb erőforrás-létrehozási művelet idempotens, de a Key Vault törli a hozzáférési szabályzatokat a sablon minden egyes használatakor. A hozzáférési házirendek törlése megszakítja a hozzáférést a Key Vault minden olyan meglévő munkaterületet, amely használja. Például az Azure Notebooks vm funkcióinak leállítása/létrehozása sikertelen lehet.  

A probléma elkerülése érdekében az alábbi módszerek egyikét javasoljuk:

* Ne telepítse a sablont egynél többször ugyanarra a paraméterre. Vagy törölje a meglévő erőforrásokat, mielőtt a sablonhasználatával újra létre.

* Vizsgálja meg a Key Vault hozzáférési szabályzatokat, majd használja ezeket a házirendeket a `accessPolicies` sablon tulajdonságának beállításához. A hozzáférési szabályzatok megtekintéséhez használja a következő Azure CLI parancsot:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    A sablon szakaszának `accessPolicies` használatáról további információt az [AccessPolicyEntry objektum hivatkozásában talál.](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry)

* Ellenőrizze, hogy a Key Vault erőforrás létezik-e már. Ha igen, ne hozza létre újra a sablonon keresztül. Ha például a meglévő Key Vaultot szeretné használni egy új létrehozása helyett, hajtsa végre a következő módosításokat a sablonon:

    * **Adjon hozzá** egy paramétert, amely elfogadja egy meglévő Key Vault-erőforrás azonosítóját:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Távolítsa el** a Key Vault-erőforrást létrehozó szakaszt:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Távolítsa** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` el a `dependsOn` sort a munkaterület szakaszából. **Szintén** Módosítsa `keyVault` a `properties` munkaterület szakaszának bejegyzését, `keyVaultId` hogy a paraméterre hivatkozzon:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    A módosítások után megadhatja a meglévő Key Vault-erőforrás azonosítóját a sablon futtatásakor. A sablon ezután újra felhasználja `keyVault` a Key Vaultot a munkaterület tulajdonságának azonosítóra állításával.

    A Key Vault azonosítójának lekért, hivatkozhat az eredeti sablon kimenetére, vagy használja az Azure CLI.To get the ID of the Key Vault, you can reference the output of the original template run or use the Azure CLI. A következő parancs egy példa az Azure CLI segítségével a Key Vault erőforrás-azonosító leéséhez:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Ez a parancs a következő höz hasonló értéket ad vissza:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>További lépések

* [Erőforrások üzembe helyezése az Erőforrás-kezelő sablonjaiés az Erőforrás-kezelő REST API segítségével.](../azure-resource-manager/templates/deploy-rest.md)
* [Azure-erőforráscsoportok létrehozása és üzembe helyezése a Visual Studióban keresztül.](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
