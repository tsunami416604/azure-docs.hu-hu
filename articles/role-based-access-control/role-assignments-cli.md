---
title: Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure CLI használatával
description: Ismerje meg, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatások vagy felügyelt identitások számára az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure CLI használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b32df50715d5e7276861e0696df1bd6ceb3f684e
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471992"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure CLI használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] ez a cikk azt ismerteti, hogyan rendelhet hozzá szerepköröket az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához a következőket kell tennie:

- `Microsoft.Authorization/roleAssignments/write` és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)
- [Bash Azure Cloud Shell](/azure/cloud-shell/overview) vagy [Azure CLI](/cli/azure) -ben

## <a name="get-object-ids"></a>Objektum-azonosítók beolvasása

A szerepkör-hozzárendelések hozzáadásához vagy eltávolításához szükség lehet egy objektum egyedi AZONOSÍTÓjának megadására. Az azonosító formátuma: `11111111-1111-1111-1111-111111111111`. Az azonosítót a Azure Portal vagy az Azure CLI használatával szerezheti be.

### <a name="user"></a>Felhasználó

Az Azure AD-felhasználóhoz tartozó objektumazonosító beszerzéséhez az [az ad User show](/cli/azure/ad/user#az-ad-user-show)lehetőséget használhatja.

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Csoport

Az Azure AD-csoporthoz tartozó objektumazonosító beszerzéséhez használhatja az [az ad Group show](/cli/azure/ad/group#az-ad-group-show) vagy [az ad Group listát](/cli/azure/ad/group#az-ad-group-list).

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Alkalmazás

Az Azure AD egyszerű szolgáltatásnév (az alkalmazás által használt identitás) objektumazonosító beszerzéséhez használhatja [az az ad SP listát](/cli/azure/ad/sp#az-ad-sp-list). Egyszerű szolgáltatásnév esetén használja az objektumazonosító azonosítót, **ne** pedig az alkalmazás azonosítóját.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

A RBAC-ben a hozzáférés engedélyezéséhez hozzá kell adnia egy szerepkör-hozzárendelést.

### <a name="user-at-a-resource-group-scope"></a>Felhasználó az erőforráscsoport hatókörében

Egy erőforráscsoport-hatókörben lévő felhasználóhoz tartozó szerepkör-hozzárendelés hozzáadásához használja [az az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)parancsot.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

A következő példa hozzárendeli a *virtuális gépi közreműködő* szerepkört a *patlong\@contoso.com* felhasználóhoz a *Pharma-Sales* erőforráscsoport hatókörében:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Az egyedi szerepkör-azonosító használata

Néhány alkalommal, amikor a szerepkör neve változhat, például:

- Saját egyéni szerepkört használ, és úgy dönt, hogy megváltoztatja a nevet.
- Olyan előzetes verziójú szerepkört használ, amely **(előzetes verzió)** szerepel a névben. A szerepkör megjelenése után a rendszer átnevezi a szerepkört.

> [!IMPORTANT]
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A szerepkör-azonosító nem változik, még akkor is, ha a szerepkör át lett nevezve. Ha parancsfájlokat vagy automationt használ a szerepkör-hozzárendelések létrehozásához, ajánlott az egyedi szerepkör-azonosítót használni a szerepkör neve helyett. Ezért ha egy szerepkört átneveznek, a parancsfájlok nagyobb valószínűséggel fognak működni.

Ha szerepkör-hozzárendelést szeretne hozzáadni a szerepkör neve helyett az egyedi szerepkör-AZONOSÍTÓval, használja az [az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)parancsot.

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

A következő példa hozzárendeli a [virtuális gép közreműködői](built-in-roles.md#virtual-machine-contributor) szerepkört a *patlong\@contoso.com* felhasználóhoz a *Pharma-Sales* erőforráscsoport hatókörében. Az egyedi szerepkör-azonosító beszerzéséhez használja az [az role definition List](/cli/azure/role/definition#az-role-definition-list) vagy [Az Azure-erőforrások beépített szerepköreit](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Csoport előfizetési hatóköre

Egy csoporthoz tartozó szerepkör-hozzárendelés hozzáadásához használja [az az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)lehetőséget. További információ a csoport objektumazonosító beszerzéséről: [objektumazonosítók beolvasása](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

A következő példa hozzárendeli az *olvasó* szerepkört az *Ann Mack Team* csoporthoz, amelynek azonosítója 22222222-2222-2222-2222-222222222222, előfizetési hatókörben.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Csoport erőforrás-hatókörben

Egy csoporthoz tartozó szerepkör-hozzárendelés hozzáadásához használja [az az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)lehetőséget. További információ a csoport objektumazonosító beszerzéséről: [objektumazonosítók beolvasása](#get-object-ids).

A következő példa hozzárendeli a *virtuálisgép-közreműködő* szerepkört az *Ann Mack Team* csoporthoz a 22222222-2222-2222-2222-222222222222 azonosítóval, amely egy *Pharma-Sales-Project-Network*nevű virtuális hálózat erőforrás-hatóköre.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Alkalmazás erőforráscsoport-hatókörben

Egy alkalmazáshoz tartozó szerepkör-hozzárendelés hozzáadásához használja [az az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)lehetőséget. További információ az alkalmazás objektum-AZONOSÍTÓjának lekéréséről: [objektumazonosítók beolvasása](#get-object-ids).

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Az alábbi példa a virtuálisgép- *közreműködő* szerepkört hozzárendeli egy 44444444-4444-4444-4444-444444444444-es azonosítójú alkalmazáshoz a *Pharma-Sales* erőforráscsoport hatókörében.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Felhasználó az előfizetés hatókörében

Ha hozzá szeretne adni egy szerepkör-hozzárendelést egy felhasználóhoz az előfizetés hatókörében, használja [az az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)parancsot. Az előfizetés-azonosító lekéréséhez a Azure Portal az **előfizetések** panelén található, vagy használhatja az [az Account List](/cli/azure/account#az-account-list)lehetőséget.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Az alábbi példa hozzárendeli az *olvasó* szerepkört a *annm\@example.com* felhasználóhoz egy előfizetési hatókörben.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Felhasználó egy felügyeleti csoport hatókörében

Egy felügyeleti csoport hatókörében lévő felhasználóhoz tartozó szerepkör-hozzárendelés hozzáadásához használja [az az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)parancsot. A felügyeleti csoport AZONOSÍTÓjának lekéréséhez a Azure Portal **felügyeleti csoportok** paneljén található, vagy használhatja az [az Account Management-Group listát](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

A következő példa hozzárendeli a *Számlázási olvasó* szerepkört az *Alain\@example.com* felhasználóhoz egy felügyeleti csoport hatókörében.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Új egyszerű szolgáltatásnév

Ha létrehoz egy új szolgáltatásnevet, és azonnal megpróbál hozzárendelni egy szerepkört az egyszerű szolgáltatáshoz, a szerepkör-hozzárendelés bizonyos esetekben sikertelen lehet. Ha például egy parancsfájl használatával új felügyelt identitást hoz létre, majd megpróbál hozzárendelni egy szerepkört az adott egyszerű szolgáltatáshoz, akkor a szerepkör-hozzárendelés sikertelen lehet. A hiba oka valószínűleg a replikálás késése. Az egyszerű szolgáltatás egy régióban jön létre; a szerepkör-hozzárendelés azonban egy másik régióban is előfordulhat, amely még nem replikálta a szolgáltatásnevet. Ennek a forgatókönyvnek a megoldásához a szerepkör-hozzárendelés létrehozásakor meg kell adnia a rendszerbiztonsági tag típusát.

Szerepkör-hozzárendelés hozzáadásához használja az [az szerepkör-hozzárendelési létrehozás](/cli/azure/role/assignment#az-role-assignment-create)lehetőséget, adjon meg egy értéket a `--assignee-object-id`számára, majd állítsa be a `--assignee-principal-type`t `ServicePrincipal`re.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

A következő példa a *virtuális gép közreműködői* szerepkört rendeli hozzá a *Pharma-Sales* erőforráscsoport hatókörének *MSI-test* felügyelt identitásához:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

A RBAC a hozzáférés eltávolításához távolítsa el a szerepkör-hozzárendelést az [az szerepkör-hozzárendelés törlése](/cli/azure/role/assignment#az-role-assignment-delete)paranccsal:

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Az alábbi példa eltávolítja a *virtuálisgép-közreműködő* szerepkör-hozzárendelést a *patlong\@contoso.com* felhasználótól a *Pharma-Sales* erőforráscsoporthoz:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Az alábbi példa eltávolítja az *olvasó* szerepkört az *Ann Mack Team* CSOPORTból az 22222222-2222-2222-2222-222222222222 azonosítóval egy előfizetési hatókörben. További információ a csoport objektumazonosító beszerzéséről: [objektumazonosítók beolvasása](#get-object-ids).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

A következő példa eltávolítja a *Számlázási olvasó* szerepkört az *Alain\@example.com* -felhasználótól a felügyeleti csoport hatókörében. A felügyeleti csoport AZONOSÍTÓjának lekéréséhez használhatja az [az Account Management-Group listát](/cli/azure/account/management-group#az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Következő lépések

- [Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure CLI használatával](role-assignments-list-cli.md)
- [Azure-erőforrások és-erőforráscsoportok kezelése az Azure CLI használatával](../azure-resource-manager/cli-azure-resource-manager.md)
