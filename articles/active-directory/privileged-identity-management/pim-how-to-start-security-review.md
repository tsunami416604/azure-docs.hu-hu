---
title: Az Azure AD-szerepkörök hozzáférési felülvizsgálat létrehozása az Azure Active Directory - a PIM |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre az Azure AD-szerepkörök hozzáférési felülvizsgálatok az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/27/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e69bebfaeb28709b43ea74b7d33289426371a2bf
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476452"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-pim"></a>A PIM az Azure AD-szerepkörök hozzáférési felülvizsgálat létrehozása

A hozzáférést az Azure AD-szerepkörök alkalmazott módosítások rendszerjogosultságú idővel. Elavult szerepkör-hozzárendeléseit a kockázatának csökkentése érdekében rendszeresen ellenőrizzék a hozzáférést. Használhatja az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) emelt szintű Azure AD-szerepkörök hozzáférési felülvizsgálatok létrehozásához. Ismétlődő a hozzáférési felülvizsgálatok automatikusan is konfigurálhatja.

Ez a cikk azt ismerteti, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatok az Azure AD-szerepkörök emelt szintű.

## <a name="prerequisites"></a>Előfeltételek

- [A kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Nyissa meg a hozzáférési felülvizsgálatok

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy felhasználóval, amely a kiemelt szerepkörű rendszergazda szerepkör tagja.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. A bal oldali menüben kattintson a **Azure AD-szerepkörök** majd **hozzáférési felülvizsgálatokkal**.

1. Kattintson a kezelés, **hozzáférési felülvizsgálatokkal**.

    ![Az Azure AD-szerepkörök – a hozzáférési felülvizsgálatok listáját megjelenítő az összes értékelést állapotát](./media/pim-how-to-start-security-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat indítása

Miután megadta a hozzáférési felülvizsgálat beállításait, kattintson a **Start**. A hozzáférési felülvizsgálat megjelenik a listában, az azt jelzi, hogy annak állapotát.

![A hozzáférési felülvizsgálatok listáját megjelenítő elindított értékelések állapotát](./media/pim-how-to-start-security-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD-e-mailt küld felülvizsgálók elindítja a felülvizsgálatot követően rövid időn belül. Ha nem rendelkezik Azure ad-ben az e-mailt, mindenképpen tájékoztatja a felülvizsgálatot, amely a hozzáférési felülvizsgálat várakozik, amíg befejeződnek. Mutathat nekik az utasításokat, hogy hogyan [tekintse át az Azure AD-szerepkörök hozzáférési](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése

Követheti a folyamat állapotát, a felülvizsgálók a legteljesebb körű áttekintette a **áttekintése** a hozzáférési felülvizsgálat lapján. Nincs hozzáférési jogosultsága a könyvtárban, amíg nem módosítják a [felülvizsgálat befejezése](pim-how-to-complete-review.md).

![Hozzáférési felülvizsgálat részleteit megjelenítő áttekintés lap](./media/pim-how-to-start-security-review/access-review-overview.png)

Ha ez egy egyszeri tekintse át, majd a hozzáférési felülvizsgálati időszak felett van, vagy a rendszergazda a hozzáférési felülvizsgálat leállítása után kövesse a lépéseket a [Azure AD-szerepkörök hozzáférési felülvizsgálat befejezése](pim-how-to-complete-review.md) megtekintéséhez és a alkalmazni az eredményeket.  

Kezelheti a hozzáférést egy sorozatát értékelések, keresse meg a hozzáférési felülvizsgálatot, és hoz közelgő események keresése az ütemezett ellenőrzések, és módosítsa a záró dátumot vagy hozzáadása/eltávolítása felülvizsgálóknak annak megfelelően.

A beállításokat az alapján **befejezést követő művelet beállításai**, automatikus alkalmazása lesz a felülvizsgálat záró dátuma, vagy ha manuálisan leállítja a felülvizsgálatot követően hajtható végre. A felülvizsgálat állapota változik **befejezve** például köztes állapotok keresztül **alkalmazása** végül állapotba **alkalmazott**. Tekintse meg a letiltott felhasználók, ha van ilyen távolít el szerepköröket néhány perc alatt kell látnia.

## <a name="next-steps"></a>További lépések

- [Hozzáférés az Azure AD-szerepkörök felülvizsgálata](pim-how-to-perform-security-review.md)
- [Az Azure AD-szerepkörök hozzáférési felülvizsgálat befejezése](pim-how-to-complete-review.md)
- [Az Azure-erőforrásszerepkörök hozzáférési felülvizsgálat létrehozása](pim-resource-roles-start-access-review.md)
