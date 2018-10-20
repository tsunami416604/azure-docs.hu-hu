---
title: Azure-erőforrások logikai szervezet címkézése |} A Microsoft Docs
description: Bemutatja a számlázási és kezelése az Azure-erőforrások rendszerezése címkékkel.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: b6386f4a23a0ca6d0134f8c4e298a3f7100cc1d6
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466953"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Azure-erőforrások rendszerezése címkékkel

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Erőforrások címkékkel, a felhasználó adott erőforrástípus írási hozzáféréssel kell rendelkeznie. Címkéjét szeretné alkalmazni az összes erőforrástípus, használja a [közreműködői](../role-based-access-control/built-in-roles.md#contributor) szerepkör. A címkék alkalmazása csak egy erőforrás típusa, használja a közreműködő szerepkört az adott erőforráshoz. Például a címkék alkalmazása a virtuális gépekhez használja a [virtuális gépek Közreműködője](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="powershell"></a>PowerShell

A cikkben szereplő példák használatához az Azure PowerShell 6.0-s vagy újabb verziója szükséges. Ha nem rendelkezik 6.0-s vagy újabb, [verzió frissítése](/powershell/azure/install-azurerm-ps).

*Erőforráscsoportok* meglévő címkéinek megtekintéséhez használja a következőt:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

A szkript a következő formátumot adja vissza:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

*Megadott erőforrás-azonosítóval rendelkező erőforrás* meglévő címkéinek megtekintéséhez használja a következőt:

```powershell
(Get-AzureRmResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Vagy *megadott névvel és erőforráscsoporttal rendelkező erőforrás* meglévő címkéinek megtekintéséhez használja a következőt:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

*Adott címkével rendelkező erőforráscsoportok* lekéréséhez használja a következőt:

```powershell
(Get-AzureRmResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

*Adott címkével rendelkező erőforrások* lekéréséhez használja a következőt:

```powershell
(Get-AzureRmResource -Tag @{ Dept="Finance"}).Name
```

Az első *, amelyek egy adott címkével nevét erőforrásokhoz*, használja:

```powershell
(Get-AzureRmResource -TagName Dept).Name
```

Minden alkalommal, amikor címkével lát el egy erőforrást vagy erőforráscsoportot, felülírja a hozzá tartozó korábbi címkéket. Ezért különböző megközelítéseket kell alkalmaznia annak függvényében, hogy az adott erőforrás vagy erőforráscsoport rendelkezik-e címkével.

Ha *meglévő címkék nélküli erőforráscsoporthoz* szeretne címkéket adni, használja a következőt:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Ha *meglévő címkékkel rendelkező erőforráscsoporthoz* szeretne címkéket adni, kérje le a meglévő címkéket, adja hozzá az új címkét, és alkalmazza ismét a címkéket:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Ha *meglévő címkék nélküli erőforráshoz* szeretne címkéket adni, használja a következőt:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Ha *meglévő címkékkel rendelkező erőforráshoz* szeretne címkéket adni, használja a következőt:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved") 
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Ha az erőforráscsoport összes címkéjét szeretné alkalmazni a csoport erőforrásaira, és *nem szeretné megőrizni az erőforrások meglévő címkéit*, használja a következő szkriptet:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Get-AzureRmResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Ha az erőforráscsoport összes címkéjét szeretné alkalmazni a csoport erőforrásaira, és *nem szeretné megőrizni az erőforrások meglévő, nem ismétlődő címkéit*, a következő szkriptet kell használnia:

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = Get-AzureRmResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzureRmResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Az összes címke eltávolításához adjon át egy üres kivonattáblát:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
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

Vagy meglévő címkéinek megtekintéséhez egy *erőforrás, amely rendelkezik a megadott név, típus és erőforrás-csoport*, használja:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Hurkolás keresztül erőforrások gyűjteménye, amikor érdemes megjelenítése az erőforrás által erőforrás-azonosítója. A cikk későbbi részében látható egy teljes példát. *Megadott erőforrás-azonosítóval rendelkező erőforrás* meglévő címkéinek megtekintéséhez használja a következőt:

```azurecli
az resource show --id <resource-id> --query tags
```

Adott címkével rendelkező erőforráscsoportok lekéréséhez használja `az group list`:

```azurecli
az group list --tag Dept=IT
```

Egy bizonyos címkével és értékkel rendelkező összes erőforrást használja `az resource list`:

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

Címkék hozzáadása a címkékkel rendelkező erőforráshoz, beolvasni a meglévő címkéket, formázza újra ezt az értéket, és alkalmazza ismét a meglévő és új címkéket: 

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Ha az erőforráscsoport összes címkéjét szeretné alkalmazni a csoport erőforrásaira, és *nem szeretné megőrizni az erőforrások meglévő címkéit*, használja a következő szkriptet:

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

Összes címkéjét szeretné alkalmazni egy erőforráscsoportot az erőforrásaira, és *szeretné megőrizni az erőforrások meglévő címkéit*, használja a következő szkriptet:

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

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Portál

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Az Azure portal és a PowerShell is használja a [Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/) a háttérben. Ha integrálja a címkézés egy másik környezetbe kell, beszerezheti a címkék használatával **első** az erőforrás-azonosító és frissíteni a csoportot a címkék használatával egy **javítására** hívja.

## <a name="tags-and-billing"></a>A címkék és számlázás

Címkék használatával az elszámolási adatok. Például ha különböző szervezetek több virtuális gépet futtat, használja a címkék használata a költséghely szerint. Címkék használatával költségek kategorizálása futtatókörnyezet, például a számlázási használata az éles környezetben futó virtuális gépek szerint.

Információ a címkék használatával lekérheti a [Azure erőforrás-használat és RateCard API-k](../billing/billing-usage-rate-card-overview.md) vagy a használati vesszővel elválasztott értékeket (CSV) fájl. A használati fájl letöltése a [Azure Account Center](https://account.azure.com/Subscriptions) vagy az Azure Portalon. További információkért lásd: [letöltéséhez vagy az Azure számlázási és napi használati adatok megtekintéséhez](../billing/billing-download-azure-invoice-daily-usage-date.md). Válassza a használatot részletező fájl az Azure Account Center letöltésekor **2-es verzió**. A címkék a számlázási támogató szolgáltatások, a címkék jelennek meg a **címkék** oszlop.

REST API-műveleteket, lásd: [Azure Billing – REST API-referencia](/rest/api/billing/).

## <a name="next-steps"></a>További lépések

* Testreszabott házirendek használatával alkalmazhat korlátozások és konvenciói előfizetését. Egy Ön által meghatározott szabályzat szükség lehet, hogy az összes erőforrásnak rendelkeznie kell egy értéket egy adott címkét. További információkért lásd: [Mi az Azure Policy?](../azure-policy/azure-policy-introduction.md)
* Az Azure PowerShell-lel erőforrások telepítésekor bemutatását lásd: [az Azure PowerShell az Azure Resource Manager](powershell-azure-resource-manager.md).
* Bevezetés az Azure CLI használatával az erőforrások telepítésekor, lásd: [Mac, Linux és Windows az Azure Resource Manager az Azure CLI használatával](xplat-cli-azure-resource-manager.md).
* Bevezetés a portál használatával, lásd: [az Azure-erőforrások kezelése az Azure portal használatával](resource-group-portal.md).  
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
