---
title: Erőforrások üzembe helyezése erőforráscsoportok számára
description: Ismerteti, hogyan lehet erőforrásokat telepíteni egy Azure Resource Manager sablonban. Bemutatja, hogyan célozhat meg egynél több erőforráscsoportot.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: fd211641d7fcc02a1db154053597497583b21ae5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681541"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Erőforráscsoportok üzembe helyezése ARM-sablonokkal

Ez a cikk azt ismerteti, hogyan lehet az üzembe helyezést egy erőforráscsoporthoz kiterjeszteni. A központi telepítéshez egy Azure Resource Manager sablont (ARM-sablont) használ. A cikk azt is bemutatja, hogyan lehet kibontani a hatókört a telepítési műveletben lévő erőforráscsoporthoz képest.

## <a name="supported-resources"></a>Támogatott erőforrások

A legtöbb erőforrás üzembe helyezhető egy erőforráscsoporthoz is. Az elérhető erőforrások listájáért lásd: [ARM-sablon referenciája](/azure/templates).

## <a name="schema"></a>Séma

Sablonok esetében használja az alábbi sémát:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

A paraméter fájljaihoz használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Üzembe helyezési parancsok

Egy erőforráscsoporthoz való üzembe helyezéshez használja az erőforráscsoport telepítési parancsait.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI esetén használja az [az Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create)paranccsal. A következő példa egy sablont helyez üzembe egy erőforráscsoport létrehozásához:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A PowerShell üzembe helyezési parancsához használja a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)parancsot. A következő példa egy sablont helyez üzembe egy erőforráscsoport létrehozásához:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

További információt az üzembe helyezési parancsokról és az ARM-sablonok üzembe helyezési lehetőségeiről a következő témakörben talál:

* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](deploy-portal.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure CLI-vel](deploy-cli.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Resource Manager REST API](deploy-rest.md)
* [Sablonok üzembe helyezése a GitHub-tárházból a központi telepítés gomb használatával](deploy-to-azure-button.md)
* [ARM-sablonok üzembe helyezése Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Központi telepítési hatókörök

Egy erőforráscsoport telepítésekor az erőforrásokat az alábbiakra lehet telepíteni:

* a cél erőforráscsoport a műveletből
* más erőforráscsoportok ugyanabban az előfizetésben vagy más előfizetésben
* a [bővítmény erőforrásai](scope-extension-resources.md) alkalmazhatók az erőforrásokra

A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

Ez a szakasz bemutatja, hogyan határozhat meg különböző hatóköröket. Ezeket a különböző hatóköröket egyetlen sablonban kombinálhatja.

### <a name="scope-to-target-resource-group"></a>Hatókör a célként megadott erőforráscsoporthoz

Ha erőforrásokat szeretne telepíteni a cél erőforrásra, adja hozzá ezeket az erőforrásokat a sablon erőforrások szakaszához.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

### <a name="scope-to-resource-group-in-same-subscription"></a>Hatókör az erőforráscsoporthoz ugyanahhoz az előfizetéshez

Ha az erőforrásokat egy másik erőforráscsoporthoz szeretné telepíteni ugyanabban az előfizetésben, adjon hozzá egy beágyazott központi telepítést, és vegye fel a `resourceGroup` tulajdonságot. Ha nem határozza meg az előfizetés-azonosítót vagy az erőforráscsoportot, a rendszer az előfizetést és az erőforráscsoportot használja a fölérendelt sablonból. Az összes erőforráscsoport léteznie kell az üzemelő példány futtatása előtt.

A következő példában a beágyazott telepítés a nevű erőforráscsoportot célozza meg `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

### <a name="scope-to-resource-group-in-different-subscription"></a>Hatókör az erőforráscsoporthoz eltérő előfizetésben

Ha egy másik előfizetésben lévő erőforráscsoporthoz szeretne erőforrásokat telepíteni, adjon hozzá egy beágyazott központi telepítést, és adja meg a `subscriptionId` és a `resourceGroup` tulajdonságokat. A következő példában a beágyazott telepítés a nevű erőforráscsoportot célozza meg `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

## <a name="cross-resource-groups"></a>Több erőforráscsoport

Egyetlen ARM-sablonban több erőforráscsoporthoz is üzembe helyezhető. Egy olyan erőforráscsoport célzásához, amely nem azonos a fölérendelt sablon nevével, használjon [beágyazott vagy csatolt sablont](linked-templates.md). A központi telepítési erőforrástípus mezőben adja meg az előfizetés-azonosító és az erőforráscsoport azon értékeit, amelyekre a beágyazott sablont telepíteni szeretné. Az erőforráscsoportok különböző előfizetésekben találhatók.

> [!NOTE]
> Egyetlen üzemelő példányban **800-erőforráscsoportok** is üzembe helyezhetők. Ez a korlátozás általában azt jelenti, hogy egy, a szülő sablonhoz megadott erőforráscsoport, valamint a beágyazott vagy csatolt központi telepítések legfeljebb 799 erőforráscsoporthoz telepíthetők. Ha azonban a fölérendelt sablon csak beágyazott vagy csatolt sablonokat tartalmaz, és nem helyezi üzembe semmilyen erőforrást, akkor akár 800 erőforráscsoportot is felvehet beágyazott vagy csatolt központi telepítésekben.

A következő példa két Storage-fiókot telepít. A rendszer az első Storage-fiókot telepíti a telepítési műveletben megadott erőforráscsoporthoz. A második Storage-fiók a (z) és a (z) paraméterben megadott erőforráscsoporthoz van telepítve `secondResourceGroup` `secondSubscriptionID` :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Ha `resourceGroup` olyan erőforráscsoport nevét állítja be, amely nem létezik, akkor a telepítés sikertelen lesz.

Az előző sablon teszteléséhez és az eredmények megtekintéséhez használja a PowerShell vagy az Azure CLI-t.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha két tárolási fiókot kíván üzembe helyezni két erőforráscsoporthoz ugyanabban az **előfizetésben** , használja a következőt:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Két Storage-fiók **két előfizetésben** való üzembe helyezéséhez használja a következőt:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha két tárolási fiókot kíván üzembe helyezni két erőforráscsoporthoz ugyanabban az **előfizetésben** , használja a következőt:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Két Storage-fiók **két előfizetésben** való üzembe helyezéséhez használja a következőt:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="next-steps"></a>Következő lépések

* A Azure Security Center munkaterület-beállításainak központi telepítésére példát a következő témakörben talál: [deployASCwithWorkspaceSettings.js](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
