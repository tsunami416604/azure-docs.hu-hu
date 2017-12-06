---
title: "Hozzon létre egyéni szerepkörök az Azure RBAC |} Microsoft Docs"
description: "Megtudhatja, hogyan pontosabb identitáskezeléshez átruházásához hozzáférés-vezérléssel egyéni szerepkörök definiálása az Azure-előfizetésben."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2bb671e1870ae22eb515adc36ce0235e1d8ecddd
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="create-custom-roles-for-azure-role-based-access-control"></a>Hozzon létre egyéni szerepkörök átruházásához hozzáférés-vezérlés
Hozzon létre egy egyéni biztonsági szerepkört a átruházásához hozzáférés-vezérlés (RBAC) Ha az adott hozzáférési igényeinek a beépített szerepkörök egyike. Egyéni szerepkörök segítségével hozhatók létre [Azure PowerShell](role-based-access-control-manage-access-powershell.md), [Azure parancssori felület](role-based-access-control-manage-access-azure-cli.md) (CLI), és a [REST API](role-based-access-control-manage-access-rest.md). Hasonlóan a beépített szerepkörök egyéni szerepkörök hozzárendelése felhasználók, csoportok és alkalmazások előfizetés, erőforráscsoport és erőforrás-hatókörök. Egyéni szerepkörök az Azure AD-bérlő tárolódnak, és előfizetések között megosztható legyen.

Mindegyik bérlő legfeljebb 2000 egyéni szerepköröket hozhatnak létre. 

A következő példa bemutatja egy egyéni biztonsági szerepkört a figyelés és a virtuális gépek újraindításával:

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Műveletek
A **műveletek** egy egyéni biztonsági szerepkört a tulajdonság határozza meg, amelyhez a szerepkör hozzáférést biztosít az Azure műveletek. Művelet karakterláncok, amelyek azonosítják az Azure erőforrás-szolgáltatók biztonságos műveletek gyűjteménye. Művelet karakterláncok kövesse formátuma `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. Helyettesítő karakterek művelet karakterláncokat (\*) hozzáférést biztosíthat a művelet karakterláncnak megfelelő összes művelethez. Például:

* `*/read`biztosít hozzáférést az olvasási műveletek az összes Azure-erőforrás-szolgáltató minden erőforrástípus esetén.
* `Microsoft.Compute/*`engedélyezi a hozzáférést a Microsoft.Compute erőforrás-szolgáltató az összes erőforrástípus összes műveletet.
* `Microsoft.Network/*/read`olvasási műveletek összes erőforrás típusához a Microsoft.Network erőforrás-szolgáltató az Azure biztosít hozzáférést.
* `Microsoft.Compute/virtualMachines/*`minden műveletet a virtuális gépek és a gyermek típusú erőforrások hozzáférést biztosít.
* `Microsoft.Web/sites/restart/Action`a hozzáférési webhely újraindítására.

Használjon `Get-AzureRmProviderOperation` (a PowerShell) vagy `azure provider operations show` (az Azure CLI) az Azure erőforrás-szolgáltatók műveletek. Ezek a parancsok annak ellenőrzéséhez, hogy egy művelet karakterlánc érvényes, és bontsa ki a helyettesítő művelet karakterláncok is használhatja.

```powershell
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![PowerShell képernyőfelvétel - Get-AzureRMProviderOperation](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```azurecli
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Az Azure CLI képernyőfelvétel - azure szolgáltató műveletek megjelenítése "Microsoft.Compute/virtualMachines/ \* /művelet" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Használja a **NotActions** tulajdonság, ha engedélyezni szeretné műveletek készletét könnyebben definiálva tiltott operatív kizárásával. Egyéni szerepkör által biztosított hozzáférést számított kivonja a **NotActions** műveletek a **műveletek** műveletek.

> [!NOTE]
> Ha egy felhasználó tartozik, amely nem tartalmazza egy műveletet a szerepkör **NotActions**, és hozzá van rendelve egy második szerepkör, amely hozzáférést biztosít a műveletet, hogy a felhasználó számára engedélyezett a művelet elvégzéséhez. **NotActions** nincs megtagadási szabály – egyszerűen csak egy kényelmes módot nyújt az engedélyezett műveletek készlet létrehozása, ha az adott műveletek ki lesznek zárva.
>
>

## <a name="assignablescopes"></a>AssignableScopes
A **AssignableScopes** az egyéni szerepkör tulajdonság határozza meg a hatókörök (előfizetések, erőforráscsoport-sablonok vagy az erőforrások) belül, amely az egyéni szerepkör érhető el a hozzárendeléshez. Az egyéni biztonsági szerepkört csak az előfizetések vagy az azt igénylő erőforráscsoportok hozzárendelés elérhetővé teszi, és nem a felhasználói élmény beállítása a előfizetések és az erőforráscsoportok többi megzavarhatják.

Érvényes hozzárendelhető hatókörök például:

* "/ subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/ subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624" - teszi két előfizetések rendelhető hozzá a szerepkört.
* "/ subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e" - hoz egy-egy előfizetéshez rendelhető hozzá a szerepkört.
* "/ előfizetések/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/hálózati" - elérhetővé teszi a szerepkör hozzárendelése csak az a hálózati erőforrás csoportba.

> [!NOTE]
> Segítségével kell legalább egy előfizetést, erőforrás vagy az erőforrás-azonosító.
>
>

## <a name="custom-roles-access-control"></a>Egyéni szerepkörök hozzáférés-vezérlés
A **AssignableScopes** tulajdonsága az egyéni biztonsági szerepkört is vezérli, akik megtekintése, módosítása és törlése a szerepkör.

* Aki hozhat létre egy egyéni biztonsági szerepkört?
    Tulajdonosok (és a felhasználói hozzáférés rendszergazdák) előfizetések, erőforráscsoport-sablonok és erőforrások hozhat létre egyéni szerepkörök használatra ezeket.
    A szerepkör létrehozása a felhasználó végezhet kell `Microsoft.Authorization/roleDefinition/write` minden műveletet a **AssignableScopes** a szerepkör.
* Módosíthatja, akik egy egyéni biztonsági szerepkört?
    Tulajdonosok (és a felhasználói hozzáférés rendszergazdák) előfizetések, erőforráscsoport-sablonok és erőforrások ezeket az egyéni szerepkörök módosíthatja. Felhasználók kell tudni elvégezni a `Microsoft.Authorization/roleDefinition/write` minden műveletet a **AssignableScopes** egy egyéni szerepkör.
* Kik tekinthetik meg egyéni szerepkörök?
    Minden beépített szerepkörök az Azure RBAC kiosztására használható szerepkörtől megtekintésének engedélyezése. Felhasználók, akik hajthat végre a `Microsoft.Authorization/roleDefinition/read` hatókörre művelet megtekintheti az adott hatókörben kiosztására használható RBAC szerepköröket.

## <a name="see-also"></a>Lásd még:
* [Szerepköralapú hozzáférés-vezérlés](role-based-access-control-configure.md): az RBAC első lépései az Azure portálon.
* Elérhető műveletek listájának megtekintéséhez lásd: [Azure Resource Manager erőforrás-szolgáltató műveletek](role-based-access-control-resource-provider-operations.md).
* Útmutató: a hozzáférés kezelése:
  * [PowerShell](role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](role-based-access-control-manage-access-azure-cli.md)
  * [REST API](role-based-access-control-manage-access-rest.md)
* [Beépített szerepkörök](role-based-access-built-in-roles.md): részletes információkat szolgáltatva a szerepköröket, az RBAC szabványos tartalmazza.
