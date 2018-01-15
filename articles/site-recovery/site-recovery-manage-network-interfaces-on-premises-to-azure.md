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
ms.openlocfilehash: 036d5c2945bd6730d65f88f72c9377047fefcde6
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2018
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-scenarios"></a>Virtuális gépek hálózati illesztői a helyszíni Azure forgatókönyvekre kezelése

Az Azure virtuális gép (VM) rendelkeznie kell legalább egy hálózati adapter nem csatlakoztatható. Számos hálózati kapcsolat kapcsolódik, a virtuális gép mérete által támogatott rendelkezhet. 

Alapértelmezés szerint az első hálózati adapter egy Azure virtuális géphez csatolt elsődleges hálózati adapterének típusúként van definiálva. A virtuális gép más hálózati adapterek másodlagos hálózati adapterrel. Is alapértelmezés szerint minden kimenő forgalom a virtuális gép által kiküldött hozzá van rendelve az elsődleges IP-konfigurációja az elsődleges hálózati adapter IP-cím.

A helyszíni környezetben, virtuális gépek vagy kiszolgálók is több hálózati illesztőre van szükség a környezetben lévő különböző hálózatokhoz. Különböző hálózatokban általában használatos például frissítések, a karbantartási és az internet-hozzáférés meghatározott műveletet hajt végre. Amikor egy helyszíni környezetből Azure-bA feladatátvétele vagy áttelepítése, vegye figyelembe, hogy ugyanaz a virtuális gép hálózati illesztők összes kapcsolódnia kell az azonos virtuális hálózatban.

Alapértelmezés szerint Azure Site Recovery hozza létre, ahol sok a network interfaces Azure virtuális géphez csatlakozik a helyi kiszolgáló. Elkerülheti a redundáns hálózati adapterek létrehozása áttelepítése vagy feladatátvétele során a hálózati illesztő beállításai a replikált virtuális gép beállítások szerkesztésével.

## <a name="select-the-target-network"></a>Válassza ki a cél hálózati

VMware és fizikai gépek, és a virtuális gépek Hyper-V (nélkül a System Center Virtual Machine Managerrel) megadhatja az egyes virtuális gépek a cél virtuális hálózat. A Virtual Machine Managerrel felügyelt Hyper-V virtuális gépek használata [hálózatleképezés](site-recovery-network-mapping.md) képezi le a Virtual Machine Manager forráskiszolgálón Virtuálisgép-hálózatok és a cél Azure-hálózatok.

1. A **replikált elemek** Recovery Services-tároló, a beállítások megadása, hogy a replikált elem elérésére bármely replikált elem kijelölése.

2. Válassza ki a **számítás és hálózat** lapon a hálózati beállítások megadása a replikált elem elérésére.

3. A **hálózati tulajdonságai**, elérhető hálózati adapterek listájából válasszon ki egy virtuális hálózatot.

    ![Hálózati beállítások](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

A célhálózat módosítása hatással van a megadott virtuális gép hálózati adaptereken.

A Virtual Machine Manager-felhőkhöz a hálózatleképezés módosítása hatással van az összes virtuális gép és a hálózati adapterek.

## <a name="select-the-target-interface-type"></a>Válassza ki a céltípus illesztőfelület

A a **hálózati illesztőt** szakasza a **számítás és hálózat** panelen megtekintheti és szerkesztheti a hálózati illesztő beállításai. A cél hálózati kapcsolat típusa is megadható.

- A **elsődleges** hálózati adapter szükség a feladatátvételi.
- Más kijelölt hálózati adapterek, ha bármelyik, **másodlagos** hálózati illesztőt.
- Válassza ki **ne használjon** szeretné kizárni egy adott hálózati csatoló a feladatátvétel létrehozását.

Alapértelmezés szerint, ha replikációs, hogy engedélyezi a Site Recovery kiválasztja azokat a helyi kiszolgálón észlelt hálózati adaptereken. Azt jelzi, hogy egy, **elsődleges** és az összes többit **másodlagos**. Bármely ezt követő felületek hozzá a helyi kiszolgálón vannak megjelölve az **ne használjon** alapértelmezés szerint. További hálózati adapterek hozzáadásakor győződjön meg arról, hogy helyes-e az Azure virtuális gép célméretét alkalmazásához szükséges hálózati adaptereken van kiválasztva.

## <a name="modify-network-interface-settings"></a>Hálózati kapcsolat beállításainak módosítása

Az alhálózat és a replikált elem hálózati illesztő IP-címet módosíthatja. Ha IP-cím nincs megadva, a Site Recovery a következő elérhető IP-cím az alhálózatból a hálózati illesztő feladatátvétel hozzárendelése.

1. Válassza ki a rendelkezésre álló hálózati csatolóhoz a hálózati kapcsolat beállításainak megnyitásához.

2. Válassza ki a kívánt alhálózat elérhető alhálózatok listája.

3. Adja meg a kívánt IP-címre (szükséges).

    ![Hálózati illesztő beállításai](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Válassza ki **OK** a Szerkesztés befejezése, és térjen vissza a **számítás és hálózat** ablaktáblán.

5. Más hálózati illesztők ismételje meg az 1-4.

6. Válassza ki **mentése** összes módosítások mentéséhez.

## <a name="next-steps"></a>További lépések
  [További](../virtual-network/virtual-network-network-interface-vm.md) az Azure virtuális gépek hálózati adapterekkel kapcsolatos.
