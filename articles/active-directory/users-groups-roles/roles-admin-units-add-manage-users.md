---
title: Felhasználók hozzáadása, eltávolítása és listázása egy felügyeleti egységben (előzetes verzió) – Azure Active Directory | Microsoft dokumentumok
description: Felhasználók és szerepkör-engedélyeik kezelése egy felügyeleti egységben az Azure Active Directoryban
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
ms.openlocfilehash: 9c2c5c083115440e1e4da203f39f2b32734458c3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684972"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Felhasználók hozzáadása és kezelése egy felügyeleti egységben az Azure Active Directoryban

Az Azure Active Directoryban (Azure AD) felhasználókat adhat hozzá egy felügyeleti egységhez (AU) a felügyeleti hatókör részletesebb hatóköre.

A PowerShell és a Microsoft Graph felügyeleti egységkezeléshez való használatának előkészítésére vonatkozó lépésekről az [Első lépések ( Első lépések) témakörben található.](roles-admin-units-manage.md#get-started)

## <a name="add-users-to-an-au"></a>Felhasználók hozzáadása AU-hoz

### <a name="azure-portal"></a>Azure Portal

A felhasználókat kétféleképpen rendelheti hozzá a felügyeleti egységekhez.

1. Egyéni hozzárendelés

    1. A portálon az Azure AD-t, és válassza ki a felhasználók, és válassza ki a felügyeleti egységhez hozzárendelendő felhasználó. Ezután a bal oldali panelen kiválaszthatja a Felügyeleti egységek lehetőséget. A felhasználó egy vagy több felügyeleti egységhez rendelhető, ha a Hozzárendelés a felügyeleti egységhez gombra kattint, és kiválasztja azokat a felügyeleti egységeket, amelyekhez a felhasználót hozzá kívánja rendelni.

       ![válassza a Hozzáadás lehetőséget, majd adja meg a felügyeleti egység nevét](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. A portálon az Azure AD-re léphet, és a bal oldali ablaktáblában kiválaszthatja a felügyeleti egységeket, majd kiválaszthatja azt a felügyeleti egységet, amelyhez a felhasználókat hozzá kell rendelni. Válassza a Bal oldali ablaktábla Minden felhasználó lehetőséget, majd válassza a Tag hozzáadása lehetőséget. Ezután a jobb oldali ablaktáblából kiválaszthat egy vagy több felhasználót a felügyeleti egységhez.

        ![válasszon ki egy felügyeleti egységet, majd válassza a Tag hozzáadása lehetőséget](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Tömeges hozzárendelés

    Nyissa meg az Azure AD-t a portálon, és válassza a felügyeleti egységek. Válassza ki azt a felügyeleti egységet, ahová a felhasználókat hozzá szeretné adni. Folytassa a Folytatást a Minden felhasználó -> Tagok hozzáadása a .csv fájlból parancsra kattintva. Ezután letöltheti a CSV sablont, és szerkesztheti a fájlt. A formátum egyszerű, és minden sorhoz egyetlen egyszerű felhasználónt kell hozzáadni. Miután a fájl elkészült, mentse el a megfelelő helyre, majd töltse fel a 3.

    ![felhasználók tömeges hozzárendelése egy felügyeleti egységhez](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

A fenti példában az Add-AzureADAdministrativeUnitMember parancsmag a felhasználó felügyeleti egységhez való hozzáadására szolgál. Argumentumként annak a felügyeleti egységnek az objektumazonosítója, amelyhez a felhasználót hozzá kell adni, és a hozzáadni a felhasználó objektumazonosítója. A kiemelt szakasz az adott környezetre vonatkozóan szükség szerint módosítható.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

Példa:

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>Felhasználó felügyeleti egységeinek listázása

### <a name="azure-portal"></a>Azure Portal

Az Azure Portalon megnyithatja a felhasználó profilját az Azure AD >-felhasználók. Kattintson a felhasználóra a felhasználó profiljának megnyitásához.

![Felhasználói profil megnyitása az Azure Active Directoryban](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

Válassza a bal oldali **panelen** a Felügyeleti egységek lehetőséget azoknak a felügyeleti egységeknek a megtekintéséhez, amelyekhez a felhasználó hozzá van rendelve.

![A felhasználó felügyeleti egységeinek listázása](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>Egyetlen felhasználó eltávolítása az AU-ból

### <a name="azure-portal"></a>Azure Portal

A felhasználókkétféleképpen távolíthatók el a felügyeleti egységből. Az Azure Portalon megnyithatja a felhasználó profilját az **Azure AD-felhasználók** > **.** Válassza ki a felhasználót a felhasználó profiljának megnyitásához. Jelölje ki azt a felügyeleti egységet, amelyről a felhasználót el szeretné távolítani, és válassza az **Eltávolítás a felügyeleti egységből**lehetőséget.

![Felhasználó eltávolítása a felügyeleti egységből a felhasználói profilból](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

A felhasználók az **Azure AD** > **felügyeleti egységek** is eltávolíthatja a felügyeleti egység kiválasztásával a felhasználók eltávolítása. Jelölje ki a felhasználót, és válassza **a Tag eltávolítása**lehetőséget.
  
![Felhasználó eltávolítása a felügyeleti egység szintjén](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>Egynél több felhasználó tömeges eltávolítása

Az Azure AD > felügyeleti egységek, és válassza ki a felügyeleti egységet, amelyből el szeretné távolítani a felhasználókat. Kattintson a Tömeges eltávolítás tag. Töltse le a CSV-sablont az eltávolítandó felhasználók listájának megadásához.

A letöltött CSV-sablon szerkesztése a megfelelő felhasználói bejegyzésekkel. Ne távolítsa el a sablon első két sorát. Minden sorhoz adjon hozzá egy felhasználói upn-t.

![A CSV-fájl szerkesztése a felhasználók tömeges eltávolításához a felügyeleti egységekből](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Miután mentette a bejegyzéseket a fájlba, töltse fel a fájlt, válassza **a Küldés**lehetőséget.

![A tömeges feltöltési fájl beküldése](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>További lépések

- [Szerepkör hozzárendelése felügyeleti egységhez](roles-admin-units-assign-roles.md)
- [Csoportok hozzáadása felügyeleti egységhez](roles-admin-units-add-manage-groups.md)
