---
title: Azure-erőforrások címkézése a logikai szervezet számára | Microsoft Docs
description: Bemutatja, hogyan alkalmazhat címkéket az Azure-erőforrások számlázáshoz és felügyelethez való rendszerezéséhez.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: tomfitz
ms.openlocfilehash: 9bcbfe1bdb501cac6ff31156db5382d1174eb8ad
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146842"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Azure-erőforrások rendszerezése címkékkel

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Ahhoz, hogy címkéket lehessen alkalmazni az erőforrásokra, a felhasználónak írási hozzáféréssel kell rendelkeznie az adott erőforrás-típushoz. Ha címkéket szeretne alkalmazni az összes erőforrástípus, használja a [közreműködő](../role-based-access-control/built-in-roles.md#contributor) szerepkört. Ha csak egy erőforrás-típusra kíván címkéket alkalmazni, használja az adott erőforrás közreműködői szerepkörét. Ha például címkéket szeretne alkalmazni a virtuális gépekre, használja a [virtuális gép közreműködőjét](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>Házirendek

A [Azure Policy](../governance/policy/overview.md) a címkézési szabályok és konvenciók betartatására használható. A szabályzat létrehozásával elkerülhető, hogy az előfizetéshez üzembe helyezett erőforrások milyen helyzetben legyenek, és nem felelnek meg a szervezete számára várt címkéknek. A címkék manuális alkalmazása vagy a nem megfelelő erőforrások keresése helyett létrehozhat egy olyan szabályzatot, amely automatikusan alkalmazza a szükséges címkéket az üzembe helyezés során. A címkék mostantól a meglévő erőforrásokra is alkalmazhatók az új [módosítási](../governance/policy/concepts/effects.md#modify) effektussal és [szervizelési feladattal](../governance/policy/how-to/remediate-resources.md). A következő szakasz példákat mutat be a címkékre.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

*Erőforráscsoportok* meglévő címkéinek megtekintéséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

A szkript a következő formátumot adja vissza:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

*Megadott erőforrás-azonosítóval rendelkező erőforrás* meglévő címkéinek megtekintéséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Vagy *megadott névvel és erőforráscsoporttal rendelkező erőforrás* meglévő címkéinek megtekintéséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

*Adott címkével rendelkező erőforráscsoportok* lekéréséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

*Adott címkével rendelkező erőforrások* lekéréséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

*Egy adott címke nevével rendelkező erőforrások*lekéréséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

Minden alkalommal, amikor címkével lát el egy erőforrást vagy erőforráscsoportot, felülírja a hozzá tartozó korábbi címkéket. Ezért különböző megközelítéseket kell alkalmaznia annak függvényében, hogy az adott erőforrás vagy erőforráscsoport rendelkezik-e címkével.

Ha *meglévő címkék nélküli erőforráscsoporthoz* szeretne címkéket adni, használja a következőt:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Ha *meglévő címkékkel rendelkező erőforráscsoporthoz* szeretne címkéket adni, kérje le a meglévő címkéket, adja hozzá az új címkét, és alkalmazza ismét a címkéket:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Ha *meglévő címkék nélküli erőforráshoz* szeretne címkéket adni, használja a következőt:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Ha *meglévő címkékkel rendelkező erőforráshoz* szeretne címkéket adni, használja a következőt:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Ha az erőforráscsoport összes címkéjét az erőforrásaira szeretné alkalmazni, és *nem szeretné megtartani a meglévő címkéket az erőforrásokon*, használja a következő parancsfájlt:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Ha az erőforráscsoport összes címkéjét az erőforrásaira kívánja alkalmazni, és a *meglévő címkéket a nem duplikált erőforrásokon*szeretné megtartani, használja a következő parancsfájlt:

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Az összes címke eltávolításához adjon át egy üres kivonattáblát:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

*Erőforráscsoportok* meglévő címkéinek megtekintéséhez használja a következőt:

```azurecli
az group show -n examplegroup --query tags
```

A szkript a következő formátumot adja vissza:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Vagy a *megadott névvel, típussal és erőforráscsoporthoz rendelkező erőforrás*meglévő címkéit a következő paranccsal tekintheti meg:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Ha egy erőforrás-gyűjteményen keresztüli hurkot szeretne használni, érdemes lehet az erőforrást erőforrás-azonosító alapján megjeleníteni. A cikk későbbi részében megjelenik egy teljes példa. *Megadott erőforrás-azonosítóval rendelkező erőforrás* meglévő címkéinek megtekintéséhez használja a következőt:

```azurecli
az resource show --id <resource-id> --query tags
```

Adott címkével rendelkező erőforráscsoportok lekéréséhez használja `az group list`a következőt:

```azurecli
az group list --tag Dept=IT
```

Az adott címkével és értékkel rendelkező erőforrások beszerzéséhez használja `az resource list`a következőt:

```azurecli
az resource list --tag Dept=Finance
```

Minden alkalommal, amikor címkével lát el egy erőforrást vagy erőforráscsoportot, felülírja a hozzá tartozó korábbi címkéket. Ezért különböző megközelítéseket kell alkalmaznia annak függvényében, hogy az adott erőforrás vagy erőforráscsoport rendelkezik-e címkével.

Ha *meglévő címkék nélküli erőforráscsoporthoz* szeretne címkéket adni, használja a következőt:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Ha *meglévő címkék nélküli erőforráshoz* szeretne címkéket adni, használja a következőt:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Ha címkéket szeretne felvenni egy olyan erőforráshoz, amely már rendelkezik címkékkel, kérje le a meglévő címkéket, formázza újra az értéket, majd alkalmazza újra a meglévő és az új címkéket:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Ha az erőforráscsoport összes címkéjét az erőforrásaira szeretné alkalmazni, és *nem szeretné megtartani a meglévő címkéket az erőforrásokon*, használja a következő parancsfájlt:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Ha az erőforráscsoport összes címkéjét az erőforrásaira kívánja alkalmazni, és a *meglévő címkéket szeretné megőrizni*az erőforrásokon, használja a következő parancsfájlt:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Sablonok

Ha egy erőforrást az üzembe helyezés során szeretne `tags` felcímkézni, adja hozzá az elemet az üzembe helyezett erőforráshoz. Adja meg a címke nevét és értékét.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Szövegkonstansérték alkalmazása a címkenévre

Az alábbi példában egy tárfiók látható két címkével (`Dept` és `Environment`), amelyek szövegkonstansértékre vannak beállítva:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

Ha a címkét datetime értékre szeretné beállítani, használja a [utcNow függvényt](resource-group-template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Objektum alkalmazása a címkeelemre

Megadhat olyan objektumparamétert, amely több címkét tartalmaz, majd alkalmazhatja azt az objektumot a címkeelemre. Az objektum minden tulajdonsága az erőforrás külön címkéjévé válik. Az alábbi példa egy `tagValues` nevű paramétert tartalmaz, amely a címkeelemre van alkalmazva.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>JSON-sztring alkalmazása a címkenévre

Ha több értéket szeretne tárolni egyetlen címkében, alkalmazzon a megfelelő értékeket képviselő JSON-sztringet. A teljes JSON-karakterlánc egyetlen címkeként van tárolva, amely nem lehet hosszabb 256 karakternél. Az alábbi példában egy `CostCenter` nevű címke szerepel, amely egy JSON-sztring számos értékét tartalmazza:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Címkék alkalmazása az erőforrás-csoportból

Ha címkéket szeretne alkalmazni egy erőforráscsoporthoz egy erőforrásra, használja a [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) függvényt. A címke értékének beolvasása során `tags.[tag-name]` a szintaxis helyett `tags.tag-name` használja a szintaxist, mert néhány karakter nem megfelelően van értelmezve a dot jelölésben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>Portál

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

A Azure Portal és a PowerShell egyaránt a [Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/) használja a jelenetek mögött. Ha a címkézést egy másik környezetbe kell integrálnia, az erőforrás-azonosító lekérése lehetőséggel beolvashatja a címkéket, és frissítheti a címkék készletét egy **javítási** hívás használatával.

## <a name="tags-and-billing"></a>Címkék és számlázás

A címkéket a számlázási adataik csoportosítására használhatja. Ha például több virtuális gépet futtat különböző szervezetekhez, a címkék használatával csoportosítsa a használatot Cost Center alapján. A címkék használatával kategorizálhatja a költségeket futásidejű környezet szerint, például az éles környezetben futó virtuális gépek számlázási használatát.

A címkékre vonatkozó információkat az [Azure erőforrás-használat és a RateCard API](../billing/billing-usage-rate-card-overview.md) -k, illetve a használat vesszővel tagolt (CSV) fájl segítségével kérheti le. A használati fájlt a [Azure Fiókközpont](https://account.azure.com/Subscriptions) vagy Azure Portal töltheti le. További információkért tekintse [meg az Azure számlázási és napi használati adatainak letöltését vagy megtekintését](../billing/billing-download-azure-invoice-daily-usage-date.md)ismertető témakört. A Azure Fiókközpont a használati fájl letöltésekor válassza a **2. verziót**. A számlázási címkéket támogató szolgáltatások esetében a címkék a **címkék** oszlopban jelennek meg.

REST API műveletekhez tekintse meg az [Azure számlázási REST API referenciáját](/rest/api/billing/).

## <a name="next-steps"></a>További lépések

* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy lehet-e címkét alkalmazni az erőforrás típusára, tekintse meg [Az Azure-erőforrások támogatásának címkézését](tag-support.md)ismertető témakört.
* A portál használatának bevezetését lásd: [a Azure Portal használata az Azure-erőforrások kezeléséhez](manage-resource-groups-portal.md).  
