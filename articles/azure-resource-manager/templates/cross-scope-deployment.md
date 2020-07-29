---
title: Erőforrások központi telepítése hatókörök között
description: Azt mutatja be, hogyan kell több hatókört megcélozni egy központi telepítés során. A hatókör lehet bérlő, felügyeleti csoport, előfizetések és erőforráscsoportok.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 6161401ac039551a814b595715f56df1ac62dd6c
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374527"
---
# <a name="deploy-azure-resources-across-scopes"></a>Azure-erőforrások üzembe helyezése hatókörök között

Azure Resource Manager-sablonokkal (ARM-sablonok) több hatókörre is telepíthető egyetlen központi telepítésben. A rendelkezésre álló hatókörök a bérlők, a felügyeleti csoportok, az előfizetések és az erőforráscsoportok. Például telepítheti az erőforrásokat egy erőforráscsoporthoz, és ugyanabban a sablonban telepítheti az erőforrásokat egy másik erőforráscsoporthoz. Emellett erőforrásokat is telepíthet egy felügyeleti csoportba, és erőforrásokat telepíthet az adott felügyeleti csoportba tartozó erőforráscsoporthoz is.

[Beágyazott vagy csatolt sablonok](linked-templates.md) használatával olyan hatóköröket adhat meg, amelyek eltérnek a telepítési művelet elsődleges hatókörével.

## <a name="available-scopes"></a>Elérhető hatókörök

A központi telepítési művelethez használt hatókör határozza meg, hogy mely egyéb hatókörök érhetők el. Telepítheti a [bérlőt](deploy-to-tenant.md), a [felügyeleti csoportot](deploy-to-management-group.md), az [előfizetést](deploy-to-subscription.md)vagy az [erőforráscsoportot](deploy-powershell.md). Az elsődleges telepítési szinttől kezdve a hierarchiában nem mehet fel szintek. Ha például egy előfizetésre telepít központilag, nem léphet fel olyan szintre, amely erőforrásokat telepít egy felügyeleti csoportba. Azonban telepítheti a felügyeleti csoportba, és leállíthatja az előfizetésre vagy az erőforráscsoporthoz való központi telepítést.

Minden hatókör esetében a sablont telepítő felhasználónak rendelkeznie kell az erőforrások létrehozásához szükséges engedélyekkel.

## <a name="cross-resource-groups"></a>Több erőforráscsoport

Egy olyan erőforráscsoport célzásához, amely nem azonos a fölérendelt sablon nevével, használjon [beágyazott vagy csatolt sablont](linked-templates.md). A központi telepítési erőforrástípus mezőben adja meg az előfizetés-azonosító és az erőforráscsoport azon értékeit, amelyekre a beágyazott sablont telepíteni szeretné. Az erőforráscsoportok különböző előfizetésekben találhatók.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Ha nem határozza meg az előfizetés-azonosítót vagy az erőforráscsoportot, a rendszer az előfizetést és az erőforráscsoportot használja a fölérendelt sablonból. Az összes erőforráscsoport léteznie kell az üzemelő példány futtatása előtt.

> [!NOTE]
> Egyetlen üzemelő példányban **800-erőforráscsoportok** is üzembe helyezhetők. Ez a korlátozás általában azt jelenti, hogy egy, a szülő sablonhoz megadott erőforráscsoport, valamint a beágyazott vagy csatolt központi telepítések legfeljebb 799 erőforráscsoporthoz telepíthetők. Ha azonban a fölérendelt sablon csak beágyazott vagy csatolt sablonokat tartalmaz, és nem helyezi üzembe semmilyen erőforrást, akkor akár 800 erőforráscsoportot is felvehet beágyazott vagy csatolt központi telepítésekben.

A következő példa két Storage-fiókot telepít. A rendszer az első Storage-fiókot telepíti a telepítési műveletben megadott erőforráscsoporthoz. A második Storage-fiók a (z) és a (z) paraméterben megadott erőforráscsoporthoz van telepítve `secondResourceGroup` `secondSubscriptionID` :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Ha `resourceGroup` olyan erőforráscsoport nevét állítja be, amely nem létezik, akkor a telepítés sikertelen lesz.

Az előző sablon teszteléséhez és az eredmények megtekintéséhez használja a PowerShell vagy az Azure CLI-t.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha két tárolási fiókot kíván üzembe helyezni két erőforráscsoporthoz ugyanabban az **előfizetésben**, használja a következőt:

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

Két Storage-fiók **két előfizetésben**való üzembe helyezéséhez használja a következőt:

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

Ha két tárolási fiókot kíván üzembe helyezni két erőforráscsoporthoz ugyanabban az **előfizetésben**, használja a következőt:

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

Két Storage-fiók **két előfizetésben**való üzembe helyezéséhez használja a következőt:

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

## <a name="cross-subscription-management-group-and-tenant"></a>Több előfizetés, felügyeleti csoport és bérlő

Az előfizetés, a felügyeleti csoport és a bérlői szintű üzemelő példányok különböző hatókörének megadásakor beágyazott központi telepítéseket használ, például az erőforráscsoportok példáját. A hatókör megadásához használt tulajdonságok eltérőek lehetnek. Ezeket a forgatókönyveket az üzembe helyezések szintjeivel kapcsolatos cikkek tartalmazzák. További információkért lásd:

* [Erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](deploy-to-subscription.md)
* [Erőforrások létrehozása a felügyeleti csoport szintjén](deploy-to-management-group.md)
* [Erőforrások létrehozása a bérlői szinten](deploy-to-tenant.md)

## <a name="how-functions-resolve-in-scopes"></a>A függvények feloldása hatókörökben

Ha egynél több hatókörre telepít központilag, a [resourceGroup ()](template-functions-resource.md#resourcegroup) és az [előfizetés ()](template-functions-resource.md#subscription) függvények a sablon megadása alapján eltérően oldhatók fel. Ha külső sablonra hivatkozik, a függvények mindig a sablon hatókörére lesznek feloldva. Ha sablonon belül ágyaz be egy sablont, a (z `expressionEvaluationOptions` ) tulajdonság használatával megadhatja, hogy a függvények feloldhatók-e az erőforráscsoport és a fölérendelt sablon vagy a beágyazott sablon előfizetése között. Állítsa a tulajdonságot úgy, `inner` hogy feloldja a beágyazott sablon hatókörét. Állítsa a tulajdonságot úgy, `outer` hogy feloldja a fölérendelt sablon hatókörét.

Az alábbi táblázat azt mutatja, hogy a függvények feloldhatók-e a szülő vagy a beágyazott erőforráscsoport és előfizetés esetében.

| Sablon típusa | Hatókör | Feloldás |
| ------------- | ----- | ---------- |
| beágyazott        | külső (alapértelmezett) | Szülő erőforráscsoport |
| beágyazott        | belső | Alerőforrás-csoport |
| csatolt        | n.a.   | Alerőforrás-csoport |

A következő [példában a sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) látható:

* beágyazott sablon alapértelmezett (külső) hatókörrel
* beágyazott sablon belső hatókörrel
* csatolt sablon

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Az előző sablon teszteléséhez és az eredmények megtekintéséhez használja a PowerShell vagy az Azure CLI-t.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Az előző példa kimenete a következő:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Az előző példa kimenete a következő:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---



## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogyan határozhat meg paramétereket a sablonban, olvassa el [a Azure Resource Manager sablonok struktúrájának és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* A gyakori telepítési hibák megoldásával kapcsolatos tippekért lásd: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](common-deployment-errors.md).
* A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [privát sablon üzembe helyezése sas-tokenrel](secure-template-with-sas-token.md).
