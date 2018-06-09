---
title: Hogyan kell elindítani egy áttekintése |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy áttekintése a kiemelt jogosultságú identitások az Azure Privileged Identity Management alkalmazással.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: protection
ms.date: 05/04/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 699076a8e651dd8dbbab08b518d7be7e60a0ac47
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233672"
---
# <a name="how-to-start-an-access-review-in-azure-ad-privileged-identity-management"></a>Az Azure AD Privileged Identity Management egy hozzáférési felülvizsgálat indítása
Szerepkör-hozzárendelések "elavult" válnak, amikor a felhasználók privilegizált hozzáférést, amelyek többé nem kell. Ahhoz, hogy ezek elavult szerepkör-hozzárendelések a kockázatának csökkentéséhez kiemelt szerepkörű rendszergazda rendszeresen tekintse át a szerepköröket, amelyek a felhasználóknak. Ez a dokumentum egy hozzáférés-ellenőrzés indítása az Azure AD Privileged Identity Management (PIM) a lépéseket ismerteti.

## <a name="start-an-access-review"></a>Hozzáférési felülvizsgálat indítása
> [!NOTE]
> Ha a PIM alkalmazást az irányítópulton nem adott meg az Azure portálon, olvassa el a [Ismerkedés az Azure Privileged Identity Management szolgáltatással](active-directory-privileged-identity-management-getting-started.md)
> 
> 

A PIM-alkalmazás fő lapján, a három módja van egy hozzáférés-ellenőrzés indítása:

* **Hozzáférési értékelést** > **hozzáadása**
* **Szerepkörök** > **felülvizsgálati** gomb
* Válassza ki a megfelelő szerepkört a szerepkörök listából vizsgálni > **felülvizsgálati** gomb

Elemre a **tekintse át** gombra, a **egy hozzáférés-ellenőrzés indítása** panel jelenik meg. A panel fog a felülvizsgálati állítson be egy nevet és a határidő, válassza ki a szerepkört, és döntse el, akik végrehajtják a felülvizsgálati.

![Indítsa el az áttekintése – képernyőkép][1]

### <a name="configure-the-review"></a>A felülvizsgálati konfigurálása
Hozzon létre egy áttekintése, szüksége egy kezdő és záró dátumát, és adjon neki nevet.

![Konfigurálja a felülvizsgálati – képernyőkép][2]

Ellenőrizze a elég hosszú a felhasználók számára meg ennek befejeződését, tekintse át a hosszát. Ha befejezte a záró dátum előtt, mindig leállíthatja a felülvizsgálati korai.

### <a name="choose-a-role-to-review"></a>Válassza ki a szerepkört áttekintése
Minden egyes felülvizsgálati csak egy szerepkör összpontosít. Kivéve, ha a áttekintése indította el egy adott szerepkör panel, szüksége lesz egy szerepkör most elemre.

1. Navigáljon a **tekintse át a szerepköri tagság**
   
    ![Tekintse át a szerepköri tagság – képernyőkép][3]
2. Válassza ki egy szerepkört a listából.

### <a name="decide-who-will-perform-the-review"></a>Döntse el, akik végrehajtják a felülvizsgálati
Ellenőrzés végrehajtása esetén három lehetőség áll rendelkezésre. A felülvizsgálati rendelhet más végezze, akkor megteheti ezt, vagy beállíthatja, hogy a minden felhasználó, tekintse át a saját hozzáférésüket.

1. Navigáljon a **felülvizsgálók kiválasztása**
   
    ![Válassza ki a felülvizsgálók – képernyőkép][4]
2. A lehetőségek közül választhat:
   
   * **Válassza ki a felülvizsgáló**: használja ezt a beállítást, ha nem biztos benne, aki hozzá kell férnie. Ezzel a beállítással a felülvizsgálati hozzárendelése egy erőforrás tulajdonosa vagy a csoport kezelőjének befejezéséhez.
   * **A nekem**: hasznos, ha meg kívánja tekinteni, hogyan hozzáférést ellenőrzi, hogy a munkahelyi, vagy meg szeretné tekinteni a személyeket, akik nem nevében.
   * **Tagok áttekintése maguk**: használja ezt a beállítást szeretné, hogy a felhasználók, tekintse át a saját szerepkör-hozzárendelések.

### <a name="start-the-review"></a>Az ellenőrzés indítása
Végül lehetősége van a szükséges, hogy a felhasználók ha azok elfogadják a hozzáférését meg okot. Ha szeretné adjon meg egy leírást, a nézze át, és válassza ki **Start**.

Győződjön meg arról, hogy engedélyezi a felhasználóknak, hogy egy entitás áttekintése, és a megjelenítésükhöz [egy hozzáférési felülvizsgálat végrehajtása](active-directory-privileged-identity-management-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Kezelheti a áttekintése
Nyomon követheti a folyamatban, a felülvizsgálók végezze el az Azure AD PIM irányítópultján, az access értékelést szakaszban az ellenőrzéseket. Nincs hozzáférési jogosultsága módosulnak, amíg a címtár [a felülvizsgálati befejezése](active-directory-privileged-identity-management-how-to-complete-review.md).

Amíg a felülvizsgálati időszak alatt, emlékeztesse a felhasználók a felülvizsgálat befejezéséhez, vagy állítsa le a korai szakaszából hozzáférés értékelést áttekintése.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="pim-table-of-contents"></a>A PIM tartalomjegyzék
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_start_review.png
[2]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_configure.png
[3]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_role.png
[4]: ./media/active-directory-privileged-identity-management-how-to-start-security-review/PIM_review_reviewers.png
