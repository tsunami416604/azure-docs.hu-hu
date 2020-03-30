---
title: Csoporttulajdonosok hozzáadása vagy eltávolítása – Azure Active Directory | Microsoft dokumentumok
description: Útmutató a csoporttulajdonosok azure Active Directory használatával hozzáadásához vagy eltávolításához.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149847"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Csoporttulajdonosok hozzáadása vagy eltávolítása az Azure Active Directoryban
Az Azure Active Directory (Azure AD) csoportok tulajdonosa és kezelése a csoport tulajdonosok. A csoporttulajdonosok lehetnek felhasználók vagy egyszerű szolgáltatástagok, és kezelhetik a csoportot a tagsággal együtt. Csak a meglévő csoporttulajdonosok vagy csoportkezelő rendszergazdák rendelhetnek hozzá csoporttulajdonosokat. A csoporttulajdonosoknak nem kell a csoport tagjainak lenniük.

Ha egy csoportnak nincs tulajdonosa, a csoportkezelő rendszergazdák továbbra is kezelhetik a csoportot. Javasoljuk, hogy minden csoportnak legalább egy tulajdonosa legyen. Miután a tulajdonosok egy csoporthoz kerültek, a csoport utolsó tulajdonosa nem távolítható el. Kérjük, győződjön meg róla, hogy válasszon egy másik tulajdonost, mielőtt eltávolítaná az utolsó tulajdonost a csoportból.

## <a name="add-an-owner-to-a-group"></a>Tulajdonos hozzáadása csoporthoz
Az alábbiakban útmutatást tartalmaz egy felhasználó tulajdonosként egy csoporthoz az Azure AD portál használatával. Ha egy csoport tulajdonosaként szeretne hozzáadni egy egyszerű szolgáltatást, kövesse az utasításokat a [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0)használatával.

### <a name="to-add-a-group-owner"></a>Csoporttulajdonos hozzáadása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza az **Azure Active Directory**lehetőséget, válassza a **Csoportok**lehetőséget, majd válassza ki azt a csoportot, amelyhez tulajdonost szeretne hozzáadni (ebben a példában *az MDM-házirend - Nyugat).*

3. Az **MDM-házirend – Nyugat áttekintés e lapon** válassza a **Tulajdonosok**lehetőséget.

    ![MDM-házirend – Nyugat-áttekintés lap kiemelt Tulajdonosok lehetőséggel](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Az **MDM-házirend – Nyugat - Tulajdonosok** lapon válassza a Tulajdonosok **hozzáadása**lehetőséget, majd keresse meg és jelölje ki azt a felhasználót, aki az új csoport tulajdonosa lesz, majd válassza a **Kijelölés lehetőséget.**

    ![MDM-házirend - Nyugat - Tulajdonosok lap Kiemelt Tulajdonosok hozzáadása lehetőséggel](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Miután kiválasztotta az új tulajdonost, frissítheti a **Tulajdonosok** lapot, és megtekintheti a tulajdonosok listájához hozzáadott nevet.

## <a name="remove-an-owner-from-a-group"></a>Tulajdonos eltávolítása egy csoportból
Tulajdonos eltávolítása egy csoportból az Azure AD használatával.

### <a name="to-remove-an-owner"></a>Tulajdonos eltávolítása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza az **Azure Active Directory**lehetőséget, válassza a **Csoportok**lehetőséget, majd válassza ki azt a csoportot, amelynek tulajdonosát el szeretné távolítani (ebben a példában *az MDM-házirend - Nyugat).*

3. Az **MDM-házirend – Nyugat áttekintés e lapon** válassza a **Tulajdonosok**lehetőséget.

    ![MDM-házirend – Nyugat-áttekintés lap kiemelt Tulajdonosok lehetőséggel](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Az **MDM-házirend – Nyugat – Tulajdonosok** lapon válassza ki azt a felhasználót, amelyet csoporttulajdonosként szeretne eltávolítani, válassza az **Eltávolítás** lehetőséget a felhasználó információs lapjáról, és válassza az **Igen** lehetőséget a döntés megerősítéséhez.

    ![A felhasználó információs lapja kiemelt Eltávolítás beállítással](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Miután eltávolította a tulajdonost, visszatérhet a **Tulajdonosok** lapra, és láthatja, hogy a név el lett távolítva a tulajdonosok listájáról.

## <a name="next-steps"></a>További lépések
- [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)

- [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md)

- [Csoportok használata hozzáférés hozzárendelése integrált SaaS-alkalmazáshoz](../users-groups-roles/groups-saasapps.md)

- [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)

- [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-v2-cmdlets.md)
