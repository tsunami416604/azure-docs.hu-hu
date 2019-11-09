---
title: Azure AD-szerepkörök hozzáférési felülvizsgálatának létrehozása a PIM-ben – Azure AD | Microsoft Docs
description: Ismerje meg, hogyan hozhatja létre az Azure AD-szerepkörök hozzáférési felülvizsgálatát Azure AD Privileged Identity Management (PIM) alkalmazásban.
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
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847113"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Azure AD-szerepkörök hozzáférési felülvizsgálatának létrehozása Privileged Identity Management

Az elavult szerepkör-hozzárendelésekhez kapcsolódó kockázatok csökkentése érdekében rendszeresen tekintse át a hozzáférést. Azure AD Privileged Identity Management (PIM) használatával hozzáférési felülvizsgálatokat hozhat létre az emelt szintű Azure AD-szerepkörökhöz. Az ismétlődő hozzáférési felülvizsgálatokat is konfigurálhatja, amelyek automatikusan megtörténnek.

Ez a cikk azt ismerteti, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatot az emelt szintű Azure AD-szerepkörökhöz.

## <a name="prerequisites"></a>Előfeltételek

[Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Hozzáférési felülvizsgálatok megnyitása

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a Kiemelt szerepkörű rendszergazda szerepkörnek.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. A bal oldali menüben válassza az **Azure ad-szerepkörök** lehetőséget, majd válassza a **hozzáférési felülvizsgálatok**lehetőséget.

1. A kezelés területen válassza a **hozzáférési felülvizsgálatok**lehetőséget.

    ![Azure AD-szerepkörök – hozzáférési felülvizsgálatok listája az összes felülvizsgálat állapotának megjelenítéséhez](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat elindítása

Miután megadta a hozzáférési felülvizsgálat beállításait, válassza az **Indítás**lehetőséget. A hozzáférési felülvizsgálat megjelenik a listában az állapotának jelzésével.

![Az elindított felülvizsgálatok állapotát megjelenítő hozzáférési felülvizsgálatok listája](./media/pim-how-to-start-security-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD egy e-mailt küld a felülvizsgálók számára röviddel a felülvizsgálat elindítása után. Ha úgy dönt, hogy nem szeretné elküldeni az Azure AD-t az e-mail-címre, tájékoztassa a véleményezőket arról, hogy a hozzáférési felülvizsgálat a befejezésre vár. Megtekintheti azokat az utasításokat, amelyekkel [ellenőrizheti az Azure ad-szerepkörök elérését](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése

Nyomon követheti a folyamat előrehaladását, mivel a felülvizsgálók a hozzáférési felülvizsgálat **Áttekintés** lapján tölthetik le az értékeléseket. A címtárban nem módosulnak hozzáférési jogosultságok, amíg a felülvizsgálat be nem [fejeződik](pim-how-to-complete-review.md).

![A felülvizsgálat részleteit megjelenítő hozzáférési felülvizsgálatok áttekintése lap](./media/pim-how-to-start-security-review/access-review-overview.png)

Ha ez egy egyszeri felülvizsgálat, akkor a hozzáférési felülvizsgálati időszak lejárta után, vagy a rendszergazda leállítja a hozzáférési felülvizsgálatot, kövesse az [Azure ad-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-how-to-complete-review.md) az eredmények megtekintéséhez és alkalmazásához című témakör lépéseit.  

A hozzáférési felülvizsgálatok sorozatának kezeléséhez navigáljon a hozzáférési felülvizsgálathoz, és keresse meg az ütemezett felülvizsgálatok közelgő eseményeit, és szerkessze a befejezési dátumot, vagy adja hozzá/távolítsa el a felülvizsgálók ennek megfelelően.

A **befejezési beállításokban megadott beállítások**alapján az automatikus alkalmazás a felülvizsgálat befejezési dátuma vagy a felülvizsgálat manuális leállítása után lesz végrehajtva. A felülvizsgálat állapota a **befejezéstől** kezdve a közbenső állapotok, például az **alkalmazás** és végül az állapot **alkalmazásával változik.** A megtagadott felhasználókat, ha vannak ilyenek, néhány percen belül el kell távolítani a szerepkörökből.

## <a name="next-steps"></a>További lépések

- [Azure AD-szerepkörök hozzáférésének áttekintése](pim-how-to-perform-security-review.md)
- [Azure AD-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-how-to-complete-review.md)
- [Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása](pim-resource-roles-start-access-review.md)
