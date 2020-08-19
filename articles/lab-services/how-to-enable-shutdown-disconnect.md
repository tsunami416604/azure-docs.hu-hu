---
title: Virtuális gépek automatikus leállításának beállítása laborhoz Azure Lab Services
description: Megtudhatja, hogyan engedélyezheti vagy tilthatja le a virtuális gépek automatikus leállítását, ha a távoli asztali kapcsolat le van választva.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 7941a3eed502f2329f5d2acf244eec7cef322615
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589765"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Virtuális gépek automatikus leállításának beállítása laborhoz

Ez a cikk bemutatja, hogyan konfigurálhatja a virtuális gépek automatikus leállítását laborokhoz.

Több automatikus leállítási költség-vezérlési funkciót is engedélyezhet, hogy proaktív módon meggátolja a további költségeket, ha a virtuális gépeket nem használják aktívan. A következő három automatikus leállítási és leválasztási funkció kombinációja a legtöbb esetben megfogást nyújt, amikor a felhasználók véletlenül elhagyják a virtuális gépeket:
 
* A felhasználók automatikus leválasztása a virtuális gépekről, amelyeket az operációs rendszer tétlennek tekint (csak Windows).
* Virtuális gépek automatikus leállítása a felhasználók leválasztásakor (Windows & Linux).
* Automatikusan leállítja az elindított virtuális gépeket, de a felhasználók nem csatlakoznak.

Tekintse át az automatikus leállítási funkciókról szóló további részleteket a [Cost Control és az automatikus leállítás beállításai](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) szakaszban.

A labor-fiók rendszergazdája konfigurálhatja ezt a beállítást azon labor-fiók esetében, amelyben a Labs-t létrehozta. További információ: a [virtuális gépek automatikus leállításának beállítása a kapcsolat bontásakor egy Lab-fiók esetében](how-to-configure-lab-accounts.md). A labor tulajdonosaként a tesztkörnyezet létrehozásakor vagy a labor létrehozása után felülbírálhatja a beállítást. 

## <a name="configure-for-the-lab-level"></a>Konfigurálás a tesztkörnyezet szintjén

Az automatikus leállítási beállítást a [Azure Lab Servicesban](https://labs.azure.com/)állíthatja be.

* tesztkörnyezet létrehozásakor (labor- **házirendekben**), vagy
* a labor létrehozása után (a **beállításokban**)

> [!div class="mx-imgBorder"]
> ![Konfigurálás a labor létrehozásakor](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Ügyeljen arra, hogy az automatikus leállítás részleteit a [Cost Control és az automatikus leállítás beállításai](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) szakaszban tekintse át.

> [!WARNING]
> Ha a Windows operációs rendszert (OS) leállítja egy virtuális gépre, mielőtt leválaszt egy RDP-munkamenetet a virtuális géphez, az automatikus leállítás funkció nem fog megfelelően működni.  

## <a name="next-steps"></a>További lépések

[Az osztályterem Labs irányítópultja](use-dashboard.md)
