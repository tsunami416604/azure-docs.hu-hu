---
title: Szerepkörök hozzárendelése és listázása felügyeleti egység hatókörrel (előzetes verzió) – Azure Active Directory | Microsoft dokumentumok
description: Felügyeleti egységek használata az Azure Active Directory szerepkör-hozzárendeléseinek hatókörének korlátozására
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
ms.openlocfilehash: 3af281846e2bd1a39e691d84e964d8a8f780a6f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870421"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Hatókörrel rendelkező szerepkörök hozzárendelése felügyeleti egységhez

Az Azure Active Directoryban (Azure AD) a felhasználók at rendelhet egy Azure AD-szerepkörhöz, amelynek hatóköre egy vagy több felügyeleti egységre (AUs) korlátozódik a részletesebb felügyeleti vezérléshez.

A PowerShell és a Microsoft Graph felügyeleti egységkezeléshez való használatának előkészítésére vonatkozó lépésekről az [Első lépések ( Első lépések) témakörben található.](roles-admin-units-manage.md#get-started)

## <a name="roles-available"></a>Elérhető szerepkörök

Szerepkör  |  Leírás
----- |  -----------
Hitelesítési rendszergazda  |  Hozzáféréssel rendelkezik a hitelesítési módszer adatainak megtekintéséhez, beállításához és alaphelyzetbe állításához minden nem rendszergazda felhasználó számára a hozzárendelt felügyeleti egységben.
Csoportok rendszergazdája  |  A csoportok és csoportok beállításainak minden aspektusát kezelheti, például a név- és elévülési házirendeket csak a hozzárendelt felügyeleti egységben.
Ügyfélszolgálati rendszergazda  |  Csak a hozzárendelt felügyeleti egységben lévő nem rendszergazdák és ügyfélszolgálati rendszergazdák jelszavait állíthatja vissza.
Licencrendszergazda  |  Csak a felügyeleti egységen belül rendelhet hozzá, távolíthat el és frissíthet licenc-hozzárendeléseket.
Jelszó-rendszergazda  |  Csak a hozzárendelt felügyeleti egységen belül állíthatja vissza a nem rendszergazdák és a jelszórendszergazdák jelszavait.
Felhasználói rendszergazda  |  A felhasználók és csoportok minden aspektusát kezelheti, beleértve a jelszavak alaphelyzetbe állítását a korlátozott rendszergazdák számára a hozzárendelt felügyeleti egységen belül.

## <a name="assign-a-scoped-role"></a>Hatókörrel megbízott szerepkör hozzárendelése

### <a name="azure-portal"></a>Azure Portal

Nyissa meg az **Azure AD > felügyeleti egységek** a portálon. Válassza ki azt a felügyeleti egységet, amelyfelett a szerepkört egy felhasználóhoz szeretné rendelni. A bal oldali ablaktáblában válassza a Szerepkörök és rendszergazdák lehetőséget az összes elérhető szerepkör listázásához.

![A szerepkörhatókör módosításához válasszon egy felügyeleti egységet](./media/roles-admin-units-assign-roles/select-role-to-scope.png)

Jelölje ki a hozzárendelendő szerepkört, majd válassza **a Hozzárendelések hozzáadása**lehetőséget. Ezzel megnyit egy panelt a jobb oldalon, ahol kiválaszthatja a szerepkörhöz rendelni kívánt egy vagy több felhasználót.

![Jelölje ki a hatókörhöz tartozó szerepkört, majd válassza a Hozzárendelések hozzáadása lehetőséget.](./media/roles-admin-units-assign-roles/select-add-assignment.png)

### <a name="powershell"></a>PowerShell

    $administrative = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $AdminUser = Get-AzureADUser -ObjectId 'janedoe@fabidentity.onmicrosoft.com'
    $uaRoleMemberInfo = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo -Property @{ObjectId = $AdminUser.ObjectId}
    Add-AzureADScopedRoleMembership -RoleObjectId $UserAdminRole.ObjectId -ObjectId $administrative unitObj.ObjectId -RoleMemberInfo  $uaRoleMemberInfo

A kiemelt szakasz az adott környezetre vonatkozóan szükség szerint módosítható.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{id}/scopedRoleMembers
    
    Request body
    {
      "roleId": "roleId-value",
      "roleMemberInfo": {
        "id": "id-value"
      }
    }

## <a name="list-the-scoped-admins-on-an-au"></a>A hatókörrel kapcsolatos rendszergazdák listázása AU-n

### <a name="azure-portal"></a>Azure Portal

A felügyeleti egység hatókörével végzett összes szerepkör-hozzárendelés megtekinthető az [Azure AD Felügyeleti egységek szakaszában.](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit) Nyissa meg az **Azure AD > felügyeleti egységek** a portálon. Válassza ki a listát a felsorolni kívánt szerepkör-hozzárendelések felügyeleti egységét. Válassza a **Szerepkörök és rendszergazdák lehetőséget,** és nyisson meg egy szerepkört a felügyeleti egység hozzárendelései megtekintéséhez.

### <a name="powershell"></a>PowerShell

    $administrative unitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    Get-AzureADScopedRoleMembership -ObjectId $administrative unitObj.ObjectId | fl *

A kiemelt szakasz az adott környezetre vonatkozóan szükség szerint módosítható.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    GET /administrativeUnits/{id}/scopedRoleMembers
    Request body
    {}

## <a name="next-steps"></a>További lépések

- [Felügyeleti egységek hibaelhárítása és gyakori kérdések](roles-admin-units-faq-troubleshoot.md)
