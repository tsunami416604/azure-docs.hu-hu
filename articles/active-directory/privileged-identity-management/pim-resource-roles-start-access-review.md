---
title: Hozzáférés-ellenőrzés végrehajtása az Azure-erőforrások Privileged Identity Management használatával |} Microsoft Docs
description: Ismerteti, hogyan egy hozzáférés-ellenőrzés indítása a Privileged Identity Management az Azure-erőforrások
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c47cdeab4edd0b8dbe9cfe7c388253d02a9148c9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699733"
---
# <a name="perform-access-reviews-in-azure-resources-by-using-privileged-identity-management"></a>Hozzáférés-ellenőrzés végrehajtása az Azure-erőforrások Privileged Identity Management használatával
Szerepkör-hozzárendelések "elavult" válnak, amikor a felhasználók privilegizált hozzáférést, amelyek többé nem kell. Ezek elavult szerepkör-hozzárendelések társított a kockázatok csökkentése érdekében kiemelt szerepkörű rendszergazda rendszeresen ellenőrizzék szerepkörök. Ez a dokumentum a Privileged Identity Management (PIM) egy hozzáférés-ellenőrzés indítása az Azure-erőforrások lépéseket ismerteti.

A PIM-alkalmazás fő lapján Ugrás:

* **Hozzáférési értékelést** > **hozzáadása**

![Hozzáférés értékelést hozzáadása](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Ha bejelöli a **Hozzáadás** gombra, a **hozzon létre egy áttekintése** panel jelenik meg. A panel a felülvizsgálati állítson be egy nevet és a határidő, tekintse át, és válassza ki a felülvizsgálati tesz szerepkör kiválasztása.

![Hozzáférési felülvizsgálat létrehozása](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>A felülvizsgálati konfigurálása
Hozzon létre egy áttekintése, először adjon neki nevet, és utána állítsa be a kezdő és záró dátumát.

![Konfigurálja a felülvizsgálati – képernyőkép](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Ellenőrizze a elég hosszú a felhasználók számára meg ennek befejeződését, tekintse át a hosszát. A lejárati dátuma előtt fejeződik be, ha azok mindig leállíthatja a felülvizsgálati korai.

### <a name="choose-a-role-to-review"></a>Válassza ki a szerepkört áttekintése
Minden egyes felülvizsgálati csak egy szerepkör összpontosít. Kivéve, ha a áttekintése indította el egy adott szerepkör panel, kell választani. most már a szerepkör.

1. Ugrás a **tekintse át a szerepköri tagság**
   
    ![Tekintse át a szerepköri tagság – képernyőkép](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Válassza ki egy szerepkört a listából.

### <a name="decide-who-will-perform-the-review"></a>Döntse el, akik végrehajtják a felülvizsgálati
Ellenőrzés végrehajtása esetén három lehetőség áll rendelkezésre. A felülvizsgálati rendelhet más végezze, azt megteheti ezt vagy minden felhasználó saját hozzáférés tekintse át.

1. A lehetőségek közül választhat:
   
   * **A kijelölt felhasználók**: használja ezt a beállítást, ha nem biztos benne, aki hozzá kell férnie. Ezzel a beállítással a felülvizsgálati hozzárendelése egy erőforrás tulajdonosa vagy a csoport kezelőjének befejezéséhez.
   * **Hozzárendelt (önálló)**: használja ezt a beállítást szeretné, hogy a felhasználók, tekintse át a saját szerepkör-hozzárendelések.
   
2. Ugrás a **ki felülvizsgálót**.
   
    ![Válassza ki a felülvizsgálók – képernyőkép](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Az ellenőrzés indítása
Végezetül megkövetelheti, hogy a felhasználók jóváhagyása a hozzáférés okát biztosítson. Ha szeretné adja meg a felülvizsgálati leírását. Válassza ki **Start**.

Győződjön meg arról, hogy engedélyezi a felhasználóknak, hogy egy entitás áttekintése, és a megjelenítésükhöz [egy hozzáférési felülvizsgálat végrehajtása](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Kezelheti a áttekintése
A PIM Azure-erőforrások irányítópulton nyomon követheti a folyamatban, a többi felhasználó végezze el az ellenőrzéseket. Nincs hozzáférési jogosultsága, módosulnak, amíg a címtár [befejezése után a felülvizsgálati](pim-resource-roles-complete-access-review.md).

Amíg a felülvizsgálati időszak alatt, emlékeztesse a felhasználók a felülvizsgálat befejezéséhez, vagy állítsa le a korai szakaszából hozzáférés értékelést áttekintése.

