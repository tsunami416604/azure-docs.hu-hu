---
title: Munkaterület létrehozása Azure Resource Manager sablonnal
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre egy új Azure Machine Learning-munkaterületet egy Azure Resource Manager sablon használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: larryfr
author: Blackmist
ms.date: 05/19/2020
ms.custom: seoapril2019
ms.openlocfilehash: 5280fe3c4a06479ae49c5064832aca6affd8167f
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433505"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Munkaterületek létrehozása Azure Machine Learninghez Azure Resource Manager sablon használatával

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning munkaterületet Azure Resource Manager sablonok használatával. A Resource Manager-sablonok segítségével egyszerűen hozhat létre erőforrásokat egyetlen, koordinált műveletként. A sablon egy JSON-dokumentum, amely meghatározza a központi telepítéshez szükséges erőforrásokat. Emellett telepítési paramétereket is megadhat. A paraméterek a sablon használatakor a bemeneti értékek biztosítására szolgálnak.

További információ: [alkalmazások központi telepítése Azure Resource Manager sablonnal](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha még nem rendelkezik ilyennel, próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* Ha a parancssori felületről szeretne sablont használni, [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-t kell használnia.

## <a name="resource-manager-template"></a>Resource Manager-sablon

A következő Resource Manager-sablon használatával létrehozhat egy Azure Machine Learning munkaterületet és a hozzá tartozó Azure-erőforrásokat:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Ez a sablon a következő Azure-szolgáltatásokat hozza létre:

* Azure-erőforráscsoport
* Azure Storage-tárfiók neve
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-munkaterület

Az erőforráscsoport az a tároló, amely a szolgáltatásokat tárolja. A Azure Machine Learning munkaterület különböző szolgáltatásokat igényel.

A példában szereplő sablonnak két paramétere van:

* A **hely** , ahol az erőforráscsoport és a szolgáltatások létre lesznek hozva.

    A sablon a legtöbb erőforráshoz kiválasztott helyet fogja használni. A kivétel a Application Insights szolgáltatás, amely nem érhető el a többi szolgáltatás összes helyén. Ha olyan helyet választ, ahol nem érhető el, a szolgáltatás az USA déli középső régiójában lesz létrehozva.

* A **munkaterület neve**, amely a Azure Machine learning munkaterület rövid neve.

    > [!NOTE]
    > A munkaterület neve megkülönbözteti a kis-és nagybetűket.

    A többi szolgáltatás neve véletlenszerűen jön létre.

> [!TIP]
> Míg a dokumentumhoz társított sablon új Azure Container Registry hoz létre, a tároló-beállításjegyzék létrehozása nélkül is létrehozhat egy új munkaterületet. A rendszer létrehoz egy tároló-beállításjegyzéket igénylő művelet végrehajtásakor. Például egy modell betanítása vagy üzembe helyezése.
>
> Egy meglévő tároló beállításjegyzék-vagy Storage-fiókra is hivatkozhat a Azure Resource Manager sablonban, ahelyett, hogy újat hozna létre.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

A sablonokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Azure Resource Manager sablonok szerzője](../azure-resource-manager/templates/template-syntax.md)
* [Alkalmazás üzembe helyezése Azure Resource Manager-sablonokkal](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft. MachineLearningServices erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Speciális sablon

Az alábbi példa bemutatja, hogyan hozhat létre egy munkaterületet három beállítással:

* A munkaterület magas titoktartási beállításainak engedélyezése
* A munkaterület titkosításának engedélyezése
* Egy meglévő Azure Key Vault használ az ügyfél által felügyelt kulcsok lekéréséhez

További információ: [titkosítás a REST](concept-enterprise-security.md#encryption-at-rest)-ben.

> [!IMPORTANT]
> A sablon használata előtt néhány speciális követelménynek meg kell felelnie az előfizetésnek:
> * Az __Azure Machine learning__ alkalmazásnak az Azure-előfizetéséhez __közreműködőnek__ kell lennie.
> * A titkosítási kulcsot tartalmazó meglévő Azure Key Vaultnak kell lennie.
> * Olyan hozzáférési szabályzattal kell rendelkeznie a Azure Key Vaultban, amely a __Azure Cosmos db__ alkalmazáshoz való hozzáférés __megszerzését__, __becsomagolását__és __kicsomagolását__ engedélyezi.
> * A Azure Key Vaultnak ugyanabban a régióban kell lennie, ahol létre kívánja hozni a Azure Machine Learning munkaterületet.

__A Azure Machine learning alkalmazás közreműködőként való hozzáadásához__használja a következő parancsokat:

1. Ha a parancssori felületről szeretne hitelesítést végezni az Azure-ban, használja a következő parancsot:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [subscription-login](../../includes/machine-learning-cli-subscription.md)]

1. A Azure Machine Learning alkalmazás objektum-AZONOSÍTÓjának lekéréséhez használja a következő parancsot. Az egyes Azure-előfizetések esetében az érték különbözhet:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    Ez a parancs az objektumazonosító értéket adja vissza, amely egy GUID azonosító.

1. Az objektum-azonosító közreműködőként való hozzáadásához használja az alábbi parancsot. Cserélje le az `<object-ID>` elemet az előző lépésben szereplő GUID azonosítóra. A helyére írja `<subscription-ID>` be az Azure-előfizetés nevét vagy azonosítóját:

    ```azurecli-interactive
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

__Ha kulcsot szeretne hozzáadni a Azure Key Vaulthoz__, használja a [kulcs, titkos kód vagy tanúsítvány hozzáadása](../key-vault/general/manage-with-cli2.md#adding-a-key-secret-or-certificate-to-the-key-vault) az Azure CLI-vel című cikkben található Key Vault- __Key Vault kezelőhöz__ című témakör információit.

__Ha hozzáférési szabályzatot szeretne hozzáadni a Key vaulthoz, használja a következő parancsokat__:

1. A Azure Cosmos DB alkalmazás objektum-AZONOSÍTÓjának lekéréséhez használja a következő parancsot. Az egyes Azure-előfizetések esetében az érték különbözhet:

    ```azurecli-interactive
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```
    
    Ez a parancs az objektumazonosító értéket adja vissza, amely egy GUID azonosító.

1. A házirend beállításához használja a következő parancsot. Cserélje le a helyére a `<keyvault-name>` meglévő Azure Key Vault nevét. Cserélje le `<object-ID>` az elemet az előző lépésben szereplő GUID azonosítóra:

    ```azurecli-interactive
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

__To get the values__ A `cmk_keyvault` sablonhoz szükséges (Key Vault) és a `resource_cmk_uri` (kulcs URI) paraméterek értékeinek lekéréséhez kövesse az alábbi lépéseket:

1. A Key Vault-azonosító beszerzéséhez használja a következő parancsot:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
    ```

    A parancs a következőhöz hasonló értéket ad vissza: `/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault` .

1. Az ügyfél által felügyelt kulcs URI azonosítójának megszerzéséhez használja a következő parancsot:

    ```azurecli-interactive
    az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
    ```

    A parancs a következőhöz hasonló értéket ad vissza: `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` .

__Példa sablonra__

:::code language="json" source="~/quickstart-templates/201-machine-learning-encrypted-workspace/azuredeploy.json":::

> [!IMPORTANT]
> Miután létrehozta a munkaterületet, nem módosíthatja a bizalmas adatok, a titkosítás, a kulcstároló-azonosító vagy a kulcs-azonosítók beállításait. Az értékek módosításához új munkaterületet kell létrehoznia az új értékekkel.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Kövesse az [erőforrások telepítése egyéni sablonból](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)című témakör lépéseit. Amikor megérkezik a __Sablon szerkesztése__ képernyőre, illessze be a sablont a dokumentumból.
1. A sablon használatához válassza a __Mentés__ lehetőséget. Adja meg a következő információkat, és fogadja el a felsorolt feltételeket és kikötéseket:

   * Előfizetés: válassza ki az erőforrásokhoz használni kívánt Azure-előfizetést.
   * Erőforráscsoport: válasszon ki vagy hozzon létre egy erőforráscsoportot, amely tartalmazza a szolgáltatásokat.
   * Munkaterület neve: a létrehozandó Azure Machine Learning munkaterület nevét fogja használni. A munkaterület nevének 3 és 33 karakter közöttinek kell lennie. Csak alfanumerikus karaktereket és "-" karaktert tartalmazhat.
   * Hely: válassza ki azt a helyet, ahová létre kívánja hozni az erőforrásokat.

További információ: [erőforrások központi telepítése egyéni sablonból](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

Ez a példa feltételezi, hogy mentette a sablont egy nevű fájlba `azuredeploy.json` az aktuális könyvtárban:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) , valamint [saját Resource Manager-sablon üzembe helyezése sas-jogkivonat és Azure PowerShell segítségével](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

Ez a példa feltételezi, hogy mentette a sablont egy nevű fájlba `azuredeploy.json` az aktuális könyvtárban:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../azure-resource-manager/templates/deploy-cli.md) -vel, valamint [saját Resource Manager-sablon üzembe helyezése sas-JOGKIVONAT és Azure CLI használatával](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault hozzáférési szabályzat és Azure Resource Manager sablonok

Ha Azure Resource Manager sablonnal hozza létre a munkaterületet és a hozzá tartozó erőforrásokat (beleértve a Azure Key Vault), többször is. Például a sablon többszöri használata ugyanazzal a paraméterekkel, mint a folyamatos integráció és üzembe helyezési folyamat részeként.

A sablonokon keresztül a legtöbb erőforrás-létrehozási művelet idempotens, de Key Vault törli a hozzáférési házirendeket a sablon használatakor. A hozzáférési házirendek törlése megszakítja a hozzáférést a Key Vault az azt használó meglévő munkaterületekhez. Előfordulhat például, hogy a Azure Notebooks virtuális gép működőképességének leállítása/létrehozása sikertelen.  

A probléma elkerüléséhez a következő módszerek egyikét javasoljuk:

* A sablont ne telepítse többször ugyanarra a paraméterekre. Vagy törölje a meglévő erőforrásokat, mielőtt a sablon használatával újra létrehozza őket.

* Vizsgálja meg a Key Vault hozzáférési házirendeket, majd használja ezeket a házirendeket a `accessPolicies` sablon tulajdonságának beállításához. A hozzáférési szabályzatok megtekintéséhez használja az alábbi Azure CLI-parancsot:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    A sablon szakaszának használatával kapcsolatos további információkért `accessPolicies` tekintse meg a [AccessPolicyEntry objektum hivatkozását](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Ellenőrizze, hogy a Key Vault erőforrás már létezik-e. Ha igen, ne hozza létre újra a sablonon keresztül. Ha például a meglévő Key Vault szeretné használni, ahelyett, hogy újat hozna létre, végezze el a következő módosításokat a sablonon:

    * **Adjon hozzá** egy olyan paramétert, amely egy meglévő Key Vault erőforrás azonosítóját fogadja el:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Távolítsa el** a Key Vault erőforrást létrehozó szakaszt:

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

    * **Távolítsa el** a `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` sort a `dependsOn` munkaterület szakaszából. **Módosítsa** a `keyVault` munkaterület szakaszának bejegyzését is a `properties` paraméterre való hivatkozáshoz `keyVaultId` :

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

    A módosítások után megadhatja a meglévő Key Vault erőforrás AZONOSÍTÓját a sablon futtatásakor. A sablon ezután újra felhasználja a Key Vault a `keyVault` munkaterület tulajdonságának az azonosítóra való beállításával.

    A Key Vault AZONOSÍTÓjának lekéréséhez hivatkozhat az eredeti sablon kimenetére, vagy használhatja az Azure CLI-t is. Az alábbi parancs egy példa arra, hogyan használhatja az Azure CLI-t a Key Vault erőforrás-azonosító lekéréséhez:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Ez a parancs az alábbi szöveghez hasonló értéket ad vissza:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Következő lépések

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és Resource Manager-Rest APIokkal](../azure-resource-manager/templates/deploy-rest.md).
* [Azure-erőforráscsoportok létrehozása és üzembe helyezése a Visual Studióval](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
