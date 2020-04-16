---
title: Csoportok hozzáadása, eltávolítása és listázása egy felügyeleti egységben (előzetes verzió) – Azure Active Directory | Microsoft dokumentumok
description: Csoportok és szerepkör-engedélyeik kezelése egy felügyeleti egységben az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f45d9e1ca5b8237779fe096154c83749aa08fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428432"
---
# <a name="add-and-manage-groups-in-administrative-units-in-azure-active-directory"></a>Csoportok hozzáadása és kezelése az Azure Active Directoryban

Az Azure Active Directoryban (Azure AD) csoportokat adhat hozzá egy felügyeleti egységhez (AU) a felügyeleti ellenőrzés részletesebb felügyeleti hatóköréhez.

A PowerShell és a Microsoft Graph felügyeleti egységfelügyelethez való használatának előkészítésére vonatkozó lépésekről [az Első lépések .](roles-admin-units-manage.md#getting-started)

## <a name="add-groups-to-an-au"></a>Csoportok hozzáadása AU-hoz

### <a name="azure-portal"></a>Azure Portal

Az előzetes verzióban a csoportokat csak egyenként rendelheti hozzá egy felügyeleti egységhez. Nincs lehetőség a csoportok tömeges hozzárendelésére egy közigazgatási egységhez. A csoport a portálon kétféleképpen rendelhet hozzá egy csoportot egy közigazgatási egységhez:

1. Az **Azure AD > Csoportok** lapjáról

    Nyissa meg a Csoportok áttekintése lapot az Azure AD-ben, és válassza ki a csoportot, amelyet hozzá kell rendelni a felügyeleti egységhez. A bal oldalon válassza a **Felügyeleti egységek** lehetőséget, ha listázni szeretné azokat a felügyeleti egységeket, amelyekhez a csoport hozzá van rendelve. A tetején megtalálja a lehetőséget Hozzárendelése a közigazgatási egységhez, és kattintson rá ad egy panel a jobb oldalon, hogy válassza ki a közigazgatási egység.

    ![csoport hozzárendelése egyénileg egy felügyeleti egységhez](./media/roles-admin-units-add-manage-groups/assign-to-group-1.png)

1. Az **Azure AD > felügyeleti egységek > minden csoport** lapról

    Nyissa meg az Összes csoport panelt az Azure AD > felügyeleti egységekben. Ha a felügyeleti egységhez már vannak csoportok rendelve, azok a jobb oldalon jelennek meg. Válassza a **Hozzáadás** a tetején, és a jobb oldali panel en csúszik az Azure AD-szervezetben elérhető csoportok listázása. Jelöljön ki egy vagy több csoportot, amelyet a közigazgatási egységekhez szeretne rendelni.

    ![válasszon ki egy felügyeleti egységet, majd válassza a Tag hozzáadása lehetőséget](./media/roles-admin-units-add-manage-groups/assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId

Ebben a példában az Add-AzureADAdministrativeUnitMember parancsmag a csoport hozzáadása a felügyeleti egységhez. A program argumentumként a közigazgatási egység objektumazonosítóját és a hozzáadni hozandó csoport objektumazonosítóját veszi fel. A kiemelt szakasz az adott környezetre vonatkozóan szükség szerint módosítható.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref

    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/groups/{id}"
    }

Példa:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
    }

## <a name="list-groups-in-an-au"></a>Csoportok listázása AU-ban

### <a name="azure-portal"></a>Azure Portal

Nyissa meg az **Azure AD > felügyeleti egységek** a portálon. Válassza ki azt a felügyeleti egységet, amelynek a felhasználóit fel szeretné sorolni. Alapértelmezés szerint **minden felhasználó** már a bal oldali panelen van kijelölve. Válassza az **Összes csoport lehetőséget,** és a jobb oldalon megtalálja a kijelölt felügyeleti egységhez tartozó csoportok listáját.

![A törölni kívánt felügyeleti egység kiválasztása](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId

Ez segít abban, hogy a közigazgatási egység minden tagja megkapja. Ha meg szeretné jeleníteni a felügyeleti egység összes csoportját, használhatja az alábbi kódrészletet:

    foreach ($member in (Get-AzureADAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
    {
    if($member.ObjectType -eq "Group")
    {
    Get-AzureADGroup -ObjectId $member.ObjectId
    }
    }

### <a name="microsoft-graph"></a>Microsoft Graph

    HTTP request
    GET /administrativeUnits/{Admin id}/members/$/microsoft.graph.group
    Request body
    {}

## <a name="list-aus-for-a-group"></a>Csoport AUs listái

### <a name="azure-portal"></a>Azure Portal

Az Azure AD portálon megnyithatja a csoport adatait a **Csoportok**megnyitásával. Jelöljön ki egy csoportot a csoport profiljának megnyitásához. Válassza **a Felügyeleti egységek** lehetőséget az összes olyan közigazgatási egység listázásához, ahol a csoport tagja.

![Csoport felügyeleti egységeinek listázása](./media/roles-admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-group-from-an-au"></a>Csoport eltávolítása AU-ból

### <a name="azure-portal"></a>Azure Portal

Kétféleképpen távolíthat el egy csoportot egy felügyeleti egységből az Azure Portalon.

Nyissa meg az Azure > **AD-csoportokat,** és nyissa meg a felügyeleti egységből eltávolítani kívánt csoport profilját. **Azure AD** Válassza a bal oldali **panelen** a Felügyeleti egységek lehetőséget az összes olyan közigazgatási egység listázásához, ahol a csoport tagja. Jelölje ki azt a felügyeleti egységet, amelyből el szeretné távolítani a csoportot, majd válassza az **Eltávolítás a felügyeleti egységből**lehetőséget.

![Csoport eltávolítása a felügyeleti egységből](./media/roles-admin-units-add-manage-groups/group-au-remove.png)

Másik lehetőségként az **Azure AD** > **felügyeleti egységek,** és válassza ki a felügyeleti egység, ahol a csoport tagja. A tagcsoportok listázásához válassza a Bal oldali **panelcsoportok** elemét. Jelölje ki a felügyeleti egységből eltávolítandó csoportot, majd válassza a **Csoportok eltávolítása**lehetőséget.

![Csoportok listázása egy közigazgatási egységben](./media/roles-admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<group-id>/$ref

## <a name="next-steps"></a>További lépések

- [Szerepkör hozzárendelése felügyeleti egységhez](roles-admin-units-assign-roles.md)
- [Felhasználók kezelése egy felügyeleti egységben](roles-admin-units-add-manage-users.md)
