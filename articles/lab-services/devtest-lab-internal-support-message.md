---
title: Egy belső támogató utasítás hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet |} A Microsoft Docs
description: Ismerje meg, hogyan lehet egy belső támogatási nyilatkozattal laborba közzététele az Azure DevTest Labs szolgáltatásban
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 2d12ca26fb2aa5abddcf44b2e634b2f08b1fb01b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696250"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Egy belső támogató utasítás hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet

Az Azure DevTest Labs lehetővé teszi, hogy a tesztkörnyezet egy belső támogató utasítással, amely a labor támogatási információkat nyújt a felhasználók számára testreszabható. Például megadhat kapcsolattartási adatokat, hogy a felhasználó meg tudja elérni a belső támogató, amikor szükségük van az hibaelhárítása vagy a laborkörnyezetben található erőforrások elérésével kapcsolatban. Belső webhelyek vagy a csapata által elérhető, mielőtt kapcsolatba lépne a támogatás – gyakori kérdések mutató hivatkozásokat is megadhatja.

Egy belső támogató utasítás célja, hogy a közzététel tesztlabor információkat, amelyek általában túl gyakran nem változnak. Labor adatait, amely több átmeneti jellegű – például a legutóbbi frissítéseinek laborszabályzatok – felhasználók értesítése az lásd [tesztkörnyezetben közleményben](devtest-lab-announcements.md).

Könnyedén letilthatja vagy támogatási nyilatkozattal szerkesztése után már nem alkalmazható.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Egy meglévő laborhoz támogatási nyilatkozattal hozzáadásának lépéseit

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, válassza ki a **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából. (Előfordulhat, hogy a tesztkörnyezet már látható az irányítópult alatt **összes erőforrás**).
1. Tesztkörnyezetek listájában jelölje ki a labor létrehozása, amelyben a támogatási nyilatkozattal hozzáadni kívánt.  
1. A laborgyakorlat **áttekintése** területen válassza **Konfigurace a zásady**.  

    ![Konfigurace a zásady gomb](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. A bal oldali alatt **beállítások**válassza **belső támogató**.

    ![Belső támogató gomb](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Létrehozhat egy belső támogató a felhasználók számára a laborban, állítsa engedélyezve **Igen**.

1. Az a **támogatási üzenet** mezőben adja meg a kívánt nyújtjuk a labor felhasználók belső támogatási nyilatkozattal. A támogatási üzenet Markdown fogad el. Mivel ad meg, hogy az üzenet szövegét, megtekintheti a **előzetes** területén megtekintheti, hogy az üzenet újból megjelenik, a felhasználók számára a képernyő alján.

    ![Az üzenet létrehozása belső támogató képernyő.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Válassza ki **mentése** elkészült a támogatási nyilatkozattal közzététele.

Ha már nem szeretné a támogatási üzenet megjelenítése a labor felhasználónak, térjen vissza a **belső támogató** lapon, és állítsa be **engedélyezve** való **nem**.

## <a name="steps-for-users-to-view-the-support-message"></a>A felhasználók számára a támogatási megtekintésére lépések

1. Az a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040), válassza ki a labor.

1. A laborgyakorlat **áttekintése** területen válassza **belső támogató**.  

    ![Belső támogató gomb](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Támogatási üzenetet tesznek közzé, ha a felhasználó megtekintheti a azt a belső támogató panelről.

    ![Belső támogató paneljéről, amelyen látható a támogatási üzenetet tesznek közzé](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* Belső támogatási nyilatkozatok jellemzően nem változik, hogy milyen gyakran támogatási információkat tartalmaznak. Azt is megtudhatja, hogyan [közzététele egy közlemény egy laborhoz](devtest-lab-announcements.md) ideiglenes módosításaiért vagy frissítéseiért a labor felhasználókkal.
* [Szabályzatok és ütemezések beállítása](devtest-lab-set-lab-policy.md) ismerteti, hogyan alkalmazhatja egyéb korlátozások és egyezmények az előfizetésében testreszabott házirendek használatával.