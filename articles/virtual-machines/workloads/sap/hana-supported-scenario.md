---
title: Támogatott forgatókönyvek SAP HANA (nagy példányok) Azure-on |} Microsoft Docs
description: Támogatott forgatókönyveket és az architektúra adatait SAP Hana Azure (nagy példány)
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
ms.date: 06/25/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b703f6d141005cf3cf29531a0586eebb61693a2
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754510"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Támogatott esetek nagy HANA-példányok
Ez a dokumentum ismerteti a támogatott forgatókönyvek mellett az architektúra részleteiről a HANA nagy példányok (HLI) számára.

>[!NOTE]
>Ha az adott helyzetben itt nem említett, forduljon a Microsoft szolgáltatás-felügyeleti csoporthoz a követelmények ellenőrzéséhez.
A kiépítés HLI egység folytatása előtt a Tervező SAP vagy a szolgáltatás megvalósítási partner ellenőrzése.

## <a name="terms-and-definitions"></a>Kifejezések és meghatározások
Most ismerje meg a feltételeket és a benne szereplő a dokumentum-definíciók.

- Biztonsági azonosító: Rendszerazonosító HANA rendszerhez.
- HLI: Nagy Hana-példányokat.
- DR: Egy vész helyreállítási helyet.
- Normál DR: A rendszer telepítése egy dedikált kizárólag a felhasznált vész-Helyreállítási célú-erőforrást.
- Multipurpose DR: A rendszer nem éles környezetben együtt vész-Helyreállítási eseményre használatára konfigurált éles példány használatára konfigurált vész-Helyreállítási helyen. 
- Egyetlen SID: A rendszer, ha egy telepített példánya.
- Több biztonsági azonosító: A rendszer több osztályt konfigurálva. Más néven MCOS környezet.


## <a name="overview"></a>Áttekintés
A nagy HANA-példányok a különböző üzleti igényei elvégzéséhez architektúrák támogatja. Az alábbi lista ismerteti a forgatókönyvek és a konfigurációs adatait. 

A származtatott architektúra pusztán az infrastruktúra szempontjából, és kell tájékozódnia a SAP vagy megvalósítási partnereivel HANA központi telepítésére vonatkozóan. A forgatókönyv nem szerepelnek a listán, ha forduljon a Microsoft fiókkezelési csapattal annak az architektúra áttekintése, és megoldást származnia meg.

**Ezek architektúrák teljes mértékben megfelelővé vált, a TDI (igazított Adatintegráció) kialakítással és SAP által támogatott.**

Ez a dokumentum ismerteti az egyes támogatott architektúrákhoz két összetevői részleteit:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Kiépített kiszolgálónként előre konfigurált ethernet a készletekkel származnak. Az alábbiakban az egyes HLI egységek konfigurált ethernet részleteit.

- **A**: / által az ügyfél-hozzáférési szolgál.
- **B**: a csomópontok kommunikációs szolgál. Ez konfigurálva van (függetlenül a kért topológia) minden olyan kiszolgálón, de csak a kibővített környezetben használható.
- **C**: Ez az interfész a csomópont a tárolóval való kapcsolat szolgál.
- **D**: Ez az interfész szolgál az iSCSI-eszköz kapcsolat STONITH telepítő csomópontra. Ez az interfész csak van beállítva, ha a HSR telepítő van szükség.  

| A HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZAT-TÍPUS | SUSE operációs rendszer neve | RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | ÍRJA BE A I | eth0.tenant | eno1.tenant | HLI ügyfél |
| B | ÍRJA BE A I | eth2.tenant | eno3.tenant | A csomópont csomópont |
| C | ÍRJA BE A I | eth1.tenant | eno2.tenant | Tárhely csomópontot |
| D | ÍRJA BE A I | eth4.tenant | eno4.tenant | STONITH |
| A | TÍPUSÚ | VLAN<tenantNo> | team0.tenant | HLI ügyfél |
| B | TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | A csomópont csomópont |
| C | TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | Tárhely csomópontot |
| D | TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | STONITH |

A konfigurált HLI egységben topológia a kapcsolatok használhatja. Például a "B" illesztőfelület van beállítva csomópontok kommunikációt, ami akkor hasznos, ha egy kibővített topológia konfigurálva van. Ez az interfész egycsomópontos méretezett konfiguráció esetén nem használható. A felület használatával kapcsolatos további információért tekintse át a szükséges forgatókönyvekben (a dokumentum későbbi szakaszában). 

Szükség esetén további hálózati kártyák definiálhat a saját. Azonban a meglévő hálózati adaptereken a konfiguráció nem módosítható.

>[!NOTE]
>További felületek, amelyek fizikai összeköttetések vagy ragasztás találhatja. A használt eset vegye figyelembe a fent említett felületek, a többi figyelmen kívül hagyja vagy vagy nem rendelkező mérsékelni kell.


### <a name="storage"></a>Storage
Tárolási kért topológia alapján előre konfigurált. A kötet mérete és a csatlakoztatási pont a kiszolgálók számát, SKU, és konfigurálva topológia függően változhat. További információért tekintse át a szükséges forgatókönyvekben (a dokumentum későbbi szakaszában). Ha további tárhely szükséges, vásárolhat egy TB emelkednie.

>[!NOTE]
>A csatlakozásipont/usr/sap/<SID> a szimbolikus hivatkozást úgy, hogy a csatlakozási pont/hana/megosztott van.


## <a name="supported-scenarios"></a>Támogatott helyzetek

Az architektúra ábrákon következő jelölések a grafikus használják:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

Az alábbi lista tartalmazza a támogatott forgatókönyvekről:

1. Egy SID egyetlen csomópont
2. Egyetlen csomópont MCOS
3. Egyetlen csomópont vész-Helyreállítási (normál)
4. A vész-Helyreállítási (Multipurpose) egyetlen csomópont
5. A STONITH HSR
6. A vész-Helyreállítási HSR (normál / Multipurpose) 
7. Állomás automatikus feladatátvétel (1 + 1) 
8. Horizontális felskálázás az készenléti
9. Horizontális felskálázás készenléti nélkül
10. Horizontális felskálázás az vész-Helyreállítási



## <a name="1-single-node-with-one-sid"></a>1. Egy SID egyetlen csomópont

Ez a topológia egy csomópontot a skálától beállítható egy SID-konfiguráció támogatja.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Egyetlen-csomópont-a-1-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Az előre a következő hálózati adapterek:

| A HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZAT-TÍPUS | SUSE operációs rendszer neve | RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | ÍRJA BE A I | eth0.tenant | eno1.tenant | HLI ügyfél |
| B | ÍRJA BE A I | eth2.tenant | eno3.tenant | Nem használni de konfigurálva |
| C | ÍRJA BE A I | eth1.tenant | eno2.tenant | Tárhely csomópontot |
| D | ÍRJA BE A I | eth4.tenant | eno4.tenant | Nem használni de konfigurálva |
| A | TÍPUSÚ | VLAN<tenantNo> | team0.tenant | HLI ügyfél |
| B | TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Nem használni de konfigurálva |
| C | TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | Tárhely csomópontot |
| D | TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Nem használni de konfigurálva |

### <a name="storage"></a>Storage
Az előre a következő csatlakozási pontok le:

| Csatlakozási pont | Használati eset | 
| --- | --- |
|/Hana/Shared/SID | HANA telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok telepítése | 
|/Hana/log/SID/mnt00001 | Fájlok telepítése | 
|/Hana/logbackups/SID | Naplók visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID a szimbolikus hivatkozást.

## <a name="2-single-node-mcos"></a>2. Egyetlen csomópont MCOS

Ez a topológia egy csomópontot a skálától beállítható több SID-konfiguráció támogatja.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Single-csomópont-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Az előre a következő hálózati adapterek:

| A HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZAT-TÍPUS | SUSE operációs rendszer neve | RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | ÍRJA BE A I | eth0.tenant | eno1.tenant | HLI ügyfél |
| B | ÍRJA BE A I | eth2.tenant | eno3.tenant | Nem használni de konfigurálva |
| C | ÍRJA BE A I | eth1.tenant | eno2.tenant | Tárhely csomópontot |
| D | ÍRJA BE A I | eth4.tenant | eno4.tenant | Nem használni de konfigurálva |
| A | TÍPUSÚ | VLAN<tenantNo> | team0.tenant | HLI ügyfél |
| B | TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Nem használni de konfigurálva |
| C | TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | Tárhely csomópontot |
| D | TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Nem használni de konfigurálva |

### <a name="storage"></a>Storage
Az előre a következő csatlakozási pontok le:

| Csatlakozási pont | Használati eset | 
| --- | --- |
|/Hana/Shared/SID1 | SID1 HANA telepítését | 
|/Hana/Data/SID1/mnt00001 | Az adatfájlok SID1 telepítése | 
|/Hana/log/SID1/mnt00001 | Naplófájlok SID1 telepítése | 
|/Hana/logbackups/SID1 | Naplók SID1 megismétlése |
|/Hana/Shared/SID2 | SID2 HANA telepítését | 
|/Hana/Data/SID2/mnt00001 | Az adatfájlok SID2 telepítése | 
|/Hana/log/SID2/mnt00001 | Naplófájlok SID2 telepítése | 
|/Hana/logbackups/SID2 | Naplók SID2 megismétlése |

### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID a szimbolikus hivatkozást.
- Kötet méretének terjesztési adatbázis méretét a memóriában épül. Tekintse meg a [áttekintése és architektúra](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) részből megtudhatja, milyen adatbázis méretét a memóriában multisid környezet használata támogatott.

## <a name="3-single-node-with-dr-normal"></a>3. Egyetlen csomópont vész-Helyreállítási (normál)
 
Ez a topológia egy csomópontot a skálától beállítható a storage-alapú replikáció, a vész-Helyreállítási hely egy elsődleges biztonsági azonosító egy vagy több SID-konfiguráció támogatja. Az ábrán csak egyetlen SID írja le az elsődleges helyen, de multisid (MCOS) is támogatott.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Single-csomópont-az-dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Az előre a következő hálózati adapterek:

| A HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZAT-TÍPUS | SUSE operációs rendszer neve | RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | ÍRJA BE A I | eth0.tenant | eno1.tenant | HLI ügyfél |
| B | ÍRJA BE A I | eth2.tenant | eno3.tenant | Nem használni de konfigurálva |
| C | ÍRJA BE A I | eth1.tenant | eno2.tenant | Tárhely csomópontot |
| D | ÍRJA BE A I | eth4.tenant | eno4.tenant | Nem használni de konfigurálva |
| A | TÍPUSÚ | VLAN<tenantNo> | team0.tenant | HLI ügyfél |
| B | TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Nem használni de konfigurálva |
| C | TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | Tárhely csomópontot |
| D | TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Nem használni de konfigurálva |

### <a name="storage"></a>Storage
Az előre a következő csatlakozási pontok le:

| Csatlakozási pont | Használati eset | 
| --- | --- |
|/Hana/Shared/SID | HANA telepítése a következő biztonsági AZONOSÍTÓHOZ: | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok telepítse a következő biztonsági AZONOSÍTÓHOZ: | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepítése a következő biztonsági AZONOSÍTÓHOZ: | 
|/Hana/logbackups/SID | Naplók visszaállítása a következő biztonsági AZONOSÍTÓHOZ: |


### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID a szimbolikus hivatkozást.
- MCOS: a kötet mérete terjesztési adatbázis méretét a memóriában épül. Tekintse meg a [áttekintése és architektúra](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) részből megtudhatja, milyen adatbázis méretét a memóriában multisid környezet használata támogatott.
- A vész-Helyreállítási:: (megjelölve "HANA telepítéshez szükséges") konfigurált a kötetek és a csatlakozási pontok le az üzemi HANA-példány telepítése a vész-Helyreállítási HLI egység. 
- A vész-Helyreállítási,:, logbackups, az adatokat a fürt megosztott kötetei ("Tárolóreplikálást" jelölésű) keresztül a munkakörnyezeti helyet a pillanatkép replikálódnak. Ezek a kötetek csatlakoztatva vannak, csak a feladatátvételi idő alatt. Tekintse meg a [vész-helyreállítási folyamattal feladatátvételi](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) további részleteket.


## <a name="4-single-node-with-dr-multipurpose"></a>4. A vész-Helyreállítási (Multipurpose) egyetlen csomópont
 
Ez a topológia egy csomópontot a skálától beállítható a storage-alapú replikáció, a vész-Helyreállítási hely egy elsődleges biztonsági azonosító egy vagy több SID-konfiguráció támogatja. Az ábrán csak egyetlen SID írja le az elsődleges helyen, de multisid (MCOS) is támogatott. A vész-Helyreállítási helyen HLI egység használható QA példány gyártási műveletek az elsődleges helyről működése közben. A vész-Helyreállítási feladatátvevő (vagy a feladatátvételi teszt) időpontjában leállítaná QA példány vész-Helyreállítási helyen.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Single-csomópont-az-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Az előre a következő hálózati adapterek:

| A HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZAT-TÍPUS | SUSE operációs rendszer neve | RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | ÍRJA BE A I | eth0.tenant | eno1.tenant | HLI ügyfél |
| B | ÍRJA BE A I | eth2.tenant | eno3.tenant | Nem használni de konfigurálva |
| C | ÍRJA BE A I | eth1.tenant | eno2.tenant | Tárhely csomópontot |
| D | ÍRJA BE A I | eth4.tenant | eno4.tenant | Nem használni de konfigurálva |
| A | TÍPUSÚ | VLAN<tenantNo> | team0.tenant | HLI ügyfél |
| B | TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Nem használni de konfigurálva |
| C | TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | Tárhely csomópontot |
| D | TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Nem használni de konfigurálva |

### <a name="storage"></a>Storage
Az előre a következő csatlakozási pontok le:

| Csatlakozási pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/Hana/Shared/SID | HANA üzemi SID telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok üzemi SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok üzemi SID telepítése | 
|/Hana/logbackups/SID | Naplók üzemi SID visszaállítása |
|**A vész-Helyreállítási helyen**|
|/Hana/Shared/SID | HANA üzemi SID telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok üzemi SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok üzemi SID telepítése | 
|/Hana/Shared/QA-SID | QA SID HANA telepítését | 
|/Hana/Data/QA-SID/mnt00001 | Az adatfájlok QA SID telepítése | 
|/Hana/log/QA-SID/mnt00001 | Naplófájlok a QA SID telepítése |
|/Hana/logbackups/QA-SID | QA SID naplók megismétlése |

### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID a szimbolikus hivatkozást.
- MCOS: a kötet mérete terjesztési adatbázis méretét a memóriában épül. Tekintse meg a [áttekintése és architektúra](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) részből megtudhatja, milyen adatbázis méretét a memóriában multisid környezet használata támogatott.
- A vész-Helyreállítási:: (megjelölve "HANA telepítéshez szükséges") konfigurált a kötetek és a csatlakozási pontok le az üzemi HANA-példány telepítése a vész-Helyreállítási HLI egység. 
- A vész-Helyreállítási,:, logbackups, az adatokat a fürt megosztott kötetei ("Tárolóreplikálást" jelölésű) keresztül a munkakörnyezeti helyet a pillanatkép replikálódnak. Ezek a kötetek csatlakoztatva vannak, csak a feladatátvételi idő alatt. Tekintse meg a [vész-helyreállítási folyamattal feladatátvételi](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) további részleteket. 
- A vész-Helyreállítási:: az adatok, a logbackups, a napló, a QA-példány telepítése (a "QA példány telepítés" jelölésű) QA megosztott kötetek vannak beállítva.

## <a name="5-hsr-with-stonith"></a>5. A STONITH HSR
 
Ez a topológia két csomópont támogatása HANA rendszer replikációs (HSR) konfigurációját. 

**Mostantól Ez az architektúra csak támogatott SUSE operációs rendszer.**


### <a name="architecture-diagram"></a>Architektúradiagram  

![HSR rendelkező STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Az előre a következő hálózati adapterek:

| A HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZAT-TÍPUS | SUSE operációs rendszer neve | RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | ÍRJA BE A I | eth0.tenant | eno1.tenant | HLI ügyfél |
| B | ÍRJA BE A I | eth2.tenant | eno3.tenant | Nem használni de konfigurálva |
| C | ÍRJA BE A I | eth1.tenant | eno2.tenant | Tárhely csomópontot |
| D | ÍRJA BE A I | eth4.tenant | eno4.tenant | STONITH használatos |
| A | TÍPUSÚ | VLAN<tenantNo> | team0.tenant | HLI ügyfél |
| B | TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Nem használni de konfigurálva |
| C | TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | Tárhely csomópontot |
| D | TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | STONITH használatos |

### <a name="storage"></a>Storage
Az előre a következő csatlakozási pontok le:

| Csatlakozási pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton lévő**|
|/Hana/Shared/SID | HANA üzemi SID telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok üzemi SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok üzemi SID telepítése | 
|/Hana/logbackups/SID | Naplók üzemi SID visszaállítása |
|**A másodlagos csomópont**|
|/Hana/Shared/SID | Másodlagos SID HANA telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok másodlagos SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepíteni a másodlagos biztonsági azonosítója | 
|/Hana/logbackups/SID | Másodlagos SID naplók megismétlése |

### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID a szimbolikus hivatkozást.
- MCOS: a kötet mérete terjesztési adatbázis méretét a memóriában épül. Tekintse meg a [áttekintése és architektúra](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) részből megtudhatja, milyen adatbázis méretét a memóriában multisid környezet használata támogatott.
- STONITH: A STONITH beállítása egy SBD van konfigurálva. STONITH használata azonban nem kötelező.


## <a name="6-hsr-with-dr"></a>6. A vész-Helyreállítási HSR
 
Ez a topológia két csomópont támogatása HANA rendszer replikációs (HSR) konfigurációját. Mind a normál és többcélú DR esetén támogatott. 

A diagramon többcélú forgatókönyvet az adott helyen a vész-Helyreállítási ideális, HLI egység QA példány szolgál, éles műveletek az elsődleges helyről működése közben. A vész-Helyreállítási feladatátvevő (vagy a feladatátvételi teszt) időpontjában leállítaná QA példány vész-Helyreállítási helyen. 



### <a name="architecture-diagram"></a>Architektúradiagram  

![HSR rendelkező DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Az előre a következő hálózati adapterek:

| A HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZAT-TÍPUS | SUSE operációs rendszer neve | RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | ÍRJA BE A I | eth0.tenant | eno1.tenant | HLI ügyfél |
| B | ÍRJA BE A I | eth2.tenant | eno3.tenant | Nem használni de konfigurálva |
| C | ÍRJA BE A I | eth1.tenant | eno2.tenant | Tárhely csomópontot |
| D | ÍRJA BE A I | eth4.tenant | eno4.tenant | STONITH használatos |
| A | TÍPUSÚ | VLAN<tenantNo> | team0.tenant | HLI ügyfél |
| B | TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Nem használni de konfigurálva |
| C | TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | Tárhely csomópontot |
| D | TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | STONITH használatos |

### <a name="storage"></a>Storage
Az előre a következő csatlakozási pontok le:

| Csatlakozási pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen az elsődleges csomóponton**|
|/Hana/Shared/SID | HANA üzemi SID telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok üzemi SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok üzemi SID telepítése | 
|/Hana/logbackups/SID | Naplók üzemi SID visszaállítása |
|**A másodlagos csomópont az elsődleges helyen**|
|/Hana/Shared/SID | Másodlagos SID HANA telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok másodlagos SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok telepíteni a másodlagos biztonsági azonosítója | 
|/Hana/logbackups/SID | Másodlagos SID naplók megismétlése |
|**A vész-Helyreállítási helyen**|
|/Hana/Shared/SID | HANA üzemi SID telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok üzemi SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok üzemi SID telepítése | 
|/Hana/Shared/QA-SID | QA SID HANA telepítését | 
|/Hana/Data/QA-SID/mnt00001 | Az adatfájlok QA SID telepítése | 
|/Hana/log/QA-SID/mnt00001 | Naplófájlok a QA SID telepítése |
|/Hana/logbackups/QA-SID | QA SID naplók megismétlése |

### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID a szimbolikus hivatkozást.
- MCOS: a kötet mérete terjesztési adatbázis méretét a memóriában épül. Tekintse meg a [áttekintése és architektúra](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) részből megtudhatja, milyen adatbázis méretét a memóriában multisid környezet használata támogatott.
- STONITH: A STONITH beállítása egy SBD van konfigurálva. STONITH használata azonban nem kötelező.
- A vész-Helyreállítási:: **tárolókötetek két csoportjára szükség** elsődleges és másodlagos csomópont replikációhoz.
- A vész-Helyreállítási:: (megjelölve "HANA telepítéshez szükséges") konfigurált a kötetek és a csatlakozási pontok le az üzemi HANA-példány telepítése a vész-Helyreállítási HLI egység. 
- A vész-Helyreállítási,:, logbackups, az adatokat a fürt megosztott kötetei ("Tárolóreplikálást" jelölésű) keresztül a munkakörnyezeti helyet a pillanatkép replikálódnak. Ezek a kötetek csatlakoztatva vannak, csak a feladatátvételi idő alatt. Tekintse meg a [vész-helyreállítási folyamattal feladatátvételi](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) további részleteket. 
- A vész-Helyreállítási:: az adatok, a logbackups, a napló, a QA-példány telepítése (a "QA példány telepítés" jelölésű) QA megosztott kötetek vannak beállítva.


## <a name="7-host-auto-failover-11"></a>7. Állomás automatikus feladatátvétel (1 + 1)
 
Ez a topológia két csomópont támogatja a gazdagép automatikus feladatátvételi konfigurációban. Egy csomópont master/feldolgozói szerepkör és más, a készenléti van. **SAP S/4 HANA csak a támogatás a forgatókönyv.** Tekintse meg a OSS Megjegyzés "[2408419 - SAP S/4HANA - többcsomópontos támogatási](https://launchpad.support.sap.com/#/notes/2408419)" További részletek.



### <a name="architecture-diagram"></a>Architektúradiagram  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Az előre a következő hálózati adapterek:

| A HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZAT-TÍPUS | SUSE operációs rendszer neve | RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | ÍRJA BE A I | eth0.tenant | eno1.tenant | HLI ügyfél |
| B | ÍRJA BE A I | eth2.tenant | eno3.tenant | Csomópontok kommunikáció csomópont |
| C | ÍRJA BE A I | eth1.tenant | eno2.tenant | Tárhely csomópontot |
| D | ÍRJA BE A I | eth4.tenant | eno4.tenant | Nem használni de konfigurálva |
| A | TÍPUSÚ | VLAN<tenantNo> | team0.tenant | HLI ügyfél |
| B | TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Csomópontok kommunikáció csomópont |
| C | TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | Tárhely csomópontot |
| D | TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Nem használni de konfigurálva |

### <a name="storage"></a>Storage
Az előre a következő csatlakozási pontok le:

| Csatlakozási pont | Használati eset | 
| --- | --- |
|**A fő és készenléti csomópontokon**|
|/ hana/megosztott | HANA üzemi SID telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok üzemi SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok üzemi SID telepítése | 
|/Hana/logbackups/SID | Naplók üzemi SID visszaállítása |



### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID a szimbolikus hivatkozást.
- Készenléti: A kötetek és a csatlakozási pontok le van beállítva, (megjelölve "HANA telepítéshez szükséges") a készenléti egység HANA példány telepíthető.
 

## <a name="8-scale-out-with-standby"></a>8. Horizontális felskálázás az készenléti
 
Ez a topológia több csomópont egy konfigurációs kiterjesztési támogatja. Készenléti főkiszolgálói szerepkört, a feldolgozói szerepkör egy vagy több csomópontot és egy vagy több csomópont egy csomópont áll. Azonban lehetnek csak egy fő csomópont idő álljon.


### <a name="architecture-diagram"></a>Architektúradiagram  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Az előre a következő hálózati adapterek:

| A HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZAT-TÍPUS | SUSE operációs rendszer neve | RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | ÍRJA BE A I | eth0.tenant | eno1.tenant | HLI ügyfél |
| B | ÍRJA BE A I | eth2.tenant | eno3.tenant | Csomópontok kommunikáció csomópont |
| C | ÍRJA BE A I | eth1.tenant | eno2.tenant | Tárhely csomópontot |
| D | ÍRJA BE A I | eth4.tenant | eno4.tenant | Nem használni de konfigurálva |
| A | TÍPUSÚ | VLAN<tenantNo> | team0.tenant | HLI ügyfél |
| B | TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Csomópontok kommunikáció csomópont |
| C | TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | Tárhely csomópontot |
| D | TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Nem használni de konfigurálva |

### <a name="storage"></a>Storage
Az előre a következő csatlakozási pontok le:

| Csatlakozási pont | Használati eset | 
| --- | --- |
|**A master, a munkavégző és a készenléti állapotban lévő csomópontok**|
|/ hana/megosztott | HANA üzemi SID telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok üzemi SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok üzemi SID telepítése | 
|/Hana/logbackups/SID | Naplók üzemi SID visszaállítása |


## <a name="9-scale-out-without-standby"></a>9. Horizontális felskálázás készenléti nélkül
 
Ez a topológia több csomópont egy konfigurációs kiterjesztési támogatja. Van egy csomópont főkiszolgálói szerepkört, és egy vagy mód csomópontokat a feldolgozói szerepkör. Azonban lehetnek csak egy fő csomópont idő álljon.


### <a name="architecture-diagram"></a>Architektúradiagram  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Az előre a következő hálózati adapterek:

| A HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZAT-TÍPUS | SUSE operációs rendszer neve | RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | ÍRJA BE A I | eth0.tenant | eno1.tenant | HLI ügyfél |
| B | ÍRJA BE A I | eth2.tenant | eno3.tenant | Csomópontok kommunikáció csomópont |
| C | ÍRJA BE A I | eth1.tenant | eno2.tenant | Tárhely csomópontot |
| D | ÍRJA BE A I | eth4.tenant | eno4.tenant | Nem használni de konfigurálva |
| A | TÍPUSÚ | VLAN<tenantNo> | team0.tenant | HLI ügyfél |
| B | TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Csomópontok kommunikáció csomópont |
| C | TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | Tárhely csomópontot |
| D | TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Nem használni de konfigurálva |

### <a name="storage"></a>Storage
Az előre a következő csatlakozási pontok le:

| Csatlakozási pont | Használati eset | 
| --- | --- |
|**A fő- és feldolgozó csomópontokon**|
|/ hana/megosztott | HANA üzemi SID telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok üzemi SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok üzemi SID telepítése | 
|/Hana/logbackups/SID | Naplók üzemi SID visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID a szimbolikus hivatkozást.

## <a name="10-scale-out-with-dr"></a>10. Horizontális felskálázás az vész-Helyreállítási
 
Ez a topológia több csomópont egy, a vész-Helyreállítási kiterjesztési támogatja. Normál és a többcélú vész-Helyreállítási esetén támogatott. Az ábrán csak a egycélú vész-Helyreállítási írja le. Ez a topológia, vagy a készenléti csomópont anélkül is igényelhet.


### <a name="architecture-diagram"></a>Architektúradiagram  

![scaleout rendelkező dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Az előre a következő hálózati adapterek:

| A HÁLÓZATI ADAPTER LOGIKAI FELÜLETEK | TERMÉKVÁLTOZAT-TÍPUS | SUSE operációs rendszer neve | RHEL operációs rendszer neve | Használati eset|
| --- | --- | --- | --- | --- |
| A | ÍRJA BE A I | eth0.tenant | eno1.tenant | HLI ügyfél |
| B | ÍRJA BE A I | eth2.tenant | eno3.tenant | Csomópontok kommunikáció csomópont |
| C | ÍRJA BE A I | eth1.tenant | eno2.tenant | Tárhely csomópontot |
| D | ÍRJA BE A I | eth4.tenant | eno4.tenant | Nem használni de konfigurálva |
| A | TÍPUSÚ | VLAN<tenantNo> | team0.tenant | HLI ügyfél |
| B | TÍPUSÚ | VLAN < tenantNo + 2 > | team0.tenant + 2 | Csomópontok kommunikáció csomópont |
| C | TÍPUSÚ | VLAN < tenantNo + 1 > | team0.tenant + 1 | Tárhely csomópontot |
| D | TÍPUSÚ | VLAN < tenantNo + 3 > | team0.tenant + 3 | Nem használni de konfigurálva |

### <a name="storage"></a>Storage
Az előre a következő csatlakozási pontok le:

| Csatlakozási pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton lévő**|
|/ hana/megosztott | HANA üzemi SID telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok üzemi SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok üzemi SID telepítése | 
|/Hana/logbackups/SID | Naplók üzemi SID visszaállítása |
|**A vész-Helyreállítási csomóponton**|
|/ hana/megosztott | HANA üzemi SID telepítése | 
|/Hana/Data/SID/mnt00001 | Az adatfájlok üzemi SID telepítése | 
|/Hana/log/SID/mnt00001 | Naplófájlok üzemi SID telepítése | 


### <a name="key-considerations"></a>Fő szempontok
- /usr/SAP/SID /hana/shared/SID a szimbolikus hivatkozást.
-  A vész-Helyreállítási:: (megjelölve "HANA telepítéshez szükséges") konfigurált a kötetek és a csatlakozási pontok le az üzemi HANA-példány telepítése a vész-Helyreállítási HLI egység. 
- A vész-Helyreállítási,:, logbackups, az adatokat a fürt megosztott kötetei ("Tárolóreplikálást" jelölésű) keresztül a munkakörnyezeti helyet a pillanatkép replikálódnak. Ezek a kötetek csatlakoztatva vannak, csak a feladatátvételi idő alatt. Tekintse meg a [vész-helyreállítási folyamattal feladatátvételi](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) további részleteket. 


## <a name="next-steps"></a>További lépések
- Tekintse meg a [infrastruktúra és a csatlakozási](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) HLI számára
- Tekintse meg a [magas rendelkezésre állási és vészhelyreállítási helyreállítási](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) HLI számára