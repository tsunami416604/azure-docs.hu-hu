---
title: A PIM hozzáférés ellenőrzés indítása az Azure-erőforrások |} Microsoft Docs
description: Ismerteti, hogyan egy hozzáférés-ellenőrzés indítása a Privileged Identity Management az Azure-erőforrások
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 61ed4e82e0b782b423668564dae6efb272967702
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---start-access-review"></a>Privileged Identity Management - szerepkör erőforrás - Start áttekintése
Szerepkör-hozzárendelések "elavult" válnak, amikor a felhasználók privilegizált hozzáférést, amelyek többé nem kell. Ahhoz, hogy ezek elavult szerepkör-hozzárendelések a kockázatának csökkentéséhez kiemelt szerepkörű rendszergazda rendszeresen tekintse át a szerepköröket, amelyek a felhasználóknak. Ez a dokumentum a Privileged Identity Management (PIM) egy hozzáférés-ellenőrzés indítása az Azure-erőforrások lépéseket ismerteti.

A PIM-alkalmazás fő lapján keresse meg:

* **Hozzáférési értékelést** > **hozzáadása**

![](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Elemre a **Hozzáadás** gombra, a **hozzon létre egy áttekintése** panel jelenik meg. A panel fog a felülvizsgálati állítson be egy nevet és a határidő, válassza ki a szerepkört, és döntse el, akik végrehajtják a felülvizsgálati.

![](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>A felülvizsgálati konfigurálása
Hozzon létre egy áttekintése, szüksége egy kezdő és záró dátumát, és adjon neki nevet.

![Konfigurálja a felülvizsgálati – képernyőkép](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Ellenőrizze a elég hosszú a felhasználók számára meg ennek befejeződését, tekintse át a hosszát. Ha befejezte a záró dátum előtt, mindig leállíthatja a felülvizsgálati korai.

### <a name="choose-a-role-to-review"></a>Válassza ki a szerepkört áttekintése
Minden egyes felülvizsgálati csak egy szerepkör összpontosít. Kivéve, ha a áttekintése indította el egy adott szerepkör panel, szüksége lesz egy szerepkör most elemre.

1. Navigáljon a **tekintse át a szerepköri tagság**
   
    ![Tekintse át a szerepköri tagság – képernyőkép](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Válassza ki egy szerepkört a listából.

### <a name="decide-who-will-perform-the-review"></a>Döntse el, akik végrehajtják a felülvizsgálati
Ellenőrzés végrehajtása esetén három lehetőség áll rendelkezésre. A felülvizsgálati rendelhet más végezze, akkor megteheti ezt, vagy beállíthatja, hogy a minden felhasználó, tekintse át a saját hozzáférésüket.

1. A lehetőségek közül választhat:
   
   * **A kijelölt felhasználók**: használja ezt a beállítást, ha nem biztos benne, aki hozzá kell férnie. Ezzel a beállítással a felülvizsgálati hozzárendelése egy erőforrás tulajdonosa vagy a csoport kezelőjének befejezéséhez.
   * **Hozzárendelt (önálló)**: használja ezt a beállítást szeretné, hogy a felhasználók, tekintse át a saját szerepkör-hozzárendelések.
   
2. Navigáljon a **felülvizsgálók kiválasztása**
   
    ![Válassza ki a felülvizsgálók – képernyőkép](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Az ellenőrzés indítása
Végül lehetősége van a szükséges, hogy a felhasználók ha azok elfogadják a hozzáférését meg okot. Ha szeretné adjon meg egy leírást, a nézze át, és válassza ki **Start**.

Győződjön meg arról, hogy engedélyezi a felhasználóknak, hogy egy entitás áttekintése, és a megjelenítésükhöz [egy hozzáférési felülvizsgálat végrehajtása](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Kezelheti a áttekintése
A felülvizsgálók készként áttekintette a PIM Azure-erőforrások irányítópulton a hozzáférés értékelést csoportban, nyomon követheti a folyamatban. Nincs hozzáférési jogosultsága módosulnak, amíg a címtár [a felülvizsgálati befejezése](pim-resource-roles-complete-access-review.md).

Amíg a felülvizsgálati időszak alatt, emlékeztesse a felhasználók a felülvizsgálat befejezéséhez, vagy állítsa le a korai szakaszából hozzáférés értékelést áttekintése.

