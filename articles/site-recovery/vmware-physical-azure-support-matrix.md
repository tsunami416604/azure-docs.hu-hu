---
title: Vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók az Azure-bA az Azure Site Recovery támogatási mátrix |} A Microsoft Docs
description: VMware virtuális gépek és fizikai kiszolgálóról az Azure Site Recovery használatával vész-helyreállítási támogatás foglalja össze.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: raynew
ms.openlocfilehash: 3ff6a1a52048e805f9236349d4fc8d45a14b78ea
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341455"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-támogatási mátrixa

Ez a cikk összefoglalja a támogatott összetevők és a beállítások vész-helyreállítási VMware virtuális gépek és fizikai kiszolgálók Azure-bA [Azure Site Recovery](site-recovery-overview.md).

- [További](vmware-azure-architecture.md) arról a VMware virtuális gépek/fizikai kiszolgáló vész helyreállítási architektúra.
- Kövesse a [oktatóanyagok](tutorial-prepare-azure.md) vész-helyreállítási kipróbálására.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

**Forgatókönyv** | **Részletek**
--- | ---
VMware virtuális gépek vészhelyreállítása | Az Azure-bA a helyszíni VMware virtuális gépek replikálása. Ebben a forgatókönyvben az Azure Portalon vagy a használatával telepíthet [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Fizikai kiszolgálók vészhelyreállítása | A helyszíni Windows/Linux fizikai kiszolgálók Azure-bA replikálását. Ebben a forgatókönyvben az Azure Portalon is telepítheti.

## <a name="on-premises-virtualization-servers"></a>A helyszíni virtualizálási kiszolgálók

**Server** | **Követelmények** | **Részletek**
--- | --- | ---
vCenter Server | 6\.7, 6.5-ös, 6.0 vagy 5.5-ös verzió | Azt javasoljuk, hogy a vCenter-kiszolgálót a vészhelyreállítás üzembe helyezésének használni.
vSphere-gazdagépek | 6\.7, 6.5-ös, 6.0 vagy 5.5-ös verzió | Azt javasoljuk, hogy a vSphere-gazdagépek és vCenter-kiszolgálók található-e a folyamatkiszolgáló ugyanazon a hálózaton. Alapértelmezés szerint a folyamatkiszolgáló a konfigurációs kiszolgálón fut. [További információk](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Site Recovery konfigurációs kiszolgálónak

A konfigurációs kiszolgáló az a Site Recovery-összetevők, beleértve a konfigurációs kiszolgáló, folyamatkiszolgáló és fő célkiszolgálót futtató helyszíni gépre.

- A VMware virtuális gépekhez a konfigurációs kiszolgáló OVF-sablonnal, VMware virtuális gép létrehozása a letöltés beállítása.
- A fizikai kiszolgálók számára, állítsa be a konfigurációs kiszolgáló gép manuálisan.

**Összetevő** | **Követelmények**
--- |---
Processzormagok | 8
RAM | 16 GB
Lemezek száma | 3 lemezzel<br/><br/> Lemezek tartalmazza az operációs rendszer lemez, a folyamatkiszolgálói gyorsítótárlemez és az adatmegőrzési meghajtó a feladat-visszavételhez.
Lemez – szabad terület | A folyamatkiszolgálói gyorsítótár-terület 600 GB.
Lemez – szabad terület | Az adatmegőrzési meghajtó-terület 600 GB.
Operációs rendszer  | A Windows Server 2012 R2 vagy Windows Server 2016 asztali kezelőfelülettel |
Operációs rendszer területi beállítása | Angol (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Konfigurációs kiszolgáló verziója esetén nem szükséges [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) vagy újabb. 
Windows Server-szerepkörök | Nem engedélyezi az Active Directory Domain Services; Az Internet Information Services (IIS) vagy a Hyper-V. 
Csoportházirendek| -Hozzáférés megakadályozása a parancssorba. <br/> -A beállításjegyzék szerkesztőeszközeihez való hozzáférés letiltása. <br/> -Megbízhatósági logika fájlmellékletekhez. <br/> – Kapcsolja be a parancsfájl végrehajtása. <br/> - [tudj meg többet](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Győződjön meg arról, hogy:<br/><br/> -Nem rendelkezik egy korábban létező alapértelmezett webhelye <br/> -Engedélyezése [a névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> -Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás  <br/> -Nem rendelkezik a már létező webhely vagy alkalmazás 443-as porton<br/>
Hálózati adapter típusa | VMXNET3 (Ha a VMware virtuális gépként telepített)
IP-cím típusa | Statikus
Portok | vezérlőcsatorna-vezénylés használt 443-as porton<br/>9443-as adatok átvitel

## <a name="replicated-machines"></a>Replikált gépek

A Site Recovery támogatja az egy támogatott gépen futó bármilyen számítási feladat replikálását.

**Összetevő** | **Részletek**
--- | ---
Gép beállításai | Az Azure-bA replikált gépek meg kell felelnie [Azure-követelmények](#azure-vm-requirements).
Gépeken futó számítási feladatokhoz | A Site Recovery támogatja az egy támogatott gépen futó bármilyen számítási feladat replikálását. [További információk](https://aka.ms/asr_workload).
Windows | – Windows Server 2019 (a támogatott [frissítési kumulatív 34](https://support.microsoft.com/help/4490016) (9.22 a mobilitási szolgáltatás verziója) és újabb verziók esetében.<br/> – Windows Server 2016 (64 bites a Server Core, kiszolgáló asztali kezelőfelülettel)<br/> – Windows Server 2012 R2, Windows Server 2012-ben<br/> – Windows Server 2008 R2, legalább SP1.<br/> – A Windows Server 2008 64 és 32 bites a legalacsonyabb SP2]. Csak az áttelepítéshez támogatott. [További információk](migrate-tutorial-windows-server-2008.md).<br/> – Windows 10, Windows 8.1, Windows 8, Windows 7 64 bites (a támogatott [frissítési kumulatív 36](https://support.microsoft.com/help/4503156) (9.22 a mobilitási szolgáltatás verziója és újabb verziók esetében). Windows 7 RTM nem támogatott. 
Linux | Csak 64 bites rendszeren támogatott. 32 bites rendszerben nem támogatott.<br/><br/>Minden Linux-kiszolgáló [Linux Integration Services (LIS) összetevők](https://www.microsoft.com/download/details.aspx?id=55106) telepítve. Fontos, hogy feladatátvétel/feladatátvételi teszt után indítsa el a kiszolgálót az Azure-ban. Ha a LIS összetevők hiányoznak, ügyeljen arra, hogy telepítse a [összetevők](https://www.microsoft.com/download/details.aspx?id=55106) a gépek Azure-ban a replikáció engedélyezése előtt. <br/><br/> A Site Recovery koordinálja a feladatátvétel futtatása a Linux-kiszolgálók az Azure-ban. Linux-szállítók azonban csak a teljes életciklusa még nem értek el terjesztési verziók támogatási előfordulhat, hogy korlátozza.<br/><br/> A Linux-disztribúció csak a tőzsdei kernelekkel, amelyek részei a terjesztési alverzió kiadási/frissítés támogatottak.<br/><br/> Frissíti a védett számítógépek közötti fő Linux terjesztési verziója nem támogatott. Szeretné frissíteni, tiltsa le a replikációt, és frissítse az operációs rendszert, majd újból engedélyezze a replikációt.<br/><br/> [További](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) Linux- és az Azure-ban nyílt forráskódú technológia támogatása.
Linux, Red Hat Enterprise | 5.2 a 5.11.</b><br/> 6.1, 6.10</b><br/> 7.0-ban való 7.6<br/> <br/> 5\.2 – 5.11 & 6.1-6.10 a Red Hat Enterprise Linux rendszert futtató kiszolgálók nem rendelkeznek [Linux Integration Services (LIS) összetevők](https://www.microsoft.com/download/details.aspx?id=55106) előre telepítve van. Ügyeljen arra, hogy telepítse, hogy a [összetevők](https://www.microsoft.com/download/details.aspx?id=55106) a gépek Azure-ban a replikáció engedélyezése előtt.
Linux: CentOS | 5.2 a 5.11.</b><br/> 6.1, 6.10</b><br/> 7.0-ban való 7.6<br/> <br/> CentOS rendszerű 5.2-5.11 & 6.1-6.10 kiszolgálók nem rendelkeznek [Linux Integration Services (LIS) összetevők](https://www.microsoft.com/download/details.aspx?id=55106) előre telepítve van. Ügyeljen arra, hogy telepítse, hogy a [összetevők](https://www.microsoft.com/download/details.aspx?id=55106) a gépek Azure-ban a replikáció engedélyezése előtt.
Ubuntu | Ubuntu 14.04 LTS server [(tekintse át a támogatott kernel-verzióknál)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server [(tekintse át a támogatott kernel-verzióknál)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(tekintse át a támogatott kernel-verzióknál)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(tekintse át a támogatott kernel-verzióknál)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Replikált gépek frissítése az SUSE Linux Enterprise Server 11 SP3 SP4 szervizcsomag nem támogatott. Szeretné frissíteni, tiltsa le a replikációt, és engedélyezze újra a frissítés után.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> A Red Hat-kompatibilis kernel vagy szoros vállalati Kernel kiadási 3, 4 & 5 (UEK3, UEK4, UEK5). 


### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel-verzióknál


**Támogatott kiadás** | **Mobilitási szolgáltatás verziójának** | **Kernel verziója** |
--- | --- | --- |
14.04 LTS | [9.24][9.25 UR]  | a 3.13.0-169-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-146-generic, 4.4.0-21-Generic<br/>a 4.15.0-1042-azure 4.15.0-1023-Azure |
14.04 LTS | [9.24][9.24 UR] | a 3.13.0-167-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-143-generic, 4.4.0-21-Generic<br/>a 4.15.0-1040-azure 4.15.0-1023-Azure |
14.04 LTS | [9.23][9.23 UR] | a 3.13.0-165-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-142-generic, 4.4.0-21-Generic<br/>a 4.15.0-1037-azure 4.15.0-1023-Azure |
14.04 LTS | [9.22][9.22 UR] | a 3.13.0-164-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-140-generic, 4.4.0-21-Generic<br/>4.15.0-1023-azure to 4.15.0-1036-azure |
|||
16.04 LTS | [9.25][9.25 UR] | a 4.4.0-146-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-45-generic, 4.13.0-16-Generic<br/>a 4.15.0-48-generic 4.15.0-13-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>a 4.15.0-1042-azure 4.15.0-1012-Azure|
16.04 LTS | [9.24][9.24 UR] | a 4.4.0-143-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-45-generic, 4.13.0-16-Generic<br/>a 4.15.0-46-generic 4.15.0-13-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>a 4.15.0-1040-azure 4.15.0-1012-Azure|
16.04 LTS | [9.23][9.23 UR] | a 4.4.0-142-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-45-generic, 4.13.0-16-Generic<br/>a 4.15.0-45-generic 4.15.0-13-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>a 4.15.0-1037-azure 4.15.0-1012-Azure|
16.04 LTS | [9.22][9.22 UR] | a 4.4.0-140-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-45-generic, 4.13.0-16-Generic<br/>a 4.15.0-43-generic 4.15.0-13-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>4.13.0-1005-azure to 4.13.0-1018-azure <br/>a 4.15.0-1036-azure 4.15.0-1012-Azure|

### <a name="debian-kernel-versions"></a>Debian kernel-verzióknál


**Támogatott kiadás** | **Mobilitási szolgáltatás verziójának** | **Kernel verziója** |
--- | --- | --- |
Debian 7 | [9.21][9.21 UR], [9.22][9.22 UR],[9.23][9.23 UR], [9,24][9.24 UR]| 3.2.0-4-amd64 to 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.25][9.25 UR] | 3.16.0-4-amd64 to 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |
Debian 8 | [9.22][9.22 UR],[9.23][9.23 UR], [9,24][9.24 UR] | 3.16.0-4-amd64 to 3.16.0-7-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |


### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 támogatott kernel verziója

**Kiadás** | **Mobilitási szolgáltatás verziójának** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server (SP1, SP2, SP3, SP4) 12 | [9.25][9.25 UR] | SP1 3.12.49-11-default 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 4.4.120-92.70-default</br></br>A 4.4.121-92.104-default SP2(LTSS) 4.4.121-92.73-default</br></br>A 4.4.176-94.88-default SP3 4.4.73-5-default</br></br>SP3 4.4.138-4.7-azure to 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure 4.12.14-6.9-azure |
SUSE Linux Enterprise Server (SP1, SP2, SP3, SP4) 12 | [9.24][9.24 UR] | SP1 3.12.49-11-default 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 4.4.120-92.70-default</br></br>A 4.4.121-92.101-default SP2(LTSS) 4.4.121-92.73-default</br></br>A 4.4.175-94.79-default SP3 4.4.73-5-default</br></br>SP4 4.12.14-94.41-default 4.12.14-95.6-default |
SUSE Linux Enterprise Server (SP1, SP2, SP3, SP4) 12 | [9.23][9.23 UR] | SP1 3.12.49-11-default 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 4.4.120-92.70-default</br></br>A 4.4.121-92.101-default SP2(LTSS) 4.4.121-92.73-default</br></br>A 4.4.162-94.69-default SP3 4.4.73-5-default</br></br>SP4 4.12.14-94.41-default 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | [9.22][9.22 UR] | SP1 3.12.49-11-default 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default 4.4.120-92.70-default</br></br>A 4.4.121-92.98-default SP2(LTSS) 4.4.121-92.73-default</br></br>A 4.4.162-94.72-default SP3 4.4.73-5-default |


## <a name="linux-file-systemsguest-storage"></a>Linux rendszerek/Vendég fájltárolás

**Összetevő** | **Támogatott**
--- | ---
Fájlrendszer | ext3, ext4, XFS
Kötetek kezelése | -LVM használata támogatott.<br/> - / boot az LVM támogatott [frissítési kumulatív 31](https://support.microsoft.com/help/4478871/) (9.20 a mobilitási szolgáltatás verziója) és újabb verziók esetében. A mobilitási szolgáltatás korábbi verziókban nem támogatott.<br/> -Több operációsrendszer-lemez nem támogatottak.
A Paravirtualizált tárolóeszközök | A paravirtualizált illesztőprogramok által exportált eszközök nem támogatottak.
Több üzenetsor blokk i/o-eszközök | Nem támogatott.
A HP CCISS tárolóvezérlő a fizikai kiszolgálók | Nem támogatott.
Eszköz/csatlakoztatási pont az elnevezési konvenciót | Eszköznév vagy csatlakoztatási pont nevének egyedinek kell lennie.<br/> Győződjön meg arról, hogy nincs két eszközök/csatlakoztatási pont rendelkezik-e a kis-és nagybetűérzékeny neveket. Ugyanazon a virtuális Gépen, mint például elnevezési eszközök *device1* és *Device1* nem támogatott.
Könyvtárak | Ha egy a mobilitási szolgáltatás 9.20 verziónál korábbi verzióját futtatja-e (a kiadott [31. kumulatív frissítés](https://support.microsoft.com/help/4478871/)), majd ezek a korlátozások vonatkoznak:<br/><br/> – A forráskiszolgálón az azonos operációsrendszer-lemez ezek a könyvtárak (Ha a beállítása, a partíciók/fájl-rendszerek különálló) kell lennie: / (gyökér), gyökérpartíció, usr, /usr/local, /var, a/etc.</br> -A gyökérpartíció könyvtár lemezpartíción kell és nem kell az LVM-kötet.<br/><br/> A verzió 9.20 és újabb verziók ezek a korlátozások nem érvényesek. 
Rendszerindítási könyvtár | -Több rendszerindító lemez támogatottak a virtuális gép <br/> - / rendszerindító között egynél több lemezt az LVM-kötet nem támogatott.<br/> -A gép rendszerindító lemez nélkül nem lehet replikálni.
Szabad lemezterület-követelmények| A/root partíción 2 GB <br/><br/> A telepítési mappa 250 MB
XFSv5 | XFSv5 szolgáltatások XFS fájlrendszereket, metaadatok ellenőrzőösszeg, például az olyan támogatott (a mobilitási szolgáltatás verziójának 9.10 és újabb verziók esetében).<br/> A xfs_info segédprogram használatával a partíció a XFS superblock ellenőrzése. Ha `ftype` XFSv5 funkciók vannak használatban 1, értékre van állítva.
BTRFS | A támogatott BTRFS [frissítési kumulatív 34](https://support.microsoft.com/help/4490016) (9.22 a mobilitási szolgáltatás verziója) és újabb verziók esetében. BTRFS nem támogatott, ha:<br/><br/> – A BTRFS file system subvolume módosul a védelem engedélyezése után.</br> -A BTRFS fájlrendszer több lemez oszlik.</br> -A BTRFS fájlrendszer RAID támogatja.

## <a name="vmdisk-management"></a>A felügyeleti Virtuálisgép-lemez

**Művelet** | **Részletek**
--- | ---
A replikált virtuális gép lemez átméretezése | Támogatott.
Lemez hozzáadása a replikált virtuális Gépen | Nem támogatott.<br/> Tiltsa le a virtuális gép replikációját, és adja hozzá a lemezt majd újból engedélyezze a replikációt.

## <a name="network"></a>Network (Hálózat)

**Összetevő** | **Támogatott**
--- | ---
Gazdagép-hálózat hálózati adapterek összevonása | VMware virtuális gépek esetében támogatott. <br/><br/>Fizikai gép replikálása nem támogatott.
Gazdagép hálózati VLAN | Igen.
Host network IPv4 | Igen.
Host network IPv6 | Nem.
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
Az Azure virtuális hálózati Szolgáltatásvégpontok<br/> | Igen
Gyorsított hálózatkezelés | Nem

## <a name="storage"></a>Storage
**Összetevő** | **Támogatott**
--- | ---
Dinamikus lemez | Operációs rendszer lemezének alaplemeznek kell lennie. <br/><br/>Az adatlemezek dinamikus lemezeket is lehet.
Docker-lemezkonfiguráció | Nem
Host NFS | Igen: VMware-ről<br/><br/> Nincs a fizikai kiszolgálók számára
Gazda (iSCSI/FC) TÁROLÓHÁLÓZAT | Igen
Gazdagép vsan-hoz | Igen: VMware-ről<br/><br/> N/A fizikai kiszolgálókhoz
Gazdagép többutas (MPIO) | Igen, teszteltük Microsoft DSM EMC PowerPath 5.7 SP4 EMC PowerPath DSM CLARiiON számára
Gazdagép virtuális kötetek (VVols) | Igen: VMware-ről<br/><br/> N/A fizikai kiszolgálókhoz
Vendég-kiszolgáló VMDK | Igen
Vendég-kiszolgáló megosztott fürtlemezen | Nem
Vendég-kiszolgáló titkosított lemez | Nem
Vendég-kiszolgáló NFS | Nem
Vendég-kiszolgáló iSCSI | Nem
Vendég-kiszolgáló SMB 3.0-s | Nem
Vendég-kiszolgáló RDM | Igen<br/><br/> N/A fizikai kiszolgálókhoz
Vendég-kiszolgáló > 1 TB-os lemez | Igen<br/><br/>Legfeljebb 4095 GB-ig<br/><br/> Lemez 1024 MB-nál nagyobbnak kell lennie.
Vendég-kiszolgáló, 4K fizikai logikai és a 4 k szektormérete lemez | Igen
512-bájtos fizikai szektorméretet és a Vendég/server 4K logikai lemez | Igen
Csíkozott lemez a Vendég/kiszolgáló kötet > 4 TB-ig <br/><br/>Logikaikötet-kezelő (LVM)| Igen
Vendég-kiszolgáló – tárolóhelyek | Nem
Vendég/server gyakran használt adatok hozzáadása/eltávolítása lemez | Nem
Vendég-kiszolgáló – zárható ki lemez | Igen
Vendég-kiszolgáló többutas (MPIO) | Nem
Vendég-kiszolgáló GPT partíció | Öt-partíciókat támogat a [frissítési kumulatív 37](https://support.microsoft.com/help/4508614/) (9,25 a mobilitási szolgáltatás verziója) és újabb verziók esetében. Korábban négy támogatta.
Vendég/server EFI/UEFI-rendszerindítás | – Támogatott, ha a mobilitási szolgáltatás verziójának 9.13 vagy újabb verzióját futtatja.<br/> – VMware virtuális gépek vagy a Windows Server 2012 rendszert futtató fizikai kiszolgálók áttelepítéséhez vagy újabb rendszeren támogatott Azure-bA.<br/> – Csak virtuális gépeket replikálhatja az áttelepítéshez. A helyszíni feladat-visszavétel nem támogatott.<br/> – Csak az NTFS fájlrendszer támogatott & UEFI biztonságos rendszerindítás-típus nem támogatott. <br/> – Lemez szektormérete 512 bájt / szektor fizikai kell lennie.

## <a name="replication-channels"></a>Replikációs csatornák

|**Replikáció típusa**   |**Támogatott**  |
|---------|---------|
|Kiszervezett adatátvitel (ODX)    |       Nem  |
|Kapcsolat nélküli beültetés        |   Nem      |
| Azure Data Box | Nem

## <a name="azure-storage"></a>Azure Storage tárterület

**Összetevő** | **Támogatott**
--- | ---
Helyileg redundáns tárolás | Igen
Georedundáns tárolás | Igen
Írásvédett georedundáns tárolás | Igen
Ritka elérésű tárterület | Nem
Gyakori elérésű tárolási| Nem
Blokkblobok | Nem
Titkosítás inaktív (SSE)| Igen
Prémium szintű Storage | Igen
Importálási/exportálási szolgáltatás | Nem
A virtuális hálózatok az Azure Storage-tűzfalak | Igen.<br/> Cél tárolási illetve gyorsítótárfiók (replikációs adatainak tárolására használt) a következőn:.
Általános célú v2-tárfiókok (gyakori és ritka elérésű szinten) | Nem

## <a name="azure-compute"></a>Az Azure compute

**Funkció** | **Támogatott**
--- | ---
Rendelkezésre állási csoportok | Igen
Rendelkezésre állási zónák | Nem
HUB | Igen
Felügyelt lemezek | Igen

## <a name="azure-vm-requirements"></a>Azure Virtuálisgép-követelmények

A helyszíni virtuális gépek Azure-bA replikált meg kell felelnie az Azure virtuális gépekre vonatkozó, az alábbi táblázatban foglaltak. Előfeltételek ellenőrzi a replikációs futtatásakor a Site Recovery az ellenőrzés sikertelen lesz, ha az egyes követelmények nem teljesülnek.

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
a virtuális gép neve | 1 és 63 karakternél.<br/><br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevét kell kezdődnie, és betűvel vagy számmal végződhet. |  Frissítse az értéket a Site Recovery virtuálisgép-tulajdonságokat.

## <a name="churn-limits"></a>Vásárlói lemorzsolódás korlátok

Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. 
- Ezek a korlátok tesztjeinken alapulnak, de nem terjed ki minden lehetséges alkalmazás i/o-kombinációját.
- A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak.
- A legjobb eredmények érdekében javasoljuk, hogy futtassa a [Deployment Planner eszköz](site-recovery-deployment-planner.md), és végezze el az alkalmazás alapos tesztelése használatával indított feladatátvételi tesztek ugyanúgy a teljesítményről lekérése az alkalmazáshoz.

**Replikációs cél** | **Forráslemez átlagos I/O-mérete** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |20 MB/s | Lemezenként 1684 GB


**Forrásadat-változás** | **Felső korlát**
---|---
Átlagos adatváltozás virtuális gépenként| 25 MB/s
Adatváltozás csúcsértéke az összes lemezen virtuális gépenként | 54 MB/s
Folyamatkiszolgáló által támogatott napi adatváltozás maximuma | 2 TB

- Ezek átlagos értékek, amelyek 30 százalékos I/O-átfedést feltételeznek.
- A Site Recovery képes magasabb átviteli sebesség kezelésére az átfedési arány, a nagyobb írási méretek és a számítási feladatok tényleges I/O-viselkedése alapján.
- Ezek a számok körülbelül öt perc alatt a tipikus várakozó feltételezik. Ez azt jelenti, hogy a feltöltést követő öt percben megtörténik az adat feldolgozása, és létrejön egy helyreállítási pont.

## <a name="vault-tasks"></a>Tároló-feladatok

**Művelet** | **Támogatott**
--- | ---
Tároló áthelyezése erőforráscsoportok közt | Nem
Helyezze át a tároló belül és azok az előfizetések között | Nem
Tárolás, hálózat, Azure-beli virtuális gépek erőforráscsoportok közötti áthelyezése | Nem
Át a tárterületet, hálózati, Azure virtuális gépeken belül és azok az előfizetések között. | Nem


## <a name="obtain-latest-components"></a>Szerezze be a legújabb összetevői

**Name (Név)** | **Leírás** | **Részletek**
--- | --- | ---
Konfigurációs kiszolgáló | Telepített helyszíni.<br/> Koordinálja a helyszíni VMware-kiszolgálók vagy fizikai gépek és Azure közötti kommunikációt. | - [Ismerje meg](vmware-physical-azure-config-process-server-overview.md) a konfigurációs kiszolgálón.<br/> - [Ismerje meg](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) a legújabb verzióra való frissítést.<br/> - [Ismerje meg](vmware-azure-deploy-configuration-server.md) a konfigurációs kiszolgálót állít be. 
Folyamatkiszolgáló | Alapértelmezés szerint telepítve van a konfigurációs kiszolgálón.<br/> Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítás segítségével optimalizálja őket, és elküldi azt az Azure-bA.<br/> Az üzembe helyezés növekedésével további folyamatkiszolgálók nagyobb mértékű replikációs forgalom kezelésére is hozzáadhat. | - [Ismerje meg](vmware-physical-azure-config-process-server-overview.md) a folyamatkiszolgáló.<br/> - [Ismerje meg](vmware-azure-manage-process-server.md#upgrade-a-process-server) a legújabb verzióra való frissítést.<br/> - [Ismerje meg](vmware-physical-large-deployment.md#set-up-a-process-server) horizontális felskálázási folyamatkiszolgáló beállítása.
A mobilitási szolgáltatás | A VMware virtuális gépek vagy fizikai kiszolgálókat replikálhat telepítve.<br/> Koordinálja a helyszíni VMware-kiszolgálók/fizikai kiszolgálók és Azure közötti replikáció.| - [Ismerje meg](vmware-physical-mobility-service-overview.md) a mobilitási szolgáltatást.<br/> - [Ismerje meg](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) a legújabb verzióra való frissítést.<br/> 



## <a name="next-steps"></a>További lépések
[Ismerje meg, hogyan](tutorial-prepare-azure.md) VMware virtuális gépek vészhelyreállítása az Azure előkészítése.

[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
