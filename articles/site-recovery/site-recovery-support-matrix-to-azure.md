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
ms.date: 02/06/2018
ms.author: rajanaki
ms.openlocfilehash: a17d0918ea5938daf81c469fd6402a7dc9764831
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
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
**PowerShell** | Támogatott | Támogatott


## <a name="support-for-datacenter-management-servers"></a>Adatközpont-felügyeleti kiszolgálók támogatása

### <a name="virtualization-management-entities"></a>Virtualizálási felügyeleti entitások

**Üzembe helyezés** | **Támogatás**
--- | ---
**VMware virtuális gép vagy fizikai kiszolgáló** | vCenter 6.5, 6.0 vagy 5.5
**A Hyper-V (Virtual Machine Managerrel)** | A System Center Virtual Machine Manager 2016 és a System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > Windows Server 2016 és 2012 R2-állomások a System Center Virtual Machine Manager 2016 felhő jelenleg nem támogatott.
  > Egy meglévő SCVMM 2012 R2-ről 2016 frissítését tartalmazó konfigurációt jelenleg nem támogatott.
### <a name="host-servers"></a>Host servers

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
64 bites Windows Server 2016 (Server Core, az asztali élmény Server)\*, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2: legalább SP1<br/><br/> Red Hat Enterprise Linux: 5.2 való 5.11, 6.1 való 6.9, 7.0-7.4<br/><br/>CentOS: 5.2 való 5.11, 6.1 való 6.9, 7.0-7.4 <br/><br/>Ubuntu 14.04 LTS server[ (támogatott kernel verziók)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS server[ (támogatott kernel verziók)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4, vagy a Red Hat kompatibilis kernel, vagy a szoros vállalati Kernel kiadás 3 (UEK3) 6.5 <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(A gépek replikálásához SLES 11 SP3 az SLES 11 SP4 frissítés nem támogatott. Ha a replikált gép SLES 11SP3 az SLES 11 SP4 frissítették, szüksége lesz tiltsa le a replikációt, és a post újra a frissítést a gép védelméhez.) | A vendég operációs rendszer [Azure által támogatott](https://technet.microsoft.com/library/cc794868.aspx)

>[!NOTE]
>
> \* Windows Server 2016 Nano Server nem támogatott.
>
> A Linux terjesztéseket csak a készlet kernelek alverzió kiadás/frissítése a terjesztési részét képező támogatottak.
>
> A Linux-disztribúció egy Azure Site Recovery a fő verziói közötti frissítés VMware rendszerű virtuális gép védett, vagy fizikai kiszolgáló nem támogatott. Az operációs rendszer frissítésekor (például CentOS 6.* CentOS 7.*) Főverziók közötti, tiltsa le a replikációt a gép, frissítse az operációs rendszert a számítógépen, és majd engedélyezze újra a replikációt.
>


### <a name="supported-ubuntu-kernel-versions-for-vmwarephysical-servers"></a>Ubuntu kernel támogatott verziók, VMware vagy fizikai kiszolgálók

**Release** | **Mobilitási szolgáltatás verziója** | Kernel-verzió |
--- | --- | --- |
14.04 LTS | 9.10 | a 3.13.0-121-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-81-generic 4.4.0-21-Generic |
14.04 LTS | 9.11 | a 3.13.0-128-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-91-generic 4.4.0-21-Generic |
14.04 LTS | 9.12 | a 3.13.0-132-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-96-generic 4.4.0-21-Generic |
14.04 LTS | 9.13 | a 3.13.0-137-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-104-generic 4.4.0-21-Generic |
16.04 LTS | 9.10 | a 4.4.0-81-generic, 4.4.0-21-Generic<br/>a 4.8.0-56-generic, 4.8.0-34-Generic<br/>a 4.10.0-24-generic 4.10.0-14-Generic |
16.04 LTS | 9.11 | a 4.4.0-91-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-32-generic 4.10.0-14-Generic |
16.04 LTS | 9.12 | a 4.4.0-96-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-35-generic 4.10.0-14-Generic |
16.04 LTS | 9.13 | a 4.4.0-104-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic 4.10.0-14-Generic |

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Támogatott fájlrendszerek és a Vendég tárolási konfigurációk alakíthatók ki Linux (VMware vagy fizikai kiszolgálók)

A következő fájl rendszerek, valamint a tárolási konfiguráció szoftver támogatottak, VMware vagy fizikai kiszolgálókon futó Linux-kiszolgálókon:
* Fájlrendszer: ext3, ext4, ReiserFS (Suse Linux Enterprise Server csak), XFS
* Kötetkezelő: LVM2
* A többutas szoftver: eszköz leképezője

Paravirtualized tárolási eszközök (az exportált paravirtualized-illesztőprogramok) nem támogatottak.<br/>
Több sor blokk IO eszközökön nem támogatottak.<br/>
A HP CCISS tárolóvezérlő fizikai kiszolgálók nem támogatottak.<br/>

>[!Note]
> A Linux-kiszolgálókon a következő könyvtárak (Ha külön partíciók /-fájlrendszerek beállított) kell lennie azon a lemezen (az operációs rendszer lemezének) a forráskiszolgálón: / (gyökér), / Boot, / usr, /usr/local, /var, etc; és a/Boot lemezpartíción legyen, és nem lehet egy LVM kötet<br/><br/>
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
Static IP (Windows) | Igen | Igen
Statikus IP-címet (Linux) | Igen <br/><br/>Virtuális gépek feladat-visszavétel DHCP használatára vannak konfigurálva.  | Nem
Multi-NIC | Igen | Igen

### <a name="failed-over-azure-vm-network-configuration"></a>Átvevő Azure Virtuálisgép-hálózati konfiguráció

Az Azure hálózatkezelés | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | ---
Express Route | Igen | Igen
ILB | Igen | Igen
ELB | Igen | Igen
Traffic Manager | Igen | Igen
Multi-NIC | Igen | Igen
Fenntartott IP | Igen | Igen
IPv4 | Igen | Igen
Tartsa meg a forrás IP-címe | Igen | Igen
Virtuális hálózati Szolgáltatásvégpontok (Azure Storage tűzfalak és virtuális hálózatok) | Nem | Nem


## <a name="support-for-storage"></a>Tároló támogatása
A következő táblázat összefoglalja a tárolási konfiguráció támogatása a különböző telepítési forgatókönyvek, amelyek az Azure Site Recovery segítségével replikálása Azure-bA.

### <a name="host-storage-configuration"></a>A gazdagép tároló konfigurálása

**Konfigurálás** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | --- | ---
NFS | VMware Igen<br/><br/> Fizikai kiszolgálók esetében nem | –
SMB 3.0 | – | Igen
SAN (ISCSI) | Igen | Igen
(MPIO) többutas<br></br>A tesztelt: Microsoft DSM, az EMC PowerPath 5.7 SP4, az EMC PowerPath DSM CLARiiON a | Igen | Igen

### <a name="guest-or-physical-server-storage-configuration"></a>Vendég vagy fizikai tárolással

**Konfigurálás** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | ---
VMDK | Igen | –
VHD/VHDX | – | Igen
Generációból 2 virtuális gép | – | Igen
EFI/UEFI| Áttelepítés Azure a Windows Server 2012 és újabb verziók VMware virtuális gépek csak. </br></br> ** Megjegyzés: a tábla végén tekintse meg.  | Igen
Megosztott fürtlemez | Nem | Nem
Titkosított lemez | Nem | Nem
NFS | Nem | –
SMB 3.0 | Nem | Nem
RDM | Igen<br/><br/> A fizikai kiszolgálók N/A | –
> 1 TB méretű lemez | Igen<br/><br/>Legfeljebb 4095 GB | Igen<br/><br/>Legfeljebb 4095 GB
4 KB-os logikai és a 4 KB-os fizikai szektort méretű lemez | Igen | Létrehozás 1 virtuális gépek esetén nem támogatott<br/><br/>Létrehozás 2 virtuális gépek esetén nem támogatott.
A 4 KB-os logikai lemez és 512 bájtos fizikai szektorméretet | Igen |  Igen
A csíkozott > 1 TB-os kötet<br/><br/> LVM logikai kötetkezelés | Igen | Igen
Tárolóhelyek | Nem | Igen
Gyakran használt adatok hozzáadása lemez | Nem | Nem
Lemez kizárása | Igen | Igen
(MPIO) többutas | – | Igen

> [!NOTE]
> ** UEFI rendszerindítási VMware virtuális gépek Windows Server 2012 rendszert futtató, vagy később telepíthetők át az Azure-bA. Következő korlátozások vonatkoznak.
> - Áttelepítés az Azure-bA csak. A feladat-visszavétel a helyszíni VMware-hely nem támogatott.
> - Az operációsrendszer-lemezképet, a kiszolgáló legfeljebb 4 partíciók támogatottak.
> - Azure Site Recovery mobilitási szolgáltatás 9.13 vagy újabb verziója szükséges.
> - Fizikai kiszolgálók esetében nem támogatott.

**Azure Storage** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | ---
LRS | Igen | Igen
GRS | Igen | Igen
RA-GRS | Igen | Igen
Ritkán használt adatok | Nem | Nem
Gyakran használt adatok| Nem | Nem
Blokkblobok | Nem | Nem
Encryption at rest(SSE)| Igen | Igen
Prémium szintű Storage | Igen | Igen
Import/export szolgáltatás | Nem | Nem
Virtuális hálózati szolgáltatás végpontok (Azure Storage tűzfalak és virtuális hálózatok) célként megadott konfigurált fiók vagy gyorsítótárazza a replikációs adatok tárolására használt tárfiók | Nem | Nem
Általános célú V2 storage-fiókok (a gyakran és ritkán. szint) | Nem | Nem


## <a name="support-for-azure-compute-configuration"></a>Az Azure számítási konfigurációhoz támogatása

**Számítási szolgáltatás** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (a/nélkül a Virtual Machine Manager)**
--- | --- | ---
Rendelkezésre állási csoportok | Igen | Igen
HUB | Igen | Igen  
Felügyelt lemezek | Igen | Igen<br/><br/>Feladat-visszavétel a helyszínen felügyelt lemezzel rendelkező Azure virtuális gépről jelenleg nem támogatott.

## <a name="failed-over-azure-vm-requirements"></a>Átvevő Azure virtuális gép követelményei

A Site Recovery szolgáltatást az Azure által támogatott bármely operációs rendszert futtató virtuális gép és fizikai kiszolgáló replikálásához üzembe helyezheti. Ez a Windows és a Linux legtöbb verzióját magában foglalja. A helyszíni replikálni kívánt virtuális gépeknek meg kell felelnie a következő Azure követelményekkel az Azure-bA replikálása során.

**Entitás** | Követelmények | **Részletek**
--- | --- | ---
Vendég operációs rendszer | Hyper-V Azure replikáció: a Site Recovery minden operációs rendszereket támogatja [használható az Azure-](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> VMware és fizikai kiszolgáló replikációs: Ellenőrizze a Windows és Linux [Előfeltételek](site-recovery-vmware-to-azure-classic.md) | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Vendég operációs rendszer architektúrája** | 64 bites | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**Operációsrendszer-lemez mérete** | Ha replikál és 2048 GB **VMware virtuális gépek vagy fizikai kiszolgálók Azure-bA**.<br/><br/>Legfeljebb 2048 GB-ot **Hyper-V 1. generációs** virtuális gépeket.<br/><br/>Legfeljebb 300 GB-ot **Hyper-V 2. generációs virtuális gépek**.  | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**Operációsrendszer-lemezek száma** | 1 | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Adatlemezek száma** | 64 vagy kevesebb if replikál **VMware virtuális gépek Azure-bA**; 16 vagy kevesebb Ha replikál **Hyper-V virtuális gépek Azure-bA** | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**Adatlemez virtuális merevlemezének mérete** | Legfeljebb 4095 GB-ig | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**Hálózati adapterek** | Több adapter támogatott |
**Megosztott VHD** | Nem támogatott | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**FC-lemez** | Nem támogatott | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**Merevlemez formátuma** | VHD <br/><br/> VHDX | Bár a VHDX jelenleg nem támogatott az Azure-ban, a Site Recovery automatikusan átalakítja a VHDX-et VHD-re, amikor feladatátvételt hajt végre az Azure-ba. Ha nem sikerül vissza a helyszíni virtuális gépek továbbra is a VHDX formátum.
**Bitlocker** | Nem támogatott | A BitLocker a virtuális gépek védelme előtt le kell tiltani.
**Virtuális gép neve** | 1 és 63 karakter között. Csak betűket, számokat és kötőjelet tartalmazhat. A virtuális gép nevének betűvel vagy számmal kell kezdődnie és végződnie. | Frissítse az értéket a virtuális gép tulajdonságai, a Site Recovery szolgáltatásban.
**Virtuális gép típusa** | 1. generációs<br/><br/> Windows – a 2. generációs | 2. generációs virtuális gépek egy basic (amely egy vagy két adatkötetek VHDX formátumú tartalmazza) lemez típusa és kisebb, mint 300 GB lemezterület támogatottak.<br></br>Linux generációs 2 virtuális gépek nem támogatottak. [További információ](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Recovery Services-tároló műveletek támogatása

**Művelet** | **VMware vagy fizikai kiszolgáló** | **A Hyper-V (nincs Virtual Machine Managerrel)** | **A Hyper-V (Virtual Machine Managerrel)**
--- | --- | --- | ---
Erőforráscsoportok közötti áthelyezése közben tároló<br/><br/> Belül és között előfizetések | Nem | Nem | Nem
Tárolási, hálózati, Azure virtuális gépek között erőforráscsoportok áthelyezéséhez<br/><br/> Belül és között előfizetések | Nem | Nem | Nem


## <a name="support-for-provider-and-agent"></a>Provider és Agent támogatása

**Name (Név)** | **Leírás** | **legújabb verzió** | **Részletek**
--- | --- | --- | --- | ---
**Az Azure Site Recovery Providert** | Koordinálja a helyszíni kiszolgálók és az Azure közötti kommunikáció <br/><br/> A helyi kiszolgálók, a Virtual Machine Manager vagy a Hyper-V kiszolgálók telepítve, ha nincs a Virtual Machine Manager-kiszolgáló | 5.1.2700.1 (elérhető a portál) | [Legújabb funkcióit és javításokat](https://aka.ms/latest_asr_updates)
**Azure Site Recovery az egységes telepítő (az Azure-bA VMware)** | Koordinálja a helyszíni VMware-kiszolgálók és az Azure közötti kommunikáció <br/><br/> Helyszíni VMware-kiszolgálókon telepítve | 9.12.4653.1 (elérhető a portál) | [Legújabb funkcióit és javításokat](https://aka.ms/latest_asr_updates)
**Mobilitási szolgáltatás** | Koordinálja a helyszíni VMware-kiszolgáló/fizikai kiszolgálók és az Azure és a másodlagos hely közötti replikálás<br/><br/> VMware virtuális gép vagy fizikai kiszolgálók replikálni kívánt telepítve  | 9.12.4653.1 (elérhető a portál) | [Legújabb funkcióit és javításokat](https://aka.ms/latest_asr_updates)
**A Microsoft Azure Recovery Services (MARS) ügynök** | Koordinálja a Hyper-V virtuális gépek és az Azure közötti replikáció<br/><br/> Telepített helyszíni Hyper-V kiszolgálón (függetlenül a Virtual Machine Manager-kiszolgáló) | Legújabb ügynököt (elérhető a portál) |






## <a name="next-steps"></a>További lépések
[Előfeltételek ellenőrzése](site-recovery-prereq.md)
