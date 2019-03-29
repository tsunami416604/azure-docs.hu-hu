---
title: Az Azure AD-szerepkörök hozzáférési felülvizsgálat indítása a PIM - Azure Active Directory |} A Microsoft Docs
description: Útmutató az Azure AD-szerepkörök hozzáférési felülvizsgálat indítása az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5cbf96c165d79c26985663ef5a9d64bbf8f9892
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574994"
---
# <a name="start-an-access-review-for-azure-ad-roles-in-pim"></a>A PIM az Azure AD-szerepkörök hozzáférési felülvizsgálat indítása
Szerepkör-hozzárendelések "elavult" válnak, amikor a felhasználók a privilegizált hozzáférés, amelyekre már nincs szükségük. A régi szerepkör-hozzárendelések, a kiemelt szerepkörű a kockázatának csökkentése érdekében vagy globális rendszergazdái rendszeresen készítsen tehet fel és rendszergazdák számára, tekintse át a szerepkörök rendelkező felhasználók a hozzáférési felülvizsgálatok. Ez a dokumentum ismerteti a lépéseket a hozzáférési felülvizsgálat indítása az Azure Active Directory (Azure AD) Privileged Identity Management (PIM).

## <a name="start-an-access-review"></a>Hozzáférési felülvizsgálat indítása
> [!NOTE]
> Ha még nem adott hozzá az irányítópulthoz a PIM alkalmazást az Azure Portalon, tekintse meg a lépéseket a [Ismerkedés az Azure Privileged Identity Management](pim-getting-started.md)
> 
> 

A PIM-alkalmazás fő lapján, a három módja van hozzáférési felülvizsgálat indítása:

* **Hozzáférési felülvizsgálatokkal** > **hozzáadása**
* **Szerepkörök** > **felülvizsgálati** gomb
* Válassza ki a megfelelő szerepkört a szerepkörök listából programtulajdonos > **felülvizsgálati** gomb

Amikor rákattint a a **áttekintése** gombra, a **hozzáférési felülvizsgálat indítása** panel jelenik meg. Ezen panel fog konfigurálása a felülvizsgálat neve és időbeli korlát, tekintse át, majd döntse el, akik végrehajtják a felülvizsgálat munkakör kiválasztása.

![Hozzáférési felülvizsgálat indítása – képernyőkép](./media/pim-how-to-start-security-review/PIM_start_review.png)

### <a name="configure-the-review"></a>A felülvizsgálat konfigurálása
Hozzáférési felülvizsgálat létrehozása, meg kell neki, és állítsa be a kezdő és záró dátumát.

![Konfigurálja a felülvizsgálati – képernyőkép](./media/pim-how-to-start-security-review/PIM_review_configure.png)

Győződjön meg arról, túl hosszú ahhoz, a felhasználók számára, végezze el a felülvizsgálatot hosszát. Ha befejezte a záró dátum előtt, mindig állítsa le a felülvizsgálat korai.

### <a name="choose-a-role-to-review"></a>Tekintse át a munkakör kiválasztása
Kritika csak egy szerepkör összpontosít. Kivéve, ha egy adott szerepkör panelen kezdi a hozzáférési felülvizsgálatot, kell most válasszon egy szerepkört.

1. Navigáljon a **szerepkörtagság felülvizsgálata**
   
    ![Tekintse át a szerepkör tagságát – képernyőkép](./media/pim-how-to-start-security-review/PIM_review_role.png)
2. A listából válasszon egy szerepkört.

### <a name="decide-who-will-perform-the-review"></a>Döntse el, akik végrehajtják a felülvizsgálat
Felülvizsgálat végrehajtásához három lehetőség áll rendelkezésre. Valaki más végrehajtásához rendelhet a felülvizsgálatot, akkor Ön is megteheti, vagy minden felhasználó, tekintse át a saját hozzáférését is használhat.

1. Navigáljon a **felülvizsgálók kiválasztása**
   
    ![Válassza ki a felülvizsgálók – képernyőkép](./media/pim-how-to-start-security-review/PIM_review_reviewers.png)
2. A lehetőségek közül választhat:
   
   * **Válassza ki a felülvizsgáló**: Használja ezt a beállítást, ha nem ismeri ki hozzá kell férnie. Ezzel a beállítással a felülvizsgálat hozzárendelheti egy erőforrás tulajdonosa vagy a csoport kezelőjének végrehajtásához.
   * **Me**: Akkor hasznos, ha meg kívánja tekinteni, hogyan a hozzáférési felülvizsgálatok munka, vagy meg szeretné tekinteni azokat, akik nem nevében.
   * **A tagok tekintse át, egymás**: Ez a beállítás segítségével a felhasználók, tekintse át a saját szerepkör-hozzárendelések.

### <a name="start-the-review"></a>A felülvizsgálat indítása
Végül lehetősége van, hogy felhasználók adhatja meg, ha azok a hozzáférés jóváhagyása szükséges. Igény szerint adjon meg egy leírást, a felülvizsgálat, és válassza ki **Start**.

Győződjön meg arról, hogy engedélyezi a felhasználóknak, hogy Várakozás azokat a hozzáférési felülvizsgálat, és nekik [hozzáférési felülvizsgálat végrehajtása](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése
Nyomon követheti a folyamat állapotát, a felülvizsgálatot, végezze el az Azure AD PIM-irányítópulton, a hozzáférési felülvizsgálatok szakaszban ellenőrzéseket. Nincs hozzáférési jogosultsága módosulnak, amíg a könyvtárban [a felülvizsgálatot követően](pim-how-to-complete-review.md).

Amíg viszonyt keresztül, emlékeztesse a felhasználókat, végezze el a felülvizsgálatot, vagy állítsa le a hozzáférési felülvizsgálatok szakasz a korai a felülvizsgálatot.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>További lépések

- [Az Azure AD PIM-szerepkörök hozzáférési felülvizsgálat befejezése](pim-how-to-complete-review.md)
- [A PIM a saját Azure AD-szerepkörök hozzáférési felülvizsgálat végrehajtása](pim-how-to-perform-security-review.md)
- [A PIM az Azure-erőforrások szerepköreihez tartozó hozzáférési felülvizsgálat indítása](pim-resource-roles-start-access-review.md)
