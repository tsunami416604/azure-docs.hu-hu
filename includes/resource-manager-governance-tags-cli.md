---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b9484336add0719749e9f0af56bdd70fa3906ef5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38752659"
---
Ha két címkét szeretne az erőforráscsoporthoz adni, használja az [az group update](/cli/azure/group#az_group_update) parancsot:

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Tegyük fel, hogy hozzá kíván adni egy harmadik címkét. Futtassa ismét a parancsot egy új címkével. A rendszer ezt a címkét hozzáfűzi a meglévő címkékhez.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Az erőforrások nem örökölnek címkéket az erőforráscsoporttól. Az erőforráscsoportnak jelenleg három címkéje van, de az erőforrásoknak nincs címkéjük. Ha az erőforráscsoport összes címkéjét alkalmazni kívánja a csoport erőforrásaira, és meg szeretné őrizni az erőforrások meglévő címkéit, használja a következő szkriptet:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

Másik megoldásként alkalmazhatja az erőforráscsoport címkéit az erőforrásokra a meglévő címkék megtartása nélkül:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

Ha több értéket szeretne kombinálni egyetlen címkében, használjon JSON-sztringet.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Az erőforráscsoport összes címkéjének eltávolításához tegye a következőket:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
