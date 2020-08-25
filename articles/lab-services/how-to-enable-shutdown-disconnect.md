---
title: Virtuális gépek automatikus leállításának beállítása laborhoz Azure Lab Services
description: Megtudhatja, hogyan engedélyezheti vagy tilthatja le a virtuális gépek automatikus leállítását, ha a távoli asztali kapcsolat le van választva.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 0df3fdcaea99c00461caac37a3b655d152a0e527
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798482"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Virtuális gépek automatikus leállításának beállítása laborhoz

Ez a cikk bemutatja, hogyan konfigurálhatja a virtuális gépek automatikus leállítását laborokhoz.

Több automatikus leállítási költség-vezérlési funkciót is engedélyezhet, hogy proaktív módon meggátolja a további költségeket, ha a virtuális gépeket nem használják aktívan. A következő három automatikus leállítási és leválasztási funkció kombinációja a legtöbb esetben megfogást nyújt, amikor a felhasználók véletlenül elhagyják a virtuális gépeket:
 
* A felhasználók automatikus leválasztása a virtuális gépekről, amelyeket az operációs rendszer tétlennek tekint (csak Windows).
* Virtuális gépek automatikus leállítása a felhasználók leválasztásakor (Windows & Linux).
* Automatikusan leállítja az elindított virtuális gépeket, de a felhasználók nem csatlakoznak.

Tekintse át az automatikus leállítási funkciókról szóló további részleteket a [Cost Control és az automatikus leállítás beállításai](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) szakaszban.

A labor-fiók rendszergazdája konfigurálhatja ezt a beállítást azon labor-fiók esetében, amelyben a Labs-t létrehozta. További információ: [virtuális gépek automatikus leállításának beállítása labor-fiókhoz](how-to-configure-lab-accounts.md). A labor tulajdonosaként a tesztkörnyezet létrehozásakor vagy a labor létrehozása után felülbírálhatja a beállítást. 

## <a name="configure-for-the-lab-level"></a>Konfigurálás a tesztkörnyezet szintjén

Az automatikus leállítási beállítást a [Azure Lab Servicesban](https://labs.azure.com/)állíthatja be.

* tesztkörnyezet létrehozásakor (labor- **házirendekben**), vagy
* a labor létrehozása után (a **beállításokban**)

> [!div class="mx-imgBorder"]
> ![Konfigurálás a labor létrehozásakor](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Ügyeljen arra, hogy az automatikus leállítás részleteit a [Cost Control és az automatikus leállítás beállításai](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) szakaszban tekintse át.

> [!WARNING]
> Ha a Windows operációs rendszert (OS) leállítja egy virtuális gépre, mielőtt leválaszt egy RDP-munkamenetet a virtuális géphez, az automatikus leállítás funkció nem fog megfelelően működni.  

## <a name="next-steps"></a>Következő lépések

[Az osztályterem Labs irányítópultja](use-dashboard.md)
