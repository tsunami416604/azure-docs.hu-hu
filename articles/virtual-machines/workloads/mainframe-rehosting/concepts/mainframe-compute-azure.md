---
title: A nagyszámítógépes számítási áthelyezése az Azure Virtual Machines
description: Azure számítási erőforrásokat összehasonlítandó favorably nagyszámítógépes kapacitás, így át, és IBM z14 alkalmazásokat.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 8aea4a74ba84855f011dada70ea75ec0d5fb64fe
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896379"
---
# <a name="move-mainframe-compute-to-azure"></a>A nagyszámítógépes számítási áthelyezése az Azure-bA

Nagyszámítógépek magas megbízhatósággal és rendelkezésre állás érdekében, hogy, és továbbra is a sok vállalat megbízható gerincét. Van gyakran azt hitték, hogy szinte korlátlanul és a számítási teljesítményt is. Egyes vállalatok azonban rendelkezik outgrown a legnagyobb rendelkezésre álló Nagyszámítógépek képességét. Ez úgy érzi, például akkor, ha az Azure rugalmasságát, elérhetőséget és infrastruktúra-megtakarítás kínál.

A nagyszámítógépes számítási feladatok futtatásához a Microsoft Azure-ban, hogyan a a nagyszámítógépes számítási lehetőségek összehasonlítása az Azure-bA ismernie kell. Egy IBM z14 nagyszámítógépes (a jelen cikk írásakor az aktuális modell) alapján, ez a cikk bemutatja, hogyan lehet hasonló eredményt ad az Azure-ban.

Első lépésként érdemes lehet a környezetek egymás mellett. Az alábbi ábra egy nagyszámítógépes környezetben futó alkalmazásokat az Azure-üzemeltetési környezet hasonlítja össze.

![Azure-szolgáltatások és az emuláció környezetek ajánlat összehasonlítható támogatja, és egyszerűsítheti az áttelepítési](media/mainframe-compute-azure.png)

Nagyszámítógépek hatékonyságát gyakran használják az online tranzakció-feldolgozási (OLTP) rendszerek, amely több millió pedig felhasználók ezreihez frissítéseinek kezelésére. Ezek az alkalmazások gyakran használnak a szoftverfrissítési tranzakció-feldolgozás, a képernyő kezelése és a képernyő bejegyzés. Egy ügyfél információk vezérlő rendszer (CICS), információ felügyeleti rendszer (IMS) vagy egy tranzakció felület csomag (TIP) is használhatnak.

Ahogy az ábrán látható, az Azure-ban egy TPM-emulátor CICS és IMS számítási feladatok kezelésére is alkalmas. Egy batch-rendszer emulátor az Azure-ban az szerepkört a feladat vezérlő nyelv (JCL). A nagyszámítógépes adatok áttelepítése az Azure, például az Azure SQL Database-adatbázisok. Azure-szolgáltatásokat vagy más szoftverek az Azure Virtual machines szolgáltatásban üzemeltetett rendszerkezelési is használható.

## <a name="mainframe-compute-at-a-glance"></a>Egyetlen pillantással nagyszámítógépes számítási

A z14 nagyszámítógépes a processzorok elrendezésére legfeljebb négy *menüivel*. A *navigációs menü* feldolgozók és chipkészletekkel egyszerűen csak egy fürt. Minden egyes fiók lehet hat aktív központi (CP) processzorral, és minden egyes CP 10 rendszer vezérlő (SC) lapkák rendelkezik. Az Intel x86 szóhasználatban nincsenek navigációs menü, valamint a 10 magok száma és a négy menüivel hat sockets. Ez az architektúra biztosít 24 sockets és 240 mag, maximum, a z14 hozzávetőleges megfelelője.

A gyors z14 CP egy 5.2-es GHz órajelű rendelkezik. Általában egy z14 kézbesíti a rendszer az összes a CPs a mezőbe. Az aktivált igény szerint. Egy ügyfél legalább négy óra / hó, annak ellenére, hogy a tényleges használat számítási idő gyakran díjszabásának.

A nagyszámítógépes processzor konfigurálhat az alábbiak egyike:

- Általános célú (GP) processzor
- System z Integrated Information Processor (zIIP)
- Integrált létesítmény Linux (IFL) processzor
- Rendszer Assist processzor (SAP)
- Integrált kapcsolási létesítmény Tűzfala processzor

## <a name="scaling-mainframe-compute-up-and-out"></a>Méretezési nagyszámítógépes számítási felskálázás és kibővítés

IBM Nagyszámítógépek ajánlat legfeljebb 240 mag (egy egységes rendszert aktuális z14 mérete) méretezési képességét. Ezenkívül IBM Nagyszámítógépek horizontálisan kapcsolási létesítmény (CF) szolgáltatás segítségével. A CF lehetővé teszi, hogy több nagyszámítógépes rendszer ugyanazokat az adatokat egyidejűleg eléréséhez. A CF-hez, a nagyszámítógépes párhuzamos Sysplex technológia csoportok nagyszámítógépes processzorok fürtök használatával. Ha ez az útmutató írták, a párhuzamos Sysplex funkció támogatott 32 csoportosítás 64 processzorral. Legfeljebb 2048 processzorok horizontális felskálázása a számítási kapacitást az ily módon csoportosíthatók.

A CF-hez lehetővé teszi, hogy a számítási fürtök közvetlen hozzáférést megoszthatják az adatokat. A zárolás, gyorsítótár és adatait, valamint a megosztott adatforrások listája szolgál. Egy vagy több CFs használatával egy párhuzamos Sysplex is értelmezhetők, a "megosztás minden" horizontális felskálázás számítási fürt. Ezek a funkciók kapcsolatos további információkért lásd: [az IBM Z párhuzamos Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) az IBM-webhelyén.

Alkalmazások használhatják ezeket a szolgáltatásokat a teljesítmény horizontális felskálázása és a magas rendelkezésre állást biztosít. Arról, hogyan CICS párhuzamos Sysplex a CF-hez, töltse le a [IBM CICS és a kapcsoló konstrukció: Alapvető túli](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) CD Audio.

## <a name="azure-compute-at-a-glance"></a>Azure-beli számítási egyetlen pillantással

Vannak, akik véletlenül úgy, hogy az Intel-alapú kiszolgálók nagy teljesítményűek, mint Nagyszámítógépek nem. Az új core sűrű, Intel-alapú rendszerek azonban a sokkal számítási kapacitását Nagyszámítógépek is rendelkezik. Ez a szakasz ismerteti az Azure számítási és tárolási infrastruktúra--szolgáltatásként (IaaS) beállítások. Azure platform –-szolgáltatásként (PaaS), valamint lehetőséget biztosít, de ez a cikk foglalkozik IaaS döntések nyomán összehasonlítható nagyszámítógép-kapacitást biztosítanak.

Azure-beli virtuális gépek a különböző méretű és típusú számítási teljesítményt nyújtanak. Az Azure-ban egy virtuális CPU (vCPU) nagyjából-adatbázisnak felel meg a nagyszámítógépes az alapszintű.

Jelenleg a tartományt az Azure virtuális gépek méretei biztosít 1 128 vcpu-k. Virtuális gép (VM) típusú adott feladatokra lettek optimalizálva. Például az alábbi listában látható a virtuális gép (a jelen cikk írásakor aktuális) típusokat és a használatukat ajánlott:

| Méret     | Típus és leírás                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D sorozat | Általános célú 64 vCPU-, és akár 3,5-GHz-es órajel                           |
| E sorozat | Akár 64 virtuális processzorral rendelkező memóriaoptimalizált                                                 |
| F sorozat | A legfeljebb 64 virtuális processzorral és 3..7 GHz órajelű optimalizált számítási                       |
| H-sorozat | Nagy teljesítményű feldolgozási (HPC) alkalmazásokat optimalizálva                          |
| L sorozat | Tároló, amely a NoSQL-adatbázisok által támogatott nagy adatmennyiségeket mozgató alkalmazásokhoz |
| M-sorozat | Legnagyobb számítási és memória optimalizált virtuális gépek és legfeljebb 128 Vcpu                        |

Rendelkezésre álló virtuális gépek kapcsolatos részletekért lásd: [virtuálisgép-sorozat](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Egy z14 nagyszámítógépes legfeljebb 240 magok is rendelkezhet. Azonban z14 Nagyszámítógépek szinte soha ne használja a Processzormagok száma egy egyetlen alkalmazást vagy munkaterhelést futtatják. Ehelyett egy nagyszámítógépes elkülöníti a számítási feladatokat logikai partíció (LPARs), és a LPARs rendelkezik minősítések – MIPS (több millió utasításokat / másodperc) vagy az MSU (1 millió szolgáltatás egység). Ha az Azure, a MIPS (vagy MSU) tényező minősítés nagyszámítógépes számítási feladatok futtatásához szükséges hasonló virtuális gépek méretének meghatározásához.

Általános becslése a következők:

-   150 MIPS / vCPU

-   1000 MIPS processzoronként

Annak megállapításához, a megfelelő Virtuálisgép-méret egy LPAR az adott számítási feladat, először a virtuális gép a számítási feladathoz tartozó optimalizálása. Ezután határozza meg a szükséges vcpu-k számát. Konzervatív becslés 150 MIPS vCPU /. Ez a becslés alapján, például 16 Vcpu-F-sorozatú virtuális gép egyszerűen támogatta az IBM DB2-höz számítási feladat egy LPAR 2400 MIPS együtt származik.

## <a name="azure-compute-scale-up"></a>Azure-beli számítási vertikális felskálázás

Az M sorozatú virtuális gépek méretezhető legfeljebb 128 Vcpu (időben ezt a cikket). A 150 MIPS konzervatív becsült vCPU / használja, az M-sorozatú virtuális gépek-adatbázisnak felel meg 19,000 MIPS készül. Az általános szabály MIPS becsléséről a nagyszámítógépes érték 1000 MIPS processzoronként. Egy z14 nagyszámítógépes is akár 24 processzorral rendelkeznek, és adja meg KB 24,000 MIPS egyetlen nagyszámítógépes rendszer.

A legnagyobb egyetlen z14 nagyszámítógépes körülbelül 5 000 MIPS több, mint az Azure-ban elérhető legnagyobb virtuális gép rendelkezik. Még fontos hasonlítsa össze a számítási feladatok telepítési módját. Ha a nagyszámítógépes rendszer egy alkalmazás és a egy relációs adatbázisban, azokat általában a való üzembe helyezésük ugyanazon a fizikai nagyszámítógépes – mindegyik a saját LPAR. Ugyanazt a megoldást az Azure-ban gyakran helyezik üzembe egy virtuális gép használata az alkalmazás és a egy különálló, megfelelő méretű virtuális Gépet az adatbázis.

Például ha egy M64 vCPU-rendszer támogatja-e az alkalmazás, és a egy M96 vCPU szolgál az adatbázist, körülbelül 150 vcpu-k van szükség – vagy körülbelül 24,000 MIPS az alábbi ábrán látható módon.

![Számítási feladatok üzembe helyezéséhez, 24,000 MIPS összehasonlítása](media/mainframe-compute-mips.png)

A módszer LPARs áttelepíteni az egyes virtuális gépekhez. Majd Azure könnyen méretezhető a kívánt méretet, a legtöbb alkalmazás egyetlen nagyszámítógépes rendszerre telepített.

## <a name="azure-compute-scale-out"></a>Azure-beli számítási horizontális felskálázás

Egy Azure-alapú megoldás előnyeit egyik, a horizontális felskálázási képességét. Szinte korlátlanul méretezési teszi a számítási kapacitás egy alkalmazás számára elérhető. Az Azure számítási teljesítmény horizontális több módszereket támogatja:

- **Terheléselosztás egy fürt között.** Ebben a forgatókönyvben egy alkalmazás használhatja egy [terheléselosztó](/azure/load-balancer/load-balancer-overview) vagy erőforrás-kezelő a számítási feladatok egy fürtben több virtuális gép között elosztva. Nagyobb számítási kapacitásra van szükség, ha a fürt további virtuális gépek kerülnek.

- **A Virtual machine scale sets.** Hirtelen ilyenkor az alkalmazás további méretezhető [számítási erőforrások](/azure/virtual-machine-scale-sets/overview) Virtuálisgép-használat alapján. Ha az igény csökken, a méretezési csoportban lévő virtuális gépek száma is választhatja le, hatékonyabban használhatók fel a számítási teljesítmény biztosítása.

- **A PaaS méretezést.** Az Azure PaaS-ajánlatok méretezni a számítási erőforrásokat. Ha például [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) lefoglalja a számítási erőforrásokat, amelyek megfelelnek a kérések mennyisége nő.

- **Kubernetes-fürtök.** Az Azure-alkalmazások használhatják [Kubernetes-fürtök](/azure/aks/concepts-clusters-workloads) megadott erőforrások számítási szolgáltatásokhoz. Az Azure Kubernetes Service (AKS) egy felügyelt szolgáltatás, Kubernetes-csomópontok, a készletek és a fürtök az Azure-ban vezénylő.

A horizontális felskálázása a számítási erőforrások a megfelelő módszer kiválasztásához, fontos tudni, hogyan Azure és a nagyszámítógépek eltérőek. A kulcs hogyan – vagy, ha – adatok számítási erőforrásokon osztozik. Az Azure-ban (alapértelmezés szerint) nem általában adatmegosztás több virtuális gép. Ha az adatok megosztásához van szükséges horizontális felskálázás több virtuális gépet számítási fürt, kell lennie, a megosztott adatok egy erőforrást, amely támogatja ezt a funkciót. Az Azure-ban az adatok megosztása magában foglalja a tárolási az alábbi szakasz ismerteti.

## <a name="azure-compute-optimization"></a>Azure-beli számítási optimalizálása

Minden egyes szintjét egy Azure-architektúra feldolgozási optimalizálhatja. Használja a leginkább megfelelő virtuális gépek és szolgáltatások az egyes környezetekhez. A következő ábrán látható egy lehetséges minta egy Db2 használó CICS alkalmazás támogatásához az Azure-beli virtuális gépek üzembe helyezéséhez. Az elsődleges helyen, az éles üzem előtti és tesztelési a virtuális gépek magas rendelkezésre állású üzembe. A másodlagos hely van a biztonsági mentésre és vészhelyreállításra.

Minden egyes réteghez is lehetővé teszi megfelelő vész helyreállítási szolgáltatások. Például éles és az adatbázis virtuális gépek szükség lehet a gyakori elérésű vagy online helyreállítás közben a fejlesztési és tesztelési virtuális gépeket támogatja a ritkán használt helyreállítási.

![Magas rendelkezésre állású telepítés, amely támogatja a vész-helyreállítási](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>További lépések

- [Nagyszámítógépek migrálása](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [A nagyszámítógépes újratárolása az Azure Virtual machines szolgáltatásban](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [A nagyszámítógépes tár áthelyezése az Azure-bA](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM-erőforrások

- [Az IBM Z párhuzamos Sysplex](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS és a kapcsoló konstrukció: Alapvető túl](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Egy Db2-pureScale szolgáltatás telepítése a szükséges felhasználók létrehozása](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - példány parancs létrehozása](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2-pureScale fürtözött adatbázis-megoldás](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Nagyszámítógépes alkalmazások a Microsoft Azure Government-felhőben](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [A Microsoft és a FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>További források a migráláshoz

- [Platform Modernization Alliance: IBM DB2-höz, az Azure-ban](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Az Azure virtuális adatközpont Lift és Shift útmutató](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
