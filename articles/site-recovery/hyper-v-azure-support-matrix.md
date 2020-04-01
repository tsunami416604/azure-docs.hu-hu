---
title: A Hyper-V virtuális gépek Azure-ba való vészhelyreállítása támogatása az Azure Site Recovery segítségével
description: Összegzi a Hyper-V VM vész-helyreállítási az Azure-ba az Azure-ba az Azure Site Recovery-vel támogatott összetevőket és követelményeket
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 1/27/2020
ms.author: raynew
ms.openlocfilehash: fd10468e823201bfa67aaf7c570071bd075ec4ac
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420826"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Támogatási mátrix a helyszíni Hyper-V virtuális gépek Azure-ba való vészhelyreállításához


Ez a cikk összefoglalja a helyszíni Hyper-V virtuális gépek azure-ba történő vész-helyreállítási támogatott összetevőit és beállításait az [Azure Site Recovery](site-recovery-overview.md)használatával.



## <a name="supported-scenarios"></a>Támogatott esetek

**Forgatókönyv** | **Részletek**
--- | ---
Hyper-V a Virtuálisgép-kezelővel <br> <br>| Vész-helyreállítási az Azure-ban futó virtuális gépek a System Center Virtual Machine Manager-környezetben kezelt, A System Center Virtual Machine Manager.<br/><br/> Ezt a forgatókönyvet az Azure Portalon vagy a PowerShell használatával telepítheti.<br/><br/> Ha a Hyper-V-állomásokat a Virtual Machine Manager kezeli, vészhelyreállítást is végrehajthat egy másodlagos helyszíni helyen. Ha többet szeretne megtudni erről a forgatókönyvről, olvassa el [ezt az oktatóanyagot.](hyper-v-vmm-disaster-recovery.md)
Hyper-V virtuálisgép-kezelő nélkül | Vész-helyreállítási az Azure-ban futó virtuális gépek, amelyek nem a Virtual Machine Manager által felügyelt.<br/><br/> Ezt a forgatókönyvet az Azure Portalon vagy a PowerShell használatával telepítheti.

## <a name="on-premises-servers"></a>Helyszíni kiszolgálók

**Kiszolgáló** | **Követelmények** | **Részletek**
--- | --- | ---
Hyper-V (virtuálisgép-kezelő nélkül futó) |  Windows Server 2019, Windows Server 2016 (beleértve a kiszolgálómag telepítését), Windows Server 2012 R2 a legújabb frissítésekkel | Ha már konfigurálta a Windows Server 2012 R2-t/vagy az SCVMM 2012 R2-t az Azure Site Recovery szolgáltatással, és tervezi az operációs rendszer frissítését, kövesse az útmutató [dokumentációját.](upgrade-2012R2-to-2016.md) 
Hyper-V (a Virtual Machine Manager rel fut) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Ha virtuálisgép-kezelőt használ, a Windows Server 2019-állomásokat a Virtual Machine Manager 2019-ben kell kezelni. Hasonlóképpen a Windows Server 2016-állomásokat is a Virtual Machine Manager 2016-ban kell kezelni.<br/><br/> Megjegyzés: A Windows Server 2019 állomásai nem támogatják a feladat-visszavételt a másik helyre.


## <a name="replicated-vms"></a>Replikált virtuális gépek


Az alábbi táblázat összefoglalja a virtuális gépek támogatását. A Site Recovery támogatja a támogatott operációs rendszeren futó munkaterheléseket.

 **Összetevő** | **Részletek**
--- | ---
Virtuális gép konfigurációja | Az Azure-ra replikáló virtuális gépeknek meg kell felelniük az [Azure követelményeinek.](#azure-vm-requirements)
Vendég operációs rendszer | Az [Azure-hoz támogatott](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)vendég operációs rendszer..<br/><br/> A Windows Server 2016 Nano Server nem támogatott.


## <a name="vmdisk-management"></a>Virtuális gép/lemez kezelése

**Művelet** | **Részletek**
--- | ---
Lemez átméretezése replikált Hyper-V virtuális gépen | Nem támogatott. Tiltsa le a replikációt, módosítsa, majd engedélyezze újra a replikációt a virtuális gép számára.
Lemez hozzáadása a replikált Hyper-V virtuális géphez | Nem támogatott. Tiltsa le a replikációt, módosítsa, majd engedélyezze újra a replikációt a virtuális gép számára.

## <a name="hyper-v-network-configuration"></a>Hyper-V hálózati konfiguráció

**Összetevő** | **Hyper-V a Virtuálisgép-kezelővel** | **Hyper-V virtuálisgép-kezelő nélkül**
--- | --- | ---
Gazdahálózat: Hálózati adapterek összeállása | Igen | Igen
Állomáshálózat: VLAN | Igen | Igen
Állomáshálózat: IPv4 | Igen | Igen
Gazdahálózat: IPv6 | Nem | Nem
Vendég virtuális gép hálózata: hálózati adapterek összeállása | Nem | Nem
Vendég virtuális gép hálózata: IPv4 | Igen | Igen
Vendég virtuális gép hálózata: IPv6 | Nem | Igen
Vendég virtuális gép hálózata: Statikus IP (Windows) | Igen | Igen
Vendég virtuális gép hálózat: Statikus IP (Linux) | Nem | Nem
Vendég virtuális gép hálózata: Multi-NIC | Igen | Igen
Https Proxy | Nem | Nem



## <a name="azure-vm-network-configuration-after-failover"></a>Az Azure VM hálózati konfigurációja (feladatátvétel után)

**Összetevő** | **Hyper-V a Virtuálisgép-kezelővel** | **Hyper-V virtuálisgép-kezelő nélkül**
--- | --- | ---
Azure ExpressRoute | Igen | Igen
ILB | Igen | Igen
Elb | Igen | Igen
Azure Traffic Manager | Igen | Igen
Több hálózati adapter | Igen | Igen
Fenntartott IP | Igen | Igen
IPv4 | Igen | Igen
Forrás IP-címének megőrzése | Igen | Igen
Az Azure virtuális hálózat szolgáltatásának végpontjai<br/> (Azure Storage-tűzfalak nélkül) | Igen | Igen
Gyorsított hálózatkezelés | Nem | Nem


## <a name="hyper-v-host-storage"></a>Hyper-V gazdagép tárolása

**Tárterület** | **Hyper-V a Virtuálisgép-kezelővel** | **Hyper-V virtuálisgép-kezelő nélkül**
--- | --- | --- 
NFS | NA | NA
SMB 3.0 | Igen | Igen
SAN (ISCSI) | Igen | Igen
Többútvonalas (MPIO). A következővel tesztelve:<br></br> Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM CLARiiON-hoz | Igen | Igen

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM vendégtároló

**Tárterület** | **Hyper-V a Virtuálisgép-kezelővel** | **Hyper-V virtuálisgép-kezelő nélkül**
--- | --- | ---
Vmdk | NA | NA
VHD/VHDX | Igen | Igen
2. generációs VM | Igen | Igen
EFI/UEFI<br></br>Az azure-ban áttelepített virtuális gép automatikusan bios-rendszerindítási virtuális gépté alakul át. A virtuális gépnek csak Windows Server 2012-es és újabb rendszert kell futtatnia. Az operációs rendszer lemezének legfeljebb öt partícióval vagy kevesebbel kell rendelkeznie, és az operációsrendszer-lemez méretének 300 GB-nál kisebbnek kell lennie.| Igen | Igen
Megosztott fürtlemez | Nem | Nem
Titkosított lemez | Nem | Nem
NFS | NA | NA
SMB 3.0 | Nem | Nem
Rdm | NA | NA
Lemez >1 TB | Igen, akár 4095 GB | Igen, akár 4095 GB
Lemez: 4K logikai és fizikai szektor | Nem támogatott: Gen 1/Gen 2 | Nem támogatott: Gen 1/Gen 2
Lemez: 4K logikai és 512 bájtos fizikai szektor | Igen |  Igen
Logikai kötetkezelés (LVM). Az LVM csak adatlemezeken támogatott. Az Azure csak egyetlen operációsrendszer-lemezt biztosít. | Igen | Igen
Kötet csíkozott lemezzel >1 TB | Igen | Igen
Tárolóhelyek | Nem | Nem
Gyors annektált lemez hozzáadása/eltávolítása | Nem | Nem
Lemez kizárása | Igen | Igen
Többútvonalas (MPIO) | Igen | Igen

## <a name="azure-storage"></a>Azure Storage

**Összetevő** | **Hyper-V a Virtuálisgép-kezelővel** | **Hyper-V virtuálisgép-kezelő nélkül**
--- | --- | ---
Helyileg redundáns tárolás | Igen | Igen
Georedundáns tárolás | Igen | Igen
Írásvédett georedundáns tárolás (RA-GRS) | Igen | Igen
Hűvös tárolás | Nem | Nem
Forró tárolás| Nem | Nem
Blokkblobok | Nem | Nem
Titkosítás inaktív helyen (SSE)| Igen | Igen
Titkosítás inaktív helyen (CMK) <br></br> (Csak a felügyelt lemezekre való feladatátvételhez)| Igen (a PowerShell Az 3.3.0 modulon keresztül) | Igen (a PowerShell Az 3.3.0 modulon keresztül)
Prémium szintű Storage | Igen | Igen
Importálási/exportálási szolgáltatás | Nem | Nem
Azure Storage-fiókok engedélyezve tűzfallal | Igen. Céltárolóhoz és gyorsítótárhoz. | Igen. Céltárolóhoz és gyorsítótárhoz.
Tárfiók módosítása | Nem. A cél Azure Storage-fiók nem módosítható a replikáció engedélyezése után. A módosításhoz tiltsa le, majd engedélyezze újra a vészhelyreállítást. | Nem


## <a name="azure-compute-features"></a>Az Azure számítási funkciói

**Szolgáltatás** | **Hyper-V a Virtuálisgép-kezelővel** | **Hyper-V virtuálisgép-kezelő nélkül**
--- | --- | ---
Rendelkezésre állási csoportok | Igen | Igen
Hub | Igen | Igen  
Felügyelt lemezek | Igen, a feladatátvételhez.<br/><br/> A felügyelt lemezek feladat-visszavétele nem támogatott. | Igen, a feladatátvételhez.<br/><br/> A felügyelt lemezek feladat-visszavétele nem támogatott.

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikáló helyszíni virtuális gépeknek meg kell felelniük az ebben a táblázatban összefoglalt Azure virtuális gép követelményeinek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | A Site Recovery az [Azure által támogatott](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)összes operációs rendszert támogatja.  | Az előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 32 bites (Windows Server 2008)/64 bites | Az előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Operációsrendszer-lemez mérete | Akár 2048 GB az 1.<br/><br/> Akár 300 GB a 2.  | Az előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Adatlemezek száma | 16 vagy kevesebb  | Az előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Adatlemez virtuális merevlemezének mérete | Akár 4095 GB | Az előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter támogatott |
Megosztott VHD | Nem támogatott | Az előfeltételek ellenőrzése sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott | Az előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Merevlemez formátuma | VHD <br/><br/> VHDX | A Site Recovery automatikusan átalakítja a VHDX-et virtuális merevlemezzésre, ha az Azure-ba kerül. Ha nem adja vissza a helyszíni, a virtuális gépek továbbra is a VHDX formátumban továbbra is.
BitLocker | Nem támogatott | A BitLocker szolgáltatást le kell tiltani, mielőtt engedélyezné a virtuális gép replikációját.
a virtuális gép neve | 1 és 63 karakter között. Csak betűket, számokat és kötőjelet tartalmazhat. A virtuális gép nevének betűvel vagy számmal kell kezdődnie és végződnie. | Frissítse a hely-helyreállítási virtuálisgép-tulajdonságok értékét.
Virtuális gép típusa | 1. generációs<br/><br/> 2. generáció - Windows | Generációs 2 virtuális gépek, amelyek operációs rendszer lemeztípusa alapszintű (amely egy vagy két, VHDX formátumú adatkötetet tartalmaz) és kevesebb mint 300 GB lemezterület támogatott.<br></br>A Linux Generation 2 virtuális gépek nem támogatottak. [További információ](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Helyreállítási szolgáltatások tárolójának műveletei

**Művelet** |  **Hyper-V VMM-mel** | **Hyper-V VMM nélkül**
--- | --- | ---
Tároló áthelyezése erőforráscsoportok között<br/><br/> Előfizetéseken belül és között | Nem | Nem
Tároló, hálózat, Azure-beli virtuális gépek áthelyezése erőforráscsoportok között<br/><br/> Előfizetéseken belül és között | Nem | Nem

> [!NOTE]
> A hyper-vms replikálása a helyszíni Azure-ba, replikálhatja csak egy AD-bérlő egy adott környezetben - Hyper-V hely vagy Hyper-V V VMM adott esetben.


## <a name="provider-and-agent"></a>Provider és Agent

Győződjön meg arról, hogy a központi telepítés kompatibilis a cikkben szereplő beállításokkal, győződjön meg arról, hogy a legújabb szolgáltatói és ügynökverziókat futtatja.

**Név** | **Leírás** | **Részletek**
--- | --- | --- 
Azure site recovery szolgáltató | Koordinálja a helyszíni kiszolgálók és az Azure közötti kommunikációt <br/><br/> Hyper-V a Virtuálisgép-kezelővel: Telepítve a Virtual Machine Manager kiszolgálóin<br/><br/> Hyper-V virtuálisgép-kezelő nélkül: Telepítve a Hyper-V gazdagépeken| Legújabb verzió: 5.1.2700.1 (elérhető az Azure Portalon)<br/><br/> [Legújabb funkciók és javítások](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services-ügynök | Koordinálja a hyper-V virtuális gépek és az Azure közötti replikációt<br/><br/> Helyszíni Hyper-V kiszolgálókra telepítve (virtuálisgép-kezelővel vagy anélkül) | A portálról elérhető legújabb ügynök






## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [készítheti elő](tutorial-prepare-azure.md) az Azure-t a helyszíni Hyper-V virtuális gépek vészutáni helyreállítására.
