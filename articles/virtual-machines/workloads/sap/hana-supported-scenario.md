---
title: Támogatott forgatókönyveket az SAP HANA az Azure-ban (nagyméretű példányok) |} A Microsoft Docs
description: Támogatott forgatókönyveket és architektúra adataikat, az SAP Hana az Azure-ban (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0e9d57c224150454677a03462368038ed8c63edf
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576493"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Nagyméretű HANA-példányokhoz tartozó támogatott esetek
Ez a dokumentum ismerteti a támogatott forgatókönyveket az architektúra részleteivel együtt a HANA nagyméretű példányok (HLI) számára.

>[!NOTE]
>Ha az adott helyzetben itt nem említett, forduljon a Microsoft Service Management csapata a követelmények felmérése.
A kiépítés HLI egység folytatása előtt ellenőrizze a tervezés, az SAP vagy megvalósítási partnere szolgáltatás.

## <a name="terms-and-definitions"></a>Kifejezések és meghatározások
Tekintsük át a feltételeket és a dokumentumban használt definíciókat.

- SID: HANA rendszer rendszer azonosítója.
- HLI: Nagyméretű Hana-példányokhoz.
- DR: Egy vész-helyreállítási helyként.
- Normál DR: A rendszer a telepítés csak használt Vészhelyreállítási célból egy dedikált erőforrásokkal.
- Multipurpose DR: A rendszer nem éles környezetben együtt használja való Vészhelyreállítás esemény éles üzemelő példányok használatára konfigurált DR-helyen. 
- Egyetlen biztonsági azonosító: A rendszer, ha telepítve van egy példánya.
- Több SID: A rendszer több példányával konfigurálva. Más néven MCOS környezet.


## <a name="overview"></a>Áttekintés
A HANA nagyméretű példányok támogatják a különböző architektúrák az üzleti követelményeinek megvalósításához. Az alábbi lista ismerteti a forgatókönyvek és azok konfigurációs adatai. 

A származtatott architektúra pusztán az infrastruktúra szempontjából, és kell tekintse meg az SAP vagy a megvalósítás partnerek a HANA üzembe helyezéshez. Ha a forgatókönyvek nem jelennek meg, forduljon a áttekinteni az architektúrát és a megoldás nyer, a Microsoft-fiókok ügyfélszolgálatához.

**Architektúrák teljes mértékben megfelelő TDI (szabott Adatintegráció) kialakítását, és az SAP által támogatott.**

Ez a dokumentum ismerteti az egyes támogatott architektúrákhoz a két összetevő részletei:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Minden üzembe helyezett kiszolgáló előre konfigurálva, az ethernet-illesztőkhöz részhalmazához származnak. Az alábbiakban HLI egységenként konfigurált ethernet-adapterek.

- **A**: Ez az interfész által az ügyfél-hozzáférési/használja.
- **B**: Ez az interfész a csomópontok kommunikációra lesz használatos. Ez az interfész az összes kiszolgálón (függetlenül a kért topológia) konfigurálva van, de csak használja a 
- felskálázási forgatókönyveket.
- **C**: Ez az interfész a csomópont a tárolási kapcsolat szolgál.
- **D**: Ez az interfész a csomópont iSCSI-eszköz kapcsolat STONITH telepítő szolgál. Ez az interfész csak van konfigurálva, a HSR-telepítő igénylésekor.  

| HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZATÁNAK TÍPUSA | A SUSE operációs rendszer neve | Az RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUS | eth0.tenant | eno1.tenant | Ügyfél HLI |
| B | I. TÍPUS | eth2.tenant | eno3.tenant | A csomópontot a csomópont |
| C | I. TÍPUS | eth1.tenant | eno2.tenant | A storage csomópont |
| D | I. TÍPUS | eth4.tenant | eno4.tenant | STONITH |
| A | II. TÍPUSÚ | VLAN<tenantNo> | team0.tenant | Ügyfél HLI |
| B | II. TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | A csomópontot a csomópont |
| C | II. TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | A storage csomópont |
| D | II. TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | STONITH |

Használhatja a felületek, a konfigurált a HLI egységen topológia alapján. Ha például a "B" felületen van beállítva csomópontok közötti kommunikáció, amely akkor hasznos, ha egy kibővített topológia konfigurálva van. Egyetlen csomópont vertikális felskálázás konfigurálása esetén ez az interfész nem használatos. A felület használatával kapcsolatos további információért tekintse át a szükséges forgatókönyveket (a dokumentum későbbi). 

Szükség esetén további hálózati kártyák megadhatja a saját maga. Azonban a meglévő hálózati adaptereket a konfiguráció nem módosítható.

>[!NOTE]
>További felületek, amelyek fizikai adaptereket, vagy ragasztás továbbra is tapasztalhatja. Vegye figyelembe a fent említett felületek az használt esetekhez, rest figyelmen kívül hagyható vagy vagy nem rendelkező mérsékelni kell.

A két IP-címek hozzárendelve egység terjesztési hasonlóan kell kinéznie:

- Ethernet "A" kiosztott IP-cím, amely kívül esik a kiszolgáló IP-készlet címtartománya a Microsoftnak elküldött kell rendelkeznie. Az IP-címet kell használható az operációs rendszer Hosts kezelésével.

- "C" Ethernet kiosztott IP-cím az NFS-kommunikációhoz használt kell rendelkeznie. Ezért ezek a címek tegye **nem** annak érdekében, hogy a példány a példány forgalmat a bérlőn belül a etc/hosts karban kell tartani.

HANA-Rendszerreplikálást vagy kibővített HANA üzembe helyezési esetben egy blade-konfigurációt a két rendelt IP-címek nem alkalmas. Ha két IP-címek csak a hozzárendelt kapcsolatban, és ilyen konfiguráció üzembe helyezése, forduljon az SAP HANA az Azure Service Management egy harmadik IP-cím beolvasása a harmadik, aki a hozzárendelt VLAN. A nagyméretű HANA-példány egység három három NIC-portot a hozzárendelt IP-címeket kellene a következő használati szabályok érvényesek:

- Ethernet "A" kiosztott IP-cím, amely kívül esik a kiszolgáló IP-készlet címtartománya a Microsoftnak elküldött kell rendelkeznie. Ezért az IP-címet kell nem használható az operációs rendszer Hosts kezelésével.

- "B" Ethernet kizárólag használandó stb /-gazdagépek számára a különböző példányok közötti kommunikáció tarthatók. Ezek a címek is használhatók az IP-címek, amelyek az IP-címek HANA használ a csomópontok közötti konfiguráció a horizontális felskálázás HANA konfigurációkban karban kell tartani.

- "C" Ethernet kiosztott IP-cím az NFS-tár és közötti kommunikációhoz használt kell rendelkeznie. Ezért az ilyen típusú címek nem fenn kell tartani a etc/hosts.

- Ethernet "D" hozzáférés STONITH eszköz támasztja kizárólag használandó. Ez az interfész megadása kötelező konfigurálásakor a HANA System replikációs (HSR), és szeretné az operációs rendszer SBD alapú eszközt használ, az Automatikus feladatátvétel érdekében.


### <a name="storage"></a>Storage
Storage a kért topológia alapján előre konfigurált. A kiszolgálók, az SKU-k és a konfigurált topológia számát a kötet mérete és a csatlakoztatási pont megoldástól. További információért tekintse át a szükséges forgatókönyveket (a dokumentum későbbi). Ha további tárhelyre szükség, a több TB-os növekmény is vásárolhat.

>[!NOTE]
>A csatlakoztatási pont usr/sap/ <SID> /hana/megosztott csatlakoztatási pont mutató szimbolikus hivatkozás.


## <a name="supported-scenarios"></a>Támogatott helyzetek

Az architektúra-diagramok a következő jelölések használják a grafikus elemek:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

Az alábbi lista tartalmazza a támogatott forgatókönyveket:

1. Egy SID-jű egyetlen csomópont
2. Egyetlen csomópont MCOS
3. A DR (normál) egyetlen csomópont
4. A DR (Multipurpose) egyetlen csomópont
5. A STONITH HSR
6. A DR HSR (normál / Multipurpose) 
7. Gazdagép automatikus feladatátvétel (1 + 1) 
8. Horizontális felskálázás való készenlét
9. Horizontális felskálázás készenléti nélkül
10. Horizontális felskálázás a Vészhelyreállítás



## <a name="1-single-node-with-one-sid"></a>1. Egy SID-jű egyetlen csomópont

Ez a topológia egy csomópontot egy méretezési csoportban egy SID-jű konfiguráció támogatja.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Single-csomópont-az-egy-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre:

| HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZATÁNAK TÍPUSA | A SUSE operációs rendszer neve | Az RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUS | eth0.tenant | eno1.tenant | Ügyfél HLI |
| B | I. TÍPUS | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C | I. TÍPUS | eth1.tenant | eno2.tenant | A storage csomópont |
| D | I. TÍPUS | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. TÍPUSÚ | VLAN<tenantNo> | team0.tenant | Ügyfél HLI |
| B | II. TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | A storage csomópont |
| D | II. TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakozási pontok le előre:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|/Hana/Shared/SID | HANA telepítése | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése | 
|/Hana/logbackups/SID | Ismételje meg a naplók |

### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID mutató szimbolikus hivatkozást.

## <a name="2-single-node-mcos"></a>2. Egyetlen csomópont MCOS

Ez a topológia több SID-konfiguráció egy méretezési csoportban egy csomópont támogatja.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Single-csomópont-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre:

| HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZATÁNAK TÍPUSA | A SUSE operációs rendszer neve | Az RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUS | eth0.tenant | eno1.tenant | Ügyfél HLI |
| B | I. TÍPUS | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C | I. TÍPUS | eth1.tenant | eno2.tenant | A storage csomópont |
| D | I. TÍPUS | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. TÍPUSÚ | VLAN<tenantNo> | team0.tenant | Ügyfél HLI |
| B | II. TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | A storage csomópont |
| D | II. TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakozási pontok le előre:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|/Hana/Shared/SID1 | SID1 HANA telepítése | 
|/Hana/Data/SID1/mnt00001 | Adatfájlok SID1 telepítése | 
|/Hana/log/SID1/mnt00001 | Naplófájlok SID1 telepítése | 
|/Hana/logbackups/SID1 | Naplók SID1 megismétlése |
|/Hana/Shared/SID2 | SID2 HANA telepítése | 
|/Hana/Data/SID2/mnt00001 | Adatfájlok SID2 telepítése | 
|/Hana/log/SID2/mnt00001 | Naplófájlok SID2 telepítése | 
|/Hana/logbackups/SID2 | Naplók SID2 megismétlése |

### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID mutató szimbolikus hivatkozást.
- Az adatbázis mérete, a memória mennyiségi eloszlás alapul. Tekintse meg a [áttekintése és architektúrája](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) részből megtudhatja, milyen adatbázist a memóriában méretezi multisid környezet használata támogatott.

## <a name="3-single-node-with-dr-normal"></a>3. A DR (normál) egyetlen csomópont
 
Ez a topológia egy csomópontja vertikális felskálázási egy vagy több biztonsági azonosítókkal való létrehozásához és a DR-helyre, egy elsődleges biztonsági azonosítója a storage-alapú replikálás konfigurációs támogatja. Az ábrán csak egyetlen SID megfelelőként jelenik az elsődleges helyen, de multisid (MCOS) használata is támogatott.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Single-csomópont-az-dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre:

| HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZATÁNAK TÍPUSA | A SUSE operációs rendszer neve | Az RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUS | eth0.tenant | eno1.tenant | Ügyfél HLI |
| B | I. TÍPUS | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C | I. TÍPUS | eth1.tenant | eno2.tenant | A storage csomópont |
| D | I. TÍPUS | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. TÍPUSÚ | VLAN<tenantNo> | team0.tenant | Ügyfél HLI |
| B | II. TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | A storage csomópont |
| D | II. TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakozási pontok le előre:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|/Hana/Shared/SID | Biztonsági azonosító HANA telepítése | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése a következő biztonsági AZONOSÍTÓHOZ | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése a következő biztonsági AZONOSÍTÓHOZ | 
|/Hana/logbackups/SID | Naplók SID megismétlése |


### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID mutató szimbolikus hivatkozást.
- MCOS: a kötet mérete terjesztési adatbázis méretét a memóriában alapul. Tekintse meg a [áttekintése és architektúrája](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) részből megtudhatja, milyen adatbázist a memóriában méretezi multisid környezet használata támogatott.
- : A DR: A kötetek és a csatlakozási pontok le vannak konfigurálva, (megjelölve "HANA telepítéshez szükséges") az éles környezetben a DR HLI egység HANA-példány telepítését. 
- : A DR: az adatok, logbackups és megosztott kötetek ("Tárreplikáció" megjelölve) lesznek replikálva az élesben használt helyet a pillanatkép-n keresztül. Ezek a kötetek csatlakoztatva vannak, csak a feladatátvételi idő alatt. További információkért olvassa el a dokumentumot [vészhelyreállítási feladatátvétel eljárást](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) további részletekért.
- Rendszerindító kötet a **I osztály Termékváltozatának típusa** DR csomópont replikálja.


## <a name="4-single-node-with-dr-multipurpose"></a>4. A DR (Multipurpose) egyetlen csomópont
 
Ez a topológia egy csomópontja vertikális felskálázási egy vagy több biztonsági azonosítókkal való létrehozásához és a DR-helyre, egy elsődleges biztonsági azonosítója a storage-alapú replikálás konfigurációs támogatja. Az ábrán csak egyetlen SID megfelelőként jelenik az elsődleges helyen, de multisid (MCOS) használata is támogatott. A DR helyen HLI egység használható QA példány éles műveletek az elsődleges helyről működése közben. A DR feladatátvételének (vagy a feladatátvételi teszt) időpontjában alkotásait QA példány DR helyen.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Single-csomópont-az-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre:

| HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZATÁNAK TÍPUSA | A SUSE operációs rendszer neve | Az RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUS | eth0.tenant | eno1.tenant | Ügyfél HLI |
| B | I. TÍPUS | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C | I. TÍPUS | eth1.tenant | eno2.tenant | A storage csomópont |
| D | I. TÍPUS | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. TÍPUSÚ | VLAN<tenantNo> | team0.tenant | Ügyfél HLI |
| B | II. TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | A storage csomópont |
| D | II. TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakozási pontok le előre:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/Hana/Shared/SID | HANA telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/logbackups/SID | Éles környezetben SID naplók visszaállítása |
|**A DR helyen**|
|/Hana/Shared/SID | HANA telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Shared/QA-SID | QA SID HANA telepítése | 
|/Hana/Data/QA-SID/mnt00001 | Az adatfájlokat a QA SID telepítése | 
|/Hana/log/QA-SID/mnt00001 | Naplófájlok a QA SID telepítése |
|/Hana/logbackups/QA-SID | QA SID naplók megismétlése |

### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID mutató szimbolikus hivatkozást.
- MCOS: a kötet mérete terjesztési adatbázis méretét a memóriában alapul. Tekintse meg a [áttekintése és architektúrája](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) részből megtudhatja, milyen adatbázist a memóriában méretezi multisid környezet használata támogatott.
- : A DR: A kötetek és a csatlakozási pontok le vannak konfigurálva, (megjelölve "HANA telepítéshez szükséges") az éles környezetben a DR HLI egység HANA-példány telepítését. 
- : A DR: az adatok, logbackups és megosztott kötetek ("Tárreplikáció" megjelölve) lesznek replikálva az élesben használt helyet a pillanatkép-n keresztül. Ezek a kötetek csatlakoztatva vannak, csak a feladatátvételi idő alatt. További információkért olvassa el a dokumentumot [vészhelyreállítási feladatátvétel eljárást](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) további részletekért. 
- : A DR: az adatok, logbackups, log, QA (a "QA-példány telepítése" megjelölve) megosztott kötetek vannak konfigurálva a QA példány telepítéséhez.
- Rendszerindító kötet a **I osztály Termékváltozatának típusa** DR csomópont replikálja.

## <a name="5-hsr-with-stonith"></a>5. A STONITH HSR
 
Ez a topológia két csomópont támogatása a HANA System replikációs (HSR) konfigurációját. Ez a konfiguráció csak támogatott a csomópont egyetlen HANA-példányokhoz. Azt jelenti, hogy, MCOS forgatókönyvek nem támogatottak.

**Ez az architektúra jelen pillanatban csak a SUSE operációs rendszer esetében támogatott.**


### <a name="architecture-diagram"></a>Architektúradiagram  

![HSR-az-STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre:

| HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZATÁNAK TÍPUSA | A SUSE operációs rendszer neve | Az RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUS | eth0.tenant | eno1.tenant | Ügyfél HLI |
| B | I. TÍPUS | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C | I. TÍPUS | eth1.tenant | eno2.tenant | A storage csomópont |
| D | I. TÍPUS | eth4.tenant | eno4.tenant | STONITH használt |
| A | II. TÍPUSÚ | VLAN<tenantNo> | team0.tenant | Ügyfél HLI |
| B | II. TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | A storage csomópont |
| D | II. TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | STONITH használt |

### <a name="storage"></a>Storage
A következő csatlakozási pontok le előre:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton**|
|/Hana/Shared/SID | HANA telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/logbackups/SID | Éles környezetben SID naplók visszaállítása |
|**A másodlagos csomópont**|
|/Hana/Shared/SID | Másodlagos SID HANA telepítése | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítse a másodlagos biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítse a másodlagos biztonsági azonosítója | 
|/Hana/logbackups/SID | Naplók megismétlése másodlagos biztonsági azonosítója |

### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID mutató szimbolikus hivatkozást.
- MCOS: a kötet mérete terjesztési adatbázis méretét a memóriában alapul. Tekintse meg a [áttekintése és architektúrája](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) részből megtudhatja, milyen adatbázist a memóriában méretezi multisid környezet használata támogatott.
- STONITH: A STONITH beállítása egy SBD van konfigurálva. STONITH használata azonban nem kötelező.


## <a name="6-hsr-with-dr"></a>6. A DR HSR
 
Ez a topológia két csomópont támogatása a HANA System replikációs (HSR) konfigurációját. A normál és a többcélú DR egyaránt támogatott. Ezek a beállítások csak egyetlen HANA-példányokhoz egy csomóponton támogatottak. Azt jelenti, hogy ezeket a konfigurációkat MCOS forgatókönyvek nem támogatottak.

Az ábrán a többcélú forgatókönyv adott helyen a Vészhelyreállítási megfelelőként jelenik, HLI egység QA példány használt, amíg az elsődleges helyről éles műveletek futnak. A DR feladatátvételének (vagy a feladatátvételi teszt) időpontjában alkotásait QA példány DR helyen. 



### <a name="architecture-diagram"></a>Architektúradiagram  

![HSR-az-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre:

| HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZATÁNAK TÍPUSA | A SUSE operációs rendszer neve | Az RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUS | eth0.tenant | eno1.tenant | Ügyfél HLI |
| B | I. TÍPUS | eth2.tenant | eno3.tenant | Konfigurálva, de nincs használatban |
| C | I. TÍPUS | eth1.tenant | eno2.tenant | A storage csomópont |
| D | I. TÍPUS | eth4.tenant | eno4.tenant | STONITH használt |
| A | II. TÍPUSÚ | VLAN<tenantNo> | team0.tenant | Ügyfél HLI |
| B | II. TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Konfigurálva, de nincs használatban |
| C | II. TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | A storage csomópont |
| D | II. TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | STONITH használt |

### <a name="storage"></a>Storage
A következő csatlakozási pontok le előre:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges hely elsődleges csomóponton**|
|/Hana/Shared/SID | HANA telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/logbackups/SID | Éles környezetben SID naplók visszaállítása |
|**A másodlagos csomópont az elsődleges helyen**|
|/Hana/Shared/SID | Másodlagos SID HANA telepítése | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítse a másodlagos biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítse a másodlagos biztonsági azonosítója | 
|/Hana/logbackups/SID | Naplók megismétlése másodlagos biztonsági azonosítója |
|**A DR helyen**|
|/Hana/Shared/SID | HANA telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Shared/QA-SID | QA SID HANA telepítése | 
|/Hana/Data/QA-SID/mnt00001 | Az adatfájlokat a QA SID telepítése | 
|/Hana/log/QA-SID/mnt00001 | Naplófájlok a QA SID telepítése |
|/Hana/logbackups/QA-SID | QA SID naplók megismétlése |

### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID mutató szimbolikus hivatkozást.
- MCOS: a kötet mérete terjesztési adatbázis méretét a memóriában alapul. Tekintse meg a [áttekintése és architektúrája](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) részből megtudhatja, milyen adatbázist a memóriában méretezi multisid környezet használata támogatott.
- STONITH: A STONITH beállítása egy SBD van konfigurálva. STONITH használata azonban nem kötelező.
- : A DR: **tárolóköteteket két csoportjára szükség** elsődleges és másodlagos csomópont-replikációhoz.
- : A DR: A kötetek és a csatlakozási pontok le vannak konfigurálva, (megjelölve "HANA telepítéshez szükséges") az éles környezetben a DR HLI egység HANA-példány telepítését. 
- : A DR: az adatok, logbackups és megosztott kötetek ("Tárreplikáció" megjelölve) lesznek replikálva az élesben használt helyet a pillanatkép-n keresztül. Ezek a kötetek csatlakoztatva vannak, csak a feladatátvételi idő alatt. További információkért olvassa el a dokumentumot [vészhelyreállítási feladatátvétel eljárást](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) további részletekért. 
- : A DR: az adatok, logbackups, log, QA (a "QA-példány telepítése" megjelölve) megosztott kötetek vannak konfigurálva a QA példány telepítéséhez.
- Rendszerindító kötet a **I osztály Termékváltozatának típusa** DR csomópont replikálja.


## <a name="7-host-auto-failover-11"></a>7. Gazdagép automatikus feladatátvétel (1 + 1)
 
Ez a topológia két csomópont támogatja a gazdagép automatikus feladatátvételi konfigurációban. Egy csomópont a fő/feldolgozói szerepkört, és más, egy a készenléti állapotban van. **SAP S/4 HANA csak a ebben a forgatókönyvben támogatja.** Tekintse meg a nyílt Forráskódú Megjegyzés "[2408419 – SAP S/4HANA - támogatás több csomópontos](https://launchpad.support.sap.com/#/notes/2408419)" További részleteket talál.



### <a name="architecture-diagram"></a>Architektúradiagram  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre:

| HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZATÁNAK TÍPUSA | A SUSE operációs rendszer neve | Az RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUS | eth0.tenant | eno1.tenant | Ügyfél HLI |
| B | I. TÍPUS | eth2.tenant | eno3.tenant | Csomópont és csomópont közötti kommunikációhoz |
| C | I. TÍPUS | eth1.tenant | eno2.tenant | A storage csomópont |
| D | I. TÍPUS | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. TÍPUSÚ | VLAN<tenantNo> | team0.tenant | Ügyfél HLI |
| B | II. TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Csomópont és csomópont közötti kommunikációhoz |
| C | II. TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | A storage csomópont |
| D | II. TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakozási pontok le előre:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**A master és a készenléti csomópontokon**|
|/ hana/megosztott | HANA telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/logbackups/SID | Éles környezetben SID naplók visszaállítása |



### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID mutató szimbolikus hivatkozást.
- Készenléti módban futhat: A kötetek és a csatlakozási pontok le vannak konfigurálva, (megjelölve "HANA telepítéshez szükséges") a HANA-példány telepítését a készenléti egység számára.
 

## <a name="8-scale-out-with-standby"></a>8. Horizontális felskálázás való készenlét
 
Ez a topológia több csomópont támogatja a horizontális felskálázás konfigurációban. Készenléti, van egy csomópont főkiszolgálói szerepkört, egy vagy több csomóponton a feldolgozói szerepkör és a egy vagy több csomópontot. Azonban lehetnek csak egy fő csomópont idő lekérdezhet.


### <a name="architecture-diagram"></a>Architektúradiagram  

![horizontális felskálázás-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre:

| HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZATÁNAK TÍPUSA | A SUSE operációs rendszer neve | Az RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUS | eth0.tenant | eno1.tenant | Ügyfél HLI |
| B | I. TÍPUS | eth2.tenant | eno3.tenant | Csomópont és csomópont közötti kommunikációhoz |
| C | I. TÍPUS | eth1.tenant | eno2.tenant | A storage csomópont |
| D | I. TÍPUS | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. TÍPUSÚ | VLAN<tenantNo> | team0.tenant | Ügyfél HLI |
| B | II. TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Csomópont és csomópont közötti kommunikációhoz |
| C | II. TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | A storage csomópont |
| D | II. TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakozási pontok le előre:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**A master, feldolgozói és készenléti csomópontokon**|
|/ hana/megosztott | HANA telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/logbackups/SID | Éles környezetben SID naplók visszaállítása |


## <a name="9-scale-out-without-standby"></a>9. Horizontális felskálázás készenléti nélkül
 
Ez a topológia több csomópont támogatja a horizontális felskálázás konfigurációban. Nincs főkiszolgálói szerepkört egy csomópont, és a egy és a feldolgozói szerepkör módban-csomópont. Azonban lehetnek csak egy fő csomópont idő lekérdezhet.


### <a name="architecture-diagram"></a>Architektúradiagram  

![horizontális felskálázás-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre:

| HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZATÁNAK TÍPUSA | A SUSE operációs rendszer neve | Az RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUS | eth0.tenant | eno1.tenant | Ügyfél HLI |
| B | I. TÍPUS | eth2.tenant | eno3.tenant | Csomópont és csomópont közötti kommunikációhoz |
| C | I. TÍPUS | eth1.tenant | eno2.tenant | A storage csomópont |
| D | I. TÍPUS | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. TÍPUSÚ | VLAN<tenantNo> | team0.tenant | Ügyfél HLI |
| B | II. TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Csomópont és csomópont közötti kommunikációhoz |
| C | II. TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | A storage csomópont |
| D | II. TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakozási pontok le előre:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**A master és a feldolgozó csomópontok**|
|/ hana/megosztott | HANA telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/logbackups/SID | Éles környezetben SID naplók visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID mutató szimbolikus hivatkozást.

## <a name="10-scale-out-with-dr"></a>10. Horizontális felskálázás a Vészhelyreállítás
 
Ez a topológia több csomópont egy DR-kibővített támogatja. Normál és a többcélú DR használata támogatott. Az ábrán csak egyetlen célja DR megfelelőként jelenik. Ez a topológia és a készenléti csomópontra anélkül kérhetnek.


### <a name="architecture-diagram"></a>Architektúradiagram  

![horizontális felskálázás-az-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre:

| HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZATÁNAK TÍPUSA | A SUSE operációs rendszer neve | Az RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | I. TÍPUS | eth0.tenant | eno1.tenant | Ügyfél HLI |
| B | I. TÍPUS | eth2.tenant | eno3.tenant | Csomópont és csomópont közötti kommunikációhoz |
| C | I. TÍPUS | eth1.tenant | eno2.tenant | A storage csomópont |
| D | I. TÍPUS | eth4.tenant | eno4.tenant | Konfigurálva, de nincs használatban |
| A | II. TÍPUSÚ | VLAN<tenantNo> | team0.tenant | Ügyfél HLI |
| B | II. TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Csomópont és csomópont közötti kommunikációhoz |
| C | II. TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | A storage csomópont |
| D | II. TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Storage
A következő csatlakozási pontok le előre:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton**|
|/ hana/megosztott | HANA telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/logbackups/SID | Éles környezetben SID naplók visszaállítása |
|**A DR-csomópont**|
|/ hana/megosztott | HANA telepítése éles környezetben biztonsági azonosítója | 
|/Hana/Data/SID/mnt00001 | Adatfájlok telepítése éles környezetben biztonsági azonosítója | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése éles környezetben biztonsági azonosítója | 


### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID mutató szimbolikus hivatkozást.
-  : A DR: A kötetek és a csatlakozási pontok le vannak konfigurálva, (megjelölve "HANA telepítéshez szükséges") az éles környezetben a DR HLI egység HANA-példány telepítését. 
- : A DR: az adatok, logbackups és megosztott kötetek ("Tárreplikáció" megjelölve) lesznek replikálva az élesben használt helyet a pillanatkép-n keresztül. Ezek a kötetek csatlakoztatva vannak, csak a feladatátvételi idő alatt. További információkért olvassa el a dokumentumot [vészhelyreállítási feladatátvétel eljárást](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) további részletekért. 
- Rendszerindító kötet a **I osztály Termékváltozatának típusa** DR csomópont replikálja.


## <a name="next-steps"></a>További lépések
- Tekintse meg [infrastruktúra és kapcsolódás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) HLI számára
- Tekintse meg [magas rendelkezésre állás és vészhelyreállítás helyreállítási](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) HLI számára