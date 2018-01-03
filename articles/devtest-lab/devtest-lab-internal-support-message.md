---
title: "Egy belső támogatási nyilatkozattal hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban |} Microsoft Docs"
description: "Útmutató egy tesztlabor egy belső támogató utasítást visszaküldeni a Azure DevTest Labs szolgáltatásban"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: c9900333-6c5e-4d7d-b0f4-889015e9550c
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: v-craic
ms.openlocfilehash: 33c2f1e9d1ffa83003b7f0dbba46a7a4588dd57a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Egy belső támogatási nyilatkozattal hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban

Azure DevTest Labs segítségével testre szabhatja a labor egy belső támogató utasítással, amely a labor támogatási információkat biztosít a felhasználók. Megadhatja például kapcsolattartási adatait, hogy a felhasználó meg tudja elérni a belső támogató, amikor hibaelhárítási erőforrások vagy eléréséhez a laborban segítségre van szükségük. Belső webhelyek vagy – gyakori kérdések, amelyekhez a csapat előtt lépjen kapcsolatba az ügyfélszolgálattal hozzáférhet mutató hivatkozásokat is megadhatja.

Egy belső támogatási nyilatkozattal olyan lehetővé teszik, hogy a labor információk, amelyek általában túl gyakran nem változik. Értesíteni a felhasználókat a labor adatait, amely több átmeneti jellegű – például a legújabb frissítéseket a labor házirendek – lásd: [Post közlemény laboratóriumi](devtest-lab-announcements.md).

Egyszerűen tiltsa le vagy támogatási nyilatkozattal szerkesztése után már nem alkalmazható.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Támogatási nyilatkozattal hozzáadása egy meglévő labor lépései

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, jelölje be **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából. (Előfordulhat, hogy az irányítópult már látható a labor **összes erőforrás**).
1. Válassza ki a labor, amelyben hozzáadandó támogatási nyilatkozattal labs listájának megtekintéséhez.  
1. A tesztlabor a **áttekintése** területen válassza **konfigurációs és házirendek**.  

    ![Konfigurációs és házirendek gomb](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. A bal oldali alatt **beállítások**, jelölje be **belső támogató**.

    ![Belső támogató gomb](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. A felhasználókhoz tartozó belső támogató üzenet létrehozása az ebben a tesztkörnyezetben, állítsa engedélyezve **Igen**.

1. Az a **támogatási üzenet** mezőbe írja be a belső támogató utasítást, amely be kívánja mutatni a labor felhasználók számára. A támogatási üzenet Markdown fogad el. Az üzenet szövege megadásakor, megtekintheti a **előzetes** terület megtekintéséhez, hogy az üzenet újból megjelenik, a felhasználók számára a képernyő alján.

    ![Belső támogató képernyő hozza létre az üzenetet.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Válassza ki **mentése** után a támogatási nyilatkozattal utáni készen áll.

Ha már nem kívánja megjeleníteni a támogatási üzenet labor felhasználók számára, térjen vissza a **belső támogató** lapon, és állítsa be **engedélyezve** való **nem**.

## <a name="steps-for-users-to-view-the-support-message"></a>A felhasználók számára a támogatási megtekintésére lépései

1. Az a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040), válassza ki a labor.

1. A tesztlabor a **áttekintése** területen válassza **belső támogató**.  

    ![Belső támogató gomb](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Ha egy támogatási üzenet van, a felhasználó tekintheti a belső támogató panelről.

    ![Belső támogató ablaktábla támogatási üzenet eseményei](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* Belső támogatási kimutatásai általában segítségével adja meg, amelyek ritkán módosulnak támogatási információkat. Azt is megtudhatja hogyan [egy laborhoz közlemény utáni](devtest-lab-announcements.md) felhasználókkal ideiglenes módosításaiért és frissítéseiért a laborkörnyezetben.
* [Állítsa be a házirendek és ütemezések](devtest-lab-set-lab-policy.md) ismerteti, hogyan alkalmazhat egyéb korlátozások és egyezmények az előfizetés testreszabott házirendekkel.