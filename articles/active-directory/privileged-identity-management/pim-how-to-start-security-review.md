---
title: Az Azure AD-címtárbeli szerepkörökhöz tartozó hozzáférési felülvizsgálat indítása a PIM |} A Microsoft Docs
description: Útmutató az Azure AD-címtárbeli szerepkörökhöz tartozó hozzáférési felülvizsgálat indítása az Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 1cf3d29bd3d7af56f3e20c1d2344ed90095229bf
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196667"
---
# <a name="start-an-access-review-for-azure-ad-directory-roles-in-pim"></a>A PIM az Azure AD-címtárbeli szerepkörökhöz tartozó hozzáférési felülvizsgálat indítása
Szerepkör-hozzárendelések "elavult" válnak, amikor a felhasználók a privilegizált hozzáférés, amelyekre már nincs szükségük. A régi szerepkör-hozzárendelések, a kiemelt szerepkörű a kockázatának csökkentése érdekében vagy globális rendszergazdái rendszeresen készítsen tehet fel és rendszergazdák számára, tekintse át a szerepkörök rendelkező felhasználók a hozzáférési felülvizsgálatok. Ez a dokumentum ismerteti a lépéseket a hozzáférési felülvizsgálat indítása az Azure AD Privileged Identity Management (PIM).

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

- [A PIM az Azure AD-címtárbeli szerepkörökhöz tartozó hozzáférési felülvizsgálat befejezése](pim-how-to-complete-review.md)
- [A saját Azure AD-címtárbeli szerepkörök hozzáférési felülvizsgálatának végrehajtása a PIM szolgáltatásban](pim-how-to-perform-security-review.md)
- [A PIM az Azure-erőforrások szerepköreihez tartozó hozzáférési felülvizsgálat indítása](pim-resource-roles-start-access-review.md)
