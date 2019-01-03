---
title: Vész-helyreállítási Azure IaaS virtuális gépek az Azure Site Recoveryvel Azure-régiók közti Azure Site Recovery támogatási mátrix |} A Microsoft Docs
description: Foglalja össze a támogatott operációs rendszerek és konfigurációk az Azure Site Recovery-replikációja az Azure-beli virtuális gépek (VM) egy adott régióban található, egy másik a vész-helyreállítási igényeit.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 24776e2d14824c6d7b028a2cb9e2ba1e1aaa5c96
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789126"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Egy Azure-régióból a másikba történő replikálásához támogatási mátrix

Ez a cikk összefoglalja támogatott konfigurációk és összetevők replikálása, feladatátvétele és helyreállítása Azure virtuális gépek egy Azure-régióból egy másikba, a vész-helyreállítási telepítésekor használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.


## <a name="deployment-method-support"></a>Üzembe helyezési módszer támogatása

**Az üzembe helyezési módszer** |  **Támogatott / nem támogatott**
--- | ---
**Azure Portal** | Támogatott
**PowerShell** | [Azure-bA replikáció a PowerShell-lel](azure-to-azure-powershell.md)
**REST API** | Jelenleg nem támogatott
**Parancssori felület** | Jelenleg nem támogatott


## <a name="resource-support"></a>Erőforrások támogatása

**Erőforrás-művelet** | **Részletek**
--- | --- | ---
**Tároló áthelyezése erőforráscsoportok közt** | Nem támogatott
**Számítási és tárolási és hálózati erőforrások áthelyezése erőforráscsoportok közt** | Nem támogatott.<br/><br/> Ha egy virtuális gép vagy a kapcsolódó összetevők, például a tárolási és hálózati után a virtuális gép áll replikálás alatt, kell tiltsa le, majd engedélyezze újra a virtuális gép replikálását.
**Azure virtuális gépek replikálása egy előfizetésből egy másikba vész-helyreállítási** | Támogatott azonos Azure Active Directory-bérlőben.
**Virtuális gépek áttelepítése támogatott földrajzi fürtökben (belül és több előfizetés)-régiók között** | Támogatott azonos Azure Active Directory-bérlőben.
**Az azonos régión belüli virtuális gépek áttelepítése** | Nem támogatott.

## <a name="region-support"></a>Régió támogatása

Replikálja, és a virtuális gépek helyreállítása ugyanazon a földrajzi fürtön belül bármely két régió között. Földrajzi fürtök és az adatok szem vannak meghatározva.

**Földrajzi fürt** | **Azure-régiók**
-- | --
Az Amerikai | Kelet-Kanada, Kanada középső, déli középső RÉGIÓJA, USA nyugati középső RÉGIÓJA, USA keleti RÉGIÓJA, USA keleti RÉGIÓJA 2, USA nyugati RÉGIÓJA, USA nyugati RÉGIÓJA 2, USA középső RÉGIÓJA, USA északi középső RÉGIÓJA
Európa | Egyesült Királyság nyugati régiója, Egyesült Királyság déli régiója, Észak-Európa, Nyugat-Európa, Franciaország közép-India, Dél-Franciaország
Ázsia | Dél-India, közép-India, Délkelet-Ázsia, Kelet-Ázsia, kelet-japán, Nyugat-japán, Korea középső régiója, Korea déli régiója
Ausztrália   | Kelet-Ausztrália, Délkelet-Ausztrália, Ausztrália középső régiója, Ausztrália 2. középső régiója
Azure Government    | USA-beli Államigazgatás – Virginia, USA-beli Államigazgatás – Iowa, USA-beli államigazgatás – Arizona, USA-beli államigazgatás – Texas, US DOD keleti régiója, USA védelmi Minisztériuma – középső régiója
Németország | Közép-India, Németország északkelet-Németország
Kína | Kelet-Kína, Észak-Kína, Kína North2, Kína East2

>[!NOTE]
>
> Dél-brazíliai régióban hogy replikálhat, és átadja a feladatokat az alábbi lehetőségek közül: USA déli középső Régiója, USA nyugati középső Régiója, USA keleti RÉGIÓJA, USA keleti RÉGIÓJA 2, USA nyugati RÉGIÓJA, 2. nyugati RÉGIÓJA és USA északi középső Régiója régióban.
>
> Megjegyzendő, hogy a Site Recovery csak engedélyezve Dél-brazíliai régióban, ahol a virtuális gépek védhetők a forrásrégióban, használható. Azt nem használható egy cél DR régióban bármely, az USA déli középső Régióját például az Azure-régióban. Folyamatban van a késés okát megfigyelt földrajzi távolságtól miatt javasoljuk, hogy válassza ki a Dél-Brazília kivételével bármely más amerikai régióban.

## <a name="cache-storage"></a>Gyorsítótár

Ez a táblázat összefoglalja a Site Recovery által replikáció közben használt gyorsítótárfiók támogatása.

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Általános célú V2 tárfiókok (gyors és lassú elérésű szint) | Nem támogatott. | A korlátozás létezik a gyorsítótárban mivel lényegesen nagyobb, mint a V1-tárfiókok tranzakciós költségeinek a v2-ben.
Virtuális hálózatok az Azure Storage-tűzfalak  | Támogatott | Ha engedélyezve van a tűzfal gyorsítótárfiókot vagy a célként megadott tárfiók használ, ügyeljen arra, hogy ["Allow megbízható Microsoft-szolgáltatások"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## <a name="replicated-machine-operating-systems"></a>A replikált gép operációs rendszerek

A Site Recovery támogatja a a jelen szakaszban felsorolt operációs rendszert futtató Azure virtuális gépek replikációját.

### <a name="windows"></a>Windows

**Operációs rendszer** | **Részletek**
--- | ---
Windows Server 2016  | A Server Core, kiszolgáló asztali kezelőfelülettel
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Az SP1 vagy újabb

#### <a name="linux"></a>Linux

**Operációs rendszer** | **Részletek**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0-ban, 7.1-es, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0-ban, 7.1-es, 7.2, 7.3, 7.4, 7.5, 7.6
Ubuntu 14.04 LTS Server | [Támogatott kernel-verzióknál](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [Támogatott kernel verziója](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Előfordulhat, hogy Ubuntu-kiszolgálók jelszó alapú hitelesítési és bejelentkezési és a cloud-init-csomag konfigurálása a cloud-beli virtuális gépek jelszóalapú bejelentkezés le van tiltva, a feladatátvétel (függően a cloudinit konfigurálása). Jelszóalapú bejelentkezés újból engedélyezhető a virtuális gépen az ügyfélszolgálattól a jelszó alaphelyzetbe állításával > Hibaelhárítás > Beállítások menüjében (a feladatátvételen átesett virtuális gép az Azure Portalon.
Debian 7 | [Támogatott kernel-verzióknál](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Támogatott kernel-verzióknál)](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2 SP3 VERZIÓT KELL HASZNÁLNIA. [Támogatott kernel-verzióknál](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> A gépek replikálásához SP3 SP4 való frissítés nem támogatott. Ha a replikált gépek frissítve lett, tiltsa le a replikációt, és engedélyezze újra a frissítés után a replikáció szüksége.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0-ban, 7.1-es, 7.2, 7.3, 7.4, 7.5 <br/><br/> A Red Hat-kompatibilis kernel vagy a szoros vállalati Kernel Release-3 (UEK3) fut.


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure-beli virtuális gépek támogatott Ubuntu kernel verziója

**Kiadás** | **Mobilitási szolgáltatás verziójának** | **Kernel verziója** |
--- | --- | --- |
14.04 LTS | 9.20 | a 3.13.0-161-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-138-generic, 4.4.0-21-Generic<br/>a 4.15.0-1030-azure 4.15.0-1023-Azure |
14.04 LTS | 9.19 | a 3.13.0-153-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-131-generic 4.4.0-21-Generic |
14.04 LTS | 9.18 | a 3.13.0-151-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-128-generic 4.4.0-21-Generic |
14.04 LTS | 9.17 | a 3.13.0-147-generic, 3.13.0-24-Generic<br/>a 3.16.0-77-generic, 3.16.0-25-Generic<br/>a 3.19.0-80-generic, 3.19.0-18-Generic<br/>a 4.2.0-42-generic, 4.2.0-18-Generic<br/>a 4.4.0-124-generic 4.4.0-21-Generic |
|||
16.04 LTS | 9.20 | a 4.4.0-138-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-45-generic, 4.13.0-16-Generic<br/>a 4.15.0-38-generic 4.15.0-13-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>a 4.13.0-1018-azure 4.13.0-1005-Azure <br/>a 4.15.0-1030-azure 4.15.0-1012-Azure|
16.04 LTS | 9.19 | a 4.4.0-131-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-45-generic, 4.13.0-16-Generic<br/>a 4.15.0-30-generic 4.15.0-13-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>a 4.13.0-1018-azure 4.13.0-1005-Azure <br/>a 4.15.0-1019-azure 4.15.0-1012-Azure|
16.04 LTS | 9.18 | a 4.4.0-128-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-45-generic, 4.13.0-16-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>a 4.13.0-1018-azure 4.13.0-1005-Azure |
16.04 LTS | 9.17 | a 4.4.0-124-generic, 4.4.0-21-Generic<br/>a 4.8.0-58-generic, 4.8.0-34-Generic<br/>a 4.10.0-42-generic, 4.10.0-14-Generic<br/>a 4.11.0-14-generic, 4.11.0-13-Generic<br/>a 4.13.0-41-generic, 4.13.0-16-Generic<br/>a 4.11.0-1016-azure, 4.11.0-1009-Azure<br/>a 4.13.0-1016-azure 4.13.0-1005-Azure |


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Debian kernel verziója támogatott Azure-beli virtuális gépek

**Kiadás** | **Mobilitási szolgáltatás verziójának** | **Kernel verziója** |
--- | --- | --- |
Debian 7 | 9.17,9.18,9.19,9.20 | a 3.2.0-6-amd64, 3.2.0-4-AMD64 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.20 | a 3.16.0-7-amd64, hogy 4.9.0-0.bpo.8-amd64 4.9.0-0.bpo.4-amd64 3.16.0-4-AMD64 |
Debian 8 | 9.19 | a 3.16.0-6-amd64, hogy 4.9.0-0.bpo.7-amd64 4.9.0-0.bpo.4-amd64 3.16.0-4-AMD64 |
Debian 8 | 9.17, 9.18 | a 3.16.0-6-amd64, hogy 4.9.0-0.bpo.6-amd64 4.9.0-0.bpo.4-amd64 3.16.0-4-AMD64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure-beli virtuális gépek támogatott SUSE Linux Enterprise Server 12 kernel verziója

**Kiadás** | **Mobilitási szolgáltatás verziójának** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.20 | SP1 3.12.49-11-default 3.12.74-60.64.40-default<br/><br/> A 3.12.74-60.64.107-default SP1(LTSS) 3.12.74-60.64.45-default<br/><br/> SP2 4.4.21-69-default 4.4.120-92.70-default<br/><br/>A 4.4.121-92.98-default SP2(LTSS) 4.4.121-92.73-default<br/><br/>A 4.4.162-94.69-default SP3 4.4.73-5-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.19 | SP1 3.12.49-11-default 3.12.74-60.64.40-default<br/><br/> A 3.12.74-60.64.93-default SP1(LTSS) 3.12.74-60.64.45-default<br/><br/> SP2 4.4.21-69-default 4.4.120-92.70-default<br/><br/>A 4.4.121-92.80-default SP2(LTSS) 4.4.121-92.73-default<br/><br/>A 4.4.140-94.42-default SP3 4.4.73-5-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.18 | SP1 3.12.49-11-default 3.12.74-60.64.40-default<br/><br/> A 3.12.74-60.64.93-default SP1(LTSS) 3.12.74-60.64.45-default<br/><br/> SP2 4.4.21-69-default 4.4.120-92.70-default<br/><br/>A 4.4.121-92.80-default SP2(LTSS) 4.4.121-92.73-default<br/><br/>A 4.4.138-94.39-default SP3 4.4.73-5-default |
SUSE Linux Enterprise Server 12 (SP3 SP1, SP2) | 9.17 | SP1 3.12.49-11-default 3.12.74-60.64.40-default<br/><br/> A 3.12.74-60.64.88-default SP1(LTSS) 3.12.74-60.64.45-default<br/><br/> SP2 4.4.21-69-default 4.4.120-92.70-default<br/><br/>SP2(LTSS) 4.4.121-92.73-default<br/><br/>A 4.4.126-94.22-default SP3 4.4.73-5-default |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikált gépek – Linux rendszer/Vendég fájltárolás

* Fájlrendszer: ext3, ext4, ReiserFS (csak Suse Linux Enterprise Server), XFS, BTRFS
* Kötetek kezelése: LVM2
* A többutas szoftver: Eszköz leképezője


## <a name="replicated-machines---compute-settings"></a>A replikált gépek – a számítási beállítások

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Méret | Minden olyan Azure virtuális gép méretét legalább 2 processzormag és 1 GB RAM | Győződjön meg arról [Azure virtuálisgép-méretek](../virtual-machines/windows/sizes.md).
Rendelkezésre állási csoportok | Támogatott | Ha engedélyezi az alapértelmezett beállításokat az Azure virtuális gép replikációját, egy rendelkezésre állási csoportot a forrás területi beállítások alapján automatikusan jön létre. Ezek a beállítások módosítása
Rendelkezésre állási zónák | Támogatott |
Hybrid Use Benefit (HUB) | Támogatott | Ha a forrásoldali virtuális gép van engedélyezve, HUB licence feladatátvételi teszt vagy feladatátvétel a virtuális gép is használ a HUB licenc.
Virtuális gépek méretezési csoportjai | Nem támogatott |
Azure-katalógus-rendszerképek – a Microsoft közzétett | Támogatott | Támogatott, ha a virtuális Gépet egy támogatott operációs rendszert futtat.
Azure-katalógus-lemezkép - harmadik féltől származó közzétett | Támogatott | Támogatott, ha a virtuális Gépet egy támogatott operációs rendszert futtat.
Egyéni lemezkép - harmadik féltől származó közzétett | Támogatott | Támogatott, ha a virtuális Gépet egy támogatott operációs rendszert futtat.
Virtuális gépek áttelepítése a Site Recovery | Támogatott | Ha VMware virtuális gép vagy fizikai gép lett migrálva az Azure Site Recovery segítségével, távolítsa el a gépen futó mobilitási szolgáltatás régebbi verzióját, és indítsa újra a gépet egy másik Azure-régióban való replikálásához, mielőtt szüksége.

## <a name="replicated-machines---disk-actions"></a>Replikált gépek - lemez műveletek

**Művelet** | **Részletek**
-- | ---
A replikált virtuális gép lemez átméretezése | Támogatott
Adjon hozzá egy lemezt egy replikált virtuális gép | Nem támogatott.<br/><br/> Meg kell letiltani a virtuális gép adja hozzá a lemezt, és majd újból engedélyezze a replikációt.

## <a name="replicated-machines---storage"></a>A replikált gépek – tárolás

Ez a táblázat foglalja össze a Azure virtuális gép operációsrendszer-lemezt, adatlemez és ideiglenes lemez támogatása.

- Fontos, hogy figyelje meg a virtuális gép korlátok és célok [Linux](../virtual-machines/linux/disk-scalability-targets.md) és [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuális gépek semmilyen teljesítménybeli problémák elkerülése érdekében.
- Ha az alapértelmezett beállításokkal telepít, a Site Recovery automatikusan létrehozza lemezek és a forrás-beállításai alapján a storage-fiókok.
- Ha testre szabni, győződjön meg arról, kövesse az útmutatást.

**Összetevő** | **Támogatás** | **Részletek**
--- | --- | ---
Az operációs rendszer lemez maximális mérete | 2048 GB | [További](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms) Virtuálisgép-lemezeket.
Ideiglenes lemez | Nem támogatott | Az ideiglenes lemez mindig ki van zárva a replikációból.<br/><br/> Nem minden olyan állandó az ideiglenes lemezen lévő adatok. [További információk](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk).
Adatlemez maximális mérete | 4095 GB |
Adatok lemez maximális száma | Egy adott Azure-beli Virtuálisgép-méretet támogatása akár 64, összhangban | [További](../virtual-machines/windows/sizes.md) Virtuálisgép-méretekkel kapcsolatos.
Lemez adatváltozási sebesség | 10 Mbps for a premium storage lemezenkénti maximális. Legfeljebb 2 MB/s Standard Storage lemezenkénti. | Ha az átlagos adatváltozási sebessége a a lemez folyamatosan magasabb, mint a maximális, replikálása nem történik meg.<br/><br/>  Azonban csak időnként túllépi a maximálisan engedélyezett, ha replikációs legérdekesebb részeit is, de előfordulhat, hogy kissé késleltetett helyreállítási pontok látható.
Adatlemez - standard storage-fiók | Támogatott |
Adatlemez - premium storage-fiók | Támogatott | Ha a virtuális gép lemezei prémium és standard szintű tárfiókok helyezkednek el, kiválaszthatja az egyes lemezek, ellenőrizze, hogy azonos tárolási konfigurációval a célrégióban egy másik céloldali tárfiókkal.
Felügyelt lemez – standard | Támogatott az Azure-régióban, amelyben az Azure Site Recovery támogatott. |
Felügyelt lemez – prémium szintű | Támogatott az Azure-régióban, amelyben az Azure Site Recovery támogatott. |
Standard SSD | Nem támogatott |
Redundancia | Az LRS és a GRS támogatottak.<br/><br/> A ZRS nem támogatott.
Ritkán használt adatok és a gyakori elérésű tárolási | Nem támogatott | Virtuálisgép-lemezek nem támogatják a ritka és gyakori elérésű tárolási
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
Virtuális hálózatok az Azure Storage-tűzfalak  | Támogatott | Ha a tárfiókok vannak, a virtuális hálózati hozzáférés korlátozása, ügyeljen arra, hogy ["Allow megbízható Microsoft-szolgáltatások"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Általános célú V2-tárfiókok (mindkét gyakori és ritka elérésű szint) | Nem | Tranzakciós költségek növelése jelentősen képest általános célú V1-tárfiókok

>[!IMPORTANT]
> Győződjön meg arról, hogy a virtuális gép lemez méretezhetőségi és teljesítménycéljai az erőforrásigények [Linux](../virtual-machines/linux/disk-scalability-targets.md) vagy [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuális gépek semmilyen teljesítménybeli problémák elkerülése érdekében. Ha követi az alapértelmezett beállításokat, a Site Recovery létrehozza a lemezek szükséges és a storage-fiókokat az adatforrás-konfiguráció alapján. Ha testre szabhatja, és válassza ki a saját beállításait, győződjön meg arról, hogy a forrás virtuális gépek esetében kövesse a lemez méretezhetőségi és teljesítménycéljai.

## <a name="azure-site-recovery-limits-to-replicate-data-change-rates"></a>Az adatok replikálása az Azure Site Recovery korlátai rátáról
Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. Ezek a korlátok a saját tesztjeinken alapulnak, de nem fedhetik le az alkalmazások minden lehetséges I/O-kombinációját. A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak. Azt is vegye figyelembe, hogy két korlátozva van, érdemes lehet lemezenként data churn, és egy virtuális gép adatainak adatváltozás.
Például, ha megnézzük a prémium szintű P20 lemez az alábbi táblázatban, a Site Recovery lemez az öt ilyen lemezek virtuális gépenkénti miatt a 25 MB/s összes adatforgalom virtuális gépenként legfeljebb 5 MB/s adatváltozással képes kezelni.

**Replikáció tárolási célja** | **Forráslemez átlagos I/O-mérete** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |10 MB/s | Lemezenként 842 GB
## <a name="replicated-machines---networking"></a>A replikált gépek – hálózatkezelés
**Konfigurálás** | **Támogatás** | **Részletek**
--- | --- | ---
Hálózati adapter | Egy adott Azure-beli Virtuálisgép-méretet a támogatott maximális száma | Hálózati adapter a virtuális gép létrehozásakor feladatátvétel során jönnek létre.<br/><br/> A hálózati adapterek száma a feladatátvételi virtuális Gépen függ a forrásoldali virtuális gép hálózati adaptereinek száma, amikor a replikáció engedélyezése megtörtént. Adja hozzá, vagy távolítsa el a hálózati adapter a replikáció engedélyezése után, ha a feladatátvételt követően nem érinti a replikált virtuális gép hálózati adaptereinek száma.
Internetes Load Balancer | Támogatott | Társítsa az előre konfigurált terheléselosztó egy Azure Automation-szkript használatával a helyreállítási terv.
Belső Load balancer | Támogatott | Társítsa az előre konfigurált terheléselosztó egy Azure Automation-szkript használatával a helyreállítási terv.
Nyilvános IP-cím | Támogatott | Egy meglévő nyilvános IP-címet társítani a hálózati adaptert. Vagy hozzon létre egy nyilvános IP-címet, és társíthatja azt egy Azure Automation-szkript használatával a helyreállítási tervben szereplő hálózati adapter.
A hálózati adapter NSG-t | Támogatott | Az NSG-t társítson a hálózati adapter egy Azure Automation-szkript használatával a helyreállítási terv.
Az alhálózati NSG | Támogatott | Az NSG-KET társíthat az alhálózat egy Azure Automation-szkript használatával a helyreállítási terv.
Fenntartott (statikus) IP-cím | Támogatott | Ha a forrásoldali virtuális gép hálózati adapter statikus IP-címmel rendelkezik, és a célként megadott alhálózat az azonos rendelkezésre álló IP-címmel rendelkezik, azt hozzá van rendelve, a feladatátvételen átesett virtuális gép.<br/><br/> Ha a célként megadott alhálózat nem rendelkezik elérhető azonos IP-cím, egy alhálózaton elérhető IP-címek a virtuális gép számára van fenntartva.<br/><br/> Azt is beállíthatja egy rögzített IP-cím és alhálózat **replikált elemek** > **beállítások** > **számítás és hálózat**  >  **Hálózati adapterek**.
Dinamikus IP-cím | Támogatott | Ha a forrás hálózati adapter dinamikus IP-címzés, a hálózati adapter a feladatátvételen átesett virtuális gép is alapértelmezés szerint dinamikus.<br/><br/> Ez módosítható rögzített IP-címmel, ha szükséges.
Traffic Manager     | Támogatott | A Traffic Manager előre is megadhatja, hogy a végpont a forrásrégióban rendszeres időközönként, valamint a végponti feladatátvétel esetén a célrégióban adatforgalmat.
Azure DNS | Támogatott |
Egyéni DNS  | Támogatott |
Hitelesített proxykiszolgálói | Támogatott | Tekintse meg [hálózati dokumentum.](site-recovery-azure-to-azure-networking-guidance.md)    
Hitelesített proxyt | Nem támogatott | Ha a virtuális gép egy hitelesített proxyt használ a kimenő hálózati kapcsolatot, azt nem lehet replikálni az Azure Site Recovery használatával.    
A helyszíni (vagy anélkül ExpressRoute) a helyek közötti VPN| Támogatott | Győződjön meg arról, hogy úgy, hogy a Site recovery nem adatforgalmat helyszíni az udr-EK és NSG-k vannak konfigurálva. Tekintse meg [hálózati dokumentum.](site-recovery-azure-to-azure-networking-guidance.md)  
Virtuális hálózatok közötti kapcsolat | Támogatott | Tekintse meg [hálózati dokumentum.](site-recovery-azure-to-azure-networking-guidance.md)  
Virtuális hálózati szolgáltatásvégpontok | Támogatott | Ha a tárfiókok vannak, a virtuális hálózati hozzáférés korlátozása, győződjön meg arról, hogy a megbízható Microsoft-szolgáltatások engedélyezettek-e a tárfiókhoz való hozzáférést.
Gyorsított hálózatkezelés | Támogatott | A forrásoldali virtuális gép gyorsított hálózatkezelés engedélyezni kell. [További információk](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>További lépések
- Olvasási [hálózatkezelési útmutató Azure virtuális gépek replikálása](site-recovery-azure-to-azure-networking-guidance.md).
- A vész-helyreállítási üzembe [Azure virtuális gépek replikálása](site-recovery-azure-to-azure.md).
