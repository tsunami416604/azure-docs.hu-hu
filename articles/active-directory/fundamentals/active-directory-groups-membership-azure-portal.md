---
title: Csoport hozzáadása vagy eltávolítása egy másik csoportból – Azure AD
description: Útmutató a csoport hozzáadásához vagy eltávolításához egy másik csoportból az Azure Active Directory használatával.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 830bf7134b3a8b0425c53673a1347dd77897a5bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75423042"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Csoport hozzáadása vagy eltávolítása egy másik csoportból az Azure Active Directory használatával
Ez a cikk segít egy csoport hozzáadásában és eltávolításában egy másik csoportból az Azure Active Directory használatával.

>[!Note]
>Ha a szülőcsoportot szeretné törölni, olvassa el a Csoport és tagjai frissítése vagy törlése című [témakört.](active-directory-groups-delete-group.md)

## <a name="add-a-group-to-another-group"></a>Csoport hozzáadása másik csoporthoz
Meglévő biztonsági csoportot hozzáadhat egy másik biztonsági csoporthoz (más néven beágyazott csoportokhoz), létrehozva egy tagcsoportot (alcsoportot) és egy szülőcsoportot. A tagcsoport örökli a szülőcsoport attribútumait és tulajdonságait, így a konfigurációs időt megtakarítja.

>[!Important]
>Jelenleg nem támogatjuk:<ul><li>Csoportok hozzáadása a helyszíni Active Directoryval szinkronizált csoporthoz.</li><li>Biztonsági csoportok hozzáadása Office 365-csoportokhoz.</li><li>Office 365-csoportok hozzáadása biztonsági csoportokhoz vagy más Office 365-csoportokhoz.</li><li>Alkalmazások hozzárendelése beágyazott csoportokhoz.</li><li>Licencek alkalmazása beágyazott csoportokra.</li><li>Terjesztési csoportok hozzáadása egymásba ágyazási forgatókönyvekben.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Csoport hozzáadása másik csoport tagjaként

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza az **Azure Active Directory**lehetőséget, majd a **Csoportok**lehetőséget.

3. A **Csoportok – Minden csoport** lapon keresse meg és jelölje ki azt a csoportot, amely egy másik csoport tagjává szeretne válni. Ehhez a gyakorlathoz az **MDM-házirendet** használjuk - Nyugati csoport.

    >[!Note]
    >A csoportot egyszerre csak egy csoporthoz veheti fel tagként. Ezenkívül a **Csoport kiválasztása** mező szűri a megjelenítést a bejegyzés nek a felhasználó vagy az eszköz nevének bármely részéhez való igazítása alapján. A helyettesítő karakterek azonban nem támogatottak.

    ![Csoportok – Minden csoport lap MDM-házirenddel – Nyugat csoport kiválasztva](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Az **MDM-házirend - Nyugat - Csoporttagságok** lapon válassza a **Csoporttagságok**lehetőséget, válassza a **Hozzáadás**lehetőséget, keresse meg azt a csoportot, amelynek a csoporttagja szeretne lenni, majd válassza a **Kijelölés lehetőséget.** Ehhez a gyakorlathoz az **MDM-szabályzatot** használjuk - Minden szervezet csoport.

    Az **MDM-házirend – West** csoport most már tagja az **MDM-házirendnek – Minden szervezeti** csoport, amely az MDM-házirend összes tulajdonságát és konfigurációját örökli - Minden szervezeti csoport.

    ![Csoporttagság létrehozása csoport másik csoporthoz való hozzáadásával](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Tekintse át az **MDM-házirend – Nyugat – Csoporttagságok** lapot a csoport- és tagkapcsolat megtekintéséhez.

6. A csoport- és tagkapcsolat részletesebb megtekintéséhez válassza ki a csoport nevét (**MDM-házirend - Minden szervezet**), és tekintse meg az **MDM-házirend - Nyugati** oldal részleteit.

## <a name="remove-a-group-from-another-group"></a>Csoport eltávolítása másik csoportból
Meglévő biztonsági csoportot eltávolíthat egy másik biztonsági csoportból. A csoport eltávolítása azonban eltávolítja a tagok örökölt attribútumait és tulajdonságait is.

### <a name="to-remove-a-member-group-from-another-group"></a>Tagcsoport eltávolítása másik csoportból
1. A **Csoportok – Minden csoport** lapon keresse meg és jelölje ki azt a csoportot, amelyet egy másik csoport tagjaként el szeretne távolítani. Ehhez a gyakorlathoz ismét az **MDM-házirendet** használjuk - Nyugat csoport.

2. Az **MDM-házirend – Nyugat áttekintés** lapon válassza a **Csoporttagságok**lehetőséget.

3. Válassza ki az **MDM-házirendet – Az Összes szervezeti** csoport az **MDM-házirendből – Nyugat – Csoporttagságok** lapon, majd válassza az **Eltávolítás** az **MDM-házirendből – Nyugat** oldal részletei lehetőséget.

    ![A csoporttagság lapja a tag és a csoport adatait is tartalmazza](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>További információ
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

- [Csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- [Tagok hozzáadása vagy eltávolítása egy csoportból](active-directory-groups-members-azure-portal.md)

- [A csoport beállításainak szerkesztése](active-directory-groups-settings-azure-portal.md)

- [Csoport használata SaaS-alkalmazásokhoz való hozzáférés kezelésére](../users-groups-roles/groups-saasapps.md)

- [Forgatókönyvek, korlátozások és ismert problémák, amelyek csoportok használatával kezelik a licencelést az Azure Active Directoryban](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
