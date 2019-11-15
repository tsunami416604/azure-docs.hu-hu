---
title: A VMware/fizikai katasztrófák helyreállításának támogatása másodlagos helyre Azure Site Recovery
description: Összefoglalja a VMware virtuális gépek és fizikai kiszolgálók vész-helyreállításának támogatását egy másodlagos helyre Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cabd3f7693c6b6b86bf0324bdafdfe1377d1ece8
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082193"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>A VMware virtuális gépek és fizikai kiszolgálók másodlagos helyre való vész-helyreállításának támogatási mátrixa

Ez a cikk azt összegzi, hogy mi támogatott, ha a [Azure site Recovery](site-recovery-overview.md) szolgáltatást használja a VMWare virtuális gépek vagy a Windows-/Linux-alapú fizikai kiszolgálók másodlagos VMware-helyekre való vész-helyreállításához.

- Ha VMware virtuális gépeket vagy fizikai kiszolgálókat szeretne replikálni az Azure-ba, tekintse át [ezt a támogatási mátrixot](vmware-physical-azure-support-matrix.md).
- Ha a Hyper-V virtuális gépeket másodlagos helyre szeretné replikálni, tekintse át [ezt a támogatási mátrixot](hyper-v-azure-support-matrix.md).

> [!NOTE]
> A helyszíni VMware virtuális gépek és fizikai kiszolgálók replikálását az inmage Scout végzi. Az inmage Scout a Azure Site Recovery szolgáltatás előfizetésében szerepel.

## <a name="end-of-support-announcement"></a>Támogatási közlemény
A helyszíni VMware-vagy fizikai adatközpontok közötti replikálás Site Recoveryi forgatókönyve támogatja a támogatás végét.

- A forgatókönyv nem konfigurálható a Recovery Services-tárolóban, és az inmage Scout szoftver nem tölthető le a tárból. 2018 A meglévő telepítések támogatottak lesznek.
- - December 31 2020-én a forgatókönyv nem támogatott.
A támogatás befejezéséig a meglévő partnerek új ügyfeleket hozhatnak be a forgatókönyvbe.
- A 2018-es és a 2019-es verzióban két frissítés jelenik meg:

    - 7\. frissítés: megjavítja a hálózati konfigurációt és a megfelelőségi problémákat, és biztosítja a TLS 1,2-támogatást.
    - 8\. frissítés: támogatást nyújt a Linux operációs rendszerek RHEL/CentOS 7.3/7.4/7.5 és SUSE 12 rendszerhez
    - A 8. frissítés után a rendszer nem szabadít fel további frissítéseket. A 8. frissítésben hozzáadott operációs rendszerek esetében korlátozott a gyorsjavítások támogatása, a hibajavítások pedig a legjobb erőfeszítés alapján.

## <a name="host-servers"></a>Gazdagép-kiszolgálók

**Operációs rendszer** | **Részletek**
--- | ---
vCenter-kiszolgáló | vCenter 5,5, 6,0 és 6,5<br/><br/> Ha a 6,0-es vagy a 6,5-es rendszerű, akkor csak a 5,5-funkciók támogatottak.


## <a name="replicated-vm-support"></a>Replikált virtuális gépek támogatása

A következő táblázat összefoglalja az operációs rendszerek támogatását Site Recovery-mel replikált gépekhez. Bármilyen munkaterhelés futtatható a támogatott operációs rendszeren.

**Operációs rendszer** | **Részletek**
--- | ---
Windows Server | 64 bites Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 legalább SP1 szervizcsomaggal.
Linux | Red Hat Enterprise Linux 6,7, 6,8, 6,9, 7,1, 7,2 <br/><br/> CentOS 6,5, 6,6, 6,7, 6,8, 6,9, 7,0, 7,1, 7,2 <br/><br/> Oracle Enterprise Linux 6,4, 6,5, 6,8, amely a Red hat kompatibilis kernelt futtatja, vagy a nem törhető Enterprise kernel 3. kiadását (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Linux rendszerű gép tárterülete

Csak a következő tárterülettel rendelkező Linux rendszerű gépek replikálása lehetséges:

- Fájlrendszer (EXT3, ETX4, ReiserFS, XFS).
- Többutas szoftver – Device mapper.
- A Volume Manager (LVM2).
- A HP CCISS-vezérlőt tároló fizikai kiszolgálók nem támogatottak.
- A ReiserFS fájlrendszer csak SUSE Linux Enterprise Server 11 SP3 rendszeren támogatott.

## <a name="network-configuration---hostguest-vm"></a>Hálózati konfiguráció – gazdagép/vendég virtuális gép

**Konfigurálás** | **Támogatott**  
--- | --- 
Gazdagép – hálózati adapterek összevonása | Igen 
Gazdagép – VLAN | Igen 
Gazdagép – IPv4 | Igen 
Gazdagép – IPv6 | Nem 
Vendég virtuális hálózati adapterek összevonása | Nem
Vendég virtuális gép – IPv4 | Igen
Vendég virtuális gép – IPv6 | Nem
Vendég virtuális gép – Windows/Linux – statikus IP-cím | Igen
Vendég virtuális gép – több hálózati adapter | Igen


## <a name="storage"></a>Tárolás

### <a name="host-storage"></a>Gazdagép tárterülete

**Storage (gazdagép)** | **Támogatott** 
--- | --- 
NFS | Igen 
SMB 3.0 | N/A 
SAN (ISCSI) | Igen 
Több elérési út (MPIO) | Igen 

### <a name="guest-or-physical-server-storage"></a>Vendég vagy fizikai kiszolgáló tárterülete

**Konfigurálás** | **Támogatott** 
--- | --- 
VMDK | Igen 
VHD/VHDX | N/A 
2\. generációs VM | N/A 
Megosztott fürtözött lemez | Igen 
Titkosított lemez | Nem 
UEFI| Igen 
NFS | Nem 
SMB 3.0 | Nem 
RDM | Igen 
Lemez > 1 TB | Igen 
Kötet szalagos lemezzel > 1 TB<br/><br/> LVM | Igen 
Tárolóhelyek | Nem 
Lemez gyors hozzáadása/eltávolítása | Igen 
Lemez kizárása | Igen 
Több elérési út (MPIO) | N/A 

## <a name="vaults"></a>Tárolók

**Művelet** | **Támogatott** 
--- | --- 
Tárolók áthelyezése az erőforráscsoportok között (vagy az előfizetések között) | Nem 
Tárterület, hálózat, Azure-beli virtuális gépek áthelyezése az erőforráscsoportok között (vagy az előfizetések között) | Nem 

## <a name="mobility-service-and-updates"></a>Mobilitási szolgáltatás és frissítések

A mobilitási szolgáltatás koordinálja a helyszíni VMware-kiszolgálók, a fizikai kiszolgálók és a másodlagos hely közötti replikációt. A replikáció beállításakor győződjön meg arról, hogy rendelkezik a mobilitási szolgáltatás legújabb verziójával és más összetevőkkel.

| **Frissítés** | **Részletek** |
| --- | --- |
|Felderítő frissítések | A Scout frissítései összegző jellegűek. <br/><br/> A legújabb Scout [-frissítések megismerése és letöltése](vmware-physical-secondary-disaster-recovery.md#updates) |
|Összetevő frissítései | A Scout frissítései tartalmazzák az összes összetevő frissítéseit, beleértve az RX-kiszolgálót, a konfigurációs kiszolgálót, a folyamat-és fő célkiszolgáló, a vContinuum-kiszolgálók és a használni kívánt forráskiszolgáló-kiszolgálókat.<br/><br/> [További információ](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Következő lépések

Az [Inmage Scout felhasználói útmutatójának](https://aka.ms/asr-scout-user-guide) letöltése

- [VMM-felhőkben lévő Hyper-V virtuális gépek replikálása másodlagos helyre](tutorial-vmm-to-vmm.md)
- [VMware-alapú virtuális gépek és fizikai kiszolgálók replikálása másodlagos helyre](tutorial-vmware-to-vmware.md)
