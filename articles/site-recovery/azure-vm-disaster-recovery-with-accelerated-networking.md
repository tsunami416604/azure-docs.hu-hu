---
title: Gyorsított hálózatkezelés engedélyezése az Azure VM vész-helyreállítási szolgáltatásához az Azure Site Recovery szolgáltatással
description: Ez a témakör azt ismerteti, hogy miként engedélyezhet gyorsított hálózatkezelés az Azure Site Recovery for Azure virtuálisgép-vész-helyreállítási szolgáltatással
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 27691d8fab3e7c8ccd60351dc0be83898ff984ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73622433"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Gyorsított hálózatkezelés az Azure virtuálisgépek vész-helyreállítási

A gyorsított hálózatkezelés lehetővé teszi az egygyökérű I/O-virtualizációt (SR-IOV) a virtuális gépszámára, jelentősen javítva a hálózati teljesítményt. Ez a nagy teljesítményű elérési út megkerüli a gazdagép a datapath, csökkenti a késést, a vibráló és a CPU-kihasználtság, a támogatott virtuálisgép-típusok legigényesebb hálózati munkaterhelések használható. Az alábbi képen két virtuális gép közötti kommunikáció látható gyorsított hálózattal és anélkül:

![Összehasonlítás](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Az Azure Site Recovery lehetővé teszi, hogy kihasználja az accelerated networking előnyeit az Azure virtuális gépek, amelyek feladatátvételt egy másik Azure-régióban. Ez a cikk bemutatja, hogyan engedélyezheti az Azure Site Recovery-vel replikált Azure-beli virtuális gépek gyorsított hálózatba szervezését.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy megértette:
-   Az Azure [virtuálisgép-replikációs architektúrája](azure-to-azure-architecture.md)
-   [A replikáció beállítása](azure-to-azure-tutorial-enable-replication.md) az Azure virtuális gépeihez
-   [Az átadás](azure-to-azure-tutorial-failover-failback.md) Azure virtuális gépek

## <a name="accelerated-networking-with-windows-vms"></a>Gyorsított hálózatkezelés Windows virtuális gépekkel

Az Azure Site Recovery csak akkor támogatja a gyorsított hálózatkezelést a replikált virtuális gépek számára, ha a forrás virtuális gép accelerated networking engedélyezve van. Ha a forrás virtuális gépen nincs engedélyezve az Accelerated Networking funkció, [itt](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)megtudhatja, hogyan engedélyezheti az Accelerated Networking for Windows virtuális gépekhez.

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A következő disztribúciók az Azure Gallery-ből származó dobozból támogatottak:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Támogatott virtuálisgép-példányok
Gyorsított hálózatkezelés támogatott a legtöbb általános célú és számítási optimalizált példány mérete2 vagy több vCPU-k.  Ezek a támogatott sorozatok a következők: D/DSv2 és F/Fs

A hyperthreading-et támogató példányokon a gyorsított hálózatkezelés 4 vagy több vCPU-val rendelkező virtuálisgép-példányokon támogatott. Támogatott sorozatok: D/DSv3, E/ESv3, Fsv2 és Ms/MMS

A virtuálisgép-példányokkal kapcsolatos további tudnivalókért olvassa el [a Windows virtuálisgépek méretei című témakört.](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="accelerated-networking-with-linux-vms"></a>Gyorsított hálózatkezelés Linuxos virtuális gépekkel

Az Azure Site Recovery csak akkor támogatja a gyorsított hálózatkezelést a replikált virtuális gépek számára, ha a forrás virtuális gép accelerated networking engedélyezve van. Ha a forrás virtuális gép nem rendelkezik gyorsított hálózatkezelés engedélyezve, megtudhatja, hogyan engedélyezheti az Accelerated Networking linuxos virtuális gépek [itt](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A következő disztribúciók az Azure Gallery-ből származó dobozból támogatottak:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7,4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" backport kernellel**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Támogatott virtuálisgép-példányok
Gyorsított hálózatkezelés támogatott a legtöbb általános célú és számítási optimalizált példány mérete2 vagy több vCPU-k.  Ezek a támogatott sorozatok a következők: D/DSv2 és F/Fs

A hyperthreading-et támogató példányokon a gyorsított hálózatkezelés 4 vagy több vCPU-val rendelkező virtuálisgép-példányokon támogatott. Támogatott sorozatok: D/DSv3, E/ESv3, Fsv2 és Ms/MMS.

A virtuálisgép-példányokkal kapcsolatos további információkért tekintse meg a [Linux virtuális gép méreteit.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Gyorsított hálózatkezelés engedélyezése a replikált virtuális gépekhez

Ha engedélyezi az Azure virtuális gépek [replikációját,](azure-to-azure-tutorial-enable-replication.md) a Site Recovery automatikusan észleli, hogy a virtuálisgép hálózati csatolóinak engedélyezve van-e az Accelerated Networking. Ha az Accelerated Networking már engedélyezve van, a Site Recovery automatikusan konfigurálja a gyorsított hálózatkezelést a replikált virtuális gép hálózati felületein.

A gyorsított hálózatkezelés állapota a replikált virtuális gép **Számítási és hálózati** beállításainak **Hálózati csatolók** szakaszában ellenőrizhető.

![Gyorsított hálózati beállítás](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Ha a replikáció engedélyezése után engedélyezte a gyorsított hálózatkezelést a forrásvirtuális gépen, a következő eljárással engedélyezheti a gyorsított hálózatkezelést a replikált virtuális gép hálózati csatolóihoz:
1. **A replikált** virtuális gép számítási és hálózati beállításainak megnyitása
2. Kattintson a hálózati adapter nevére a **Hálózati csatolók** szakaszban
3. Válassza az **Engedélyezett** lehetőséget a Gyorsított hálózatlegördülő menü ből a **Cél** oszlopban.

![Gyorsított hálózatkezelés engedélyezése](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

A fenti folyamatot a meglévő replikált virtuális gépek esetében is követni kell, amelyekkorábban nem engedélyezték a Site Recovery által automatikusan engedélyezett gyorsított hálózatkezelést.

## <a name="next-steps"></a>További lépések
- További információ [az Accelerated Networking előnyeiről.](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits)
- További információ a [Windows virtuális gépek](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) és Linux virtuális [gépek](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints)gyorsított hálózatba szolgáltatásának korlátairól és korlátairól.
- További információ az alkalmazásfeladat-átvétel automatizálását célzó [helyreállítási tervekről.](site-recovery-create-recovery-plans.md)
