---
title: "Az Azure CLI szerepköralapú hozzáférés-vezérlés (RBAC) kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a szerepköralapú hozzáférés-vezérlést (RBAC) az Azure parancssori felületével, szerepkörök és a szerepkör műveletek listázása és szerepkörök hozzárendelése az előfizetés és az alkalmazás hatókörhöz."
services: active-directory
documentationcenter: 
author: andredm7
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: b99264eb69f115db6e334b6aceae6ed897202d56
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Szerepköralapú hozzáférés-vezérlés az Azure parancssori felületével kezelése
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
> * [REST API](role-based-access-control-manage-access-rest.md)


Szerepköralapú hozzáférés-vezérlést (RBAC) az Azure portálon, és az Azure Resource Manager API segítségével kezelheti az előfizetést és a minden részletre kiterjedő szinten erőforrásokhoz való hozzáférését. Ez a szolgáltatás egyes szerepkörök hozzárendelése el egy adott hatókörhöz szerint engedélyezheti a hozzáférést az Active Directory felhasználók, csoportok vagy szolgáltatásnevekről.

Az Azure parancssori felület (CLI) kezelheti az RBAC használata előtt rendelkeznie kell a következő előfeltételek teljesülését:

* Az Azure CLI 0.8.8 verzió vagy újabb. Telepítse a legújabb verziót, és társítsa azt az Azure-előfizetése, [telepítése és konfigurálása az Azure parancssori felület](../cli-install-nodejs.md).
* Az Azure Resource Manager az Azure parancssori felület. Ugrás a [az Azure parancssori felület használatával az erőforrás-kezelővel](../xplat-cli-azure-resource-manager.md) további részleteket.

## <a name="list-roles"></a>Lista szerepkörök
### <a name="list-all-available-roles"></a>Elérhető szerepkörök felsorolása
Az elérhető szerepkörök listájában, használja:

        azure role list

A következő példa bemutatja a listája *elérhető szerepkörök*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![RBAC Azure - azure szerepkör lista - parancssori képernyőképe](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### <a name="list-actions-of-a-role"></a>Egy szerepkör lista műveletek
A műveletek a szerepkörök listájában, használja:

    azure role show "<role name>"

A következő példa bemutatja a műveleteket a *közreműködő* és *virtuális gép közreműködő* szerepkörök.

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![Szerepalapú Azure parancssori - azure szerepkör megjelenítése – képernyőkép](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

## <a name="list-access"></a>A hozzáférési lista
### <a name="list-role-assignments-effective-on-a-resource-group"></a>Lista szerepkör-hozzárendelések lép az erőforráscsoport
A szerepkör-hozzárendelések meglévő erőforráscsoport listájában, használja:

    azure role assignment list --resource-group <resource group name>

A következő példa bemutatja a szerepkör-hozzárendelések a *pharma-értékesítési-projecforcast* csoport.

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Szerepalapú Azure parancssori - társításának listája azure szerepkör csoport – képernyőkép](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### <a name="list-role-assignments-for-a-user"></a>Szerepkör-hozzárendelések listáját egy felhasználó számára
A szerepkör-hozzárendelések egy adott felhasználó és felhasználói csoportok hozzárendelése listájában, használja:

    azure role assignment list --signInName <user email>

A parancs módosításával csoportok örökölt szerepkör-hozzárendelések is látható:

    azure role assignment list --expandPrincipalGroups --signInName <user email>

A következő példa bemutatja a szerepkör-hozzárendeléseket, kapott a  *sameert@aaddemo.com*  felhasználó. Ez magában foglalja, amely közvetlenül a felhasználóhoz rendelt szerepkörök és csoportok öröklődtek szerepkörtől.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![RBAC Azure - hozzárendelés lista azure szerepkör felhasználó - parancssori képernyőképe](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

## <a name="grant-access"></a>Hozzáférés biztosítása
Miután azonosította a hozzárendelni kívánt szerepkör hozzáférés biztosításához használja:

    azure role assignment create

### <a name="assign-a-role-to-group-at-the-subscription-scope"></a>Az előfizetési hatókört a csoport szerepkör hozzárendelése
Rendelhet hozzá egy szerepkört az előfizetés hatókörben egy csoportot, használja:

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

Az alábbi példa a *olvasó* szerepkör *Christine Koch Team* , a *előfizetés* hatókör.

![Szerepalapú Azure parancssori - azure szerepkör-hozzárendelés létrehozása csoport – képernyőkép](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Az alkalmazást az előfizetési hatókört szerepkör hozzárendelése
Szerepkör hozzárendelése az előfizetési hatókört az alkalmazást, használja:

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

A következő példa engedélyezi a *közreműködő* szerepkört egy *az Azure AD* alkalmazást a kijelölt előfizetésben.

 ![Szerepalapú Azure parancssori - alkalmazás azure szerepkör-hozzárendelés létrehozása](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>A szerepkör hozzárendelése egy felhasználóhoz a erőforrás csoport hatóköre:
A szerepkör hozzárendelése egy felhasználóhoz a erőforrás csoport hatókörben, használja:

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

A következő példa engedélyezi a *virtuális gép közreműködő* szerepkör  *samert@aaddemo.com*  felhasználójának a *Pharma-értékesítési-ProjectForcast* erőforrás csoport hatóköre.

![Az RBAC Azure parancssori - azure szerepkör-hozzárendelés létrehozása felhasználó – képernyőkép](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>A szerepkör hozzárendelése a erőforrás hatókörben csoporthoz
A szerepkör hozzárendelése egy csoporthoz az erőforrás-hatókörben, használja:

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

A következő példa engedélyezi a *virtuális gép közreműködő* szerepkört egy *az Azure AD* csoporthoz egy *alhálózati*.

![Szerepalapú Azure parancssori - azure szerepkör-hozzárendelés létrehozása csoport – képernyőkép](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

## <a name="remove-access"></a>Megszünteti a hozzáférést
Szerepkör-hozzárendelés eltávolításához használja:

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

A következő példában eltávolítjuk a *virtuális gép közreműködő* a szerepkör-hozzárendelés a  *sammert@aaddemo.com*  felhasználó számára a *Pharma-értékesítési-ProjectForcast* erőforrás a csoport.
A példa a szerepkör-hozzárendelés majd eltávolítja az előfizetésben a csoportból.

![RBAC Azure - azure szerepkör-hozzárendelés törlése - parancssori képernyőképe](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása
Segítségével hozhat létre egy egyéni biztonsági szerepkört:

    azure role create --inputfile <file path>

Az alábbi példa létrehoz egy egyéni biztonsági szerepkört nevű *virtuális gépet üzemeltető*. Az egyéni szerepkör hozzáférést biztosít az összes olvasási műveletek a *Microsoft.Compute*, *Microsoft.Storage*, és *Microsoft.Network* erőforrás-szolgáltatók és engedélyezi a hozzáférést Indítsa el, és indítsa újra a virtuális gépek figyelése. Az egyéni szerepkör két előfizetések használható. A példa egy JSON-fájl bemenetként.

![JSON - egyéni szerepkör-definíció – képernyőkép](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Szerepalapú Azure parancssori - azure szerepkör létrehozása – képernyőfelvétel](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## <a name="modify-a-custom-role"></a>Egyéni szerepkör módosítása
Ha módosít egy egyéni biztonsági szerepkört, először az a `azure role list` parancsot a szerepkör-definíció lekéréséhez. A szerepkör-definíciós fájlt, végezze el a szükséges módosításokat. Végül `azure role set` menteni a módosított szerepkör-definíció.

    azure role set --inputfile <file path>

A következő példakóddal felveheti a *Microsoft.Insights/diagnosticSettings/* művelet a **műveletek**, és az Azure-előfizetés a **AssignableScopes** , a Virtuális gép operátor egyéni biztonsági szerepkört.

![JSON - módosítása egyéni szerepkör-definíció – képernyőkép](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![RBAC Azure - azure szerepkör set - parancssori képernyőképe](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## <a name="delete-a-custom-role"></a>Egyéni szerepkör törléséhez
Egyéni szerepkör törléséhez először használja a `azure role list` parancsot annak meghatározásához, a **azonosító** a szerepkör. Ezt követően a `azure role delete` parancs megadásával a szerepkör törlése a **azonosító**.

A következő példában eltávolítjuk a *virtuális gépet üzemeltető* egyéni biztonsági szerepkört.

![Szerepalapú Azure parancssori - azure szerepkör törlésének – képernyőkép](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## <a name="list-custom-roles"></a>Egyéni szerepkörök listája
A szerepkörök, amelyek rendelhető hozzá hatókör kilistázhatja a `azure role list` parancsot.

A következő parancs megjeleníti az összes szerepkör, amely a kijelölt előfizetés kiosztására használható.

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![RBAC Azure - azure szerepkör lista - parancssori képernyőképe](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

A következő példában a *virtuális gépet üzemeltető* egyéni szerepkör nem érhető el a *Production4* előfizetés, mert az adott előfizetéshez nem szerepel a **AssignableScopes** a szerepkör.

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![RBAC Azure - egyéni szerepkörök listája azure szerepkör - parancssori képernyőképe](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)

## <a name="next-steps"></a>További lépések
[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

