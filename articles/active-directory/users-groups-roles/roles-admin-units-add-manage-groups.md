---
title: Csoportok hozzáadása, eltávolítása és listázása egy felügyeleti egységben – Azure Active Directory | Microsoft Docs
description: Csoportok és szerepkörük engedélyeinek kezelése egy felügyeleti egységben Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 324d0ff0659270c6f2e90c0456ded83344f73936
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91265013"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Csoportok hozzáadása és kezelése felügyeleti egységekben Azure Active Directory

A Azure Active Directory (Azure AD) szolgáltatásban hozzáadhat csoportokat egy felügyeleti egységhez (AU) az irányítás részletesebb felügyeleti hatóköréhez.

A PowerShell és a Microsoft Graph felügyeleti egység felügyeletéhez való előkészítésének lépéseiért lásd: első [lépések](roles-admin-units-manage.md#get-started).

## <a name="add-groups-to-an-au"></a>Csoportok hozzáadása AU-hoz

### <a name="azure-portal"></a>Azure Portal

A csoportokat csak egyenként rendelheti hozzá egy felügyeleti egységhez. A csoportok tömeges kiosztása nem lehetséges egy felügyeleti egységben. A portálon a következő két módszer egyikével rendelhet hozzá egy csoportot egy felügyeleti egységhez:

1. Az **Azure AD > csoportok** lapról

    Nyissa meg a csoportok áttekintése lapot az Azure AD-ben, és válassza ki azt a csoportot, amelyet hozzá kell rendelni a felügyeleti egységhez. A bal oldalon válassza ki a **felügyeleti egységek** elemet azon felügyeleti egységek listázásához, amelyekre a csoport hozzá van rendelve. A felső részen megtalálja a felügyeleti egységhez való hozzárendelés lehetőséget, majd a gombra kattintva a felügyeleti egységet kiválaszthatja a jobb oldali panelen.

    ![Csoport társítása egyenként egy felügyeleti egységhez](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Az **Azure AD > felügyeleti egységek > minden csoport** oldalon

    Nyissa meg a minden csoport panelt az Azure AD > felügyeleti egységekben. Ha vannak olyan csoportok, amelyek már hozzá vannak rendelve a felügyeleti egységhez, akkor a jobb oldalon jelennek meg. A felső és a jobb oldali panelen válassza a **Hozzáadás** lehetőséget, majd az Azure ad-szervezetben elérhető csoportok listázásával. Válasszon ki egy vagy több olyan csoportot, amelyet hozzá szeretne rendelni a felügyeleti egységekhez.

    ![Válasszon ki egy felügyeleti egységet, majd válassza a tag hozzáadása elemet.](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

Ebben a példában az Add-AzureADAdministrativeUnitMember parancsmag használatával lehet hozzáadni a csoportot a felügyeleti egységhez. A felügyeleti egység és a hozzáadni kívánt csoport objektumazonosító-azonosítója argumentumként történik. A Kiemelt szakasz szükség szerint módosítható az adott környezetben.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
}
```

Példa:

```http
{
"@odata.id":"https://graph.microsoft.com/beta/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="list-groups-in-an-au"></a>AU-beli csoportok listázása

### <a name="azure-portal"></a>Azure Portal

Nyissa meg az **Azure AD > felügyeleti egységeket** a portálon. Válassza ki azt a felügyeleti egységet, amelynek a felhasználóit szeretné listázni. Alapértelmezés szerint az **összes felhasználó** már a bal oldali panelen van kiválasztva. Válassza az **összes csoport** lehetőséget, és a jobb oldalon megtalálja azon csoportok listáját, amelyek a kiválasztott felügyeleti egység tagjai.

![Felügyeleti egységben lévő csoportok listázása](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

Ez segítséget nyújt a felügyeleti egység összes tagjának beszerzéséhez. Ha meg szeretné jeleníteni az összes olyan csoportot, amely tagja a felügyeleti egységnek, használhatja az alábbi kódrészletet:

```http
foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```
### <a name="microsoft-graph"></a>Microsoft Graph

```http
HTTP request
GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="list-aus-for-a-group"></a>Csoporthoz tartozó AUs listázása

### <a name="azure-portal"></a>Azure Portal

Az Azure AD-portálon **csoportok**megnyitásával nyithatja meg a csoport adatait. Válasszon ki egy csoportot a csoport profiljának megnyitásához. Válassza a **felügyeleti egységek** lehetőséget az összes olyan felügyeleti egység listázásához, ahol a csoport tagja.

![Csoport felügyeleti egységeinek listázása](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-au"></a>Csoport eltávolítása egy AU-ból

### <a name="azure-portal"></a>Azure Portal

A Azure Portal két módon távolíthatja el a csoportot egy felügyeleti egységből.

Nyissa meg az **Azure ad**  >  -**csoportokat** , és nyissa meg a felügyeleti egységből eltávolítani kívánt csoport profilját. Válassza a **felügyeleti egységek** lehetőséget a bal oldali panelen az összes olyan felügyeleti egység listázásához, ahol a csoport tagja. Válassza ki azt a felügyeleti egységet, amelyből el szeretné távolítani a csoportot, majd válassza az **Eltávolítás a felügyeleti egységből**lehetőséget.

![Csoport eltávolítása felügyeleti egységből](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Másik lehetőségként megnyithatja az **Azure ad**  >  **felügyeleti egységeket** , és kiválaszthatja azt a felügyeleti egységet, amelyben a csoport tagja. Válassza a **csoportok** lehetőséget a bal oldali panelen a tagok listájának listázásához. Válassza ki a felügyeleti egységből eltávolítani kívánt csoportot, majd válassza a **csoportok eltávolítása**lehetőséget.

![Felügyeleti egységben lévő csoportok listázása](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>Következő lépések

- [Szerepkör társítása egy felügyeleti egységhez](roles-admin-units-assign-roles.md)
- [Felhasználók kezelése egy felügyeleti egységben](roles-admin-units-add-manage-users.md)
