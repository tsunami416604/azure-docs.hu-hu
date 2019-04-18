---
title: Vész-helyreállítási VMware virtuális gépek vagy fizikai kiszolgálók másodlagos VMware-helyről az Azure Site Recovery támogatási mátrix |} A Microsoft Docs
description: A VMware virtuális gépek és fizikai kiszolgálók másodlagos helyre az Azure Site Recovery vész-helyreállítási támogatás foglalja össze.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: raynew
ms.openlocfilehash: b316d6a8293d9f23eb89e8b6fffedac316759df4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360158"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók másodlagos helyre támogatási mátrix

Ez a cikk összefoglalja, mit támogat használatakor a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás vész-helyreállítási VMware virtuális gépeket és a Windows/Linux fizikai kiszolgálók másodlagos VMware-hellyel.

- Ha azt szeretné, a VMware virtuális gépek vagy fizikai kiszolgálók replikálása az Azure-ba, tekintse át a [a támogatási mátrix](vmware-physical-azure-support-matrix.md).
- Ha azt szeretné, a Hyper-V virtuális gépek replikálása másodlagos helyre, tekintse át a [a támogatási mátrix](hyper-v-azure-support-matrix.md).

> [!NOTE]
> A helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálása az InMage Scout biztosítják. Az InMage Scout segédprogramot az Azure Site Recovery szolgáltatási előfizetés tartalmazza.


## <a name="host-servers"></a>Gazdagép-kiszolgálónak

**Operációs rendszer** | **Részletek**
--- | ---
vCenter Server | 5.5-ös, 6.0-s és 6.5-ös vCenter<br/><br/> Ha futtatta a 6.0-s vagy 6.5-ös, vegye figyelembe, hogy csak 5.5-ös szolgáltatások támogatottak.


## <a name="replicated-vm-support"></a>A replikált virtuális gépek támogatása

A következő táblázat összefoglalja a Site Recovery szolgáltatásba replikált gépek operációs rendszerének támogatása. Bármilyen számítási feladatot futtathat a támogatott operációs rendszeren.

**Operációs rendszer** | **Részletek**
--- | ---
Windows Server | 64 bites Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, legalább SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1-es, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0-ban, 7.1-es, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5-ös, a Red Hat-kompatibilis kernel, vagy a szoros vállalati Kernel kiadási 3 (UEK3) 6.8 <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Linux rendszerű gép tárolójának

A következő tároló csak Linux rendszerű gépek replikálhatók:

- Fájlrendszer (EXT3, ETX4, ReiserFS, XFS).
- A többutas szoftver-eszköz Eseményleképező.
- Volume manager (LVM2).
- A HP CCISS vezérlő storage fizikai kiszolgálók nem támogatottak.
- SUSE Linux Enterprise Server 11 SP3 csak támogatott a ReiserFS fájlrendszer.

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
Vendég virtuális gép – Windows/Linux - statikus IP-cím | Igen
Vendég virtuális gép – több hálózati Adapterrel | Igen


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Tárolók

**Storage (gazdagép)** | **Támogatott** 
--- | --- 
NFS | Igen 
SMB 3.0 | – 
TÁROLÓHÁLÓZAT (ISCSI) | Igen 
Több elérési út (MPIO) | Igen 

### <a name="guest-or-physical-server-storage"></a>Vendég- és fizikai kiszolgáló

**Konfigurálás** | **Támogatott** 
--- | --- 
VMDK | Igen 
VHD/VHDX | – 
Velikost haldy 2 virtuális gép | – 
A megosztott fürtlemezre | Igen 
Titkosított lemez | Nem 
UEFI| Igen 
NFS | Nem 
SMB 3.0 | Nem 
RDM | Igen 
> 1 TB-os lemez | Igen 
A csíkozott lemez > 1 TB-os kötetet<br/><br/> LVM | Igen 
A tárolóhelyek | Nem 
Gyakori elérésű hozzáadása/eltávolítása lemez | Igen 
Lemez kizárása | Igen 
Több elérési út (MPIO) | – 

## <a name="vaults"></a>Tárolók

**Művelet** | **Támogatott** 
--- | --- 
Helyezze át a tárolók több erőforráscsoportban (belül vagy előfizetések között) | Nem 
Át a tárterületet, hálózati, Azure-beli virtuális gépek több erőforráscsoportban (belül vagy előfizetések között) | Nem 

## <a name="mobility-service-and-updates"></a>Mobilitási szolgáltatás és a frissítések

A mobilitási szolgáltatás koordinálja a helyszíni VMware-kiszolgálók vagy fizikai kiszolgálók és a másodlagos hely közötti replikációt. Replikációs beállításakor győződjön meg arról, hogy a mobilitási szolgáltatást, és más összetevők a legújabb verzióval rendelkezik.

| **Update** | **Részletek** |
| --- | --- |
|Scout frissítések | Scout frissítések összesítettek. <br/><br/> [Ismerje meg, és töltse le](vmware-physical-secondary-disaster-recovery.md#updates) Scout legújabb frissítései |
|Összetevő-frissítései | Scout frissítések összetevők, például a RX server, konfigurációs kiszolgáló, folyamat és a fő célkiszolgálókat, vContinuum-kiszolgáló, és védeni kívánt adatforrás-kiszolgálók frissítést is tartalmaznak.<br/><br/> [További információk](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>További lépések

Töltse le a [InMage Scout felhasználói útmutatójának](https://aka.ms/asr-scout-user-guide)

- [VMM-felhőkben futó Hyper-V virtuális gépek replikálása másodlagos helyre](tutorial-vmm-to-vmm.md)
- [VMware-alapú virtuális gépek és fizikai kiszolgálók replikálása másodlagos helyre](tutorial-vmware-to-vmware.md)
