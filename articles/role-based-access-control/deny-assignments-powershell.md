---
title: Az Azure PowerShell használatával az Azure-erőforrások letagadni idák listájának listája
description: Megtudhatja, hogyan listázhat a felhasználók, csoportok, egyszerű szolgáltatásés felügyelt identitások, amelyek megtagadták a hozzáférést az adott Azure-erőforrás-műveletek adott hatókörök ben az Azure PowerShell használatával.
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
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137401"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Az Azure-erőforrások letagadni idulásának listája az Azure PowerShell használatával

[A hozzárendelések letiltása letiltja](deny-assignments.md) a felhasználókat bizonyos Azure-erőforrás-műveletek végrehajtásában, még akkor is, ha egy szerepkör-hozzárendelés hozzáférést biztosít számukra. Ez a cikk ismerteti, hogyan listázhatja a megtagadási hozzárendelések az Azure PowerShell használatával.

> [!NOTE]
> Nem hozhat létre közvetlenül saját megtagadási hozzárendeléseket. A megtagadási hozzárendelések létrehozásáról a [Hozzárendelések megtagadása](deny-assignments.md)című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

A megtagadási feladattal kapcsolatos információk hozásához a következőkre van szüksége:

- `Microsoft.Authorization/denyAssignments/read`engedély, amely az [Azure-erőforrások](built-in-roles.md) legtöbb beépített szerepkörében megtalálható
- [PowerShell az Azure Cloud Shellben](/azure/cloud-shell/overview) vagy [az Azure PowerShellben](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Megtagadás-hozzárendelések felsorolása

### <a name="list-all-deny-assignments"></a>Az összes megtagadási hozzárendelés listázása

Az aktuális előfizetés összes megtagadási hozzárendelésének listázásához használja a [Get-AzDenyAssignment programot.](/powershell/module/az.resources/get-azdenyassignment)

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Megtagadási hozzárendelések listázása erőforráscsoport hatókörén

Az erőforráscsoport hatókörén lévő összes megtagadási hozzárendelés listázásához használja a [Get-AzDenyAssignment küldetést.](/powershell/module/az.resources/get-azdenyassignment)

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Megtagadási hozzárendelések listázása előfizetési hatókörben

Az összes megtagadási hozzárendelés listázásához használja a [Get-AzDenyAssignment küldetést.](/powershell/module/az.resources/get-azdenyassignment) Az előfizetés-azonosító lekért, megtalálja azt az **Azure Portalon** az Előfizetések panelen, vagy használhatja a [Get-AzSubscription .To get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrások megtagadási hozzárendeléseinek ismertetése](deny-assignments.md)
- [Az Azure-erőforrások letagadni idulásának listája az Azure Portal használatával](deny-assignments-portal.md)
- [Az Azure-erőforrások megtagadási hozzárendeléseinek listája a REST API használatával](deny-assignments-rest.md)