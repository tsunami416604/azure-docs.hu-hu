---
title: "Egy announcment egy laborhoz fel Azure DevTest Labs szolgáltatásban |} Microsoft Docs"
description: "Megtudhatja, hogyan hirdetmény hozzáadása egy laborhoz a Azure DevTest Labs szolgáltatásban"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 67a09946-4584-425e-a94c-abe57c9cbb82
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/29/2018
ms.author: v-craic
ms.openlocfilehash: 99b0938d5f4c8b022ead3473a0367de5d75cd6ff
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="post-an-announcement-to-a-lab-in-azure-devtest-labs"></a>A laborokhoz közlemény fel Azure DevTest Labs szolgáltatásban

Tesztlabor-rendszergazdaként egy egyéni közlemény beküldheti egy meglévő laborban értesíteni a felhasználókat a legutóbbi módosítások vagy kiegészítés érkezett a laborkörnyezetben. Például előfordulhat, hogy szeretné a felhasználókat:

- Rendelkezésre álló új Virtuálisgép-méretek
- Lemezképbe, amelyek jelenleg nem használható
- Labor szükséges frissítéseket

Ha közzétett, az értesítés jelenik meg a tesztkörnyezet áttekintése lapon, és a felhasználó kiválaszthatja a részleteket.

A közlemény szolgáltatást arra szolgál, hogy az ideiglenes értesítések használható.  Bejelentés után már nem szükséges könnyedén letilthatja.

## <a name="steps-to-post-an-announcement-in-an-existing-lab"></a>Bejelentés fel egy meglévő labor lépései

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Ha szükséges, jelölje be **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából. (Előfordulhat, hogy az irányítópult már látható a labor **összes erőforrás**).
1. Válassza ki a labor, amelyben bejelentés használni kívánt labs listájának megtekintéséhez.  
1. A tesztlabor a **áttekintése** területen válassza **konfigurációs és házirendek**.  

    ![Konfigurációs és házirendek gomb](./media/devtest-lab-announcements/devtestlab-config-and-policies.png)

1. A bal oldali alatt **beállítások**, jelölje be **labor közlemény**.

    ![Labor közlemény gomb](./media/devtest-lab-announcements/devtestlab-announcements.png)

1. Hozzon létre egy üzenetet, a felhasználóknak ebben a tesztkörnyezetben, állítsa **engedélyezve** való **Igen**.

1. Megadhat egy **lejárati dátum** adhatja meg a dátumot és időpontot, amely után a közlemény már nem jelenik meg a felhasználók számára. Ha nem adja meg a lejárat dátumát, a közlemény marad letilthatja azt.

   > [!NOTE]
   > A közlemény lejárata után már nem jelenik meg a felhasználók számára, de továbbra is szerepel a **labor közlemény** ablaktáblán. Módosíthatja azt is, és engedélyezze újra azt, hogy legyen aktív újra.
   >
   >

1. Adjon meg egy **közlemény címét** és a **közlemény szöveg**.

   A cím legfeljebb 100 karakter hosszúságú lehet, és megjelenik-e a felhasználó a tesztlabor áttekintése lapon. Ha a felhasználó kijelöli a cím, az értesítés jelenik meg.

   A közlemény szöveg markdown fogad el. A közlemény szöveget ad meg, az üzenet megtekintheti az előnézeti területen a képernyő alján.

    ![Labor közlemény képernyő hozza létre az üzenetet.](./media/devtest-lab-announcements/devtestlab-post-announcement.png)


1. Válassza ki **mentése** Ha hirdetmény visszaküldeni az elkészült.

Ha már nem kívánja megjeleníteni ezt az értesítést a labor felhasználók számára, térjen vissza a **labor közlemény** lapon, és állítsa be **engedélyezve** való **nem**. Ha meg van adva a lejárat dátumát, a közlemény le van tiltva automatikusan, amely dátum és idő.

## <a name="steps-for-users-to-view-an-announcement"></a>Felhasználók megtekintése bejelentés lépései

1. Az a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040), válassza ki a labor.

1. Ha a labor elküldeni az értesítést, egy a tájékoztató a labor – Áttekintés lap tetején látható. Ez a tájékoztató a közlemény címét, amely a közlemény létrehozásakor van megadva.

    ![Labor című közleményen – áttekintés oldalra](./media/devtest-lab-announcements/devtestlab-user-announcement.png)

1. A felhasználó választhat az üzenetet a teljes közlemény megtekintéséhez.

    ![További információ a labor közlemény](./media/devtest-lab-announcements/devtestlab-user-announcement-text.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
* Ha módosít, vagy egy tesztlabor házirend beállítása, érdemes tájékoztatják a felhasználókat a közlemény utáni. [Állítsa be a házirendek és ütemezések](devtest-lab-set-lab-policy.md) alkalmazza a korlátozások és egyezmények az előfizetés testreszabott házirendekkel kapcsolatos információkat biztosít.
* Megismerkedhet a [Office DevTest Labs Azure Resource Manager gyorsindítási sablonok](https://github.com/Azure/azure-devtestlab/tree/master/Samples).
