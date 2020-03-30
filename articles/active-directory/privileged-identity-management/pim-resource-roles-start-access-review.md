---
title: Az Azure-erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása a PIM -Azure AD-ben | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre access review az Azure-erőforrás-szerepkörök az Azure AD kiemelt identitáskezelés (PIM) használatával.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae70b8386b1dc3ebd570d2651cded3eda75dfc53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847075"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Azure-erőforrás-szerepkörök hozzáférés-felülvizsgálatának létrehozása a kiemelt identitáskezelésben

Hozzáférés a kiemelt Azure-erőforrás-szerepkörök az alkalmazottak idővel változik. Az elavult szerepkör-hozzárendelések kockázatának csökkentése érdekében rendszeresen tekintse át a hozzáférést. Az Azure Active Directory (Azure AD) privilegizált identitáskezelés (PIM) használatával hozzáférési felülvizsgálatokat hozhat létre a kiemelt Azure-erőforrás-szerepkörökhöz. Az automatikusan előforduló ismétlődő hozzáférési felülvizsgálatokat is beállíthatja.

Ez a cikk ismerteti, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatok emelt szintű Azure-erőforrás-szerepkörök.

## <a name="prerequisites"></a>Előfeltételek

[Kiemelt szerepkör-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Nyílt hozzáférésű felülvizsgálatok

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) egy olyan felhasználóval, aki a kiemelt szerepkör-rendszergazdai szerepkör tagja.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. A bal oldali menüben válassza az **Azure-erőforrások lehetőséget.**

1. Válassza ki a kezelni kívánt erőforrást, például egy előfizetést vagy felügyeleti csoportot.

1. A Kezelés csoportban válassza az **Access-ellenőrzések lehetőséget.**

    ![Azure-erőforrások – Access-értékelések listája az összes értékelés állapotát megjelenítő listáról](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat indítása

Miután megadta a hozzáférési ellenőrzés beállításait, kattintson a **Start**gombra. A hozzáférési felülvizsgálat megjelenik a listában az állapotjelzővel.

![Hozzáférési felülvizsgálatok listája az elindított ellenőrzés állapotáról](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD e-mailt küld az ellenőrzőknek röviddel az ellenőrzés megkezdése után. Ha úgy dönt, hogy nem az Azure AD küldje el az e-mailt, győződjön meg róla, hogy tájékoztassa az ellenőrzők, hogy egy hozzáférési felülvizsgálat vár rájuk, hogy teljes. Megjelenítheti nekik az [Azure-erőforrás-szerepkörökhöz való hozzáférés áttekintésére](pim-resource-roles-perform-access-review.md)vonatkozó utasításokat.

## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése

Nyomon követheti a folyamatot, ahogy az ellenőrzők befejezik az ellenőrzéseket a hozzáférési felülvizsgálat **Áttekintés lapján.** Az [ellenőrzés befejezéséig](pim-resource-roles-complete-access-review.md)a címtárban nem módosulnak a hozzáférési jogok.

![Access-értékelések áttekintő lapja, amely az értékelés részleteit mutatja](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Ha ez egy egyszeri felülvizsgálat, majd a hozzáférési felülvizsgálati időszak lejárta után, vagy a rendszergazda leállítja a hozzáférési felülvizsgálat, kövesse az [Azure-erőforrás-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-resource-roles-complete-access-review.md) az eredmények megtekintéséhez és alkalmazásához.  

A hozzáférési felülvizsgálatok sorozatának kezeléséhez keresse meg a hozzáférési felülvizsgálatot, és megtalálja a közelgő előfordulásokat az ütemezett ellenőrzésekben, és ennek megfelelően szerkesztheti a befejezési dátumot, vagy ennek megfelelően hozzáadó/távolíthatja el az ellenőrzőket.

A **Befejezéskor beállításban**megadott beállítások alapján az automatikus alkalmazás az ellenőrzés záró dátuma után kerül végrehajtásra, vagy amikor manuálisan leállítja az értékelést. A felülvizsgálat állapota **befejezettről** köztes állapotokra változik, például **a Kiegyenlítés** és végül az **Alkalmazott**állapotra. A megtagadott felhasználók , ha vannak ilyenek, néhány percen belül eltávolításra kerülnek a szerepkörökből.

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrás-szerepkörökhöz való hozzáférés áttekintése](pim-resource-roles-perform-access-review.md)
- [Az Azure-erőforrás-szerepkörök hozzáférés-felülvizsgálatának befejezése](pim-resource-roles-complete-access-review.md)
- [Azure AD-szerepkörök hozzáférési felülvizsgálatának létrehozása](pim-how-to-start-security-review.md)
