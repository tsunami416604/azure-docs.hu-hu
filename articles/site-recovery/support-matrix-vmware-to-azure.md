---
title: "Az Azure Site Recovery mátrix a VMware virtuális gépek és fizikai kiszolgálók replikálása Azure-bA |} Microsoft Docs"
description: "A támogatott operációs rendszerek és a VMware virtuális gépek replikálása Azure-bA az Azure Site Recovery szolgáltatással összetevőket foglalja össze."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 01/11/2018
ms.author: raynew
ms.openlocfilehash: 857bbd42fda4abddd9a7551f4de016cecae03868
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>VMware és fizikai kiszolgálók replikálást az Azure-támogatási mátrix


Ez a cikk foglalja össze, támogatott összetevők és az Azure-ba, a VMware virtuális gépek vész-helyreállítási beállítások használata a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.


## <a name="supported-scenarios"></a>Támogatott esetek

**Scenario** | **Részletek** 
--- | --- 
**VMware virtuális gépek** | Helyszíni VMware virtuális gépek esetén az Azure-bA vész-helyreállítási végezheti el. Ebben a forgatókönyvben az Azure-portálon, vagy a PowerShell használatával telepítheti.
**Fizikai kiszolgálók** | A helyszíni windowsos/Linuxos fizikai kiszolgálók Azure-bA vész-helyreállítási végezheti el. Ebben a forgatókönyvben az Azure portálon telepítése.

## <a name="on-premises-virtualization-servers"></a>A helyszíni virtualizálási kiszolgálók

**Kiszolgáló** | **Követelmények** | **Részletek**
--- | --- | ---
**VMware** | vCenter Server 6.5-ös, 6.0 vagy 5.5 vagy vSphere 6.5, 6.0, 5.5 | Javasoljuk, hogy a vCenter-kiszolgáló használata
**Fizikai kiszolgálók** | NA


## <a name="replicated-machines"></a>Replikált gép

A következő táblázat összefoglalja a gépek replikáció támogatása. Bármelyik támogatott operációs rendszerű gépen futó feladat replikálását a Site Recovery támogatja.

**Összetevő** | **Részletek**
--- | ---
Számítógép-konfiguráció | Meg kell felelnie a gépek replikálása Azure-bA [Azure-követelményeknek](#failed-over-azure-vm-requirements).
Gép operációs rendszerének (Windows) | 64 bites Windows Server 2016 (Server Core, az asztali élmény Server)\*, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2: legalább SP1
Gép operációs rendszerének (Linux) | Red Hat Enterprise Linux: 5.2 való 5.11, 6.1 való 6.9, 7.0 való 7.3 <br/><br/>CentOS: 5.2 való 5.11, 6.1 való 6.9, 7.0 való 7.3 <br/><br/>Ubuntu 14.04 LTS server[ (támogatott kernel verziók)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Ubuntu 16.04 LTS server[ (támogatott kernel verziók)](#supported-ubuntu-kernel-versions-for-vmwarephysical-servers)<br/><br/>Debian 7 <br/><br/>Debian 8<br/><br/>Oracle Enterprise Linux 6.4, vagy a Red Hat kompatibilis kernel, vagy a szoros vállalati Kernel kiadás 3 (UEK3) 6.5 <br/><br/>SUSE Linux Enterprise Server 11 SP3 <br/><br/>SUSE Linux Enterprise Server 11 SP4 <br/>(A gépek replikálásához SLES 11 SP3 az SLES 11 SP4 frissítés nem támogatott. Ha a replikált gép SLES 11SP3 az SLES 11 SP4 frissítették, szüksége lesz tiltsa le a replikációt, és a post újra a frissítést a gép védelméhez.)
Linux kernel verziója | Red Hat Enterprise Linux Server 7 + és CentOS 7 + kiszolgálókon kernel verzió 3.10.0-514 támogatott az Azure Site Recovery mobilitási szolgáltatás 9.8 verziójával kezdve.<br/><br/> Az ügyfelek a 3.10.0-514 kernel a mobilitási szolgáltatás 9.8 verziónál korábbi verziójával is tiltsa le a replikációt. frissítse a mobilitási szolgáltatás verziója 9.8 verzióra, majd engedélyezze újra a replikációt.


### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel-verziók


**Támogatott kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel-verzió** |
--- | --- | --- |
14.04 LTS | 9.9 | a 3.13.0-117-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-75-generic 4.4.0-21-Generic |
14.04 LTS | 9.10 | a 3.13.0-121-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-81-generic 4.4.0-21-Generic |
14.04 LTS | 9.11 | a 3.13.0-128-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-91-generic 4.4.0-21-Generic |
14.04 LTS | 9.12 | a 3.13.0-132-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-96-generic 4.4.0-21-Generic |
16.04 LTS | 9.10 | a 4.4.0-81-generic, 4.4.0-21-Generic<br/>a 4.8.0-56-generic, 4.8.0-34-Generic<br/>a 4.10.0-24-generic 4.10.0-14-Generic |
16.04 LTS | 9.11 | a 4.4.0-91-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-32-generic 4.10.0-14-Generic |
16.04 LTS | 9.12 | a 4.4.0-96-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-35-generic 4.10.0-14-Generic |

## <a name="linux-file-systemsguest-storage-configurations"></a>Linux fájl rendszerek/Vendég tárolási konfigurációk

**Összetevő** | **Támogatott**
--- | ---
fájlrendszer | ext3, ext4, ReiserFS (csak Suse Linux Enterprise Server), XFS
Kötetkezelő | LVM2
A többutas szoftver | Eszköz leképezője
Paravirtualized tárolóeszközök | Paravirtualized illesztőprogramok által exportált eszközökön nem támogatottak.
Több sor blokk IO eszközök | Nem támogatott.
A HP CCISS tárolóvezérlő fizikai kiszolgálók | Nem támogatott.
Könyvtárak | Ezeket a könyvtárakat (Ha külön partíciók /-fájlrendszerek beállított) kell lennie az azonos operációsrendszer-lemezképet a forráskiszolgálón lévő: / (gyökér), / Boot, / usr, /usr/local, /var, etc
XFSv5 | XFSv5 szolgáltatások XFS fájlrendszeren, például a metaadatok ellenőrzőösszeg 9.10 a verziójáról a mobilitási szolgáltatás és újabb verziók esetében támogatottak. A xfs_info segédprogram használatával ellenőrizze a XFS superblock a partíció. Ha ftype értéke 1, XFSv5 szolgáltatások használatát is.



## <a name="network"></a>Network (Hálózat)

**Összetevő** | **Támogatott** 
--- | --- 
Gazdagép hálózati hálózati adapterek összevonása | VMware virtuális gépek esetén támogatott <br/><br/>Fizikai gép replikálása nem támogatott
Gazdagép hálózati VLAN | Igen 
Gazdagép hálózati IPv4 | Igen 
Gazdagép hálózati IPv6 | Nem 
Vendég-kiszolgáló hálózati hálózati adapterek összevonása | Nem 
Vendég-kiszolgáló hálózati IPv4 | Igen 
Guest/server network IPv6 | Nem 
Vendég/kiszolgáló hálózati statikus IP-címe (Windows) | Igen 
Vendég/kiszolgáló hálózati statikus IP-címe (Linux) | Igen <br/><br/>Virtuális gépek feladat-visszavétel DHCP használatára vannak konfigurálva.  
Vendég-kiszolgáló hálózati több hálózati adapter | Igen 


## <a name="azure-vm-network-after-failover"></a>Az Azure Virtuálisgép-hálózat (után feladatátvétel)

**Összetevő** | **Támogatott** 
--- | --- 
Express Route | Igen 
ILB | Igen 
ELB | Igen 
Traffic Manager | Igen 
Multi-NIC | Igen 
Fenntartott IP-cím | Igen 
IPv4 | Igen 
Tartsa meg a forrás IP-címe | Igen 
Virtuális hálózati szolgáltatási végpont<br/><br/> (Az azure storage tűzfalak és Vnetek) | Nem 


## <a name="storage"></a>Tárolás


**Összetevő** | **Támogatott** 
--- | --- 
Állomás NFS | VMware Igen<br/><br/> Fizikai kiszolgálók esetében nem 
Host SAN (ISCSI) | Igen
Állomás többutas (MPIO) | Igen – tesztelték: Microsoft DSM, EMC PowerPath 5.7 SP4 EMC PowerPath DSM CLARiiON
Vendég-kiszolgáló vmdk-fájl | Igen 
Vendég-kiszolgáló EFI/UEFI| Nem
Vendég-kiszolgáló megosztott fürtlemez | Nem 
Vendég/server titkosított lemez | Nem 
NFS Vendég-kiszolgáló | Nem 
Az SMB 3.0 Vendég-kiszolgáló | Nem
Guest/server RDM | Igen<br/><br/> A fizikai kiszolgálók N/A 
Vendég-kiszolgáló > 1 TB méretű lemez | Igen<br/><br/>Legfeljebb 4095 GB 
Vendég/server 4 KB-os logikai és a 4 KB-os fizikai szektort méretű lemez | Igen
Vendég/server 4 KB-os logikai lemez és 512 bájtos fizikai szektorméretet | Igen 
Csíkozott > 1 TB-os kötet Vendég-kiszolgáló<br/><br/> LVM logikai kötetkezelés Vendég/kiszolgáló - tárolóhelyek |} Vendég-kiszolgáló gyakran használt adatok hozzáadása lemez |} Vendég/kiszolgáló - a(z) |} Vendég-kiszolgáló több utas (MPIO) Igen |} N/A 

## <a name="azure-storage"></a>Azure Storage tárterület

**Összetevő** | **Támogatott** 
--- | --- 
LRS | Igen 
GRS | Igen 
RA-GRS | Igen 
Ritkán használt adatok | Nem 
Gyakran használt adatok| Nem 
Blokkblobok | Nem 
Encryption at rest(SSE)| Igen 
Prémium szintű Storage | Igen 
Import/export szolgáltatás | Nem 
Virtuális hálózati szolgáltatási végpont<br/><br/> Az Azure storage tűzfalak és a Vnetek konfigurálva a célként megadott tárolási/gyorsítótár storage-fiók (használt replikációs adatokat tároló) | Nem 
Általános célú V2 storage-fiókok (a gyakran és ritkán. szint) | Nem 


## <a name="azure-compute"></a>Az Azure compute

**Featuree** | **Támogatott** 
--- | --- 
Rendelkezésre állási csoportok | Igen 
HUB | Igen   
Felügyelt lemezek | Igen 

## <a name="azure-vm-requirements"></a>Azure Virtuálisgép-követelményeknek

A helyszíni virtuális gépek replikálása Azure-ba, meg kell felelnie Azure virtuális Gépen az alábbi táblázatban foglaltak.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
**Vendég operációs rendszer** | Győződjön meg arról [támogatott operációs rendszerek](#replicated machines) | Előfeltételek ellenőrzése sikertelen, ha nem támogatott.
**Vendég operációs rendszer architektúrája** | 64 bites | Előfeltételek ellenőrzése sikertelen, ha nem támogatott
**Operációs rendszert tároló lemez mérete** | Legfeljebb 2048 GB | Előfeltételek ellenőrzése sikertelen, ha nem támogatott
**Operációs rendszer lemez száma** | 1 | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott.
**Adatlemez** | 64 vagy kevesebb if replikál **VMware virtuális gépek Azure-bA**; 16 vagy kevesebb Ha replikál **Hyper-V virtuális gépek Azure-bA** | Előfeltételek ellenőrzése sikertelen lesz, ha nem támogatott
**Adattároló lemez virtuális merevlemez mérete** | Legfeljebb 4095 GB | Előfeltételek ellenőrzése sikertelen, ha nem támogatott
**Hálózati adapterek** | Több adapter támogatottak. | 
**Megosztott virtuális merevlemez** | Nem támogatott | Előfeltételek ellenőrzése sikertelen, ha nem támogatott
**FC-lemez** | Nem támogatott | Előfeltételek ellenőrzése sikertelen, ha nem támogatott
**Merevlemez formátuma** | VHD <br/><br/> VHDX | Bár VHDX jelenleg nem támogatott az Azure-ban, a Site Recovery automatikusan átalakítja VHDX virtuális merevlemezre történő feladatátadást követően Azure-bA. Ha nem sikerül vissza a helyszíni virtuális gépek továbbra is a VHDX formátum.
**Bitlocker** | Nem támogatott | A BitLocker a gépek replikációjának engedélyezése előtt le kell tiltani.
**Virtuális gép neve** | 1 – 63 karakter.<br/><br/> Kizárólag betűket, számokat és kötőjeleket tartalmazhat.<br/><br/> A számítógépnév kell kezdődnie, és betűvel vagy számmal végződhet. | Frissítse az értéket a virtuálisgép-tulajdonságokat a Site Recovery szolgáltatásban.
**Virtuálisgép-típussá** | 1. generációs<br/><br/> Windows – a 2. generációs | 2. generációs virtuális gépek egy basic (amely egy vagy két adatkötetek VHDX formátumú tartalmazza) lemez típusa és kisebb, mint 300 GB lemezterület támogatottak.<br></br>Linux generációs 2 virtuális gépek nem támogatottak. [További információ](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="vault-tasks"></a>Tároló feladatok

**Művelet** | **Támogatott** 
--- | --- 
Erőforráscsoportok közötti áthelyezése közben tároló<br/><br/> Belül és között előfizetések | Nem 
Tárolási, hálózati, Azure virtuális gépek között erőforráscsoportok áthelyezéséhez<br/><br/> Belül és között előfizetések | Nem 


## <a name="mobility-service"></a>Mobilitási szolgáltatás

**Name (Név)** | **Leírás** | **Legújabb verzió** | **Részletek**
--- | --- | --- | --- | ---
** Az egységes telepítő azure Site Recovery ** | Koordinálja a helyszíni VMware-kiszolgálók és az Azure közötti kommunikáció <br/><br/> Helyszíni VMware-kiszolgálókon telepítve | 9.12.4653.1 (elérhető a portál) | [Legújabb funkcióit és javításokat](https://aka.ms/latest_asr_updates)
**Mobilitási szolgáltatás** | Koordinálja a helyszíni VMware-kiszolgáló/fizikai kiszolgálók és az Azure és a másodlagos hely közötti replikálás<br/><br/> VMware virtuális gép vagy fizikai kiszolgálók replikálni kívánt telepítve  | 9.12.4653.1 (elérhető a portál) | [Legújabb funkcióit és javításokat](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>További lépések
[Megtudhatja, hogyan](tutorial-prepare-azure.md) vész-helyreállítási VMware virtuális gépek Azure előkészítéséhez.
