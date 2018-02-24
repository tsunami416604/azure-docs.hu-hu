---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b9484336add0719749e9f0af56bdd70fa3906ef5
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
Két címkét adhat hozzá egy erőforráscsoport, a [az csoport frissítése](/cli/azure/group#az_group_update) parancs:

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Tegyük fel, hogy hozzá szeretne adni egy harmadik címkét. Futtassa újra a parancsot az új címke. Hozzáfűzi a meglévő címkéket.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Erőforrások címkék nem örökli az erőforráscsoportot. Jelenleg az erőforráscsoport három címkék rendelkezik, de az erőforrások nem rendelkeznek címkéket. Címkéket az összes erőforráscsoport erőforrásaihoz, és tartsa meg az erőforrás meglévő címkék, használja a következő parancsfájlt:

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

Alternatív megoldásként címkékkel láthatja az erőforráscsoportból forrásokat tartani a meglévő címkék nélkül:

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

Egyesítése egyetlen címkének értékeket, használja a JSON karakterláncnak.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Az erőforráscsoport összes címkék eltávolításához használja:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
