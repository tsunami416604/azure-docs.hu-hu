---
title: SAP HANA áttelepítése az Azure-ban (nagy példányok) az Azure virtuális gépekre| Microsoft dokumentumok
description: Sap HANA áttelepítése az Azure-ban (nagy példányok) Az Azure virtuális gépekre
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd1267711871b3e55f1a6229e46ae27b360322f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617036"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA az Azure nagy példányáttelepítése az Azure virtuális gépek
Ez a cikk ismerteti a lehetséges Azure nagypéldány telepítési forgatókönyveket, és tervezési és áttelepítési megközelítést kínál a minimális átmeneti állásidővel

## <a name="overview"></a>Áttekintés
Az SAP HANA (HLI) azure-beli nagypéldányainak 2016 szeptemberi bejelentése óta számos ügyfél ezt a hardvert a memórián belüli számítási platformszolgáltatásként alkalmazta.  Az elmúlt években az Azure virtuális gép méretbővítménye a HANA kibővített telepítés támogatásával együtt meghaladta a legtöbb vállalati ügyfél ERP-adatbázis-kapacitásigényét.  Kezdjük látni az ügyfelek számára, hogy kifejezzék az SAP HANA számítási feladatuk fizikai kiszolgálókról Azure-beli virtuális gépekre való áttelepítésének érdeklődését.
Ez az útmutató nem lépésről lépésre konfigurálási dokumentum. Ismerteti a közös üzembe helyezési modellek és kínál tervezési és áttelepítési tanácsokat.  A szándék az, hogy hívja ki a szükséges szempontokat az átmeneti állásidő minimalizálása érdekében.

## <a name="assumptions"></a>Feltételezések
Ez a cikk a következő feltételezéseket teszi:
- Az egyetlen figyelembe vett érdeklődés egy homogén HANA-adatbázis számítási szolgáltatás áttelepítése hana nagy példány (HLI) az Azure virtuális gép jelentős szoftverfrissítés vagy javítás nélkül. Ezek a kisebb frissítések közé tartozik egy újabb operációsrendszer-verzió vagy HANA-verzió kifejezetten kijelentette, a megfelelő SAP-megjegyzések által támogatott használatával. 
- Minden frissítési/frissítési tevékenységet az áttelepítés előtt vagy után kell elvégezni.  Például az SAP HANA MCOS konvertálás a MDC központi telepítés. 
- Az áttelepítési megközelítés, amely a legkevesebb állásidőt kínálna az SAP HANA rendszerreplikáció. Más áttelepítési módszerek nem tartoznak a dokumentum hatálya alá.
- Ez az útmutató a HLI Rev3 és Rev4 ska-ira egyaránt vonatkozik.
- Hana telepítési architektúra elsősorban változatlan marad az áttelepítés során.  Ez azt, hogy egy rendszer egy példánydr marad ugyanúgy a célhelyen.
- Az ügyfelek áttekintették és megértették a cél (leendő) architektúra szolgáltatásiszint-szerződését (SLA). 
- A HLI-k és a virtuális gépek közötti kereskedelmi feltételek eltérőek. Az ügyfelek nek figyelniük kell a virtuális gépeik használatát a költségkezeléshez.
- Az ügyfelek megértik, hogy a HLI egy dedikált számítási platform, míg a virtuális gépek megosztott, mégis elszigetelt infrastruktúrán futnak.
- Az ügyfelek ellenőrizték, hogy a célvirtuális gépek támogatják a tervezett architektúrát. Az SAP HANA-telepítéshez hitelesített összes támogatott vm-termékkészlet megtekintéséhez tekintse meg az [SAP HANA hardverkönyvtárát.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
- Az ügyfelek ellenőrizték a tervezési és áttelepítési tervet.
- Tervezze meg a vész-helyreállítási virtuális gép az elsődleges hely mellett.  Az ügyfelek nem használhatják a HLI-t a virtuális gépeken futó elsődleges hely DR-csomópontjaként az áttelepítés után.
- Az ügyfelek az üzleti helyreállíthatóság és megfelelőségi követelmények alapján átmásolták a szükséges biztonsági másolatfájlokat a virtuális gépek megcélzására. Virtuális gép által elérhető biztonsági mentések, lehetővé teszi a point-in-time helyreállítás az átmeneti időszakban.
- A HSR HA esetében az ügyfeleknek sap HANA HA útmutatókonként kell beállítaniuk és konfigurálniuk a STONITH-eszközt [az SLES-hez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) és az [RHEL-hez.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)  Nincs előre konfigurálva, mint a HLI ügy.
- Ez az áttelepítési megközelítés nem terjed ki a HLI-sk Optane konfigurációval.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek
A HLI-ügyfelekkel közös üzembe helyezési modelleket az alábbi táblázat foglalja össze.  Az Azure-beli virtuális gépekre való áttelepítés az összes HLI-forgatókönyvhez lehetséges.  A rendelkezésre álló kiegészítő Azure-szolgáltatások előnyeinek kihasználása érdekében kisebb architekturális módosításokra lehet szükség.

| Forgatókönyv azonosítója | HLI forgatókönyv | Szó szerint áttelepül a vm-re? | Megjegyzés |
| --- | --- | --- | --- |
| 1 | [Egyetlen csomópont egyetlen SID-vel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Igen | - |
| 2 | [Egyetlen csomópont MCOS-szal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Igen | - |
| 3 | [Egyetlen csomópont a DR-rel a tárolóreplikáció használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Nem | A tárolási replikáció nem érhető el az Azure virtuális platformján, módosítsa a jelenlegi VÉSZ-megoldást HSR-re vagy biztonsági mentésre/visszaállításra |
| 4 | [Egyetlen csomópont DR-rel (többcélú) tárolóreplikációval](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Nem | A tárolási replikáció nem érhető el az Azure virtuális platformján, módosítsa a jelenlegi VÉSZ-megoldást HSR-re vagy biztonsági mentésre/visszaállításra |
| 5 | [HSR STONITH-tal a magas rendelkezésre állás érdekében](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Igen | Nincs előre konfigurált SBD a cél virtuális gépekhez.  STONITH-megoldás kiválasztása és üzembe helyezése.  Lehetséges lehetőségek: Azure Fencing Agent (támogatott mindkét [RHEL,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) [SLES),](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)SBD |
| 6 | [HA HSR-rel, DR tárolóreplikációval](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Nem | A DR-igényekre vonatkozó tárolóreplikáció cseréje HSR-re vagy biztonsági mentésre/visszaállításra |
| 7 | [Állomás automatikus feladatátvétele (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Igen | Anf használata megosztott tároláshoz az Azure virtuális gépeivel |
| 8 | [Horizontális felskálázás készenléti állapottal](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Igen | BW/4HANA M128-as, M416-os, M416ms-es virtuális gépekkel, amelyek csak tárolásra használják az ANF-et |
| 9 | [Horizontális felskálázás készenlét nélkül](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Igen | BW/4HANA M128-as, M416-os, M416ms-es virtuális gépekkel (ANF-fel vagy anélkül tárolásra) |
| 10 | [Horizontális felskálázás a DR-rel a tárolóreplikáció használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Nem | A DR-igényekre vonatkozó tárolóreplikáció cseréje HSR-re vagy biztonsági mentésre/visszaállításra |
| 11 | [Egyetlen csomópont DR-rel hsr használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Igen | - |
| 12 | [Egycsomópontos HSR–DR (költségoptimalizált)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Igen | - |
| 13 | [HA és DR HSR-rel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Igen | - |
| 14 | [HA és DR HSR-rel (költségoptimalizált)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Igen | - |
| 15 | [Horizontális felskálázás dr-rel hsr használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Igen | BW/4HANA M128-asokkal. M416s, M416ms virtuális gépek (ANF-fel vagy anélkül) |


## <a name="source-hli-planning"></a>Forrás (HLI) tervezése
HlI-kiszolgáló esetén a Microsoft Service Management és az ügyfelek is végigmentek az SAP HANA-adatbázis futtatásához szükséges számítási, hálózati, tárolási és operációsrendszer-specifikus beállítások tervezésén.  Hasonló tervezést kell lezajlaniaz Azure virtuális gépre való migráláshoz.

### <a name="sap-hana-housekeeping"></a>SAP HANA háztartás 
Célszerű az adatbázis tartalmát rendet adni, így a nem kívánt, elavult adatok vagy elavult naplók nem kerülnek át az új adatbázisba.  A takarítás általában régi, lejárt vagy inaktív adatok törlését vagy archiválását jelenti.  Ezeket az "adathigiéniai" intézkedéseket nem termelési rendszerekben kell vizsgálni, hogy a termelés megkezdése előtt érvényesítsék az adattrim érvényességüket.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Hálózati kapcsolat engedélyezése új virtuális gépekhez és virtuális hálózatokhoz 
Az ügyfél HLI-telepítésében a hálózat az [SAP HANA (Nagy példányok) hálózati architektúrájának](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)cikkében ismertetett információk alapján lett beállítva. Emellett a hálózati forgalom útválasztása az "Útválasztás az Azure-ban" című szakaszban ismertetett módon történik.
- Ha egy új virtuális gép beállítása az áttelepítési cél, Ha a meglévő virtuális hálózat ban található, ip-címtartományok már engedélyezett a HLI-hez való csatlakozáshoz, nincs szükség további kapcsolódási frissítésre.
- Ha az új Azure virtuális gép egy új Microsoft Azure virtuális hálózatba kerül, lehet, hogy egy másik régióban van, és a meglévő virtuális hálózattal társviszonyban van, az ExpressRoute szolgáltatáskulcs és az eredeti HLI-kiépítés erőforrásazonosítója használható az új virtuális hálózathoz való hozzáférés engedélyezéséhez. hálózati IP-tartományban.  Egyeztesse a Microsoft Service Management szolgáltatáskezelővel, hogy a virtuális hálózat hli kapcsolathoz is képes lesz.  Megjegyzés: Az alkalmazás és az adatbázisrétegek közötti hálózati késés minimalizálása érdekében mind az alkalmazás, mind az adatbázis-rétegeknek ugyanazon a virtuális hálózaton kell lenniük.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Meglévő alkalmazásréteg rendelkezésre állási beállítása, rendelkezésre állási zónák és közelségi elhelyezési csoport (PPG)
A jelenlegi telepítési modell bizonyos szolgáltatásiszint-célkitűzések teljesítése érdekében történik.  Ebben a lépésben győződjön meg arról, hogy a célinfrastruktúra eléri vagy meghaladja a kitűzött célokat.  
Valószínűbb, mint nem, az ügyfelek SAP-alkalmazáskiszolgálók egy rendelkezésre állási csoportba kerülnek.  Ha a jelenlegi telepítési szolgáltatási szint kielégítő, és 
- Ha a cél virtuális gép feltételezi a HLI logikai név állomásnevét, a virtuális gép IP-címére mutató DNS-címfeloldás frissítése sap-profilok frissítése nélkül működne
- Ha nem ppg-t használ, a hálózati késés minimalizálása érdekében helyezze az összes alkalmazás- és DB-kiszolgálót ugyanabban a zónában.
- PPG használata esetén olvassa el a jelen dokumentum "Céltervezés, rendelkezésre állási csoport, rendelkezésre állási zónák és közelségelhelyezési csoport (PPG)" című szakaszát.

### <a name="storage-replication-discontinuance-process-if-used"></a>A tárolás replikációjának megszüntetésére szolgáló folyamat (ha van ilyen)
Ha a tárolási replikációt a VÉSZ-megoldásként használja, az SAP-alkalmazás leállítása után le kell állítani (nem ütemezett).  Emellett az utolsó SAP HANA katalógus, naplófájl és adatbiztonsági mentések replikálásra kerültek a távoli DR HLI tárolókötetekre.  Ezt elővigyázatosságból, ha egy katasztrófa történik a fizikai kiszolgáló azure-beli virtuális gép átmenet során.

### <a name="data-backups-preservation-consideration"></a>Az adatbiztonsági mentések megőrzésének megfontolása
Az Azure-beli virtuális gép SAP HANA-ra való átdolgozása után a hli összes pillanatkép-alapú adat- vagy naplóbiztonsági mentése nem könnyen elérhető vagy helyreállítható a virtuális gép számára, ha szükséges. A korai átmeneti időszakban, mielőtt az Azure-alapú biztonsági mentés imént épít ia pont-in-time helyreállítási követelmények, azt javasoljuk, hogy a fájlszintű biztonsági mentések mellett a pillanatképek a HLI, napok vagy hetek előtt cut-over.  Ezeket a biztonsági másolatokat másolja egy Azure Storage-fiókaz új SAP HANA virtuális gép által elérhető.
A HLI-tartalom biztonsági mentése mellett célszerű az SAP-környezet teljes biztonsági mentései is könnyen hozzáférhetők, ha visszaállításra van szükség.

### <a name="adjusting-system-monitoring"></a>A rendszer figyelésének beállítása 
Az ügyfelek számos különböző eszközt használnak az SAP-környezetükön belüli rendszerek riasztási értesítéseinek figyelésére és küldésére.  Ez az elem csak egy felhívás a megfelelő lépésekre a figyelési módosítások beépítéséhez és szükség esetén a riasztási értesítések címzettjeinek frissítéséhez.

### <a name="microsoft-operations-team-involvement"></a>A Microsoft Operations csapatának bevonása 
Nyisson meg egy jegyet az Azure Portalról a meglévő HLI-példány alapján.  A támogatási jegy létrehozása után egy támogatási szakember e-mailben felveszi Önnel a kapcsolatot.  

### <a name="engage-microsoft-account-team"></a>A Microsoft-fiók csapatának bevonása
Tervezze meg az áttelepítést a HLI-szerződés évfordulós megújítási idejének közelében, hogy minimalizálja a számítási erőforrás szükségtelen túlköltségét. A HLI kés leszereléséhez koordinálni kell a szerződés megszüntetését és az egység tényleges leállítását.

## <a name="destination-planning"></a>Céltervezés
Álljon fel egy új infrastruktúrát, hogy a helyére egy meglévőt érdemel némi gondolkodás, hogy az új mellett illeszkedik a nagy rendszer a dolgok.  Az alábbiakban néhány kulcsfontosságú pontot a szemlélődés.

### <a name="resource-availability-in-the-target-region"></a>Erőforrás elérhetősége a célrégióban 
Az aktuális SAP-alkalmazáskiszolgálók telepítési régiója általában közel van a kapcsolódó HLI-khez.  A HLI-k azonban kevesebb helyen érhetők el, mint a rendelkezésre álló Azure-régiók.  A fizikai HLI Azure-ba való áttelepítésekor is jó alkalom arra, hogy "finomítsa" az összes kapcsolódó szolgáltatás közelségi távolságát a teljesítmény optimalizálása érdekében.  Eközben az egyik legfontosabb szempont annak biztosítása, hogy a kiválasztott régió rendelkezik az összes szükséges erőforrással.  Például a rendelkezésre álló bizonyos virtuálisgép-család vagy az Azure Zones magas rendelkezésre állású beállítás.

### <a name="virtual-network"></a>Virtuális hálózat 
Az ügyfeleknek ki kell választaniuk, hogy az új HANA-adatbázist egy meglévő virtuális hálózaton futtatják-e, vagy egy újat hoznak létre.  Az elsődleges döntő tényező az SAP-környezet aktuális hálózati elrendezése.  Akkor is, ha az infrastruktúra megy egy zónából két zónás üzembe helyezési és ppg-t használ, ez kikényszeríti az architekturális változás. További információ: Az [Azure PPG az SAP-alkalmazással való optimális hálózati késésről szóló cikkben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)olvashat.   

### <a name="security"></a>Biztonság
Függetlenül attól, hogy az új SAP HANA virtuális gép egy új vagy meglévő virtuális hálózaton/alhálózaton való leszállás, egy új, üzleti legkritikusabb szolgáltatást jelent, amely védelmet igényel.  A vállalati adatok biztonsági házirendjének megfelelő hozzáférés-vezérlést ki kell értékelni és telepíteni kell ehhez az új szolgáltatásosztályhoz.

### <a name="vm-sizing-recommendation"></a>Virtuálisgép méretezési javaslata
Ez az áttelepítés is lehetőséget ahana számítási motor megfelelő méretére is alkalmas.  Hana [rendszernézetek et](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) hana studio együtt a rendszer erőforrás-felhasználás megértéséhez, amely lehetővé teszi a jobb méretezése a kiadások hatékonyságát.

### <a name="storage"></a>Storage 
A tárolási teljesítmény az SAP-alkalmazások felhasználói élményét befolyásoló egyik tényező.  Egy adott virtuálisgép-termékváltozat alapján minimális tárolási elrendezés közzétett [SAP HANA Azure virtuálisgép-tároló konfigurációk](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)vannak. Javasoljuk, hogy tekintse át ezeket a minimális specifikációkat, és hasonlítsa össze a meglévő HLI rendszer statisztikák kal való összehasonlítása az új HANA virtuális gép megfelelő I/kapacitásának és teljesítményének biztosítása érdekében.

Ha konfigurálja a PPG-t az új HANA virtuális géphez és a hozzá tartozó megszakításokhoz, küldjön el egy támogatási jegyet a tároló és a virtuális gép helymegosztásának ellenőrzéséhez és biztosításához. Mivel a biztonsági mentési megoldás módosítása szükséges, a tárolási költségeket is felül kell vizsgálni, hogy elkerülje a működési kiadások meglepetések elkerülése érdekében.

### <a name="storage-replication-for-disaster-recovery"></a>Tárolási replikáció a vészhelyreállításhoz
A HLI-vel a rendszer a vész-helyreállítási szolgáltatás alapértelmezett beállításaként a tárolási replikációt ajánlotta fel. Ez a szolgáltatás nem az alapértelmezett beállítás az SAP HANA az Azure virtuális gép. Fontolja meg a HSR, biztonsági mentés /visszaállítás vagy más támogatott megoldásokat, amelyek megfelelnek az üzleti igényeinek.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Rendelkezésre állási csoportok, rendelkezésre állási zónák és közelségi elhelyezési csoportok 
Az alkalmazásréteg és az SAP HANA közötti távolság lerövidítése a hálózati késés minimális szinten tartásához, az új adatbázis virtuális gép és a jelenlegi SAP-alkalmazáskiszolgálók ppg-be kell helyezni. Tekintse meg a [Közelségelhelyezési csoport,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) megtudhatja, hogyan Azure rendelkezésre állási csoport és rendelkezésre állási zónák együttműködnek ppg az SAP-telepítések.
Ha a cél HANA-rendszer tagjai egynél több Azure Zone-ban vannak telepítve, az ügyfeleknek világos képet kell kapniuk a kiválasztott zónák késési profiljáról. Az SAP rendszerösszetevők elhelyezése optimális az SAP-alkalmazás és az adatbázis közötti proximális távolság tekintetében.  A nyilvános [rendelkezésre állási zóna késésteszteszköz](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) megkönnyíti a mérést.  


### <a name="backup-strategy"></a>Biztonsági mentési stratégia
Sok ügyfél már használja a harmadik féltől származó biztonsági mentési megoldások SAP HANA HLI-n.  Ebben az esetben csak egy további védett virtuális gép és HANA-adatbázisok at kell konfigurálni.  A folyamatban lévő HLI biztonsági mentési feladatok mostantól nem ütemezhetők, ha a gépet az áttelepítés után leszerelik.
Az Azure Backup sap HANA virtuális gép most már általánosan elérhető.  Tekintse meg ezeket a hivatkozásokat a következő részletes információkért: [Biztonsági mentés](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [Visszaállítás](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), SAP HANA biztonsági mentés [kezelése](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) az Azure virtuális gépeken.

### <a name="dr-strategy"></a>DR stratégia
Ha a szolgáltatási szint céljai hosszabb helyreállítási időt biztosít, a legegyszerűbb és legolcsóbb VÉSZ-stratégia egy egyszerű biztonsági mentés a blob storage-ba és a blob storage-visszaállításhoz vagy egy új virtuális gép visszaállításához.  
Mint a nagy példány platform, ahol hana DR általában történik HSR; Az Azure VM-ben a HSR egyben a legtermészetesebb és natív SAP HANA DR-megoldás is.  Függetlenül attól, hogy a forrás központi telepítése egypéldányos vagy fürtözött, a vész-és erőforrás-régióban szükség van a forrásinfrastruktúra replikájára.
Ez a VÉSZ-replika az elsődleges HLI-virtuális gép áttelepítése után lesz konfigurálva.  A DR HANA DB másodlagos replikációs helyként regisztrálja az elsődleges SAP HANA-t a virtuális gép példányán.  

### <a name="sap-application-server-connectivity-destination-change"></a>Az SAP alkalmazáskiszolgáló kapcsolódási céljának módosítása
A HSR-áttelepítés egy új HANA DB-gazdat eredményez, és így egy új DB-állomásnevet az alkalmazásréteghez, az SAP-profilokat módosítani kell, hogy tükrözzék az új állomásnevet.  Ha a váltás névfeloldással történik, megőrizve az állomásnevet, nincs szükség profilmódosításra.

### <a name="operating-system"></a>Operációs rendszer
Az operációs rendszer lemezképei a HLI és a virtuális gép, annak ellenére, hogy ugyanazon a kiadási szinten, SLES 12 SP4 például, nem azonosak. Az ügyfeleknek ellenőrizniük kell a SZÜKSÉGES csomagokat, gyorsjavításokat, javításokat, kerneleket és biztonsági javításokat a HLI-n, hogy ugyanazokat a csomagokat telepíthessék a célra.  A HSR használata egy régebbi operációs rendszerről egy újabb operációs rendszerrel rendelkező virtuális gépre replikálására támogatott.  Ellenőrizze az adott támogatott verziókat az [SAP 2763388 megjegyzésének áttekintésével.](https://launchpad.support.sap.com/#/notes/2763388)

### <a name="new-sap-license-request"></a>Új SAP-licenckérelem
Egy egyszerű hívás egy új SAP-licenc az új HANA-rendszer most, hogy már átlett telepítve a virtuális gépekre.

### <a name="service-level-agreement-sla-differences"></a>Szolgáltatásiszint-szerződés (SLA) különbségei
A szerzők szeretnék felhívni a HLI és az Azure virtuális gép közötti rendelkezésre állási SLA közötti különbséget.  A fürtözött HLIs HA párok például 99,99%-os rendelkezésre állást kínálnak. Ugyanazt az SLA eléréséhez virtuális gépeket kell telepíteni a rendelkezésre állási zónákban. Ez [a cikk](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) ismerteti a rendelkezésre álló kapcsolódó központi telepítési architektúrák, így az ügyfelek ennek megfelelően tervezhetik meg a célinfrastruktúrát.


## <a name="migration-strategy"></a>Migrálási stratégia
Ebben a dokumentumban csak a HANA rendszerreplikációs megközelítést fedjük le a HLI-ről az Azure Virtuális gépre való áttéréshez.  A telepített céltárolási megoldástól függ, a folyamat kissé eltér. A magas szintű lépéseket az alábbiakban ismertetjük.

### <a name="vm-with-premiumultra-disks-for-data"></a>Virtuális gép prémium/ultra-lemezekkel az adatokhoz
A prémium vagy ultralemezekkel telepített virtuális gépek esetében a szabványos SAP HANA rendszerreplikációs konfiguráció alkalmazható a HSR beállításához.  Az [SAP súgócikk](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) áttekintést nyújt a rendszerreplikáció beállításával, a másodlagos rendszer átvételével, az elsődleges rendszerre való visszalépéssel és a rendszerreplikáció letiltásával kapcsolatos lépésekről.  Az áttelepítés céljából csak a beállításra, az átvételre és a replikációs lépések letiltására lesz szükség.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>Virtuális gép ANF-fel az adatok és a naplókötetek számára
Magas szinten a teljes adatok és naplókötetek legújabb HLI-tárolási pillanatképeit át kell másolni az Azure Storage-ba, ahol a cél HANA virtuális gép elérhető és helyreállítható.  A másolási folyamat bármely natív Linux másolási eszközzel elvégezhető.  

> [!IMPORTANT]
> A másolás és az adatátvitel órákig is eltarthat, a HANA adatbázis méretétől és a hálózati sávszélességtől függ.  A másolási folyamat nagy részét az elsődleges HANA DB állásidő előtt kell elvégezni.

### <a name="mcos-to-mdc-conversion"></a>MCOS–MDC átalakítás
A Több összetevő egy rendszerben (MCOS) telepítési modellt néhány HLI-ügyfelünk használta.  A motiváció az volt, hogy megkerülje a több adatbázis-tároló (MDC) tárolási pillanatkép korlátozása a korábbi SAP HANA-verziók.  Az MCOS-modellben több független SAP HANA-példány egy HLI-panelben van halmozva.  HSR használata az áttelepítéshez jól működne, de ami több HANA virtuális gépek egy bérlő i.a.  Ez a végállapot teszi a forgalmasabb táj, mint amit egy ügyfél lehetett volna hozzászokott.  Az SAP HANA 2.0 alapértelmezett központi telepítése MDC, egy életképes alternatíva a [HANA-bérlő áthelyezése](https://launchpad.support.sap.com/#/notes/2472478) a HSR-áttelepítés után.  Ez a folyamat "konszolidálja" ezeket a független HANA-adatbázisokat egyetlen HANA-tárolóban társbérlőkbe.  

### <a name="application-layer-consideration"></a>Az alkalmazásréteg megfontolása
A DB-kiszolgáló az SAP-rendszer középpontjának tekinthető.  Minden alkalmazáskiszolgálónak az SAP HANA DB közelében kell lennie.  Bizonyos esetekben, amikor a PPG új használatára van szükség, a meglévő alkalmazáskiszolgálók áthelyezése a PPG-re, ahol a HANA virtuális gép szükséges lehet.  Az új alkalmazáskiszolgálók létrehozása egyszerűbbnek tekinthető, ha már rendelkezik kéznél üzembe helyezési sablonokkal.  
Ha a meglévő alkalmazáskiszolgálók és az új HANA virtuális gép optimálisan helyezkednek el, nem kell új alkalmazáskiszolgálókat építeni, kivéve, ha további kapacitásra van szükség.  
Ha egy új infrastruktúra a szolgáltatás rendelkezésre állásának javítása érdekében épül fel, a meglévő alkalmazáskiszolgálók szükségtelenné válhatnak, ezért le kell állítani és törölni kell őket.
Ha a cél virtuálisgép-állomásnév megváltozott, és eltér a HLI állomásnév, SAP alkalmazáskiszolgáló profilokat kell módosítani, hogy pont az új állomás.  Ha csak a HANA DB IP-cím megváltozott, dns-rekordfrissítésre van szükség a bejövő kapcsolatok az új HANA virtuális géphez való vezetéséhez.

### <a name="acceptance-test"></a>Elfogadási vizsgálat
Bár a HLI-ről a virtuális gépre való áttérés nem változtat lényeges en az adatbázis tartalmában a heterogén áttelepítéshez képest, továbbra is javasoljuk az új beállítás kulcsfontosságú funkcióinak és teljesítményszempontjázatának érvényesítését.  

### <a name="cutover-plan"></a>Átépítés terv
Bár ez a migráció egyenesen előre, ez azonban magában foglalja a leszerelésegy meglévő DB.  Gondos tervezés, hogy megőrizze a forrásrendszer a kapcsolódó tartalom és a biztonsági másolat képek kritikus abban az esetben, tartalék van szükség.  A jó tervezés gyorsabb megfordítást kínál.   


## <a name="post-migration"></a>Migráció utáni feladatok
Az áttelepítési feladat addig nem történik meg, amíg biztonságosan el nem választjuk a HLI-függő szolgáltatásokat vagy a kapcsolatot az adatok integritásának megőrzése érdekében.  Is, állítsa le a felesleges szolgáltatásokat.  Ez a szakasz néhány legkiválóbb elemet jelent.

### <a name="decommissioning-the-hli"></a>A HLI leszerelése
A HANA DB azure-beli virtuális gépre történő sikeres áttelepítése után győződjön meg arról, hogy nem futnak hatékony üzleti tranzakciók a HLI-adatbázison.  Azonban a HLI-t egy ideig a helyi biztonsági mentési megőrzési ablakával egyenlő ideig tartja, biztonságos gyakorlat, amely szükség esetén gyorsabb helyreállítást biztosít.  Csak akkor kell a HLI pengét leszerelni.  Az ügyfelek szerződésben kötik hli kötelezettségvállalásaikat a Microsofttal, ha felveszik a kapcsolatot a Microsoft képviselőivel.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>A HLI-hez konfigurált proxy (pl. Iptables, BIGIP) eltávolítása 
Ha egy proxyszolgáltatás, például az IPTables a helyszíni forgalmat a HLI-be és a HLI-ből történő továbbítására használja, akkor a virtuális gépre való sikeres áttelepítés után már nincs szükség rá.  Ezt a kapcsolódási szolgáltatást azonban mindaddig meg kell őrizni, amíg a HLI-panel még készenlétben áll.  Csak a HLI panel teljes leszerelése után állítsa le a szolgáltatást.

### <a name="remove-global-reach-for-hli"></a>A HLI globális elérése eltávolítása 
A Global Reach az ügyfelek ExpressRoute-átjárójának a HLI ExpressRoute-átjáróval való összekapcsolására szolgál.  Lehetővé teszi, hogy az ügyfelek helyszíni forgalma közvetlenül elérje a HLI-bérlőt proxyszolgáltatás használata nélkül.  Erre a kapcsolatra az áttelepítés után nincs szükség a HLI-egységre.  Az IPTables proxyszolgáltatás hoz hasonlóan a GlobalReach-et is meg kell őrizni, amíg a HLI-panel teljesen le nem szerelhető.

### <a name="operating-system-subscription--movereuse"></a>Operációs rendszer-előfizetés – áthelyezés/újrafelhasználás
A virtuális gép kiszolgálóinak felállása és a HLI-panelek leszerelése miatt az operációs rendszer-előfizetések cserélhetők vagy újrafelhasználhatók az operációs rendszer licencei kétszeres fizetésének elkerülése érdekében.



## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:
- [SAP HANA infrastruktúra-konfigurációk és műveletek az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP-számítási feladatok az Azure-ban: tervezési és üzembe helyezési ellenőrzőlista.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
