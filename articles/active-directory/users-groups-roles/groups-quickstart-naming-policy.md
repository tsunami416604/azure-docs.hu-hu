---
title: Csoport elnevezési szabályzatának rövid útmutatója – Azure Active Directory | Microsoft Docs
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
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026939"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Rövid útmutató: Elnevezési szabályzat az Azure Active Directoryban lévő csoportokhoz

Ebben a rövid útmutatóban elnevezési szabályzatot állíthat be az Azure Active Directory (Azure AD) bérlőjében a felhasználók által létrehozott Office 365-csoportokhoz, hogy a bérlő csoportjainak rendszerezése és keresése egyszerűbbé váljon. Az elnevezési szabályzatot például a következőkre használhatja:

* Tájékoztatást adhat a csoport funkciójáról, tagságáról, földrajzi régiójáról vagy a csoport létrehozójáról.
* Segíthet a csoportok a címtárban való besorolásában.
* Letilthatja bizonyos szavak használatát a csoportnevekben és aliasokban.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal"></a>A csoport elnevezési szabályzatának konfigurálása a bérlőhöz Azure Portal használatával

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) egy felhasználói rendszergazdai fiókkal.
1. Válassza a **csoportok**lehetőséget, majd válassza az **elnevezési házirend** elemet az elnevezési házirend lap megnyitásához.

    ![az elnevezési szabályzat lap megnyitása a felügyeleti központban](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Az előtag-utótag elnevezési házirend megtekintése vagy szerkesztése

1. Az **elnevezési házirend** lapon válassza a **csoport elnevezési házirend**elemet.
1. Az aktuális előtag-vagy utótag-elnevezési házirendeket egyenként tekintheti meg vagy szerkesztheti. Ehhez válassza ki azokat az attribútumokat vagy karakterláncokat, amelyeket az elnevezési szabályzat részeként szeretne kikényszeríteni.
1. Ha el szeretne távolítani egy előtagot vagy utótagot a listából, válassza ki az előtagot vagy utótagot, majd válassza a **Törlés**lehetőséget. Egyszerre több elem is törölhető.
1. A módosítások érvénybe léptetéséhez válassza a **Mentés** lehetőséget a szabályzat módosításaihoz.

### <a name="view-or-edit-the-custom-blocked-words"></a>Az egyéni letiltott szavak megtekintése és szerkesztése

1. A **névadási házirend** lapon válassza a **tiltott szavak**elemet.

    ![tiltott szavak listájának szerkesztése és feltöltése elnevezési házirendhez](./media/groups-naming-policy/blockedwords.png)

1. A **Letöltés**lehetőség kiválasztásával megtekintheti vagy szerkesztheti az egyéni tiltott szavak aktuális listáját.
1. Töltse fel az egyéni tiltott szavak új listáját a fájl ikon kiválasztásával.
1. A módosítások érvénybe léptetéséhez válassza a **Mentés** lehetőséget a szabályzat módosításaihoz.

Ennyi az egész. Beállította az elnevezési szabályzatot és hozzáadta az egyéni letiltott szavak listáját.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="remove-the-naming-policy-using-azure-portal"></a>Az elnevezési házirend eltávolítása Azure Portal használatával

1. Az **elnevezési házirend** lapon válassza a **házirend törlése**lehetőséget.
1. A Törlés megerősítése után a rendszer eltávolítja az elnevezési házirendet, beleértve az összes előtag-utótag elnevezési házirendet és az egyéni letiltott szavakat is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan állíthatja be az Azure AD-szervezet elnevezési házirendjét a Azure Portal használatával.

Folytassa a következő cikkel, amely további információkat tartalmaz: az elnevezési házirendhez, a technikai korlátozásokhoz, az egyéni blokkolt szavak listájának hozzáadásához, valamint az Office 365-alkalmazások végfelhasználói tapasztalataihoz tartozó PowerShell-parancsmagok.
> [!div class="nextstepaction"]
> [Elnevezési házirend PowerShell](groups-naming-policy.md)