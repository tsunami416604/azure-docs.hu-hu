---
title: "Az Azure Site Recovery mátrix a replikálása Azure-bA |} Microsoft Docs"
description: "A támogatott operációs rendszerek és összetevők összegzi az Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: rajanaki
ms.openlocfilehash: 0302b4f8f4171d288a7e7c62de036c6f1cec8212
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-on-premises-to-azure"></a>Az Azure Site Recovery mátrix a helyszíni replikálása Azure-bA


Ez a cikk az Azure Site Recovery replikálásához és helyreállítása Azure által támogatott konfigurációk és összetevőket foglalja össze. Az Azure Site Recovery követelményeiről kapcsolatban bővebben lásd: a [Előfeltételek](site-recovery-prereq.md).

> [!NOTE]
> Győződjön meg arról, hogy a Site Recovery provider és agent a frissítéseket, a támogatási mátrixban kompatibilitási eléréséhez a legújabb verzióra frissíteni.


## <a name="support-for-deployment-options"></a>Telepítési lehetőségek támogatása

**Üzembe helyezés** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)** |
--- | --- | ---
**Azure Portal** | Helyszíni VMware virtuális gépek az Azure storage, Azure Resource Manager vagy a hagyományos tárolási és a hálózatok.<br/><br/> A Resource Manager-alapú vagy a klasszikus virtuális gépek feladatátvétele. | A helyszíni Hyper-V virtuális gépek Azure-tárolót, a Resource Manager és a hagyományos tárolási és a hálózatok.<br/><br/> A Resource Manager-alapú vagy a klasszikus virtuális gépek feladatátvétele.
**Klasszikus portál** | Karbantartási mód csak. Nem hozható létre új tárolók. | Karbantartási mód csak.
**PowerShell** | Jelenleg nem támogatott. | Támogatott


## <a name="support-for-datacenter-management-servers"></a>Adatközpont-felügyeleti kiszolgálók támogatása

### <a name="virtualization-management-entities"></a>Virtualizálási felügyeleti entitások

**Üzembe helyezés** | **Támogatás**
--- | ---
**VMware virtuális gép vagy fizikai kiszolgáló** | vCenter 6.5, 6.0 vagy 5.5
**A Hyper-V (Virtual Machine Managerrel)** | A System Center Virtual Machine Manager 2016 és a System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > Windows Server 2016 és 2012 R2-állomások a System Center Virtual Machine Manager 2016 felhő jelenleg nem támogatott.
  > Egy meglévő SCVMM 2012 R2-ről 2016 frissítését tartalmazó konfigurációt jelenleg nem támogatott.
### <a name="host-servers"></a>Kiszolgálók

**Üzembe helyezés** | **Támogatás**
--- | ---
**VMware virtuális gép vagy fizikai kiszolgáló** | vSphere 6.5, 6.0, 5.5
**A Hyper-V (a/nélkül a Virtual Machine Manager)** | Windows Server 2016-ot, a Windows Server 2012 R2 legújabb frissítéseit.<br></br>Az SCVMM használata esetén a Windows Server 2016 állomások SCVMM 2016 kell kezelnie.


  >[!Note]
  >A Hyper-V helyet, amely a Windows Server 2016 és 2012 R2 rendszert futtató gazdagépeken keveri jelenleg nem támogatott. A Windows Server 2016 gazdagépen virtuális gépeket egy másik helyre történő helyreállítás jelenleg nem támogatott.

## <a name="support-for-replicated-machine-os-versions"></a>A replikált gép operációsrendszer-verziók támogatása

Meg kell felelnie a védett virtuális gépek [Azure-követelményeknek](#failed-over-azure-vm-requirements) az Azure-bA replikálása esetén.
A következő táblázat összefoglalja a replikált operációs rendszer támogatásának különböző telepítési forgatókönyvek az Azure Site Recovery használata során. Ez a támogatás nem alkalmazható az említett operációs rendszer bármilyen számítási feladatot.

 **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/VMM nélkül)** |
--- | --- |
64 bites Windows Server 2016 (Server Core, az asztali élmény Server)\*, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2: legalább SP1<br/><br/> Red Hat Enterprise Linux: 5.2 való 5.11, 6.1 való 6.9, 7.0 való 7.3 <br/><br/>CentOS: 5.2 való 5.11, 6.1 való 6.9, 7.0 való 7.3 <br/><br/>Ubuntu 14.04 LTS server[ (támogatott kernel verziók)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS server[ (támogatott kernel verziók)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4, vagy a Red Hat kompatibilis kernel, vagy a szoros vállalati Kernel kiadás 3 (UEK3) 6.5 <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(A gépek replikálásához SLES 11 SP3 az SLES 11 SP4 frissítés nem támogatott. Ha a replikált gép SLES 11SP3 az SLES 11 SP4 frissítették, szüksége lesz tiltsa le a replikációt, és a post újra a frissítést a gép védelméhez.) | A vendég operációs rendszer [Azure által támogatott](https://technet.microsoft.com/library/cc794868.aspx)

>[!NOTE]
>
> \*Windows Server 2016 Nano Server nem támogatott.

>[!IMPORTANT]
>(VMware vagy fizikai kiszolgálók replikálása Azure-bA vonatkozik)
>
> Red Hat Enterprise Linux Server 7 + és CentOS 7 + kiszolgálókon kernel verzió 3.10.0-514 támogatott az Azure Site Recovery mobilitási szolgáltatás 9.8 verziójával kezdve.<br/><br/>
> Az ügyfelek a 3.10.0-514 kernel a mobilitási szolgáltatás 9.8 verziónál korábbi verziójával is tiltsa le a replikációt. frissítse a mobilitási szolgáltatás verziója 9.8 verzióra, majd engedélyezze újra a replikációt.


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>Ubuntu kernel támogatott verziók, VMware vagy fizikai kiszolgálók

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel-verzió** |
--- | --- | --- |
14.04 LTS | 9.9 | a 3.13.0-117-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-75-generic 4.4.0-21-Generic |
14.04 LTS | 9.10 | a 3.13.0-121-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-81-generic 4.4.0-21-Generic |
14.04 LTS | 9.11 | a 3.13.0-128-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-91-generic 4.4.0-21-Generic |
14.04 LTS | 9.12 | a 3.13.0-132-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-96-generic 4.4.0-21-Generic |
16.04 LTS | 9.10 | a 4.4.0-81-generic, 4.4.0-21-Generic<br/>a 4.8.0-56-generic, 4.8.0-34-Generic<br/>a 4.10.0-24-generic 4.10.0-14-Generic |
16.04 LTS | 9.11 | a 4.4.0-91-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-32-generic 4.10.0-14-Generic |
16.04 LTS | 9.12 | a 4.4.0-96-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-35-generic 4.10.0-14-Generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Támogatott fájlrendszerek és a Vendég tárolási konfigurációk alakíthatók ki Linux (VMware vagy fizikai kiszolgálók)

A következő fájl rendszerek és a tárolási konfiguráció szoftvereket támogatja a VMware vagy fizikai kiszolgálókon futó Linux-kiszolgálókon:
* Fájlrendszer: ext3, ext4, ReiserFS (Suse Linux Enterprise Server csak), XFS
* Kötetkezelő: LVM2
* A többutas szoftver: eszköz leképezője

Paravirtualized tárolási eszközök (az exportált paravirtualized-illesztőprogramok) nem támogatottak.<br/>
Több sor blokk IO eszközökön nem támogatottak.<br/>
A HP CCISS tárolóvezérlő fizikai kiszolgálók nem támogatottak.<br/>

>[!Note]
> A Linux-kiszolgálókon a következő könyvtárak (Ha külön partíciók /-fájlrendszerek beállított) kell lennie azon a lemezen (az operációs rendszer lemezének) a forráskiszolgálón: / (gyökér), / Boot, / usr, /usr/local, /var, etc<br/><br/>
> A metaadatok ellenőrzőösszeg például XFS fájlrendszerek XFSv5 szolgáltatást támogat a mobilitási szolgáltatás 9.10 verziójával kezdve. Ha XFSv5 funkciókat használ, győződjön meg arról, 9.10 vagy későbbi Mobilitásiszolgáltatás-verziót futtat. A xfs_info segédprogram segítségével ellenőrizze a XFS superblock a partíció. Ha ftype értéke 1, majd XFSv5 szolgáltatások használatban van.
>


## <a name="support-for-network-configuration"></a>Hálózati konfiguráció támogatása
A következő táblázat összefoglalja a hálózati konfiguráció támogatása a különböző telepítési forgatókönyvek, amelyek az Azure Site Recovery segítségével replikálása Azure-bA.

### <a name="host-network-configuration"></a>Gazdagép hálózati konfigurációja

**Konfigurálás** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | ---
A hálózati adapterek összevonása | Igen<br/><br/>Nem támogatott, ha a fizikai gépeket replikálja a rendszer| Igen
VLAN | Igen | Igen
IPv4 | Igen | Igen
IPv6 | Nem | Nem

### <a name="guest-vm-network-configuration"></a>Vendég virtuális gép hálózati konfigurációja

**Konfigurálás** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | ---
A hálózati adapterek összevonása | Nem | Nem
IPv4 | Igen | Igen
IPv6 | Nem | Nem
Statikus IP-címet (Windows) | Igen | Igen
Statikus IP-címet (Linux) | Igen <br/><br/>Virtuális gépek feladat-visszavétel DHCP használatára van konfigurálva.  | Nem
Több hálózati Adapterrel | Igen | Igen

### <a name="failed-over-azure-vm-network-configuration"></a>Átvevő Azure Virtuálisgép-hálózati konfiguráció

**Az Azure hálózatkezelés** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | ---
Express Route | Igen | Igen
ILB | Igen | Igen
ÜZEMBE HELYEZETT ELB | Igen | Igen
Traffic Manager | Igen | Igen
Több hálózati Adapterrel | Igen | Igen
Fenntartott IP | Igen | Igen
IPv4 | Igen | Igen
Tartsa meg a forrás IP-címe | Igen | Igen
Virtuális hálózati Szolgáltatásvégpontok (Azure Storage tűzfalak és virtuális hálózatok) | Nem | Nem


## <a name="support-for-storage"></a>Tároló támogatása
A következő táblázat összefoglalja a tárolási konfiguráció támogatása a különböző telepítési forgatókönyvek, amelyek az Azure Site Recovery segítségével replikálása Azure-bA.

### <a name="host-storage-configuration"></a>A gazdagép tároló konfigurálása

**Konfigurálás** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | --- | ---
NFS | VMware Igen<br/><br/> Fizikai kiszolgálók esetében nem | N/A
SMB 3.0 | N/A | Igen
SAN (ISCSI) | Igen | Igen
(MPIO) többutas<br></br>A tesztelt: Microsoft DSM, az EMC PowerPath 5.7 SP4, az EMC PowerPath DSM CLARiiON a | Igen | Igen

### <a name="guest-or-physical-server-storage-configuration"></a>Vendég vagy fizikai tárolással

**Konfigurálás** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | ---
VMDK-FÁJL | Igen | N/A
VHD/VHDX | N/A | Igen
Generációból 2 virtuális gép | N/A | Igen
EFI/UEFI| Nem | Igen
Megosztott fürtlemez | Nem | Nem
Titkosított lemez | Nem | Nem
NFS | Nem | N/A
SMB 3.0 | Nem | Nem
RDM | Igen<br/><br/> A fizikai kiszolgálók N/A | N/A
> 1 TB méretű lemez | Igen<br/><br/>Legfeljebb 4095 GB | Igen<br/><br/>Legfeljebb 4095 GB
4 KB-os logikai és a 4 KB-os fizikai szektort méretű lemez | Igen | Létrehozás 1 virtuális gépek esetén nem támogatott<br/><br/>Létrehozás 2 virtuális gépek esetén nem támogatott.
A 4 KB-os logikai lemez és 512 bájtos fizikai szektorméretet | Igen |  Igen
A csíkozott > 1 TB-os kötet<br/><br/> LVM logikai kötetkezelés | Igen | Igen
Tárolóhelyek | Nem | Igen
Gyakran használt adatok hozzáadása lemez | Nem | Nem
Lemez kizárása | Igen | Igen
(MPIO) többutas | N/A | Igen

**Azure Storage** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | ---
LRS | Igen | Igen
GRS | Igen | Igen
RA-GRS | Igen | Igen
Ritkán használt adatok | Nem | Nem
Gyakran használt adatok| Nem | Nem
Blokkblobok | Nem | Nem
Rest(SSE) titkosítását| Igen | Igen
Prémium szintű Storage | Igen | Igen
Import/export szolgáltatás | Nem | Nem
Virtuális hálózati szolgáltatás végpontok (Azure Storage tűzfalak és virtuális hálózatok) célként megadott konfigurált fiók vagy gyorsítótárazza a replikációs adatok tárolására használt tárfiók | Nem | Nem


## <a name="support-for-azure-compute-configuration"></a>Az Azure számítási konfigurációhoz támogatása

**Számítási szolgáltatás** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | ---
Rendelkezésre állási csoportok | Igen | Igen
HUB | Igen | Igen  
Felügyelt lemezek | Igen | Igen<br/><br/>Feladat-visszavétel a helyszínen felügyelt lemezzel rendelkező Azure virtuális gépről jelenleg nem támogatott.

## <a name="failed-over-azure-vm-requirements"></a>Átvevő Azure virtuális gép követelményei

A Site Recovery szolgáltatást az Azure által támogatott bármely operációs rendszert futtató virtuális gép és fizikai kiszolgáló replikálásához üzembe helyezheti. Ez a Windows és a Linux legtöbb verzióját magában foglalja. A helyszíni replikálni kívánt virtuális gépeknek meg kell felelnie a következő Azure követelményekkel az Azure-bA replikálása során.

**Entitás** | **Követelmények** | **Részletek**
--- | --- | ---
**Vendég operációs rendszer** | Hyper-V Azure replikáció: a Site Recovery minden operációs rendszereket támogatja [használható az Azure-](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> VMware és fizikai kiszolgáló replikációs: Ellenőrizze a Windows és Linux [Előfeltételek](site-recovery-vmware-to-azure-classic.md) | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Vendég operációs rendszer architektúrája** | 64 bites | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**Operációs rendszert tároló lemez mérete** | Ha replikál és 2048 GB **VMware virtuális gépek vagy fizikai kiszolgálók Azure-bA**.<br/><br/>Legfeljebb 2048 GB-ot **Hyper-V 1. generációs** virtuális gépeket.<br/><br/>Legfeljebb 300 GB-ot **Hyper-V 2. generációs virtuális gépek**.  | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**Operációs rendszer lemez száma** | 1 | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Adatlemez** | 64 vagy kevesebb if replikál **VMware virtuális gépek Azure-bA**; 16 vagy kevesebb Ha replikál **Hyper-V virtuális gépek Azure-bA** | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**Adattároló lemez virtuális merevlemez mérete** | Legfeljebb 4095 GB | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**Hálózati adapterek** | Több adapter támogatottak. |
**Megosztott virtuális merevlemez** | Nem támogatott | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**FC-lemez** | Nem támogatott | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**Merevlemez formátuma** | VIRTUÁLIS MEREVLEMEZ <br/><br/> VHDX | Bár VHDX jelenleg nem támogatott az Azure-ban, a Site Recovery automatikusan átalakítja VHDX virtuális merevlemezre történő feladatátadást követően Azure-bA. Ha nem sikerül vissza a helyszíni virtuális gépek továbbra is a VHDX formátum.
**A BitLocker** | Nem támogatott | A BitLocker a virtuális gépek védelme előtt le kell tiltani.
**Virtuális gép neve** | 1 és 63 karakter közötti. Kizárólag betűket, számokat és kötőjeleket tartalmazhat. A virtuális gép nevét kell kezdődnie, és betűvel vagy számmal végződhet. | Frissítse az értéket a virtuális gép tulajdonságai, a Site Recovery szolgáltatásban.
**Virtuálisgép-típussá** | 1. generációs<br/><br/> Windows – a 2. generációs | 2. generációs virtuális gépek egy basic (amely egy vagy két adatkötetek VHDX formátumú tartalmazza) lemez típusa és kisebb, mint 300 GB lemezterület támogatottak.<br></br>Linux generációs 2 virtuális gépek nem támogatottak. [További információ](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Recovery Services-tároló műveletek támogatása

**Művelet** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (nincs Virtual Machine Managerrel)** | **A Hyper-V (Virtual Machine Managerrel)**
--- | --- | --- | ---
Erőforráscsoportok közötti áthelyezése közben tároló<br/><br/> Belül és között előfizetések | Nem | Nem | Nem
Tárolási, hálózati, Azure virtuális gépek között erőforráscsoportok áthelyezéséhez<br/><br/> Belül és között előfizetések | Nem | Nem | Nem


## <a name="support-for-provider-and-agent"></a>Provider és Agent támogatása

**Name (Név)** | **Leírás** | **Legújabb verzió** | **Részletek**
--- | --- | --- | --- | ---
**Az Azure Site Recovery Providert** | Koordinálja a helyszíni kiszolgálók és az Azure közötti kommunikáció <br/><br/> A helyi kiszolgálók, a Virtual Machine Manager vagy a Hyper-V kiszolgálók telepítve, ha nincs a Virtual Machine Manager-kiszolgáló | 5.1.2700.1 (elérhető a portál) | [Legújabb funkcióit és javításokat](https://aka.ms/latest_asr_updates)
**Azure Site Recovery az egységes telepítő (az Azure-bA VMware)** | Koordinálja a helyszíni VMware-kiszolgálók és az Azure közötti kommunikáció <br/><br/> Helyszíni VMware-kiszolgálókon telepítve | 9.12.4653.1 (elérhető a portál) | [Legújabb funkcióit és javításokat](https://aka.ms/latest_asr_updates)
**Mobilitási szolgáltatás** | Koordinálja a helyszíni VMware-kiszolgáló/fizikai kiszolgálók és az Azure és a másodlagos hely közötti replikálás<br/><br/> VMware virtuális gép vagy fizikai kiszolgálók replikálni kívánt telepítve  | 9.12.4653.1 (elérhető a portál) | [Legújabb funkcióit és javításokat](https://aka.ms/latest_asr_updates)
**A Microsoft Azure Recovery Services (MARS) ügynök** | Koordinálja a Hyper-V virtuális gépek és az Azure közötti replikáció<br/><br/> Telepített helyszíni Hyper-V kiszolgálón (függetlenül a Virtual Machine Manager-kiszolgáló) | Legújabb ügynököt (elérhető a portál) |






## <a name="next-steps"></a>Következő lépések
[Előfeltételek ellenőrzése](site-recovery-prereq.md)
