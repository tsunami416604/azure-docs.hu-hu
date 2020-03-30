---
title: Az SAP HANA támogatott forgatókönyvei az Azure-ban (nagy példányok)| Microsoft dokumentumok
description: Támogatott forgatókönyvek és architektúrájuk részletei az SAP HANA azure-beli (nagy példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 019f462d4264d19bcc4806d91223029a95f9d819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617182"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Hana nagy példányok támogatott forgatókönyvei
Ez a cikk ismerteti a hana nagy példányok (HLI) támogatott forgatókönyvek és architektúra részleteit.

>[!NOTE]
>Ha a szükséges forgatókönyv nem szerepel ebben a cikkben, lépjen kapcsolatba a Microsoft Service Management csapatával, hogy felmérje a követelményeket.
A HLI-egység beállítása előtt ellenőrizze a tervet az SAP-lal vagy a szolgáltatás végrehajtási partnerével.

## <a name="terms-and-definitions"></a>Feltételek és definíciók
Ismerjük a cikkben használt kifejezéseket és definíciókat:

- **SID**: A HANA rendszer rendszerazonosítója
- **HLI**: Hana nagy példányok
- **DR**: Katasztrófa utáni helyreállítás
- **Normál DR**: A rendszer beállítása egy dedikált erőforrás csak DR célokra
- **Többcélú DR:** Egy VÉSZ-hely rendszer, amely úgy van beállítva, hogy egy nem éles környezet mellett egy éles példány, amely konfigurálva van egy VÉSZ-esemény 
- **Egy-SID**: Egy olyan rendszer, amelyen egy példány van telepítve
- **Multi-SID**: Több példányt konfigurált rendszer; más néven MCOS környezet
- **HSR**: SAP HANA rendszer replikációja

## <a name="overview"></a>Áttekintés
A HANA nagy példányai számos architektúrát támogatnak, amelyek segítenek az üzleti követelmények teljesítésében. A következő szakaszok az architekturális forgatókönyveket és azok konfigurációs részleteit ismertetik. 

A származtatott architektúra-tervezés pusztán egy infrastruktúra szempontjából, és konzultálnia kell az SAP vagy a megvalósítási partnerek a HANA-telepítés. Ha a forgatókönyvek nem szerepelnek ebben a cikkben, lépjen kapcsolatba a Microsoft-fiók csapatával, hogy áttekintse az architektúrát, és megoldást találjon az Ön számára.

> [!NOTE]
> Ezek az architektúrák teljes mértékben megfelelnek a testreszabott adatintegrációs (TDI) kialakításnak, és az SAP támogatja őket.

Ez a cikk az egyes támogatott architektúra két összetevőjének részleteit ismerteti:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Minden egyes kiépített kiszolgáló előre konfigurált Ethernet-csatolókészletekkel rendelkezik. Az egyes HLI-egységeken konfigurált Ethernet-csatolók négy típusba vannak sorolva:

- **A**: Ügyfélhozzáféréshez vagy ügyfélhozzáféréshez használható.
- **B**: Csomópont-csomópont kommunikációra szolgál. Ez az összeköttetés minden kiszolgálón konfigurálva van (a kért topológiától függetlenül), de csak horizontális felskálázási forgatókönyvekhez használatos.
- **C**: Csomópont-tároló kapcsolatra szolgál.
- **D**: A csomópont-iSCSI eszközkapcsolathoz használható a STONITH beállításhoz. Ez az összeköttetés csak akkor van konfigurálva, ha hsr-beállítást kér.  

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Csomópont-csomópont között|
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | STONITH |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Csomópont-csomópont között|
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

A felületet a HLI-egységen konfigurált topológia alapján választhatja ki. A "B" kapcsolat például csomópont-csomópont kommunikációra van beállítva, ami akkor hasznos, ha kibővített topológia van konfigurálva. Ez a felület nem használható egycsomópontos, felskálázási konfigurációkhoz. A felület használatáról további információt a szükséges forgatókönyvek áttekintésével (a cikk későbbi részében). 

Szükség esetén további NIC-kártyákat is megadhat. A meglévő hálózati adapterek konfigurációja azonban *nem* módosítható.

>[!NOTE]
>Előfordulhat, hogy további felületeket talál, amelyek fizikai interfészek vagy kötések. Csak a korábban említett felületeket vegye figyelembe a használati esethez. Minden más figyelmen kívül hagyható.

A két hozzárendelt IP-címmel rendelkező egységek eloszlásának így kell kinéznie:

- Az "A" Ethernet-nek olyan hozzárendelt IP-címmel kell rendelkeznie, amely a Microsoftnak elküldött kiszolgáló IP-készletcímtartományán belül van. Ezt az IP-címet az operációs rendszer */etc/hosts* könyvtárában kell tartani.

- Ethernet "C" rendelkeznie kell egy hozzárendelt IP-címet, amely az NFS-kommunikációra szolgál. Ezt a címet *nem* kell karbantartani az *etc/hosts* könyvtárban, hogy a bérlőn belül példányok között forgalmat lehessen engedélyezni.

Hana rendszerreplikáció vagy HANA horizontális felskálázás központi telepítése esetén a két hozzárendelt IP-címmel rendelkező panelkonfiguráció nem megfelelő. Ha csak két hozzárendelt IP-címmel rendelkezik, és ilyen konfigurációt szeretne telepíteni, forduljon az SAP HANA-hoz az Azure Service Management szolgáltatáskezelés ben. Ők is hozzá egy harmadik IP-címet egy harmadik VLAN. A HANA nagy példányok három hozzárendelt IP-címet három hálózati porton, a következő használati szabályok vonatkoznak:

- Az "A" Ethernet kapcsolatának olyan IP-címmel kell rendelkeznie, amely kívül esik a Microsoftnak elküldött kiszolgáló IP-készletcímtartományán. Ezt az IP-címet nem szabad az operációs rendszer *etc/hosts* könyvtárában tartani.

- Ethernet "B" fenn kell tartani kizárólag az *etc / hosts* könyvtár közötti kommunikáció a különböző példányok. Ezek azok az IP-címek, amelyeket a kibővített HANA-konfigurációkban kell karbantartani, mint a HANA által a csomópontok közötti konfigurációhoz használt IP-címek.

- Ethernet "C" rendelkeznie kell egy hozzárendelt IP-címet, amely az NFS-tárolóval való kommunikációhoz használható. Az ilyen típusú címet nem szabad az *etc/hosts* könyvtárban tartani.

- Ethernet "D" kizárólag a hozzáférést STONITH eszközök Pacemaker. Erre az összeköttetésre akkor van szükség, ha konfigurálja a HANA rendszerreplikációt, és az operációs rendszer automatikus feladatátvételt szeretne elérni egy SBD-alapú eszközzel.


### <a name="storage"></a>Storage
A tárolás előre konfigurálva van a kért topológia alapján. A kötetméretek és a csatlakoztatási pontok a kiszolgálók számától, a sk-ek számától és a konfigurált topológiától függően változnak. További információkért tekintse át a szükséges eseteket (a cikk későbbi részében). Ha több tárhelyre van szüksége, 1 TB-os lépésekben vásárolhatja meg.

>[!NOTE]
>A /usr/sap/\<SID> csatlakoztatási pont a /hana/shared mount point szimbolikus hivatkozása.


## <a name="supported-scenarios"></a>Támogatott esetek

A következő szakaszokban található architektúradiagramok a következő feljelöléseket használják:

[![Építészeti diagramok táblázata](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

A támogatott forgatókönyvek a következők:

* Egyetlen csomópont egyetlen SID-vel
* Egycsomópontos MCOS
* Egyetlen csomópont DR-rel (normál)
* Egyetlen csomópont DR-rel (többcélú)
* HSR STONITH-tal
* HSR DR-rel (normál/többcélú) 
* Állomás automatikus feladatátvétele (1+1) 
* Horizontális felskálázás készenléti állapottal
* Horizontális felskálázás készenlét nélkül
* Horizontális felskálázás dr-rel

## <a name="single-node-with-one-sid"></a>Egyetlen csomópont egyetlen SID-vel

Ez a topológia egy csomópontot támogat egy biztonsági csoportban lévő konfigurációban egy SID-vel.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egyetlen csomópont egyetlen SID-vel](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurálva, de nincs használatban |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|/hana/shared/SID | HANA telepítés | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése | 
|/hana/logbackups/SID | Naplók ismétlése |

### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.

## <a name="single-node-mcos"></a>Egycsomópontos MCOS

Ez a topológia támogatja az egyik csomópont ot egy több biztonsági azonosítóval rendelkező skálázási konfigurációban.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egycsomópontos MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurálva, de nincs használatban |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|/hana/shared/SID1 | HANA telepítés sid1-hez | 
|/hana/data/SID1/mnt00001 | Adatfájlok telepítése a SID1-hez | 
|/hana/log/SID1/mnt00001 | Naplófájlok telepítése a SID1-hez | 
|/hana/logbackups/SID1 | A SID1 naplóinak ismétlése |
|/hana/shared/SID2 | HANA telepítés sid2-hez | 
|/hana/data/SID2/mnt00001 | Adatfájlok telepítése sid2 | 
|/hana/log/SID2/mnt00001 | Naplófájlok telepítése a SID2-hez | 
|/hana/logbackups/SID2 | A SID2 naplóinak ismétlése |

### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
- A kötetméret-terjesztés a memóriában lévő adatbázis méretén alapul. Ha meg szeretné tudni, hogy a memóriában lévő adatbázisméretek milyen fájlokat támogatnak többSID-környezetben, [olvassa el az Áttekintés és az architektúra című témakört.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

## <a name="single-node-with-dr-using-storage-replication"></a>Egyetlen csomópont a DR-rel a tárolóreplikáció használatával
 
Ez a topológia egy csomópontot támogat egy vagy több SID-vel rendelkező skálázási konfigurációban, és a vész-vész-vész-biztonsági kiszolgáló tárolóalapú replikációját támogatja a VÉSZ-tároló helyre. Az ábrán csak egy-SID rendszer látható az elsődleges helyen, de az MCOS rendszerek is támogatottak.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egyetlen csomópont a DR-rel a tárolóreplikáció használatával](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurálva, de nincs használatban |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|/hana/shared/SID | HANA telepítés sid-hez | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése sid-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése sid-hez | 
|/hana/logbackups/SID | A SID naplóinak ismétlése |


### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
- MCOS esetén: A kötetméret-terjesztés a memóriában lévő adatbázis méretén alapul. Ha meg szeretné tudni, hogy a memóriában lévő adatbázisméretek milyen fájlokat támogatnak többSID-környezetben, [olvassa el az Áttekintés és az architektúra című témakört.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- A DR-helyen: A kötetek és a csatlakoztatási pontok konfigurálva vannak (a HANA telepítéséhez szükségesként vannak megjelölve) a DR HLI-egységben az éles HANA-példány telepítéséhez. 
- A VÉSZ-hely: Az adatok, napló biztonsági mentések és a megosztott kötetek ("Tároló replikáció") replikálódik pillanatkép a termelési helyről. Ezek a kötetek csak feladatátvétel esetén vannak csatlakoztatva. További információ: [Vész-helyreállítási feladatátvételi eljárás.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- Az *I. típusú termékváltozat rendszerindító kötete* replikálódik a DR-csomópontra.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Egyetlen csomópont DR-rel (többcélú) tárolóreplikációval
 
Ez a topológia egy csomópontot támogat egy vagy több SID-vel rendelkező skálázási konfigurációban, és a vész-vész-vész-biztonsági kiszolgáló tárolóalapú replikációját támogatja a VÉSZ-tároló helyre. Az ábrán csak egy-SID rendszer látható az elsődleges helyen, de a multi-SID (MCOS) rendszerek is támogatottak. A VÉSZ-helyen a HLI-egység a minőségbiztosítási példányhoz használatos, miközben az éles műveletek az elsődleges helyről futnak. A VÉSZ-feladatátvétel (vagy feladatátvételi teszt) során a vész-vész-és a dr-hely a minőségbiztosítási példány t.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egyetlen csomópont DR-rel (többcélú) tárolóreplikációval](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurálva, de nincs használatban |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |
|**A DR oldalon**|
|/hana/shared/SID | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/shared/QA-SID | HANA telepítés a minőségbiztosítási hibajogosultjához | 
|/hana/data/QA-SID/mnt00001 | Adatfájlok telepítése a minőségbiztosítási hibajogosultjához | 
|/hana/log/QA-SID/mnt00001 | Naplófájlok telepítése a minőségbiztosítási hibajogosulthoz |
|/hana/logbackups/QA-SID | A minőségbiztosítási azonosító naplóinak ismétlése |

### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
- MCOS esetén: A kötetméret-terjesztés a memóriában lévő adatbázis méretén alapul. Ha meg szeretné tudni, hogy a memóriában lévő adatbázisméretek milyen fájlokat támogatnak többSID-környezetben, [olvassa el az Áttekintés és az architektúra című témakört.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- A DR-helyen: A kötetek és a csatlakoztatási pontok konfigurálva vannak (a HANA telepítéséhez szükségesként vannak megjelölve) a DR HLI-egységben az éles HANA-példány telepítéséhez. 
- A VÉSZ-hely: Az adatok, napló biztonsági mentések és a megosztott kötetek ("Tároló replikáció") replikálódik pillanatkép a termelési helyről. Ezek a kötetek csak feladatátvétel esetén vannak csatlakoztatva. További információ: [Vész-helyreállítási feladatátvételi eljárás.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 
- A VÉSZ-hely: Az adatok, napló mentések, napló és megosztott kötetek a qa (jelölt "QA példány telepítése") vannak konfigurálva a minőségbiztosítási példány telepítése.
- Az *I. típusú termékváltozat rendszerindító kötete* replikálódik a DR-csomópontra.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR STONITH-tal a magas rendelkezésre állás érdekében
 
Ez a topológia két csomópontot támogat a HANA rendszerreplikációs konfigurációhoz. Ez a konfiguráció csak egy HANA-példányok egy csomóponton támogatott. Ez azt jelenti, hogy az MCOS-forgatókönyvek *nem* támogatottak.

> [!NOTE]
> 2019 decemberétől ez az architektúra csak a SUSE operációs rendszer számára támogatott.


### <a name="architecture-diagram"></a>Architektúradiagram  

![HSR STONITH-tal a magas rendelkezésre állás érdekében](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Használt STONITH |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurálva, de nincs használatban |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Használt STONITH |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton**|
|/hana/shared/SID | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |
|**A másodlagos csomóponton**|
|/hana/shared/SID | HANA telepítés másodlagos biztonsági kiszolgálóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése másodlagos biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése másodlagos biztonsági azonosítóhoz | 
|/hana/logbackups/SID | A másodlagos biztonsági azonosító naplóinak ismétlése |

### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
- MCOS esetén: A kötetméret-terjesztés a memóriában lévő adatbázis méretén alapul. Ha meg szeretné tudni, hogy a memóriában lévő adatbázisméretek milyen fájlokat támogatnak többSID-környezetben, [olvassa el az Áttekintés és az architektúra című témakört.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- STONITH: Az SBD a STONITH beállításához van konfigurálva. A STONITH használata azonban nem kötelező.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Magas rendelkezésre állás hsr és DR tároló replikációval
 
Ez a topológia két csomópontot támogat a HANA rendszerreplikációs konfigurációhoz. Mind a normál, mind a többcélú DR támogatott. Ezek a konfigurációk csak egy HANA-példányok egy csomóponton támogatottak. Ez azt jelenti, hogy az MCOS-forgatókönyvek *nem* támogatottak ezekkel a konfigurációkkal.

Az ábrán egy többcélú forgatókönyv látható a DR helyen, ahol a HLI-egység a minőségbiztosítási példányhoz használatos, miközben a termelési műveletek az elsődleges helyről futnak. A VÉSZ-feladatátvétel (vagy feladatátvételi teszt) során a vész-vész-és a dr-hely a minőségbiztosítási példány t. 

### <a name="architecture-diagram"></a>Architektúradiagram  

![Magas rendelkezésre állás hsr és DR tároló replikációval](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Használt STONITH |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurálva, de nincs használatban |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Használt STONITH |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton az elsődleges helyen**|
|/hana/shared/SID | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |
|**Az elsődleges hely másodlagos csomópontján**|
|/hana/shared/SID | HANA telepítés másodlagos biztonsági kiszolgálóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése másodlagos biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése másodlagos biztonsági azonosítóhoz | 
|/hana/logbackups/SID | A másodlagos biztonsági azonosító naplóinak ismétlése |
|**A DR oldalon**|
|/hana/shared/SID | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/shared/QA-SID | HANA telepítés a minőségbiztosítási hibajogosultjához | 
|/hana/data/QA-SID/mnt00001 | Adatfájlok telepítése a minőségbiztosítási hibajogosultjához | 
|/hana/log/QA-SID/mnt00001 | Naplófájlok telepítése a minőségbiztosítási hibajogosulthoz |
|/hana/logbackups/QA-SID | A minőségbiztosítási azonosító naplóinak ismétlése |

### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
- MCOS esetén: A kötetméret-terjesztés a memóriában lévő adatbázis méretén alapul. Ha meg szeretné tudni, hogy a memóriában lévő adatbázisméretek milyen fájlokat támogatnak többSID-környezetben, [olvassa el az Áttekintés és az architektúra című témakört.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- STONITH: Az SBD a STONITH beállításához van konfigurálva. A STONITH használata azonban nem kötelező.
- A VÉSZ-helyen: *Két tárolókötet-készlet szükséges az* elsődleges és másodlagos csomópont replikációjához.
- A DR-helyen: A kötetek és a csatlakoztatási pontok konfigurálva vannak (a HANA telepítéséhez szükségesként vannak megjelölve) a DR HLI-egységben az éles HANA-példány telepítéséhez. 
- A VÉSZ-hely: Az adatok, napló biztonsági mentések és a megosztott kötetek ("Tároló replikáció") replikálódik pillanatkép a termelési helyről. Ezek a kötetek csak feladatátvétel esetén vannak csatlakoztatva. További információ: [Vész-helyreállítási feladatátvételi eljárás.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 
- A VÉSZ-hely: Az adatok, napló mentések, napló és megosztott kötetek a qa (jelölt "QA példány telepítése") vannak konfigurálva a minőségbiztosítási példány telepítése.
- Az *I. típusú termékváltozat rendszerindító kötete* replikálódik a DR-csomópontra.


## <a name="host-auto-failover-11"></a>Állomás automatikus feladatátvétele (1+1)
 
Ez a topológia két csomópontot támogat egy állomás automatikus feladatátvételi konfigurációjában. Van egy fő/feldolgozó szerepkörrel rendelkező csomópont, egy másik pedig készenléti állapot. *Az SAP csak az S/4 HANA esetén támogatja ezt a forgatókönyvet.* További információ: [OSS note 2408419 - SAP S/4HANA - Multi-Node Support](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Architektúradiagram  

![Állomás automatikus feladatátvétele (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Csomópont-csomópont kommunikáció |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Csomópont-csomópont kommunikáció |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|**A fő- és készenléti csomópontokon**|
|/hana/megosztott | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |



### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
- Készenléti állapotban: A kötetek és a csatlakoztatási pontok konfigurálva vannak (a HANA-telepítéshez szükségesként vannak megjelölve) a hana-példány telepítéséhez a készenléti egységben.
 

## <a name="scale-out-with-standby"></a>Horizontális felskálázás készenléti állapottal
 
Ez a topológia több csomópontot támogat egy kibővített konfigurációban. Van egy főszerepkörrel rendelkező csomópont, egy vagy több feldolgozói szerepkörrel rendelkező csomópont, és egy vagy több csomópont készenléti állapotban. Azonban csak egy főcsomópont lehet egyetlen időpontban.


### <a name="architecture-diagram"></a>Architektúradiagram  

![Horizontális felskálázás készenléti állapottal](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Csomópont-csomópont kommunikáció |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Csomópont-csomópont kommunikáció |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|**A fő, a feldolgozó és a készenléti csomópontokon**|
|/hana/megosztott | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |


## <a name="scale-out-without-standby"></a>Horizontális felskálázás készenlét nélkül
 
Ez a topológia több csomópontot támogat egy kibővített konfigurációban. Van egy főszerepkörrel rendelkező csomópont, és egy vagy több feldolgozói szerepkörrel rendelkező csomópont. Azonban csak egy főcsomópont lehet egyetlen időpontban.


### <a name="architecture-diagram"></a>Architektúradiagram  

![Horizontális felskálázás készenlét nélkül](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Csomópont-csomópont kommunikáció |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Csomópont-csomópont kommunikáció |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|**A fő- és a feldolgozócsomóponton**|
|/hana/megosztott | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |


### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.

## <a name="scale-out-with-dr-using-storage-replication"></a>Horizontális felskálázás a DR-rel a tárolóreplikáció használatával
 
Ez a topológia támogatja a dr-rel rendelkező horizontális felskálázás több csomópontját. Mind a normál, mind a többcélú DR támogatott. Az ábrán csak az egyetlen célú DR látható. Ezt a topológiát a készenléti csomón nal vagy anélkül is kérheti.


### <a name="architecture-diagram"></a>Architektúradiagram  

![Horizontális felskálázás a DR-rel a tárolóreplikáció használatával](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Csomópont-csomópont kommunikáció |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Csomópont-csomópont kommunikáció |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton**|
|/hana/megosztott | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |
|**A DR-csomóponton**|
|/hana/megosztott | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 


### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
-  A DR-helyen: A kötetek és a csatlakoztatási pontok konfigurálva vannak (a HANA telepítéséhez szükségesként vannak megjelölve) a DR HLI-egységben az éles HANA-példány telepítéséhez. 
- A VÉSZ-hely: Az adatok, napló biztonsági mentések és a megosztott kötetek ("Tároló replikáció") replikálódik pillanatkép a termelési helyről. Ezek a kötetek csak feladatátvétel esetén vannak csatlakoztatva. További információ: [Vész-helyreállítási feladatátvételi eljárás.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) 
- Az *I. típusú termékváltozat rendszerindító kötete* replikálódik a DR-csomópontra.


## <a name="single-node-with-dr-using-hsr"></a>Egyetlen csomópont DR-rel hsr használatával
 
Ez a topológia egy csomópontot támogat egy biztonsági másolattal rendelkező skálázási konfigurációban, a HANA-rendszer replikációjával a VÉSZ-HELYRE egy elsődleges biztonsági azonosító esetén. Az ábrán csak egy-SID rendszer látható az elsődleges helyen, de a multi-SID (MCOS) rendszerek is támogatottak.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egyetlen csomópont DR-rel hsr használatával](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI/HSR |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI/HSR |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurálva, de nincs használatban |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva mind a HLI egységek (Elsődleges és DR):

| Rögzítési pont | Használati eset | 
| --- | --- |
|/hana/shared/SID | HANA telepítés sid-hez | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése sid-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése sid-hez | 
|/hana/logbackups/SID | A SID naplóinak ismétlése |


### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
- MCOS esetén: A kötetméret-terjesztés a memóriában lévő adatbázis méretén alapul. Ha meg szeretné tudni, hogy a memóriában lévő adatbázisméretek milyen fájlokat támogatnak többSID-környezetben, [olvassa el az Áttekintés és az architektúra című témakört.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- Az elsődleges csomópont a HANA rendszerreplikáció használatával szinkronizálja a VÉSZ-csomópontot. 
- [A Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) az ExpressRoute-áramkörök összekapcsolására szolgál, hogy magánhálózatot hozlétre a regionális hálózatok között.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>Egycsomópontos HSR–DR (költségoptimalizált) 
 
 Ez a topológia egy csomópontot támogat egy biztonsági másolattal rendelkező skálázási konfigurációban, a HANA-rendszer replikációjával a VÉSZ-HELYRE egy elsődleges biztonsági azonosító esetén. Az ábrán csak egy-SID rendszer látható az elsődleges helyen, de a multi-SID (MCOS) rendszerek is támogatottak. A VÉSZ-helyen egy HLI-egységet használnak a minőségbiztosítási példányhoz, miközben az éles műveletek az elsődleges helyről futnak. A VÉSZ-feladatátvétel (vagy feladatátvételi teszt) során a vész-vész-és a dr-hely a minőségbiztosítási példány t.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egycsomópontos HSR–DR (költségoptimalizált)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI/HSR |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI/HSR |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurálva, de nincs használatban |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |
|**A DR oldalon**|
|/hana/shared/SID | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |
|/hana/shared/QA-SID | HANA telepítés a minőségbiztosítási hibajogosultjához | 
|/hana/data/QA-SID/mnt00001 | Adatfájlok telepítése a minőségbiztosítási hibajogosultjához | 
|/hana/log/QA-SID/mnt00001 | Naplófájlok telepítése a minőségbiztosítási hibajogosulthoz |
|/hana/logbackups/QA-SID | A minőségbiztosítási azonosító naplóinak ismétlése |

### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
- MCOS esetén: A kötetméret-terjesztés a memóriában lévő adatbázis méretén alapul. Ha meg szeretné tudni, hogy a memóriában lévő adatbázisméretek milyen fájlokat támogatnak többSID-környezetben, [olvassa el az Áttekintés és az architektúra című témakört.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- A DR-helyen: A kötetek és a csatlakoztatási pontok konfigurálva vannak ("PROD példány a DR helyen") az éles HANA-példány telepítéséhez a DR HLI egységben. 
- A VÉSZ-hely: Az adatok, napló mentések, napló és megosztott kötetek a qa (jelölt "QA példány telepítése") vannak konfigurálva a minőségbiztosítási példány telepítése.
- Az elsődleges csomópont a HANA rendszerreplikáció használatával szinkronizálja a VÉSZ-csomópontot. 
- [A Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) az ExpressRoute-áramkörök összekapcsolására szolgál, hogy magánhálózatot hozlétre a regionális hálózatok között.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Magas rendelkezésre állás és vészhelyreállítás a HSR-rel 
 
 Ez a topológia két csomópontot támogat a HANA rendszerreplikációs konfigurációhoz a helyi régiók magas rendelkezésre állásához. A DR esetében a VÉSZ-régió harmadik csomópontja hsr (aszinkron mód) használatával szinkronizálja az elsődleges helyet. 

### <a name="architecture-diagram"></a>Architektúradiagram  

![Magas rendelkezésre állás és vészhelyreállítás a HSR-rel](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI/HSR |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI/HSR |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurálva, de nincs használatban |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |
|**A DR oldalon**|
|/hana/shared/SID | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |


### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
- A DR-helyen: A kötetek és a csatlakoztatási pontok konfigurálva vannak ("PROD DR példányként" megjelölve) az éles HANA-példány telepítéséhez a DR HLI egységben. 
- Az elsődleges helycsomópont a HANA rendszerreplikáció használatával szinkronizálódik a VÉSZ-csomóponttal. 
- [A Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) az ExpressRoute-áramkörök összekapcsolására szolgál, hogy magánhálózatot hozlétre a regionális hálózatok között.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Magas rendelkezésre állás és vészhelyreállítás a HSR-rel (költségoptimalizált)
 
 Ez a topológia két csomópontot támogat a HANA rendszerreplikációs konfigurációhoz a helyi régiók magas rendelkezésre állásához. A DR esetében a VÉSZ-régió harmadik csomópontja szinkronizálja az elsődleges helyet a HSR (async mód) használatával, míg egy másik példány (például minőségbiztosítási) már fut ki a DR-csomópontból. 

### <a name="architecture-diagram"></a>Architektúradiagram  

![Magas rendelkezésre állás és vészhelyreállítás a HSR-rel (költségoptimalizált)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI/HSR |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI/HSR |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Konfigurálva, de nincs használatban |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |
|**A DR oldalon**|
|/hana/shared/SID | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |
|/hana/shared/QA-SID | HANA telepítés a minőségbiztosítási hibajogosultjához | 
|/hana/data/QA-SID/mnt00001 | Adatfájlok telepítése a minőségbiztosítási hibajogosultjához | 
|/hana/log/QA-SID/mnt00001 | Naplófájlok telepítése a minőségbiztosítási hibajogosulthoz |
|/hana/logbackups/QA-SID | A minőségbiztosítási azonosító naplóinak ismétlése |

### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
- A DR-helyen: A kötetek és a csatlakoztatási pontok konfigurálva vannak ("PROD DR példányként" megjelölve) az éles HANA-példány telepítéséhez a DR HLI egységben. 
- A VÉSZ-hely: Az adatok, napló mentések, napló és megosztott kötetek a qa (jelölt "QA példány telepítése") vannak konfigurálva a minőségbiztosítási példány telepítése.
- Az elsődleges helycsomópont a HANA rendszerreplikáció használatával szinkronizálódik a VÉSZ-csomóponttal. 
- [A Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) az ExpressRoute-áramkörök összekapcsolására szolgál, hogy magánhálózatot hozlétre a regionális hálózatok között.

## <a name="scale-out-with-dr-using-hsr"></a>Horizontális felskálázás dr-rel hsr használatával
 
Ez a topológia támogatja a dr-rel rendelkező horizontális felskálázás több csomópontját. Ezt a topológiát a készenléti csomón nal vagy anélkül is kérheti. Az elsődleges helycsomópont a HANA rendszerreplikáció (aszinkron mód) használatával szinkronizálja a VÉSZ-hely csomópontját.


### <a name="architecture-diagram"></a>Architektúradiagram  

[![Horizontális felskálázás dr-rel hsr használatával](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek vannak előre konfigurálva:

| Hálózati adapter logikai kapcsolata | Termékváltozat típusa | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUSÚ | eth0.tenant | eno1.tenant | Ügyfél-HLI/HSR |
| B | I. TÍPUSÚ | eth2.tenant | eno3.tenant | Csomópont-csomópont kommunikáció |
| C# | I. TÍPUSÚ | eth1.tenant | eno2.tenant | Csomópont-tároló |
| D | I. TÍPUSÚ | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. | vlan\<tenantNo> | team0.tenant | Ügyfél-HLI/HSR |
| B | II. | vlan\<tenantNo+2> | team0.tenant+2 | Csomópont-csomópont kommunikáció |
| C# | II. | vlan\<tenantNo+1> | team0.tenant+1 | Csomópont-tároló |
| D | II. | vlan\<tenantNo+3> | team0.tenant+3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakoztatási pontok vannak előre konfigurálva:

| Rögzítési pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton**|
|/hana/megosztott | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |
|**A DR-csomóponton**|
|/hana/megosztott | HANA telepítés éles biztonsági azonosítóhoz | 
|/hana/data/SID/mnt00001 | Adatfájlok telepítése az éles biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése az éles biztonsági lemezazonosítóhoz | 
|/hana/logbackups/SID | Az éles biztonsági azonosító naplóinak ismétlése |


### <a name="key-considerations"></a>Fő szempontok
- A /usr/sap/SID a /hana/shared/SID szimbolikus hivatkozás.
- A DR-hely: A kötetek és a csatlakoztatási pontok vannak konfigurálva az éles HANA példány telepítése a DR HLI egység. 
- Az elsődleges helycsomópont a HANA rendszerreplikáció használatával szinkronizálódik a VÉSZ-csomóponttal. 
- [A Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) az ExpressRoute-áramkörök összekapcsolására szolgál, hogy magánhálózatot hozlétre a regionális hálózatok között.


## <a name="next-steps"></a>További lépések
- [Infrastruktúra és kapcsolat](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) hana nagy példányok
- [Magas rendelkezésre állás és vészhelyreállítás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) hana nagy példányok
