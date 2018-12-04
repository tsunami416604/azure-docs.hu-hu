---
title: Vész-helyreállítási a helyszíni Hyper-V virtuális gépek az Azure-támogatási mátrixa |} A Microsoft Docs
description: Összefoglalja a támogatott összetevők és a Hyper-V virtuális gép vészhelyreállítás az Azure-bA az Azure Site Recovery követelményei
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: 8020db5f099f0d2c3dc8203f2d4afeea6a38cb41
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52844753"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Vész-helyreállítási a helyszíni Hyper-V virtuális gépek az Azure-támogatási mátrixa


Ez a cikk összefoglalja a támogatott összetevők és az Azure-bA a helyszíni Hyper-V virtuális gépek vész-helyreállítási beállítások használatával [Azure Site Recovery](site-recovery-overview.md).


## <a name="supported-scenarios"></a>Támogatott esetek

**Forgatókönyv** | **Részletek**
--- | ---
A Hyper-V Virtual Machine Manager | Vészhelyreállítás az Azure-bA végezheti a System Center Virtual Machine Manager-hálóban felügyelt Hyper-V gazdagépeken futó virtuális gépek számára.<br/><br/> Ebben a forgatókönyvben az Azure Portalon vagy a PowerShell használatával is telepítheti.<br/><br/> Ha a Virtual Machine Manager által felügyelt Hyper-V-gazdagépek, is elvégezhet vészhelyreállítást egy másodlagos helyszíni helyre. Ebben a forgatókönyvben kapcsolatos további információkért olvassa el [ebben az oktatóanyagban](hyper-v-vmm-disaster-recovery.md).
A Hyper-V Virtual Machine Manager nélkül | Vészhelyreállítás az Azure-bA végezheti a Virtual Machine Manager által felügyelt Hyper-V gazdagépeken futó virtuális gépek.<br/><br/> Ebben a forgatókönyvben az Azure Portalon vagy a PowerShell használatával is telepítheti.


## <a name="on-premises-servers"></a>Helyszíni kiszolgálók

**Kiszolgáló** | **Követelmények** | **Részletek**
--- | --- | ---
A Hyper-V (fut a Virtual Machine Manager nélkül) | A Windows Server 2016 (beleértve server core telepítés), Windows Server 2012 R2 legújabb frissítéseit | A Hyper-V helyet a Site Recoveryben konfigurálásakor a Windows Server 2016 és 2012 R2 rendszert futtató gazdagépeken keverése nem támogatott.<br/><br/> A virtuális gépek Windows Server 2016 rendszerű gazdagépen található egy másik helyre történő helyreállítás nem támogatott.
A Hyper-V (fut a Virtual Machine Manager) | Virtual Machine Manager 2016-Virtual Machine Manager 2012 R2-ben | Virtual Machine Manager használata esetén a Windows Server 2016-gazdagépeket a Virtual Machine Manager 2016-ban kell kezelni.<br/><br/> A Virtual Machine Manager cloud, amely a Windows Server 2016 és 2012 R2 rendszert futtató Hyper-V-gazdagépek eredményét jelenleg nem támogatott.<br/><br/> Egy meglévő Virtual Machine Manager 2012 R2 Server 2016-ra való frissítés tartalmazó környezetek nem támogatottak.


## <a name="replicated-vms"></a>A replikált virtuális gépek


Az alábbi táblázat foglalja össze a virtuális gépek támogatása. A Site Recovery a támogatott operációs rendszeren futó bármilyen számítási feladatot támogat.

 **Összetevő** | **Részletek**
--- | ---
Virtuálisgép-konfiguráció | Az Azure-bA replikált virtuális gépek meg kell felelnie [Azure-követelmények](#azure-vm-requirements).
Vendég operációs rendszer | Bármilyen vendég operációs rendszer [az Azure-ban támogatott](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)...<br/><br/> A Windows Server 2016 Nano Server nem támogatott.


## <a name="vmdisk-management"></a>A felügyeleti Virtuálisgép-lemez

**Művelet** | **Részletek**
--- | ---
A replikált Hyper-V virtuális lemez átméretezése | Nem támogatott. Tiltsa le a replikációt, és módosítsa a majd újraengedélyezni a virtuális gép replikálását.
Lemez hozzáadása a replikált Hyper-V virtuális Gépen | Nem támogatott. Tiltsa le a replikációt, és módosítsa a majd újraengedélyezni a virtuális gép replikálását.

## <a name="hyper-v-network-configuration"></a>A Hyper-V hálózati konfigurációja

**Összetevő** | **A Hyper-V Virtual Machine Manager** | **A Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Gazdagép-hálózat: NIC-összevonás | Igen | Igen
Gazdagép hálózati: VLAN | Igen | Igen
Gazdagép hálózati: IPv4 | Igen | Igen
Gazdagép hálózati: IPv6 | Nem | Nem
Vendég Virtuálisgép-hálózat: NIC-összevonás | Nem | Nem
Vendég virtuális gép hálózati: IPv4 | Igen | Igen
Vendég virtuális gép hálózati: IPv6 | Nem | Igen
Vendég virtuális gép hálózati: statikus IP-cím (Windows) | Igen | Igen
Vendég virtuális gép hálózati: statikus IP-cím (Linux) | Nem | Nem
Vendég virtuális gép hálózati: több hálózati Adapterrel | Igen | Igen



## <a name="azure-vm-network-configuration-after-failover"></a>Az Azure VM-hálózat konfiguráció (feladatátvétel) után

**Összetevő** | **A Hyper-V Virtual Machine Manager** | **A Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Azure ExpressRoute | Igen | Igen
ILB | Igen | Igen
ELB | Igen | Igen
Azure Traffic Manager | Igen | Igen
Multi-NIC | Igen | Igen
Fenntartott IP | Igen | Igen
IPv4 | Igen | Igen
Forrás IP-cím megőrzése | Igen | Igen
Az Azure virtuális hálózati Szolgáltatásvégpontok<br/> (nem Azure Storage-tűzfalak) | Igen | Igen
Gyorsított hálózatkezelés | Nem | Nem


## <a name="hyper-v-host-storage"></a>A Hyper-V-gazdagép tárolási

**Tárolás** | **A Hyper-V Virtual Machine Manager** | **A Hyper-V Virtual Machine Manager nélkül**
--- | --- | --- | ---
AZ NFS | NA | NA
SMB 3.0 | Igen | Igen
TÁROLÓHÁLÓZAT (ISCSI) | Igen | Igen
Több elérési út (MPIO). Tesztelték:<br></br> A Microsoft DSM-et, EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM CLARiiON számára | Igen | Igen

## <a name="hyper-v-vm-guest-storage"></a>A Hyper-V virtuális gép vendég storage

**Tárolás** | **A Hyper-V Virtual Machine Manager** | **A Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
VMDK | NA | NA
VHD/VHDX | Igen | Igen
2. generációs virtuális gép | Igen | Igen
EFI/UEFI| Igen | Igen
A megosztott fürtlemezre | Nem | Nem
Titkosított lemez | Nem | Nem
AZ NFS | NA | NA
SMB 3.0 | Nem | Nem
RDM | NA | NA
Lemez > 1 TB-ot | Igen, legfeljebb 4095 GB-ig | Igen, legfeljebb 4095 GB-ig
Lemez: 4 KB-os logikai és fizikai szektort. | Nem támogatott: 1/2. generációs gyűjtések általános | Nem támogatott: 1/2. generációs gyűjtések általános
Lemez: 4K logikai és 512 bájtnál nagyobb fizikai szektormérettel | Igen |  Igen
Logikaikötet-kezelő (LVM). LVM csak az adatlemezek használata támogatott. Az Azure az egyetlen operációsrendszer-lemez biztosít. | Igen | Igen
A csíkozott lemez-kötettel > 1 TB-ot | Igen | Igen
A tárolóhelyek | Igen | Igen
Gyakori elérésű hozzáadása/eltávolítása lemez | Nem | Nem
Lemez kizárása | Igen | Igen
Több elérési út (MPIO) | Igen | Igen

## <a name="azure-storage"></a>Azure Storage

**Összetevő** | **A Hyper-V Virtual Machine Manager** | **A Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Helyileg redundáns tárolás | Igen | Igen
Georedundáns tárolás | Igen | Igen
Írásvédett georedundáns tárolás | Igen | Igen
Ritka elérésű tárterület | Nem | Nem
Gyakori elérésű tárolási| Nem | Nem
Blokkblobok | Nem | Nem
Titkosítás inaktív állapotban (SSE)| Igen | Igen
Prémium szintű Storage | Igen | Igen
Importálási/exportálási szolgáltatás | Nem | Nem
A cél tárolási illetve gyorsítótárfiók (replikációs adatainak tárolására használt) konfigurált virtuális hálózatok az Azure Storage-tűzfalak | Nem | Nem


## <a name="azure-compute-features"></a>Azure-beli számítási szolgáltatások

**Funkció** | **A Hyper-V Virtual Machine Manager** | **A Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Rendelkezésre állási csoportok | Igen | Igen
HUB | Igen | Igen  
Felügyelt lemezek | Igen, a feladatátvételre.<br/><br/> A felügyelt lemezek a feladat-visszavétel nem támogatott. | Igen, a feladatátvételre.<br/><br/> A felügyelt lemezek a feladat-visszavétel nem támogatott.

## <a name="azure-vm-requirements"></a>Azure Virtuálisgép-követelmények

Az Azure-bA replikált helyszíni virtuális gépek meg kell felelnie az Azure virtuális gépekre vonatkozó, az alábbi táblázatban foglaltak.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | A Site Recovery minden operációs rendszereket támogatja [Azure által támogatott](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).  | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Operációsrendszer-lemez mérete | Legfeljebb 2048 GB 1. generációs virtuális gépek számára.<br/><br/> Legfeljebb 300 GB 2. generációs virtuális gépek számára.  | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Adatlemezek száma | 16 vagy kevesebb  | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Virtuális merevlemez adatlemez mérete | Legfeljebb 4095 GB-ig | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter támogatott |
Megosztott VHD | Nem támogatott | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Merevlemez formátuma | VHD <br/><br/> VHDX | A Site Recovery automatikusan átalakítja a VHDX VHD-t, ha átadja a feladatokat az Azure-bA. Visszaadja a feladatokat a helyszíni, ha a virtuális gépek továbbra is a VHDX formátumot használja.
BitLocker | Nem támogatott | A BitLocker egy virtuális gép a replikáció engedélyezése előtt le kell tiltani.
a virtuális gép neve | 1 és 63 karakter között. Csak betűket, számokat és kötőjelet tartalmazhat. A virtuális gép nevének betűvel vagy számmal kell kezdődnie és végződnie. | Frissítse az értéket a virtuális gép tulajdonságait, a Site Recoveryben.
Virtuális gép típusa | Az 1.<br/><br/> Windows – a 2. generációs | 2. generációs virtuális gépek egy operációs rendszer lemez típusa (amely tartalmaz egy vagy két adatkötetek VHDX formátumú) az alapszintű és a kevesebb mint 300 GB méretű lemezterület is támogatottak.<br></br>Linux rendszerű Generation 2 virtuális gépek nem támogatottak. [További információk](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Recovery Services vault műveletek

**Művelet** |  **A Hyper-V Virtual Machine Manager** | **A Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Tároló áthelyezése erőforráscsoportok közt<br/><br/> Belül és azok az előfizetések között | Nem | Nem
Tárolás, hálózat, Azure-beli virtuális gépek erőforráscsoportok közötti áthelyezése<br/><br/> Belül és azok az előfizetések között | Nem | Nem


## <a name="provider-and-agent"></a>Provider és Agent

Ahhoz, hogy a központi telepítés beállításai Ez a cikk a kompatibilis, ellenőrizze a legújabb szolgáltató és az ügynökverziókat futtatja.

**Name (Név)** | **Leírás** | **Részletek**
--- | --- | --- | --- | ---
Az Azure Site Recovery provider | Koordinálja a helyszíni kiszolgálók és Azure közötti kommunikáció <br/><br/> A Hyper-V Virtual Machine Manager: a Virtual Machine Manager-kiszolgálókra telepített<br/><br/> A Hyper-V Virtual Machine Manager nélkül: telepítve a Hyper-V-gazdagépek| Legújabb verzió: 5.1.2700.1 (az Azure Portalról érhető el)<br/><br/> [Legújabb funkciókkal és javításokkal](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
A Microsoft Azure Recovery Services-ügynök | Koordinálja a replikációt a Hyper-V virtuális gépek és az Azure között<br/><br/> Telepítve van a helyszíni Hyper-V kiszolgálók (a vagy Virtual Machine Manager nélkül) | A portálon elérhető legújabb ügynök






## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [készítheti elő az Azure](tutorial-prepare-azure.md) vész-helyreállítási a helyszíni Hyper-V virtuális gépek.
