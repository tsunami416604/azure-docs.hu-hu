---
title: A VMware virtuális gépek és fizikai kiszolgálók replikálása Azure-bA az Azure Site Recovery támogatási mátrix |} Microsoft Docs
description: A támogatott operációs rendszerek és a VMware virtuális gépek és fizikai kiszolgálók replikálása az Azure Site Recovery segítségével Azure összetevőket foglalja össze.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2018
ms.author: raynew
ms.openlocfilehash: c80b47dcf2477fda5406c2e6efe3ee87ad1188d6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>VMware és fizikai kiszolgálók replikálást az Azure-támogatási mátrix

Ez a cikk összefoglalja támogatott összetevők és az Azure-bA VMware virtuális gépek vész-helyreállítási beállítások használatával [Azure Site Recovery](site-recovery-overview.md).

## <a name="replication-scenario"></a>Replikációs forgatókönyv

**Forgatókönyv** | **Részletek**
--- | ---
VMware virtuális gépek | Helyszíni VMware virtuális gépek Azure-bA replikálását. Ebben a forgatókönyvben, az Azure portálon vagy a PowerShell használatával telepítheti.
Fizikai kiszolgálók | A helyszíni windowsos/Linuxos fizikai serversto Azure replikációját. Ebben a forgatókönyvben az Azure portálon telepítése.

## <a name="on-premises-virtualization-servers"></a>A helyszíni virtualizálási kiszolgálók

**Kiszolgáló** | **Követelmények** | **Részletek**
--- | --- | ---
VMware | vCenter Server 6.5 6.0, vagy 5.5 vagy vSphere 6.5, 6.0 vagy 5.5 | Azt javasoljuk, hogy használja-e a vCenter-kiszolgálót.<br/><br/> Azt javasoljuk, hogy a vSphere gazdagépek és vCenter-kiszolgáló található-e a folyamatkiszolgáló és a ugyanahhoz a hálózathoz. Alapértelmezés szerint a folyamat kiszolgáló-összetevők fut a konfigurációs kiszolgálón, így ez lesz a hálózatot, amelyben állítsa be a konfigurációs kiszolgáló, kivéve, ha egy dedikált folyamat kiszolgáló. 
Fizikai | –

## <a name="site-recovery-configuration-server"></a>Helykiszolgáló helyreállítási konfiguráció

A konfigurációs kiszolgálón a helyi számítógépen, a Site Recovery összetevők, beleértve a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgálón futó. VMware-replikáció beállíthatja a kiszolgáló az összes követelményeinek, a VMware virtuális gépek létrehozása OVF sablonnal. Fizikai kiszolgáló replikációs beállíthatja a konfigurációs kiszolgáló gépet manuálisan.

**Összetevő** | **Követelmények**
--- |---
Processzormagok | 8 
RAM | 12 GB
Lemezek száma | 3 lemezek<br/><br/> Lemezek tartalmazza az operációs rendszer lemez, a folyamat kiszolgáló gyorsítótár lemez és a feladat-visszavételi adatmegőrzési meghajtó.
Szabad lemezterület | 600 GB lemezterület, a folyamat gyorsítótár-kiszolgáló szükséges.
Szabad lemezterület | 600 GB lemezterület az adatmegőrzési meghajtó szükséges.
Operációs rendszer  | Windows Server 2012 R2 vagy Windows Server 2016 | 
Operációs rendszer területi beállítása | Angol (en-us) 
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") kell telepíteni.
Windows Server-szerepkörök | Nem engedélyezi: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V |
Csoportházirendek| Nem engedélyezi: <br> -Tagadni a hozzáférést a parancssorba. <br> -A hozzáférés megakadályozása a beállításjegyzék szerkesztésével eszközök. <br> – Megbízható vonatkozó logikát. <br> -Parancsfájl végrehajtása bekapcsolása. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Ellenőrizze, hogy:<br/><br/> – Nem kell egy korábban létező alapértelmezett webhely <br> -Engedélyezése [névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás  <br> -Nincs telepítve elérésű, korábban létező webhely vagy alkalmazás figyeli a 443-as port<br>
A hálózati adapter típusa | VMXNET3 (ha VMware virtuális gépként telepített) 
IP-cím típusa | Statikus 
Portok | a vezérlő csatorna vezénylés használt 443)<br>adatok átvitel használt 9443

## <a name="replicated-machines"></a>Replikált gép

Bármelyik támogatott gépen futó feladat replikálását a Site Recovery támogatja.

**Összetevő** | **Részletek**
--- | ---
Számítógép-beállítások | Meg kell felelnie a gépek replikálása Azure-bA [Azure-követelményeknek](#azure-vm-requirements).
A Windows operációs rendszer | 64 bites Windows Server 2016 (Server Core, az asztali élmény kiszolgáló), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2: legalább SP1. Windows 2016 Nano Server nem támogatott.
Linux operációs rendszer | Red Hat Enterprise Linux: 5.2 való 5.11, 6.1 való 6.9, 7.0-7.4 <br/><br/>CentOS: 5.2 való 5.11, 6.1 való 6.9, 7.0-7.4 <br/><br/>Ubuntu 14.04 LTS server[ (támogatott kernel verziók)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server[ (támogatott kernel verziók)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (támogatott kernel verziók)](#debian-kernel-versions)<br/><br/>Oracle Enterprise Linux 6.4, a Red Hat kompatibilis kernel vagy szoros vállalati Kernel kiadási-3 (UEK3) 6.5 <br/><br/>SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4 <br/><br/>Replikált gép SP3 verzióra történő frissítése SP4 nem támogatott. A frissítéshez tiltsa le a replikációt, majd engedélyezze újra a frissítés után.

>[!NOTE]
>
> - A Linux terjesztéseket csak a készlet kernelek a terjesztési Alverzió/frissítési kiadás részét képező támogatottak.
>
> - Frissíti a védett gépek közötti fő Linux terjesztési verziója nem támogatott. Frissítéséhez tiltsa le a replikációt, és frissítse az operációs rendszert, majd engedélyezze újra a replikációt.
>

### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel-verziók


**Támogatott kiadás** | **Az Azure Site Recovery mobilitási szolgáltatás-verzió** | **Kernel-verzió** |
--- | --- | --- |
14.04 LTS | 9.12 | a 3.13.0-132-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-96-generic 4.4.0-21-Generic |
14.04 LTS | 9.13 | a 3.13.0-137-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-104-generic 4.4.0-21-Generic |
14.04 LTS | 9.14 | a 3.13.0-142-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-116-generic 4.4.0-21-Generic |
14.04 LTS | 9.15 | a 3.13.0-144-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-119-generic 4.4.0-21-Generic |
16.04 LTS | 9.12 | a 4.4.0-96-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-35-generic 4.10.0-14-Generic |
16.04 LTS | 9.13 | a 4.4.0-104-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic 4.10.0-14-Generic |
16.04 LTS | 9.14 | a 4.4.0-116-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-36-generic, 4.13.0-16-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>a 4.13.0-1011-azure 4.13.0-1005-Azure |
16.04 LTS | 9.15 | a 4.4.0-119-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-38-generic, 4.13.0-16-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>a 4.13.0-1012-azure 4.13.0-1005-Azure |



### <a name="debian-kernel-versions"></a>Debian kernel-verziók


**Támogatott kiadás** | **Az Azure Site Recovery mobilitási szolgáltatás-verzió** | **Kernel-verzió** |
--- | --- | --- |
Debian 7 | 9.14, 9.15 | a 3.2.0-5-amd64, 3.2.0-4-AMD64 3.16.0-0.bpo.4-amd64 |
Debian 8 | 9.14, 9.15 | a 3.16.0-5-amd64, hogy 4.9.0-0.bpo.5-amd64 4.9.0-0.bpo.4-amd64 3.16.0-4-AMD64 |


## <a name="linux-file-systemsguest-storage"></a>Linux rendszerek/Vendég fájltároló

**Összetevő** | **Támogatott**
--- | ---
fájlrendszer | ext3, ext4, XFS.
Kötetkezelő | LVM2.
A többutas szoftver | Eszköz leképező.
Paravirtualized tárolóeszközök | A paravirtualizált illesztőprogramok által exportált eszközök nem támogatottak.
Több sor blokk IO eszközök | Nem támogatott.
A HP CCISS tárolóvezérlő fizikai kiszolgálók | Nem támogatott.
Könyvtárak | Ezeket a könyvtárakat (Ha külön partíciók /-fájlrendszerek beállított) kell lennie az azonos operációsrendszer-lemezképet a forráskiszolgálón lévő: / (gyökér), / Boot, / usr, /usr/local, /var, ETC.</br></br> / Boot lemezpartíción legyen, és nem lehet egy LVM kötet.<br/><br/>
Szabad lemezterület| A/root partíción 2 GB <br/><br/> A telepítési mappa 250 MB
XFSv5 | A 9.10 Mobilitásiszolgáltatás-verziót meghajtóbetűjeltől XFS fájlrendszeren, például a metaadatok ellenőrzőösszeg XFSv5 funkciókat támogatja. A xfs_info segédprogram használatával ellenőrizze a XFS superblock a partíció. Ha ftype értéke 1, XFSv5 szolgáltatások használatát is.



## <a name="network"></a>Network (Hálózat)

**Összetevő** | **Támogatott**
--- | ---
Gazdagép hálózati hálózati adapterek összevonása | VMware virtuális gépek esetén támogatott. <br/><br/>Fizikai gép replikálása nem támogatott.
Gazdagép hálózati VLAN | Igen.
Gazdagép hálózati IPv4 | Igen.
Gazdagép hálózati IPv6 | Nem.
Vendég-kiszolgáló hálózati hálózati adapterek összevonása | Nem.
Vendég-kiszolgáló hálózati IPv4 | Igen.
Vendég-kiszolgáló hálózati IPv6 | Nem.
Vendég/kiszolgáló hálózati statikus IP-címe (Windows) | Igen.
Vendég/kiszolgáló hálózati statikus IP-címe (Linux) | Igen. <br/><br/>Virtuális gépek feladat-visszavétel DHCP használatára vannak konfigurálva.
Vendég-kiszolgáló hálózati több hálózati adapter | Igen.


## <a name="azure-vm-network-after-failover"></a>Az Azure Virtuálisgép-hálózat (után feladatátvétel)

**Összetevő** | **Támogatott**
--- | ---
Azure ExpressRoute | Igen
ILB | Igen
ELB | Igen
Azure Traffic Manager | Igen
Multi-NIC | Igen
Fenntartott IP-cím | Igen
IPv4 | Igen
Tartsa meg a forrás IP-címe | Igen
Azure virtuális hálózat szolgáltatás-végpontok<br/><br/> (Az azure Storage tűzfalak és virtuális hálózatok) | Nem

## <a name="storage"></a>Tárolás
**Összetevő** | **Támogatott**
--- | ---
Állomás NFS | VMware Igen<br/><br/> Fizikai kiszolgálók esetében nem
Állomás SAN (ISCSI) | Igen
Állomás többutas (MPIO) | A Microsoft DSM, EMC PowerPath 5.7 SP4 EMC PowerPath DSM CLARiiON Igen, tesztelve
Vendég-kiszolgáló vmdk-fájl | Igen
Vendég-kiszolgáló EFI/UEFI| Részleges (Azure a Windows Server 2012 és újabb verziók VMware virtuális gépek csak áttelepítés) </br></br> Lásd a táblázat végén a megjegyzést
Vendég-kiszolgáló megosztott fürtlemez | Nem
Vendég/server titkosított lemez | Nem
NFS Vendég-kiszolgáló | Nem
Az SMB 3.0 Vendég-kiszolgáló | Nem
Vendég-kiszolgáló RDM | Igen<br/><br/> A fizikai kiszolgálók N/A
Vendég-kiszolgáló > 1 TB méretű lemez | Igen<br/><br/>Legfeljebb 4095 GB
Vendég/server 4 KB-os logikai és a 4 KB-os fizikai szektort méretű lemez | Igen
Vendég/server 4 KB-os logikai lemez és 512 bájtos fizikai szektorméretet | Igen
Csíkozott Vendég/server kötetet > 4 TB-os <br><br/>Logikai kötetkezelés (LVM)| Igen
Vendég/kiszolgáló - tárolóhelyek | Nem
Vendég/server gyakran használt adatok hozzáadása lemez | Nem
Vendég/kiszolgáló - kizárási lemez | Igen
Vendég-kiszolgáló többutas (MPIO) | –

> [!NOTE]
> UEFI rendszerindítási VMware virtuális gépek Windows Server 2012 rendszert futtató, vagy később is telepíthető az Azure-bA. A következő korlátozások vonatkoznak:

> - Csak az Azure-bA áttelepítése támogatott. A feladat-visszavétel a helyszíni VMware-hely nem támogatott.
> - A kiszolgáló nem rendelkezik négynél több partíciót az operációsrendszer-lemezképet.
> - Mobilitási szolgáltatás verziója 9.13 vagy újabb verzió szükséges.
> - Fizikai kiszolgálók esetében nem támogatott.

## <a name="azure-storage"></a>Azure Storage tárterület

**Összetevő** | **Támogatott**
--- | ---
Helyileg redundáns tárolás | Igen
Georedundáns tárolás | Igen
Írásvédett georedundáns tárolás | Igen
Ritkán használt adatok | Nem
Gyakran használt adatok| Nem
Blokkblobok | Nem
Titkosítását (Storage szolgáltatás titkosítási)| Igen
Prémium szintű Storage | Igen
Import/export szolgáltatás | Nem
Virtuális hálózati szolgáltatási végpont<br/><br/> Tárolási tűzfalak és a célként megadott tárolási/gyorsítótár storage-fiók (használt replikációs adatokat tároló) konfigurált virtuális hálózatok | Nem
Általános célú v2 storage-fiókok (a gyakran és ritkán használt rétegek) | Nem

## <a name="azure-compute"></a>Az Azure compute

**Funkció** | **Támogatott**
--- | ---
Rendelkezésre állási csoportok | Igen
HUB | Igen
Felügyelt lemezek | Igen

## <a name="azure-vm-requirements"></a>Azure Virtuálisgép-követelményeknek

A helyszíni virtuális gépek replikálása Azure-ba, meg kell felelnie Azure virtuális Gépen az alábbi táblázatban foglaltak. A előfeltétel-ellenőrzés futtatásakor a Site Recovery azt meghiúsul, ha a követelmények nem teljesültek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | Győződjön meg arról [támogatott operációs rendszerek](#replicated machines). | Ellenőrzés sikertelen lesz, ha nem támogatott. 
Vendég operációs rendszer architektúrája | 64 bites. | Ellenőrzés sikertelen lesz, ha nem támogatott. 
Operációs rendszert tároló lemez mérete | Legfeljebb 2048 GB. | Ellenőrzés sikertelen lesz, ha nem támogatott. 
Operációs rendszer lemez száma | 1 | Ellenőrzés sikertelen lesz, ha nem támogatott.  
Adatlemez | 64 vagy kisebb. | Ellenőrzés sikertelen lesz, ha nem támogatott.  
Adattároló lemezeinek mérete | Legfeljebb 4095 GB | Ellenőrzés sikertelen lesz, ha nem támogatott. 
Hálózati adapterek | Több adapter támogatottak. | 
Megosztott virtuális merevlemez | Nem támogatott. | Ellenőrzés sikertelen lesz, ha nem támogatott. 
FC-lemez | Nem támogatott. | Ellenőrzés sikertelen lesz, ha nem támogatott. 
BitLocker | Nem támogatott. | A BitLocker a gépek replikációjának engedélyezése előtt le kell tiltani. | 
a virtuális gép neve | 1 és 63 karakter.<br/><br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A számítógépnév kell kezdődnie, és betűvel vagy számmal végződhet. |  Frissítse az értéket a virtuálisgép-tulajdonságokat a Site Recovery szolgáltatásban.


## <a name="vault-tasks"></a>Tároló feladatok

**Művelet** | **Támogatott**
--- | ---
Erőforráscsoportok közötti áthelyezése közben tároló<br/><br/> Belül és között előfizetések | Nem
Tárolási, hálózati, Azure virtuális gépek között erőforráscsoportok áthelyezéséhez<br/><br/> Belül és között előfizetések | Nem


## <a name="mobility-service"></a>Mobilitási szolgáltatás

**Name (Név)** | **Leírás** | **legújabb verzió** | **Részletek**
--- | --- | --- | --- | ---
Azure Site Recovery egyesített telepítő | Koordinálja a helyszíni VMware-kiszolgálók és az Azure közötti kommunikáció <br/><br/> Helyszíni VMware-kiszolgálókon telepítve | 9.12.4653.1 (elérhető a portál) | [Legújabb funkcióit és javításokat](https://aka.ms/latest_asr_updates)
Mobilitási szolgáltatás | Koordinálja a helyszíni VMware-kiszolgáló/fizikai kiszolgálók és az Azure és a másodlagos hely közötti replikálás<br/><br/> VMware virtuális gép vagy fizikai kiszolgálók replikálni kívánt telepítve | 9.12.4653.1 (elérhető a portál) | [Legújabb funkcióit és javításokat](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>További lépések
[Megtudhatja, hogyan](tutorial-prepare-azure.md) vész-helyreállítási VMware virtuális gépek Azure előkészítéséhez.
