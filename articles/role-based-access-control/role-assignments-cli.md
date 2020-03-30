---
title: Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure CLI használatával
description: Ismerje meg, hogyan adhat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatástagok vagy felügyelt identitások számára az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure CLI használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245667"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure CLI használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Ez a cikk ismerteti, hogyan rendelhet szerepköröket az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához a következőkre van szükség:

- `Microsoft.Authorization/roleAssignments/write`és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például [a Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy [a tulajdonos](built-in-roles.md#owner)
- [Bash az Azure Cloud Shell](/azure/cloud-shell/overview) ben vagy [az Azure CLI-ben](/cli/azure)

## <a name="get-object-ids"></a>Objektumazonosítók beszerezése

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához előfordulhat, hogy meg kell adnia egy objektum egyedi azonosítóját. Az azonosító formátuma: `11111111-1111-1111-1111-111111111111`. Az Azure Portalon vagy az Azure CLI-n keresztül kaphatja le az azonosítót.

### <a name="user"></a>Felhasználó

Az Azure AD-felhasználó objektumazonosítójának lekérnie [használhatja az ad felhasználói show-t.](/cli/azure/ad/user#az-ad-user-show)

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>Csoport

Egy Azure AD-csoport objektumazonosítójának lekérnie használhatja az ad group show vagy az ad group list .To get the object ID d for a Object ID group, you can use [az ad group show](/cli/azure/ad/group#az-ad-group-show) or az [ad group list.](/cli/azure/ad/group#az-ad-group-list)

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Alkalmazás

Az Azure AD egyszerű szolgáltatás (egy alkalmazás által használt identitás) objektumazonosítójának lekérnie használhatja [az ad sp list .](/cli/azure/ad/sp#az-ad-sp-list) Egyszerű szolgáltatás esetén az objektumazonosítót használja, **ne** az alkalmazásazonosítót.

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az RBAC-ban a hozzáférés engedélyezéséhez egy szerepkör-hozzárendelést kell hozzáadnia.

### <a name="user-at-a-resource-group-scope"></a>Felhasználó egy erőforráscsoport hatókörén

Ha szerepkör-hozzárendelést szeretne hozzáadni egy felhasználóhoz egy erőforráscsoport hatókörén, használja [az az szerepkör-hozzárendelés létrehozása .](/cli/azure/role/assignment#az-role-assignment-create)

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

A következő példa a *Virtuálisgép közreműködői* szerepkört a *patlong\@contoso.com* felhasználóhoz rendeli hozzá a *pharma-sales* erőforráscsoport hatókörén:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>Az egyedi szerepkör-azonosító használata

Néhány alkalommal, amikor egy szerepkör neve megváltozhat, például:

- Saját egyéni szerepkört használ, és úgy dönt, hogy módosítja a nevet.
- Olyan előnézeti szerepkört használ, amelynek **neve (előnézet)** szerepel. A szerepkör megjelenésekor a szerepkör átnevezésre kerül.

> [!IMPORTANT]
> Az előzetes verzió szolgáltatásiszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Még ha egy szerepkör t átnevezik is, a szerepkör-azonosító nem változik. Ha parancsfájlokat vagy automatizálást használ a szerepkör-hozzárendelések létrehozásához, ajánlott az egyedi szerepkör-azonosítót használni a szerepkör neve helyett. Ezért ha egy szerepkör tátlonlás, a parancsfájlok nagyobb valószínűséggel működnek.

Ha a szerepkörnév helyett az egyedi szerepkör-azonosítót használó szerepkör-hozzárendelést szeretne hozzáadni, használja az [az szerepkör-hozzárendelés létrehozása .](/cli/azure/role/assignment#az-role-assignment-create)

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

A következő példa hozzárendeli a [Virtuálisgép közreműködő](built-in-roles.md#virtual-machine-contributor) szerepkört a *\@patlong contoso.com* felhasználóhoz a *pharma-sales* erőforráscsoport hatókörén. Az egyedi szerepkör-azonosító lekérni, használhatja [az szerepkör-definíciós lista,](/cli/azure/role/definition#az-role-definition-list) vagy tekintse meg [a beépített szerepkörök az Azure-erőforrások.](built-in-roles.md)

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>Csoport egy előfizetési hatókörben

Ha szerepkör-hozzárendelést szeretne hozzáadni egy csoporthoz, használja [az az szerepkör-hozzárendelés létrehozása .](/cli/azure/role/assignment#az-role-assignment-create) A csoport objektumazonosítójának beszerzéséről az [Objektumazonosítók beszedése](#get-object-ids)című témakörben talál további információt.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

A következő példa az *Olvasó* szerepkört a 2222222222-es azonosítóval rendelkező *Ann Mack-csapatcsoporthoz* rendeli hozzá, előfizetési hatókörön.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>Csoportosítás erőforráshatókörben

Ha szerepkör-hozzárendelést szeretne hozzáadni egy csoporthoz, használja [az az szerepkör-hozzárendelés létrehozása .](/cli/azure/role/assignment#az-role-assignment-create) A csoport objektumazonosítójának beszerzéséről az [Objektumazonosítók beszedése](#get-object-ids)című témakörben talál további információt.

A következő példa hozzárendeli a *Virtuálisgép közreműködői* szerepkört a 222222222-2222-2222-2222222222222222222222-es azonosítóval rendelkező *Ann Mack-csapatcsoporthoz* egy *pharma-sales-project-network*nevű virtuális hálózat erőforráshatókörén.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>Alkalmazás erőforráscsoport hatókörén

Ha szerepkör-hozzárendelést szeretne hozzáadni egy alkalmazáshoz, használja [az az szerepkör-hozzárendelés létrehozása .](/cli/azure/role/assignment#az-role-assignment-create) Az alkalmazás objektumazonosítójának beszerzéséről az [Objektumazonosítók beszedése](#get-object-ids)című témakörben talál további információt.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

A következő példa a 444444444-4444-4444-4444-4444444444444-es objektummal rendelkező alkalmazáshoz rendeli *a* *Virtuálisgép közreműködői* szerepkört.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>Felhasználó az előfizetés hatókörén

Ha egy előfizetési hatókörben szeretne szerepkör-hozzárendelést hozzáadni egy felhasználóhoz, használja [az az szerepkör-hozzárendelés létrehozása .](/cli/azure/role/assignment#az-role-assignment-create) Az előfizetés-azonosító lekért, megtalálja azt az **Azure Portalon** az Előfizetések panelen, vagy [használhatja az az-fióklistát.](/cli/azure/account#az-account-list)

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

A következő példa az *Olvasó* szerepkört rendeli hozzá az *annm\@example.com* felhasználóhoz egy előfizetési hatókörben.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>Felhasználó a felügyeleti csoport hatókörén

Ha egy felhasználóhoz szerepkör-hozzárendelést szeretne hozzáadni egy felügyeleti csoport hatókörén, használja [az az szerepkör-hozzárendelés létrehozása .](/cli/azure/role/assignment#az-role-assignment-create) A felügyeleti csoport azonosítójának lekért, **megtalálhatja** azt az Azure Portalfelügyeleti csoportok panelen, vagy használhatja [az az-fiókfelügyeleti csoport listáját.](/cli/azure/account/management-group#az-account-management-group-list)

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

A következő példa hozzárendeli a *Számlázási olvasó* szerepkört az *\@alain example.com* felhasználóhoz egy felügyeleti csoport hatókörén.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>Új szolgáltatásnév

Ha létrehoz egy új egyszerű szolgáltatást, és azonnal megpróbál szerepkört hozzárendelni az egyszerű szolgáltatáshoz, a szerepkör-hozzárendelés bizonyos esetekben sikertelen lehet. Ha például egy parancsfájl segítségével hoz létre egy új felügyelt identitást, majd megpróbál szerepkört rendelni az egyszerű szolgáltatáshoz, a szerepkör-hozzárendelés sikertelen lehet. A hiba oka valószínűleg replikációs késleltetés. A szolgáltatásnév egy régióban jön létre; azonban a szerepkör-hozzárendelés előfordulhat egy másik régióban, amely még nem replikálta a szolgáltatás egyszerű még. A forgatókönyv megoldásához adja meg a fő típust a szerepkör-hozzárendelés létrehozásakor.

Szerepkör-hozzárendelés hozzáadásához használja [az az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create), adja meg az értéket a hoz, `--assignee-object-id`majd állítsa a érték . `--assignee-principal-type` `ServicePrincipal`

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

A következő példa hozzárendeli a *Virtuálisgép közreműködőszerepkört* az *msi-teszt* által felügyelt identitáshoz a *pharma-sales* erőforráscsoport hatókörén:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Az RBAC-ban a hozzáférés eltávolításához távolítson el egy szerepkör-hozzárendelést [az szerepkör-hozzárendelés törlésével:](/cli/azure/role/assignment#az-role-assignment-delete)

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

A következő példa eltávolítja a *Virtuálisgép közreműködőszerepkör-hozzárendelést* a *patlong\@contoso.com* felhasználótól a *pharma-sales* erőforráscsoportban:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

A következő példa eltávolítja az *Olvasó* szerepkört az *Ann Mack-csapat* csoport 222222222-2222-2222-2222222222222222222222-es azonosítóval egy előfizetési hatókörön. A csoport objektumazonosítójának beszerzéséről az [Objektumazonosítók beszedése](#get-object-ids)című témakörben talál további információt.

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

A következő példa eltávolítja a *Számlázási olvasó* szerepkört az *\@alain example.com* felhasználó a felügyeleti csoport hatókörében. A felügyeleti csoport azonosítójának lekért érdekében használhatja az [fiókkezelő csoport listáját.](/cli/azure/account/management-group#az-account-management-group-list)

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>További lépések

- [Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure CLI használatával](role-assignments-list-cli.md)
- [Az Azure CLI használatával kezelheti az Azure-erőforrásokat és erőforráscsoportokat](../azure-resource-manager/cli-azure-resource-manager.md)
