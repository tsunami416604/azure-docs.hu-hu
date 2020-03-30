---
title: A matrix-Hyper-V vész-helyreállítási szolgáltatás támogatása egy másodlagos VMM-helyre az Azure Site Recovery szolgáltatással
description: Összefoglalja a Hyper-V VM replikációjának támogatását a VMM-felhőkben egy másodlagos helyre az Azure Site Recovery segítségével.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 1126a85ed22ee17879767a93ca75dc76dd04b747
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74132966"
---
# <a name="support-matrix-for-disaster-recovery-of-hyper-v-vms-to-a-secondary-site"></a>Támogatási mátrix Hyper-V virtuális gépek másodlagos helyre történő vészhelyreállításához

Ez a cikk összefoglalja, hogy mi támogatott, ha az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás használatával replikálja a System Center virtuálisgép-felhőkben kezelt Hyper-V virtuális gépeket egy másodlagos helyre. Ha hyper-v virtuális gépeket szeretne replikálni az Azure-ba, tekintse át [ezt a támogatási mátrixot.](hyper-v-azure-support-matrix.md)

> [!NOTE]
> Csak akkor replikálható egy másodlagos helyre, ha a Hyper-V gazdagépek vmm-felhőkben vannak kezelve.


## <a name="host-servers"></a>Állomáskiszolgálók

**Operációs rendszer** | **Részletek**
--- | ---
Windows Server 2012 R2 | A kiszolgálóknak a legújabb frissítéseket kell futtatniuk.
Windows Server 2016 |  A VMM 2016-felhők a Windows Server 2016 és a 2012 R2 gazdagépeinek keverékével jelenleg nem támogatottak.<br/><br/> A System Center 2012 R2 VMM 2012 R2 rendszerről system center 2016-ra frissített telepítések jelenleg nem támogatottak.


## <a name="replicated-vm-support"></a>Replikált virtuális gép támogatása

Az alábbi táblázat a Site Recovery szolgáltatással replikált gépek operációsrendszer-támogatását foglalja össze. Bármilyen számítási feladat futtatható a támogatott operációs rendszeren.

**Windows-verzió** | **Hyper-V (VMM-mel)**
--- | ---
Windows Server 2016 | A [Hyper-V által támogatott](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Windows-guest-operating-systems-for-Hyper-V-on-Windows) bármely vendég operációs rendszer Windows Server 2016 rendszeren 
Windows Server 2012 R2 | A [Hyper-V által támogatott](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn792027%28v%3dws.11%29) bármely vendég operációs rendszer Windows Server 2012 R2 rendszeren

## <a name="linux-machine-storage"></a>Linux gépi tárolás

Csak a következő tárolóval rendelkező Linux-gépek replikálhatók:

- fájlrendszer (EXT3, ETX4, ReiserFS, XFS).
- Többutas szoftver-eszköz mapper.
- Kötetkezelő (LVM2).
- A HP CCISS vezérlőtárolóval rendelkező fizikai kiszolgálók nem támogatottak.
- A ReiserFS fájlrendszer csak a SUSE Linux Enterprise Server 11 SP3 szervizcsomagban támogatott.

## <a name="network-configuration---hostguest-vm"></a>Hálózati konfiguráció - Állomás/Vendég virtuális gép

**Konfiguráció** | **Támogatott**  
--- | --- 
Állomás - hálózati adapterek összeállása | Igen 
Fogadó - VLAN | Igen 
Állomás - IPv4 | Igen 
Állomás - IPv6 | Nem 
Vendég virtuális gép - hálózati adapterek összeáll | Nem
Vendég virtuális gép - IPv4 | Igen
Vendég VM - IPv6 | Nem
Vendég virtuális gép - Windows/Linux - Statikus IP-cím | Igen
Vendég virtuális gép - multi-NIC | Igen


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Gazdagép tárolása

**Tárolás (állomás)** | **Támogatott**
--- | --- 
NFS | N/A
SMB 3.0 |  Igen
SAN (ISCSI) | Igen
Többútvonalas (MPIO) | Igen

### <a name="guest-or-physical-server-storage"></a>Vendég vagy fizikai kiszolgáló tárolása

**Konfiguráció** | **Támogatott**
--- | --- | 
Vmdk |  N/A
VHD/VHDX | Igen (legfeljebb 16 lemez)
Gen 2 virtuális gép | Igen
Megosztott fürtlemez | Nem
Titkosított lemez | Nem
Uefi| N/A
NFS | Nem
SMB 3.0 | Nem
Rdm | N/A
Lemez > 1 TB | Igen
Kötet csíkozott lemezzel > 1 TB<br/><br/> Lvm | Igen
Tárolóhelyek | Igen
Gyors annektált lemez hozzáadása/eltávolítása | Nem
Lemez kizárása | Igen
Többútvonalas (MPIO) | Igen

## <a name="vaults"></a>Tárolók

**Művelet** | **Támogatott**
--- | --- 
Tárolók áthelyezése erőforráscsoportok között (előfizetéseken belül vagy azok között) |  Nem
Tároló, hálózat, Azure-beli virtuális gépek áthelyezése erőforráscsoportok között (előfizetéseken belül vagy előfizetések között) | Nem

## <a name="azure-site-recovery-provider"></a>Azure webhely-helyreállítási szolgáltató

A Szolgáltató koordinálja a VMM-kiszolgálók közötti kommunikációt. 

**Legújabb** | **Frissítések**
--- | --- 
5.1.19 ([elérhető a portálról](https://aka.ms/downloaddra) | [Legújabb funkciók és javítások](https://support.microsoft.com/kb/3155002)



## <a name="next-steps"></a>További lépések

[Hyper-V virtuális gépek replikálása VMM-felhőkben egy másodlagos helyre](tutorial-vmm-to-vmm.md)

