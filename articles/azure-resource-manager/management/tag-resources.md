---
title: Erőforrások, erőforráscsoportok és előfizetések címkézése a logikai szervezet számára
description: Bemutatja, hogyan alkalmazhat címkéket az Azure-erőforrások számlázáshoz és felügyelethez való rendszerezéséhez.
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 2f437682a2ac415ce8478b09a44bff044bd9511b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255124"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Címkék használata az Azure-erőforrások és a felügyeleti hierarchia rendszerezéséhez

Címkéket alkalmazhat az Azure-erőforrások, az erőforráscsoportok és az előfizetések számára, hogy logikailag szervezze őket a besorolásba. Minden címke egy nevet és egy érték párokat tartalmaz. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

A címkézési stratégia megvalósításával kapcsolatos javaslatokért lásd: [erőforrás-elnevezési és címkézési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

> [!IMPORTANT]
> A címkék nevei kis-és nagybetűk megkülönböztetése nélkül. A címke értékei megkülönböztetik a kis-és nagybetűket.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Szükséges hozzáférés

Ha címkéket szeretne alkalmazni egy erőforrásra, írási hozzáféréssel kell rendelkeznie a **Microsoft. Resources/Tags** erőforrástípus-típushoz. A [címke közreműködői](../../role-based-access-control/built-in-roles.md#tag-contributor) szerepkör lehetővé teszi, hogy címkéket alkalmazzon egy entitásra anélkül, hogy az entitáshoz hozzá kellene férnie. Jelenleg a tag közreműködői szerepkör nem alkalmazhat címkéket az erőforrásokra vagy az erőforráscsoportok a portálon keresztül. Címkéket is alkalmazhat az előfizetésekhez a portálon keresztül. Támogatja az összes címkézési műveletet a PowerShell és a REST API használatával.  

A [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkör emellett biztosítja a szükséges hozzáférést a címkék bármely entitásra való alkalmazásához. Ha csak egy erőforrás-típusra kíván címkéket alkalmazni, használja az adott erőforrás közreműködői szerepkörét. Ha például címkéket szeretne alkalmazni a virtuális gépekre, használja a [virtuális gép közreműködőjét](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Címkék alkalmazása

Azure PowerShell két parancsot kínál a címkék alkalmazásához: [New-AzTag](/powershell/module/az.resources/new-aztag) és [Update-AzTag](/powershell/module/az.resources/update-aztag). Az az. Resources Module 1.12.0 vagy újabb verzióval kell rendelkeznie. A verzióját a segítségével is megtekintheti `Get-Module Az.Resources`. Telepítheti a modult, vagy [telepítheti a Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1-es vagy újabb verzióját.

A **New-AzTag** az erőforráson, az erőforráscsoporton vagy az előfizetésen lévő összes címkét lecseréli. A parancs hívásakor adja meg a címkével ellátni kívánt entitás erőforrás-AZONOSÍTÓját.

Az alábbi példa a címkék egy készletét alkalmazza a Storage-fiókra:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Ha a parancs befejeződik, figyelje meg, hogy az erőforrásnak két címkéje van.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Ha újra futtatja a parancsot, de ezúttal más címkékkel, figyelje meg, hogy a korábbi címkék el lesznek távolítva.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Ha címkéket szeretne felvenni egy olyan erőforráshoz, amely már rendelkezik címkékkel, használja az **Update-AzTag**. Állítsa be a **-Operation** paramétert az **egyesítéshez**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Figyelje meg, hogy a két új címke hozzá lett adva a két meglévő címkéhez.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Minden címke nevének csak egy értéke lehet. Ha egy címkéhez új értéket ad meg, akkor a rendszer akkor is lecseréli a régi értéket, ha az egyesítési műveletet használja. Az alábbi példa megváltoztatja az állapot címkéjét a Normálról zöldre.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Ha a **-Operation** paramétert **lecseréli**, a meglévő címkéket a címkék új készlete váltja fel.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Csak az új címkék maradnak meg az erőforráson.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Ugyanezek a parancsok az erőforráscsoportok vagy az előfizetések esetében is működnek. Adja meg a címkével ellátni kívánt erőforráscsoport vagy előfizetés azonosítóját.

Ha új címkéket szeretne hozzáadni egy erőforráscsoporthoz, használja a következőt:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Egy erőforráscsoport címkéinak frissítéséhez használja a következőt:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Új címkék előfizetéshez való hozzáadásához használja a következőt:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Az előfizetéshez tartozó címkék frissítéséhez használja a következőt:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Egy erőforráscsoporthoz több azonos nevű erőforrás is tartozhat. Ebben az esetben beállíthatja az egyes erőforrásokat a következő parancsokkal:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Címkék listázása

Ha egy erőforrás, erőforráscsoport vagy előfizetés címkéit szeretné lekérni, használja a [Get-AzTag](/powershell/module/az.resources/get-aztag) parancsot, és adja meg az entitás erőforrás-azonosítóját.

Az adott erőforráshoz tartozó címkék megtekintéséhez használja a következőt:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Egy erőforráscsoport címkéit a következő paranccsal tekintheti meg:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Az előfizetés címkéit a következő paranccsal tekintheti meg:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Listázás címke szerint

A megadott címke névvel és értékkel rendelkező erőforrások lekéréséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Ha olyan erőforrásokat szeretne lekérni, amelyek címkével megadott névvel rendelkeznek, használja a következőt:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

A megadott címke névvel és értékkel rendelkező erőforráscsoportok lekéréséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Címkék eltávolítása

Adott címkék eltávolításához használja az **Update-AzTag** és a set **-Operation** parancsot a **törléshez**. Adja meg a törölni kívánt címkéket.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

A megadott címkék el lesznek távolítva.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Az összes címke eltávolításához használja a [Remove-AzTag](/powershell/module/az.resources/remove-aztag) parancsot.

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>Címkék alkalmazása

Ha címkéket ad hozzá egy erőforráscsoporthoz vagy erőforráshoz, felülírhatja a meglévő címkéket, vagy hozzáfűzheti az új címkéket a meglévő címkékhez.

Egy erőforrás címkéit a következő paranccsal írhatja felül:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Ha címkét szeretne hozzáfűzni egy erőforrás meglévő címkéjéhez, használja a következőt:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Egy erőforráscsoport meglévő címkéit a következő paranccsal írhatja felül:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Ha címkét szeretne hozzáfűzni egy erőforráscsoport meglévő címkéjéhez, használja a következőt:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Az Azure CLI jelenleg nem támogatja a címkék alkalmazását az előfizetésekhez.

### <a name="list-tags"></a>Címkék listázása

Az adott erőforráshoz tartozó meglévő címkék megtekintéséhez használja a következőt:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Erőforráscsoportok meglévő címkéinek megtekintéséhez használja a következőt:

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

### <a name="list-by-tag"></a>Listázás címke szerint

Az adott címkével és értékkel rendelkező erőforrások beszerzéséhez használja `az resource list`a következőt:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Adott címkével rendelkező erőforráscsoportok lekéréséhez használja `az group list`a következőt:

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Szóközök feldolgozása

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

Resource Manager-sablonnal az üzembe helyezés során erőforrásokat, erőforráscsoportokat és előfizetéseket címkézheti.

### <a name="apply-values"></a>Értékek alkalmazása

Az alábbi példa három címkével rendelkező Storage-fiókot telepít. A címkék közül kettő (`Dept` és `Environment`) konstans értékre van beállítva. Az egyik címke`LastDeployed`() egy olyan paraméterre van beállítva, amely alapértelmezett értéke az aktuális dátum.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
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
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
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

### <a name="apply-an-object"></a>Objektum alkalmazása

Megadhat olyan objektumparamétert, amely több címkét tartalmaz, majd alkalmazhatja azt az objektumot a címkeelemre. Ez a megközelítés nagyobb rugalmasságot biztosít az előző példánál, mert az objektum különböző tulajdonságokkal rendelkezhet. Az objektum minden tulajdonsága az erőforrás külön címkéjévé válik. Az alábbi példa egy `tagValues` nevű paramétert tartalmaz, amely a címkeelemre van alkalmazva.

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

### <a name="apply-a-json-string"></a>JSON-karakterlánc alkalmazása

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

Ha címkéket szeretne alkalmazni egy erőforráscsoporthoz egy erőforrásra, használja a [resourceGroup](../templates/template-functions-resource.md#resourcegroup) függvényt. A címke értékének beolvasása során `tags[tag-name]` a szintaxis helyett `tags.tag-name` használja a szintaxist, mert néhány karakter nem megfelelően van értelmezve a dot jelölésben.

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Címkék alkalmazása erőforráscsoportok vagy előfizetések számára

Hozzáadhat címkéket egy erőforráscsoporthoz vagy előfizetéshez a **Microsoft. Resources/Tags** erőforrástípus üzembe helyezésével. A címkéket a rendszer a célként megadott erőforráscsoporthoz vagy előfizetésre alkalmazza a központi telepítéshez. Minden alkalommal, amikor központilag telepíti a sablont, a rendszer már alkalmazta a címkéket.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Ha a címkéket egy erőforráscsoporthoz szeretné alkalmazni, használja a PowerShell vagy az Azure CLI-t. Telepítse a címkével ellátni kívánt erőforráscsoportot.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

A címkék előfizetésre való alkalmazásához használja a PowerShell vagy az Azure CLI-t. Telepítse a címkével ellátni kívánt előfizetést.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

A következő sablon hozzáadja a címkéket egy objektumból egy erőforráscsoporthoz vagy előfizetésbe.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portál

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Ha az Azure REST API használatával szeretne címkékkel dolgozni, használja a következőt:

* [Címkék – létrehozás vagy frissítés hatókör](/rest/api/resources/tags/createorupdateatscope) (Put művelet)
* [Címkék – frissítés a hatókörben](/rest/api/resources/tags/updateatscope) (javítási művelet)
* [Címkék – beolvasás hatóköre](/rest/api/resources/tags/getatscope) (lekérési művelet)
* [Címkék – törlés a hatókörben](/rest/api/resources/tags/deleteatscope) (törlési művelet)

## <a name="inherit-tags"></a>Címkék öröklése

Az erőforráscsoport vagy az előfizetés által alkalmazott címkéket nem öröklik az erőforrások. Ha címkéket szeretne alkalmazni egy előfizetésből vagy erőforráscsoporthoz az erőforrásokra, tekintse meg az [Azure-szabályzatok – címkék](tag-policies.md)című témakört.

## <a name="tags-and-billing"></a>Címkék és számlázás

Címkék segítségével a számlázási adatok is csoportosíthatók. Ha például több virtuális gépet futtat különböző vállalatok számára, akkor a használatot címkék segítségével tudja költséghely szerint csoportosítani. A címkék a költségek futtatókörnyezet szerinti besorolására, például az éles környezetben futó virtuális gépek használatának kiszámlázására is felhasználhatók.

A címkékre vonatkozó információkat az [Azure erőforrás-használat és a RateCard API](../../billing/billing-usage-rate-card-overview.md) -k, illetve a használat vesszővel tagolt (CSV) fájl segítségével kérheti le. A használati fájlt a [Azure Fiókközpont](https://account.azure.com/Subscriptions) vagy Azure Portal töltheti le. További információkért tekintse [meg az Azure számlázási és napi használati adatainak letöltését vagy megtekintését](../../billing/billing-download-azure-invoice-daily-usage-date.md)ismertető témakört. A Azure Fiókközpont a használati fájl letöltésekor válassza a **2. verziót**. A számlázási címkéket támogató szolgáltatások esetében a címkék a **címkék** oszlopban jelennek meg.

REST API műveletekhez tekintse meg az [Azure számlázási REST API referenciáját](/rest/api/billing/).

## <a name="limitations"></a>Korlátozások

Az alábbi korlátozások érvényesek a címkékre:

* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy lehet-e címkét alkalmazni az erőforrás típusára, tekintse meg [Az Azure-erőforrások támogatásának címkézését](tag-support.md)ismertető témakört.
* A felügyeleti csoportok jelenleg nem támogatják a címkéket.
* Minden erőforrás, erőforráscsoport és előfizetés legfeljebb 50 címke név/érték párokat tartalmazhat. Ha a maximálisan megengedettnél több címkét kell alkalmaznia, használjon egy JSON-karakterláncot a címke értékhez. A JSON-sztring sok olyan értéket tartalmazhat, amelyek egyetlen címkenévre vannak alkalmazva. Egy erőforráscsoport vagy előfizetés több olyan erőforrást is tartalmazhat, amelyek mindegyike 50 címke név/érték párokat tartalmaz.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Az általánosított virtuális gépek nem támogatják a címkéket.
* A címkék nem alkalmazhatók a klasszikus erőforrásokra, például a Cloud Servicesra.
* A címkék nevei nem tartalmazhatják a `<`következő `>`karaktereket `%`: `&` `\`,, `?`,,,,`/`

   > [!NOTE]
   > Jelenleg Azure DNS zónák és Traffic Manger-szolgáltatások nem engedélyezik a szóközök használatát a címkében.

## <a name="next-steps"></a>További lépések

* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy lehet-e címkét alkalmazni az erőforrás típusára, tekintse meg [Az Azure-erőforrások támogatásának címkézését](tag-support.md)ismertető témakört.
* A címkézési stratégia megvalósításával kapcsolatos javaslatokért lásd: [erőforrás-elnevezési és címkézési döntési útmutató](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
