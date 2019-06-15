---
title: Adja hozzá, vagy távolítsa el a csoportok tulajdonosainak – Azure Active Directory |} A Microsoft Docs
description: Az Azure Active Directoryval csoporttulajdonosok hozzáadása vagy eltávolítása kapcsolatos utasításokat.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd684e1bd48f877a74280b33b4df65d7baaa0fe7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507175"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Hozzáadása vagy eltávolítása a csoportok tulajdonosainak az Azure Active Directoryban
Az Azure Active Directory (Azure AD-) csoportok tulajdonosai és kezeli a csoport tulajdonosai. Csoporttulajdonosok lehet felhasználók vagy egyszerű szolgáltatások, és kezelhetik a csoport tagságát. Csak a meglévő csoporttulajdonosok vagy a csoport-kezelésével a Rendszergazdák csoport onwers rendelhet hozzá. Csoporttulajdonosok lehetnek a csoport tagjai, nem szükséges.

Egy csoportnak nincs tulajdonosa van, amikor a Rendszergazdák csoport kezelésének tudnak továbbra is a csoport kezeléséhez.

## <a name="add-an-owner-to-a-group"></a>Tulajdonos hozzáadása csoporthoz
Az alábbiakban útmutatást tulajdonosai a felhasználó hozzáadása csoporthoz az Azure AD portálon használja. A csoport tulajdonosai szolgáltatásnév hozzáadásához kövesse az utasításokat ennek a végrehajtására használatával [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Csoporttulajdonos hozzáadása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza ki **Azure Active Directory**, jelölje be **csoportok**, majd válassza ki a csoport, amelynek tulajdonosa hozzá szeretne (ebben a példában *mobileszköz-kezelési szabályzat – Nyugat-India*).

3. Az a **mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés** lapon jelölje be **tulajdonosok**.

    ![Mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés oldalra a tulajdonosok opció kiemelésével](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. A a **mobileszköz-kezelési szabályzat – Nyugat - tulajdonosok** lapon jelölje be **tulajdonosok hozzáadása**, és keressen rá, és válassza ki a felhasználót, hogy az új csoport tulajdonosa legyen, és válassza a **kiválasztása**.

    ![Mobileszköz-kezelési szabályzat – Nyugat - tulajdonosok lap Hozzáadás tulajdonosok lehetőséggel kiemelve](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Miután kiválasztotta az új tulajdonos, frissítheti a **tulajdonosok** lapon, és tekintse meg a nevét, a tulajdonosok listája hozzá.

## <a name="remove-an-owner-from-a-group"></a>Tulajdonos eltávolítása egy csoportból
Tulajdonos eltávolítása az Azure AD-csoport.

### <a name="to-remove-an-owner"></a>Tulajdonos eltávolítása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza ki **Azure Active Directory**válassza **csoportok**, majd válassza ki a csoportot, amelyre el kívánja távolítani egy olyan tulajdonost (ebben a példában *mobileszköz-kezelési szabályzat – Nyugat-India*).

3. Az a **mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés** lapon jelölje be **tulajdonosok**.

    ![Mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés oldalra a tulajdonosok opció kiemelésével](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Az a **mobileszköz-kezelési szabályzat – Nyugat - tulajdonosok** lapra, jelölje be a csoporttulajdonos eltávolítása, válassza a kívánt felhasználó **eltávolítása** a felhasználó adatai lap, és válassza a **Igen** megerősítéséhez a döntés.

    ![Az Eltávolítás opció kiemelésével felhasználó információi lap](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Miután eltávolította a tulajdonos, visszatérhet a **tulajdonosok** lapon, és tekintse meg a nevét, a tulajdonosok listája lett távolítva.

## <a name="next-steps"></a>További lépések
- [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)

- [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md)

- [Csoportok használata hozzáférések hozzárendelése az integrált SaaS-alkalmazások](../users-groups-roles/groups-saasapps.md)

- [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)

- [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-v2-cmdlets.md)
