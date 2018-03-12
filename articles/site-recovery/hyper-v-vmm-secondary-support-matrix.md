---
title: "Támogatási mátrix a VMM-ben a Hyper-V virtuális gépek replikálását egy másodlagos helyre, az Azure Site Recovery felhők |} Microsoft Docs"
description: "Támogatja a Hyper-V virtuális gép replikációs egy másodlagos helyre, az Azure Site Recovery VMM-felhőkben foglalja össze."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: 767b0e76b73c082ddb75374f51700b85272f713e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="support-matrix-for-replication-of-hyper-v-vms-to-a-secondary-site"></a>A mátrix támogatása a Hyper-V virtuális gépek replikálása másodlagos helyre

Ez a cikk összefoglalja, hogy mi támogatja, ha használja a [Azure Site Recovery](site-recovery-overview.md) a másodlagos helyek a System Center Virtual Machine Manager (VMM) felhőkben felügyelt Hyper-V virtuális gépek replikálása szolgáltatást. Ha azt szeretné, a Hyper-V virtuális gépek replikálása az Azure-ba, tekintse át a [a támogatási mátrix](hyper-v-azure-support-matrix.md).

> [!NOTE]
> Csak segítségével replikálhatja egy másodlagos helyre, ha a Hyper-V-gazdagépek felügyelete a VMM-felhőkben.

  

## <a name="host-servers"></a>Host servers

**Operációs rendszer** | **Részletek**
--- | ---
Windows Server 2012 R2 | Kiszolgálók a legújabb frissítéseket kell futtatnia.
Windows Server 2016 |  Windows Server 2016 és 2012 R2-állomások VMM 2016 felhők jelenleg nem támogatottak.<br/><br/> A System Center 2016 System Center 2012 R2 VMM 2012 R2-ről frissített központi telepítések jelenleg nem támogatottak.


## <a name="replicated-vm-support"></a>Replikált virtuális gép támogatása

A következő táblázat összefoglalja az operációs rendszer támogatása gépeket replikálni a Site Recovery szolgáltatással. Bármilyen munkaterhelést futtathat a támogatott operációs rendszeren.

**Windows-verzió** | **A Hyper-V (a VMM-mel)**
--- | ---
Windows Server 2016 | A vendég operációs rendszer [Hyper-V által támogatott](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) Windows Server 2016 
Windows Server 2012 R2 | A vendég operációs rendszer [Hyper-V által támogatott](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) Windows Server 2012 R2 rendszeren

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
NFS | –
SMB 3.0 |  Igen
SAN (ISCSI) | Igen
(MPIO) többutas | Igen

### <a name="guest-or-physical-server-storage"></a>Vendég vagy fizikai kiszolgálón történő tárolás

**Konfigurálás** | **Támogatott**
--- | --- | 
VMDK |  –
VHD/VHDX | Igen (legfeljebb 16 lemez)
Generációból 2 virtuális gép | Igen
Megosztott fürtlemez | Nem
Titkosított lemez | Nem
UEFI| –
NFS | Nem
SMB 3.0 | Nem
RDM | –
> 1 TB méretű lemez | Igen
A csíkozott > 1 TB-os kötet<br/><br/> LVM | Igen
Tárolóhelyek | Igen
Gyakran használt adatok hozzáadása lemez | Nem
Lemez kizárása | Igen
(MPIO) többutas | Igen

## <a name="vaults"></a>Tárolók

**Művelet** | **Támogatott**
--- | --- 
Erőforráscsoportok (belül vagy között, előfizetések) közötti áthelyezése közben tárolók |  Nem
Tárolási, hálózati, Azure virtuális gépek között (belül vagy között, előfizetések) erőforráscsoportok áthelyezéséhez | Nem

## <a name="azure-site-recovery-provider"></a>Azure Site Recovery Provider

A szolgáltató koordinálja a VMM-kiszolgáló közötti kommunikáció. 

**legújabb** | **Frissítések**
--- | --- | --- | --- | ---
5.1.19 ([elérhető portálról](http://aka.ms/downloaddra) | [Legújabb funkcióit és javításokat](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>További lépések

[Hyper-V virtuális gépek VMM-felhőkben replikálása egy másodlagos helyre](tutorial-vmm-to-vmm.md)

