---
title: Belső támogatási utasítás hozzáadása egy tesztkörnyezethez az Azure DevTest Labsben
description: Ismerje meg, hogyan könyvelhat egy belső támogatási nyilatkozatot egy laborban az Azure DevTest Labs-ben
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
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 591dcec36dc62143901d3b49db24196e84d58c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76170356"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Belső támogatási utasítás hozzáadása egy tesztkörnyezethez az Azure DevTest Labsben

Az Azure DevTest Labs lehetővé teszi, hogy testre szabhatja a labor egy belső támogatási nyilatkozatot, amely a felhasználók számára a labor támogatási információkat. Megadhat például kapcsolattartási adatokat, hogy a felhasználó tudja, hogyan érheti el a belső támogatást, ha segítségre van szüksége a tesztkörnyezetben lévő erőforrások hibaelhárításához vagy eléréséhez. Belső webhelyekre vagy gyakori kérdésekre mutató hivatkozásokat is megadhat, amelyeket a csapata az ügyfélszolgálattal való kapcsolatfelvétel előtt elérhet.

A belső támogatási utasítás célja, hogy lehetővé tegye a laboradatok, amelyek általában nem változnak túl gyakran. Ha értesíteni szeretné a felhasználókat az ideiglenesebb jellegű laboratóriumi adatokról – például a laborszabályzat legutóbbi frissítéseiről –, olvassa el a Közlemény közzététele laborban című [témakört.](devtest-lab-announcements.md)

A támogatási nyilatkozatokat egyszerűen letilthatja vagy szerkesztheti, miután az már nem alkalmazható.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Támogatási utasítás hozzáadása meglévő tesztkörnyezethez

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
1. Ha szükséges, válassza a **Minden szolgáltatás**lehetőséget, majd válassza a **DevTest Labs** elemet a listából. (Előfordulhat, hogy a tesztkörnyezet már látható az irányítópulton a **Minden erőforrás csoportban).**
1. A laborok listájából válassza ki azt a tesztkörnyezetet, amelyben támogatási nyilatkozatot szeretne hozzáadni.  
1. A tesztkörnyezet **áttekintése** területen válassza a **Konfiguráció és házirendek**lehetőséget.  

    ![Konfiguráció és házirendek gomb](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. A **beállítások**csoportbal válassza a **Belső támogatás**lehetőséget.

    ![Belső támogatás gomb](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Ha belső támogatási üzenetet szeretne létrehozni a tesztkörnyezetfelhasználói számára, állítsa az Engedélyezve értéket **Igen**értékre.

1. A **Támogatás üzenet** mezőbe írja be a belső támogatási utasítást, amelyet meg szeretne jelenmutatni a tesztkörnyezet felhasználóinak. A támogatási üzenet elfogadja a Markdown-t. Az üzenet szövegének beírásakor megtekintheti a képernyő alján található **Előnézet** területet, és megtekintheti, hogyan jelenik meg az üzenet a felhasználók számára.

    ![Belső támogatási képernyő az üzenet létrehozásához.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Válassza a **Mentés** lehetőséget, ha a támogatási nyilatkozat készen áll a közzétételre.

Ha már nem szeretné megjeleníteni ezt a támogatási üzenetet a tesztkörnyezet felhasználóinak, térjen vissza a **Belső támogatási** lapra, és állítsa **az Engedélyezve értéket** **Nem**értékre.

## <a name="steps-for-users-to-view-the-support-message"></a>A támogatási üzenet megtekintésének lépései a felhasználók számára

1. Az [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040)válasszon ki egy tesztkörnyezetet.

1. A labor **áttekintése** területen válassza a **Belső támogatás**lehetőséget.  

    ![Belső támogatás gomb](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Ha egy támogatási üzenet fel van véve, a felhasználó megtekintheti azt a Belső támogatási ablaktáblából.

    ![Belső támogatás ablaktábla a feladott támogatási üzenetet megjelenítő ablaktáblával](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* A belső támogatási utasítások általában olyan támogatási információk nyújtására szolgálnak, amelyek nem változtatják meg gyakran. Azt is megtudhatja, hogyan [tehet közzé egy közleményt egy tesztkörnyezetben,](devtest-lab-announcements.md) hogy tájékoztassa a felhasználókat az ideiglenes változásokról vagy frissítésekről a laborban.
* [A Házirendek és ütemezések beállítása](devtest-lab-set-lab-policy.md) szolgáltatás arról nyújt tájékoztatást, hogy miként alkalmazhat más korlátozásokat és konvenciókat az előfizetésben a testreszabott házirendek használatával.