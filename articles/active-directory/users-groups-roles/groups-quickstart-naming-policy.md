---
title: Csoportelnevezési házirend – Azure Active Directory | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogyan vehet fel új felhasználókat, vagy hogyan törölhet meglévő felhasználókat az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e419ac5c3d292b7e630d1ebb3d3b9f59ef7b8a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74026939"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Rövid útmutató: Elnevezési szabályzat az Azure Active Directoryban lévő csoportokhoz

Ebben a rövid útmutatóban elnevezési szabályzatot állíthat be az Azure Active Directory (Azure AD) bérlőjében a felhasználók által létrehozott Office 365-csoportokhoz, hogy a bérlő csoportjainak rendszerezése és keresése egyszerűbbé váljon. Az elnevezési szabályzatot például a következőkre használhatja:

* Tájékoztatást adhat a csoport funkciójáról, tagságáról, földrajzi régiójáról vagy a csoport létrehozójáról.
* Segíthet a csoportok a címtárban való besorolásában.
* Letilthatja bizonyos szavak használatát a csoportnevekben és aliasokban.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal"></a>Bérlő csoportelnevezési házirendjének konfigurálása az Azure Portal használatával

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy felhasználói rendszergazdai fiókkal.
1. Válassza **a Csoportok**lehetőséget, majd az **Elnevezési házirend** lehetőséget választva nyissa meg az Elnevezési házirend lapot.

    ![az Elnevezési házirend lap megnyitása a felügyeleti központban](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Az előtag-utótag elnevezési házirendjének megtekintése vagy szerkesztése

1. Az **Elnevezési házirend** lapon válassza a **Csoportelnevezési házirend**lehetőséget.
1. Az aktuális előtagot vagy utótag-elnevezési házirendeket egyenként is megtekintheti vagy szerkesztheti, ha kiválasztja az elnevezési házirend részeként érvényesíteni kívánt attribútumokat vagy karakterláncokat.
1. Ha el szeretne távolítani egy előtagot vagy utótagot a listából, jelölje ki az előtagot vagy utótagot, majd kattintson a **Törlés gombra.** Egyszerre több elem is törölhető.
1. Válassza a **Mentés** lehetőséget a házirend módosításaihoz.

### <a name="view-or-edit-the-custom-blocked-words"></a>Az egyéni letiltott szavak megtekintése vagy szerkesztése

1. Az **Elnevezési házirend** lapon válassza a **Letiltott szavak**lehetőséget.

    ![letiltott szavak listájának szerkesztése és feltöltése az elnevezési házirendhez](./media/groups-naming-policy/blockedwords.png)

1. A **Letöltés**gombra kattintva megtekintheti vagy szerkesztheti az egyéni blokkolt szavak aktuális listáját.
1. Töltse fel az egyéni blokkolt szavak új listáját a fájl ikonjának kiválasztásával.
1. Válassza a **Mentés** lehetőséget a házirend módosításaihoz.

Ennyi az egész. Beállította az elnevezési szabályzatot és hozzáadta az egyéni letiltott szavak listáját.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="remove-the-naming-policy-using-azure-portal"></a>Az elnevezési szabályzat eltávolítása az Azure Portal használatával

1. Az **Elnevezési házirend** lapon válassza a **Házirend törlése**lehetőséget.
1. A törlés megerősítése után az elnevezési házirend törlődik, beleértve az összes előtag-utótag elnevezési házirendet és az egyéni letiltott szavakat.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan állíthatja be az Azure AD-szervezet elnevezési szabályzatát az Azure Portalon keresztül.

További információt a következő cikkre, például az elnevezési szabályzatra vonatkozó PowerShell-parancsmagokról, a technikai korlátozásokról, az egyéni letiltott szavak listájának hozzáadásáról és a végfelhasználói élményről az Office 365-alkalmazásokban olvashat bővebben.
> [!div class="nextstepaction"]
> [A PowerShell elnevezési házirendje](groups-naming-policy.md)