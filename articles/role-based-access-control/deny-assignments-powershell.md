---
title: Listában elutasítása hozzárendelések Azure-erőforrások Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogy a felhasználók, csoportok, a szolgáltatásnevek és felügyelt identitások, amelyek az adott Azure-erőforrás műveleteket hajthat végre, az Azure PowerShell-lel adott hatókörök hozzáférése listázása.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c1ea26fdb4d60262f89ea6ab0f87220a08c01e68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110484"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Listában elutasítása hozzárendelések Azure-erőforrások Azure PowerShell-lel

[Hozzárendelések megtagadása](deny-assignments.md) meggátolja a felhasználókat adott Azure-erőforrás műveleteket végrehajtani, akkor is, ha a szerepkör-hozzárendelés hozzáférést biztosít számukra. Ez a cikk azt ismerteti, hogyan kell felsorolni megtagadása hozzárendelések Azure PowerShell-lel.

> [!NOTE]
> Nem közvetlenül hozhat létre saját hozzárendelések elutasítása. Információ a nem engedélyezi a hozzárendelések jönnek létre, lásd: [hozzárendelések megtagadása](deny-assignments.md).

## <a name="prerequisites"></a>Előfeltételek

Egy megtagadási hozzárendelés adatainak beolvasása, kell rendelkeznie:

- `Microsoft.Authorization/denyAssignments/read` engedéllyel, amely tartalmazza a legtöbb [beépített szerepkörök az Azure-erőforrásokhoz](built-in-roles.md)
- [PowerShell az Azure Cloud Shellben](/azure/cloud-shell/overview) vagy [Azure PowerShell-lel](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Listában elutasítása hozzárendelések

### <a name="list-all-deny-assignments"></a>A lista az összes elutasítása hozzárendelések

Listázásához-hozzárendelését az aktuális előfizetésben, használja az összes elutasítása [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Listában elutasítása erőforrás hatókört-hozzárendelést

Listázásához hozzárendelések erőforrás csoport hatókörre, használja az összes elutasítása [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Listában elutasítása egy előfizetési hatókört-hozzárendelést

Listázásához hozzárendelés előfizetést hatókörre, használja az összes elutasítása [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Az előfizetés-azonosító lekéréséhez, megtalálhatja a **előfizetések** panel az Azure Portalon, vagy Ön használhatja [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>További lépések

- [Megismerheti az Azure-erőforrások hozzárendelések megtagadása](deny-assignments.md)
- [Listában elutasítása hozzárendelések Azure-erőforrások az Azure portal használatával](deny-assignments-portal.md)
- [Listában elutasítása a REST API használatával az Azure erőforrás-hozzárendelések](deny-assignments-rest.md)