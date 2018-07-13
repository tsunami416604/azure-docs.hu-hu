---
title: A labor-announcment közzététele az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan közlemény hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 3282a90069ecd119154df492ac6dc366d26b5300
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611205"
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>Egy közlemény egy laborhoz közzététele az Azure DevTest Labs szolgáltatásban

Tesztlabor-rendszergazdaként felteheti egy meglévő tesztkörnyezetben történő legutóbbi módosítása vagy bővítése a labor létrehozása a felhasználók értesítése egy egyéni közleményt. Például érdemes ellenére tájékoztassa a felhasználókat:

- Új Virtuálisgép-méretek érhetők el
- Használhatatlan rendszerképek
- Laborszabályzatok frissítései

Miután tesznek közzé, az értesítés jelenik meg a labor létrehozása – áttekintés oldalra, és a felhasználó kiválaszthat a részleteket.

A közlemény funkció hivatott ideiglenes értesítésekhez használható.  Könnyedén letilthatja a bejelentés után már nem szükséges.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Lépéseket egy meglévő tesztlabor egy közlemény közzététele

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, válassza ki a **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából. (Előfordulhat, hogy a tesztkörnyezet már látható az irányítópult alatt **összes erőforrás**).
1. Tesztkörnyezetek listájában jelölje ki a labor, amelyekben egy közleményt használni kívánt.  
1. A laborgyakorlat **áttekintése** területen válassza **Konfigurace a zásady**.  

    ![Konfigurace a zásady gomb](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. A bal oldali alatt **beállítások**, jelölje be **labor közlemény**.

    ![Labor közlemény gomb](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Hozzon létre egy üzenetet, a felhasználók számára a laborban, állítsa **engedélyezve** való **Igen**.

1. Megadhat egy **lejárati dátum** , adja meg a dátumot és időpontot, amely után a közlemény többé nem jelenik meg a felhasználók számára. Ha nem adja meg a lejárat dátumát, a közlemény marad, amíg tiltsa le azt.

   > [!NOTE]
   > A közlemény lejárata után ez már nem jelenik meg a felhasználók számára, de továbbra is szerepel a **labor közlemény** ablaktáblán. Módosíthatja azt is, és engedélyezze újra a aktiválható újra.
   >
   >

1. Adjon meg egy **název Oznámení** és a **text Oznámení**.

   A cím legfeljebb 100 karakterből állhat, és a felhasználónak a tesztkörnyezet áttekintése lapon jelenik meg. Ha a felhasználó címe, a közlemény szöveg jelenik meg.

   A közlemény szöveg markdown fogad el. A text oznámení meg, megtekintheti az üzenetet az előzetes verzió területen a képernyő alján.

    ![Labor közlemény képernyő létrehozása az üzenetet.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Válassza ki **mentése** elkészült a közlemény közzététele.

Ha már nem szeretné ezt a hirdetményt megjelenítése a labor felhasználónak, térjen vissza a **labor közlemény** lapon, és állítsa be **engedélyezve** való **nem**. Ha megadott lejárati dátumot, a közlemény le van tiltva automatikusan, hogy a dátum és idő.

## <a name="steps-for-users-to-view-an-announcement"></a>A lépések a felhasználók számára a bejelentés megtekintése

1. Az a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040), válassza ki a labor.

1. Ha a labor létrehozása egy közlemény tesznek közzé a hozzá tartozó, egy tájékoztató a labor létrehozása – áttekintés lap tetején látható. Ez a tájékoztató a közlemény létrehozásakor megadott název oznámení.

    ![Labor közlemény, Áttekintés lap](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. A felhasználó kiválaszthat a teljes bejelentés megtekintése az üzenetet.

    ![További információ a labor közlemény](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* Ha módosít, vagy egy tesztlabor házirend beállítása, érdemes egy értesítés tájékoztatja a felhasználót a közzététele. [Szabályzatok és ütemezések beállítása](devtest-lab-set-lab-policy.md) korlátozások és konvenciói nagyobb figyelmet az előfizetésében alkalmazásával testre szabott házirendekkel kapcsolatos információkat nyújt.
* Fedezze fel a [DevTest Labs Azure Resource Manager gyorsindítási sablon katalógus](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
