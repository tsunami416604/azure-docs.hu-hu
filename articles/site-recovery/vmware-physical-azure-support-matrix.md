---
title: Vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure-bA az Azure Site Recovery támogatási mátrix |} A Microsoft Docs
description: A támogatott operációs rendszerek és a vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure-ban az Azure Site Recovery összetevőinek foglalja össze.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: raynew
ms.openlocfilehash: 076cd987cdc74cad07287c15ad52394ef304f251
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51015366"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-támogatási mátrixa

Ez a cikk összefoglalja a támogatott összetevők és a VMware virtuális gépek vészhelyreállítása az Azure-bA beállításait [Azure Site Recovery](site-recovery-overview.md).

A legegyszerűbb környezet az Azure Site Recovery használatának megkezdéséhez látogasson el a [oktatóanyagok](tutorial-prepare-azure.md). További információ az Azure Site Recovery architektúrájáról [Itt](vmware-azure-architecture.md).

## <a name="replication-scenario"></a>Replikációs forgatókönyv

**Forgatókönyv** | **Részletek**
--- | ---
VMware virtuális gépek | Az Azure-bA a helyszíni VMware virtuális gépek replikálása. Ebben a forgatókönyvben az Azure Portalon vagy a használatával telepíthet [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Fizikai kiszolgálók | A helyszíni Windows/Linux fizikai kiszolgálók Azure-bA replikálását. Ebben a forgatókönyvben az Azure Portalon is telepítheti.

## <a name="on-premises-virtualization-servers"></a>A helyszíni virtualizálási kiszolgálók

**Server** | **Követelmények** | **Részletek**
--- | --- | ---
VMware | vCenter Server 6.7, 6.5-ös, 6.0 vagy 5.5-ös vagy vSphere 6.7, 6.5-ös, 6.0 vagy 5.5 | Azt javasoljuk, hogy a vCenter-kiszolgáló használja.<br/><br/> Azt javasoljuk, hogy a vSphere-gazdagépek és vCenter-kiszolgálók található-e a folyamatkiszolgáló ugyanazon a hálózaton. Alapértelmezés szerint a folyamat kiszolgáló-összetevők fut a konfigurációs kiszolgálón, így ez lesz a hálózatot, amelyben állítsa be a konfigurációs kiszolgálót, kivéve, ha dedikált kiszolgáló beállításához.
Fizikai | –

## <a name="site-recovery-configuration-server"></a>Site Recovery konfigurációs kiszolgálónak

A konfigurációs kiszolgáló az a Site Recovery-összetevők, beleértve a konfigurációs kiszolgáló, folyamatkiszolgáló és fő célkiszolgálót futtató helyszíni gépre. VMware-replikációhoz beállította a konfigurációs kiszolgáló az összes követelményeket, VMware virtuális gép létrehozása egy OVF-sablon használatával. Fizikai kiszolgáló replikációjához beállíthatja a konfigurációs kiszolgáló gép manuálisan.

**Összetevő** | **Követelmények**
--- |---
Processzormagok | 8
RAM | 16 GB
Lemezek száma | 3 lemezzel<br/><br/> Lemezek tartalmazza az operációs rendszer lemez, a folyamatkiszolgálói gyorsítótárlemez és az adatmegőrzési meghajtó a feladat-visszavételhez.
Lemez – szabad terület | Szükséges folyamatkiszolgálói gyorsítótár-terület 600 GB.
Lemez – szabad terület | 600 GB lemezterület az adatmegőrzési meghajtó szükséges.
Operációs rendszer  | A Windows Server 2012 R2 vagy Windows Server 2016-ban |
Operációs rendszer területi beállítása | Angol (en-us)
A PowerCLI | [A PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") telepítve kell lennie.
Windows Server-szerepkörök | Ne legyen engedélyezve: <br> - Active Directory tartományi szolgáltatások <br>– Internet Information Services <br> - Hyper-V |
Csoportházirendek| Ne legyen engedélyezve: <br> -Hozzáférés megakadályozása a parancssorba. <br> -A beállításjegyzék szerkesztőeszközeihez való hozzáférés letiltása. <br> -Megbízhatósági logika fájlmellékletekhez. <br> – Kapcsolja be a parancsfájl végrehajtása. <br> [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Győződjön meg arról, hogy:<br/><br/> -Nem rendelkezik egy korábban létező alapértelmezett webhelye <br> -Engedélyezése [a névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás  <br> -Nem rendelkezik a már létező webhely vagy alkalmazás 443-as porton<br>
Hálózati adapter típusa | VMXNET3 (Ha a VMware virtuális gépként telepített)
IP-cím típusa | Statikus
Portok | 443-as használt vezérlőcsatorna-vezénylés)<br>a 9443-as használt átviteli adatok

## <a name="replicated-machines"></a>Replikált gépek

A Site Recovery támogatja az egy támogatott gépen futó bármilyen számítási feladat replikálását.

**Összetevő** | **Részletek**
--- | ---
Gép beállításai | Az Azure-bA replikált gépek meg kell felelnie [Azure-követelmények](#azure-vm-requirements).
Windows operációs rendszer | 64 bites Windows Server 2016 (Server Core, kiszolgáló asztali kezelőfelülettel), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2, legalább SP1. </br></br>  [A Windows Server 2008, legalább SP2 – 32 bites és 64 bites](migrate-tutorial-windows-server-2008.md) (csak a migrálás). </br></br> Windows 2016 Nano Server nem támogatott.
Linux operációs rendszer | Red Hat Enterprise Linux: 5.2 5.11<b>\*\*</b>, 6.1, 6.10<b>\*\*</b>, 7.0-ban való 7.5 <br/><br/>CentOS: 5.2 5.11<b>\*\*</b>, 6.1, 6.10<b>\*\*</b>, 7.0-ban való 7.5 <br/><br/>Ubuntu 14.04 LTS server[ (támogatott kernel-verzióknál)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server[ (támogatott kernel-verzióknál)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (támogatott kernel-verzióknál)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1, SP2 SP3 [ (támogatott kernel-verzióknál)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b>, SUSE Linux Enterprise Server 11 SP4 * </br></br>Oracle Linux 6.4, 6.5, 6.6, a Red Hat-kompatibilis kernel vagy a szoros vállalati Kernel kiadási 3 (UEK3) 6.7 <br/><br/></br>– A replikált gépek SUSE Linux Enterprise Server 11 SP3 rendszerről történő SP4 szervizcsomag nem támogatott. Szeretné frissíteni, tiltsa le a replikációt, és engedélyezze újra a frissítés után.</br></br> - [További](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) Linux- és az Azure-ban nyílt forráskódú technológia támogatása. A Site Recovery koordinálja a feladatátvétel futtatása a Linux-kiszolgálók az Azure-ban. Linux-szállítók azonban csak a teljes életciklusa még nem értek el terjesztési verziók támogatási előfordulhat, hogy korlátozza.<br/><br/> – A Linux-disztribúció csak a tőzsdei kernelekkel, amelyek részei a terjesztési alverzió kiadási/frissítés támogatottak.<br/><br/> -Frissítése a védett számítógépek között jelentős Linux terjesztési verziója nem támogatott. Szeretné frissíteni, tiltsa le a replikációt, és frissítse az operációs rendszert, majd újból engedélyezze a replikációt.<br/><br/> – Red Hat Enterprise Linux 5.2-5.11 vagy CentOS 5.2-5.11 operációs rendszert futtató kiszolgálókat kell rendelkeznie a [Linux Integration Services (LIS) összetevők](https://www.microsoft.com/download/details.aspx?id=55106) telepítve van a gépek Azure-ban.



### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel-verzióknál


**Támogatott kiadás** | **Az Azure Site Recovery mobilitási szolgáltatás ezen verziója** | **Kernel verziója** |
--- | --- | --- |
14.04 LTS | 9.19 | a 3.13.0-153-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-131-generic 4.4.0-21-Generic |
14.04 LTS | 9.18 | a 3.13.0-153-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-130-generic 4.4.0-21-Generic |
14.04 LTS | 9.17 | a 3.13.0-149-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-127-generic 4.4.0-21-Generic |
14.04 LTS | 9.16 | a 3.13.0-144-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-119-generic 4.4.0-21-Generic |
|||
16.04 LTS | 9.19 | a 4.4.0-131-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-45-generic, 4.13.0-16-Generic<br/>a 4.15.0-30-generic 4.15.0-13-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>a 4.13.0-1018-azure 4.13.0-1005-Azure <br/>a 4.15.0-1019-azure 4.15.0-1012-Azure|
16.04 LTS | 9.18 | a 4.4.0-130-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-45-generic 4.13.0-16-Generic |
16.04 LTS | 9.17 | a 4.4.0-127-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-43-generic 4.13.0-16-Generic |
16.04 LTS | 9.16 | a 4.4.0-119-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-38-generic 4.13.0-16-Generic |


### <a name="debian-kernel-versions"></a>Debian kernel-verzióknál


**Támogatott kiadás** | **Az Azure Site Recovery mobilitási szolgáltatás ezen verziója** | **Kernel verziója** |
--- | --- | --- |
Debian 7 | 9.17,9.18,9.19 | a 3.2.0-6-amd64, 3.2.0-4-AMD64 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | a 3.2.0-5-amd64, 3.2.0-4-AMD64 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | a 3.16.0-6-amd64, hogy 4.9.0-0.bpo.7-amd64 4.9.0-0.bpo.4-amd64 3.16.0-4-AMD64 |
Debian 8 | 9.17, 9.18 | a 3.16.0-6-amd64, hogy 4.9.0-0.bpo.6-amd64 4.9.0-0.bpo.4-amd64 3.16.0-4-AMD64 |
Debian 8 | 9.16 | a 3.16.0-5-amd64, hogy 4.9.0-0.bpo.6-amd64 4.9.0-0.bpo.4-amd64 3.16.0-4-AMD64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 támogatott kernel verziója

**Kiadás** | **Mobilitási szolgáltatás verziójának** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.19 | SP1 3.12.49-11-default 3.12.74-60.64.40-default</br></br> A 3.12.74-60.64.96-default SP1(LTSS) 3.12.74-60.64.45-default</br></br> SP2 4.4.21-69-default 4.4.120-92.70-default</br></br>A 4.4.121-92.85-default SP2(LTSS) 4.4.121-92.73-default</br></br>A 4.4.140-94.42-default SP3 4.4.73-5-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.18 | SP1 3.12.49-11-default 3.12.74-60.64.40-default</br></br> A 3.12.74-60.64.96-default SP1(LTSS) 3.12.74-60.64.45-default</br></br> SP2 4.4.21-69-default 4.4.120-92.70-default</br></br>A 4.4.121-92.85-default SP2(LTSS) 4.4.121-92.73-default</br></br>A 4.4.138-94.39-default SP3 4.4.73-5-default |

## <a name="linux-file-systemsguest-storage"></a>Linux rendszerek/Vendég fájltárolás

**Összetevő** | **Támogatott**
--- | ---
Fájlrendszer | ext3, ext4, XFS.
Kötetek kezelése | LVM2. LVM csak az adatlemezek használata támogatott. Az Azure virtuális gépek egyetlen operációsrendszer-lemez rendelkeznek.
A Paravirtualizált tárolóeszközök | A paravirtualizált illesztőprogramok által exportált eszközök nem támogatottak.
Több üzenetsor blokk i/o-eszközök | Nem támogatott.
A HP CCISS tárolóvezérlő a fizikai kiszolgálók | Nem támogatott.
Könyvtárak | Ezek a könyvtárak (ha csoportként külön partíciókat és-fájlrendszerek) összes kell lennie a forráskiszolgálón az azonos operációsrendszer-lemez a: / (gyökér), gyökérpartíció, usr, /usr/local, /var, a/etc.</br></br> gyökérpartíció lemezpartíción kell és nem kell az LVM-kötet.<br/><br/>
Szabad lemezterület-követelmények| A/root partíción 2 GB <br/><br/> A telepítési mappa 250 MB
XFSv5 | XFSv5 funkcióinak XFS fájlrendszereket, metaadatok ellenőrzőösszeg, például a mobilitási szolgáltatás verziójának 9.10 meghajtóbetűjeltől támogatottak. A xfs_info segédprogram használatával a partíció a XFS superblock ellenőrzése. Ha ftype 1 értékre van állítva, majd XFSv5 funkciók használatban vannak.

## <a name="vmdisk-management"></a>A felügyeleti Virtuálisgép-lemez

**Művelet** | **Részletek**
--- | ---
A replikált virtuális gép lemez átméretezése | Támogatott.
Lemez hozzáadása a replikált virtuális Gépen | Tiltsa le a replikációt a virtuális gép, és a lemezt adja hozzá a majd újraengedélyezni replikációs. Lemez hozzáadása egy replikáló gépen jelenleg nem támogatott.

## <a name="network"></a>Hálózat

**Összetevő** | **Támogatott**
--- | ---
Gazdagép-hálózat hálózati adapterek összevonása | VMware virtuális gépek esetében támogatott. <br/><br/>Fizikai gép replikálása nem támogatott.
Gazdagép hálózati VLAN | Igen.
Gazdagép hálózati IPv4 | Igen.
Gazdagép hálózati IPv6 | Nem.
Vendég-kiszolgáló hálózati hálózati adapterek összevonása | Nem.
Vendég-kiszolgáló hálózati IPv4 | Igen.
Vendég-kiszolgáló hálózati IPv6 | Nem.
Vendég-kiszolgáló hálózati statikus IP-cím (Windows) | Igen.
Vendég-kiszolgáló hálózati statikus IP-cím (Linux) | Igen. <br/><br/>Virtuális gépek feladat-visszavételi DHCP használatára vannak konfigurálva.
Vendég-kiszolgáló hálózati adapterrel | Igen.


## <a name="azure-vm-network-after-failover"></a>Az Azure Virtuálisgép-hálózat (feladatátvétel) után

**Összetevő** | **Támogatott**
--- | ---
Azure ExpressRoute | Igen
ILB | Igen
ELB | Igen
Azure Traffic Manager | Igen
Multi-NIC | Igen
Fenntartott IP-cím | Igen
IPv4 | Igen
Forrás IP-cím megőrzése | Igen
Az Azure virtuális hálózati Szolgáltatásvégpontok<br/> (nem Azure Storage-tűzfalak) | Igen
Gyorsított hálózatkezelés | Nem

## <a name="storage"></a>Tárhely
**Összetevő** | **Támogatott**
--- | ---
Gazdagép NFS | Igen: VMware-ről<br/><br/> Nincs a fizikai kiszolgálók számára
Gazda (iSCSI/FC) TÁROLÓHÁLÓZAT | Igen
Gazdagép vsan-hoz | Igen: VMware-ről<br/><br/> N/A fizikai kiszolgálókhoz
Gazdagép többutas (MPIO) | Igen, teszteltük Microsoft DSM EMC PowerPath 5.7 SP4 EMC PowerPath DSM CLARiiON számára
Gazdagép virtuális kötetek (VVols) | Igen: VMware-ről<br/><br/> N/A fizikai kiszolgálókhoz
Vendég-kiszolgáló VMDK | Igen
Vendég/server EFI/UEFI| Részlegesen (Azure for Windows Server 2012 és újabb VMware virtuális gépek csak az áttelepítés) </br></br> Tekintse meg az eljárás végén található a táblában
Vendég-kiszolgáló megosztott fürtlemezen | Nem
Vendég-kiszolgáló titkosított lemez | Nem
Vendég-kiszolgáló NFS | Nem
Vendég-kiszolgáló SMB 3.0-s | Nem
Vendég-kiszolgáló RDM | Igen<br/><br/> N/A fizikai kiszolgálókhoz
Vendég-kiszolgáló > 1 TB-os lemez | Igen<br/><br/>Legfeljebb 4095 GB-ig
Vendég-kiszolgáló, 4K fizikai logikai és a 4 k szektormérete lemez | Igen
Vendég/server 4K logikai lemez és 512 bájtos fizikai szektorméretet | Igen
Csíkozott lemez a Vendég/kiszolgáló kötet > 4 TB-ig <br><br/>Logikaikötet-kezelő (LVM)| Igen
Vendég-kiszolgáló – tárolóhelyek | Nem
Vendég/server gyakran használt adatok hozzáadása/eltávolítása lemez | Nem
Vendég-kiszolgáló – zárható ki lemez | Igen
Vendég-kiszolgáló többutas (MPIO) | Nem

> [!NOTE]
> UEFI rendszerindítási VMware virtuális gépek Windows Server 2012 rendszert futtató, vagy később telepíthetők át az Azure-bA. A következő korlátozások vonatkoznak:

> - Csak az Azure-bA áttelepítése támogatott. A helyszíni VMware-helyre történő feladat-visszavétel nem támogatott.
> - A kiszolgáló az operációsrendszer-lemez nem tartalmazhat több mint négy partícióval.
> - A mobilitási szolgáltatás 9.13 vagy újabb verziójára van szükség.
> - Fizikai kiszolgálók esetében nem támogatott.

## <a name="azure-storage"></a>Azure Storage

**Összetevő** | **Támogatott**
--- | ---
Helyileg redundáns tárolás | Igen
Georedundáns tárolás | Igen
Írásvédett georedundáns tárolás | Igen
Ritka elérésű tárterület | Nem
Gyakori elérésű tárolási| Nem
Blokkblobok | Nem
Titkosítás inaktív állapotban (a Storage Service Encryption)| Igen
Prémium szintű Storage | Igen
Importálási/exportálási szolgáltatás | Nem
A cél tárolási illetve gyorsítótárfiók (replikációs adatainak tárolására használt) konfigurált virtuális hálózatok az Azure Storage-tűzfalak | Nem
Általános célú v2-tárfiókok (egyaránt gyakori és ritka elérésű szinten) | Nem

## <a name="azure-compute"></a>Az Azure compute

**Funkció** | **Támogatott**
--- | ---
Rendelkezésre állási csoportok | Igen
HUB | Igen
Managed Disks | Igen

## <a name="azure-vm-requirements"></a>Azure Virtuálisgép-követelmények

Az Azure-bA replikált helyszíni virtuális gépek meg kell felelnie az Azure virtuális gépekre vonatkozó, az alábbi táblázatban foglaltak. Egy előfeltétel-ellenőrzés futtatásakor a Site Recovery nem fog működni, ha egyes követelmények nem teljesülnek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | Győződjön meg arról [támogatott operációs rendszerek](#replicated-machines) a replikált gépek. | Az ellenőrzés sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemez mérete | Legfeljebb 2048 GB-ig. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.  
Adatlemezek száma | 64 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.  
Adatlemez mérete | Legfeljebb 4095 GB-ig | Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter támogatott. |
Megosztott VHD | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A BitLocker az adott gép replikálását engedélyezése előtt le kell tiltani. |
Virtuális gép neve | 1 és 63 karakternél.<br/><br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevét kell kezdődnie, és betűvel vagy számmal végződhet. |  Frissítse az értéket a Site Recovery virtuálisgép-tulajdonságokat.


## <a name="vault-tasks"></a>Tároló-feladatok

**Művelet** | **Támogatott**
--- | ---
Tároló áthelyezése erőforráscsoportok közt<br/><br/> Belül és azok az előfizetések között | Nem
Tárolás, hálózat, Azure-beli virtuális gépek erőforráscsoportok közötti áthelyezése<br/><br/> Belül és azok az előfizetések között | Nem


## <a name="download-latest-azure-site-recovery-components"></a>Töltse le a legújabb Azure Site Recovery-összetevők

**Name (Név)** | **Leírás** | **Legújabb verzió letöltése utasítások**
--- | --- | --- | --- | ---
Konfigurációs kiszolgáló | Koordinálja a helyszíni VMware-kiszolgálók és Azure közötti kommunikációt <br/><br/> A helyszíni VMware-kiszolgálók telepítése | Kattintson a friss telepítés helyett, [Itt](vmware-azure-deploy-configuration-server.md). Meglévő összetevő legújabb verziójára való frissítését, kattintson a [Itt](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server).
Folyamatkiszolgáló|Alapértelmezés szerint telepítve van a konfigurációs kiszolgálón. Ez fogadja a replikált adatokat; gyorsítótárazás, tömörítés és titkosítással optimalizálja őket és elküldi azt az Azure Storage. Az üzembe helyezés növekedésével további, külön folyamatkiszolgálók nagyobb mértékű replikációs forgalom kezelésére is hozzáadhat.| Kattintson a friss telepítés helyett, [Itt](vmware-azure-set-up-process-server-scale.md). Meglévő összetevő legújabb verziójára való frissítését, kattintson a [Itt](vmware-azure-manage-process-server.md#upgrade-a-process-server).
A mobilitási szolgáltatás | Koordinálja a helyszíni VMware-kiszolgálók/fizikai kiszolgálók és az Azure és a másodlagos hely közötti replikáció<br/><br/> A VMware virtuális gépek vagy fizikai kiszolgálókat szeretne replikálni telepítve | Kattintson a friss telepítés helyett, [Itt](vmware-azure-install-mobility-service.md). Meglévő összetevő legújabb verziójára való frissítését, kattintson a [Itt](vmware-physical-mobility-service-overview.md#update-the-mobility-service).

A legújabb funkciókkal és javításokkal kapcsolatos további információkért kattintson [Itt](https://aka.ms/latest_asr_updates).


## <a name="next-steps"></a>További lépések
[Ismerje meg, hogyan](tutorial-prepare-azure.md) VMware virtuális gépek vészhelyreállítása az Azure előkészítése.
