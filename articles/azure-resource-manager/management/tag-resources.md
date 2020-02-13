---
title: Erőforrások címkézése a logikai szervezet számára
description: Bemutatja, hogyan alkalmazhat címkéket az Azure-erőforrások számlázáshoz és felügyelethez való rendszerezéséhez.
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: c7f8d8672e205fa677bff33c8ed173c1105b26c6
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166614"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Címkék használata az Azure-erőforrások rendszerezéséhez

Címkéket alkalmazhat az Azure-erőforrásokra, hogy logikailag szervezze őket a besorolásba. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

A címkék alkalmazása után az előfizetés összes erőforrását lekérheti az adott címke nevével és értékével. A címkék lehetővé teszik a kapcsolódó erőforrások különböző erőforráscsoportokből való lekérését. Ez a megoldás akkor hasznos, ha számlázás vagy felügyelet céljából kell rendszereznie erőforrásait.

A besorolásnak egy önkiszolgáló metaadat-címkézési stratégiát kell figyelembe vennie az automatikus címkézési stratégia mellett, hogy csökkentse a felhasználók terhelését, és növelje a pontosságot.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>Korlátozások

Az alábbi korlátozások érvényesek a címkékre:

* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy lehet-e címkét alkalmazni az erőforrás típusára, tekintse meg [Az Azure-erőforrások támogatásának címkézését](tag-support.md)ismertető témakört.
* Minden erőforrás vagy erőforráscsoport legfeljebb 50 címke név/érték párokat tartalmazhat. Ha a maximálisan megengedettnél több címkét kell alkalmaznia, használjon egy JSON-karakterláncot a címke értékhez. A JSON-sztring sok olyan értéket tartalmazhat, amelyek egyetlen címkenévre vannak alkalmazva. Az erőforráscsoportok számos olyan erőforrást tartalmazhatnak, amelyek mindegyike 50 címke név/érték párokat tartalmaz.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Az általánosított virtuális gépek nem támogatják a címkéket.
* Az egyes erőforráscsoportokra alkalmazott címkéket nem öröklik az adott erőforráscsoportba tartozó erőforrások.
* A címkék nem alkalmazhatók a klasszikus erőforrásokra, például a Cloud Servicesra.
* A címkék neve nem tartalmazhatja a következő karaktereket: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Jelenleg Azure DNS zónák és Traffic Manger-szolgáltatások nem engedélyezik a szóközök használatát a címkében. 

## <a name="required-access"></a>Szükséges hozzáférés

Ahhoz, hogy címkéket lehessen alkalmazni az erőforrásokra, a felhasználónak írási hozzáféréssel kell rendelkeznie az adott erőforrás-típushoz. Ha címkéket szeretne alkalmazni az összes erőforrástípus, használja a [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkört. Ha csak egy erőforrás-típusra kíván címkéket alkalmazni, használja az adott erőforrás közreműködői szerepkörét. Ha például címkéket szeretne alkalmazni a virtuális gépekre, használja a [virtuális gép közreműködőjét](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="policies"></a>Szabályzatok

A [Azure Policy](../../governance/policy/overview.md) a címkézési szabályok és konvenciók betartatására használható. A szabályzat létrehozásával elkerülhető, hogy az előfizetéshez üzembe helyezett erőforrások milyen helyzetben legyenek, és nem felelnek meg a szervezete számára várt címkéknek. A címkék manuális alkalmazása vagy a nem megfelelő erőforrások keresése helyett létrehozhat egy olyan szabályzatot, amely automatikusan alkalmazza a szükséges címkéket az üzembe helyezés során. A címkék mostantól a meglévő erőforrásokra is alkalmazhatók az új [módosítási](../../governance/policy/concepts/effects.md#modify) effektussal és [szervizelési feladattal](../../governance/policy/how-to/remediate-resources.md). A következő szakasz példákat mutat be a címkékre.

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="powershell"></a>PowerShell

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

Ha meg szeretné tekinteni egy *adott névvel és erőforráscsoporthoz rendelkező erőforrás*meglévő címkéit, használja a következőt:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Ha pedig egy erőforrás erőforrás-azonosítója van, átadhatja ezt az erőforrás-azonosítót a címkék beszerzéséhez.

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

A *megadott címke névvel és értékkel rendelkező erőforráscsoportok*lekéréséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

A *megadott címke névvel és értékkel rendelkező erőforrások*lekéréséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

*Egy adott címke nevével rendelkező erőforrások*lekéréséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Minden alkalommal, amikor címkével lát el egy erőforrást vagy erőforráscsoportot, felülírja a hozzá tartozó korábbi címkéket. Ezért különböző megközelítéseket kell alkalmaznia annak függvényében, hogy az adott erőforrás vagy erőforráscsoport rendelkezik-e címkével.

Ha *meglévő címkék nélküli erőforráscsoporthoz* szeretne címkéket adni, használja a következőt:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ "Dept"="IT"; "Environment"="Test" }
```

Ha *meglévő címkékkel rendelkező erőforráscsoporthoz* szeretne címkéket adni, kérje le a meglévő címkéket, adja hozzá az új címkét, és alkalmazza ismét a címkéket:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Ha *meglévő címkék nélküli erőforráshoz* szeretne címkéket adni, használja a következőt:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $resource.ResourceId -Force
```

Egy erőforráscsoporthoz több azonos nevű erőforrás is tartozhat. Ebben az esetben beállíthatja az egyes erőforrásokat a következő parancsokkal:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Force }
```

Ha *meglévő címkékkel rendelkező erőforráshoz* szeretne címkéket adni, használja a következőt:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$resource.Tags.Add("Status", "Approved")
Set-AzResource -Tag $resource.Tags -ResourceId $resource.ResourceId -Force
```

Ha az erőforráscsoport összes címkéjét az erőforrásaira szeretné alkalmazni, és *nem szeretné megtartani a meglévő címkéket az erőforrásokon*, használja a következő parancsfájlt:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $group.Tags -Force }
```

Ha az erőforráscsoport összes címkéjét az erőforrásaira kívánja alkalmazni, és a *meglévő címkéket a nem duplikált erőforrásokon szeretné megtartani*, használja a következő parancsfájlt:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
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

```azurecli-interactive
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

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Ha egy erőforrás-gyűjteményen keresztüli hurkot szeretne használni, érdemes lehet az erőforrást erőforrás-azonosító alapján megjeleníteni. A cikk későbbi részében megjelenik egy teljes példa. *Megadott erőforrás-azonosítóval rendelkező erőforrás* meglévő címkéinek megtekintéséhez használja a következőt:

```azurecli-interactive
az resource show --id <resource-id> --query tags
```

Adott címkével rendelkező erőforráscsoportok lekéréséhez használja a `az group list`:

```azurecli-interactive
az group list --tag Dept=IT
```

Az adott címkével és értékkel rendelkező összes erőforrás beszerzéséhez használja a `az resource list`:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Ha címkéket ad hozzá egy erőforráscsoporthoz vagy erőforráshoz, felülírhatja a meglévő címkéket, vagy hozzáfűzheti az új címkéket a meglévő címkékhez.

Egy erőforráscsoport meglévő címkéit a következő paranccsal írhatja felül:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Ha címkét szeretne hozzáfűzni egy erőforráscsoport meglévő címkéjéhez, használja a következőt:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Egy erőforrás címkéit a következő paranccsal írhatja felül:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Ha címkét szeretne hozzáfűzni egy erőforrás meglévő címkéjéhez, használja a következőt:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Ha az erőforráscsoport összes címkéjét az erőforrásaira szeretné alkalmazni, és *nem szeretné megtartani a meglévő címkéket az erőforrásokon*, használja a következő parancsfájlt:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags $tags --id $id
done
```

Ha az erőforráscsoport összes címkéjét az erőforrásaira kívánja alkalmazni, és a *meglévő címkéket szeretné megőrizni az erőforrásokon*, használja a következő parancsfájlt:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')

resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  resourcejsontags=$(az resource show --id $id --query tags -o json)
  resourcetags=$(echo $resourcejsontags | tr -d '"{},' | sed 's/: /=/g')
  az resource tag --tags $tags$resourcetags --id $id
done
```

Ha a címke neve vagy értéke szóközt tartalmaz, néhány további lépést is végre kell hajtania. Az alábbi példa egy erőforráscsoport összes címkéjét az erőforrásaira alkalmazza, amikor a címkék szóközöket tartalmazhatnak.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>Sablonok

Ha egy erőforrást az üzembe helyezés során szeretne címkézni, adja hozzá a `tags` elemet a telepíteni kívánt erőforráshoz. Adja meg a címke nevét és értékét.

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

Ha a címkét datetime értékre szeretné beállítani, használja a [utcNow függvényt](../templates/template-functions-string.md#utcnow).

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

Ha címkéket szeretne alkalmazni egy erőforráscsoporthoz egy erőforrásra, használja a [resourceGroup](../templates/template-functions-resource.md#resourcegroup) függvényt. A címke értékének beolvasásakor használja a `tags[tag-name]` szintaxist a `tags.tag-name` szintaxis helyett, mert egyes karaktereket nem megfelelően értelmezi a pont jelölése.

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

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

A Azure Portal és a PowerShell egyaránt a [Resource Manager REST API](/rest/api/resources/) használja a jelenetek mögött. Ha a címkézést egy másik környezetbe kell integrálnia, az erőforrás-azonosító **lekérése** lehetőséggel beolvashatja a címkéket, és frissítheti a címkék készletét egy **javítási** hívás használatával.

## <a name="tags-and-billing"></a>Címkék és számlázás

A címkéket a számlázási adataik csoportosítására használhatja. Ha például több virtuális gépet futtat különböző szervezetekhez, a címkék használatával csoportosítsa a használatot Cost Center alapján. A címkék használatával kategorizálhatja a költségeket futásidejű környezet szerint, például az éles környezetben futó virtuális gépek számlázási használatát.

A címkékre vonatkozó információkat az [Azure erőforrás-használat és a RateCard API](../../billing/billing-usage-rate-card-overview.md) -k, illetve a használat vesszővel tagolt (CSV) fájl segítségével kérheti le. A használati fájlt a [Azure Fiókközpont](https://account.azure.com/Subscriptions) vagy Azure Portal töltheti le. További információkért tekintse [meg az Azure számlázási és napi használati adatainak letöltését vagy megtekintését](../../billing/billing-download-azure-invoice-daily-usage-date.md)ismertető témakört. A Azure Fiókközpont a használati fájl letöltésekor válassza a **2. verziót**. A számlázási címkéket támogató szolgáltatások esetében a címkék a **címkék** oszlopban jelennek meg.

REST API műveletekhez tekintse meg az [Azure számlázási REST API referenciáját](/rest/api/billing/).

## <a name="next-steps"></a>Következő lépések

* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy lehet-e címkét alkalmazni az erőforrás típusára, tekintse meg [Az Azure-erőforrások támogatásának címkézését](tag-support.md)ismertető témakört.
* A portál használatának bevezetését lásd: [a Azure Portal használata az Azure-erőforrások kezeléséhez](manage-resource-groups-portal.md).  
