---
title: Nagyszámítógépes számítás áthelyezése az Azure virtuális gépekre
description: Az Azure számítási erőforrásai kedvezően viszonyulnak a nagyszámítógépek kapacitásához, így áttelepítheti és modernizálhatja az IBM z14-alkalmazásokat.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288931"
---
# <a name="move-mainframe-compute-to-azure"></a>Nagyszámítógépes számítás áthelyezése az Azure-ba

A nagyszámítógépek nagy megbízhatóságról és rendelkezésre állásról híresek, és továbbra is számos vállalat megbízható gerincét képezik. Gyakran úgy gondolják, hogy szinte korlátlan skálázhatóságés a számítási teljesítmény is. Egyes vállalatok azonban kinőtték a legnagyobb elérhető nagyszámítógépek képességeit. Ha ez a hangúgy hangzik, az Azure agilitást, elérést és infrastrukturális megtakarításokat kínál.

A nagyszámítógépes számítási feladatok futtatásához a Microsoft Azure-ban tudnia kell, hogy a nagyszámítógép számítási képességei hogyan viszonyulnak az Azure-hoz. Az IBM z14 nagyszámítógép (a legfrissebb modell, mint az írás), ez a cikk bemutatja, hogyan lehet összehasonlítható eredményeket az Azure-ban.

A kezdéshez vegye figyelembe a környezeteket egymás mellett. Az alábbi ábra összehasonlítja az alkalmazások futtatásához használható nagyszámítógépes környezetet egy Azure-üzemeltetési környezettel.

![Az Azure-szolgáltatások és emulációs környezetek hasonló támogatást nyújtanak, és leegyszerűsítik az áttelepítést](media/mainframe-compute-azure.png)

A nagyszámítógépek erejét gyakran használják online tranzakciófeldolgozási (OLTP) rendszerekhez, amelyek több millió frissítést kezelnek több ezer felhasználó számára. Ezek az alkalmazások gyakran használnak szoftvereket a tranzakciók feldolgozásához, a képernyőkezeléshez és az űrlapbevitelhez. Használhatják az ügyfélinformáció-ellenőrző rendszert (CICS), az információkezelő rendszert (IMS) vagy a tranzakciós felületcsomagot (TIP).

Ahogy az ábra is mutatja, az Azure-beli TPM-emulátor képes kezelni a CICS- és Az IMS-számítási feladatokat. Az Azure-beli kötegrendszer-emulátor a Feladat-vezérlési nyelv (JCL) szerepét látja el. A nagyszámítógépes adatok átkerülnek az Azure-adatbázisokba, például az Azure SQL Database-be. Az Azure-szolgáltatások vagy az Azure virtuális gépeken üzemeltetett egyéb szoftverek rendszerkezeléshez használhatók.

## <a name="mainframe-compute-at-a-glance"></a>Nagyszámítógépes számítás egy pillantással

A z14-es nagygépben a processzorok legfeljebb négy *fiókba*vannak elrendezve. A *fiók* egyszerűen processzorok és lapkakészletek csoportja. Minden fiókban lehet hat aktív központi processzor (CP) chip, és minden CP 10 rendszervezérlő (SC) chiptel rendelkezik. Az Intel x86 terminológiában fiókonként hat aljzat, foglalatonként 10 mag és négy fiók található. Ez az architektúra 24 foglalat és 240 mag durva megfelelőjét biztosítja, maximum egy z14-hez.

A gyors z14 CP 5,2 GHz-es órajeljelgel rendelkezik. A z14 általában az összes központi szerződő féllel együtt kerül kiszállításra a dobozban. Szükség szerint aktiválódnak. Az ügyfél nek általában legalább négy óra számítási időt kell fizetnie havonta a tényleges használat ellenére.

A nagyszámítógépes processzor a következő típusok egyikeként konfigurálható:

- Általános célú (GP) processzor
- System z integrált információfeldolgozó (zIIP)
- Integrált eszköz Linux (IFL) processzorhoz
- Rendszersegéd processzor (SAP)
- Integrált kapcsolóberendezés (ICF) processzora

## <a name="scaling-mainframe-compute-up-and-out"></a>Nagyszámítógépes számítás méretezése fel- és ki

Az IBM nagyszámítógépek akár 240 mag (az egyetlen rendszer jelenlegi z14-es mérete) méretezését teszik lehetővé. Emellett az IBM nagyszámítógépek is kiszélesedhetnek a kapcsolóberendezés (CF) nevű funkción keresztül. A CF lehetővé teszi, hogy egyszerre több nagyszámítógépes rendszer férhessen hozzá ugyanazokhoz az adatokhoz. A CF használatával a mainframe Parallel Sysplex technológia csoportosítja a nagyszámítógépes processzorokat a klaszterekben. Az útmutató megírásakor a Párhuzamos Sysplex szolgáltatás egyenként 32, egyenként 64 processzorból álló csoportot támogatott. Akár 2048 processzor is csoportosítható ily módon a számítási kapacitás horizontális felskálázása érdekében.

A CF lehetővé teszi, hogy a számítási fürtök adatokat osszanak meg közvetlen hozzáféréssel. Az adatok zárolására, a gyorsítótár-információkra és a megosztott adaterőforrások listájára szolgál. Egy vagy több cf-et használó párhuzamos Sysplex "mindent megosztott" kibővített számítási fürtnek tekinthető. Ezekről a funkciókról további információt az [IBM Z párhuzamos Sysplex oldalán](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) talál az IBM webhelyén.

Az alkalmazások ezeket a funkciókat a horizontális felskálázási teljesítmény és a magas rendelkezésre állás biztosításához is használhatják. Ha további információra van arról, hogy a CICS hogyan használhatja a Parallel Sysplex-et a CF-szel, töltse le az [IBM CICS-t és a Coupling Facility: Beyond the Basics](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook-ot.

## <a name="azure-compute-at-a-glance"></a>Az Azure-számítás áttekintése

Vannak, akik tévesen úgy gondolják, hogy az Intel-alapú szerverek nem olyan erősek, mint a nagyszámítógépek. Az új, magsűrű, Intel-alapú rendszerek azonban annyi számítási kapacitással rendelkeznek, mint a nagyszámítógépek. Ez a szakasz az Azure-infrastruktúra szolgáltatásként (IaaS) számítási és tárolási lehetőségeket ismerteti. Az Azure platformszolgáltatásként (PaaS) is biztosít lehetőségeket, de ez a cikk az IaaS-lehetőségekre összpontosít, amelyek hasonló nagyszámítógépes kapacitást biztosítanak.

Az Azure virtuális gépek számítási teljesítményt biztosítanak különböző méretekben és típusokban. Az Azure-ban egy virtuális CPU (vCPU) nagyjából megegyezik a mainframe magjával.

Jelenleg az Azure virtuális gép méretei 1 és 128 vCPU között biztosít. Virtuálisgép(VM) típusok vannak optimalizálva az adott számítási feladatok. Az alábbi lista például a virtuális gép típusait (az írástól aktuálisan) és azok ajánlott felhasználását mutatja be:

| Méret     | Típus és leírás                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D-sorozat | Általános célú 64 vCPU-val és akár 3,5 GHz-es órajellel                           |
| E-sorozat | Akár 64 vCPU-val optimalizált memória                                                 |
| F-sorozat | Akár 64 vCPU-val és 3..7 GHz-es órajeljellel optimalizált számítás                       |
| H-sorozat | Nagy teljesítményű számítástechnikai (HPC) alkalmazásokhoz optimalizálva                          |
| L-sorozat | Nagy átmenő súgás alkalmazásokra optimalizált tároló, amelyet olyan adatbázisok támogatnak, mint a NoSQL |
| M sorozat | A legnagyobb, akár 128 vCPU-val rendelkező, számítási és memóriaoptimalizált virtuális gépek                        |

Az elérhető virtuális gépekről a [Virtuálisgép-sorozat című](https://azure.microsoft.com/pricing/details/virtual-machines/series/)témakörben talál részleteket.

A z14-es központi számítógép akár 240 maggal is rendelkezhet. A z14-es nagyszámítógépek azonban szinte soha nem használják az összes magot egyetlen alkalmazáshoz vagy számítási feladathoz. Ehelyett a nagyszámítógép logikai partíciókra (LpARs) elkülöníti a számítási feladatokat, és az LpARs minősítéssel rendelkezik – MIPS (Másodpercenként több millió utasítás) vagy MSU (millió szolgáltatási egység). A nagyszámítógépes számítási feladatok Azure-on való futtatásához szükséges összehasonlítható virtuális gép méretének meghatározásakor vegye figyelembe a MIPS (vagy MSU) minősítést.

Az általános becslések a következők:

-   150 MIPS vCPU-nként

-   1000 MIPS processzoronként

Az LPAR adott számítási feladatának megfelelő virtuális gépméretének meghatározásához először optimalizálja a virtuális gép a számítási feladatokhoz. Ezután határozza meg a szükséges vCPU-k számát. A konzervatív becslés 150 MIPS vCPU-nként. Ezen a becslésen alapul, például egy F sorozatú virtuális gép 16 vCPU-val könnyedén támogathatja az IBM Db2 számítási feladatokat egy 2400 MIPS-t követő LPAR-ból.

## <a name="azure-compute-scale-up"></a>Azure számítási felskálázás

Az M sorozatú virtuális gépek skálázása legfeljebb 128 vCPU-k (abban az időben ez a cikk volt írva). A vCPU-nként i 150 MIPS-re vonatkozó konzervatív becslést használva az M sorozatú virtuális gép körülbelül 19 000 MIPS-nek felel meg. A nagyszámítógép MIPS-ének becslésére vonatkozó általános szabály processzoronként 1000 MIPS. A z14 nagyszámítógép legfeljebb 24 processzorral rendelkezhet, és körülbelül 24 000 MIPS-t biztosít egyetlen nagyszámítógépes rendszerhez.

A legnagyobb z14-es nagyszámítógép körülbelül 5000 MIPS-sel rendelkezik, mint az Azure-ban elérhető legnagyobb virtuális gép. Mégis fontos összehasonlítani a számítási feladatok üzembe helyezésének módját. Ha egy nagyszámítógépes rendszer rendelkezik egy alkalmazásés egy relációs adatbázissal is, akkor azok általában ugyanazon a fizikai nagyszámítógépen vannak telepítve – mindegyik a saját LPAR-jában. Ugyanaz tazúra az Azure-ban gyakran egy virtuális gép az alkalmazás és egy külön, megfelelő méretű virtuális gép az adatbázishoz használatával.

Ha például egy M64 vCPU-rendszer támogatja az alkalmazást, és az adatbázishoz M96 vCPU-t használ, körülbelül 150 vCPU-ra van szükség – vagy körülbelül 24 000 MIPS-re, ahogy az alábbi ábra mutatja.

![24 000 MIPS-es számítási feladatok összehasonlítása](media/mainframe-compute-mips.png)

A megközelítés az alacsony lprs áttelepítése az egyes virtuális gépekre. Ezután az Azure könnyedén skálázható a legtöbb, egyetlen nagyszámítógépes rendszeren üzembe helyezett alkalmazások számára szükséges méretre.

## <a name="azure-compute-scale-out"></a>Azure számítási horizontális felskálázás

Az Azure-alapú megoldások egyik előnye a horizontális felskálázás. Skálázás teszi szinte korlátlan számítási kapacitás áll rendelkezésre egy alkalmazás számára. Az Azure több módszert is támogat a számítási teljesítmény horizontális felskálázásához:

- **Terheléselosztás a fürtön keresztül.** Ebben a forgatókönyvben egy alkalmazás egy [terheléselosztó](/azure/load-balancer/load-balancer-overview) vagy erőforrás-kezelő segítségével szétoszthatja a munkaterhelést a fürt több virtuális gépe között. Ha több számítási kapacitásra van szükség, további virtuális gépek kerülnek a fürthöz.

- **Virtuálisgép-méretezési készletek.** Ebben a burst forgatókönyvben egy alkalmazás a virtuális gép használata alapján további [számítási erőforrásokra](/azure/virtual-machine-scale-sets/overview) skálázható. Ha az igény csökken, a méretezési készletben lévő virtuális gépek száma is lemaradhat, így biztosítva a számítási teljesítmény hatékony kihasználását.

- **PaaS méretezés.** Az Azure PaaS-ajánlatok méretezése számítási erőforrások. Például [az Azure Service Fabric](/azure/service-fabric/service-fabric-overview) számítási erőforrásokat rendel a kérelmek mennyiségének növekedéséhez.

- **Kubernetes-fürtök.** Az Azure-beli alkalmazások [kubernetes-fürtöket](/azure/aks/concepts-clusters-workloads) használhatnak a számítási szolgáltatásokhoz a megadott erőforrásokhoz. Az Azure Kubernetes-szolgáltatás (AKS) egy felügyelt szolgáltatás, amely vezényli a Kubernetes csomópontokat, készleteket és fürtöket az Azure-ban.

A számítási erőforrások horizontális felskálázásának megfelelő módszer kiválasztásához fontos megérteni, hogy az Azure és a nagyszámítógépek hogyan különböznek egymástól. A kulcs az, hogy az adatokat hogyan osztják meg a számítási erőforrások, vagy ha megosztják az adatokat. Az Azure-ban az adatokat (alapértelmezés szerint) általában nem osztja meg több virtuális gép. Ha egy kibővített számítási fürtben több virtuális gép is megköveteli az adatmegosztást, a megosztott adatoknak olyan erőforrásban kell eltartózkodniuk, amely támogatja ezt a funkciót. Az Azure-ban az adatmegosztás magában foglalja a tárolást, ahogy azt a következő szakasz ismerteti.

## <a name="azure-compute-optimization"></a>Az Azure számításoptimalizálása

Az Azure-architektúra minden egyes feldolgozási szintjét optimalizálhatja. Használja a legmegfelelőbb típusú virtuális gépek és szolgáltatások minden környezetben. Az alábbi ábrán látható egy lehetséges minta üzembe helyezése virtuális gépek az Azure-ban, hogy támogassa a CICS-alkalmazás, amely db2-t használ. Az elsődleges helyen az éles, az üzem előtti és a tesztelési virtuális gépek magas rendelkezésre állású üzembe helyezése. A másodlagos hely a biztonsági mentés és a vészhelyreállítás.

Minden egyes szint is megfelelő vész-helyreállítási szolgáltatások. Például éles és adatbázis virtuális gépek szükség lehet egy meleg vagy meleg helyreállítást, míg a fejlesztési és tesztelési virtuális gépek támogatja a hideg helyreállítást.

![Magas rendelkezésre állású telepítés, amely támogatja a vészhelyreállítást](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>További lépések

- [Nagyszámítógép migrálása](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe rehosting az Azure virtuális gépek](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Nagyszámítógépes tárhely áthelyezése az Azure-ba](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM források

- [Párhuzamos Sysplex az IBM Z-n](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS és a kapcsolórendszer: Az alapokon túl](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [A Db2 pureScale funkció telepítéséhez szükséges felhasználók létrehozása](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt – Példány létrehozása parancs](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale fürtözött adatbázismegoldás](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government felhő nagyszámítógépes alkalmazásokhoz](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [A Microsoft és a FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>További áttelepítési erőforrások

- [Az Azure virtuális adatközpontok –– fuvar- és műszakútmutató](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
