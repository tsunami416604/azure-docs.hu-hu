---
title: A Privileged Identity Management végre az Azure-erőforrások hozzáférési felülvizsgálatok |} A Microsoft Docs
description: Ismerteti a hozzáférési felülvizsgálat indítása a Privileged Identity Management az Azure-erőforrások
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f88c4a2f7e6eb569c9c0de33ab86e8b484a923e3
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622881"
---
# <a name="perform-access-reviews-in-azure-resources-by-using-privileged-identity-management"></a>Hajtsa végre a hozzáférési felülvizsgálatok az Azure-erőforrások a Privileged Identity Management használatával
Szerepkör-hozzárendelések "elavult" válnak, amikor a felhasználók a privilegizált hozzáférés, amelyekre már nincs szükségük. A régi szerepkör-hozzárendelések társított kockázatának csökkentése érdekében, a kiemelt szerepkörű rendszergazdák szerepkörök rendszeresen tekintse át. Ez a dokumentum ismerteti a lépéseket az Azure-erőforrások a Privileged Identity Management (PIM) hozzáférési felülvizsgálat indítása.

A PIM-alkalmazás fő lapján lépjen a:

* **Hozzáférési felülvizsgálatokkal** > **hozzáadása**

![Adja hozzá a hozzáférési felülvizsgálatok](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Amikor kiválasztja a **Hozzáadás** gombra, a **hozzáférési felülvizsgálat létrehozása** panel jelenik meg. Ezen a panelen a felülvizsgálat konfigurálása neve és időbeli korlát, tekintse át, és válassza ki a felülvizsgálat tesz a munkakör kiválasztása.

![Hozzáférési felülvizsgálat létrehozása](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>A felülvizsgálat konfigurálása
Hozzáférési felülvizsgálat létrehozása, először neki, és állítsa a kezdő és záró dátumát.

![Konfigurálja a felülvizsgálati – képernyőkép](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Győződjön meg arról, túl hosszú ahhoz, a felhasználók számára, végezze el a felülvizsgálatot hosszát. A záró dátumnál korábbinak fejeződik be, ha azok mindig leállíthatja a felülvizsgálat korai.

### <a name="choose-a-role-to-review"></a>Tekintse át a munkakör kiválasztása
Kritika csak egy szerepkör összpontosít. Kivéve, ha egy adott szerepkör panelen kezdi a hozzáférési felülvizsgálatot, munkakör kiválasztása most kell.

1. Lépjen a **szerepkörtagság felülvizsgálata**
   
    ![Tekintse át a szerepkör tagságát – képernyőkép](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. A listából válasszon egy szerepkört.

### <a name="decide-who-will-perform-the-review"></a>Döntse el, akik végrehajtják a felülvizsgálat
Felülvizsgálat végrehajtásához három lehetőség áll rendelkezésre. Valaki más végrehajtásához rendelhet a felülvizsgálatot, akkor Ön is megteheti, vagy minden felhasználó a saját hozzáférését tekintheti át.

1. A lehetőségek közül választhat:
   
   * **A kijelölt felhasználók**: használja ezt a beállítást, ha nem ismeri ki hozzá kell férnie. Ezzel a beállítással a felülvizsgálat hozzárendelheti egy erőforrás tulajdonosa vagy a csoport kezelőjének végrehajtásához.
   * **Hozzárendelt (self)**: használja ezt a beállítást szeretné, hogy a felhasználók, ellenőrizze a saját szerepkör-hozzárendeléseket.
   
2. Lépjen a **felülvizsgálók kiválasztása**.
   
    ![Válassza ki a felülvizsgálók – képernyőkép](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>A felülvizsgálat indítása
Végül megkövetelheti, hogy a felhasználók biztosítani hagyja jóvá a hozzáférés okát. Igény szerint adjon meg egy leírást, a felülvizsgálat. Válassza ki **Start**.

Győződjön meg arról, hogy engedélyezi a felhasználóknak, hogy Várakozás azokat a hozzáférési felülvizsgálat, és nekik [hozzáférési felülvizsgálat végrehajtása](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése
A PIM az Azure-erőforrások irányítópultot követheti a folyamat állapotát, a felülvizsgálatot, végezze el az ellenőrzéseket. Nincs hozzáférési jogosultsága változnak, amíg a könyvtárban [a felülvizsgálat befejeződött](pim-resource-roles-complete-access-review.md).

Amíg viszonyt keresztül, emlékeztesse a felhasználókat, végezze el a felülvizsgálatot, vagy állítsa le a hozzáférési felülvizsgálatok szakasz a korai a felülvizsgálatot.

