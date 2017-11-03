---
title: "A replikálás az Azure Site Recovery egy másodlagos hely támogatási mátrix |} Microsoft Docs"
description: "A támogatott operációs rendszerek és összetevők összegzi az Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: c0f86e13e21f2af323e0a306b381054b6eb76755
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="support-matrix-for-replication-to-a-secondary-site-with-azure-site-recovery"></a>A replikálás az Azure Site Recovery egy másodlagos hely támogatási mátrix

Ez a cikk összefoglalja, hogy mi támogatja, ha használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás egy másodlagos helyszíni helyre történő replikálásához.

## <a name="supported-scenarios"></a>Támogatott esetek

**Üzembe helyezés** | **Részletek** 
--- | ---
**VMware és VMware közötti replikáció** | Vészhelyreállítás a helyszíni VMware virtuális gépek másodlagos VMware-helyre.<br/><br/> Töltse le a [InMage Scout felhasználói útmutatója](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)
**Hyper-V – Hyper-V** | Vészhelyreállítás a helyszíni Hyper-V virtuális gépek VMM-felhőkben másodlagos VMM-felhőhöz.<br></br> A VMM nem támogatott.



  

## <a name="host-servers"></a>Kiszolgálók

**Üzembe helyezés** | **Támogatás**
--- | ---
**VMware virtuális gép vagy fizikai kiszolgáló** | vCenter 5.5 vagy 6.0 (csak a 5.5 szolgáltatások támogatása)
**A Hyper-V a VMM-mel** | Windows Server 2016 és a Windows Server 2012 R2 legújabb frissítéseit.<br/><br/> Windows Server 2016 gazdagépek a VMM 2016 kell kezelnie.<br/><br/> Windows Server 2016 és 2012 R2-állomások VMM 2016 felhők jelenleg nem támogatottak.<br/><br/> Központi telepítés egy létező VMM 2012 R2-ben a System Center 2016 verziófrissítést jelenleg nem támogatottak.


## <a name="support-for-replicated-machine-os-versions"></a>A replikált gép operációsrendszer-verziók támogatása

A következő táblázat összefoglalja az operációs rendszer támogatása gépeket replikálni a Site Recovery szolgáltatással. Bármilyen munkaterhelést futtathat a támogatott operációs rendszeren.

**VMware vagy fizikai kiszolgáló** | **A Hyper-V (a VMM-mel)**
--- | ---
64 bites Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2: legalább SP1<br/><br/> Red Hat Enterprise Linux 6.7, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 7.0, 7.1, 7.2 <br/><br/> Oracle 6.4 vagy 6.5 fut a Red Hat Enterprise Linux kompatibilis kernel vagy szoros vállalati Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 | A vendég operációs rendszer [Hyper-V által támogatott](https://technet.microsoft.com/library/mt126277.aspx)

## <a name="linux-machine-storage"></a>Linux-gépek tárhelye

A következő tárolási csak Linux gépek replikálhatja:

- Fájlrendszer (EXT3, ETX4, ReiserFS, XFS).
- A többutas szoftver-eszköz leképező.
- Kötetkezelő (LVM2).
- HP CCISS vezérlő tároló fizikai kiszolgálók nem támogatottak.
- SUSE Linux Enterprise Server 11 SP3 csak támogatott ReiserFS fájlrendszert.

## <a name="network-configuration"></a>Hálózati konfiguráció

### <a name="hosts"></a>Hosts

**Konfigurálás** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a VMM-mel)**
--- | --- | ---
A hálózati adapterek összevonása | Igen | Igen
VLAN | Igen | Igen
IPv4-alapú | Igen | Igen
IPv6 | Nem | Nem

### <a name="guest-vms"></a>Vendég virtuális gépek

**Konfigurálás** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a VMM-mel)**
--- | --- | ---
A hálózati adapterek összevonása | Nem | Nem
IPv4-alapú | Igen | Igen
IPv6 | Nem | Nem
Statikus IP-címet (Windows) | Igen | Igen
Statikus IP-címet (Linux) | Igen | Igen
Több hálózati Adapterrel | Igen | Igen


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Tárolók

**Tárhely (gazdagép)** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a VMM-mel)**
--- | --- | ---
NFS | Igen | N/A
SMB 3.0 | N/A | Igen
SAN (ISCSI) | Igen | Igen
(MPIO) többutas | Igen | Igen

### <a name="guest-or-physical-server-storage"></a>Vendég vagy fizikai kiszolgálón történő tárolás

**Konfigurálás** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a VMM-mel)**
--- | --- | ---
VMDK-FÁJL | Igen | N/A
VHD/VHDX | N/A | Igen (legfeljebb 16 lemez)
Generációból 2 virtuális gép | N/A | Igen
Megosztott fürtlemez | Igen  | Nem
Titkosított lemez | Nem | Nem
UEFI| Nem | N/A
NFS | Nem | Nem
SMB 3.0 | Nem | Nem
RDM | Igen | N/A
> 1 TB méretű lemez | Nem | Igen
A csíkozott > 1 TB-os kötet<br/><br/> LVM | Igen | Igen
Tárolóhelyek | Nem | Igen
Gyakran használt adatok hozzáadása lemez | Nem | Nem
Lemez kizárása | Nem | Igen
(MPIO) többutas | N/A | Igen

## <a name="vaults"></a>Tárolók

**Művelet** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a VMM-mel)**
--- | --- | ---
Erőforráscsoportok (belül vagy között, előfizetések) közötti áthelyezése közben tárolók | Nem | Nem
Tárolási, hálózati, Azure virtuális gépek között (belül vagy között, előfizetések) erőforráscsoportok áthelyezéséhez | Nem | Nem

## <a name="provider-and-agent"></a>Provider és Agent

**Name (Név)** | **Leírás** | **Legújabb verzió** | **Részletek**
--- | --- | --- | --- | ---
**Az Azure Site Recovery Providert** | Koordinálja a helyszíni kiszolgálók és az Azure közötti kommunikáció <br/><br/> A helyszíni VMM-kiszolgálókon, vagy a Hyper-V kiszolgálókon telepítve, ha nincs VMM-kiszolgáló | 5.1.19 ([elérhető portálról](http://aka.ms/downloaddra)) | [Legújabb funkcióit és javításokat](https://support.microsoft.com/kb/3155002)
**Mobilitási szolgáltatás** | Koordinálja a helyszíni VMware Server vagy fizikai kiszolgálók és a másodlagos hely közötti replikálás<br/><br/> VMware virtuális gép vagy fizikai kiszolgálók replikálni kívánt telepítve  | N/A (elérhető a portál) | N/A


## <a name="next-steps"></a>Következő lépések

- [Hyper-V virtuális gépek VMM-felhőkben replikálása egy másodlagos helyre](tutorial-vmm-to-vmm.md)
- [VMware-alapú virtuális gépek és fizikai kiszolgálók replikálása másodlagos helyre](tutorial-vmware-to-vmware.md)
