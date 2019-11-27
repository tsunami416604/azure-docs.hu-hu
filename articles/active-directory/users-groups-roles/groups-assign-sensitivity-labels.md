---
title: Érzékenységi címkék társítása csoportokhoz – Azure AD | Microsoft Docs
description: Tagsági szabályok létrehozása a csoportok automatikus feltöltéséhez és a szabályra vonatkozó hivatkozások létrehozásához.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/19/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07859299805c5f7be869350adbdbfa675775888c
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404808"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Érzékenységi címkék társítása az Office 365-csoportokhoz Azure Active Directory (előzetes verzió)

Azure Active Directory (Azure AD) támogatja az [Microsoft 365 megfelelőségi központ](https://sip.protection.office.com/homepage) által az Office 365-csoportok számára közzétett érzékenységi címkék alkalmazását. Az érzékenységi címkék olyan szolgáltatásokra vonatkoznak, mint az Outlook, a Microsoft Teams és a SharePoint. Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.

> [!IMPORTANT]
> Az Office 365-csoportok Azure AD-érzékenységi címkéjének használatához prémium szintű Azure Active Directory P1-licenc szükséges.

## <a name="group-settings-controlled-by-labels"></a>Címkék által vezérelt csoportok beállításai

A címkéhez két beállítás társítható:

- **Adatvédelem**: a rendszergazdák az adatvédelmi beállításokat a címkével társítva megadhatják, hogy a csoport nyilvános vagy magánjellegű.
- **Vendég hozzáférés**: a rendszergazdák az összes olyan csoportra kihasználhatják a vendég házirendet, amelyhez a címke hozzá van rendelve. Ez a házirend határozza meg, hogy a vendégek hozzáadhatók-e tagként vagy sem. Ha a vendég házirend címkére van konfigurálva, akkor a címkét hozzárendelő csoportok nem teszik lehetővé a AllowToAddGuests beállítás módosítását.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Az érzékenységi címke támogatásának engedélyezése a PowerShellben

A közzétett címkék csoportokba való alkalmazásához először engedélyeznie kell a szolgáltatást. Ezekkel a lépésekkel engedélyezheti az Azure AD funkcióját.

1. Nyisson meg egy Windows PowerShell-ablakot a számítógépen. Emelt szintű jogosultságok nélkül is megnyithatja.
1. Futtassa a következő parancsokat a parancsmagok futtatásának előkészítéséhez.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    A **Bejelentkezés a fiókba** lapon adja meg a rendszergazdai fiókját és jelszavát a szolgáltatáshoz való kapcsolódáshoz, majd válassza a **Bejelentkezés**lehetőséget.
1. Az Azure AD-szervezet aktuális csoportjának beállításainak beolvasása.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Ha nem hoztak létre csoportházirend-beállításokat ehhez az Azure AD-szervezethez, először létre kell hoznia a beállításokat. Az Azure AD-szervezethez tartozó csoportházirend-beállítások létrehozásához kövesse a [Azure Active Directory parancsmagok](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) című szakasz lépéseit.

1. Ezután jelenítse meg az aktuális csoport beállításait.

    ```PowerShell
    $Setting.Values
    ```

1. Ezután engedélyezze a szolgáltatást:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Ezután mentse a módosításokat, és alkalmazza a beállításokat:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Ennyi az egész. Engedélyezte a funkciót, és közzétett címkéket alkalmazhat a csoportokra.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Címke kiosztása egy új csoportba Azure Portal

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com).
1. Válassza a **csoportok**lehetőséget, majd válassza az **új csoport**lehetőséget.
1. Az **új csoport** lapon válassza az **Office 365**lehetőséget, majd adja meg az új csoport szükséges adatait, és válasszon ki egy érzékenységi címkét a listából.

   ![Érzékenységi címke kiosztása az új csoportok lapon](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Mentse a módosításokat, és válassza a **Létrehozás**lehetőséget.

A rendszer létrehozza a csoportot, és automatikusan kényszeríti a kiválasztott címkéhez társított házirendeket.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Címke kiosztása meglévő csoporthoz Azure Portal

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) globális rendszergazdai vagy csoportos rendszergazdai fiókkal, vagy egy csoport tulajdonosaként.
1. Válassza a **csoportok**lehetőséget.
1. A **minden csoport** lapon válassza ki a címkézni kívánt csoportot.
1. A kiválasztott csoport lapon válassza a **Tulajdonságok** lehetőséget, és válasszon egy érzékenységi címkét a listából.

   ![Érzékenységi címke kiosztása egy csoport áttekintés lapján](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. A módosítások mentéséhez kattintson a **Save (Mentés** ) gombra.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Címke eltávolítása meglévő csoportból Azure Portal

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) globális rendszergazdai vagy csoportos rendszergazdai fiókkal, vagy egy csoport tulajdonosaként.
1. Válassza a **csoportok**lehetőséget.
1. A **minden csoport** lapon válassza ki azt a csoportot, amelyből el szeretné távolítani a címkét.
1. A **csoport** lapon válassza a **Tulajdonságok**lehetőséget.
1. Válassza az **Eltávolítás** lehetőséget.
1. A módosítások alkalmazásához válassza a **Mentés** lehetőséget.

## <a name="office-365-app-support-for-sensitivity-labels"></a>Office 365-alkalmazások támogatása az érzékenységi címkékhez

Az alábbi Office 365-alkalmazások és-szolgáltatások támogatják az előnézet érzékenységi címkéit:

- Azure AD felügyeleti központ
- Microsoft 365 megfelelőségi központ
- SharePoint
- Webes Outlook
- Csapatok
- SharePoint felügyeleti központ

Az Office 365-alkalmazások támogatásával kapcsolatos további információkért lásd: [office 365-támogatás az érzékenységi címkékhez](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-new-sensitivity-labels).

## <a name="using-classic-azure-ad-classifications"></a>Klasszikus Azure AD-besorolások használata

A szolgáltatás engedélyezése után az Office 365 már nem támogatja az új csoportok "klasszikus" besorolását. A klasszikus besorolások a régi besorolások, amelyet az Azure AD PowerShell `ClassificationList` beállításának értékeinek meghatározásával állíthat be. Ha ez a szolgáltatás engedélyezve van, a rendszer nem alkalmazza ezeket a besorolásokat a csoportokra.

## <a name="troubleshooting-issues"></a>Hibaelhárítási problémák

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Az érzékenységi címkék nem érhetők el a csoporton való hozzárendeléshez

Az érzékenységi címke beállítás csak akkor jelenik meg a csoportok esetében, ha a következő feltételek teljesülnek:

1. A címkék a Microsoft 365 megfelelőségi központban jelennek meg a bérlő számára.
1. A szolgáltatás engedélyezve van, a EnableMIPLabels True értékre van állítva a PowerShellben.
1. A csoport egy Office 365-csoport.
1. A bérlő aktív prémium szintű Azure Active Directory P1 licenccel rendelkezik.
1. A jelenleg bejelentkezett felhasználó hozzáférhet a közzétett címkékhez.
1. A jelenlegi bejelentkezett felhasználónak megfelelő jogosultsága van a címkék hozzárendeléséhez. A felhasználónak globális rendszergazdának, csoport rendszergazdának vagy a csoport tulajdonosának kell lennie.
1. A jelenlegi bejelentkezett felhasználónak van egy Office 365-licence rendelve. A licencekre vonatkozó követelményekkel kapcsolatos további információkért lásd: [az Office-alkalmazások érzékenységi címkéi](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-office-apps).

Győződjön meg arról, hogy az összes feltétel teljesül, hogy címkéket rendeljen egy csoporthoz.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>A hozzárendelni kívánt címke nem szerepel a listában

Ha a keresett címke nem szerepel a listán, akkor ez a következő okok egyike lehet:

- Előfordulhat, hogy a címke nem kerül közzétételre a Microsoft 365 megfelelőségi központban. Ez a már nem közzétett címkékre is alkalmazható. További információért forduljon a rendszergazdához.
- Előfordulhat, hogy a címke közzé van téve, de nem érhető el a bejelentkezett felhasználó számára. Kérjen segítséget a rendszergazdától a címke elérésének módjával kapcsolatban.

### <a name="how-can-i-change-the-label-on-a-group"></a>Hogyan változtathatom meg egy csoport címkéjét?

A címkék bármikor felcserélhetők, ha ugyanazokat a lépéseket használják, mint a címkék egy meglévő csoportba való hozzárendelésével, az alábbiak szerint:

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) globális vagy csoportos rendszergazdai fiókkal vagy a csoport tulajdonosaként.
1. Válassza a **csoportok**lehetőséget.
1. A **minden csoport** lapon válassza ki a címkézni kívánt csoportot.
1. A kiválasztott csoport lapon válassza a **Tulajdonságok** lehetőséget, és válasszon egy új érzékenységi címkét a listából.
1. Kattintson a **Mentés** gombra.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>A csoportok a közzétett címkékre vonatkozó módosításait nem frissítik a csoportokon

Ajánlott eljárásként Azt javasoljuk, hogy a címke beállításait a csoportokra való alkalmazás után módosítsa a címkékre. Ha a [Microsoft 365 megfelelőségi központban](https://sip.protection.office.com/homepage)közzétett címkékhez társított csoportházirend-beállításokat módosítja, a rendszer ezeket a házirend-módosításokat nem alkalmazza automatikusan az érintett csoportokra.

Ha módosítania kell a módosításokat, az [Azure ad PowerShell-parancsfájllal](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) manuálisan alkalmazhatja az érintett csoportok frissítéseit. Ezzel a módszerrel gondoskodhat arról, hogy minden meglévő csoport érvényesítse az új beállítást.

## <a name="next-steps"></a>Következő lépések

- [Érzékenységi címkék használata a Microsoft Teams, az Office 365-csoportok és a SharePoint-webhelyek használatával](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Csoportok frissítése a címkézési házirend módosítása után manuálisan az Azure AD PowerShell-parancsfájllal](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [A csoport beállításainak szerkesztése](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Csoportok kezelése PowerShell-parancsokkal](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
