---
title: Módosítása, törlése és a felügyeleti csoportok – Azure Cégirányítási kezelése
description: Útmutató megtekintése, kezelése, frissítése és a felügyeleti csoport hierarchia törlése.
author: rthorn17
ms.service: azure-resource-manager
ms.date: 04/04/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: 58f8b3346ddd250736e09966b7c36e570b62bb10
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057744"
---
# <a name="manage-your-resources-with-management-groups"></a>Az erőforrások kezelése a felügyeleti csoportokkal

Ha a vállalatnak sok előfizetése van, jól jöhet egy módszer, hogy hatékonyan kezelje az előfizetésekhez való hozzáférést, a szabályzatokat és a megfelelőséget. Az Azure Management Groups előfizetések fölötti hatókörszintet biztosít. Az előfizetéseket „felügyeleti csoportok” nevű tárolókba rendezheti, és az irányítási feltételeket alkalmazhatja a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott feltételeket.

A felügyeleti csoportok nagy léptékű, nagyvállalati szintű felügyeletet tesznek lehetővé, függetlenül az előfizetése típusától.  Felügyeleti csoportok kapcsolatos további információkért lásd: [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="change-the-name-of-a-management-group"></a>A felügyeleti csoport nevének módosítása

A felügyeleti csoport nevét a portal, PowerShell vagy az Azure CLI használatával módosíthatja.

### <a name="change-the-name-in-the-portal"></a>Módosítsa a nevet a portálon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. Válassza ki az átnevezni kívánt felügyeleti csoport.

1. Válassza ki **részletek**.

1. Válassza ki a **csoport átnevezése** lehetőséget az oldal tetején.

   ![Csoport átnevezése a beállítás a felügyeleti csoport oldalán](./media/detail_action_small.png)

1. Amikor megnyílik a menüben, adja meg az új nevet, amelyet szeretne megjeleníteni.

   ![Csoport átnevezése panelen felügyeleti csoport átnevezése](./media/rename_context.png)

1. Kattintson a **Mentés** gombra.

### <a name="change-the-name-in-powershell"></a>Módosítsa a nevet a PowerShellben

Frissíteni a megjelenítési név használata **Update-AzManagementGroup**. Például módosíthatja egy felügyeleti csoport megjelenített neve a "Contoso IT" a "Contoso csoport", az alábbi parancsot:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Módosítsa a nevet az Azure CLI-ben

Azure CLI esetén használja az update parancs.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>A felügyeleti csoport törlése

A felügyeleti csoport törléséhez az alábbi követelményeknek kell teljesülniük:

1. Nincsenek alárendelt felügyeleti csoportok vagy előfizetések a felügyeleti csoportban.

   - Egy felügyeleti csoportot ki egy előfizetést áthelyezésével kapcsolatban lásd: [előfizetés áthelyezése egy másik felügyeleti csoportba](#move-subscriptions-in-the-hierarchy).

   - A felügyeleti csoport áthelyezése egy másik felügyeleti csoportba: [felügyeleti csoportok kerül a hierarchia](#move-management-groups-in-the-hierarchy).

1. A felügyeleti csoport ("Owner", "Közreműködő" vagy "Felügyeleti csoport közreműködői") van is írási engedélye. Milyen engedélyek megtekintéséhez, rendelkezik, válassza ki a felügyeleti csoportot, majd **IAM**. RBAC-szerepkörökkel kapcsolatos további tudnivalókért lásd: [hozzáférést és engedélyeket az RBAC kezelése](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Törölje a portálon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. Válassza ki a törölni kívánt felügyeleti csoport.

1. Válassza ki **részletek**.

1. Válassza ki **törlése**

    > [!TIP]
    > Ha az ikon le van tiltva, az egér választó egérmutatót az ikon bemutatja, okát.

   ![Csoport beállítása törlése](./media/delete.png)

1. Nincs egy ablak, amely megerősíti, hogy törli a felügyeleti csoport megnyílik.

   ![Megerősítés ablakban csoport törlése](./media/delete_confirm.png)

1. Válassza ki **Igen**.

### <a name="delete-in-powershell"></a>A PowerShellben törlése

Használja a **Remove-AzManagementGroup** lévő felügyeleti csoportok törlése PowerShell parancsot.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Törlés az Azure CLI felületen

Az Azure CLI-vel a parancs az fiók felügyeleticsoport törlési használja.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Felügyeleti csoportok megtekintése

Közvetlen vagy az örökölt RBAC szerepkör rendelkezik a felügyeleti csoporthoz is megtekintheti.  

### <a name="view-in-the-portal"></a>Megtekintése a portálon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. A felügyeleti csoport hierarchia oldal betöltődik. Ez a lap, ahol áttekintheti az összes felügyeleti csoport és az előfizetések fér hozzá. A csoport nevének kijelölésekor megnyílik a hierarchia egy szinttel lejjebb. A navigációs ugyanúgy működik, mint a Fájlkezelőben is.

1. A felügyeleti csoport a részletek megtekintéséhez válasszon a **(részletek)** hivatkozást a felügyeleti csoport címe mellett. Ez a hivatkozás nem érhető el, ha nincs a felügyeleti csoport megtekintéséhez szükséges engedélyekkel.

   ![Elsődleges](./media/main.png)

### <a name="view-in-powershell"></a>Megtekintése a PowerShellben

A Get-AzManagementGroup paranccsal az összes csoport beolvasása.  Lásd: [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) modulok teljes listája megtalálható a felügyeleti csoport első Powershell-parancsokat.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Egyetlen felügyeleti csoport információt használja a - GroupName paramétert

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Egy adott felügyeleti csoport és az alatta a hierarchia minden szintet használja **-bontsa ki a** és **-Recurse** paramétereket.  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Az Azure CLI-ben megtekintése

A lista paranccsal az összes csoport beolvasása.  

```azurecli-interactive
az account management-group list
```

Egyetlen felügyeleti csoport információkért kövesse a bemutatott parancsot

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Egy adott felügyeleti csoport és az alatta a hierarchia minden szintet használja **-bontsa ki a** és **-Recurse** paramétereket.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Előfizetések kerül a hierarchia

Hozzon létre egy felügyeleti csoportot akkor előfizetések kötegeléséhez együtt. Csak a felügyeleti csoportok és az előfizetések lehet tenni egy másik felügyeleti csoportba gyermekei. Olyan előfizetést, amelyet a felügyeleti csoport áthelyezése a fölérendelt felügyeleti csoport összes felhasználói hozzáférés és a szabályzatok örökli.

Helyezze át az előfizetést, a következő RBAC-engedélyek mindegyikének birtokában kell teljesülniük:

- A gyermek előfizetés "Owner" szerepkört.
- A cél szülő felügyeleti group.* a "Tulajdonos", "Közreműködő" vagy "Felügyeleti csoport közreműködő" szerepkör
- A meglévő szülő felügyeleti group.* a "Tulajdonos", "Közreműködő" vagy "Felügyeleti csoport közreműködő" szerepkör

*: Kivéve, ha a cél- vagy a meglévő szülő felügyeleti csoport gyökérszintű felügyeleti csoport. Mivel a legfelső szintű felügyeleti csoport alapértelmezés szerint az összes felügyeleti csoportokat és az előfizetések helyszíni üzenetsorokra, a felhasználóknak nincs szükségük engedélyekre, az elem áthelyezése.

Milyen engedélyekkel rendelkezik az Azure Portalon, válassza a felügyeleti csoportot, majd válasszon **IAM**. RBAC-szerepkörökkel kapcsolatos további tudnivalókért lásd: [hozzáférést és engedélyeket az RBAC kezelése](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Helyezze át az előfizetést a portálon

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Meglévő előfizetés hozzáadása egy felügyeleti csoporthoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. Válassza ki a felügyeleti csoport fölérendelt szeretné.

1. A lap tetején válassza **előfizetés hozzáadása**.

1. Válassza ki az előfizetést a listában a helyes azonosítóval.

   ![A felügyeleti csoport hozzáadása a rendelkezésre álló előfizetések](./media/add_context_sub.png)

1. Válassza ki "a Mentés".

#### <a name="remove-a-subscription-from-a-management-group"></a>Előfizetés eltávolítása a felügyeleti csoport

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. Válassza ki a felügyeleti csoport, amely az aktuális szülő tervezi.  

1. Válassza ki az előfizetés a sor végén található három pontra szeretné helyezni a listában.

   ![Helyezze át a kapcsolót a felügyeleti csoport](./media/move_small.png)

1. Válassza ki **áthelyezése**.

1. A megnyíló menüben válassza a **fölérendelt felügyeleti csoport**.

   ![Helyezze át a szülő-csoport panel](./media/move_small_context.png)

1. Kattintson a **Mentés** gombra.

### <a name="move-subscriptions-in-powershell"></a>Helyezze át az előfizetések a PowerShellben

Helyezze át az előfizetést a PowerShell, a New-AzManagementGroupSubscription parancsot használhatja.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Közötti kapcsolat eltávolításához és az előfizetés és a felügyeleti csoport használja a Remove-AzManagementGroupSubscription parancsot.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Előfizetések áthelyezése az Azure CLI-ben

Szeretné áthelyezni egy előfizetés parancssori felületen, használja a Hozzáadás parancsot.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

A felügyeleti csoportból az előfizetés eltávolításához használja az előfizetés Eltávolítás parancsot.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Felügyeleti csoportok kerül a hierarchia  

Ha áthelyezi a fölérendelt felügyeleti csoport, a hierarchia adott csoportban követi.

### <a name="move-management-groups-in-the-portal"></a>Helyezze át a felügyeleti csoportok a portálon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. Válassza ki a felügyeleti csoport fölérendelt szeretné.

1. A lap tetején válassza **felügyeleti csoport hozzáadása**.

1. A megnyíló menüben válassza, ha azt szeretné egy új, vagy egy meglévő felügyeleti csoportot.

   - Új kiválasztásával létrehoz egy új felügyeleti csoport.
   - Válassza egy meglévő megjelennek egy legördülő áthelyezheti a felügyeleti csoport összes felügyeleti csoportot.  

   ![Új vagy meglévő csoport a felügyeleti csoport áthelyezése](./media/add_context_MG.png)

1. Kattintson a **Mentés** gombra.

### <a name="move-management-groups-in-powershell"></a>Helyezze át a felügyeleti csoportok a PowerShellben

Használja az Update-AzManagementGroup parancsot a PowerShellben áthelyezése egy másik csoportban a felügyeleti csoport.

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'Contoso' -ParentId '/providers/Microsoft.Management/managementGroups/ContosoIT'
```  

### <a name="move-management-groups-in-azure-cli"></a>Felügyeleti csoportok áthelyezése az Azure CLI-ben

A frissítés paranccsal helyezze át a felügyeleti csoport az Azure CLI használatával.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent-id '/providers/Microsoft.Management/managementGroups/ContosoIT'
```

## <a name="audit-management-groups-using-activity-logs"></a>Felügyeleti csoportok naplózása tevékenységnaplókkal

A felügyeleti csoportok támogatottak az [Azure-tevékenységnaplóban](../../azure-monitor/platform/activity-logs-overview.md). Lekérdezheti, ha egy felügyeleti csoporthoz, egyéb Azure-erőforrások központi ugyanazon a helyen lévő összes eseményeket.  Például megtekintheti egy adott felügyeleti csoporthoz tartozó összes szerepkör-hozzárendelés vagy szabályzat-hozzárendelés módosításait.

![Tevékenységnaplók felügyeleti csoportokkal](media/al-mg.png)

Az Azure Portalon kívüli felügyeleti csoportok lekérdezésekor a felügyeleti csoportok célhatóköre a következőhöz hasonlóan néz ki: **"/ providers/Microsoft.Management/managementGroups/{yourMgID}"**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Felügyeleti csoportok hivatkozó más erőforrás-szolgáltatók

Felügyeleti csoportok hivatkozó más erőforrás-szolgáltató műveleteket, ha használja a következő elérési út hatókörként. Az elérési út akkor használatos, ha a PowerShell, az Azure CLI és REST API-k használatával.  

>"/ providers/Microsoft.Management/managementGroups/{yourMgID}"

Példa az elérési út akkor, ha egy új szerepkör-hozzárendelés hozzárendelése a PowerShellben a felügyeleti csoport

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Az azonos hatókör elérési utat használja, a felügyeleti csoport egy szabályzat-definíció beolvasásakor.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>További lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Az Azure-erőforrások rendszerezése felügyeleti csoportok létrehozása](create.md)
- [Módosítása, törlése és a felügyeleti csoportok kezelése](manage.md)
- [Tekintse át a felügyeleti csoportok az Azure PowerShell modul](/powershell/module/az.resources#resources)
- [Tekintse át a felügyeleti csoportok REST API-ban](/rest/api/resources/managementgroups)
- [Tekintse át a felügyeleti csoportok az Azure CLI-vel](/cli/azure/account/management-group)