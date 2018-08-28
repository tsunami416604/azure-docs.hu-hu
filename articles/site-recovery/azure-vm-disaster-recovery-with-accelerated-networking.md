---
title: Gyorsított hálózatkezelést az Azure virtuális gépek vészhelyreállítása |} A Microsoft Docs
description: Ismerteti, hogyan lehet engedélyezni a gyorsított hálózatkezelés az Azure Site Recovery az Azure virtuális gép vész-helyreállítási
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/24/2018
ms.author: manayar
ms.openlocfilehash: af29333956fb962ed31133260ecbc9607de38fd3
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43052063"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Vészhelyreállítás Azure virtuális gép gyorsított hálózatkezelés

Gyorsított hálózatkezelés lehetővé teszi, hogy az egygyökerű i/o-virtualizálás (SR-IOV) egy virtuális géphez, nagy mértékben javítva a hálózati teljesítmény. A nagy teljesítményű elérési út megkerüli a gazdagép a datapath csökkenti a késés, a jitter és a Processzor kihasználtsága a legnagyobb hálózati számítási a támogatott Virtuálisgép-típusok való használatra. Az alábbi képen látható a két virtuális gépet, és anélkül gyorsított hálózatkezelés közötti kommunikáció:

![Összehasonlítás](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Az Azure Site Recovery lehetővé teszi, hogy előnyeinek gyorsított hálózatkezelés, az Azure virtual machines szolgáltatásban, melyek feladatai át egy másik régióba való használatát. Ez a cikk bemutatja, hogyan engedélyezheti a gyorsított hálózatkezelés-beli virtuális gépek replikálása az Azure Site Recoveryvel.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, tisztában kell lennie:
-   Az Azure virtuális gép [replikáció architektúrája](azure-to-azure-architecture.md)
-   [A replikáció](azure-to-azure-tutorial-enable-replication.md) Azure-beli virtuális gépek
-   [Feladatátvétele](azure-to-azure-tutorial-failover-failback.md) Azure-beli virtuális gépek

## <a name="accelerated-networking-with-windows-vms"></a>Gyorsított hálózatkezelés a Windows-alapú virtuális gépekhez

Az Azure Site Recovery támogatja az engedélyezése a gyorsított hálózatkezelés a replikált virtuális gépek esetében csak akkor, ha a forrás virtuális gép van a gyorsított hálózatkezelés engedélyezett. Ha a forrás virtuális gép nem rendelkezik a gyorsított hálózatkezelés engedélyezett, megismerheti a gyorsított hálózatkezelés a Windows virtuális gépek engedélyezéséhez [Itt](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A következő disztribúciók az Azure katalógusából beépített támogatottak:
* **A Windows Server 2016 Datacenter**
* **A Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Támogatott Virtuálisgép-példányok
Gyorsított hálózatkezelés legtöbb általános célú és a 2 vagy több vcpu-k méretű számításra optimalizált példányok esetén támogatott.  A támogatott sorozat: D/DSv2 és az F/Fs

Hyper-Threading technológia támogató példányokon gyorsított hálózatkezelés támogatott 4 vagy több vcpu-k a Virtuálisgép-példányokon. Támogatott sorozat: a DSv3/D, E/ESv3, Fsv2 és Ms és Mms

További információ a Virtuálisgép-példányokon: [Windows Virtuálisgép-méretek](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Linux rendszerű virtuális gépek gyorsított hálózatkezelés

Az Azure Site Recovery támogatja az engedélyezése a gyorsított hálózatkezelés a replikált virtuális gépek esetében csak akkor, ha a forrás virtuális gép van a gyorsított hálózatkezelés engedélyezett. Ha a forrás virtuális gép nem rendelkezik a gyorsított hálózatkezelés engedélyezett, megismerheti a Linux rendszerű virtuális gépek a gyorsított hálózatkezelés engedélyezéséhez [Itt](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A következő disztribúciók az Azure katalógusából beépített támogatottak:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **7.4 RHEL**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "kiterjeszthető" portolások kernel**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Támogatott Virtuálisgép-példányok
Gyorsított hálózatkezelés legtöbb általános célú és a 2 vagy több vcpu-k méretű számításra optimalizált példányok esetén támogatott.  A támogatott sorozat: D/DSv2 és az F/Fs

Hyper-Threading technológia támogató példányokon gyorsított hálózatkezelés támogatott 4 vagy több vcpu-k a Virtuálisgép-példányokon. Támogatott sorozat: a DSv3/D, E/ESv3, Fsv2 és Ms és Mms.

További információ a Virtuálisgép-példányokon: [Linux Virtuálisgép-méretek](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Gyorsított hálózatkezelés engedélyezéséhez replikált virtuális gépek

Ha Ön [engedélyezze a replikációt](azure-to-azure-tutorial-enable-replication.md) Azure-beli virtuális gépek, a Site Recovery automatikusan észlelni fogja-e a virtuális géphez tartozó hálózati adapter van-e a gyorsított hálózatkezelés engedélyezett. Ha a gyorsított hálózatkezelés már engedélyezve van, a Site Recovery automatikusan konfigurálja a gyorsított hálózatkezelés a replikált virtuális gép hálózati adapteren.

A gyorsított hálózatkezelés állapota ellenőrizhető a **hálózati adapterek** szakaszában a **számítás és hálózat** a replikált virtuális gép beállításait.

![Gyorsított Hálózatkezelés beállítása](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Ha engedélyezte a gyorsított hálózatkezelés a forrás virtuális gép a replikáció engedélyezése után, engedélyezheti a gyorsított hálózatkezelés a replikált virtuális gép hálózati adapterek, a következő folyamat:
1. Nyissa meg **számítás és hálózat** a replikált virtuális gép beállításai
2. Kattintson a hálózati adapter nevére a **hálózati adapterek** szakasz
3. Válassza ki **engedélyezve** gyorsított hálózatkezelés alatt a legördülő listából a **cél** oszlop

![Gyorsított hálózatkezelés engedélyezéséhez](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

A fenti folyamatot kell alkalmazni meglévő replikált virtuális gépek esetében, amelyhez nem korábban gyorsított hálózatkezelés, a Site Recovery automatikusan engedélyezve van.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [gyorsított hálózatkezelés előnyei](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- További információ a korlátozások és megkötések a gyorsított hálózatkezelés [Windows virtuális gépek](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) és [Linux rendszerű virtuális gépek](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Tudjon meg többet [helyreállítási tervek](site-recovery-create-recovery-plans.md) alkalmazás a feladatátvétel automatizálásához.
