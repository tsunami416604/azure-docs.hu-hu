---
title: Erőforrások telepítése több előfizetés & erőforráscsoporthoz
description: Bemutatja, hogyan célozhat meg több Azure-előfizetést és erőforráscsoportot az üzembe helyezés során.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 34de1d9df53d61d849ffbb81a57b468020bc3b65
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057381"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>Azure-erőforrások üzembe helyezése előfizetések vagy erőforráscsoportok között

A Resource Manager lehetővé teszi, hogy egyetlen üzemelő példányban egynél több erőforráscsoporthoz telepítsen. A beágyazott sablonok segítségével megadhatja a telepítési műveletben szereplő erőforráscsoporthoz eltérő erőforráscsoportokat. Az erőforráscsoportok különböző előfizetésekben találhatók.

> [!NOTE]
> Egyetlen üzemelő példányban **800-erőforráscsoportok** is üzembe helyezhetők. Ez a korlátozás általában azt jelenti, hogy egy, a szülő sablonhoz megadott erőforráscsoport, valamint a beágyazott vagy csatolt központi telepítések legfeljebb 799 erőforráscsoporthoz telepíthetők. Ha azonban a fölérendelt sablon csak beágyazott vagy csatolt sablonokat tartalmaz, és nem helyezi üzembe semmilyen erőforrást, akkor akár 800 erőforráscsoportot is felvehet beágyazott vagy csatolt központi telepítésekben.

## <a name="specify-subscription-and-resource-group"></a>Előfizetés és erőforráscsoport meghatározása

Egy olyan erőforráscsoport célzásához, amely nem azonos a fölérendelt sablon nevével, használjon [beágyazott vagy csatolt sablont](linked-templates.md). A központi telepítési erőforrástípus mezőben adja meg az előfizetés-azonosító és az erőforráscsoport azon értékeit, amelyekre a beágyazott sablont telepíteni szeretné.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Ha nem határozza meg az előfizetés-azonosítót vagy az erőforráscsoportot, a rendszer az előfizetést és az erőforráscsoportot használja a fölérendelt sablonból. Az összes erőforráscsoport léteznie kell az üzemelő példány futtatása előtt.

A sablont telepítő fióknak engedéllyel kell rendelkeznie a megadott előfizetés-AZONOSÍTÓhoz való üzembe helyezéshez. Ha a megadott előfizetés egy másik Azure Active Directory-bérlőn létezik, a [vendég felhasználókat hozzá kell adnia egy másik címtárból](../../active-directory/b2b/what-is-b2b.md).

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

## <a name="use-functions"></a>Függvények használata

A [resourceGroup ()](template-functions-resource.md#resourcegroup) és az [előfizetés ()](template-functions-resource.md#subscription) függvények a sablon megadásának módjától függően eltérő módon oldhatók fel. Ha külső sablonra hivatkozik, a függvények mindig a sablon hatókörére lesznek feloldva. Ha sablonon belül ágyaz be egy sablont, a (z `expressionEvaluationOptions` ) tulajdonság használatával megadhatja, hogy a függvények feloldhatók-e az erőforráscsoport és a fölérendelt sablon vagy a beágyazott sablon előfizetése között. Állítsa a tulajdonságot úgy, `inner` hogy feloldja a beágyazott sablon hatókörét. Állítsa a tulajdonságot úgy, `outer` hogy feloldja a fölérendelt sablon hatókörét.

Az alábbi táblázat azt mutatja, hogy a függvények feloldhatók-e a szülő vagy a beágyazott erőforráscsoport és előfizetés esetében.

| Sablon típusa | Hatókör | Megoldás: |
| ------------- | ----- | ---------- |
| beágyazott        | külső (alapértelmezett) | Szülő erőforráscsoport |
| beágyazott        | belső | Alerőforrás-csoport |
| csatolt        | N.A.   | Alerőforrás-csoport |

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
