---
title: Felhasználók hozzáadása, eltávolítása és listázása egy felügyeleti egységben – Azure Active Directory | Microsoft Docs
description: A felhasználók és a szerepkörük engedélyeinek kezelése egy felügyeleti egységben Azure Active Directory
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
ms.openlocfilehash: 75e518a66ae2eedd952f521e0a67769b6e08de87
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450437"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Felhasználók hozzáadása és kezelése egy felügyeleti egységben Azure Active Directory

A Azure Active Directory (Azure AD) szolgáltatásban hozzáadhat felhasználókat egy felügyeleti egységhez (AU) az irányítás részletesebb felügyeleti hatóköréhez.

A PowerShell és a Microsoft Graph felügyeleti egység felügyeletéhez való előkészítésének lépéseiért lásd: első [lépések](roles-admin-units-manage.md#get-started).

## <a name="add-users-to-an-au"></a>Felhasználók hozzáadása AU-hoz

### <a name="azure-portal"></a>Azure Portal

A felhasználókat kétféleképpen rendelheti hozzá felügyeleti egységekhez.

1. Egyéni hozzárendelés

    1. Nyissa meg az Azure AD-t a portálon, és válassza a felhasználók lehetőséget, majd válassza ki a felügyeleti egységhez hozzárendelni kívánt felhasználót. Ezután a bal oldali panelen kiválaszthatja a felügyeleti egységeket. A felhasználó egy vagy több felügyeleti egységhez is hozzárendelhető, ha a * * hozzárendelés felügyeleti egységhez gombra kattint, és kiválasztja azokat a felügyeleti egységeket, amelyekhez a felhasználót hozzá szeretné rendelni.

       ![Válassza a Hozzáadás lehetőséget, majd adja meg a felügyeleti egység nevét.](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. Nyissa meg az Azure AD-t a portálon, és válassza a felügyeleti egységek lehetőséget a bal oldali ablaktáblán, majd válassza ki azt a felügyeleti egységet, amelyben a felhasználókat hozzá szeretné rendelni. Válassza **a minden felhasználó** lehetőséget, majd válassza a **tag hozzáadása**elemet. Ezután a jobb oldali panelen kiválaszthat egy vagy több felhasználót a felügyeleti egységhez való hozzárendeléshez.

        ![Válasszon ki egy felügyeleti egységet, majd válassza a tag hozzáadása elemet.](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. Tömeges hozzárendelés

    Nyissa meg az Azure AD-t a portálon, és válassza a felügyeleti egységek lehetőséget. Válassza ki azt a felügyeleti egységet, ahová a felhasználókat hozzá szeretné adni. Folytassa a minden felhasználóra kattintva – > Tagok hozzáadása. csv-fájlból. Ezután letöltheti a CSV-sablont, és szerkesztheti a fájlt. A formátum egyszerű, és minden sorba hozzá kell adni egy egyszerű FELHASZNÁLÓNEVEt. Ha a fájl elkészült, mentse a fájlt egy megfelelő helyre, majd töltse fel a 3. lépésben, ahogy az a pillanatképben ki van jelölve.

    ![felhasználók tömeges kiosztása egy felügyeleti egységhez](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```

A fenti példában az Add-AzureADAdministrativeUnitMember parancsmag használatával lehet hozzáadni a felhasználót a felügyeleti egységhez. Annak a felügyeleti egységnek az azonosítója, amelyhez a felhasználót hozzá kívánja adni, valamint a hozzáadni kívánt felhasználó objektumazonosító argumentumként. A Kiemelt szakasz szükség szerint módosítható az adott környezetben.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

Példa:

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="list-administrative-units-for-a-user"></a>Felhasználók felügyeleti egységeinek listázása

### <a name="azure-portal"></a>Azure Portal

A Azure Portal a következőket teheti meg a felhasználó profiljának megnyitásával:

1. **Azure ad**-  >  **felhasználók**megnyitása.

1. Válassza ki a felhasználót a felhasználó profiljának megnyitásához.

1. Válassza ki a **felügyeleti egységek** elemet azon felügyeleti egységek listájának megtekintéséhez, amelyekben a felhasználó hozzá van rendelve.

   ![Felhasználó felügyeleti egységének listázása](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

### <a name="microsoft-graph"></a>Microsoft Graph

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-au"></a>Egyetlen felhasználó eltávolítása egy AU-ból

### <a name="azure-portal"></a>Azure Portal

A felhasználókat kétféleképpen lehet eltávolítani egy felügyeleti egységből. A Azure Portal megnyithatja a felhasználó profilját az **Azure ad**-  >  **felhasználók**lehetőséggel. Válassza ki a felhasználót a felhasználó profiljának megnyitásához. Válassza ki azt a felügyeleti egységet, amelyről el szeretné távolítani a felhasználót, majd válassza az **Eltávolítás a felügyeleti egységből**lehetőséget.

![Felhasználó eltávolítása felügyeleti egységből a felhasználói profilból](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

Egy felhasználót az **Azure ad**  >  **felügyeleti egységekben** is eltávolíthat, ha kiválasztja azt a felügyeleti egységet, amelyből el szeretné távolítani a felhasználókat. Válassza ki a felhasználót, és válassza a **tag eltávolítása**lehetőséget.
  
![Felhasználó eltávolítása a felügyeleti egység szintjén](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref

## <a name="bulk-remove-more-than-one-user"></a>Egynél több felhasználó tömeges eltávolítása

Nyissa meg az Azure AD > felügyeleti egységeket, és válassza ki azt a felügyeleti egységet, amelyből el szeretné távolítani a felhasználókat. Kattintson a tömeges eltávolítási tag elemre. Töltse le a CSV-sablont az eltávolítandó felhasználók listájának biztosításához.

Szerkessze a letöltött CSV-sablont a megfelelő felhasználói bejegyzésekkel. Ne távolítsa el a sablon első két sorát. Minden sorban adjon hozzá egy egyszerű felhasználónevet.

![A CSV-fájl szerkesztése a tömeges felhasználói eltávolításhoz a felügyeleti egységekből](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

Miután mentette a fájlban szereplő bejegyzéseket, töltse fel a fájlt, majd válassza a **Submit (Küldés**) lehetőséget.

![A tömeges feltöltési fájl elküldése](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>Következő lépések

- [Szerepkör társítása egy felügyeleti egységhez](roles-admin-units-assign-roles.md)
- [Csoportok hozzáadása egy felügyeleti egységhez](roles-admin-units-add-manage-groups.md)
