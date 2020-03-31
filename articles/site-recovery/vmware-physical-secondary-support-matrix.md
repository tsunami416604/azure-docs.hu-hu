---
title: VMware-támogatás/fizikai vészhelyreállítás egy másodlagos helyre az Azure Site Recovery szolgáltatással
description: Összefoglalja a vmware virtuális gépek és a fizikai kiszolgálók vész-helyreállítási támogatásának támogatását egy másodlagos helyre az Azure Site Recovery segítségével.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cabd3f7693c6b6b86bf0324bdafdfe1377d1ece8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256795"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Támogatási mátrix a VMware virtuális gépek és a fizikai kiszolgálók másodlagos helyre való vészhelyreállításához

Ez a cikk összefoglalja, hogy mi támogatott, ha az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás használata a VMware virtuális gépek vagy a Windows/Linux fizikai kiszolgálók vész-helyreállítási egy másodlagos VMware-hely.

- Ha VMware virtuális gépeket vagy fizikai kiszolgálókat szeretne replikálni az Azure-ba, tekintse át [ezt a támogatási mátrixot.](vmware-physical-azure-support-matrix.md)
- Ha hyper-v virtuális gépeket szeretne replikálni egy másodlagos helyre, tekintse át [ezt a támogatási mátrixot.](hyper-v-azure-support-matrix.md)

> [!NOTE]
> A helyszíni VMware virtuális gépek és fizikai kiszolgálók replikációját az InMage Scout biztosítja. Az InMage Scout az Azure Site Recovery szolgáltatás-előfizetésrésze.

## <a name="end-of-support-announcement"></a>Támogatás megszűnésének bejelentése
A helyszíni VMware vagy fizikai adatközpontok közötti replikáció site recovery forgatókönyve eléri a támogatás végét.

- 2018 augusztusától a forgatókönyv nem konfigurálható a Recovery Services tárolójában, és az InMage Scout szoftver nem tölthető le a tárolóból. A meglévő telepítések támogatottak lesznek.
- - 2020. december 31-től a forgatókönyv nem támogatott.
A meglévő partnerek új ügyfeleket vehetnek fel a forgatókönyvbe, amíg a támogatás véget nem ér.
- 2018-ban és 2019-ben két frissítés jelenik meg:

    - 7. frissítés: Javítja a hálózati konfigurációval és megfelelőségi problémákkal kapcsolatos problémákat, és támogatja a TLS 1.2-t.
    - 8. frissítés: Támogatja az RHEL/CentOS 7.3/7.4/7.5 linuxos operációs rendszereket és a SUSE 12-t
    - A 8- as frissítés után nem jelenik meg további frissítés. A 8.

## <a name="host-servers"></a>Állomáskiszolgálók

**Operációs rendszer** | **Részletek**
--- | ---
vCenter-kiszolgáló | vCenter 5.5, 6.0 és 6.5<br/><br/> Ha a 6.0-s vagy a 6.5-ös futtatást futtatja, vegye figyelembe, hogy csak az 5.5-ös funkciók támogatottak.


## <a name="replicated-vm-support"></a>Replikált virtuális gép támogatása

Az alábbi táblázat a Site Recovery szolgáltatással replikált gépek operációsrendszer-támogatását foglalja össze. Bármilyen számítási feladat futtatható a támogatott operációs rendszeren.

**Operációs rendszer** | **Részletek**
--- | ---
Windows Server | 64 bites Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 legalább SP1 szervizcsomaggal.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6,5, 6,6, 6,7, 6,8, 6,9, 7,0, 7,1, 7,2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 fut a Red Hat kompatibilis kernel, vagy Törhetetlen Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


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
NFS | Igen 
SMB 3.0 | N/A 
SAN (ISCSI) | Igen 
Többútvonalas (MPIO) | Igen 

### <a name="guest-or-physical-server-storage"></a>Vendég vagy fizikai kiszolgáló tárolása

**Konfiguráció** | **Támogatott** 
--- | --- 
Vmdk | Igen 
VHD/VHDX | N/A 
Gen 2 virtuális gép | N/A 
Megosztott fürtlemez | Igen 
Titkosított lemez | Nem 
Uefi| Igen 
NFS | Nem 
SMB 3.0 | Nem 
Rdm | Igen 
Lemez > 1 TB | Igen 
Kötet csíkozott lemezzel > 1 TB<br/><br/> Lvm | Igen 
Tárolóhelyek | Nem 
Gyors annektált lemez hozzáadása/eltávolítása | Igen 
Lemez kizárása | Igen 
Többútvonalas (MPIO) | N/A 

## <a name="vaults"></a>Tárolók

**Művelet** | **Támogatott** 
--- | --- 
Tárolók áthelyezése erőforráscsoportok között (előfizetéseken belül vagy azok között) | Nem 
Tároló, hálózat, Azure-beli virtuális gépek áthelyezése erőforráscsoportok között (előfizetéseken belül vagy előfizetések között) | Nem 

## <a name="mobility-service-and-updates"></a>Mobilitási szolgáltatás és frissítések

A Mobilitás szolgáltatás koordinálja a replikációt a helyszíni VMware-kiszolgálók vagy fizikai kiszolgálók, valamint a másodlagos hely között. A replikáció beállításakor győződjön meg arról, hogy a Mobility szolgáltatás és más összetevők legújabb verziójával rendelkezik.

| **Frissítés** | **Részletek** |
| --- | --- |
|Scout frissítések | A felderítő frissítések kumulatívak. <br/><br/> [Tudjon meg többet és töltse le](vmware-physical-secondary-disaster-recovery.md#updates) a Scout legújabb frissítéseit |
|Összetevő frissítései | A Felderítő-frissítések közé tartoznak a védeni kívánt összes összetevő frissítései, beleértve az RX-kiszolgálót, a konfigurációs kiszolgálót, a folyamat- és főcélkiszolgálókat, a vContinuum-kiszolgálókat és a védeni kívánt forráskiszolgálókat.<br/><br/> [További információ](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>További lépések

Az [InMage Scout felhasználói útmutatójának](https://aka.ms/asr-scout-user-guide) letöltése

- [Hyper-V virtuális gépek replikálása VMM-felhőkben egy másodlagos helyre](tutorial-vmm-to-vmm.md)
- [VMware-alapú virtuális gépek és fizikai kiszolgálók replikálása másodlagos helyre](tutorial-vmware-to-vmware.md)
