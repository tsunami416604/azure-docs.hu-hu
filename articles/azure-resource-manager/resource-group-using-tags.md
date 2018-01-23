---
title: "Azure-erőforrások logikai szervezet címke |} Microsoft Docs"
description: "Bemutatja, hogyan számlázási és kezelése az Azure-erőforrások rendszerezéséhez címkékkel."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: tomfitz
ms.openlocfilehash: 31477cbf478d2d836c2d7c3472e3a53f13831480
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Azure-erőforrások rendszerezése címkékkel

[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [resource-manager-tag-resources-powershell](../../includes/resource-manager-tag-resources-powershell.md)]

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

Vagy, hogy a meglévő címkék egy *erőforrást, amely rendelkezik a megadott nevét, típusát és erőforrás-csoport*, használja:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Amikor ismétlése erőforrások gyűjteménye, érdemes megjelenítése az erőforrás által erőforrás-azonosító. A teljes példa a cikk későbbi részében. *Megadott erőforrás-azonosítóval rendelkező erőforrás* meglévő címkéinek megtekintéséhez használja a következőt:

```azurecli
az resource show --id <resource-id> --query tags
```

Egy adott címkét tartalmazó erőforráscsoportokat használatához `az group list`:

```azurecli
az group list --tag Dept=IT
```

Az erőforrások, amelyek egy adott címke és érték használatához `az resource list`:

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

Címkék hozzáadása egy erőforrást, amely már a címkék, beolvasni a meglévő címkéket, formázza újra ezt az értéket, és alkalmazza újra a meglévő és új címkék: 

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

A címkéket az összes erőforráscsoport erőforrásaihoz, és *tartsa meg az erőforrás meglévő címkék*, használja a következő parancsfájlt:

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

Az Azure portál és a PowerShell is használható a [Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/) a háttérben. Egy másik környezetbe címkézés integrálni kell, ha címkék használatával kaphat **beolvasása** az erőforrás-azonosító és a frissítés címkék használatával készlete egy **javítás** hívható meg.

## <a name="tags-and-billing"></a>Címkék és a számlázás

Címkék segítségével az elszámolási adatok. Például, ha a másik szervezet számára több virtuális gép fut, a címkék használata csoport használatra költségközpont által. Címkék segítségével költségek futtatókörnyezetben, például a számlázási használata az éles környezetben futó virtuális gépek szerinti kategóriák.

Információ a címkék keresztül lekérhesse a [Azure erőforrás-használat és RateCard API-k](../billing/billing-usage-rate-card-overview.md) vagy a használati vesszővel tagolt (CSV) fájl. Használat a fájl letöltése a [Azure-fiók portálon](https://account.windowsazure.com/) vagy [EA portal](https://ea.azure.com). További információ a számlázási adatokat programozott hozzáférést: [betekintést nyerhet a Microsoft Azure erőforrás-felhasználás](../billing/billing-usage-rate-card-overview.md). A REST API-műveleteket, lásd: [Azure számlázási REST API-referencia](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

Amikor a fürt megosztott kötetei szolgáltatás használata számlázási címkék támogató szolgáltatások tölt le, a címkék jelennek meg a **címkék** oszlop. További információkért lásd: [a számlázási megérteni a Microsoft Azure](../billing/billing-understand-your-bill.md).

![Tekintse meg a számlázási címkék](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>További lépések

* Testreszabott házirendekkel alkalmazhat korlátozások és egyezmények az előfizetéshez. Megadhat egy házirendet, minden erőforrás rendelkezik-e egy adott címke értéke lehet szükség. További információ: [Mi az az Azure Policy?](../azure-policy/azure-policy-introduction.md)
* Megismerkedhet az Azure PowerShell használatával, ha az erőforrások telepítése esetén, lásd: [az Azure PowerShell használata Azure Resource Managerrel](powershell-azure-resource-manager.md).
* Témakörben megismerkedhet az Azure parancssori felület használatával, ha az erőforrások telepítése esetén, [az Azure parancssori felület Mac, Linux és a Windows Azure Resource Manager használatával](xplat-cli-azure-resource-manager.md).
* Megismerkedhet a portál használatával, lásd: [az Azure portál használata az Azure erőforrások kezeléséhez](resource-group-portal.md).  
* Nagyvállalatoknak az [Azure enterprise scaffold - prescriptive subscription governance](resource-manager-subscription-governance.md) (Azure nagyvállalati struktúra - előíró előfizetés-irányítás) című cikk nyújt útmutatást az előfizetéseknek a Resource Managerrel való hatékony kezeléséről.
