---
title: Érzékenységi címkék hozzárendelése csoportokhoz - Azure AD | Microsoft dokumentumok
description: A csoportok automatikus feltöltéséhez és a szabályhivatkozáshoz létrejön tagsági szabályok.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 667fb39aabfec14cff01221b82a45ba8ad1d68d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329732"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Érzékenységi címkék hozzárendelése Office 365-csoportokhoz az Azure Active Directoryban (előzetes verzió)

Az Azure Active Directory (Azure AD) támogatja a Microsoft 365 megfelelőségi központ által az Office [365-csoportokra](https://sip.protection.office.com/homepage) közzétett érzékenységi címkék alkalmazását. Az érzékenységi címkék a különböző szolgáltatások, például az Outlook, a Microsoft Teams és a SharePoint csoportra vonatkoznak. Ez a funkció jelenleg nyilvános előzetes verzióban érhető el. Az Office 365-alkalmazások támogatásáról az [Office 365 érzékenységi címkék támogatása című témakörben](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)talál további információt.

> [!IMPORTANT]
> A szolgáltatás konfigurálásához legalább egy aktív Azure Active Directory Prémium P1 licencnek kell lennie az Azure AD-szervezetben.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Érzékenységi címke támogatás engedélyezése a PowerShellben

Ahhoz, hogy közzétett címkéket alkalmazzon a csoportokra, először engedélyeznie kell a szolgáltatást. Ezek a lépések lehetővé teszik a funkciót az Azure AD-ben.

1. Nyisson meg egy Windows PowerShell-ablakot a számítógépen. Megnyithatja emelt szintű jogosultságok nélkül.
1. Futtassa a következő parancsokat a parancsmagok futtatásának előkészítéséhez.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    A Bejelentkezés a **fiókba** lapon adja meg rendszergazdai fiókját és jelszavát a szolgáltatáshoz való csatlakozáshoz, és válassza **a Bejelentkezés**lehetőséget.
1. Az Azure AD-szervezet aktuális csoportbeállításainak beolvasása.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Ha ehhez az Azure AD-szervezethez nem hoztak létre csoportbeállításokat, először létre kell hoznia a beállításokat. Kövesse az [Azure Active Directory-parancsmagok lépéseit a csoportbeállítások konfigurálásához](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) az Azure AD-szervezet csoportbeállításainak létrehozásához.

1. Ezután jelenítse meg az aktuális csoportbeállításokat.

    ```PowerShell
    $Setting.Values
    ```

1. Ezután engedélyezze a funkciót:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Ezután mentse a módosításokat, és alkalmazza a beállításokat:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Ennyi az egész. Engedélyezte a funkciót, és közzétett címkéket alkalmazhat a csoportokra.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Címke hozzárendelése új csoporthoz az Azure Portalon

1. Jelentkezzen be az [Azure AD felügyeleti központba.](https://aad.portal.azure.com)
1. Válassza a **Csoportok**lehetőséget, majd az **Új csoport**lehetőséget.
1. Az **Új csoport** lapon válassza az **Office 365**lehetőséget, majd töltse ki az új csoporthoz szükséges adatokat, és válasszon egy érzékenységi címkét a listából.

   ![Érzékenységi címke hozzárendelése az Új csoportok lapon](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Mentse a módosításokat, és válassza a **Létrehozás lehetőséget.**

A csoport létrejön, és a kijelölt címkéhez társított webhely- és csoportbeállítások automatikusan érvénybe lépnek.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Címke hozzárendelése meglévő csoporthoz az Azure Portalon

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy Csoportok rendszergazdai fiókkal vagy csoporttulajdonosként.
1. Válassza a **Csoportok lehetőséget.**
1. A **Minden csoport** lapon jelölje ki a címkézni kívánt csoportot.
1. A kijelölt csoport lapján válassza a **Tulajdonságok** lehetőséget, és válasszon egy érzékenységi címkét a listából.

   ![Érzékenységi címke hozzárendelése az áttekintő lapon egy csoporthoz](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. A módosítások mentéséhez válassza a **Mentés** gombot.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Címke eltávolítása egy meglévő csoportból az Azure Portalon

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy globális rendszergazdai vagy csoportok rendszergazdai fiókkal, vagy csoporttulajdonosként.
1. Válassza a **Csoportok lehetőséget.**
1. A **Minden csoport** lapon jelölje ki azt a csoportot, amelyből el szeretné távolítani a címkét.
1. A **Csoport** lapon válassza a **Tulajdonságok lehetőséget.**
1. Válassza **az Eltávolítás**lehetőséget.
1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

## <a name="using-classic-azure-ad-classifications"></a>Klasszikus Azure AD-besorolások használata

Miután engedélyezte ezt a funkciót, a "klasszikus" besorolások csoportok jelennek meg csak a meglévő csoportok és webhelyek, és csak akkor kell használni őket az új csoportok csak akkor, ha csoportok létrehozása az alkalmazásokban, amelyek nem támogatják az érzékenységi címkéket. A rendszergazda szükség esetén később érzékenységi címkékké alakíthatja őket. Klasszikus besorolások a régi besorolások, amelyek et `ClassificationList` az Azure AD PowerShell-ben a beállítás értékeinek meghatározásával állított be. Ha ez a funkció engedélyezve van, ezek a besorolások nem lesznek alkalmazva a csoportokra.

## <a name="troubleshooting-issues"></a>Problémák hibaelhárítása

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Az érzékenységi címkék nem érhetők el csoporthozzárendeléséhez

Az érzékenységi címke beállítás csak akkor jelenik meg csoportok esetében, ha az alábbi feltételek mindegyike teljesül:

1. A címkék közzé vannak téve a Microsoft 365 Megfelelőségi központ a bérlő.
1. A szolgáltatás engedélyezve van, enableMIPLabels van beállítva, hogy igaz a PowerShellben.
1. A csoport egy Office 365-csoport.
1. A bérlő egy aktív Azure Active Directory Prémium P1 licenccel rendelkezik.
1. Az aktuális bejelentkezett felhasználó rendelkezik a címkék hozzárendeléséhez szükséges jogosultságokkal. A felhasználónak globális rendszergazdának, csoportadminisztrátornak vagy csoporttulajdonosnak kell lennie.

Kérjük, győződjön meg róla, hogy minden feltétel teljesül, hogy címkéket rendeljen egy csoporthoz.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>A hozzárendelni kívánt címke nem szerepel a listában

Ha a keresett címke nem szerepel a listában, ez a következő okok valamelyike lehet:

- Előfordulhat, hogy a címke nem jelenik meg a Microsoft 365 Megfelelőségi központban. Ez a már nem közzétett címkékre is vonatkozhat. További információért forduljon a rendszergazdához.
- A címke közzétehető, azonban nem érhető el a bejelentkezett felhasználó számára. A címkéhez való hozzáférésről érdeklődjön a rendszergazdánál.

### <a name="how-to-change-the-label-on-a-group"></a>Csoport címkéjének módosítása

A címkék bármikor cserélhetők a címke meglévő csoporthoz való hozzárendelésével azonos lépésekkel, az alábbiak szerint:

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy globális vagy csoport rendszergazdai fiókkal vagy csoporttulajdonosként.
1. Válassza a **Csoportok lehetőséget.**
1. A **Minden csoport** lapon jelölje ki a címkézni kívánt csoportot.
1. A kijelölt csoport lapján válassza a **Tulajdonságok** lehetőséget, és válasszon egy új érzékenységi címkét a listából.
1. Kattintson a **Mentés** gombra.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>A közzétett címkék csoportbeállítási módosításai nem frissülnek a csoportokon

Ajánlott eljárásként nem javasoljuk, hogy módosítsa a címke csoportbeállításait, miután a címkét csoportokra alkalmazta. Ha módosítja a [Microsoft 365 megfelelőségi központban](https://sip.protection.office.com/homepage)közzétett címkékhez társított csoportbeállításokat, a házirend-módosítások nem lesznek automatikusan alkalmazva az érintett csoportokra.

Ha módosítania kell, használjon [egy Azure AD PowerShell-parancsfájlt,](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) hogy manuálisan alkalmazza a frissítéseket az érintett csoportokra. Ez a módszer gondoskodik arról, hogy az összes meglévő csoport kényszerítse az új beállítást.

## <a name="next-steps"></a>További lépések

- [Érzékenységi címkék használata Microsoft Teams, Office 365-csoportok és SharePoint-webhelyek használatával](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Csoportok frissítése a címkeházirend manuális módosítása után az Azure AD PowerShell-parancsfájllal](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [A csoport beállításainak szerkesztése](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Csoportok kezelése PowerShell-parancsokkal](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
