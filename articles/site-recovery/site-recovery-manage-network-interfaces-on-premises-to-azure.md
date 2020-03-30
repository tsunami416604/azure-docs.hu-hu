---
title: Hálózati adapterek kezelése helyszíni vészhelyreállításhoz az Azure Site Recovery szolgáltatással
description: A helyszíni vész-helyreállítási hálózati felületek azure-beli kezelésének ismertetése az Azure Site Recovery szolgáltatással
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 2a4752b501e40f9e8a4f3bc82cb2533c11f9e526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954590"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Virtuálisgép hálózati csatolóinak kezelése az Azure-ba irányuló helyszíni vészhelyreállításhoz

Az Azure-ban egy virtuális géphez (VM) legalább egy hálózati adapternek hozzá kell kapcsolódnia. Annyi hálózati adaptert csatlakoztathat hozzá, amennyit a virtuális gép mérete támogat.

Alapértelmezés szerint az Azure virtuális géphez csatlakoztatott első hálózati illesztő elsődleges hálózati csatolóként van definiálva. A virtuális gép összes többi hálózati csatolója másodlagos hálózati csatoló. Alapértelmezés szerint a virtuális gépről érkező összes kimenő forgalom kiküldi az elsődleges hálózati adapter elsődleges IP-konfigurációjához rendelt IP-címet.

Helyszíni környezetben a virtuális gépek vagy kiszolgálók több hálózati adapterrel is rendelkezhetnek a környezeten belüli különböző hálózatokhoz. A különböző hálózatokat általában meghatározott műveletek, például frissítések, karbantartás és internet-hozzáférés elvégzésére használják. Amikor egy helyszíni környezetből migrál vagy ad át az Azure-ba, ne feledje, hogy az ugyanazon a virtuális gépen lévő hálózati csatolóknak mind ugyanahhoz a virtuális hálózathoz kell csatlakozniuk.

Alapértelmezés szerint az Azure Site Recovery annyi hálózati felületet hoz létre egy Azure virtuális gépen, ahány a helyszíni kiszolgálóhoz csatlakozik. A replikált virtuális gép beállításai alatt elkerülheti a redundáns hálózati csatolók létrehozását az áttelepítés vagy a feladatátvétel során.

## <a name="select-the-target-network"></a>A célhálózat kiválasztása

VMware és fizikai gépek, valamint a Hyper-V (anélkül, hogy system center virtual machine manager) virtuális gépek, megadhatja a cél virtuális hálózat az egyes virtuális gépek. A Virtual Machine Managerrel kezelt Hyper-V virtuális gépek esetén [a hálózati leképezéssel](site-recovery-network-mapping.md) rendelje hozzá a virtuális gép hálózatokat a forrás Virtual Machine Manager kiszolgálón, és célozza meg az Azure-hálózatokat.

1. A Recovery Services-tároló **replikált elemeinek** csoportban válassza ki a replikált elemeket a replikált elem beállításainak eléréséhez.

2. A **Számítás és a Hálózat** lapon érheti el a replikált elem hálózati beállításait.

3. A **Hálózati tulajdonságok csoportban**válasszon egy virtuális hálózatot az elérhető hálózati adapterek listájából.

    ![Hálózati beállítások](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

A célhálózat módosítása hatással van az adott virtuális gép összes hálózati kapcsolatára.

A Virtual Machine Manager-felhők esetében a hálózati leképezés módosítása az összes virtuális gépre és azok hálózati interfészeire hatással van.

## <a name="select-the-target-interface-type"></a>A célillesztő típusának kiválasztása

A **Számítási és hálózati** ablaktábla Hálózati **csatolók** szakaszában megtekintheti és szerkesztheti a hálózati csatoló beállításait. Megadhatja a célhálózati adapter típusát is.

- A feladatátvételhez **elsődleges** hálózati adapter szükséges.
- Az összes többi kijelölt hálózati csatoló , ha van ilyen, **másodlagos** hálózati adapter.
- Jelölje be **a Ne használja** lehetőséget, ha ki szeretné zárni a hálózati adaptert a feladatátvételkori létrehozásból.

Alapértelmezés szerint a replikáció engedélyezésekor a Site Recovery kiválasztja az összes észlelt hálózati adaptert a helyszíni kiszolgálón. Az egyiket **elsődlegesként,** az összes többit **pedig másodlagosként jelöli**meg. A helyszíni kiszolgálón hozzáadott további összeköttetések alapértelmezés szerint **a Használat kinem használata** jelölésűek. Ha további hálózati csatolók hozzáadásakor győződjön meg arról, hogy a megfelelő Azure virtuálisgép-célméret van kiválasztva az összes szükséges hálózati adapterek befogadására.

## <a name="modify-network-interface-settings"></a>Hálózati illesztő beállításainak módosítása

A replikált elem hálózati csatolóinak alhálózati és IP-címét módosíthatja. Ha nincs megadva IP-cím, a Site Recovery hozzárendeli a következő elérhető IP-címet az alhálózatból a feladatátvételkori hálózati adapterhez.

1. A hálózati adapter beállításainak megnyitásához válassza ki az elérhető hálózati adaptereket.

2. Válassza ki a kívánt alhálózatot az elérhető alhálózatok listájából.

3. Adja meg a kívánt IP-címet (szükség szerint).

    ![Hálózati adapter beállításai](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Válassza **az OK gombot** a szerkesztés befejezéséhez és a **Számítás és hálózat** ablaktáblához való visszatéréshez.

5. Ismételje meg az 1-4.

6. Az összes módosítás mentéséhez válassza a **Mentés** gombot.

## <a name="next-steps"></a>További lépések
  [További információ](../virtual-network/virtual-network-network-interface-vm.md) az Azure virtuális gépek hálózati felületeiről.
