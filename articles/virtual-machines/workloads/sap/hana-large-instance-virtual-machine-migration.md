---
title: SAP HANA migrálása az Azure-ban (nagyméretű példányok) az Azure Virtual Machines szolgáltatásba | Microsoft Docs
description: SAP HANA migrálása az Azure-ban (nagyméretű példányok) Azure-beli virtuális gépekre
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dd01bf60104939fcf1f9bd1ccec0e7d72710041
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154919"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA Azure-beli nagyméretű példányok áttelepítése az Azure-ba Virtual Machines
Ez a cikk a nagyméretű Azure-példányok lehetséges üzembe helyezési forgatókönyveit ismerteti, és tervezési és áttelepítési módszert kínál a lehető legkevesebb átmeneti állásidő

## <a name="overview"></a>Áttekintés
A SAP HANA (HLI) Azure nagyméretű példányainak bejelentése óta a 2016-es verzióban számos ügyfél elfogadta ezt a hardvert a memóriában tárolt számítási platform szolgáltatásként.  Az elmúlt években az Azure-beli virtuálisgép-méretezési bővítmény a HANA-kibővített üzembe helyezési modell támogatásával együtt túllépte a legtöbb nagyvállalati ügyfél ERP-adatbázisának kapacitás iránti igényét.  Kezdjük azon ügyfeleinket látni, akik a SAP HANA számítási feladatok fizikai kiszolgálókról Azure-beli virtuális gépekre való áttelepítésére vágynak.
Ez az útmutató nem egy lépésenkénti konfigurációs dokumentum, hanem leírja a közös üzembe helyezési modelleket, és megtervezi, áttelepítési javaslatot tesz arra, hogy meghívja a szükséges szempontokat az átmeneti állásidő csökkentése érdekében.

## <a name="assumptions"></a>Feltételezések
Ez a cikk feltételezésekre a következő:
- Az egyetlen megfontolandó érdek egy homogén HANA-adatbázis számítási szolgáltatásának áttelepítése a Hana Large instance (HLI)-ből az Azure virtuális gépre jelentős szoftverfrissítés vagy javítás nélkül. Ezek a kisebb frissítések a megfelelő SAP-megjegyzések által támogatottnál újabb operációsrendszer-verzió vagy HANA-verzió használatát tartalmazzák. 
- Az összes frissítést/frissítést, ha szükséges, az áttelepítés előtt vagy után el kell végezni.  Például SAP HANA MCOS konvertálása MDC-telepítésre. 
- A lehető legkevesebb állásidőt biztosító áttelepítési megközelítés SAP HANA a rendszer replikálását. A többi áttelepítési módszer nem része a dokumentum hatókörének.
- Ez a HLI Rev3 és Rev4 SKU-ra is érvényes.
- A HANA üzembe helyezési architektúrája elsősorban változatlan marad az áttelepítés során.  Ez azt eredményezi, hogy egy olyan rendszer, amelynek a DR egyetlen példánya van, ugyanúgy fog maradni a célhelyen.
- Az ügyfelek áttekintették és megértették a cél (-be) architektúrájának szolgáltatói szerződés (SLA). 
- A HLIs és a virtuális gépek közötti kereskedelmi feltételek különbözősége miatt az ügyfeleknek a költséghatékonyságot kell figyelniük a virtuális gépek használatáról.
- Az ügyfelek tudomásul veszik és elismerik, hogy a HLI dedikált számítási platform. A virtuális gépek azonban megosztott infrastruktúrán futnak, amely a többi bérlőtől biztonságos és elkülönített.
- Az ügyfelek ellenőrizték, hogy a cél virtuális gépek támogatják a kívánt architektúrát. Ha meg szeretné tekinteni az összes SAP HANA-telepítésre tanúsított támogatott virtuálisgép-SKU-t, tekintse meg a [SAP HANA hardveres könyvtárat](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Az ügyfelek ellenőrizték a tervezési és áttelepítési tervet.
- Tervezze meg a vész-helyreállítási csomópontot az elsődleges hellyel együtt.  Az ügyfelek nem fogják tudni használni a HLI DR csomópontját az áttelepítés után virtuális gépeken futó elsődleges helyhez.
- Az ügyfelek az üzleti helyreállítás és a megfelelőségi követelmények alapján másolták a szükséges biztonságimásolat-fájlokat a cél virtuális gépekre. Az áttérési időszak alatt az adott időponthoz tartozó helyreállításra van szükség.
- Ha a HSR, az ügyfeleknek be kell állítania és konfigurálniuk kell a STONITH-eszközt a [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) és a [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)-hez SAP HANA ha útmutatók.  Nincs előre konfigurálva, mint a HLI eset.
- Ez az áttelepítési módszer nem fedi le a Optane-konfigurációval rendelkező HLI SKU-ket.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek
A HLI-ügyfelekkel közös üzembe helyezési modelleket az alábbi táblázat foglalja össze.  Az Azure-beli virtuális gépekre való Migrálás minden HLI-forgatókönyv esetén lehetséges.  Néhány forgatókönyv esetében előfordulhat, hogy kisebb építészeti módosításokra van szükség az aktuális Azure-szolgáltatások előnyeinek megismeréséhez.

| Forgatókönyv azonosítója | HLI forgatókönyv | Áttelepítés a Verbatim virtuális gépre? | Megjegyzés |
| --- | --- | --- | --- |
| 1 | [Egyetlen csomópont egyetlen SID-vel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Igen | - |
| 2 | [Egyetlen csomópont a MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Igen | - |
| 3 | [Önálló csomópont a Storage-replikációt használó DR használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Nem | A Storage-replikáció nem érhető el az Azure Virtual platformon, és nem változtathatja meg a jelenlegi DR-megoldást HSR vagy biztonsági mentésre vagy visszaállításra |
| 4 | [Egyetlen csomópont DR (többcélú) tároló-replikáció használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Nem | A Storage-replikáció nem érhető el az Azure Virtual platformon, és nem változtathatja meg a jelenlegi DR-megoldást HSR vagy biztonsági mentésre vagy visszaállításra |
| 5 | [HSR és STONITH a magas rendelkezésre állás érdekében](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Igen | Nincsenek előre konfigurált SBD a cél virtuális gépekhez.  Válasszon ki és helyezzen üzembe egy STONITH-megoldást.  Lehetséges beállítások: Azure vívó Agent ( [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)), SBD |
| 6 | [HA a HSR, a DR és a Storage replikációja](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Nem | Cserélje le a DR HSR vagy a Backup/Restore szolgáltatáshoz tartozó tárolási replikációt |
| 7 | [Gazdagép automatikus feladatátvétele (1 + 1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Igen | ANF használata megosztott tárolóhoz Azure-beli virtuális gépekkel |
| 8 | [Kibővíthető készenléti állapottal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Igen | BW/4HANA a M128s, a M416s, a M416ms virtuális gépek és a ANF használatával |
| 9 | [Vertikális felskálázás készenlét nélkül](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Igen | BW/4HANA a M128s, a M416s és a M416ms virtuális gépekkel (a Storage-ANF használatával vagy anélkül) |
| 10 | [Méretezés a DR használatával a Storage replikálásával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Nem | Cserélje le a DR HSR vagy a Backup/Restore szolgáltatáshoz tartozó tárolási replikációt |
| 11 | [Egyetlen csomópont a DR használatával HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Igen | - |
| 12 | [Egyetlen csomópontos HSR a DR (Cost optimalizált)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Igen | - |
| 13 | [HA és DR a HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Igen | - |
| 14 | [HA és DR a HSR (Cost optimalizált)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Igen | - |
| 15 | [Vertikális felskálázás DR használatával HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Igen | BW/4HANA a M128s. M416s, M416ms virtuális gépek (ANF használatával vagy anélkül) |


## <a name="source-hli-planning"></a>Forrás (HLI) tervezése
Egy HLI-kiszolgáló bevezetéséhez a Microsoft Service Management és az ügyfelek is megtervezték a számítási, hálózati, tárolási és operációsrendszer-specifikus beállításokat a SAP HANA adatbázis futtatásához.  Hasonló tervezésre van szükség az Azure-beli virtuális gépre való Migrálás során.

### <a name="sap-hana-housekeeping"></a>SAP HANA háztartás 
A HANA-adatbázis migrálása előtt érdemes kialakítani az adatbázis tartalmát, hogy a nemkívánatos, elavult vagy elavult naplók ne legyenek áttelepítve az új adatbázisba.  A háztartások általánosságban a régi, lejárt vagy inaktív adatmennyiségek törlését vagy archiválását is magukban foglalják.  Ezért ezeket az "adathigiéniai" műveleteket nem üzemi rendszerekben kell tesztelni, hogy az adatok éles környezetben való használatának érvényessége megtörténjen.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Hálózati kapcsolat engedélyezése az új virtuális gépek és a virtuális hálózatok számára 
Az ügyfél HLI üzembe helyezése során az Azure virtuális hálózatok hálózati kapcsolata a [SAP HANA (nagyméretű példányok) hálózati architektúrája](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)című cikkben ismertetett információk alapján lett létrehozva. Emellett a hálózati forgalom útválasztását az "útválasztás az Azure-ban" szakaszban ismertetett módon végezheti el.
1. Ha új virtuális GÉPET kíván beállítani az áttelepítés HSR céljaként, ha az új Azure virtuális gépet olyan IP-címtartományok használatával helyezi el a meglévő virtuális hálózatban, amely már rendelkezik engedéllyel a HLI-csomóponthoz való csatlakozáshoz, nincs szükség további HLI-kapcsolat frissítésére.
2. Ha az új Azure-beli virtuális gép egy új virtuális hálózatba kerül (esetleg egy másik régióban lehet), és a meglévő virtuális hálózattal van társítva, akkor az eredeti HLI-kiépítés ExpressRoute-szolgáltatási kulcsa és erőforrás-azonosítója használható az új virtuális hálózati IP-címtartomány elérésének engedélyezéséhez.  Hangolja össze a Microsoft Service Management szolgáltatást, hogy a virtuális hálózat HLI a kapcsolatot.  Megjegyzés: az alkalmazás és az adatbázis rétegek közötti hálózati késés csökkentése érdekében az alkalmazás-és az adatbázis-rétegnek ugyanazon a virtuális hálózaton kell lennie.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group"></a>Meglévő alkalmazási réteg rendelkezésre állási készlete, Availability Zones és közelsége elhelyezési csoport
A jelenlegi üzembe helyezési modell bizonyos szolgáltatási szintű célkitűzések kielégítése érdekében történik.  Ebben a mozgásban győződjön meg arról, hogy a cél-infrastruktúra megfelel vagy meghaladja a beállított célokat.  
Valószínűleg nem, az ügyfelek SAP-alkalmazáskiszolgáló egy rendelkezésre állási csoportba kerül.  Ha a jelenlegi központi telepítési szolgáltatási szint kielégítő, és 
- Ha a SAP HANA-kiszolgáló cseréjét névfeloldás útján végzi el az adatbázis-állomásnév IP-címeinek cseréjével, semmi további teendőt nem kell végrehajtania.  
- Ha nem a PPG-t használja, ügyeljen arra, hogy az összes alkalmazás-és adatbázis-kiszolgálót ugyanabban a zónában helyezze el, hogy csökkentse a hálózati késést.
- Ha a PPG-t használja, tekintse meg a jelen dokumentum célhelyének megtervezése, rendelkezésre állási csoport, Availability Zones és a közelség elhelyezése (PPG) című szakaszt.

### <a name="storage-replication-discontinuance-process-if-used"></a>Tárolási replikálás megszakítási folyamata (ha használatban van)
Ha a tárolási replikáció DR-megoldásként van használatban, a replikálást le kell állítani (nem ütemezett) az SAP-alkalmazás leállítása után, az utolsó SAP HANA katalógust, a naplófájlt, az adatbiztonsági másolatokat a távoli tároló köteteire replikálta a rendszer.  Ez a művelet egy elővigyázatossági taktika, amely szerint a DR HLI aktuális adatbázisa a fizikai és az Azure-beli virtuális gépek közötti áttérés során vész végbe.

### <a name="data-backups-preservation-consideration"></a>Az adatbiztonsági másolatok megőrzésének megfontolása
Miután az Azure-beli virtuális gépen SAP HANA a átváltás, a HLI lévő összes pillanatkép-alapú vagy naplózott biztonsági másolat nem könnyen hozzáférhető vagy a virtuális géphez állítható be, ha szükséges. A korai átmeneti időszakra az Azure-alapú biztonsági mentés elég előzményeket hoz létre az időponthoz tartozó helyreállítási követelmények kielégítése érdekében, a pillanatképek mellett a HLI, napokat és heteket is figyelembe kell venni a átváltás előtt.  Ezeket a biztonsági másolatokat az új SAP HANA virtuális gép által elérhető Azure Storage-fiókba másolta. A HLI-tartalom biztonsági mentésén kívül az is körültekintően működik, hogy az SAP-környezet teljes biztonsági másolatai azonnal elérhetők legyenek, ha visszaállításra van szükség.

### <a name="adjusting-system-monitoring"></a>A rendszer figyelésének módosítása 
Az ügyfelek számos különböző eszközt használnak az SAP-környezetben lévő rendszerekre vonatkozó riasztási értesítések figyelésére és küldésére.  Ez az érték csupán egy egyszerű hívás a megfelelő művelethez, amikor az új virtuális gép (ek) hozzáadásával a figyeléshez és a riasztási értesítés címzettjeihez kapcsolódóan szükség van a riasztási értesítések fogadására.

### <a name="microsoft-operations-team-involvement"></a>A Microsoft Operations Team bevonása 
Nyisson meg egy jegyet a Azure Portal a meglévő HLI-példányon.  A támogatási jegy létrehozása után a támogatási szakember e-mailben fogja felvenni Önnel a kapcsolatot.  

### <a name="engage-microsoft-account-team"></a>Microsoft-fiókkal foglalkozó csapat bevonása
Tervezze meg az áttelepítést a HLI-szerződés évfordulós megújítási ideje felé, hogy csökkentse a számítási erőforrásokra vonatkozó felesleges terheket. A HLI panel leszereléséhez szükség van a szerződés lezárásának és az egység tényleges leállásának koordinálására.

## <a name="destination-planning"></a>Célhely tervezése
Egy új infrastruktúra kiépítésével egy meglévőt kell megérdemelni annak érdekében, hogy az új Hozzáadás a dolgok nagy rendjében is illeszkedjen.  Az alábbiakban néhány kulcsfontosságú pontot talál a szemlélődés számára.

### <a name="resource-availability-in-the-target-region"></a>Erőforrás rendelkezésre állása a célként megadott régióban 
A jelenlegi SAP-alkalmazáskiszolgáló központi telepítési régiója jellemzően a társított HLIs van közel.  A HLIs azonban a rendelkezésre álló Azure-régióknál kevesebb helyen kínáljuk.  A fizikai HLI az Azure-beli virtuális gépre való Migrálás során a teljesítmény optimalizálása érdekében az összes kapcsolódó szolgáltatás közelségi távolságát mutatja be.  Ennek során kulcsfontosságú szempont lenne annak biztosítása, hogy a kiválasztott régió rendelkezik a fontos erőforrásokkal.  Például egy adott virtuálisgép-család rendelkezésre állása, vagy az Azure zónák ajánlata magas rendelkezésre állású megfontoláshoz.

### <a name="virtual-network"></a>Virtuális hálózat 
Az infrastruktúra-architektúra arra lesz kiválasztva, hogy az új HANA-adatbázist a meglévő SAP-alkalmazás virtuális hálózatán belül futtatja-e, vagy újat kíván létrehozni.  Az elsődleges döntési tényező az SAP-környezet aktuális hálózati elrendezése.  Abban az esetben, ha a központi telepítési infrastruktúra megváltozása megtörténik, például az egyik zónából a két zónából álló üzembe helyezést és a PPG előnyeit. Ez a rendelkezésre állási fejlesztés az építészeti változásokat veti fel. További információkért tekintse [meg az Azure PPG optimális hálózati késése az SAP-alkalmazással](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)című cikket.   

### <a name="security"></a>Biztonság
Azt jelzi, hogy az új SAP HANA virtuális gép új vagy meglévő vnet/alhálózaton van-e, egy új, üzleti szempontból kritikus fontosságú szolgáltatást jelent, amely védelmet igényel.  A megfelelő hozzáférés-vezérlést az új szolgáltatási osztályra vonatkozó vállalati adatok biztonsági szabályzatának megfelelően értékelni és telepíteni kell. 

### <a name="vm-sizing-recommendation"></a>VM-méretezési javaslat
Ez a Migrálás lehetőséget biztosít a HANA számítási motor megfelelő méretének kiszámítására.  A Hana Studióval együtt a HANA- [rendszernézeteket](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) is kihasználhatja a rendszererőforrás-felhasználás megismeréséhez, ami lehetővé teszi a jobb méretezést a kiadások hatékonyságának növelése érdekében.

### <a name="storage"></a>Tárterület 
A tárolási teljesítmény az SAP-alkalmazások felhasználói élményét befolyásoló tényezők egyike.  Egy adott VM-SKU-on alapuló alapszintű tárolási elrendezési javaslat [SAP HANA Azure-beli virtuális gépek tárolási konfigurációjában](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)van közzétéve. Javasoljuk, hogy tekintse át ezeket a minimális specifikációkat, és hasonlítsa össze a meglévő HLI rendszerstatisztikával, hogy biztosítsa az új HANA-beli virtuális gép megfelelő i/o-kapacitását és teljesítményét.

Ha úgy konfigurálja a PPG-t az új HANA-beli virtuális géphez és a hozzá tartozó alrendszerekhez, küldjön be egy támogatási jegyet a tároló és a HANA virtuális gép létrehozásakor közös elhelyezés ellenőrzéséhez és biztosításához.  
Mivel előfordulhat, hogy a biztonsági mentési megoldás módosítására van szükség, a tárolási költségeket is újra kell látni, hogy elkerülje a működési kiadások meglepetéseit. 

### <a name="storage-replication-for-disaster-recovery"></a>Tárolási replikáció a vész-helyreállításhoz
A HLI-ben a Storage-replikáció a HANA-adatbázis vész-helyreállítási replikálásának alapértelmezett beállításaként lett felkínálva. Ez a funkció nem az alapértelmezett beállítás az Azure virtuális gépen. Vegye fontolóra az üzleti igényeinek megfelelő HSR, biztonsági mentési/helyreállítási vagy más támogatott megoldásokat.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Rendelkezésre állási csoportok, Availability Zones és Proximity elhelyezési csoportok 
Tekintettel arra, hogy az alkalmazási réteg és a SAP HANA közötti távolság minimalizálása minimálisan megtartja a hálózati késést, az új adatbázis virtuális gépet és az aktuális SAP-alkalmazás-kiszolgálókat egy közeli elhelyezési csoportba (PPG) kell elhelyezni. Az SAP-környezetek eléréséhez az Azure-beli rendelkezésre állási [csoport](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) és a AVAILABILITY ZONES a PPG használatával foglalkozó témakörben talál további információt.
Ha a cél HANA rendszer tagjai több Azure-zónában vannak telepítve, akkor az ügyfeleknek egyértelmű képet kell kapniuk a kiválasztott zónák késési profiljáról. Az SAP-rendszerösszetevők elhelyezése optimális az SAP-alkalmazás és az adatbázis közötti proximális távolság tekintetében.  A nyilvános tartomány [rendelkezésre állási zónájának késési teszt eszköze](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) megkönnyíti a mérést.  


### <a name="backup-strategy"></a>Biztonsági mentési stratégia
Számos ügyfél már használ külső gyártótól származó biztonsági mentési megoldásokat a HLI-SAP HANA.  Ebben az esetben csak egy további védett virtuális gép és HANA-adatbázis konfigurálására van szükség.  A most már ütemezett HLI biztonsági mentési feladatok nem ütemezhetők, ha a gépet az áttelepítés után leszerelik.
A virtuális gépen SAP HANA Azure Backup már általánosan elérhető.  A következő hivatkozásokra kattintva részletes információkat talál: [biztonsági mentés](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [visszaállítás](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) [, SAP HANA biztonsági](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) mentés az Azure-beli virtuális gépeken.

### <a name="dr-strategy"></a>DR-stratégia
Ha a szolgáltatási szint célkitűzései a blob Storage-ba történő egyszerű biztonsági mentésének és visszaállításának, illetve egy új virtuális gépnek a hosszú helyreállítási idejét is kielégítik, akkor ez a legegyszerűbb és legdrágább DR-stratégia.  
A nagyméretű példány platformhoz hasonlóan, ahol a HANA DR jellemzően a HSR-szel együtt történik; Az Azure-beli virtuális gépen a HSR a legtermészetesebb és a natív SAP HANA DR-megoldás is.  Függetlenül attól, hogy a forrás üzembe helyezése egypéldányos-e, automatikus feladatátvételsel vagy anélkül fürtözött, vagy több csomópontos kibővített HANA, a DR régióban a forrás-infrastruktúra HSR replikájának megadása szükséges. Ez a HSR-cél DR-replika a virtuális gép áttelepítésének elsődleges HLI után lesz létrehozva.  A DR HANA-példány a virtuálisgép-példány elsődleges SAP HANA a másodlagos replikációs helyként regisztrálja.  

### <a name="sap-application-server-connectivity-destination-change"></a>Az SAP alkalmazáskiszolgáló kapcsolódási célhelyének változása
A HSR áttelepítési módszer egy új HANA DB-gazdagépet eredményez, ezért az alkalmazási réteg új adatbázis-állomásneve, az SAP-profilokat az új állomásnévnek megfelelően módosítani kell.  Ha a kapcsolót névfeloldással megőrzi a hostname, a profil módosítása nem szükséges.

### <a name="operating-system"></a>Operációs rendszer
A HLI és a virtuális gép operációsrendszer-lemezképei annak ellenére, hogy azonos kiadási szinten vannak, a SLES 11 SP4 például nem azonos. Az ügyfeleknek ellenőriznie kell a szükséges csomagokat, a gyors javításokat, a javításokat, a kernelt és a biztonsági javításokat a HLI, hogy ezek a cél virtuális gép operációs rendszerére is telepíthetők legyenek.  Emellett az ügyfelek számára gyakori, hogy az SAP-HSR a cél virtuális gépen a legújabb operációsrendszer-verziót konfigurálja.  Ez a [2763388 SAP-megjegyzésekben](https://launchpad.support.sap.com/#/notes/2763388)támogatott.

### <a name="new-sap-license-request"></a>Új SAP-licencelési kérelem
Egy egyszerű hívás, amely új SAP-licencet igényel az új HANA-rendszer számára, hogy áttelepítse a virtuális gépekre.

### <a name="service-level-agreement-sla-differences"></a>A szolgáltatói szerződés (SLA) közötti különbségek
A szerzők Szeretnék meghívni a HLI és az Azure-beli virtuális gép közötti rendelkezésre állási SLA különbségeit.  Például a fürtözött HLIs HA pár 99,99%-os rendelkezésre állást kínál. Ha ugyanazt a SLA-t szeretné elérni, egy virtuális gépet kell üzembe helyezni a rendelkezésre állási zónákban. Ez a [cikk](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) a kapcsolódó üzembe helyezési architektúrákkal való rendelkezésre állást ismerteti, hogy az ügyfelek ennek megfelelően tervezzék meg a cél infrastruktúrát.


## <a name="migration-strategy"></a>Migrálási stratégia
Ebben a dokumentumban csak a HANA rendszer replikációs megközelítését fedi le a HLI-ről az Azure virtuális gépre való áttelepítésre.  Az üzembe helyezett cél tárolási megoldástól függ, a folyamat némileg eltér. A magas szintű lépéseket az alábbiakban ismertetjük.

### <a name="vm-with-premiumultra-disks-for-data"></a>Virtuális gép Premium/Ultra-Disks szolgáltatással
A prémium vagy ultrakönnyű lemezekkel üzembe helyezett virtuális gépek esetében a rendszer a szabványos SAP HANA rendszerreplikálási konfigurációt alkalmazza, és a HSR beállítására is használható.  A hivatkozott [SAP-súgótémakör](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) áttekintést nyújt a rendszerreplikáció két rendszer közötti beállításának lépéseiről, a másodlagos rendszerre való átvételről, az elsődleges rendszer visszavonásáról, valamint a rendszerreplikáció letiltásáról.  Az áttelepítés céljából a HLI-forrás lépésein csak a telepítésre, a átvételre és a rendszerreplikáció letiltására lesz szükség.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>ANF-alapú virtuális gép adatés naplózási kötetekhez
Magas szinten a teljes adat-és naplózási kötetek legújabb HLI-tárolási pillanatképeit át kell másolni az Azure Storage-ba, ahol elérhetők és a cél HANA virtuális gép helyreállítható.  A másolási folyamat bármilyen natív linuxos másolási eszközzel elvégezhető.  

>[!Important]
> A másolási és az adatátviteli órák a HANA-adatbázis méretétől és a hálózati sávszélességtől függően változhatnak.  A másolási folyamat nagy részét az elsődleges HANA-adatbázis leállása előtt kell elvégezni.

### <a name="mcos-to-mdc-conversion"></a>MCOS – MDC átalakítás
Az egyik rendszerbeli (MCOS) telepítési modell több összetevőjét is kiválasztotta a HLI ügyfeleink közül.  A motiváció az volt, hogy megkerüljék a több adatbázis-tároló (MDC) tárolási pillanatképének korlátozását a korábbi SAP HANA verziók esetében.  A MCOS-modellben több független SAP HANA példány van halmozva egy HLI-panelen.  A HSR használata az áttelepítéshez a megfelelő működést eredményezi, így több HANA-beli virtuális gép mindegyike egy bérlői DB-vel.  Ez a végső állapot olyan üzleti környezetet tesz lehetővé, mint amit az ügyfél esetleg megszokt.  A SAP HANA 2,0 alapértelmezett üzembe helyezése a MDC, amely életképes alternatíva a [HANA-bérlők](https://launchpad.support.sap.com/#/notes/2472478) a HSR Migrálás utáni áthelyezéséhez.  Ezzel a folyamattal a független HANA-adatbázisokat egyetlen HANA-tárolóban egyesítheti egymással.  

### <a name="application-layer-consideration"></a>Alkalmazás rétegének megfontolása
Az adatbázis-kiszolgáló egy SAP-rendszer középpontjának tekinthető.  Az összes alkalmazás-kiszolgálónak a SAP HANA adatbázis közelében kell lennie.  Bizonyos esetekben, amikor a PPG új használata szükséges, a meglévő alkalmazás-kiszolgálók áthelyezése arra a PPG-ra, ahol a HANA virtuális gép szükség lehet.  Ha már rendelkezik üzembe helyezési sablonokkal, az új alkalmazás-kiszolgálók létrehozása egyszerűbbnek tekinthető.  
Ha a meglévő alkalmazás-kiszolgálók és az új HANA virtuális gép optimális helyen található, akkor nem szükséges új alkalmazás-kiszolgálókat létrehozni, kivéve ha további kapacitásra lenne szükség.  
Ha új infrastruktúra van felépítve a szolgáltatás rendelkezésre állásának növelésére, előfordulhat, hogy a meglévő alkalmazás-kiszolgálók szükségtelenek lesznek, és azokat le kell állítani és törölni kell.
Ha a cél virtuális gép állomásneve megváltozott, és eltér a HLI állomásnévtől, az SAP-alkalmazáskiszolgáló profiljait úgy kell beállítani, hogy az új gazdagépre mutasson.  Ha csak a HANA DB IP-címe módosult, a DNS-rekord frissítése szükséges ahhoz, hogy a bejövő kapcsolatokat az új HANA virtuális géphez lehessen vezetni.

### <a name="acceptance-test"></a>Elfogadási teszt
Bár a HLI-ről a virtuális gépre való Migrálás nem változtatja meg az adatbázis tartalmát a heterogén áttelepítéshez képest, továbbra is javasoljuk az új beállítás legfontosabb funkcióinak és teljesítményének érvényesítését.  

### <a name="cutover-plan"></a>Átváltás-csomag
Bár ez az áttelepítés egyenes előre, azonban egy meglévő adatbázis leszerelését is magában foglalja.  A forrásrendszer a kapcsolódó tartalommal való megőrzése és a biztonsági mentési lemezképek alapos megtervezése kritikus fontosságú abban az esetben, ha tartalék szükséges.  A jó tervezés gyorsabb megfordítást tesz lehetővé.   


## <a name="post-migration"></a>Áttelepítés utáni
Az áttelepítési feladatot addig nem hajtja végre, amíg az adatok integritásának megőrzése érdekében nem biztos, hogy biztonságosan leválasztott bármilyen HLI szolgáltatást vagy kapcsolatot.  Továbbá a szükségtelen szolgáltatások leállítása.  Ez a szakasz néhány legfontosabb elemet hív meg.

### <a name="decommissioning-the-hli"></a>A HLI leszerelése
Miután sikeresen áttelepíti a HANA-adatbázist az Azure-beli virtuális gépre, a HLI-ADATBÁZISon nem kell üzemi üzleti tranzakciókat végrehajtania.  Ha azonban a HLI a helyi biztonsági mentési adatmegőrzési időszaktal megegyező időtartamra tartja, egy biztonságos eljárás gondoskodik a gyorsabb adatok helyreállításáról, ha szükséges.  Az HLI panelt csak ezután szerelje le.  Amellett, hogy a technikai vállalkozás, az ügyfelek a legjobb érdekükben állnak, a Microsofttal HLI kötelezettségvállalásokat kell kötniük.  Az ügyfeleknek kapcsolatba kell lépniük a Microsoft képviselőivel, hogy a HLI leszerelési folyamaton keresztül működjenek.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Távolítsa el a HLI konfigurált bármely proxyt (pl.: iptables, BIGIP) 
Ha egy olyan proxy-szolgáltatás, amely egy iptables-hez hasonló, a helyszíni forgalom a HLI felé irányuló és onnan történő irányítására szolgál, a virtuális gépre való sikeres áttelepítés után már nincs szükség erre a szolgáltatásra.  Ezt a csatlakozási szolgáltatást azonban meg kell őrizni mindaddig, amíg a HLI panel továbbra is áll a leszerelési témakörben tárgyalt módon.  Ezt a szolgáltatást a HLI panel teljes leszerelése után lehet leállítani. 

### <a name="remove-global-reach-for-hli"></a>A HLI Global Reach eltávolítása 
A Global Reach általában az ügyfél ExpressRoute-átjárójának a HLI ExpressRoute-átjáróval való összekapcsolására szolgál, amely lehetővé teszi az ügyfél helyszíni forgalmát, hogy közvetlenül a proxy szolgáltatás igénye nélkül elérje a HLI-bérlőt.  Az iptables proxy szolgáltatáshoz hasonlóan a GlobalReach-t is meg kell őrizni, amíg a HLI panel teljes leszerelése meg nem történik.

### <a name="operating-system-subscription--movereuse"></a>Operációs rendszer előfizetése – áthelyezés/újrafelhasználás
Ahogy a virtuálisgép-kiszolgálók felálltak, és a HLI-pengék le vannak szerelve, az operációs rendszer előfizetéseit lecserélheti vagy újra felhasználhatja, hogy elkerülje az operációs rendszer licencének dupla kifizetését.



## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikkeket:
- [SAP HANA infrastruktúra-konfigurációk és-műveletek az Azure-](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)ban.
- [SAP-munkaterhelések az Azure-ban: tervezési és üzembe helyezési ellenőrzőlista](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
