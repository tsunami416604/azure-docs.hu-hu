---
title: Erőforrások, erőforráscsoportok és előfizetések címkézése logikai szervezethez
description: Bemutatja, hogyan alkalmazhat címkéket az Azure-erőforrások számlázáshoz és kezeléshez való rendszerezéséhez.
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: 2f437682a2ac415ce8478b09a44bff044bd9511b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255124"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Címkék használata az Azure-erőforrások és a felügyeleti hierarchia rendszerezéséhez

Címkéket alkalmazhat az Azure-erőforrásokra, az erőforráscsoportokra és az előfizetésekre, hogy logikailag rendszertanilag rendszerezze őket. Minden címke egy névből és egy értékpárból áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

A címkézési stratégia megvalósításával kapcsolatos javaslatokaz [Erőforrás-elnevezési és címkézési döntési útmutatóban](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)talál.

> [!IMPORTANT]
> A címkenevek ben nem lehet a kis- és nagybetűket. A címkeértékek ben a kis- és nagybetűk et is figyelembe kell.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Szükséges hozzáférés

Címkék erőforrásra való alkalmazásához írási hozzáféréssel kell rendelkeznie a **Microsoft.Resources/tags** erőforrástípushoz. A [Tag közreműködő](../../role-based-access-control/built-in-roles.md#tag-contributor) szerepkör lehetővé teszi, hogy címkéket alkalmazzon egy entitásra anélkül, hogy magának az entitásnak kellene hozzáférnie. Jelenleg a címke közreműködői szerepkör nem alkalmazhat címkéket az erőforrásokra vagy erőforráscsoportokra a portálon keresztül. Címkéket alkalmazhat az előfizetésekre a portálon keresztül. Támogatja az összes címke műveletek et PowerShell és a REST API-n keresztül.  

A [közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szerepkör is biztosítja a szükséges hozzáférést a címkék alkalmazása bármely entitásra. Ha csak egy erőforrástípusra szeretne címkéket alkalmazni, használja az erőforrás közreműködői szerepkörét. Ha például címkéket szeretne alkalmazni a virtuális gépekre, használja a [Virtuálisgép közreműködőjét.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Címkék alkalmazása

Az Azure PowerShell két parancsot kínál a címkék alkalmazásához : [New-AzTag](/powershell/module/az.resources/new-aztag) és [Update-AzTag.](/powershell/module/az.resources/update-aztag) Az Az.Resources modul 1.12.0 vagy újabb. A verziót a `Get-Module Az.Resources`segítségével ellenőrizheti. Telepítheti ezt a modult, vagy [telepítheti az Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1-es vagy újabb.

A **New-AzTag** felülírja az erőforrás, erőforráscsoport vagy előfizetés összes címkéjét. A parancs hívásakor adja át a címzni kívánt entitás erőforrásazonosítóját.

A következő példa címkéket alkalmaz egy tárfiókra:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Amikor a parancs befejeződik, figyelje meg, hogy az erőforrás két címkével rendelkezik.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Ha újra futtatja a parancsot, de ezúttal különböző címkékkel, figyelje meg, hogy a korábbi címkék törlődnek.

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

Ha címkéket szeretne hozzáadni egy olyan erőforráshoz, amely már rendelkezik címkékkel, használja **az Update-AzTag**szolgáltatást. Állítsa a **-Operation** paramétert **egyesítésre.**

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Figyelje meg, hogy a két új címke hozzá adódik a két meglévő címkéhez.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Minden címkenévnek csak egy értéke lehet. Ha új értéket ad meg egy címkéhez, a régi érték akkor is lecserélődik, ha az egyesítési műveletet használja. A következő példa az állapotcímkét Normál ról zöldre módosítja.

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

Ha a **-Operation** paramétert **csere**beállításra állítja, a meglévő címkéket az új címkekészlet váltja fel.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Csak az új címkék maradnak az erőforráson.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Ugyanezek a parancsok erőforráscsoportokkal vagy előfizetésekkel is működnek. Adja át a címzést megcímkézni kívánt erőforráscsoport vagy előfizetés azonosítóját.

Ha új címkekészletet szeretne hozzáadni egy erőforráscsoporthoz, használja a következőket:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Egy erőforráscsoport címkéinek frissítéséhez használja a következőket:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Ha új címkekészletet szeretne hozzáadni egy előfizetéshez, használja a következőket:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Az előfizetés címkéinek frissítéséhez használja a következőket:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Előfordulhat, hogy egy erőforráscsoportban több azonos nevű erőforrás is található. Ebben az esetben az egyes erőforrásokat a következő parancsokkal állíthatja be:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Címkék listázása

Egy erőforrás, erőforráscsoport vagy előfizetés címkéinek lekérni, használja a [Get-AzTag](/powershell/module/az.resources/get-aztag) parancsot, és adja át az entitás erőforrás-azonosítóját.

Egy erőforrás címkéinek megtekintéséhez használja a következőket:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Egy erőforráscsoport címkéinek megtekintéséhez használja a következőket:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Az előfizetés címkéinek megtekintéséhez használja a következőket:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Lista címke szerint

Ha adott címkenévvel és értékkel rendelkező erőforrásokat szeretne beszerezni, használja a következőket:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Ha olyan erőforrásokat szeretne beszerezni, amelyek nek bármilyen címkeértékkel rendelkező címkeneve van, használja a következőket:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Ha adott címkenévvel és értékkel rendelkező erőforráscsoportokat szeretne beszerezni, használja a következőket:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Címkék eltávolítása

Adott címkék eltávolításához használja **az Update-AzTag** parancsot, és állítsa be a **-Operation to** **Delete parancsot.** Adja át a törölni kívánt címkéket.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

A megadott címkék törlődnek.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Az összes címke eltávolításához használja az [Eltávolítás-AzTag](/powershell/module/az.resources/remove-aztag) parancsot.

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>Címkék alkalmazása

Ha címkéket ad hozzá egy erőforráscsoporthoz vagy erőforráshoz, felülírhatja a meglévő címkéket, vagy új címkéket fűzhet a meglévő címkékhez.

Az erőforrás címkéinek felülírásához használja a következőket:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Ha egy címkét hozzá szeretne fűzni egy erőforrás meglévő címkéihez, használja a következőket:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Az erőforráscsoport meglévő címkéinek felülírásához használja a következőket:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Ha egy erőforráscsoport meglévő címkéihez szeretne címkét hozzáfűzött, használja a következőket:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Jelenleg az Azure CLI nem támogatja a címkék alkalmazása az előfizetések.

### <a name="list-tags"></a>Címkék listázása

Egy erőforrás meglévő címkéinek megtekintéséhez használja a következőket:

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

### <a name="list-by-tag"></a>Lista címke szerint

Ha egy adott címkével és értékkel `az resource list`rendelkező összes erőforrást be szeretné szerezni, használja a következőket:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Ha adott címkével ellátott erőforráscsoportokat szeretne beszerezni, használja a következőket: `az group list`

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Terek kezelése

Ha a címkenevek vagy értékek szóközöket tartalmaznak, néhány további lépést kell tennie. A következő példa az erőforráscsoport összes címkéjét alkalmazza az erőforrásaira, ha a címkék szóközöket tartalmazhatnak.

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

Az erőforrásokat, az erőforráscsoportokat és az előfizetéseket az Erőforrás-kezelő sablonnal jelölheti meg az üzembe helyezés során.

### <a name="apply-values"></a>Értékek alkalmazása

A következő példa egy három címkét ú tárfiókot telepít. A címkék közül`Dept` `Environment`kettő ( és ) literális értékre van beállítva. Az egyik`LastDeployed`címke ( ) olyan paraméterre van beállítva, amely alapértelmezés szerint az aktuális dátumra érvényes.

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

Megadhat olyan objektumparamétert, amely több címkét tartalmaz, majd alkalmazhatja azt az objektumot a címkeelemre. Ez a megközelítés nagyobb rugalmasságot biztosít, mint az előző példában, mert az objektum különböző tulajdonságokkal rendelkezhet. Az objektum minden tulajdonsága az erőforrás külön címkéjévé válik. Az alábbi példa egy `tagValues` nevű paramétert tartalmaz, amely a címkeelemre van alkalmazva.

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

Ha több értéket szeretne tárolni egyetlen címkében, alkalmazzon a megfelelő értékeket képviselő JSON-sztringet. A teljes JSON-karakterlánc egy címkeként van tárolva, amely nem haladhatja meg a 256 karaktert. Az alábbi példában egy `CostCenter` nevű címke szerepel, amely egy JSON-sztring számos értékét tartalmazza:  

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

### <a name="apply-tags-from-resource-group"></a>Címkék alkalmazása erőforráscsoportból

Ha címkéket szeretne alkalmazni egy erőforráscsoportból egy erőforrásra, használja az [erőforráscsoport](../templates/template-functions-resource.md#resourcegroup) függvényt. A címkeérték beszerzésekor a szintaxis helyett használja a `tags[tag-name]` `tags.tag-name` szintaxist, mert néhány karakter nem megfelelően van elemezve a sorjelölésben.

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

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Címkék alkalmazása erőforráscsoportokra vagy -előfizetésekre

Címkéket adhat hozzá egy erőforráscsoporthoz vagy előfizetéshez a **Microsoft.Resources/tags** erőforrástípus telepítésével. A címkék a célerőforrás-csoportra vagy a központi telepítésre vonatkozó előfizetésre vonatkoznak. Minden alkalommal, amikor telepíti a sablont, lecseréli a címkéket, amelyekkorábban már alkalmazva voltak.

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

A címkék et egy erőforráscsoportra alkalmazni, használja a PowerShell vagy az Azure CLI. Telepítse a megcímkézni kívánt erőforráscsoportba.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

A címkék et egy előfizetésre, használja a PowerShell vagy az Azure CLI. Telepítse a címzést megcímkézni kívánt előfizetésre.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

A következő sablon hozzáadja az objektum címkéit egy erőforráscsoporthoz vagy előfizetéshez.

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

Ha címkékkel szeretne dolgozni az Azure REST API-n keresztül, használja a következőket:

* [Címkék - létrehozás vagy frissítés hatókörben](/rest/api/resources/tags/createorupdateatscope) (PUT művelet)
* [Címkék - Frissítés hatókörön](/rest/api/resources/tags/updateatscope) (PATCH művelet)
* [Címkék - Get At Scope](/rest/api/resources/tags/getatscope) (GET művelet)
* [Címkék - Törlés hatókörben](/rest/api/resources/tags/deleteatscope) (DELETE művelet)

## <a name="inherit-tags"></a>Címkék öröklése

Az erőforráscsoportra vagy előfizetésre alkalmazott címkéket nem öröklik az erőforrások. Ha előfizetésből vagy erőforráscsoportból szeretne címkéket alkalmazni az erőforrásokra, olvassa el az [Azure-szabályzatok – címkék című témakört.](tag-policies.md)

## <a name="tags-and-billing"></a>Címkék és számlázás

Címkék segítségével a számlázási adatok is csoportosíthatók. Ha például több virtuális gépet futtat különböző vállalatok számára, akkor a használatot címkék segítségével tudja költséghely szerint csoportosítani. A címkék a költségek futtatókörnyezet szerinti besorolására, például az éles környezetben futó virtuális gépek használatának kiszámlázására is felhasználhatók.

A címkékkel kapcsolatos információkat az [Azure Resource Usage és rateCard API-k](../../billing/billing-usage-rate-card-overview.md) on vagy a használati vesszővel tagolt értékek (CSV) fájlon keresztül kérheti le. A használati fájlt az [Azure Account Centerből](https://account.azure.com/Subscriptions) vagy az Azure Portalról töltheti le. További információ: [Az Azure-beli számlázási számla és a napi használati adatok letöltése vagy megtekintése.](../../billing/billing-download-azure-invoice-daily-usage-date.md) Amikor letölti a használati fájlt az Azure Account Centerből, válassza **a 2-es verzió**lehetőséget. A számlázási címkéket támogató szolgáltatások esetében a címkék a **Címkék** oszlopban jelennek meg.

Rest API-műveletek, lásd: [Azure Billing REST API-referencia.](/rest/api/billing/)

## <a name="limitations"></a>Korlátozások

Az alábbi korlátozások érvényesek a címkékre:

* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy alkalmazhat-e címkét egy erőforrástípusra, olvassa el [az Azure-erőforrások támogatásának címkézése.](tag-support.md)
* A felügyeleti csoportok jelenleg nem támogatják a címkéket.
* Minden erőforrás, erőforráscsoport és előfizetés legfeljebb 50 címkenév/értékpár lehet. Ha a maximálisan megengedett számnál több címkét kell alkalmaznia, használjon JSON-karakterláncot a címkeértékhez. A JSON-sztring sok olyan értéket tartalmazhat, amelyek egyetlen címkenévre vannak alkalmazva. Egy erőforráscsoport vagy előfizetés számos olyan erőforrást tartalmazhat, amelyek mindegyike 50 címkenév/értékpárral rendelkezik.
* A címke neve legfeljebb 512 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet. A tárfiókok esetében a címke neve legfeljebb 128 karakter, a címke értéke pedig legfeljebb 256 karakter hosszúságú lehet.
* Az általánosan általánosgépezett virtuális gépek nem támogatják a címkéket.
* Címkék nem alkalmazhatók a klasszikus erőforrások, például a Cloud Services.
* A címkenevek nem tartalmazhatják `<` `>`a `%` `&`következő `\` `?`karaktereket: , , , , , ,`/`

   > [!NOTE]
   > Jelenleg az Azure DNS-zónák és a Traffic Manger szolgáltatások is nem teszik lehetővé a szóközök használatát a címkében.

## <a name="next-steps"></a>További lépések

* Nem minden erőforrástípus támogatja a címkéket. Annak megállapításához, hogy alkalmazhat-e címkét egy erőforrástípusra, olvassa el [az Azure-erőforrások támogatásának címkézése.](tag-support.md)
* A címkézési stratégia megvalósításával kapcsolatos javaslatokaz [Erőforrás-elnevezési és címkézési döntési útmutatóban](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)talál.
