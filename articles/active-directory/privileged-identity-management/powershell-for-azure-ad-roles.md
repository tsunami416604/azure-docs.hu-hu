---
title: PowerShell Azure AD-szerepkörök pim - Azure AD | Microsoft dokumentumok
description: Azure AD-szerepkörök kezelése powershell-parancsmagokkal az Azure AD kiemelt identitáskezelés (PIM) használatával.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 364b0268be99685b18db175ffbabd9350531d500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048876"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>PowerShell azure-beli AD-szerepkörök a kiemelt identitáskezelés

Ez a cikk az Azure Active Directory (Azure AD) PowerShell-parancsmagok használatára vonatkozó utasításokat tartalmaz az Azure AD-szerepkörök emelt szintű identitáskezelésben (PIM) történő kezeléséhez. Azt is bemutatja, hogyan lehet beállítani az Azure AD PowerShell-modul.

> [!Note]
> A hivatalos PowerShell csak akkor támogatott, ha az Azure AD kiemelt identitáskezelés új verzióját használja. Kérjük, lépjen a Privilegizált identitáskezelés, és győződjön meg arról, hogy a következő banner a gyorsindítási panelen.
> [![ellenőrizze a kiemelt identitáskezelés verzióját](media/pim-how-to-add-role-to-user/pim-new-version.png "Válassza ki az Azure AD > kiemelt identitáskezelés")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Ha nem rendelkezik ezzel a banner, kérjük, várjon, mint mi jelenleg a folyamatban gördülő ki ezt a frissített élményt az elkövetkező hetekben.
> A kiemelt identitáskezelés PowerShell-parancsmagokat az Azure AD előzetes verzió modul támogatja. Ha egy másik modult használ, és a modul most hibaüzenetet ad vissza, kérjük, kezdje el használni ezt az új modult. Ha egy másik modulra épített termelési rendszerrel rendelkezik, kérjük,pim_preview@microsoft.com

## <a name="installation-and-setup"></a>Telepítés és beállítás

1. Az Azure AD előzetes modul telepítése

        Install-module AzureADPreview

1. A folytatás előtt győződjön meg arról, hogy rendelkezik a szükséges szerepkör-engedélyekkel. Ha felügyeleti feladatokat próbál végrehajtani, például szerepkör-hozzárendelést vagy szerepkör-beállítást szeretne végrehajtani, győződjön meg arról, hogy globális rendszergazdai vagy kiemelt szerepkör-rendszergazdai szerepkörrel rendelkezik. Ha csak a saját hozzárendelését próbálja aktiválni, az alapértelmezett felhasználói engedélyeken kívül nincs szükség engedélyekre.

1. Csatlakozzon az Azure AD-hez.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. Keresse meg a bérlői azonosítót az **Azure Active Directory** > tulajdonságai**címtárazonosítójának****Properties** > megkeresésével. A parancsmagok szakaszban használja ezt az azonosítót, ha szükséges a resourceId.

    ![A bérlőazonosító megkeresése az Azure AD-szervezet tulajdonságaiközött](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> A következő szakaszok egyszerű példák, amelyek segíthetnek önnek, és fut. A következő parancsmagokra vonatkozó részletesebb dokumentációt a. https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management Azonban a providerID paraméterben az "azureResources" "aadRoles" helyett kell cserélnie. Azt is meg kell emlékezni, hogy a bérlői azonosítót az Azure AD-szervezet erőforrás-azonosító paraméterként.

## <a name="retrieving-role-definitions"></a>Szerepkör-definíciók beolvasása

A következő parancsmag használatával az Azure AD-szervezet (bérlő) összes beépített és egyéni Azure AD-szerepkörét lejuthat. Ez a fontos lépés a szerepkör neve és a roleDefinitionId közötti leképezést adja meg. A roleDefinitionId ezekben a parancsmagokban egy adott szerepkörre való hivatkozásérdekében használatos.

A roleDefinitionId az Azure AD-szervezetre jellemző, és eltér a szerepkör-kezelési API által visszaadott roleDefinitionId.The roleDefinitionId is specific to your Azure AD organization and is different from the roleDefinitionId returned by the roleManagement API.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Eredmény:

![Az Azure AD-szervezet összes szerepkörének beszereznie](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Szerepkör-hozzárendelések beolvasása

A következő parancsmag használatával az Azure AD-szervezet összes szerepkör-hozzárendelését lekérheti.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

A következő parancsmag segítségével egy adott felhasználó összes szerepkör-hozzárendelését lekérheti. Ez a lista az Azure AD-portálon "Saját szerepkörök" néven is ismert. Az egyetlen különbség itt az, hogy hozzáadott egy szűrőt a tárgyazonosítóhoz. Ebben a környezetben a tulajdonosazonosító a felhasználói azonosító vagy a csoportazonosító.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

A következő parancsmag segítségével egy adott szerepkör összes szerepkör-hozzárendelését lekérheti. A roleDefinitionId itt az előző parancsmag által visszaadott azonosító.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

A parancsmagok az alábbi szerepkör-hozzárendelési objektumok listáját eredményezik. A tulajdonosazonosító annak a felhasználónak a felhasználói azonosítója, akihez a szerepkör hozzá van rendelve. A hozzárendelési állapot lehet aktív vagy jogosult. Ha a felhasználó aktív, és van egy azonosító a LinkedEligibleRoleAssignmentId mezőben, az azt jelenti, hogy a szerepkör jelenleg aktiválva van.

Eredmény:

![Az Azure AD-szervezet összes szerepkör-hozzárendelésének lekérése](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Szerepkör hozzárendelése

A következő parancsmag használatával hozzon létre egy jogosult hozzárendelést.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

Az ütemezés, amely meghatározza a hozzárendelés kezdési és befejezési idejét, egy olyan objektum, amely a következő példához hasonlóan hozható létre:

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"

## <a name="activate-a-role-assignment"></a>Szerepkör-hozzárendelés aktiválása

A következő parancsmag használatával aktiválhat egy jogosult hozzárendelést.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

Ez a parancsmag majdnem megegyezik a szerepkör-hozzárendelés létrehozásához szükséges parancsmagével. A legfontosabb különbség a parancsmagok között az, hogy a –Type paraméter aktiválása "userAdd" helyett "adminAdd". A másik különbség az, hogy a -AssignmentState paraméter "Aktív" a "Jogosult" helyett.

> [!Note]
> A PowerShellen keresztüli szerepkör-aktiváláshoz két korlátozó forgatókönyv létezik.
> 1. Ha a szerepkör-beállításban jegyrendszer / jegyszám szükséges, akkor ezeket nem lehet paraméterként megadni. Így nem lenne lehetséges a szerepkör aktiválása az Azure Portalon túl. Ez a funkció a következő néhány hónapban kerül bevezetésre a PowerShellben.
> 1. Ha többtényezős hitelesítést igényel a szerepkör aktiválásához, jelenleg nincs mód arra, hogy a PowerShell kihívást jelentsen a felhasználószámára a szerepkör aktiválásakor. Ehelyett a felhasználóknak el kell indítaniuk az MFA-kihívást, amikor az Azure AD-hez csatlakoznak, az egyik mérnökünk [blogbejegyzését](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) követve. Ha pim-alkalmazást fejleszt, az egyik lehetséges megvalósítás a felhasználók kihívása és újracsatlakoztatása a modulhoz, miután "MfaRule" hibaüzenetet kaptak.

## <a name="retrieving-and-updating-role-settings"></a>Szerepkör-beállítások beolvasása és frissítése

A következő parancsmag használatával az Azure AD-szervezet összes szerepkör-beállítását lekaphatja.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

A beállításban négy fő objektum található. Ezek közül az objektumok közül jelenleg csak hármat használ a PIM. A UserMemberSettings az aktiválási beállítások, az AdminEligibleSettings a jogosult hozzárendelések hozzárendelési beállításai, a RendszergazdatagBeállítások pedig az aktív hozzárendelések hozzárendelési beállításai.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

A szerepkör-beállítás frissítéséhez először meg kell határoznia egy beállítási objektumot a következőképpen:

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

Ezután folytathatja a beállítást az egyik objektumra egy adott szerepkörhöz az alábbiak szerint. Az azonosító itt az a szerepkör-beállítási azonosító, amely a listaszerepkör-beállítások parancsmagjának eredményéből beolvasható.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>További lépések

- [Azure AD egyéni szerepkör hozzárendelése](azure-ad-custom-roles-assign.md)
- [Azure AD egyéni szerepkör-hozzárendelés eltávolítása vagy frissítése](azure-ad-custom-roles-update-remove.md)
- [Azure AD egyéni szerepkör-hozzárendelés konfigurálása](azure-ad-custom-roles-configure.md)
- [Szerepkör-definíciók az Azure AD-ben](../users-groups-roles/directory-assign-admin-roles.md)
