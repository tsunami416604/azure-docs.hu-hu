---
title: Az Azure Site Recovery a helyszíni vészhelyreállítás az Azure hálózati adapterek kezelése |} A Microsoft Docs
description: Ismerteti, hogyan lehet az Azure-bA az Azure Site Recovery a helyszíni vész-helyreállítási hálózati adapterek kezelése
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 5d5dd7bc3f6b60c2f9d7c2179f2bd356ca101dc4
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360413"
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Az Azure-bA a helyszíni vész-helyreállítási virtuális gép hálózati adapterek kezelése
Az Azure-beli virtuális gép (VM) rendelkeznie kell legalább egy hálózati adapter csatlakozik. Hálózati adapter csatlakozik a virtuális gép mérete támogatja, mint rendelkezhet.

Alapértelmezés szerint az első hálózati adapter egy Azure virtuális géphez csatolt számít, ha az elsődleges hálózati adapter. A virtuális gép más hálózati adapterek másodlagos hálózati adapterek. Is alapértelmezés szerint minden kimenő forgalom a virtuális gép küldése az elsődleges hálózati adapter elsődleges IP-konfigurációhoz rendelt IP-címét.

A helyszíni környezetben virtuális gépek vagy kiszolgálók rendelkezhet több hálózati adapterrel a környezeten belül más hálózatokhoz. A különböző hálózatok szolgálnak általában hajt végre bizonyos műveleteket, például frissítések, karbantartási és internet-hozzáféréssel. Amikor áttelepítése vagy feladatátvétele egy a helyszíni környezetből az Azure-ba, vegye figyelembe, hogy ugyanaz a virtuális gép hálózati adaptereinek összes kapcsolódnia kell az azonos virtuális hálózatban.

Alapértelmezés szerint az Azure Site Recovery számos hálózati adapter egy Azure virtuális gépen, a helyszíni kiszolgálóhoz csatlakozik, ahogy hoz létre. Elkerülheti a redundáns hálózati adapterek létrehozása áttelepítés vagy a feladatátvétel során a hálózati adapter beállításai a replikált virtuális gép beállításaiban szerkesztésével.

## <a name="select-the-target-network"></a>Válassza ki a cél hálózati

VMware-alapú és fizikai gépek számára, és a Hyper-V (nem a System Center Virtual Machine Manager) virtuális gépek megadhatja a cél virtuális hálózattal, az egyes virtuális gépeket. A Virtual Machine Manager által felügyelt Hyper-V virtuális gépek használata [hálózatleképezés](site-recovery-network-mapping.md) képezze le a Virtual Machine Manager forráskiszolgálón Virtuálisgép-hálózatok és a cél Azure-hálózatok.

1. A **replikált elemek** a Recovery Services-tárolót, válassza ki a minden olyan replikált elem, hogy a replikált elem a beállítások eléréséhez.

2. Válassza ki a **számítás és hálózat** lap eléréséhez a hálózati beállításokat a replikált elemhez.

3. A **hálózati tulajdonságok**, a rendelkezésre álló hálózati adapterek listájából válassza ki a virtuális hálózat.

    ![Hálózati beállítások](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

A célhálózat módosítása hatással van, hogy adott virtuális gép hálózati adaptereken.

A Virtual Machine Manager-felhőkben a hálózatleképezés módosítása hatással van az összes virtuális gép és a hálózati adapterek.

## <a name="select-the-target-interface-type"></a>Válassza ki a cél typ rozhraní

Alatt a **hálózati adapterek** szakaszában a **számítás és hálózat** panelen megtekintheti és szerkesztheti a hálózatiadapter-beállítások. Emellett megadhatja a céloldali hálózati adapter típusa.

- A **elsődleges** hálózati adapterre szükség a feladatátvételhez.
- Az összes kiválasztott hálózati adapterek, ha bármelyik, **másodlagos** hálózati adapterek.
- Válassza ki **ne használjon** kizárandó egy hálózati adapter létrehozása a feladatátvétel során.

Alapértelmezés szerint engedélyezi a replikációt, amikor a Site Recovery választja ki a helyszíni kiszolgálón észlelt hálózati adaptereken. Azt jelzi, hogy több mint **elsődleges** és minden más, **másodlagos**. Minden további kapcsolat hozzáadása a helyi kiszolgálón vannak megjelölve **ne használjon** alapértelmezés szerint. Ha több hálózati adapter hozzáadása esetén győződjön meg arról, hogy a megfelelő Azure virtuális gép cél mérete befogadásához az összes szükséges hálózati adapter van-e kiválasztva.

## <a name="modify-network-interface-settings"></a>Hálózati adapter beállításainak módosítása

Az alhálózat és a egy replikált elem hálózati adapterek IP-címet módosíthatja. Ha IP-cím nincs megadva, a Site Recovery a következő elérhető IP-címe az alhálózatról feladatátvétel során a hálózati adapterhez hozzárendelni.

1. Válassza ki bármelyik elérhető hálózati adaptert a hálózati adapter beállításainak megnyitásához.

2. Válassza ki a kívánt alhálózat elérhető alhálózatok listájában.

3. Adja meg a kívánt IP-címre (kötelező).

    ![Hálózatiadapter-beállítások](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Válassza ki **OK** alkalmazásfájl szerkesztésének befejezése, és térjen vissza a **számítás és hálózat** ablaktáblán.

5. Más hálózati adapterek ismételje meg az 1 – 4.

6. Válassza ki **mentése** összes módosítások mentéséhez.

## <a name="next-steps"></a>További lépések
  [További](../virtual-network/virtual-network-network-interface-vm.md) kapcsolatos Azure-beli virtuális gépek hálózati adaptereket.
