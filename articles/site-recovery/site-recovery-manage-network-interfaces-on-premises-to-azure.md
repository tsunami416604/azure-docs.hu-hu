---
title: "Hálózati illesztők az Azure Site Recovery a helyszíni Azure forgatókönyvekre kezelése |} Microsoft Docs"
description: "Hálózati illesztők Azure forgatókönyvek az Azure Site Recovery szolgáltatással történő helyszíni kezelése"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2017
ms.author: manayar
ms.openlocfilehash: b60c746ae6c243d2b448056f8768df3baaed4cc1
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/23/2017
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-scenarios"></a>Virtuális gépek hálózati illesztői a helyszíni Azure forgatókönyvekre kezelése

Az Azure virtuális gép rendelkeznie kell legalább egy hálózati adapter nem csatlakoztatható, és is rendelkezik annyi hálózati adapterek nem csatlakoztatható, a virtuális gép mérete által támogatott. Alapértelmezés szerint az első hálózati adapter egy Azure virtuális géphez csatolt elsődleges hálózati adapterének típusúként van definiálva. A virtuális gép más hálózati adapterek másodlagos hálózati adapterrel. Alapértelmezés szerint minden kimenő forgalom a virtuális gép által kiküldött a hozzárendelt elsődleges IP-konfigurációja az elsődleges hálózati adapter IP-címet.

A helyszíni környezetben, virtuális gépek/kiszolgáló is több hálózati illesztőre van szükség a környezetben lévő különböző hálózatokhoz. Különböző hálózatokban általában használják az adott műveleteket (például frissítések, karbantartási, internet-hozzáférés). Amikor áttelepítése vagy feladatátvétele egy a helyszíni környezetből Azure-ba, vegye figyelembe, hogy ugyanaz a virtuális gép hálózati illesztők összes kapcsolódnia kell az azonos virtuális hálózatban.

Alapértelmezés szerint a Site Recovery hozza létre, ahol sok a network interfaces Azure virtuális géphez csatlakozik a helyi kiszolgáló. Elkerülheti a redundáns hálózati adapterek létrehozása áttelepítése vagy feladatátvétele során a hálózati illesztő beállításai a replikált virtuális gép beállítások szerkesztésével.

## <a name="select-the-target-network"></a>Válassza ki a cél hálózati

VMware és fizikai gépek, valamint a virtuális gépek Hyper-V (VMM nélkül) megadhatja az egyes virtuális gépek a cél virtuális hálózat. A VMM-mel, felügyelt Hyper-V virtuális gépekhez [hálózatleképezés](site-recovery-network-mapping.md) képezze le a Virtuálisgép-hálózatok a forrás VMM-kiszolgálót, majd a cél Azure-hálózatok segítségével.

1. "Replikált elemek" a Recovery Services-tároló, a kattintson a beállítások megadása, hogy a replikált elem elérésére bármelyik replikált elemre.

2. Lapon kattintson a "Számítás és hálózat" a hálózati beállítások megadása a replikált elem elérésére.

3. A "Hálózati tulajdonságok" Válasszon a virtuális hálózatot a rendelkezésre álló hálózati illesztők listája.

    ![Számítás és hálózat](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

A célhálózat módosítása hatással van a megadott virtuális gép hálózati adaptereken.

A VMM-felhőkben hálózatleképezés módosítása hatással van az összes virtuális gép és a hálózati adapterek.

## <a name="select-the-target-interface-type"></a>Válassza ki a céltípus illesztőfelület

A "Hálózati csatolók" szakaszban a "Számítás és hálózat" panelről megtekintése és szerkesztése hálózati kapcsolati beállítások, és adja meg a cél hálózati kapcsolat típusa.

- A **elsődleges** hálózati adapter szükség a feladatátvételi.
- Más kijelölt hálózati adapterek, ha bármelyik, **másodlagos** hálózati illesztőt.
- Válassza ki **ne használjon** szeretné kizárni egy adott hálózati csatoló a feladatátvétel létrehozását.

Alapértelmezés szerint amikor kijelöli engedélyezése replikációs, a Site Recovery minden észlelt a helyszíni kiszolgálón, mint amit az "Elsődleges", és minden más jelölés, "Másodlagos" hálózati adapterek hálózati adapterek. Bármely további hozzá a helyi kiszolgálón kapcsolat meg van jelölve, "az" alapértelmezett. További hálózati adapterek hozzáadásakor győződjön meg arról, hogy helyes-e az Azure virtuális gép célméretét alkalmazásához szükséges hálózati adaptereken van kiválasztva.

## <a name="modifying-network-interface-settings"></a>Hálózati kapcsolati beállítások módosítása

Módosíthatja az alhálózatot és IP-egy replikált elem a hálózati adaptereken. Ha nincs megadva olyan IP-címet, a Site Recovery a következő elérhető IP-az alhálózatból a hálózati illesztő feladatátvétel hozzárendelése.

1. Kattintson az elérhető hálózati csatolóhoz hálózati illesztő beállításai panel megnyitásához.

2. Válassza ki a kívánt alhálózat elérhető alhálózatok listája.

3. Adja meg a kívánt IP (szükség szerint).

    ![Hálózati illesztő beállításai](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Kattintson az OK gombra a Szerkesztés befejezése és a "Számítás és hálózat" panelre való visszatéréshez.

5. Más hálózati illesztők ismételje meg az 1-4.

6. Minden módosítások mentéséhez kattintson a "Mentés".

## <a name="next-steps"></a>További lépések
  [További](../virtual-network/virtual-network-network-interface-vm.md) az Azure virtuális gépek hálózati adapterekkel kapcsolatos.
