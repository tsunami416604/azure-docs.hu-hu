---
title: Adja hozzá, vagy távolítsa el a csoportok tulajdonosainak – Azure Active Directory |} A Microsoft Docs
description: Az Azure Active Directoryval csoporttulajdonosok hozzáadása vagy eltávolítása kapcsolatos utasításokat.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83a48ad13fe4ae217319b2aa85adf976aa6a7ae
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149847"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Hozzáadása vagy eltávolítása a csoportok tulajdonosainak az Azure Active Directoryban
Az Azure Active Directory (Azure AD-) csoportok tulajdonosai és kezeli a csoport tulajdonosai. A csoport tulajdonosai lehetnek felhasználók vagy egyszerű szolgáltatások, és képesek a csoport kezelésére, beleértve a tagságot is. A csoport tulajdonosai csak a meglévő tulajdonosok vagy csoport-felügyeleti rendszergazdák számára rendelhetők hozzá. Csoporttulajdonosok lehetnek a csoport tagjai, nem szükséges.

Ha egy csoport nem rendelkezik tulajdonossal, a csoport kezelésére szolgáló rendszergazdák továbbra is kezelhetik a csoportot. Azt javasoljuk, hogy minden csoportban legyen legalább egy tulajdonos. Ha a tulajdonosok hozzárendelni egy csoportba, a csoport utolsó tulajdonosa nem távolítható el. Győződjön meg arról, hogy a csoport utolsó tulajdonosának eltávolítása előtt kiválaszt egy másik tulajdonost.

## <a name="add-an-owner-to-a-group"></a>Tulajdonos hozzáadása csoporthoz
Az alábbi útmutatást követve adhat hozzá felhasználót tulajdonosként egy csoporthoz az Azure AD-portál használatával. Ha egy egyszerű szolgáltatást szeretne hozzáadni egy csoport tulajdonosaként, kövesse az utasításokat a [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0)használatával.

### <a name="to-add-a-group-owner"></a>Csoporttulajdonos hozzáadása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza a **Azure Active Directory**lehetőséget, válassza a **csoportok**lehetőséget, majd válassza ki azt a csoportot, amelyhez hozzá kíván adni egy tulajdonost (ebben a példában a *Mdm Policy-West*).

3. A **Mdm házirend – Nyugat áttekintése** lapon válassza a **tulajdonosok**lehetőséget.

    ![Mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés oldalra a tulajdonosok opció kiemelésével](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. A **Mdm házirend-Nyugat-tulajdonosok** lapon válassza a **tulajdonosok hozzáadása**lehetőséget, majd keresse meg és válassza ki azt a felhasználót, aki az új csoport tulajdonosa lesz, majd válassza a **kiválasztás**lehetőséget.

    ![Mobileszköz-kezelési szabályzat – Nyugat - tulajdonosok lap Hozzáadás tulajdonosok lehetőséggel kiemelve](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Miután kiválasztotta az új tulajdonost, frissítheti a **tulajdonosok** lapot, és megtekintheti a tulajdonosok listájához hozzáadott nevet.

## <a name="remove-an-owner-from-a-group"></a>Tulajdonos eltávolítása egy csoportból
Tulajdonos eltávolítása az Azure AD-csoport.

### <a name="to-remove-an-owner"></a>Tulajdonos eltávolítása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza a **Azure Active Directory**lehetőséget, válassza a **csoportok**lehetőséget, majd válassza ki azt a csoportot, amelynek el szeretné távolítani a tulajdonosát (ebben a példában a *Mdm Policy-West*).

3. A **Mdm házirend – Nyugat áttekintése** lapon válassza a **tulajdonosok**lehetőséget.

    ![Mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés oldalra a tulajdonosok opció kiemelésével](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. A **Mdm házirend-Nyugat-tulajdonosok** lapon válassza ki az eltávolítani kívánt felhasználót a csoport tulajdonosaként, válassza az **Eltávolítás** lehetőséget a felhasználó adatai lapról, és válassza az **Igen** lehetőséget a döntés megerősítéséhez.

    ![Az Eltávolítás opció kiemelésével felhasználó információi lap](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    A tulajdonos eltávolítása után visszatérhet a **tulajdonosok** lapra, és megtekintheti a név eltávolítását a tulajdonosok listájából.

## <a name="next-steps"></a>Következő lépések
- [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)

- [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md)

- [Csoportok használata egy integrált SaaS-alkalmazáshoz való hozzáféréshez](../users-groups-roles/groups-saasapps.md)

- [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)

- [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-v2-cmdlets.md)
