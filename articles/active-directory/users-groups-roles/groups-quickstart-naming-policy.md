---
title: Adja hozzá a kiosztási szabályzat a rövid útmutató az Office 365-csoportok – Azure Active Directory |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan vehet fel új felhasználókat, vagy hogyan törölhet meglévő felhasználókat az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0e600204479bc54a590df6bf1bbcd634eaac7fc
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605640"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Gyors útmutató: Az Azure Active Directory elnevezési szabályzat

Ebben a rövid útmutatóban elnevezési szabályzatot állíthat be az Azure Active Directory (Azure AD) bérlőjében a felhasználók által létrehozott Office 365-csoportokhoz, hogy a bérlő csoportjainak rendszerezése és keresése egyszerűbbé váljon. Az elnevezési szabályzatot például a következőkre használhatja:

* Tájékoztatást adhat a csoport funkciójáról, tagságáról, földrajzi régiójáról vagy a csoport létrehozójáról.
* Segíthet a csoportok a címtárban való besorolásában.
* Letilthatja bizonyos szavak használatát a csoportnevekben és aliasokban.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal-preview"></a>Az Azure portal (előzetes verzió) használatával egy bérlő csoportelnevezési házirend konfigurálása

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com) rendszergazdai felhasználói fiókkal.
1. Válassza ki **csoportok**, majd **elnevezési szabályzatában foglalt** elnevezési szabályzat lapjának megnyitásához.

    ![Nyissa meg az elnevezési szabályzat oldalt a felügyeleti központ](./media/groups-naming-policy/policy-preview.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Zobrazí nebo upraví az előtag-utótag csoportelnevezési házirend

1. Az a **elnevezési szabályzatában foglalt** lapon jelölje be **elnevezési csoportházirend**.
1. Megtekintheti vagy szerkesztheti a jelenlegi előtag vagy utótag házirendek külön-külön elnevezési az attribútumok vagy karakterláncok kényszeríteni a kiosztási szabályzat részeként szeretné kiválasztásával.
1. Elő- vagy utótag eltávolítása a listából, válassza ki a előtag vagy utótag, majd jelölje ki **törlése**. Egyszerre több elem is lehet törölni.
1. Válassza ki **mentése** a módosítások a házirend lép érvénybe.

### <a name="view-or-edit-the-custom-blocked-words"></a>Zobrazí nebo upraví egyéni letiltott szavakat

1. Az a **elnevezési szabályzatában foglalt** lapon jelölje be **letiltott szavakat**.

    ![szerkesztése és feltöltése az elnevezési szabályzatában foglalt letiltott szavakat listázása](./media/groups-naming-policy/blockedwords-preview.png)

1. Zobrazí nebo upraví egyéni letiltott szavakat aktuális listájának kiválasztásával **letöltése**.
1. A fájl ikonra kattintva töltse fel az új egyéni letiltott szavak listáját.
1. Válassza ki **mentése** a módosítások a házirend lép érvénybe.

Ennyi az egész. Beállította az elnevezési szabályzatot és hozzáadta az egyéni letiltott szavak listáját.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="remove-the-naming-policy-using-azure-portal-preview"></a>Távolítsa el a kiosztási szabályzat (előzetes verzió) az Azure portal használatával

1. Az a **elnevezési szabályzatában foglalt** lapon jelölje be **házirend törlése**.
1. Miután meggyőződött a törlés, a kiosztási szabályzat törlődik a, beleértve az összes előtag-utótag elnevezési házirend és egyéni letiltott szavakat.

## <a name="next-steps"></a>További lépések

Ez a rövid útmutatóban bemutattuk, hogyan állíthatja be a csoportelnevezési házirend, az Azure Portalon az Azure AD szervezete számára.

Folytassa a következő cikk további információk a PowerShell-parancsmagok a kiosztási szabályzat, műszaki korlátozások, egyéni letiltott szavakat, és a végfelhasználói élményt hozzáadása az Office 365-alkalmazások között.
> [!div class="nextstepaction"]
> [Kiosztási szabályzat PowerShell](groups-naming-policy.md)