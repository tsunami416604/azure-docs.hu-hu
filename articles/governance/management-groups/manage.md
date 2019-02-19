---
title: Módosítása, törlése és az Azure-ban – Azure Cégirányítási felügyeleti csoportok kezelése
description: Útmutató megtekintése, kezelése, frissítése és a felügyeleti csoport hierarchia törlése.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: dbfb6ecb9f29a82a8871922982a64dbefc338969
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342582"
---
# <a name="manage-your-resources-with-management-groups"></a>Az erőforrások kezelése a felügyeleti csoportokkal

Felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférési házirend és megfelelőség több előfizetést is. Módosítása, törlése, és ezek a tárolók szeretné, hogy használható-hierarchia kezelése [Azure Policy](../policy/overview.md) és [Azure szerepköralapú hozzáférés vezérlők (RBAC)](../../role-based-access-control/overview.md). Felügyeleti csoportok kapcsolatos további információkért lásd: [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](overview.md).

Módosít egy felügyeleti csoportot, egy tulajdonosi vagy közreműködői szerepkörhöz kell rendelkeznie a felügyeleti csoportban. Milyen engedélyek megtekintéséhez, rendelkezik, válassza ki a felügyeleti csoportot, majd **IAM**. RBAC-szerepkörök kapcsolatos további információkért lásd: [hozzáférést és engedélyeket az RBAC kezelése](../../role-based-access-control/overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>A felügyeleti csoport nevének módosítása

A felügyeleti csoport nevét a portal, PowerShell vagy az Azure CLI használatával módosíthatja.

### <a name="change-the-name-in-the-portal"></a>Módosítsa a nevet a portálon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. Válassza ki az átnevezni kívánt felügyeleti csoport.

1. Válassza ki a **csoport átnevezése** lehetőséget az oldal tetején.

   ![Csoport átnevezése lehetőség](./media/detail_action_small.png)

1. Amikor megnyílik a menüben, adja meg az új nevet, amelyet szeretne megjeleníteni.

   ![Csoport átnevezése panel](./media/rename_context.png)

1. Kattintson a **Mentés** gombra.

### <a name="change-the-name-in-powershell"></a>Módosítsa a nevet a PowerShellben

Frissíteni a megjelenítési név használata **Update-AzureRmManagementGroup**. Például ha módosítani szeretné egy felügyeleti csoportok nevét, a "Contoso IT" a "Contoso csoport", akkor futtassa a következő parancsot:

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Módosítsa a nevet az Azure CLI-ben

Azure CLI esetén használja az update parancs.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>A felügyeleti csoport törlése

A felügyeleti csoport törléséhez az alábbi követelményeknek kell teljesülniük:

1. Nincsenek alárendelt felügyeleti csoportok vagy előfizetések a felügyeleti csoportban.

   - Egy felügyeleti csoportot ki egy előfizetést áthelyezésével kapcsolatban lásd: [előfizetés áthelyezése egy másik felügyeleti csoportba](#Move-subscriptions-in-the-hierarchy).

   - A felügyeleti csoport áthelyezése egy másik felügyeleti csoportba: [felügyeleti csoportok kerül a hierarchia](#Move-management-groups-in-the-hierarchy).

1. A felügyeleti csoport tulajdonosi vagy közreműködői szerepkört a felügyeleti csoport írási engedélyei vannak. Milyen engedélyek megtekintéséhez, rendelkezik, válassza ki a felügyeleti csoportot, majd **IAM**. RBAC-szerepkörökkel kapcsolatos további tudnivalókért lásd: [hozzáférést és engedélyeket az RBAC kezelése](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Törölje a portálon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. Válassza ki a törölni kívánt felügyeleti csoport.

1. Válassza ki **törlése**

   - Ha az ikon le van tiltva, az egér választó egérmutatót az ikon bemutatja, okát.

   ![Csoport beállítása törlése](./media/delete.png)

1. Nincs egy ablak, amely megerősíti, hogy törli a felügyeleti csoport megnyílik.

   ![Megerősítés ablakban csoport törlése](./media/delete_confirm.png)

1. Válassza ki **Igen**.

### <a name="delete-in-powershell"></a>A PowerShellben törlése

Használja a **Remove-AzureRmManagementGroup** lévő felügyeleti csoportok törlése PowerShell parancsot.

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName 'Contoso'
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

1. A felügyeleti csoport hierarchia terheléseket, ahol megismerheti a felügyeleti csoportok és hozzáfér az előfizetések lapon. A csoport nevének kijelölésekor megnyílik a hierarchia egy szinttel lejjebb. A navigációs ugyanúgy működik, mint a Fájlkezelőben is.

   ![Elsődleges](./media/main.png)

1. A felügyeleti csoport a részletek megtekintéséhez válasszon a **(részletek)** hivatkozást a felügyeleti csoport címe mellett. Ez a hivatkozás nem érhető el, ha nincs a felügyeleti csoport megtekintéséhez szükséges engedélyekkel.  

### <a name="view-in-powershell"></a>Megtekintése a PowerShellben

A Get-AzureRmManagementGroup paranccsal az összes csoport beolvasása.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```

Egyetlen felügyeleti csoport információt használja a - GroupName paramétert

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName 'Contoso'
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

## <a name="move-subscriptions-in-the-hierarchy"></a>Előfizetések kerül a hierarchia

Hozzon létre egy felügyeleti csoportot akkor előfizetések kötegeléséhez együtt. Csak a felügyeleti csoportok és az előfizetések lehet tenni egy másik felügyeleti csoportba gyermekei. Olyan előfizetést, amelyet a felügyeleti csoport áthelyezése a fölérendelt felügyeleti csoport összes felhasználói hozzáférés és a szabályzatok örökli.

Helyezze át az előfizetést, van néhány engedélyekkel kell rendelkeznie:

- A gyermek előfizetés "Owner" szerepkört.
- Az új fölérendelt felügyeleti csoport "Owner" vagy "Közreműködő" szerepkör.
- A régi felügyeleti szülőcsoport "Owner" vagy "Közreműködő" szerepkör.

Milyen engedélyek megtekintéséhez, rendelkezik, válassza ki a felügyeleti csoportot, majd **IAM**. RBAC-szerepkörökkel kapcsolatos további tudnivalókért lásd: [hozzáférést és engedélyeket az RBAC kezelése](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Helyezze át az előfizetést a portálon

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Meglévő előfizetés hozzáadása egy felügyeleti csoporthoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. Válassza ki a felügyeleti csoport fölérendelt szeretné.

1. A lap tetején válassza **előfizetés hozzáadása**.

1. Válassza ki az előfizetést a listában a helyes azonosítóval.

   ![Gyermekek](./media/add_context_sub.png)

1. Válassza ki "a Mentés".

#### <a name="remove-a-subscription-from-a-management-group"></a>Előfizetés eltávolítása a felügyeleti csoport

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. Válassza ki a felügyeleti csoport, amely az aktuális szülő tervezi.  

1. Válassza ki az előfizetés a sor végén található három pontra szeretné helyezni a listában.

   ![Elem áthelyezése](./media/move_small.png)

1. Válassza ki **áthelyezése**.

1. A megnyíló menüben válassza a **fölérendelt felügyeleti csoport**.

   ![Ablak áthelyezése](./media/move_small_context.png)

1. Kattintson a **Mentés** gombra.

### <a name="move-subscriptions-in-powershell"></a>Helyezze át az előfizetések a PowerShellben

Helyezze át az előfizetést a PowerShell, a New-AzureRmManagementGroupSubscription parancsot használhatja.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Közötti kapcsolat eltávolításához és az előfizetés és a felügyeleti csoport használja a Remove-AzureRmManagementGroupSubscription parancsot.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
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

Ha áthelyezi a fölérendelt felügyeleti csoport összes gyermekerőforrás, amely tartalmazza a felügyeleti csoportokhoz, előfizetések, erőforráscsoportok és a szülő erőforrások áthelyezése.

### <a name="move-management-groups-in-the-portal"></a>Helyezze át a felügyeleti csoportok a portálon

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **minden szolgáltatás** > **felügyeleti csoportok**.

1. Válassza ki a felügyeleti csoport fölérendelt szeretné.

1. A lap tetején válassza **felügyeleti csoport hozzáadása**.

1. A megnyíló menüben válassza, ha azt szeretné egy új, vagy egy meglévő felügyeleti csoportot.

   - Új kiválasztásával létrehoz egy új felügyeleti csoport.
   - Válassza egy meglévő megjelennek egy legördülő áthelyezheti a felügyeleti csoport összes felügyeleti csoportot.  

   ![Áthelyezés](./media/add_context_MG.png)

1. Kattintson a **Mentés** gombra.

### <a name="move-management-groups-in-powershell"></a>Helyezze át a felügyeleti csoportok a PowerShellben

Használja az Update-AzureRmManagementGroup parancsot a PowerShellben áthelyezése egy másik csoportban a felügyeleti csoport.

```azurepowershell-interactive
Update-AzureRmManagementGroup -GroupName 'Contoso' -ParentName 'ContosoIT'
```  

### <a name="move-management-groups-in-azure-cli"></a>Felügyeleti csoportok áthelyezése az Azure CLI-ben

A frissítés paranccsal helyezze át a felügyeleti csoport az Azure CLI használatával.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent 'Contoso Tenant'
```

## <a name="audit-management-groups-using-activity-logs"></a>Felügyeleti csoportok naplózása tevékenységnaplókkal

Ha ezzel az API-val szeretné nyomon követni a felügyeleti csoportokat, használja a [Tenant Activity Log API-t](/rest/api/monitor/tenantactivitylogs). A felügyeleti csoportok tevékenységének nyomon követéséhez jelenleg nem használható a PowerShell, a parancssori felület vagy az Azure Portal.

1. Az Azure AD-bérlő bérlői rendszergazdájaként [emelheti a jogosultságszintet](../../role-based-access-control/elevate-access-global-admin.md), majd Olvasó szerepkört rendelhet a naplózást végző felhasználóhoz a `/providers/microsoft.insights/eventtypes/management` hatókörben.
1. Naplózási felhasználóként meghívhatja a [Tenant Activity Log API-t](/rest/api/monitor/tenantactivitylogs) a felügyeleti csoportok tevékenységének megtekintésére. Minden felügyeleticsoport-tevékenységnél érdemes a **Microsoft.Management** erőforrás-szolgáltató szerint szűrni.  Példa:

```xml
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Management'"
```

> [!NOTE]
> Az API-t kényelmesen meghívhatja a parancssorból az [ARMClient](https://github.com/projectkudu/ARMClient) segítségével.

## <a name="next-steps"></a>További lépések

A felügyeleti csoportokkal kapcsolatos további tudnivalókért lásd:

- [Felügyeleti csoportok létrehozása az Azure-erőforrások rendszerezéséhez](create.md)
- [Felügyeleti csoportok módosítása, törlése és kezelése](manage.md)
- [Felügyeleti csoportok áttekintése az Azure PowerShell Erőforrások moduljában](https://aka.ms/mgPSdocs)
- [Felügyeleti csoportok áttekintése a REST API-ban](https://aka.ms/mgAPIdocs)
- [Felügyeleti csoportok áttekintése az Azure CLI-ben](https://aka.ms/mgclidoc)
