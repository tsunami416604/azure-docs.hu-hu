---
title: Az Azure-erőforrás-szerepkörök hozzáférésének befejezése a PIM-ben – Azure AD | Microsoft dokumentumok
description: Megtudhatja, hogyan végezheti el az Azure-erőforrás-szerepkörök access review of Azure Active Directory ban emelt szintű identitáskezelés.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021985"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Access-ellenőrzés befejezése az Azure-erőforrás-szerepkörökről a kiemelt identitáskezelésben

A kiemelt jogosultságú szerepkör-rendszergazdák a [hozzáférés-ellenőrzés megkezdése](pim-resource-roles-start-access-review.md)után áttekinthetik a kiemelt hozzáféréseket. Az Azure Active Directory (Azure AD) emelt szintű identitáskezelés (PIM) automatikusan küld egy e-mailt, amely kéri a felhasználókat, hogy tekintse át a hozzáférést. Ha a felhasználó nem kap e-mailt, elküldheti nekik a [hozzáférési felülvizsgálat elvégzéséhez](pim-resource-roles-perform-access-review.md)vonatkozó utasításokat.

Miután a hozzáférési felülvizsgálati időszak véget ért, vagy miután az összes felhasználó befejezte az önellenőrzés, kövesse a cikkben leírt lépéseket az ellenőrzés kezeléséhez és az eredmények megtekintéséhez.

## <a name="manage-access-reviews"></a>Hozzáférés-felülvizsgálatok kezelése

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com/) Az irányítópulton válassza ki az **Azure-erőforrások** szolgáltatást.

2. Válassza ki az erőforrást.

3. Válassza ki az **irányítópult Access-ellenőrzések** szakaszát.

    ![Azure-erőforrások – Access-felülvizsgálatok listája a szerepkört, a tulajdonost, a kezdő dátumot, a befejezési dátumot és az állapotot megjelenítő](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Jelölje ki a kezelni kívánt hozzáférési felülvizsgálatot.

A hozzáférés-felülvizsgálat részletes lapján számos lehetőség van az ellenőrzés kezelésére. A következő lehetőségek közül választhat:

![Az ellenőrzés kezelésének lehetőségei - Leállítás, Visszaállítás, Alkalmaz, Törlés](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Leállítás

Minden hozzáférési felülvizsgálatnak van záró dátuma. A Korai befejezéshez válassza a **Leállítás** lehetőséget. Azok a felhasználók, akik eddig nem fejezték be az értékelést, nem tudják befejezni azt az ellenőrzés leállítása után. Az ellenőrzés leállítása után nem lehet újraindítani.

### <a name="reset"></a>Alaphelyzetbe állítás

A hozzáférés-felülvizsgálat alaphelyzetbe állítása a rajta hozott összes döntés eltávolításához. Miután visszaállította a hozzáférési felülvizsgálatot, a rendszer minden felhasználót úgy jelöl meg, hogy nem ellenőrzi kitévét újra.

### <a name="apply"></a>Alkalmaz

A hozzáférési felülvizsgálat befejezése után válassza az **Alkalmazás** lehetőséget a felülvizsgálat eredményének megvalósításához. Ha egy felhasználó hozzáférését megtagadták az ellenőrzés, ez a lépés eltávolítja a szerepkör-hozzárendelés.  

### <a name="delete"></a>Törlés

Ha már nem érdekel az értékelés, törölje azt. Válassza **a Véleményezés törlése** lehetőséget a kiemelt identitáskezelési szolgáltatásból.

## <a name="results"></a>Results (Eredmények)

Az **Eredmények** lapon tekintse meg és töltse le az értékelés eredményeinek listáját.

![Eredmények oldal felsorolása felhasználók, eredmény, ok, felül kell vizsgálni, alkalmazni, és alkalmazza az eredményt](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Lektorok

Megtekintheti és hozzáadhatja az ellenőrzőket a meglévő hozzáférési felülvizsgálathoz. Emlékeztesd a véleményezőket, hogy fejezzék be az értékeléseiket.

![Véleményezők laplista neve és egyszerű felhasználónév](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>További lépések

- [Access-ellenőrzés indítása az Azure-erőforrás-szerepkörökhöz a kiemelt identitáskezelésben](pim-resource-roles-start-access-review.md)
- [Az Azure-erőforrás-szerepkörök hozzáférés-felülvizsgálatának végrehajtása a Kiemelt identitáskezelés ben](pim-resource-roles-perform-access-review.md)
