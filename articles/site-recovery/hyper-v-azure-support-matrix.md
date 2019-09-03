---
title: A helyszíni Hyper-V virtuális gépek Azure-ba való vész-helyreállításának támogatási mátrixa | Microsoft Docs
description: Összefoglalja a Hyper-V virtuális gépeknek az Azure-ba történő helyreállításának támogatott összetevőit és követelményeit Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: b4f9c0ab3ca50b0ce8c9ba27d8773c58a72dcfa9
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70230966"
---
# <a name="support-matrix-for-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>A helyszíni Hyper-V virtuális gépek Azure-ba való vész-helyreállításának támogatási mátrixa


Ez a cikk a helyszíni Hyper-V virtuális gépek Azure-ba történő helyreállításának támogatott összetevőit és beállításait foglalja össze [Azure site Recovery](site-recovery-overview.md)használatával.


## <a name="supported-scenarios"></a>Támogatott esetek

**Forgatókönyv** | **Részletek**
--- | ---
Hyper-V Virtual Machine Manager | A System Center Virtual Machine Manager-hálóban felügyelt Hyper-V-gazdagépeken futó virtuális gépek esetén vész-helyreállítást hajthat végre az Azure-ban.<br/><br/> Ezt a forgatókönyvet a Azure Portal vagy a PowerShell használatával is telepítheti.<br/><br/> Ha a Hyper-V-gazdagépeket a Virtual Machine Manager felügyeli, a másodlagos helyszíni helyre is elvégezheti a vész-helyreállítást. Ha többet szeretne megtudni erről a forgatókönyvről, olvassa el [ezt](hyper-v-vmm-disaster-recovery.md)az oktatóanyagot.
Hyper-V Virtual Machine Manager nélkül | A nem a Virtual Machine Manager által felügyelt Hyper-V-gazdagépeken futó virtuális gépek esetén vész-helyreállítást végezhet az Azure-ban.<br/><br/> Ezt a forgatókönyvet a Azure Portal vagy a PowerShell használatával is telepítheti.


## <a name="on-premises-servers"></a>Helyszíni kiszolgálók

**Server** | **Követelmények** | **Részletek**
--- | --- | ---
Hyper-V (Virtual Machine Manager nélkül fut) |  Windows Server 2019, Windows Server 2016 (beleértve a Server Core telepítést), a Windows Server 2012 R2 és a legújabb frissítések | Ha már konfigurálta a Windows Server 2012 R2-t a/vagy a SCVMM 2012 R2-vel Azure Site Recovery és tervezi az operációs rendszer frissítését, kövesse az útmutató [dokumentációját.](upgrade-2012R2-to-2016.md) 
Hyper-V (fut Virtual Machine Manager) | Virtual Machine Manager 2019, Virtual Machine Manager 2016, Virtual Machine Manager 2012 R2 | Ha Virtual Machine Manager van használatban, a Windows Server 2019-gazdagépeket Virtual Machine Manager 2019-ben kell felügyelni. Hasonlóképpen, a Windows Server 2016-gazdagépeket Virtual Machine Manager 2016-ben kell felügyelni.<br/><br/>


## <a name="replicated-vms"></a>Replikált virtuális gépek


A következő táblázat összefoglalja a virtuális gépek támogatását. Site Recovery támogatja a támogatott operációs rendszereken futó munkaterheléseket.

 **Összetevő** | **Részletek**
--- | ---
Virtuális gép konfigurációja | Az Azure-ba replikált virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](#azure-vm-requirements).
Vendég operációs rendszer | Bármely [, az Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-guestos-update-matrix#family-5-releases)-hoz támogatott vendég operációs rendszer.<br/><br/> A Windows Server 2016 Nano Server nem támogatott.


## <a name="vmdisk-management"></a>VIRTUÁLIS gépek/lemezek kezelése

**Művelet** | **Részletek**
--- | ---
Lemez átméretezése replikált Hyper-V virtuális gépen | Nem támogatott. Tiltsa le a replikációt, végezze el a módosítást, majd engedélyezze újra a virtuális gép replikálását.
Lemez hozzáadása a replikált Hyper-V virtuális gépen | Nem támogatott. Tiltsa le a replikációt, végezze el a módosítást, majd engedélyezze újra a virtuális gép replikálását.

## <a name="hyper-v-network-configuration"></a>Hyper-V hálózati konfiguráció

**Összetevő** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Gazdagép hálózata: Hálózati adapterek összevonása | Igen | Igen
Gazdagép hálózata: Virtuális helyi hálózat | Igen | Igen
Gazdagép hálózata: IPv4 | Igen | Igen
Gazdagép hálózata: IPv6 | Nem | Nem
Vendég VM-hálózat: Hálózati adapterek összevonása | Nem | Nem
Vendég VM-hálózat: IPv4 | Igen | Igen
Vendég VM-hálózat: IPv6 | Nem | Igen
Vendég VM-hálózat: Statikus IP-cím (Windows) | Igen | Igen
Vendég VM-hálózat: Statikus IP-cím (Linux) | Nem | Nem
Vendég VM-hálózat: Multi-NIC | Igen | Igen



## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM hálózati konfiguráció (feladatátvétel után)

**Összetevő** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Azure ExpressRoute | Igen | Igen
ILB | Igen | Igen
ELB | Igen | Igen
Azure Traffic Manager | Igen | Igen
Multi-NIC | Igen | Igen
Fenntartott IP | Igen | Igen
IPv4 | Igen | Igen
Forrás IP-címének megőrzése | Igen | Igen
Azure Virtual Network szolgáltatási végpontok<br/> (Azure Storage-tűzfalak nélkül) | Igen | Igen
Gyorsított hálózatkezelés | Nem | Nem


## <a name="hyper-v-host-storage"></a>Hyper-V gazdagép tárolója

**Storage** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | --- 
NFS | NA | NA
SMB 3.0 | Igen | Igen
SAN (ISCSI) | Igen | Igen
Több elérési út (MPIO). Tesztelve:<br></br> Microsoft DSM, EMC PowerPath 5,7 SP4<br/><br/> EMC PowerPath DSM a CLARiiON | Igen | Igen

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V virtuális gép Guest Storage

**Storage** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
VMDK | NA | NA
VHD/VHDX | Igen | Igen
2\. generációs VM | Igen | Igen
EFI/UEFI| Igen | Igen
Megosztott fürtözött lemez | Nem | Nem
Titkosított lemez | Nem | Nem
NFS | NA | NA
SMB 3.0 | Nem | Nem
RDM | NA | NA
Lemez > 1 TB | Igen, akár 4 095 GB-ig | Igen, akár 4 095 GB-ig
Lemez 4K logikai és fizikai szektor | Nem támogatott: 1. generációs/2. gen | Nem támogatott: 1. generációs/2. gen
Lemez 4K logikai és 512 bájtos fizikai szektor | Igen |  Igen
Logikai kötet kezelése (LVM). Az LVM csak adatlemezeken támogatott. Az Azure csak egyetlen operációsrendszer-lemezt biztosít. | Igen | Igen
Kötet szalagos lemezzel > 1 TB | Igen | Igen
Tárolóhelyek | Nem | Nem
Lemez gyors hozzáadása/eltávolítása | Nem | Nem
Lemez kizárása | Igen | Igen
Több elérési út (MPIO) | Igen | Igen

## <a name="azure-storage"></a>Azure Storage

**Összetevő** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Helyileg redundáns tárolás | Igen | Igen
Georedundáns tárolás | Igen | Igen
Olvasási hozzáférés – geo-redundáns tárolás | Igen | Igen
Ritkán használt tároló | Nem | Nem
Gyors tárolás| Nem | Nem
Blokkblobok | Nem | Nem
Titkosítás nyugalmi állapotban (SSE)| Igen | Igen
Prémium szintű Storage | Igen | Igen
Importálási/exportálási szolgáltatás | Nem | Nem
Azure Storage-tűzfalak a cél Storage/cache Storage-fiókban konfigurált virtuális hálózatokhoz (a replikációs adattároláshoz használatos) | Nem | Nem


## <a name="azure-compute-features"></a>Az Azure számítási funkciói

**Funkció** | **Hyper-V Virtual Machine Manager** | **Hyper-V Virtual Machine Manager nélkül**
--- | --- | ---
Rendelkezésre állási csoportok | Igen | Igen
HUB | Igen | Igen  
Felügyelt lemezek | Igen, a feladatátvételhez.<br/><br/> A felügyelt lemezek feladat-visszavétele nem támogatott. | Igen, a feladatátvételhez.<br/><br/> A felügyelt lemezek feladat-visszavétele nem támogatott.

## <a name="azure-vm-requirements"></a>Azure-beli virtuális gépekre vonatkozó követelmények

Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az ebben a táblázatban összefoglalt Azure-beli virtuálisgép-követelményeknek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | A Site Recovery az [Azure által támogatott](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)összes operációs rendszert támogatja.  | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 32 bites (Windows Server 2008)/64-bit | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Operációs rendszer lemezének mérete | Akár 2 048 GB az 1. generációs virtuális gépekhez.<br/><br/> Akár 300 GB a 2. generációs virtuális gépek számára.  | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Adatlemezek száma | 16 vagy kevesebb  | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Adatlemez virtuális merevlemezének mérete | Legfeljebb 4 095 GB | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter támogatott |
Megosztott virtuális merevlemez | Nem támogatott | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott | Az Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
Merevlemez formátuma | VHD <br/><br/> VHDX | A Site Recovery automatikusan átalakítja a VHDX a VHD-re, amikor feladatátvételt hajt végre az Azure-ba. Ha a feladatátvételt a helyszíni rendszerre végzi, a virtuális gépek továbbra is a VHDX formátumot használják.
BitLocker | Nem támogatott | A virtuális gép replikálásának engedélyezése előtt le kell tiltani a BitLockert.
a virtuális gép neve | 1 és 63 karakter között. Csak betűket, számokat és kötőjelet tartalmazhat. A virtuális gép nevének betűvel vagy számmal kell kezdődnie és végződnie. | Frissítse az értéket a virtuális gép tulajdonságainál Site Recoveryban.
Virtuális gép típusa | 1\. generáció<br/><br/> Generation 2--Windows | A 2. generációs virtuális gépek alapszintű operációsrendszer-lemezzel (amely egy vagy két VHDX formázott adatkötetet tartalmaz) és kevesebb mint 300 GB lemezterületet támogat.<br></br>A Linux 2. generációs virtuális gépek nem támogatottak. [További információk](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).|

## <a name="recovery-services-vault-actions"></a>Recovery Services tároló műveletei

**Művelet** |  **Hyper-V és VMM** | **Hyper-V VMM nélkül**
--- | --- | ---
Tár áthelyezése az erőforráscsoportok között<br/><br/> Előfizetéseken belül és között | Nem | Nem
Tárterület, hálózat, Azure-beli virtuális gépek áthelyezése az erőforráscsoportok között<br/><br/> Előfizetéseken belül és között | Nem | Nem

> [!NOTE]
> Ha a helyszíni rendszerből az Azure-ba replikálja a Hyper-VM-et, replikálhat csak egy AD-bérlőre egy adott környezetből – a Hyper-V-helyről vagy a Hyper-V-t a VMM-mel.


## <a name="provider-and-agent"></a>Provider és Agent

Annak ellenőrzéséhez, hogy a telepítés kompatibilis-e a jelen cikkben található beállításokkal, győződjön meg arról, hogy a legújabb szolgáltatói és ügynök-verziókat futtatja.

**Name** | **Leírás** | **Részletek**
--- | --- | --- 
Azure Site Recovery szolgáltató | A helyszíni kiszolgálók és az Azure közötti kommunikáció koordinálása <br/><br/> Hyper-V Virtual Machine Managerkal: Virtual Machine Manager kiszolgálókon telepítve<br/><br/> Hyper-V Virtual Machine Manager nélkül: Hyper-V gazdagépekre telepítve| Legújabb verzió: 5.1.2700.1 (elérhető a Azure Portal)<br/><br/> [Legújabb funkciók és javítások](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery)
Microsoft Azure Recovery Services ügynök | A Hyper-V virtuális gépek és az Azure közötti replikáció koordinálása<br/><br/> Helyszíni Hyper-V-kiszolgálókra telepítve (Virtual Machine Manager) vagy anélkül | A portálon elérhető legújabb ügynök






## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [készítheti elő az Azure](tutorial-prepare-azure.md) -t a helyszíni Hyper-V virtuális gépek vész-helyreállításához.
