---
title: Az Azure AD-szerepkörök hozzáférés-felülvizsgálatának létrehozása a PIM- Azure AD-ben | Microsoft dokumentumok
description: Megtudhatja, hogyan hozhat létre access review az Azure AD-szerepkörök az Azure AD kiemelt identitáskezelés (PIM) használatával.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f7e9ef503a9a3469ecbc835be8d9229fbd0167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847113"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Access-ellenőrzés létrehozása az Azure AD-szerepkörökről a kiemelt identitáskezelésben

Az elavult szerepkör-hozzárendelések kockázatának csökkentése érdekében rendszeresen tekintse át a hozzáférést. Az Azure AD emelt szintű identitáskezelés (PIM) használatával hozzáférési felülvizsgálatokat hozhat létre a kiemelt Azure AD-szerepkörökhöz. Az automatikusan előforduló ismétlődő hozzáférési felülvizsgálatokat is beállíthatja.

Ez a cikk ismerteti, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatok emelt szintű Azure AD-szerepkörök.

## <a name="prerequisites"></a>Előfeltételek

[Kiemelt szerepkör-rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Nyílt hozzáférésű felülvizsgálatok

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) egy olyan felhasználóval, aki a kiemelt szerepkör-rendszergazdai szerepkör tagja.

1. Nyissa meg **az Azure AD kiemelt identitáskezelés .**

1. A bal oldali menüben válassza az **Azure AD-szerepköröket,** majd válassza **az Access-felülvizsgálatok**lehetőséget.

1. A Kezelés csoportban válassza az **Access-ellenőrzések lehetőséget.**

    ![Azure AD-szerepkörök – Access-vélemények listája az összes értékelés állapotát megjelenítő listában](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat indítása

Miután megadta a hozzáférési felülvizsgálat beállításait, válassza a **Start**lehetőséget. A hozzáférési felülvizsgálat megjelenik a listában az állapotjelzővel.

![Hozzáférési felülvizsgálatok listája az elindított ellenőrzések állapotát megjelenítő listában](./media/pim-how-to-start-security-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD e-mailt küld az ellenőrzőknek röviddel az ellenőrzés megkezdése után. Ha úgy dönt, hogy nem az Azure AD küldje el az e-mailt, győződjön meg róla, hogy tájékoztassa az ellenőrzők, hogy egy hozzáférési felülvizsgálat vár rájuk, hogy teljes. Megjelenítheti nekik az [Azure AD-szerepkörökhöz való hozzáférés áttekintésére](pim-how-to-perform-security-review.md)vonatkozó utasításokat.

## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése

Nyomon követheti a folyamatot, ahogy az ellenőrzők befejezik az ellenőrzéseket a hozzáférési felülvizsgálat **Áttekintés lapján.** Az [ellenőrzés befejezéséig](pim-how-to-complete-review.md)a címtárban nem módosulnak a hozzáférési jogok.

![Access-értékelések áttekintő lapja, amely az értékelés részleteit mutatja](./media/pim-how-to-start-security-review/access-review-overview.png)

Ha ez egy egyszeri felülvizsgálat, majd a hozzáférési felülvizsgálati időszak lejárta után, vagy a rendszergazda leállítja a hozzáférési felülvizsgálat, kövesse az [Azure AD-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-how-to-complete-review.md) az eredmények megtekintéséhez és alkalmazásához.  

A hozzáférési felülvizsgálatok sorozatának kezeléséhez keresse meg a hozzáférési felülvizsgálatot, és megtalálja a közelgő előfordulásokat az ütemezett ellenőrzésekben, és ennek megfelelően szerkesztheti a befejezési dátumot, vagy ennek megfelelően hozzáadó/távolíthatja el az ellenőrzőket.

A **Befejezéskor beállításban**megadott beállítások alapján az automatikus alkalmazás az ellenőrzés záró dátuma után kerül végrehajtásra, vagy amikor manuálisan leállítja az értékelést. A felülvizsgálat állapota **befejezettről** köztes állapotokra változik, például **a Kiegyenlítés** és végül az **Alkalmazott**állapotra. A megtagadott felhasználók , ha vannak ilyenek, néhány percen belül eltávolításra kerülnek a szerepkörökből.

## <a name="next-steps"></a>További lépések

- [Az Azure AD-szerepkörökhöz való hozzáférés áttekintése](pim-how-to-perform-security-review.md)
- [Azure AD-szerepkörök hozzáférés-felülvizsgálatának befejezése](pim-how-to-complete-review.md)
- [Azure-erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása](pim-resource-roles-start-access-review.md)
