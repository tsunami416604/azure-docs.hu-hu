---
title: Virtuális gépek automatikus leállításának konfigurálása a Azure Lab Servicesban
description: Ez a cikk azt ismerteti, hogyan konfigurálható a virtuális gépek automatikus leállítása a labor-fiókban.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8647aed0e66993b8a7b8e5c0a42c8ceabbb1fb9e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798448"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Virtuális gépek automatikus leállításának beállítása labor-fiókhoz

Több automatikus leállítási költség-vezérlési funkciót is engedélyezhet, hogy proaktív módon meggátolja a további költségeket, ha a virtuális gépeket nem használják aktívan. A következő három automatikus leállítási és leválasztási funkció kombinációja a legtöbb esetben megfogást nyújt, amikor a felhasználók véletlenül elhagyják a virtuális gépeket:
 
- A felhasználók automatikus leválasztása a virtuális gépekről, amelyeket az operációs rendszer tétlennek tekint (csak Windows).
- Virtuális gépek automatikus leállítása a felhasználók leválasztásakor (Windows & Linux).
- Automatikusan leállítja az elindított virtuális gépeket, de a felhasználók nem csatlakoznak.

Tekintse át az automatikus leállítási funkciókról szóló további részleteket a [Cost Control és az automatikus leállítás beállításai](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) szakaszban.

## <a name="enable-automatic-shutdown"></a>Automatikus leállítás engedélyezése

1. A [Azure Portal](https://portal.azure.com/) navigáljon a **labor-fiók** lapra.
1. Válassza a **Labs-beállítások** elemet a bal oldali menüben.
1. Válassza ki az adott forgatókönyvnek megfelelő automatikus leállítási beállítást (ka) t.  

    > [!div class="mx-imgBorder"]
    > ![Automatikus leállítás beállítása Lab-fiókban](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    A beállítás (ok) a labor-fiókban létrehozott összes laborra érvényes. A tesztkörnyezet létrehozója (pedagógus) a tesztkörnyezet szintjén felülbírálhatja ezt a beállítást. A labor-fiókban a beállítás módosítása csak a módosítás után létrehozott laborokra lesz hatással.

    A beállítás (ok) letiltásához törölje a jelölőnégyzet (eke) t ezen a lapon. 

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogy a labor tulajdonosa hogyan konfigurálhatja vagy felülbírálhatja ezt a beállítást a labor szintjén, tekintse meg a [virtuális gépek automatikus leállításának beállítása laborhoz](how-to-enable-shutdown-disconnect.md) című témakört.
