---
title: Támogatott forgatókönyvek SAP HANA az Azure-ban (nagyméretű példányok) | Microsoft Docs
description: Támogatott forgatókönyvek és az Azure-beli SAP HANA architektúrájának részletei (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ed63f5caa6b1f1c0072a92f6a60ad43c5431af0
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538389"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>A HANA nagyméretű példányainak támogatott forgatókönyvei
Ez a dokumentum ismerteti a támogatott forgatókönyveket, valamint a HANA Large instances (HLI) architektúrájának részleteit.

>[!NOTE]
>Ha a szükséges forgatókönyvet itt nem említi, a követelmények felméréséhez forduljon a Microsoft Service Management csapatához.
Mielőtt folytatná a HLI egység kiépítését, ellenőrizze a kialakítást az SAP-vel vagy a szolgáltatás implementációs partnerével.

## <a name="terms-and-definitions"></a>Feltételek és definíciók
Ismerkedjen meg a dokumentumban használt feltételekkel és definíciókkal.

- SID: a HANA rendszer rendszerazonosítója.
- HLI: Hana nagyméretű példányok.
- DR: vész-helyreállítási hely.
- Normál DR: egy olyan Rendszerbeállítás, amely kizárólag a DR célra használt dedikált erőforrással rendelkezik.
- Többcélú DR: A Dr-helyen A nem éles környezet használatára konfigurált rendszer, valamint a DR eseményhez való használatra konfigurált éles példány. 
- Egyetlen SID: egy telepített rendszer egy példányával.
- Többszörös SID: több példányban konfigurált rendszer. Más néven MCOS-környezet.
- HSR: SAP HANA rendszerreplikáció.

## <a name="overview"></a>Áttekintés
A HANA nagyméretű példányai támogatják a különböző architektúrákat az üzleti követelmények teljesítéséhez. A következő lista ismerteti a forgatókönyveket és azok konfigurációs adatait. 

A származtatott architektúra kialakítása kizárólag az infrastruktúra szempontjából hasznos, és az SAP-t vagy a saját implementációs partnereit kell megadnia a HANA üzembe helyezéséhez. Ha a forgatókönyvek nem szerepelnek a felsorolásban, lépjen kapcsolatba a Microsoft-fiók csapatával, és tekintse át az architektúrát, és származtatja a megoldást.

**Ezek az architektúrák teljes mértékben megfelelnek a TDI (testreszabott Adatintegráció) kialakításának és az SAP által támogatottnak.**

Ez a dokumentum ismerteti az egyes Támogatott architektúrák két összetevőjének részleteit:

- Ethernet
- Tárolás

### <a name="ethernet"></a>Ethernet

Minden kiszolgáló üzembe helyezése előre konfigurálva van az Ethernet-adapterek készletével. Az alábbiakban láthatók az egyes HLI-egységeken konfigurált Ethernet-felületek részletei.

- **A**: ezt a felületet használja az ügyfél-hozzáférés.
- **B**: ez az interfész a csomópontok közötti kommunikációra szolgál. Ez az interfész az összes kiszolgálón konfigurálva van (függetlenül a kért topológiától), de csak a 
- Felskálázási forgatókönyvek.
- **C**: ezt a felületet használja a rendszer a csomóponthoz a tárolási kapcsolathoz.
- **D**: ezt a felületet használja a csomópont iSCSI-eszköz kapcsolatára a STONITH telepítéséhez. Ez az illesztőfelület csak a HSR-telepítés kérelmezése után van konfigurálva.  

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | HLI-ügyfél |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópontról csomópontra |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | STONITH |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | HLI-ügyfél |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Csomópontról csomópontra |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | STONITH |

A csatolókat a HLI egységen konfigurált topológia alapján használhatja. A "B" illesztőfelület például a csomópontok közötti kommunikációra van beállítva, ami akkor lehet hasznos, ha kibővíthető topológia van konfigurálva. Az egycsomópontos méretezési konfiguráció esetében ez az illesztőfelület nincs használatban. Tekintse át a szükséges forgatókönyveket (a dokumentum későbbi részében), hogy további információkat kapjon az interfész használatáról. 

Szükség esetén további hálózati adapter-kártyákat is meghatározhat saját maga. A meglévő hálózati adaptereken azonban nem lehet módosítani a konfigurációt.

>[!NOTE]
>Továbbra is megtalálhatja a fizikai felületek vagy a kötések további felületeit. Érdemes megfontolni a fent említett, a használatban lévő felületeket, a REST-et figyelmen kívül hagyhatja, és nem is végezheti el.

A két hozzárendelt IP-címmel rendelkező egységek eloszlásának a következőhöz hasonlóan kell kinéznie:

- Az "A" Ethernet-címnek olyan IP-címmel kell rendelkeznie, amely ki van osztva a Microsoft számára elküldött kiszolgáló IP-címkészlet-címtartományból. Ezt az IP-címet kell használni az operációs rendszer/etc/hosts való karbantartáshoz.

- A "C" Ethernet-címnek hozzá kell rendelnie egy IP-címet, amelyet a rendszer az NFS-hez való kommunikációhoz használ. Ezért ezeket a címeket **nem** kell karbantartani az etc/hosts szolgáltatásban, hogy a példány a bérlőn belül is forgalomba kerüljön.

A HANA rendszerreplikáció vagy a HANA kibővítő üzembe helyezési eseteihez a két IP-címet hozzárendelő panel-konfiguráció nem megfelelő. Ha két IP-cím van hozzárendelve, és egy ilyen konfigurációt szeretne üzembe helyezni, lépjen kapcsolatba SAP HANA az Azure Service Management szolgáltatásban, hogy harmadik IP-címet kapjon a hozzárendelt harmadik VLAN-ban. A három NIC-porton három IP-címmel rendelkező HANA nagyméretű példányok esetén a következő használati szabályok érvényesek:

- Az "A" Ethernet-címnek olyan IP-címmel kell rendelkeznie, amely ki van osztva a Microsoft számára elküldött kiszolgáló IP-címkészlet-címtartományból. Ezért ez az IP-cím nem használható az operációs rendszer/etc/hosts való karbantartáshoz.

- A "B" Ethernetet kizárólag a különböző példányok közötti kommunikációhoz használatos, etc/gazdagépeken kell fenntartani. Ezek a címek azok az IP-címek is, amelyeket a kibővített HANA-konfigurációkban kell karbantartani, mivel a HANA által használt IP-címek a csomópontok közötti konfigurációhoz tartoznak.

- A "C" Ethernet-címnek hozzá kell rendelnie egy IP-címet, amely az NFS-tárolóval való kommunikációhoz használatos. Ezért az ilyen típusú címeket nem szabad karbantartani az etc/hosts szolgáltatásban.

- A "D" Ethernetet kizárólag a pacemakerhez való hozzáféréshez kell használni a STONITH eszközön. Ez az interfész akkor szükséges, ha a HANA rendszerreplikációt (HSR) konfigurálja, és egy SBD-alapú eszköz használatával szeretné elérni az automatikus feladatátvételt az operációs rendszeren.


### <a name="storage"></a>Tárolás
A tárterület előre konfigurálva van a kért topológia alapján. A kötetek mérete és a csatlakoztatási pont a kiszolgálók, a SKU-i és a topológiák számától függően változhat. További információkért tekintse át a szükséges forgatókönyveket (a dokumentum későbbi részében). Ha további tárterületre van szükség, megvásárolhatja azt egy TB-os növekményben.

>[!NOTE]
>A csatlakoztatási pont/usr/SAP/\<SID > egy szimbolikus hivatkozás a/Hana/Shared csatlakoztatási pont.


## <a name="supported-scenarios"></a>Támogatott forgatókönyvek

Az architektúra diagramjaiban a következő jelöléseket kell használni a grafikákhoz:

[![Legends. PNG](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

A támogatott forgatókönyvek a következő listában láthatók:

1. Egyetlen csomópont egyetlen SID-vel
2. Egyetlen csomópontos MCOS
3. Egyetlen csomópont DR-vel (normál)
4. Egyetlen csomópont DR-vel (többcélú)
5. HSR a STONITH
6. HSR DR-vel (normál/többcélú) 
7. Gazdagép automatikus feladatátvétele (1 + 1) 
8. Kibővíthető készenléti állapottal
9. Vertikális felskálázás készenlét nélkül
10. Vertikális felskálázás DR-vel



## <a name="1-single-node-with-one-sid"></a>1. egyetlen csomópont egyetlen SID-vel

Ez a topológia egy biztonsági Felskálázási konfiguráció egyik csomópontját támogatja egy SID-vel.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Single-node-with-one-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | HLI-ügyfél |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | HLI-ügyfél |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|/hana/shared/SID | HANA-telepítés | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése | 
|/hana/logbackups/SID | Naplók visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása

## <a name="2-single-node-mcos"></a>2. egyetlen csomópontos MCOS

Ez a topológia a több SID-vel rendelkező Felskálázási konfiguráció egyik csomópontját támogatja.

### <a name="architecture-diagram"></a>Architektúradiagram  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | HLI-ügyfél |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | HLI-ügyfél |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|/hana/shared/SID1 | HANA-telepítés a SID1 | 
|/hana/data/SID1/mnt00001 | Az adatfájlok telepítése a SID1 | 
|/hana/log/SID1/mnt00001 | A naplófájlok telepítése a SID1 | 
|/hana/logbackups/SID1 | SID1-naplók visszaállítása |
|/hana/shared/SID2 | HANA-telepítés a SID2 | 
|/hana/data/SID2/mnt00001 | Az adatfájlok telepítése a SID2 | 
|/hana/log/SID2/mnt00001 | A naplófájlok telepítése a SID2 | 
|/hana/logbackups/SID2 | SID2-naplók visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A kötet méretének eloszlása a memóriában lévő adatbázis méretétől függ. Tekintse át az [Áttekintés és az architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) szakaszt, amelyből megtudhatja, hogy az multisid-környezet hogyan támogatja a memóriában lévő adatbázisok méretét.

## <a name="3-single-node-with-dr-using-storage-replication"></a>3. egyetlen csomópont a DR használatával tárolási replikációval
 
Ez a topológia támogatja az egyik csomópontot egy vertikális Felskálázási konfigurációban egy vagy több SID-vel, és az elsődleges biztonsági azonosítóhoz tartozó, Storage-alapú replikációt a DR-helyre. A diagramon csak egyetlen SID látható az elsődleges helyen, de a multisid (MCOS) is támogatott.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Single-node-with-dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | HLI-ügyfél |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | HLI-ügyfél |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|/hana/shared/SID | HANA-telepítés SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése SID-hez | 
|/hana/logbackups/SID | SID-naplók visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memóriában lévő adatbázis méretétől függ. Tekintse át az [Áttekintés és az architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) szakaszt, amelyből megtudhatja, hogy az multisid-környezet hogyan támogatja a memóriában lévő adatbázisok méretét.
- A DR: a kötetek és a csatolási konfigurálása (a HANA telepítéséhez szükséges ") a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- A DR: az adatok, a logbackups és a megosztott kötetek (a "tárolási replikáció" jelöléssel) az üzemi helyről pillanatképen keresztül replikálódnak. Ezek a kötetek csak a feladatátvételi idő alatt vannak csatlakoztatva. További információért olvassa el a következő dokumentumot: vész- [helyreállítási feladatátvételi eljárás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) további részletekért.
- Az I. **típusú SKU** rendszerindító kötetét a rendszer a Dr csomópontba replikálja.


## <a name="4-single-node-with-dr-multipurpose-using-storage-replication"></a>4. egyetlen csomópont DR (többcélú) tároló-replikáció használatával
 
Ez a topológia támogatja az egyik csomópontot egy vertikális Felskálázási konfigurációban egy vagy több SID-vel, és az elsődleges biztonsági azonosítóhoz tartozó, Storage-alapú replikációt a DR-helyre. A diagramon csak egyetlen SID látható az elsődleges helyen, de a multisid (MCOS) is támogatott. A DR helyen a HLI egységet a QA-példányhoz használja a rendszer, miközben az elsődleges helyről fut az üzemi művelet. A DR feladatátvétel (vagy feladatátvételi teszt) idején a DR helyen található QA-példány le lesz hajtva.

### <a name="architecture-diagram"></a>Architektúradiagram  

![single-node-with-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | HLI-ügyfél |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | HLI-ügyfél |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR helyen**|
|/hana/shared/SID | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/shared/QA-SID | HANA-telepítés a QA SID-hez | 
|/hana/data/QA-SID/mnt00001 | Az adatfájlok telepítése a QA SID-re | 
|/hana/log/QA-SID/mnt00001 | A naplófájlok telepítése a QA SID-re |
|/hana/logbackups/QA-SID | A QA SID naplóinak visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memóriában lévő adatbázis méretétől függ. Tekintse át az [Áttekintés és az architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) szakaszt, amelyből megtudhatja, hogy az multisid-környezet hogyan támogatja a memóriában lévő adatbázisok méretét.
- A DR: a kötetek és a csatolási konfigurálása (a HANA telepítéséhez szükséges ") a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- A DR: az adatok, a logbackups és a megosztott kötetek (a "tárolási replikáció" jelöléssel) az üzemi helyről pillanatképen keresztül replikálódnak. Ezek a kötetek csak a feladatátvételi idő alatt vannak csatlakoztatva. További információért olvassa el a következő dokumentumot: vész- [helyreállítási feladatátvételi eljárás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) további részletekért. 
- A DR: The logbackups, a log, a QA megosztott kötetei ("QA példány telepítése" jelöléssel jelölt) a QA példány telepítésére vannak konfigurálva.
- Az I. **típusú SKU** rendszerindító kötetét a rendszer a Dr csomópontba replikálja.

## <a name="5-hsr-with-stonith-for-high-availability"></a>5. HSR és STONITH a magas rendelkezésre állás érdekében
 
Ez a topológia két csomópontot támogat a HANA System Replication (HSR) konfigurációhoz. Ez a konfiguráció csak egyetlen HANA-példány esetén támogatott a csomóponton. Azt jelenti, hogy a MCOS-forgatókönyvek nem támogatottak.

**Mostantól ez az architektúra csak SUSE operációs rendszer esetén támogatott.**


### <a name="architecture-diagram"></a>Architektúradiagram  

![HSR-with-STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | HLI-ügyfél |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | A STONITH esetében használatos |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | HLI-ügyfél |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | A STONITH esetében használatos |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton**|
|/hana/shared/SID | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A másodlagos csomóponton**|
|/hana/shared/SID | HANA-telepítés másodlagos SID-hez | 
|/hana/data/SID/mnt00001 | Másodlagos SID-re telepítendő adatfájlok | 
|/hana/log/SID/mnt00001 | A naplófájlok másodlagos SID-re való telepítése | 
|/hana/logbackups/SID | Másodlagos SID-naplók visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memóriában lévő adatbázis méretétől függ. Tekintse át az [Áttekintés és az architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) szakaszt, amelyből megtudhatja, hogy az multisid-környezet hogyan támogatja a memóriában lévő adatbázisok méretét.
- STONITH: egy SBD van konfigurálva a STONITH-telepítőhöz. A STONITH használata azonban nem kötelező.


## <a name="6-high-availability-with-hsr-and-dr-with-storage-replication"></a>6. magas rendelkezésre állás a HSR és a DR tároló-replikálással
 
Ez a topológia két csomópontot támogat a HANA System Replication (HSR) konfigurációhoz. A normál és a többcélú DR is támogatott. Ezek a konfigurációk csak egyetlen HANA-példány esetén támogatottak egy csomóponton. Azt jelenti, hogy ezekhez a konfigurációkhoz nem támogatottak a MCOS-forgatókönyvek.

A diagramon a többcélú forgatókönyv látható, ahol a DR helyen a HLI egységet használja a QA-példányhoz, miközben az elsődleges helyről fut az üzemi művelet. A DR feladatátvétel (vagy feladatátvételi teszt) idején a DR helyen található QA-példány le lesz hajtva. 



### <a name="architecture-diagram"></a>Architektúradiagram  

![HSR-with-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | HLI-ügyfél |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | A STONITH esetében használatos |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | HLI-ügyfél |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | A STONITH esetében használatos |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges hely elsődleges csomópontján**|
|/hana/shared/SID | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**Az elsődleges hely másodlagos csomópontján**|
|/hana/shared/SID | HANA-telepítés másodlagos SID-hez | 
|/hana/data/SID/mnt00001 | Másodlagos SID-re telepítendő adatfájlok | 
|/hana/log/SID/mnt00001 | A naplófájlok másodlagos SID-re való telepítése | 
|/hana/logbackups/SID | Másodlagos SID-naplók visszaállítása |
|**A DR helyen**|
|/hana/shared/SID | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/shared/QA-SID | HANA-telepítés a QA SID-hez | 
|/hana/data/QA-SID/mnt00001 | Az adatfájlok telepítése a QA SID-re | 
|/hana/log/QA-SID/mnt00001 | A naplófájlok telepítése a QA SID-re |
|/hana/logbackups/QA-SID | A QA SID naplóinak visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memóriában lévő adatbázis méretétől függ. Tekintse át az [Áttekintés és az architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) szakaszt, amelyből megtudhatja, hogy az multisid-környezet hogyan támogatja a memóriában lévő adatbázisok méretét.
- STONITH: egy SBD van konfigurálva a STONITH-telepítőhöz. A STONITH használata azonban nem kötelező.
- A DR: **két tárolási kötetre van szükség** az elsődleges és a másodlagos csomópont-replikációhoz.
- A DR: a kötetek és a csatolási konfigurálása (a HANA telepítéséhez szükséges ") a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- A DR: az adatok, a logbackups és a megosztott kötetek (a "tárolási replikáció" jelöléssel) az üzemi helyről pillanatképen keresztül replikálódnak. Ezek a kötetek csak a feladatátvételi idő alatt vannak csatlakoztatva. További információért olvassa el a következő dokumentumot: vész- [helyreállítási feladatátvételi eljárás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) további részletekért. 
- A DR: The logbackups, a log, a QA megosztott kötetei ("QA példány telepítése" jelöléssel jelölt) a QA példány telepítésére vannak konfigurálva.
- Az I. **típusú SKU** rendszerindító kötetét a rendszer a Dr csomópontba replikálja.


## <a name="7-host-auto-failover-11"></a>7. gazdagép automatikus feladatátvétele (1 + 1)
 
Ez a topológia két csomópontot támogat a gazdagép automatikus feladatátvételi konfigurációjában. A főkiszolgálóval/feldolgozói szerepkörrel rendelkező csomópontok egy része készenléti állapotban van. **Az SAP ezt a forgatókönyvet csak az S/4 HANA esetében támogatja.** További részletekért tekintse meg az OSS Megjegyzés "[2408419-SAP S/4HANA-multi-Node support](https://launchpad.support.sap.com/#/notes/2408419)" című szakaszát.



### <a name="architecture-diagram"></a>Architektúradiagram  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | HLI-ügyfél |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópontok közötti kommunikáció |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | HLI-ügyfél |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Csomópontok közötti kommunikáció |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**A fő és a készenléti csomópontokon**|
|/hana/shared | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |



### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- Készenléti állapotban: a kötetek és a csatolási konfigurálása (a HANA-telepítéshez szükséges) a HANA-példány telepítéséhez a készenléti egységben.
 

## <a name="8-scale-out-with-standby"></a>8. kibővíthető készenléti állapottal
 
Ez a topológia több csomópontot támogat egy kibővíthető konfigurációban. Létezik egy főkiszolgálói szerepkörrel rendelkező csomópont, egy vagy több, feldolgozói szerepkörrel rendelkező csomópont, valamint egy vagy több csomópont készenléti állapotban. Az adott időpontban azonban csak egyetlen fő csomópont lehet.


### <a name="architecture-diagram"></a>Architektúradiagram  

![scaleout-nm-standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | HLI-ügyfél |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópontok közötti kommunikáció |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | HLI-ügyfél |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Csomópontok közötti kommunikáció |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**A fő, a feldolgozói és a készenléti csomópontok**|
|/hana/shared | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |


## <a name="9-scale-out-without-standby"></a>9. vertikális felskálázás készenlét nélkül
 
Ez a topológia több csomópontot támogat egy kibővíthető konfigurációban. Létezik egy főkiszolgálói szerepkörrel rendelkező csomópont, és egy vagy több, feldolgozói szerepkörrel rendelkező csomópont. Az adott időpontban azonban csak egyetlen fő csomópont lehet.


### <a name="architecture-diagram"></a>Architektúradiagram  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | HLI-ügyfél |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópontok közötti kommunikáció |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | HLI-ügyfél |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Csomópontok közötti kommunikáció |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**A fő-és munkavégző csomópontokon**|
|/hana/shared | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása

## <a name="10-scale-out-with-dr-using-storage-replication"></a>10. méretezés a DR-ben a Storage-replikáció használatával
 
Ez a topológia több csomópontot támogat a kibővíthető DR-ben. A normál és a többcélú DR is támogatott. A diagramon csak az egyetlen cél DR látható. Ezt a topológiát készenléti csomóponttal vagy anélkül is kérheti.


### <a name="architecture-diagram"></a>Architektúradiagram  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | HLI-ügyfél |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópontok közötti kommunikáció |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | HLI-ügyfél |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Csomópontok közötti kommunikáció |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton**|
|/hana/shared | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR csomóponton**|
|/hana/shared | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
-  A DR: a kötetek és a csatolási konfigurálása (a HANA telepítéséhez szükséges ") a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- A DR: az adatok, a logbackups és a megosztott kötetek (a "tárolási replikáció" jelöléssel) az üzemi helyről pillanatképen keresztül replikálódnak. Ezek a kötetek csak a feladatátvételi idő alatt vannak csatlakoztatva. További információért olvassa el a következő dokumentumot: vész- [helyreállítási feladatátvételi eljárás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) további részletekért. 
- Az I. **típusú SKU** rendszerindító kötetét a rendszer a Dr csomópontba replikálja.


## <a name="11-single-node-with-dr-using-hsr"></a>11. egyetlen csomópont a DR használatával HSR
 
Ez a topológia támogatja az egyik csomópontot egy vertikális Felskálázási konfigurációban egy biztonsági azonosítóval, és a HANA rendszer replikálása a DR-helyre az elsődleges biztonsági azonosítóhoz. A diagramon csak egyetlen SID látható az elsődleges helyen, de a multisid (MCOS) is támogatott.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Single-Node-HSR-Dr-111. png](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI/HSR |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | Ügyfél – HLI/HSR |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak a HLI egységeken (elsődleges és DR):

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|/hana/shared/SID | HANA-telepítés SID-hez | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése SID-hez | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése SID-hez | 
|/hana/logbackups/SID | SID-naplók visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memóriában lévő adatbázis méretétől függ. Tekintse át az [Áttekintés és az architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) szakaszt, amelyből megtudhatja, hogy az multisid-környezet hogyan támogatja a memóriában lévő adatbázisok méretét.
- Az elsődleges csomópont beolvassa a DR csomópontot a HANA rendszerreplikáció használatával. 
- A [Global REACH](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) a ExpressRoute-áramkörök összekapcsolására szolgál, hogy magánhálózat legyen a régiók hálózata között.



## <a name="12-single-node-hsr-to-dr-cost-optimized"></a>12. egyetlen csomópontos HSR a DR (Cost optimalizált) 
 
 Ez a topológia támogatja az egyik csomópontot egy vertikális Felskálázási konfigurációban egy biztonsági azonosítóval, és a HANA rendszer replikálása a DR-helyre az elsődleges biztonsági azonosítóhoz. A diagramon csak egyetlen SID látható az elsődleges helyen, de a multisid (MCOS) is támogatott. A DR helyen a HLI egységet a QA-példányhoz használja a rendszer, miközben az elsődleges helyről fut az üzemi művelet. A DR feladatátvétel (vagy feladatátvételi teszt) idején a DR helyen található QA-példány le lesz hajtva.

### <a name="architecture-diagram"></a>Architektúradiagram  

![Single-Node-HSR-Dr-Cost-Optimized-121. png](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI/HSR |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | Ügyfél – HLI/HSR |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR helyen**|
|/hana/shared/SID | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|/hana/shared/QA-SID | HANA-telepítés a QA SID-hez | 
|/hana/data/QA-SID/mnt00001 | Az adatfájlok telepítése a QA SID-re | 
|/hana/log/QA-SID/mnt00001 | A naplófájlok telepítése a QA SID-re |
|/hana/logbackups/QA-SID | A QA SID naplóinak visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A MCOS esetében: a kötet méretének eloszlása a memóriában lévő adatbázis méretétől függ. Tekintse át az [Áttekintés és az architektúra](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) szakaszt, amelyből megtudhatja, hogy az multisid-környezet hogyan támogatja a memóriában lévő adatbázisok méretét.
- A DR: a kötetek és a csatolási konfigurálása (a Dr-helyen található "GYÁRTÁSIRENDELÉS-példány" jelöléssel) a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- A DR: The logbackups, a log, a QA megosztott kötetei ("QA példány telepítése" jelöléssel jelölt) a QA példány telepítésére vannak konfigurálva.
- Az elsődleges csomópont beolvassa a DR csomópontot a HANA rendszerreplikáció használatával. 
- A [Global REACH](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) a ExpressRoute-áramkörök összekapcsolására szolgál, hogy magánhálózat legyen a régiók hálózata között.

## <a name="13-high-availability-and-disaster-recovery-with-hsr"></a>13. magas rendelkezésre állás és vész-helyreállítási HSR 
 
 Ez a topológia két csomópontot támogat a HANA System Replication (HSR) konfigurációhoz a helyi régiók magas rendelkezésre állása érdekében. DR esetében a DR régió harmadik csomópontja a HSR (aszinkron mód) használatával szinkronizál az elsődleges helyről. 

### <a name="architecture-diagram"></a>Architektúradiagram  

![Hana-System-Replication-Dr-131. png](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI/HSR |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | Ügyfél – HLI/HSR |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR helyen**|
|/hana/shared/SID | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A DR: a kötetek és a csatolási konfigurálása ("PROD DR-példány") a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- Az elsődleges hely csomópontja a HANA rendszerreplikáció használatával kap szinkronizálást DR-csomóponttal. 
- A [Global REACH](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) a ExpressRoute-áramkörök összekapcsolására szolgál, hogy magánhálózat legyen a régiók hálózata között.

## <a name="14-high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>14. magas rendelkezésre állás és vész-helyreállítási HSR (Cost optimalizált)
 
 Ez a topológia két csomópontot támogat a HANA System Replication (HSR) konfigurációhoz a helyi régiók magas rendelkezésre állása érdekében. A DR régióban a DR régió harmadik csomópontja a HSR (aszinkron mód) használatával szinkronizál az elsődleges helyről, egy másik példányban (például:). QA) már fut a DR csomóponton. 

### <a name="architecture-diagram"></a>Architektúradiagram  

![Hana-System-Replication-Dr-Cost-Optimized-141. png](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI/HSR |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Konfigurálva, de nincs használatban |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | Ügyfél – HLI/HSR |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Konfigurálva, de nincs használatban |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges helyen**|
|/hana/shared/SID | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR helyen**|
|/hana/shared/SID | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|/hana/shared/QA-SID | HANA-telepítés a QA SID-hez | 
|/hana/data/QA-SID/mnt00001 | Az adatfájlok telepítése a QA SID-re | 
|/hana/log/QA-SID/mnt00001 | A naplófájlok telepítése a QA SID-re |
|/hana/logbackups/QA-SID | A QA SID naplóinak visszaállítása |

### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A DR: a kötetek és a csatolási konfigurálása ("PROD DR-példány") a DR HLI egységben a termelési HANA-példány telepítéséhez. 
- A DR: The logbackups, a log, a QA megosztott kötetei ("QA példány telepítése" jelöléssel jelölt) a QA példány telepítésére vannak konfigurálva.
- Az elsődleges hely csomópontja a HANA rendszerreplikáció használatával kap szinkronizálást DR-csomóponttal. 
- A [Global REACH](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) a ExpressRoute-áramkörök összekapcsolására szolgál, hogy magánhálózat legyen a régiók hálózata között.

## <a name="15-scale-out-with-dr-using-hsr"></a>15. felskálázás DR használatával HSR
 
Ez a topológia több csomópontot támogat a kibővíthető DR-ben. Ezt a topológiát készenléti csomóponttal vagy anélkül is kérheti. Az elsődleges hely csomópontjai a DR hely csomópontjaival szinkronizálják a HANA rendszerreplikációt (aszinkron mód).


### <a name="architecture-diagram"></a>Architektúradiagram  

[![scale-out-Dr-HSR-151. png](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
A következő hálózati adapterek előre konfigurálva vannak:

| NIC LOGIKAI FELÜLETEK | SKU TÍPUSA | Név SUSE operációs rendszerrel | Név RHEL operációs rendszerrel | Használati eset|
| --- | --- | --- | --- | --- |
| A | TÍPUS | eth0. Bérlő | eno1. Bérlő | Ügyfél – HLI/HSR |
| B | TÍPUS | eth2. Bérlő | eno3. Bérlő | Csomópontok közötti kommunikáció |
| C# | TÍPUS | eth1. Bérlő | eno2. Bérlő | Csomópont – tárterület |
| D | TÍPUS | eth4. Bérlő | eno4. Bérlő | Konfigurálva, de nincs használatban |
| A | II. TÍPUS | VLAN\<tenantNo > | team0. Bérlő | Ügyfél – HLI/HSR |
| B | II. TÍPUS | VLAN\<tenantNo + 2 > | team0. Bérlő + 2 | Csomópontok közötti kommunikáció |
| C# | II. TÍPUS | VLAN\<tenantNo + 1 > | team0. Bérlő + 1 | Csomópont – tárterület |
| D | II. TÍPUS | VLAN\<tenantNo + 3 > | team0. Bérlő + 3 | Konfigurálva, de nincs használatban |

### <a name="storage"></a>Tárolás
A következő csatolási előre konfigurálva vannak:

| Csatlakoztatási pont | Használati eset | 
| --- | --- |
|**Az elsődleges csomóponton**|
|/hana/shared | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |
|**A DR csomóponton**|
|/hana/shared | A HANA telepítése üzemi SID-re | 
|/hana/data/SID/mnt00001 | Az adatfájlok telepítése a termelési biztonsági azonosítóhoz | 
|/hana/log/SID/mnt00001 | Naplófájlok telepítése üzemi SID-re | 
|/hana/logbackups/SID | Az üzemi SID-naplók visszaállítása |


### <a name="key-considerations"></a>Fő szempontok
- a/usr/sap/SID a/hana/shared/SID. szimbolikus hivatkozása
- A DR: The Volumes és csatolási konfigurálva van a DR HLI egységben található éles HANA-példány telepítéséhez. 
- Az elsődleges hely csomópontjain a HANA rendszerreplikáció használatával lehet szinkronizálni a DR-csomópontokkal. 
- A [Global REACH](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) a ExpressRoute-áramkörök összekapcsolására szolgál, hogy magánhálózat legyen a régiók hálózata között.


## <a name="next-steps"></a>Következő lépések
- A HLI [infrastruktúrájának és kapcsolatának](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) átirányítása
- [Magas rendelkezésre állás és vész-helyreállítási](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) HLI
