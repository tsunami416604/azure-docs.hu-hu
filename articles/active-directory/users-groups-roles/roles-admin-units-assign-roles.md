---
title: Szerepkörök kiosztása és listázása a felügyeleti egység hatókörével – Azure Active Directory | Microsoft Docs
description: Felügyeleti egységek használata a szerepkör-hozzárendelések hatókörének korlátozásához a Azure Active Directory
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
ms.openlocfilehash: 00b5f39363e4c8b2fd3a0d74a8c013d315bff1fe
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264930"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Hatókörrel rendelkező szerepkörök társítása egy felügyeleti egységhez

A Azure Active Directory (Azure AD) segítségével felhasználókat rendelhet egy Azure AD-szerepkörhöz, amely egy vagy több felügyeleti egységre (AUs) korlátozódik részletesebb felügyeleti felügyeletre.

A PowerShell és a Microsoft Graph felügyeleti egység felügyeletéhez való előkészítésének lépéseiért lásd: első [lépések](roles-admin-units-manage.md#get-started).

## <a name="roles-available"></a>Elérhető szerepkörök

Szerepkör  |  Leírás
----- |  -----------
Hitelesítés rendszergazdája  |  A csak a hozzárendelt felügyeleti egységben lévő nem rendszergazda felhasználók számára biztosít hozzáférést a hitelesítési módszer megtekintéséhez, beállításához és visszaállításához.
Csoportok rendszergazdája  |  A a csoportok és csoportok beállításait, például a névadási és lejárati házirendeket csak a hozzárendelt felügyeleti egységben tudja kezelni.
Segélyszolgálat rendszergazdája  |  A nem rendszergazdák és az ügyfélszolgálati rendszergazdák jelszavait alaphelyzetbe állíthatja a hozzárendelt felügyeleti egységben.
Licenc rendszergazdája  |  Csak a felügyeleti egységen belül lehet hozzárendelni, eltávolítani és frissíteni a licenc-hozzárendeléseket.
Jelszó-rendszergazda  |  A csak a hozzárendelt felügyeleti egységen belül állíthatja alaphelyzetbe a nem rendszergazdák és a jelszó-rendszergazdák jelszavát.
Felhasználói rendszergazda  |  A a felhasználók és csoportok minden aspektusát képes kezelni, beleértve a korlátozott rendszergazdák jelszavainak alaphelyzetbe állítását a hozzárendelt felügyeleti egységen belül.

## <a name="assign-a-scoped-role"></a>Hatókörrel rendelkező szerepkör kiosztása

### <a name="azure-portal"></a>Azure Portal

Nyissa meg az **Azure AD > felügyeleti egységeket** a portálon. Válassza ki azt a felügyeleti egységet, amelyhez hozzá szeretné rendelni a szerepkört egy felhasználóhoz. A bal oldali ablaktáblán válassza a szerepkörök és rendszergazdák lehetőséget az összes elérhető szerepkör listázásához.

![Válasszon ki egy felügyeleti egységet a szerepkör hatókörének módosításához](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Válassza ki a hozzárendelni kívánt szerepkört, majd válassza a **hozzárendelések hozzáadása**lehetőséget. A jobb oldalon megnyílik egy panel, ahol kiválaszthatja a szerepkörhöz hozzárendelni kívánt felhasználókat.

![Válassza ki a hatókörhöz tartozó szerepkört, majd válassza a hozzárendelések hozzáadása elemet.](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

```powershell
$AdminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$Role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$administrativeUnit = Get-AzureADAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$RoleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$RoleMember.ObjectId = $AdminUser.ObjectId
Add-AzureADScopedRoleMembership -ObjectId $administrativeUnit.ObjectId -RoleObjectId $Role.ObjectId -RoleMemberInfo $RoleMember
```

A Kiemelt szakasz szükség szerint módosítható az adott környezetben.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
POST /administrativeUnits/{id}/scopedRoleMembers
    
Request body
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="list-the-scoped-admins-on-an-au"></a>A hatókörrel rendelkező rendszergazdák listázása egy AU-on

### <a name="azure-portal"></a>Azure Portal

A felügyeleti egység hatókörével végzett összes szerepkör-hozzárendelést az [Azure ad felügyeleti egységek szakaszában](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)tekintheti meg. Nyissa meg az **Azure AD > felügyeleti egységeket** a portálon. Válassza ki a listázni kívánt szerepkör-hozzárendelések felügyeleti egységét. Válassza a **szerepkörök és rendszergazdák** lehetőséget, és nyisson meg egy szerepkört a hozzárendelések megtekintéséhez a felügyeleti egységben.

### <a name="powershell"></a>PowerShell

```powershell
$administrativeUnit = Get-AzureADAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADScopedRoleMembership -ObjectId $administrativeUnit.ObjectId | fl *
```

A Kiemelt szakasz szükség szerint módosítható az adott környezetben.

### <a name="microsoft-graph"></a>Microsoft Graph

```http
Http request
GET /administrativeUnits/{id}/scopedRoleMembers
Request body
{}
```

## <a name="next-steps"></a>Következő lépések

- [Felhőalapú csoportok használata a szerepkör-hozzárendelések kezeléséhez](roles-groups-concept.md)
- [A felhőbeli csoportokhoz hozzárendelt szerepkörökkel kapcsolatos hibák elhárítása](roles-groups-faq-troubleshooting.md)
