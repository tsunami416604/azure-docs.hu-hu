---
title: Az Azure-ból az Azure-ba történő replikálásához az Azure Site Recovery támogatási mátrix |} A Microsoft Docs
description: Foglalja össze a támogatott operációs rendszerek és konfigurációk az Azure Site Recovery-replikációja az Azure-beli virtuális gépek (VM) egy adott régióban található, egy másik a vész-helyreállítási igényeit.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: sujayt
ms.openlocfilehash: 49773e076ed8bb06ff76f9f654b914a709051fb5
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378618"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Egy Azure-régióból a másikba történő replikálásához támogatási mátrix



Ez a cikk összefoglalja a támogatott konfigurációk és replikálásakor összetevők és az Azure virtuális gépek helyreállítása egy adott régióban található egy másik régióba, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

## <a name="user-interface-options"></a>Felhasználói felület beállításai

**Felhasználói felület** |  **Támogatott / nem támogatott**
--- | ---
**Azure Portal** | Támogatott
**PowerShell** | [Azure-bA replikáció a PowerShell-lel](azure-to-azure-powershell.md)
**REST API** | Jelenleg nem támogatott
**Parancssori felület** | Jelenleg nem támogatott


## <a name="resource-support"></a>Erőforrások támogatása

**Erőforrás-áthelyezési típusa** | **Részletek**
--- | --- | ---
**Tároló áthelyezése erőforráscsoportok közt** | Nem támogatott<br/><br/> Recovery services-tároló nem helyezhetők át erőforráscsoportok közt.
**Számítási és tárolási és hálózati erőforrások áthelyezése erőforráscsoportok közt** | Nem támogatott.<br/><br/> Ha egy virtuális gép vagy a kapcsolódó összetevők, például a tárolási és hálózati után végez, replikáció letiltása és a virtuális gép újraengedélyezni szüksége.
**Azure virtuális gépek replikálása egy előfizetésből egy másikba vész-helyreállítási** | Támogatott azonos Azure Active Directory-bérlőben. Klasszikus virtuális gépek nem támogatják.
**Virtuális gépek áttelepítése a támogatott földrajzi fürtökben (belül és több előfizetés)-régiók között** | Támogatott a "Resource manager üzemi modelljét" virtuális gépek azonos Azure Active Directory-bérlőben. Nem, "A klasszikus üzemi modellt" virtuális gépek esetében támogatott.
**Az azonos régión belüli virtuális gépek áttelepítése** | Nem támogatott.


## <a name="support-for-replicated-machine-os-versions"></a>A replikált gép operációsrendszer-verziók támogatása

Az alábbi támogatási alkalmazható az említett operációs rendszeren futó bármilyen számítási feladat számára.

#### <a name="windows"></a>Windows

- A Windows Server 2016 (Server Core, kiszolgáló asztali kezelőfelülettel) *
- Windows Server 2012 R2
- Windows Server 2012
- A Windows Server 2008 R2, legalább SP1

>[!NOTE]
>
> \* A Windows Server 2016 Nano Server nem támogatott.

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7, 6.8, 6.9, 6.10, 7.0-ban, 7.1-es, 7.2, 7.3, 7.4, 7.5   
- CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0-ban, 7.1-es, 7.2, 7.3,7.4, 7.5
- Ubuntu 14.04 LTS Server [ (támogatott kernel-verzióknál)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Ubuntu 16.04 LTS Server [ (támogatott kernel-verzióknál)](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
- Debian 7 [ (támogatott kernel-verzióknál)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- Debian 8 [ (támogatott kernel-verzióknál)](#supported-debian-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 12 SP1, SP2 SP3 [ (támogatott kernel-verzióknál)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
- SUSE Linux Enterprise Server 11 SP3
- SUSE Linux Enterprise Server 11 SP4
- Oracle Linux 6.4, 6.5, 6.6, a Red Hat-kompatibilis kernel vagy a szoros vállalati Kernel Release-3 (UEK3) futó 6.7

(A gépek replikálásához az SLES 11 SP3 az SLES 11 SP4 frissítés nem támogatott. Ha a replikált gépek az SLES 11 SP4 SLES 11SP3 frissítették, meg kell tiltsa le a replikációt és a post újra a frissítés a gép védelmét.)

>[!NOTE]
>
> Előfordulhat, hogy Ubuntu-kiszolgálók jelszóalapú hitelesítéssel és a bejelentkezési és a cloud-init csomag használatával konfigurálhatja a felhőbeli virtuális gépekhez, jelszóalapú bejelentkezés idején feladatátvétel (függően cloudinit konfigurációja.) Jelszóalapú bejelentkezés újból engedélyezhető a virtuális gépen ehhez állítsa alaphelyzetbe a jelszót a beállítások menüben (a támogatás + hibaelhárítás alatt a szakaszt) a sikertelen virtuális gépet az Azure Portalon keresztül.

### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure-beli virtuális gépek támogatott Ubuntu kernel verziója

**Kiadás** | **Mobilitási szolgáltatás verziójának** | **Kernel verziója** |
--- | --- | --- |
14.04 LTS | 9.19 | a 3.13.0-153-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-131-generic 4.4.0-21-Generic |
14.04 LTS | 9.18 | a 3.13.0-151-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-128-generic 4.4.0-21-Generic |
14.04 LTS | 9.17 | a 3.13.0-147-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-124-generic 4.4.0-21-Generic |
14.04 LTS | 9.16 | a 3.13.0-144-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-119-generic 4.4.0-21-Generic |
|||
16.04 LTS | 9.19 | a 4.4.0-131-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-45-generic, 4.13.0-16-Generic<br/>a 4.15.0-30-generic 4.15.0-13-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>a 4.13.0-1018-azure 4.13.0-1005-Azure <br/>a 4.15.0-1019-azure 4.15.0-1012-Azure|
16.04 LTS | 9.18 | a 4.4.0-128-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-45-generic, 4.13.0-16-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>a 4.13.0-1018-azure 4.13.0-1005-Azure |
16.04 LTS | 9.17 | a 4.4.0-124-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-41-generic, 4.13.0-16-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>a 4.13.0-1016-azure 4.13.0-1005-Azure |
16.04 LTS | 9.16 | a 4.4.0-119-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-38-generic, 4.13.0-16-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>a 4.13.0-1012-azure 4.13.0-1005-Azure |


### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Debian kernel verziója támogatott Azure-beli virtuális gépek

**Kiadás** | **Mobilitási szolgáltatás verziójának** | **Kernel verziója** |
--- | --- | --- |
Debian 7 | 9.17,9.18,9.19 | a 3.2.0-6-amd64, 3.2.0-4-AMD64 3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.16 | a 3.2.0-5-amd64, 3.2.0-4-AMD64 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.19 | a 3.16.0-6-amd64, hogy 4.9.0-0.bpo.7-amd64 4.9.0-0.bpo.4-amd64 3.16.0-4-AMD64 |
Debian 8 | 9.17, 9.18 | a 3.16.0-6-amd64, hogy 4.9.0-0.bpo.6-amd64 4.9.0-0.bpo.4-amd64 3.16.0-4-AMD64 |
Debian 8 | 9.16 | a 3.16.0-5-amd64, hogy 4.9.0-0.bpo.5-amd64 4.9.0-0.bpo.4-amd64 3.16.0-4-AMD64 |

### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure-beli virtuális gépek támogatott SUSE Linux Enterprise Server 12 kernel verziója

**Kiadás** | **Mobilitási szolgáltatás verziójának** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.19 | SP1 3.12.49-11-default 3.12.74-60.64.40-default</br></br> A 3.12.74-60.64.93-default SP1(LTSS) 3.12.74-60.64.45-default</br></br> SP2 4.4.21-69-default 4.4.120-92.70-default</br></br>A 4.4.121-92.80-default SP2(LTSS) 4.4.121-92.73-default</br></br>A 4.4.140-94.42-default SP3 4.4.73-5-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.18 | SP1 3.12.49-11-default 3.12.74-60.64.40-default</br></br> A 3.12.74-60.64.93-default SP1(LTSS) 3.12.74-60.64.45-default</br></br> SP2 4.4.21-69-default 4.4.120-92.70-default</br></br>A 4.4.121-92.80-default SP2(LTSS) 4.4.121-92.73-default</br></br>A 4.4.138-94.39-default SP3 4.4.73-5-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.17 | SP1 3.12.49-11-default 3.12.74-60.64.40-default</br></br> A 3.12.74-60.64.88-default SP1(LTSS) 3.12.74-60.64.45-default</br></br> SP2 4.4.21-69-default 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>A 4.4.126-94.22-default SP3 4.4.73-5-default |

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>Támogatott rendszerek és a Vendég tárolási konfigurációk Linux operációs rendszert futtató Azure-beli virtuális gépeken

* Fájlrendszer: ext3, ext4, ReiserFS (Suse Linux Enterprise Server csak), XFS
* Kötetek kezelése: LVM2
* A többutas szoftver: eszköz leképezője

## <a name="region-support"></a>Régió támogatása

Replikálja, és a virtuális gépek helyreállítása ugyanazon a földrajzi fürtön belül bármely két régió között.

**Földrajzi fürt** | **Azure-régiók**
-- | --
Az Amerikai | Kelet-Kanada, Kanada középső, déli középső RÉGIÓJA, USA nyugati középső RÉGIÓJA, USA keleti RÉGIÓJA, USA keleti RÉGIÓJA 2, USA nyugati RÉGIÓJA, USA nyugati RÉGIÓJA 2, USA középső RÉGIÓJA, USA északi középső RÉGIÓJA
Európa | Egyesült Királyság nyugati régiója, Egyesült Királyság déli régiója, Észak-Európa, Nyugat-Európa, Franciaország közép-India, Dél-Franciaország
Ázsia | Dél-India, közép-India, Délkelet-Ázsia, Kelet-Ázsia, kelet-japán, Nyugat-japán, Korea középső régiója, Korea déli régiója
Ausztrália   | Kelet-Ausztrália, Délkelet-Ausztrália, Ausztrália középső régiója, Ausztrália 2. középső régiója
Azure Government    | USA-beli Államigazgatás – Virginia, USA-beli Államigazgatás – Iowa, USA-beli államigazgatás – Arizona, USA-beli államigazgatás – Texas, US DOD keleti régiója, USA védelmi Minisztériuma – középső régiója
Németország | Közép-India, Németország északkelet-Németország
Kína | Kelet-Kína, Észak-Kína

>[!NOTE]
>
> Dél-brazíliai régióban csak replikálni, és az USA déli középső Régiója, USA nyugati középső Régiója, USA keleti RÉGIÓJA, USA keleti RÉGIÓJA 2, USA nyugati RÉGIÓJA, 2. nyugati RÉGIÓJA és USA északi középső Régiója régiók egyik feladatátvétel és feladat-visszavételt.

## <a name="support-for-vmdisk-management"></a>Virtuálisgép-lemez felügyelet támogatása

**Művelet** | **Részletek**
-- | ---
A replikált virtuális gép lemez átméretezése | Támogatott
Lemez hozzáadása a replikált virtuális gép | Nem támogatott. Meg kell letiltani a virtuális gép adja hozzá a lemezt, és majd újból engedélyezze a replikációt.


## <a name="support-for-compute-configuration"></a>A számítási konfiguráció támogatása

**Konfigurálás** | **Támogatott/nem támogatott** | **Megjegyzések**
--- | --- | ---
Méret | Minden olyan Azure virtuális gép méretét legalább 2 processzormag és 1 GB RAM | Tekintse meg [Azure virtuálisgép-méretek](../virtual-machines/windows/sizes.md)
Rendelkezésre állási csoportok | Támogatott | A portál az alapértelmezett beállítás a "Replikáció engedélyezése" lépés során használ, a rendelkezésre állási csoport-e automatikusan létrehozott forrás régió konfigurációja alapján. Módosíthatja a cél rendelkezésre állási csoport "replikált elem > Beállítások > Számítás és hálózat > rendelkezésre állási csoport" bármikor.
Rendelkezésre állási zónák | Nem támogatott | Rendelkezésre állási zónában üzembe helyezett virtuális gépek jelenleg nem támogatott.
A hibrid használatra Benefit (HUB) virtuális gépek | Támogatott | Ha a forrásoldali virtuális gép HUB licenc engedélyezve van, a feladatátvételi teszt vagy feladatátvétel virtuális gép is az ESEMÉNYKÖZPONT-licencet használ.
Virtuálisgép-méretezési csoportok | Nem támogatott |
Az Azure-katalógus rendszerképek – a Microsoft közzétett | Támogatott | Támogatott mindaddig, amíg a virtuális gép Site Recovery által támogatott operációs rendszeren futtatja.
Azure-katalógus-lemezkép - harmadik féltől származó közzétett | Támogatott | Site Recovery által támogatott operációs rendszeren futtatja a virtuális gép is támogatja.
Egyéni lemezkép - harmadik féltől származó közzétett | Támogatott | Site Recovery által támogatott operációs rendszeren futtatja a virtuális gép is támogatja.
Virtuális gépek áttelepítése a Site Recovery | Támogatott | Ha a VMware-ről/fizikai gépek migrálása az Azure Site Recovery segítségével, távolítsa el a mobilitási szolgáltatás régebbi verzióját, és indítsa újra a gépet egy másik Azure-régióban való replikálásához, mielőtt szüksége.

## <a name="support-for-storage-configuration"></a>Tárolási konfiguráció támogatása

**Konfigurálás** | **Támogatott/nem támogatott** | **Megjegyzések**
--- | --- | ---
Maximális operációsrendszer-lemez mérete | 2048 GB | Tekintse meg [virtuális gépek által használt lemezek.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Maximális adatlemez mérete | 4095 GB | Tekintse meg [virtuális gépek által használt lemezek.](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)
Adatlemezek száma | Legfeljebb 64, egy adott Azure-beli Virtuálisgép-méretet támogatja | Tekintse meg [Azure virtuálisgép-méretek](../virtual-machines/windows/sizes.md)
Ideiglenes lemez | Mindig ki van zárva a replikációból | Ideiglenes lemez a replikációból mindig ki van zárva. Azure útmutató alapján ideiglenes lemezen ne helyezzen állandó adatokat. Tekintse meg [Azure virtuális gépeken ideiglenes lemez](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk) további részletekért.
A módosult adatok aránya a lemezen | Legfeljebb 10 MBps for a Premium storage lemezenkénti és a standard szintű storage lemezenkénti 2 MB/s | Ha az átlagos adatváltozási sebessége a lemezen, meghaladja a 10 MB/s (a prémium szintű) és 2 MB/s (Standard) folyamatosan, replikációs nem képes. Azonban ha egy alkalmanként adatokat adatlöketek, és az adatmódosítási arány meghaladja a 10 MB/s (a prémium szintű) és 2 MB/s (Standard) egy kis ideig, és előre, replikációs fog legérdekesebb részeit. Ebben az esetben jelenhet meg némileg késleltetett helyreállítási pontokat.
Lemezek a standard szintű storage-fiókok | Támogatott |
Lemezek a prémium szintű storage-fiókok | Támogatott | Ha egy virtuális gép lemezei prémium és standard szintű tárfiókok helyezkednek el, kiválaszthatja az egyes lemezek biztosíthatja, hogy azonos tárolási konfigurációval a célrégióban egy másik céloldali tárfiókkal
A standard szintű felügyelt lemezek | Támogatott az Azure-régióban, amelyben az Azure Site Recovery támogatott. |  
Prémium szintű felügyelt lemezek | Támogatott az Azure-régióban, amelyben az Azure Site Recovery támogatott. |
A tárolóhelyek | Támogatott |         
Titkosítás inaktív állapotban (SSE) | Támogatott | Az SSE az alapértelmezett beállítás, a storage-fiókok.   
Az Azure Disk Encryption (ADE) a Windows operációs rendszer | Engedélyezve van a virtuális gépek [az Azure AD-alkalmazást encryption](https://aka.ms/ade-aad-app) támogatottak |
Az Azure Disk Encryption (ADE) a Linux operációs rendszer | Nem támogatott |
Gyakori elérésű hozzáadása/eltávolítása lemez | Nem támogatott | Ha ad hozzá, vagy távolítsa el az adatlemezt a virtuális gépen, tiltsa le a replikációt, és engedélyezze újra a virtuális gép replikációját szüksége.
Lemez kizárása | Nem támogatott|   Ideiglenes lemez alapértelmezés szerint ki van zárva.
Közvetlen tárolóhelyek  | Nem támogatott|
Kibővített fájlkiszolgáló  | Nem támogatott|
LRS | Támogatott |
GRS | Támogatott |
RA-GRS | Támogatott |
ZRS | Nem támogatott |  
Ritkán használt adatok és a gyakori elérésű tárolási | Nem támogatott | Virtuálisgép-lemezek nem támogatottak a ritkán használt adatok és a gyakori elérésű tárolási
Virtuális hálózatok az Azure Storage-tűzfalak  | Nem | Engedélyezi a hozzáférést az adott Azure virtuális hálózatokhoz a replikált adatok tárolására szolgáló gyorsítótár tárfiókokhoz nem támogatott.
Általános célú V2-tárfiókok (mindkét gyakori és ritka elérésű szint) | Nem | Tranzakciós költségek növelése jelentősen képest általános célú V1-tárfiókok

>[!IMPORTANT]
> Győződjön meg arról, hogy a virtuális gép lemez méretezhetőségi és teljesítménycéljai az erőforrásigények [Linux](../virtual-machines/linux/disk-scalability-targets.md) vagy [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuális gépek semmilyen teljesítménybeli problémák elkerülése érdekében. Ha követi az alapértelmezett beállításokat, a Site Recovery létrehozza a lemezek szükséges és a storage-fiókokat az adatforrás-konfiguráció alapján. Ha testre szabhatja, és válassza ki a saját beállításait, győződjön meg arról, hogy a forrás virtuális gépek esetében kövesse a lemez méretezhetőségi és teljesítménycéljai.

## <a name="support-for-network-configuration"></a>Hálózati konfiguráció támogatása
**Konfigurálás** | **Támogatott/nem támogatott** | **Megjegyzések**
--- | --- | ---
Hálózati adapter (NIC) | Legfeljebb egy adott Azure virtuális gép mérete által támogatott hálózati adapterek maximális száma | A feladatátvételi teszt vagy a feladatátvételi művelet részeként jön létre a virtuális gép hálózati adaptereket hoz létre. A hálózati adapterek száma a forrás virtuális gép rendelkezik a replikáció engedélyezése idején függ a hálózati adapterek száma a feladatátvételi virtuális Gépen. Ha Ön hozzáadása/eltávolítása NIC replikáció engedélyezése után, ne befolyásolja a feladatátvételi virtuális Gépen hálózati adapter száma.
Internetes Load Balancer | Támogatott | Az előre konfigurált terheléselosztó egy azure automation-szkript használatával a helyreállítási terv társítani kell.
Belső Load balancer | Támogatott | Az előre konfigurált terheléselosztó egy azure automation-szkript használatával a helyreállítási terv társítani kell.
Nyilvános IP-cím| Támogatott | Már meglévő nyilvános IP-cím a hálózati adapterhez társítani, vagy hozzon létre egyet, és társítsa a hálózati adapter egy azure automation-szkript használatával a helyreállítási tervben szereplő szüksége.
NSG-t a hálózati Adapterre (Resource Manager)| Támogatott | A hálózati adapter egy azure automation-szkript használatával a helyreállítási tervben szereplő NSG hozzárendelése kell.  
NSG-t az alhálózat (Resource Manager és Klasszikus modell)| Támogatott | Az NSG az alhálózathoz, egy azure automation-szkript használatával a helyreállítási tervben szereplő társítani kell.
NSG-t a virtuális gép (klasszikus)| Támogatott | A hálózati adapter egy azure automation-szkript használatával a helyreállítási tervben szereplő NSG hozzárendelése kell.
Fenntartott IP (statikus IP-cím) / forrás IP-cím megőrzése | Támogatott | Ha a forrásoldali virtuális gép hálózati adapter statikus IP-konfigurációt pedig a célként megadott alhálózat a azonos IP-cím elérhető, hozzá van rendelve a feladatátvételi virtuális Gépen. A célként megadott alhálózat nem rendelkezik a azonos IP-cím elérhető, ha az alhálózaton elérhető IP-címekről egyik virtuális gép számára van fenntartva. Megadhatja, hogy a választott fix IP-cím "replikált elem > Beállítások > Számítás és hálózat > hálózati adapter. Válassza ki a hálózati Adaptert, és adja meg az alhálózatot és IP-címét a választott.
Dinamikus IP| Támogatott | Ha a forrásoldali virtuális gép hálózati adapter dinamikus IP-konfiguráció, a hálózati adapter a feladatátvételi virtuális Géphez nem is alapértelmezés szerint dinamikus. Megadhatja, hogy a választott fix IP-cím "replikált elem > Beállítások > Számítás és hálózat > hálózati adapter. Válassza ki a hálózati Adaptert, és adja meg az alhálózatot és IP-címét a választott.
A Traffic Manager integrálása | Támogatott | Előre konfigurálhatja úgy, hogy az adatforgalmat a végpont a forrásrégióban rendszeres időközönként, és a feladatátvétel esetén a célrégióban a végpont a traffic manager.
Azure managed DNS | Támogatott |
Egyéni DNS  | Támogatott |    
Hitelesített proxykiszolgálói | Támogatott | Tekintse meg [hálózati dokumentum.](site-recovery-azure-to-azure-networking-guidance.md)    
Hitelesített proxyt | Nem támogatott | Ha a virtuális gép egy hitelesített proxyt használ a kimenő hálózati kapcsolatot, azt nem lehet replikálni az Azure Site Recovery használatával.    
A helyszíni (vagy anélkül ExpressRoute) a helyek közötti VPN| Támogatott | Győződjön meg arról, hogy úgy, hogy a Site recovery nem adatforgalmat helyszíni az udr-EK és NSG-k vannak konfigurálva. Tekintse meg [hálózati dokumentum.](site-recovery-azure-to-azure-networking-guidance.md)  
Virtuális hálózatok közötti kapcsolat | Támogatott | Tekintse meg [hálózati dokumentum.](site-recovery-azure-to-azure-networking-guidance.md)  
Virtuális hálózati szolgáltatásvégpontok | Támogatott | Az Azure Storage-tűzfalak, virtuális hálózatok esetében nem támogatottak. Engedélyezi a hozzáférést az adott Azure virtuális hálózatokhoz a replikált adatok tárolására szolgáló gyorsítótár tárfiókokhoz nem támogatott.
Gyorsított hálózatkezelés | Támogatott | A forrásoldali virtuális gép gyorsított hálózatkezelés engedélyezni kell. [További információk](azure-vm-disaster-recovery-with-accelerated-networking.md).


## <a name="next-steps"></a>További lépések
- Tudjon meg többet [hálózatkezelési útmutató Azure virtuális gépek replikálása](site-recovery-azure-to-azure-networking-guidance.md)
- A számítási feladatai védelméről [Azure virtuális gépek replikálása](site-recovery-azure-to-azure.md)
