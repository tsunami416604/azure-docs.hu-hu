---
title: Hogyan lehet hozzáadni, vagy egy másik csoportot az Azure Active Directory csoport eltávolítása |} A Microsoft Docs
description: Megtudhatja, hogyan hozzáadni vagy eltávolítani egy csoportot az Azure Active Directory használatával egy másik csoportot.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/28/2018
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: c28fe5ef226fac993fde221b16bfa875ba4845ca
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579768"
---
# <a name="how-to-add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Hogyan: hozzáadása vagy egy másik csoportot az Azure Active Directory csoport eltávolítása
Ez a cikk segít hozzáadhat és eltávolíthat egy csoportot az Azure Active Directory használatával egy másik csoportot.

>[!Note]
>A szülőcsoport törölni próbál, ha [frissíteni vagy törölni a csoportot és annak tagjait](active-directory-groups-delete-group.md).

## <a name="add-a-group-as-a-member-to-another-group"></a>Egy másik csoportot, amelynek csoport hozzáadása
Hozzáadhat egy meglévő csoportot egy másik meglévő csoporthoz, tag létrehozása (alcsoport) csoport és a egy szülőcsoportot. A tagcsoportnak örökli az attribútumokat és a tulajdonságokat a szülőcsoporthoz, így konfigurációs időt takarít meg.

### <a name="to-add-a-group-as-a-member-to-another-group"></a>Egy csoport egy másik csoportot, amelynek hozzáadása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) a címtár egy globális rendszergazdai fiók használatával.

2. Válassza ki **Azure Active Directory**, majd válassza ki **csoportok**.

3. Az a **csoportok – összes csoport** lapon keresse meg és válassza ki a csoportot, amely egy másik csoport tagjává válik. Ebben a gyakorlatban használjuk a **mobileszköz-kezelési szabályzat – Nyugat-India** csoport.

    >[!Note]
    >Adhat hozzá a csoporthoz, amelynek csak egyetlen csoport egyszerre. Ezenkívül a **csoport kijelölése** box szűri a megjelenített megfelelt a bejegyzés egy felhasználó vagy az eszköz nevének bármelyik részét. Azonban a helyettesítő karakterek nem támogatottak.

    ![Kijelölt csoportok – minden csoportok lap az MDM-szabályzat – Nyugat-csoport](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Az a **mobileszköz-kezelési szabályzat – Nyugat - csoporttagságok** lapon jelölje be **csoporttagságok**, jelölje be **Hozzáadás**, keresse meg a csoport tagja, és válassza a acsoportba **Válassza ki**. Ebben a gyakorlatban használjuk a **mobileszköz-kezelési szabályzat – az összes szervezeti** csoport.

    A **mobileszköz-kezelési szabályzat – Nyugat-India** csoport már tagja a **mobileszköz-kezelési szabályzat – az összes szervezeti** csoport, a tulajdonságok és a mobileszköz-kezelési házirend - az összes szervezeti csoport konfiguráció örökli.

    ![Hozzon létre egy csoport tagságát csoport egy másik csoporthoz való hozzáadásával](media/active-directory-groups-membership-azure-portal/add-group-membership.png)

5. Tekintse át a **mobileszköz-kezelési szabályzat – Nyugat - csoporttagságok** oldalon tekintheti meg a csoport- és tagobjektumok kapcsolatban.

    ![Mobileszköz-kezelési szabályzat – Nyugat - csoport tagságát-oldalról a szülőcsoport](media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. A csoport- és tagobjektumok kapcsolatban részletesebb megjelenítéséhez, válassza ki a csoport nevét (**mobileszköz-kezelési szabályzat – az összes szervezeti**), és tekintse meg a **mobileszköz-kezelési szabályzat – Nyugat-India** részletei lapon.

    ![Csoport tagsági oldaláról is a tagja, és a csoport részletei](media/active-directory-groups-membership-azure-portal/group-membership-review.png)

## <a name="remove-a-member-group-from-another-group"></a>Egy másik csoport tag csoport eltávolítása
Egy meglévő tagcsoportnak távolíthat el egy másik csoportot. Azonban a tagságot is eltávolítása bármely örökölt attribútumok és a tulajdonságok a felhasználók számára.

### <a name="to-remove-a-member-group-from-another-group"></a>Egy tag csoport eltávolítása egy másik csoportot
1. Az a **csoportok – összes csoport** lapon keresse meg és válassza ki azt a csoportot, amely tagja egy másik csoportra, el kell távolítani. Ebben a gyakorlatban újra használjuk a **mobileszköz-kezelési szabályzat – Nyugat-India** csoport.

2. Az a **mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés** lapon jelölje be **csoporttagságok**.

    ![Mobileszköz-kezelési szabályzat – Nyugat-India – áttekintés oldalra](media/active-directory-groups-membership-azure-portal/group-membership-overview.png)

3. Válassza ki a **mobileszköz-kezelési szabályzat – az összes szervezeti** csoportra a **mobileszköz-kezelési szabályzat – Nyugat - csoporttagságok** oldalra, és kattintson **eltávolítása** származó a **mobileszköz-kezelési szabályzat – Nyugat-India** részletei lapon.

    ![Csoport tagsági oldaláról is a tagja, és a csoport részletei](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)


## <a name="additional-information"></a>További információ
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

- [A csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [Hozzon létre egy alapszintű csoportot, és tagokat vehet fel](active-directory-groups-create-azure-portal.md)

- [Tagok hozzáadása vagy eltávolítása a csoportból](active-directory-groups-members-azure-portal.md)

- [A csoport beállításainak szerkesztése](active-directory-groups-settings-azure-portal.md)

- [Licencek hozzárendelése a felhasználókhoz, csoport szerint](../users-groups-roles/licensing-groups-assign.md)
