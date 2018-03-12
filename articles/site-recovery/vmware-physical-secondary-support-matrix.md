---
title: "A VMware virtuális gépek replikálása vagy az Azure Site Recovery másodlagos VMware-hely fizikai kiszolgálók támogatási mátrix |} Microsoft Docs"
description: "Összefoglalja, VMware vagy fizikai kiszolgáló replikációs egy másodlagos helyre, az Azure Site Recovery támogatása"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: b51a4573ad7a8461b7261f08d94639d2030492d9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-replication-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>VMware virtuális gépek replikálását és fizikai kiszolgálók egy másodlagos helyre támogatási mátrix

Ez a cikk összefoglalja, hogy mi támogatja, ha használja a [Azure Site Recovery](site-recovery-overview.md) VMware virtuális gépek vagy windowsos/Linuxos fizikai kiszolgálók replikálása egy másodlagos VMware-hely szolgáltatást.

- Ha a kívánt VMware virtuális gépek vagy fizikai kiszolgálók replikálása Azure-ba, tekintse át a [a támogatási mátrix](vmware-physical-azure-support-matrix.md).
- Ha azt szeretné, a Hyper-V virtuális gépek replikálása másodlagos helyre, tekintse át a [a támogatási mátrix](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálása InMage Scout biztosítja. Azure Site Recovery szolgáltatás előfizetésének InMage Scout tartalmazza.


## <a name="host-servers"></a>Host servers

**Operációs rendszer** | **Részletek**
--- | ---
vCenter-kiszolgáló | vCenter 5.5, 6.0 és 6.5<br/><br/> Ha 6.0 vagy 6.5 futtatja, vegye figyelembe, hogy csak 5.5 szolgáltatások támogatottak.


## <a name="replicated-vm-support"></a>Replikált virtuális gép támogatása

A következő táblázat összefoglalja az operációs rendszer támogatása gépeket replikálni a Site Recovery szolgáltatással. Bármilyen munkaterhelést futtathat a támogatott operációs rendszeren.

**Operációs rendszer** | **Részletek**
--- | ---
Windows Server | 64 bites Windows Server 2016-os, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2: legalább SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4 6.5, 6.8 a Red Hat kompatibilis kernel vagy szoros vállalati Kernel kiadási-3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Linux-gépek tárhelye

A következő tárolási csak Linux gépek replikálhatja:

- File system (EXT3, ETX4, ReiserFS, XFS).
- A többutas szoftver-eszköz leképező.
- Kötetkezelő (LVM2).
- HP CCISS vezérlő tároló fizikai kiszolgálók nem támogatottak.
- SUSE Linux Enterprise Server 11 SP3 csak támogatott ReiserFS fájlrendszert.

## <a name="network-configuration---hostguest-vm"></a>Hálózati konfiguráció - gazdagép vagy Vendég virtuális gép

**Konfigurálás** | **Támogatott**  
--- | --- 
Gazdagép - hálózati adapterek összevonása | Igen 
Gazdagép - VLAN | Igen 
Gazdagép - IPv4 | Igen 
Gazdagép - IPv6 | Nem 
Virtuálisgép - Vendég hálózati adapterek összevonása | Nem
Vendég virtuális Gépen – IPv4 | Igen
Vendég virtuális Gépen – IPv6 | Nem
Gues Virtuálisgép Windows/Linux - statikus IP-címe | Igen
Vendég Virtuálisgép - több hálózati Adapterrel | Igen


## <a name="storage"></a>Tárolás

### <a name="host-storage"></a>Tárolók

**Tárhely (gazdagép)** | **Támogatott** 
--- | --- 
NFS | Igen 
SMB 3.0 | – 
SAN (ISCSI) | Igen 
(MPIO) többutas | Igen 

### <a name="guest-or-physical-server-storage"></a>Vendég vagy fizikai kiszolgálón történő tárolás

**Konfigurálás** | **Támogatott** 
--- | --- 
VMDK | Igen 
VHD/VHDX | – 
Generációból 2 virtuális gép | – 
Megosztott fürtlemez | Igen 
Titkosított lemez | Nem 
UEFI| Igen 
NFS | Nem 
SMB 3.0 | Nem 
RDM | Igen 
> 1 TB méretű lemez | Igen 
A csíkozott > 1 TB-os kötet<br/><br/> LVM | Igen 
Tárolóhelyek | Nem 
Gyakran használt adatok hozzáadása lemez | Igen 
Lemez kizárása | Igen 
(MPIO) többutas | – 

## <a name="vaults"></a>Tárolók

**Művelet** | **Támogatott** 
--- | --- 
Erőforráscsoportok (belül vagy között, előfizetések) közötti áthelyezése közben tárolók | Nem 
Tárolási, hálózati, Azure virtuális gépek között (belül vagy között, előfizetések) erőforráscsoportok áthelyezéséhez | Nem 

## <a name="mobility-service-and-updates"></a>Mobilitási szolgáltatás és a frissítések

A mobilitási szolgáltatás koordinálja a helyszíni VMware Server vagy fizikai kiszolgálók és a másodlagos hely közötti replikációt. Replikációs beállításakor győződjön meg arról, hogy a mobilitási szolgáltatást, és az egyéb összetevők a legújabb verzióra.

**Update** | **Részletek** 
--- | --- 
Scout frissítések | [További tudnivalók és letöltése](/vmware-physical-secondary-disaster-recovery.md#updates) a legújabb Scout | Összegző Scout frissítésekről szó.
Összetevő-frissítések | Scout frissítések összetevők, beleértve a RX kiszolgáló, konfigurációs kiszolgáló, folyamat és a fő célkiszolgálóra, vContinuum-kiszolgáló és a védeni kívánt adatforrás-kiszolgálók frissítést tartalmaznak.<br/><br/> [További információk](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).


## <a name="next-steps"></a>További lépések

Töltse le a [InMage Scout felhasználói útmutatója](https://aka.ms/asr-scout-user-guide)

- [Hyper-V virtuális gépek VMM-felhőkben replikálása egy másodlagos helyre](tutorial-vmm-to-vmm.md)
- [VMware-alapú virtuális gépek és fizikai kiszolgálók replikálása másodlagos helyre](tutorial-vmware-to-vmware.md)
