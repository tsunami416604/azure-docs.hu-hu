---
title: Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása a PIM-Azure Active Directoryban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre hozzáférési felülvizsgálatot az Azure Resource roles szolgáltatásban Azure AD Privileged Identity Management (PIM) alkalmazásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/29/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba674c143cd8d85b9764c8ade776b52dd1d3598d
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804156"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-pim"></a>Azure-beli erőforrás-szerepkörök hozzáférési felülvizsgálatának létrehozása a PIM-ben

A Kiemelt Azure-beli erőforrás-szerepkörökhöz való hozzáférés az alkalmazottak számára időbeli változásokkal. Az elavult szerepkör-hozzárendelésekhez kapcsolódó kockázatok csökkentése érdekében rendszeresen tekintse át a hozzáférést. Azure Active Directory (Azure AD) Privileged Identity Management (PIM) használatával hozzáférési felülvizsgálatokat hozhat létre az emelt szintű Azure-erőforrás-szerepkörökhöz. Az ismétlődő hozzáférési felülvizsgálatokat is konfigurálhatja, amelyek automatikusan megtörténnek.

Ez a cikk azt ismerteti, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatot a Kiemelt Azure-beli erőforrás-szerepkörökhöz.

## <a name="prerequisites"></a>Előfeltételek

- [Kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Hozzáférési felülvizsgálatok megnyitása

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com/) egy olyan felhasználóval, aki tagja a Kiemelt szerepkörű rendszergazda szerepkörnek.

1. Nyissa meg **Azure ad Privileged Identity Management**.

1. A bal oldali menüben kattintson az **Azure-erőforrások**elemre.

1. Kattintson a kezelni kívánt erőforrásra, például egy előfizetésre vagy egy felügyeleti csoportra.

1. A kezelés alatt kattintson a **hozzáférési felülvizsgálatok**elemre.

    ![Azure-erőforrások – hozzáférési felülvizsgálatok listája az összes felülvizsgálat állapotának megjelenítéséhez](./media/pim-resource-roles-start-access-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat elindítása

Miután megadta a hozzáférési felülvizsgálat beállításait, kattintson a **Start**gombra. A hozzáférési felülvizsgálat megjelenik a listában az állapotának jelzésével.

![Az elindított felülvizsgálati állapotot megjelenítő hozzáférési felülvizsgálatok listája](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD egy e-mailt küld a felülvizsgálók számára röviddel a felülvizsgálat elindítása után. Ha úgy dönt, hogy nem szeretné elküldeni az Azure AD-t az e-mail-címre, tájékoztassa a véleményezőket arról, hogy a hozzáférési felülvizsgálat a befejezésre vár. Megtekintheti azokat az utasításokat, amelyekkel [áttekintheti az Azure-erőforrás szerepköreinek elérését](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése

Nyomon követheti a folyamat előrehaladását, mivel a felülvizsgálók a hozzáférési felülvizsgálat **Áttekintés** lapján tölthetik le az értékeléseket. A címtárban nem módosulnak hozzáférési jogosultságok, amíg a felülvizsgálat be nem [fejeződik](pim-resource-roles-complete-access-review.md).

![A felülvizsgálat részleteit megjelenítő hozzáférési felülvizsgálatok áttekintése lap](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Ha ez egy egyszeri felülvizsgálat, akkor a hozzáférési felülvizsgálati időszak lejárta után, vagy a rendszergazda leállítja a hozzáférési felülvizsgálatot, kövesse az [Azure-erőforrás-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-resource-roles-complete-access-review.md) az eredmények megtekintéséhez és alkalmazásához című témakör lépéseit.  

A hozzáférési felülvizsgálatok sorozatának kezeléséhez navigáljon a hozzáférési felülvizsgálathoz, és keresse meg az ütemezett felülvizsgálatok közelgő eseményeit, és szerkessze a befejezési dátumot, vagy adja hozzá/távolítsa el a felülvizsgálók ennek megfelelően.

A **befejezési beállításokban megadott beállítások**alapján az automatikus alkalmazás a felülvizsgálat befejezési dátuma vagy a felülvizsgálat manuális leállítása után lesz végrehajtva. A felülvizsgálat állapota a **befejezéstől** kezdve a közbenső állapotok, például az **alkalmazás** és végül az állapot **alkalmazásával változik.** A megtagadott felhasználókat, ha vannak ilyenek, néhány percen belül el kell távolítani a szerepkörökből.

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrás szerepköreihez való hozzáférés áttekintése](pim-resource-roles-perform-access-review.md)
- [Azure-erőforrás-szerepkörök hozzáférési felülvizsgálatának befejezése](pim-resource-roles-complete-access-review.md)
- [Azure AD-szerepkörök hozzáférési felülvizsgálatának létrehozása](pim-how-to-start-security-review.md)
