---
title: Az SAP számítási feladatok tervezési és telepítési ellenőrzőlista |} A Microsoft Docs
description: Ellenőrzőlista a tervezési és központi telepítését az SAP számítási feladatok Azure-ban
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/07/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 901dfc502470e52600e3a0fafe3f6b91b7686197
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201329"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Az SAP számítási feladatok az Azure tervezési és telepítési ellenőrzőlista 

Ezzel az ellenőrzőlistával célja az SAP NetWeaver, S/4hana-t és az Azure infrastruktúra-szolgáltatás Hybris alkalmazások áthelyezése ügyfelek számára.  Ezzel az ellenőrzőlistával át kell tekintenie a vevő és/vagy az SAP partneri a projekt időtartama alatt. Fontos, hogy az ellenőrzések számos megjelenésével elején a projekt és a tervezési fázisban. Ha végzett az üzembe helyezés, elemi módosítások üzembe helyezett Azure-infrastruktúra vagy SAP elhárítása bonyolult feladat lehet. Tekintse át az ezzel az ellenőrzőlistával egész projekt fő mérföldkövek.  Kisebb problémák észlelése előtt nagy problémát okoznának és létezik-e elegendő idő újra mérnök, és tesztelje a szükséges módosításokat. A feladatlista nem módon jogcímek befejeződik. Egyes helyzettől függ is számos további ellenőrzések kíván végezni. 

Az összeállított ellenőrzőlista nem tartalmazza a feladatokat, amelyek függetlenek az Azure.  Példa: SAP alkalmazás felületek módosítása során egy lépés, Azure nyilvános Felhőjében vagy egy szolgáltatóhoz.    

Ezzel az ellenőrzőlistával is használható a már üzembe helyezett rendszerekhez. Új funkciók, például a Write Accelerator, a rendelkezésre állási zónák és új virtuális gépek típusai hozzáadható telepített óta.  Éppen ezért hasznos, ha szeretné, tekintse át az ellenőrzőlista rendszeres időközönként ellenőrizze, hogy ismeri az új funkciókat az Azure platformon. 

## <a name="project-preparation-and-planning-phase"></a>Projekt előkészítési és tervezési fázis
Ebben a fázisban tervezett áttelepítés SAP számítási feladatok Azure nyilvános felhőbe. Az entitások és elemek minimális készletét tárgyalt, és a lista definiálva:

1. Ez a dokumentum magas szintű tervezési dokumentum – kell tartalmaznia:
    1. Az aktuális készlet, SAP-összetevők és az alkalmazások és a cél leltár az Azure-ban
    2. A megoldás magas szintű architektúrája
    3. Döntés az Azure-régióban való üzembe helyezéséhez. Az Azure-régiók listája, ellenőrizze a [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/). Az egyes Azure-régiókban elérhető szolgáltatások esetén ellenőrizze a cikk [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/)
    4. A hálózati architektúra az Azure-bA a helyszíni csatlakozni. Indítsa el, hogy ismeri a saját maga a [az Azure Virtual Datacenter tervezet](https://docs.microsoft.com/azure/architecture/vdc/)
    5. Az Azure-ban futó jelentős üzleti hatású adatokat biztonsági alapelveket. Jelentős kezdés olvasásához [Azure Security dokumentációja](https://docs.microsoft.com/azure/security/)
2.  Műszaki tervezés – tartalmazó dokumentumot:
    1.  A megoldás blokkblob ábrája 
    2.  Méretezés, a számítási, tárolási és hálózati összetevőket az Azure-ban. Az Azure virtuális gépeken futó SAP méretezéshez, tekintse meg az SAP támogatási Megjegyzés [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Üzletmenet-folytonossági és vészhelyreállítási architektúra
    4.  Részletes operációs rendszer, DB, Kernel és az SAP támogatási szervizcsomag-verziót. Nincs olyan, hogy minden olyan SAP NetWeaver és az S/4HANA által támogatott operációsrendszer-kiadás támogatott Azure-beli virtuális gépeken. Ugyanez érvényes adatbázis-kezelő kiadások. Fontos, hogy a következő adatforrások beolvasása igazítás, és szükség esetén frissítse SAP kiadások, az adatbázis-kezelő kiadások vagy kiadásainak listáját ahhoz, hogy az SAP-ben be van jelölve, és az Azure támogatott időszak megadása kötelező. Fontos, hogy SAP között van, és az Azure támogatott kiadás kombinációt az SAP és a Microsoft teljes körű támogatás kötelező. Szükség esetén meg kell terveznie frissítése néhány szoftverösszetevőket. További részleteket a támogatott SAP, az operációs rendszer és az adatbázis-kezelő szoftver leírása itt található helyek:
        1.  SAP támogatási Megjegyzés [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Ez a Megjegyzés határozza meg a minimális operációsrendszer-kiadások támogatott Azure-beli virtuális gépeken. A legtöbb nem HANA-adatbázis szükséges minimális adatbázis verziókban is meghatározza. A Megjegyzés a különböző típusú támogatott SAP az Azure virtuális gép SAP méretezését is jár.
        2.  SAP támogatási Megjegyzés [#2039619](https://launchpad.support.sap.com/#/notes/2039619). A Megjegyzés: az Azure-ban határozza meg az Oracle-támogatási mátrixa. Vegye figyelembe, hogy Oracle csak objektumokként Windows- és Oracle Linux vendég operációs rendszer az Azure-beli SAP számítási feladatokhoz. Támogatási nyilatkozattal az SAP alkalmazásrétegre futó SAP-példányok is vonatkozik. Azonban Oracle nem támogatja magas rendelkezésre állás az SAP Central Services. Ennek eredményeképpen szüksége lehet egy másik operációs rendszer, és az SAP Central Services, amely nem csatlakozik, az Oracle-adatbázis-kezelő
        3.  SAP támogatási Megjegyzés [#2235581](https://launchpad.support.sap.com/#/notes/2235581) támogatási mátrixa beolvasni a különböző operációs rendszereken az SAP HANA-kiadások
        4.  SAP HANA támogatott Azure-beli virtuális gépek és [HANA nagyméretű példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) felsorolt [Itt](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        5.  [Az SAP-termék rendelkezésre állási mátrix](https://support.sap.com/en/)
        6.  Egyéb SAP-megjegyzések más adott SAP-termékek  
    5.  Azt javasoljuk, hogy a szigorú 3 szintű tervek SAP éles rendszerek esetén. Kombinálva ASCS + alkalmazás kiszolgálók ugyanazon a virtuális gép nem ajánlott.  Több SID-vel fürtkonfigurációkat az SAP Central Services használata támogatott a Windows, a vendég operációs rendszer az Azure-ban. Mivel az SAP Central Services több biztonsági AZONOSÍTÓVAL fürt konfigurációk nem támogatottak a Linux operációs rendszert az Azure-ban. A vendég Windows operációs rendszer eset dokumentációjában található:
        1.  [Az SAP ASCS/SCS-példány több SID-vel a magas rendelkezésre állás, a Windows Server feladatátvételi fürtszolgáltatás és megosztott lemezt az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [Az SAP ASCS/SCS-példány több biztonsági AZONOSÍTÓVAL rendelkező Windows Server feladatátvételi fürtszolgáltatási és -fájlmegosztást az Azure-ban magas rendelkezésre állás](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Magas rendelkezésre állás és vészhelyreállítás recovery architektúrájáról
        1.  RTO és RPO alapján a magas rendelkezésre állás és vészhelyreállítás recovery architektúrájáról kell kinéznie definiálása
        2.  Magas rendelkezésre állást ugyanabban a zónában ellenőrizze a kívánt adatbázis-kezelő által biztosított lehetőségeket az Azure-ban. A legtöbb adatbázis-kezelő egy szinkron készenléti, javasoljuk, hogy éles rendszerek esetén, amelyek szinkron módszereit kínálnak.
        3.  Vész-helyreállítási különböző Azure-régióban ellenőrizze, milyen lehetőségek érhetők el a különböző adatbázis-kezelő szolgáltató által. Ezek többsége támogatja az aszinkron replikáció és naplóküldés
        4.  Az SAP alkalmazásrétegre határoznia e futtatná az üzleti regressziós tesztrendszerek, amelyek ideális esetben az éles környezetekben üzemelő példányok replikával, az azonos Azure-régió vagy a DR régióban. Az utóbbi esetben meg célként adott üzleti regressziós rendszer DR célként az éles környezetben
        5.  Ha nem kívánja a DR-hely nem éles rendszerek, megvizsgáljuk Azure Site Recovery az SAP alkalmazásrétegre replikálása az Azure-beli Vészhelyreállítási régióba életképes módszerként. Lásd még: [vészhelyreállítás egy többrétegű SAP NetWeaver az alkalmazástelepítés beállítása](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  Ha úgy dönt, hogy egy kombinált HA/DR konfigurációs egészíti segítségével [Azure-beli rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview) győződjön meg arról, saját magának ismeri a Where rendelkezésre állási zónák érhetők el, és növelni a korlátozások, amelyek segítségével vezeti be az Azure-régiók hálózati késések két rendelkezésre állási zónák között  
3.  Ügyfél vagy Partner hozzon létre egy készlet minden SAP-adapterek (SAP és az SAP). 
4.  Foundation Services – tervezés – Ez a kialakítás, tervezés beletartozik például a
    1.  Az Active Directory és a DSN tervezése
    2.  Az Azure és a hozzárendelés különböző SAP-rendszerek belül a hálózati topológia
    3.  [Szerepkör alapú hozzáférés](https://docs.microsoft.com/azure/role-based-access-control/overview) struktúráját a különböző csapatok által kezelt infrastruktúra és az SAP alkalmazások az Azure-ban
    3.  Erőforráscsoport-topológia 
    4.  [Címkézési stratégia](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Elnevezési egyezmény a virtuális gépek és más infrastruktúra-összetevőket és/vagy a logikai nevek
5.  A Microsoft Premier szintű támogatási szerződés – az MS technikai vezető (TAM) azonosításához. Támogatási követelmények az SAP az SAP támogatási Megjegyzés olvassa el a [#2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Adja meg az Azure-előfizetések és -magkvóta eltérő előfizetésekben számát. [Nyissa meg az Azure-előfizetések kvóták növelését a támogatási kérések](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) szükség szerint 
7.  Adatcsökkentés és adatok áttelepítési tervezze meg az SAP-adatok áttelepítése az Azure-bA. SAP NetWeaver rendszerekhez hogyan kell fenntartani a nagy mennyiségű adatok korlátozott mennyiségű irányelvek az SAP rendelkezik. Közzétett SAP [alapvető útmutatóban](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) az adatkezelést, a SAP ERP-rendszerekkel kapcsolatban. Azonban bizonyos tartalmakat vonatkozik NetWeaver és az S/4HANA-rendszerek általában.
8.  Határozzon meg, és döntse el, az automatikus központi telepítési módszer. Automation mögötti infrastruktúra-megoldásokat az Azure-ban célja, hogy determinisztikus módon telepítheti és determinisztikus eredményeket beolvasása. Számos ügyfél használja a parancshéjat vagy alapú CLI-szkriptek. De a segítségével üzembe helyezése az Azure-infrastruktúra az SAP és SAP még telepítése különböző nyílt forráskódú technológiákat. Példák a githubon található:
    1.  [Automatizált SAP üzemelő példányok az Azure-felhőben](https://github.com/Azure/sap-hana)
    2.  [Az SAP HANA telepítése](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Adja meg a reguláris megtervezéséhez és üzembe helyezési felülvizsgálati kiadása ütemben történik között, mert az ügyfél, rendszerintegrátor a Microsoft és egyéb érintett felek

 
## <a name="pilot-phase-optional"></a>Próbaüzem fázisában (nem kötelező)
 
A próbaüzem előtt, vagy projekttervezés és-előkészítés párhuzamosan futtathatók. A fázis is módszerek és a tervezési és az előkészítési fázisban végrehajtott tervezési teszteléséhez használható. A próbaüzem fázisában fogalmak valós megvalósíthatósági kiterjesztve is lehet. Javasoljuk, hogy állítsa be, és ellenőrizze a teljes magas rendelkezésre ÁLLÁSÚ és Vészhelyreállítási megoldást és a biztonság kialakítása során próbacélú telepítéshez. Néhány ügyfél esetben méretezhetőség vizsgálatokat is képes elvégezni ebben a fázisban. Más ügyfelek számára a próbaüzem fázisában védőfal SAP-rendszerek telepítése használjuk. Feltételezzük, hogy a rendszer, amely egy próbaprogram futtatása céljából az Azure-bA migrálni kívánt azonosított.

1.  Optimalizálja Azure-ba való adatátvitel. Nagymértékben függ a felhasználói esetek átviteli keresztül [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) helyszíni volt leggyorsabb, ha az Express kapcsolatcsoport rendelkezett elegendő sávszélesség. Más ügyfelekkel interneten keresztül szükséges a gyorsabb
2.  Egy SAP esetén heterogén adatplatform migrálását követően, amely magában foglalja az exportálás és az adatbázis az adatok importálása, teszteléséhez, és exportálási optimalizálása, és importálja a fázisok. Nagy áttelepítések használata esetén az SQL Server, a cél-platformként, javaslatok található [Itt](https://blogs.msdn.microsoft.com/saponsqlserver/2017/05/08/sap-osdb-migration-to-sql-server-faq-v6-2-april-2017/). A figyelő/SWPM áttelepítési módszert is igénybe vehet, abban az esetben nem kell egy kombinált kiadásban frissítést vagy [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) feldolgozni az áttelepítés egy SAP kiadásban frissítést egyesítése és bizonyos forrás- és adatbázis-kezelő platform teljesítése kombináció, például a dokumentált [adatbázis áttelepítési beállítás (DMO) a SUM 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152). 
    1.  Exportálja a forrás, exportálási fájl feltöltése az Azure és az importálási teljesítmény.  Az exportálás és importálás közötti átfedés maximalizálása
    2.  Kiértékelni között ahhoz, hogy tükrözze az infrastruktúra méretezése a cél- és cél platform adatbázis mérete    
    3.  Ellenőrizze és időzítési optimalizálása 
3.  Műszaki érvényesítést 
    1.  Virtuális gépek típusai
        1.  Az SAP támogatási megjegyzések, az SAP HANA hardver directory és az SAP PAM újra, győződjön meg arról, hogy voltak-e az Azure-hoz, a támogatott virtuális gépek változásainak kiadásainak listáját támogatott Virtuálisgép-típusokhoz és SAP- és adatbázis-kezelő kiadások, az erőforrások ellenőrzése
        2.  Ellenőrizze újra az alkalmazás és az Azure-ban üzembe helyezett infrastruktúra méretezését. Esetén a meglévő alkalmazások áthelyezése, gyakran is származtatást a szükséges SAP használt infrastruktúra és a [SAP referenciaalap weblap](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) és hasonlítsa össze az SAP támogatási Megjegyzés felsorolt SAP számok [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Is, hogy a [Ez a cikk](https://blogs.msdn.microsoft.com/saponsqlserver/2018/11/04/saps-ratings-on-azure-vms-where-to-look-and-where-you-can-get-confused/) szem előtt
        3.  Értékelje ki és tesztelje a méretezése az Azure virtuális gépek maximális tárterületek átviteli sebességének és hálózati átviteli sebessége a tervezési fázisban választott a virtuális gépek különböző típusairól. Az adatok találhatók:
            1.  [Az Azure-beli Windows virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Fontos figyelembe venni a **maximális nem gyorsítótárazott lemezteljesítmény** méretezéshez
            2.  [Az Azure-ban Linux rendszerű virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) fontos figyelembe venni a **maximális nem gyorsítótárazott lemezteljesítmény** méretezéshez
    2.  Storage
        1.  Adatbázis-beli virtuális gépek az Azure Premium Storage használata
        2.  Használat [Azure managed disks](https://azure.microsoft.com/services/managed-disks/)
        3.  Azure Write Accelerator használja az adatbázis-kezelő log meghajtót az M-sorozat. Write accelerator korlátok és használati leírtak szerint [Írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
        4.  A különböző DBMS-típusokat, ellenőrizze a [általános SAP DBMS dokumentációban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) és az adott DBMS-dokumentáció az általános dokumentum mutat, hogy
        5.  Az SAP Hana-hoz, a további részletek szerepelnek [SAP HANA-infrastruktúra konfigurációi és a műveletek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
    3.  Hálózat
        1.  Tesztelheti, kiértékelheti a VNet-infrastruktúrát és az SAP-alkalmazások a különböző Azure virtual networkök belül vagy a terjesztési és
            1.  A hub és küllő virtuális hálózati architektúrát, vagy alapján egyetlen Azure virtuális hálózaton belüli microsegmentation megközelítés kiértékelése
                1.  A költségek miatt közötti adatcsere [Azure virtuális hálózatok társviszonyba](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Ellenőrizze az költségek [Virtual Network szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/virtual-network/)
                2.  Gyors előnyeit, a módosítás elkülönítésére az olyan esetekben, ahol vált az alkalmazások és a egy, a virtuális hálózat alhálózatán lévő üzemeltetett virtuális gépek biztonsági kockázatot jelent a virtuális hálózaton belül egy alhálózat NSG támogatáshoz képest korlátozottabb Azure virtuális hálózatok közötti társviszony leválasztása
                3.  Közép-naplózás helyszíni külvilág és a létrehozott Azure virtuális adatközpont közötti hálózati forgalom
            2.  Értékelje ki és teszteléséhez adatelérési útjának alkalmazásréteg SAP és SAP DBMS réteg között. 
                1.  Bármely elhelyezését [Azure hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) az SAP-alkalmazás és a egy SAP NetWeaver, Hybris vagy S/4hana-t a DBMS réteg közötti kommunikációs útvonal alapján SAP-rendszerek nem támogatják minden
                2.  Különböző Azure virtuális hálózatok társviszonyba nem helyezi el alkalmazásréteg SAP és SAP DBMS nem támogatott.
                3.  [Az Azure Alkalmazásbiztonsági és NSG-szabályok](https://docs.microsoft.com/azure/virtual-network/security-overview) támogatottak az útvonalakat az alkalmazásrétegre SAP és SAP DBMS réteg között
            3.  Győződjön meg arról, hogy [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van a virtuális gépeken, a SAP alkalmazás réteget és az SAP DBMS réteg használhatók. Ne feledje, hogy az operációs rendszer különböző szintű támogatásához a gyorsított hálózatkezelés az Azure-ban van szükség:
                1.  A Windows Server 2012 R2 vagy újabb verziókban
                2.  SUSE Linux 12 SP3 vagy újabb verziókban
                3.  RHEL 7.4 vagy újabb verziókban
                4.  Oracle Linux 7,5-öt. A RHCKL kernelt használ, a kiadás kell lennie a 3.10.0-862.13.1.el7. Az Oracle UEK használatával kernel kiadás 5 megadása kötelező
            4.   Tesztelheti, kiértékelheti az alkalmazásrétegre SAP virtuális gép és az adatbázis-kezelő virtuális gép közötti hálózati késés SAP támogatási Megjegyzés és [#500235](https://launchpad.support.sap.com/#/notes/500235) és SAP támogatási Megjegyzés [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Útmutató a hálózati késés elleni SAP támogatási Megjegyzés eredmények értékelése [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A hálózati késést a közepes és a helyes tartományban kell lennie. Virtuális gépek és a nagyméretű HANA-példány közötti forgalom egységek dokumentált kivétel érvényes [Itt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
            5.   Győződjön meg arról, hogy a ILB központi telepítések a közvetlen kiszolgálói válasz használatára vannak beállítva. Ezzel a beállítással csökkenti a késést azokban az esetekben, ahol az adatbázis-kezelő rétegen magas rendelkezésre állású konfigurációkhoz használhatók Azure ilb-k
    4.   Magas rendelkezésre állás és vészhelyreállítás recovery üzemelő példánya. 
        1.   Ha az SAP alkalmazásrétegre egy adott Azure rendelkezésre állási zónában definiálása nélkül telepít, győződjön meg róla, hogy az SAP párbeszédpanel példány vagy közbenső példánya egyetlen SAP-rendszerrel futó összes virtuális gép telepítve vannak-e az egy [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
            1.   Abban az esetben nem magas rendelkezésre állásra van szüksége az SAP Central Services és az adatbázis-kezelő, ezek a virtuális gépek ugyanazon rendelkezésre állási csoportban, az SAP alkalmazásrétegre telepíthető
        2.   Az SAP Central Services és a magas rendelkezésre állású passzív replikákkal rendelkező adatbázis-kezelő réteg védelméről, van-e a két csomópont egy külön rendelkezésre állási csoportban SAP Central Services és a egy másik rendelkezésre állási csoportban a két adatbázis-kezelő csomópont
        3.   Az Azure rendelkezésre állási zónában történő központi telepítésekor rendelkezésre állási csoportok nem tudják hasznosítani. Azonban győződjön meg arról, hogy két különböző rendelkezésre állási zónában történő, amely a legkisebb késés megjelenítése zónák között az aktív és passzív Central Services csomópontok üzembe lenne szüksége.
            1.   Ne feledje, hogy kell használnia [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) a kis-és Windows vagy támasztja a feladatátvevő fürtök létrehozásáról a DBMS- és SAP Central Services a réteghez tartozó rendelkezésre állási zónák között. [Alapszintű Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) nem használható a zónaszintű központi telepítések 
    5.   Időtúllépés beállításai
        1.   Ellenőrizze a SAP NetWeaver fejlesztői nyomkövetések különböző SAP-példánya, és győződjön meg arról, hogy sorba kiszolgáló és az SAP-munkafolyamatok közötti kapcsolat szünetek jeleztük. E kapcsolat megszakítások elkerülhető az alábbi két beállításkulcs paraméterek beállításával:
            1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime 120000 - = lásd még: [Ez a cikk](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
            2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval 120000 - = lásd még: [Ez a cikk](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
        2.   Annak érdekében, hogy grafikus felhasználói Felülettel időtúllépéssel között egy helyszínen üzembe helyezett SAP grafikus felhasználói felületek és az Azure-ban üzembe helyezett SAP-alkalmazási rétegekben, akkor ellenőrizze-e a következő paraméterek vannak-e beállítva a default.pfl vagy a példány profil:
            1.   rdisp/keepalive_timeout = 3600
            2.   rdisp/életben tartási = 20
        3.   Windows feladatátvevő fürt konfigurációjának használatakor győződjön meg arról, hogy a az időbe nem válaszol csomóponton megfelelően van-e állítva az Azure-hoz. A Microsoft-cikk [finomhangolása a feladatátvevő fürt hálózati küszöbértékek](https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/) paramétereket, és milyen hatással van a feladatátvételi övvisszahúzónál sorolja fel. A felsorolt paraméterek ezen két paramétert kell beállítani, azokra az értékekre:
            1.   SameSubNetDelay = 2
            2.   SameSubNetThreshold = 15
4.   Tesztelje a magas rendelkezésre állás és vészhelyreállítás helyreállításának folyamata
    1.   Feladatátvételi helyzetek szimulálása leáll a virtuális gépek vagy az operációs rendszerek üzembe Pánik módban annak érdekében, hogy döntse el, hogy a feladatátvétel konfigurációk megfelelően működni. 
    2.   A feladatátvétel végrehajtásához szükséges idő mérjük. Ha túl hosszú az időpontokat, vegye figyelembe:
        1.   SUSE Linux rendszeren SBD eszközök használata helyett az Azure-Elkerítjük ügynök feladatátvételi felgyorsítása érdekében
        2.   Az SAP Hana-hoz Ha túl sokáig tart az adatok újbóli betöltés fontolja meg további tárolási sávszélességet kiépítenie
    3.   Tesztelje a biztonsági mentési és visszaállítási feladatütemezési, és ha szükséges
    4.   Tesztelje a DR régióban funkciókat és architektúra
5.  Biztonsági ellenőrzések
    1.  Teszt érvényességét, az Azure szerepkör-alapú megvalósította a hozzáférés-(vezérléssel RBAC) architektúra. Célja, hogy külön, és korlátozza a hozzáférést és engedélyeket a különböző csapatok különböző. Tegyük fel SAP alapon csapat tagjai virtuális gépek üzembe helyezése, és rendelje hozzá a lemezt az Azure storage-ból egy adott Azure-beli virtuális hálózatban kell lennie. Az SAP-alapon csapat azonban nem kell tudni a saját virtuális hálózatok létrehozása, vagy módosítsa a meglévő virtuális hálózatok beállításait. A másik oldalon a csoport tagjai nem lehet képes virtuális gépek virtuális hálózatokon üzembe helyezhető, SAP-alkalmazás és az adatbázis-kezelő virtuális gépek futnak. Sem a csoport tagjainak képesnek kell lennie, módosítsa a virtuális gépek attribútumait, vagy törölhet virtuális gépeket vagy lemezek is.  
    2.  Győződjön meg arról [NSG-t és az ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) szabályokat a várt módon működnek, és a védett erőforrások védelme biztosítható
    3.  Győződjön meg arról, hogy az összes olyan erőforrást, amely titkosítani kell titkosítottak. Határozza meg, és hajtsa végre a tanúsítványok, készítsen biztonsági mentést tárolja, és ezeknek a tanúsítványoknak hozzáférésére és visszaállítani a titkosított entitások folyamatokat. 
    4.  Használat [az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) , illetve ahol lehetséges, az operációs rendszer támogatja-e a szempontjából operációsrendszer-lemezek esetén
    5.  Ellenőrizze, hogy a titkosítás nem túl sok rétegek használatban van. Akkor értelme korlátozott Azure Disk encryption használatát, és ezután a leggyakoribb adatbázis-kezelő átlátható adatbázis titkosítási módszer
6.  Teljesítménytesztelés
    1.  Az SAP az SAP-nyomkövetés és mérések alapján hasonlítsa össze az aktuális végrehajtási felső 10 online jelentések, adott esetben 
    2.  Az SAP az SAP-nyomkövetés és mérések alapján hasonlítsa össze az első 10 kötegelt feladatok jelenlegi implementációja, ahol lehetséges 
    3.  Az SAP az SAP-nyomkövetés és mérések alapján hasonlítsa össze az adatforgalom az SAP-rendszerrel felületeken keresztül. Összpontosítson a felületek, ahol ismeri, hogy az átvitel most intézni fog, például a helyszínről az Azure-ba is különböző helyek között 


## <a name="non-production-phase"></a>Nem éles fázis 
Ebben a fázisban feltételezzük, hogy egy sikeres kísérleti vagy megvalósíthatósági vizsgálat után kezdi nem éles SAP-rendszerek telepítése az Azure-bA. A tapasztalatainkat és a koncepció igazolása fogalmak kívül élményt az ilyen módosítani kell. A feltételek és a koncepció igazolása során felsorolt lépéseket az ilyen típusú központi telepítések, valamint a alkalmazni. Ebben a fázisban általában fejlesztési rendszerek központi telepítéséhez, egység teszteli, rendszerek és üzleti regressziós rendszerek teszteli, az Azure-bA. Javasoljuk, hogy legalább egy nem éles rendszerek egy SAP alkalmazás sor a teljes magas rendelkezésre állású konfigurációval rendelkezik, mivel a jövőbeli éles rendszer lesz. Figyelembe kell vennie az adott fázis során további lépések a következők:  

1.  Mielőtt az Azure-bA a régi platformról rendszerek consumption-erőforrásadatok gyűjtésére, például a CPU-használat, a tárterületek átviteli sebességének és IOPS-adatok. Főleg a DBMS réteg egységeket, application layer egység alapján is. Hálózati és tárolási késés is mérjük.
2.  Jegyezze fel a rendelkezésre állási használati idő minták rendszerekhez. Célja, hogy döntse el, hogy nem termelő rendszerei kell lennie 7 x 24 érhető el vagy nem éles rendszerek, amelyet egy heti vagy havi bizonyos szakaszaiban kell leállítani vannak-e
3.  Tesztelje, és határozza meg, hogy kívánja-e saját operációsrendszer-lemezképek létrehozása a virtuális géphez az Azure-ban, vagy hogy lemezképek használata az Azure lemezkép-katalógusában ki szeretné. Kép használja ki az Azure-katalógus, hogy tegye meg a megfelelő lemezképet, amely a támogatási szerződés a operációs rendszer gyártója által biztosított tükrözi. Az egyes operációsrendszer-szállítók az Azure-katalógusok ajánlja fel a saját licenc lemezképek. Mások operációsrendszer-lemezképek támogatása az Azure által jegyzett díja tartalmazza. Ha úgy dönt, hogy a saját operációsrendszer-lemezképek létrehozása, dokumentáció ezekben a cikkekben találja:
    1.  Hozhat létre Azure-ban üzembe helyezett Windows virtuális gépek általános rendszerképek [ebben a dokumentációban](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    2.  Hozhat létre egy Linux virtuális gép alapján az Azure-ban üzembe helyezett általános rendszerképek [ebben a dokumentációban](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Győződjön meg arról, hogy a vonatkozó Microsoft támogatási szerződés rendelkezik SAP támogatási követelmények teljesítéséhez. Információ az SAP támogatási megjegyzés található [#2015553](https://launchpad.support.sap.com/#/notes/2015553). Nagyméretű HANA-példányokhoz, tekintse meg a dokumentum [bevezetési követelmények](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Győződjön meg arról, hogy a megfelelő emberek számára minél [tervezett karbantartásával kapcsolatos értesítések](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/), ebben az esetben választhat az állásidő és a virtuális gépek újraindítását időben
5.  Folyamatosan ellenőrizze az Azure-dokumentáció csatornán, például a Microsoft-bemutatók [Channel9](https://channel9.msdn.com/) az új funkciók, amelyek alkalmazandók lehetnek az üzemelő példányokhoz
6.  Ellenőrzés SAP-megjegyzések kapcsolódó Azure-ba, például a támogatási Megjegyzés [#1928533](https://launchpad.support.sap.com/#/notes/1928533) új virtuális gépek Termékváltozatait vagy újonnan támogatott operációs rendszerek és adatbázis-kezelő kiadás. Hasonlítsa össze az új virtuális gépek típusai régebbi virtuális gépek elleni típusok a díjszabás, tehát, az üzembe helyezés virtuális gépeken a legjobb ár-teljesítmény arány
7.  Az SAP támogatási megjegyzések, az SAP HANA hardver directory és a SAP PAM újra, győződjön meg arról, hogy nem történt változás a támogatott virtuális gépek az Azure-hoz, a kiadások támogatott operációs rendszer ezen virtuális gépek és a támogatott SAP és az adatbázis-kezelő kiadások, az erőforrások ellenőrzése
8.  Ellenőrizze [Itt](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) az új HANA-tanúsítvánnyal rendelkező SKU-k az Azure és a tervezett, és végül módosítsa az egységek beszerezni a jobb ár-teljesítmény adagjának olyanokra díjszabás összehasonlítása 
9.  Az üzembe helyezési parancsfájlok a új virtuális gépek típusai használatát, és építse be az Azure használni kívánt új funkciói
10. Az infrastruktúra üzembe helyezés után tesztelheti, kiértékelheti és az alkalmazásrétegre SAP virtuális gép és az adatbázis-kezelő virtuális gép közötti hálózati késés SAP támogatási Megjegyzés [#500235](https://launchpad.support.sap.com/#/notes/500235) és SAP támogatási Megjegyzés [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Útmutató a hálózati késés elleni SAP támogatási Megjegyzés eredmények értékelése [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A hálózati késést a közepes és a helyes tartományban kell lennie. Virtuális gépek és a nagyméretű HANA-példány közötti forgalom egységek dokumentált kivétel érvényes [Itt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Győződjön meg arról, hogy a korlátozások egyike az említett [SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) és [SAP HANA-infrastruktúra konfigurációi és a műveletek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) az üzemelő példány
11. Az egyéb ellenőrzések elvégzéséhez a számítási feladatok alkalmazása előtt a koncepció-fogalmak szakaszban felsoroltak szerint
12. Számítási feladatok vonatkozik, ahogy jegyezze fel ezeket a rendszer az erőforrás-használat az Azure-ban, és vesse össze a régi platformját származó rekordokat. Ha látja, hogy rendelkezik-e nagyobb különbségek, módosítsa a virtuális gép méretezése a későbbiekben. Ne feledje, hogy esetén downsizing, a storage és a egy virtuális gép hálózati sávszélesség is csökken:
    1.  [Az Azure-beli Windows virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Az Azure-ban Linux rendszerű virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Másolás rendszerfunkcióit és folyamatok működik. A célja, hogy könnyen használható a fejlesztői rendszerhez, vagy egy tesztgépen, ezért másolja, amelyek a projekt csapatok is új rendszerek gyors eléréséhez. Érdemes lehet [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) elvégezve például eszközként.
14. Optimalizálhatja, és gyarapítsa csapata Azure szerepkör-alapú hozzáfér, engedélyek és folyamatok érdekében győződjön meg arról, hogy rendelkezik-e a vámot szétválasztása egy oldalán. A másik oldalon szeretne biztosítani az összes csoport engedélyezve van az Azure-infrastruktúra a feladatok elvégzéséhez.
15. A gyakorlatban, tesztelési és a dokumentum magas rendelkezésre állású és vész helyreállítási eljárásokat ahhoz, hogy a munkatársak az ilyen feladatok végrehajtásához. A hiányosságok azonosítása és alkalmazkodik az üzemelő példányokat, integrálja az Azure funkciókat

 
## <a name="production-preparation-phase"></a>Éles előkészítési fázisban 
Ez a fázis a hol szeretne gyűjteni a élmény és a nem éles környezetekben a tapasztalatainkat és alkalmazza őket a későbbiekben éles környezetekben üzemelő példányok. Emellett a fázis előtt, hogy is kell készíteni a munkát, az adatforgalom, az aktuális üzemeltetési helyet és az Azure között. 

1.  Haladjon végig szükséges SAP kiadás frissítése során az éles rendszereket az Azure környezetbe történő áthelyezés előtt
2.  Elfogadom az üzleti tulajdonos a működési és üzleti teszteket szeretne elvégezni, az éles rendszer a migrálás után
    1.  Győződjön meg arról, hogy ezek a tesztek végrehajtása és a forrás rendszerekben az aktuális üzemeltetési helyet. El szeretné kerülni, a rendszer Azure-ba való áthelyezése után első alkalommal végzett tesztek
2.  Éles áttelepítési folyamat tesztelése az Azure-bA. Esetén nem helyezi az összes éles rendszereket az Azure-bA az időkeretből, hozhat létre csoportokat, a üzemeltetési ugyanott kell éles rendszerek. Adatok áttelepítése a gyakorlatban és a teszteléshez. Például általános metódusok listája:
    1.  Az SQL Server AlwaysOn, a HANA-Rendszerreplikálást vagy a napló szállítási együtt DBMS módszerek, például a biztonsági mentési és visszaállítási használatával ültet be, és szinkronizálja a adatbázis tartalmát az Azure-ban
    2.  Használja a biztonsági mentési és visszaállítási kisebb adatbázisok
    3.  Használja az SAP áttelepítési figyelője megvalósított SAP SWPM eszközt heterogén áttelepítések végrehajtásához
    4.  Használja a [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) kombinálja az SAP kiadásban frissítést kell feldolgozni. Ne feledje, hogy a forrás- és adatbázis-kezelő nem minden kombinációját támogatottak. További információ található a meghatározott SAP támogatási kiegészítő DMO különböző kiadásait. Ha például [adatbázis áttelepítési beállítás (DMO) a SUM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Tesztelje, hogy az adatforgalom az interneten keresztül vagy expressroute-on keresztül nagyobb átviteli sebesség, abban az esetben át kell helyeznie a biztonsági mentések vagy SAP fájlok exportálása. Vegye figyelembe, hogy az esetet az interneten keresztül helyez át adatokat, előfordulhat, hogy módosítania egyes működik a jövőbeli éles rendszerek esetén a szükséges NSG-t vagy Alkalmazásbiztonsági biztonsági szabályok
3.  Mielőtt az Azure-bA a régi platformról rendszerek consumption-erőforrásadatok gyűjtésére, például a CPU-használat, a tárterületek átviteli sebességének és IOPS-adatok. Főleg a DBMS réteg egységeket, application layer egység alapján is. Hálózati és tárolási szél is mérése
4.  ency.
4.  Az SAP támogatási megjegyzések, az SAP HANA hardver directory és a SAP PAM újra, győződjön meg arról, hogy nem történt változás a támogatott virtuális gépek az Azure-hoz, a kiadások támogatott operációs rendszer ezen virtuális gépek és a támogatott SAP és az adatbázis-kezelő kiadások, az erőforrások ellenőrzése 
4.  A legutóbbi változtatásokat, úgy döntött, ha a Virtuálisgép-típusok és az Azure funkcióinak az üzembe helyezési parancsfájlok
5.  Miután infrastruktúra és az alkalmazás központi telepítési hajtania egy sor ellenőrzést a rendelés érvényesítése:
    1.  A megfelelő attribútumokat és a tárterületet telepítve van a megfelelő virtuális gépek típusai
    2.  Ellenőrizze, hogy a virtuális gépek a megfelelő és a kívánt kiadásainak listáját és azok javításait, és egységes
    3.  Ellenőrizze, hogy a virtuális gépek vezethet be, szükséges és egységes
    4.  Ellenőrizze, hogy a megfelelő alkalmazás-kiadások és javítások van-e telepítve
    5.  A virtuális gépek van üzembe helyezve az Azure rendelkezésre állási csoportok tervek szerint
    6.  Az Azure Premium Storage a késés bizalmas lemezek használták, vagy ahol a [egyetlen virtuális gép garantált 99,9 %](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) megadása kötelező
    7.  Azure Write Accelerator helyesen hajtotta-ellenőrzés
        1.  Győződjön meg arról, hogy a virtuális gépen, a tárolóhelyek, vagy a paritásos megfelelően van beépített támogatás az Azure Írásgyorsító lemezek között
    8.  [Az Azure managed disks](https://azure.microsoft.com/services/managed-disks/) kizárólagos használata
    9.  Virtuális gépek a megfelelő rendelkezésre állási csoportokat, és a rendelkezésre állási zónák van telepítve.
    10. Győződjön meg arról, hogy [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van, a SAP alkalmazás réteget és az SAP DBMS réteg használt virtuális gépeken
    11. Nincs elhelyezését [Azure hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) az SAP-alkalmazás és a egy SAP NetWeaver DBMS rétege közötti kommunikációs útvonal, a Hybris, vagy S/4HANA-alapú SAP-rendszerek
    12. Engedélyezi a kommunikációt a kívánt és tervezett Alkalmazásbiztonsági és NSG-szabályokat, és blokkolja a kommunikációt, ha szükséges
    13. Időtúllépés beállításai a fentebb leírt módon lettek állítva megfelelően
    14. Tesztelheti, kiértékelheti az alkalmazásrétegre SAP virtuális gép és az adatbázis-kezelő virtuális gép közötti hálózati késés SAP támogatási Megjegyzés és [#500235](https://launchpad.support.sap.com/#/notes/500235) és SAP támogatási Megjegyzés [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Útmutató a hálózati késés elleni SAP támogatási Megjegyzés eredmények értékelése [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A hálózati késést a közepes és a helyes tartományban kell lennie. Virtuális gépek és a nagyméretű HANA-példány közötti forgalom egységek dokumentált kivétel érvényes [Itt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    15. Ellenőrizze, hogy e titkosítási van üzembe helyezve szükség esetén, és a szükséges titkosítási módszer
    16. Ellenőrizze, hogy a felhasználói felületek és más alkalmazások kapcsolódhat az újonnan üzembe helyezett infrastruktúra
6.  Egy tervezett karbantartás az Azure reagáló forgatókönyv létrehozásához. Tervezett karbantartás esetén újra kell indítani a rendszerek és a virtuális gépek sorrendben
    

## <a name="go-live-phase"></a>Nyissa meg az élő fázis
Az éles fázisokban kövesse a forgatókönyvek a korábbi fázisokban adatösszekötőt kell. Hajtsa végre a lépéseket, amelyeket tesztelt és betanított. Nem fogadja el a konfigurációkat és a folyamat utolsó pillanatban változásai. Amellett, hogy alkalmazza a következő:

1. Győződjön meg arról, hogy az Azure portal figyelés és más monitorozási eszközök működik.  Ajánlott eszközök állnak a Teljesítményfigyelőt (Windows) vagy KKT (Linux): 
    1.  CPU-számlálók 
        1.  Átlagos CPU-idő – teljes (minden Processzor)
        2.  Átlagos CPU-idő – minden egyes processzor (tehát 128 processzorok m128 virtuális gépen)
        3.  CPU-idő kernel – minden egyes processzor
        4.  CPU-idő felhasználói – minden egyes processzor
    5.  Memory (Memória) 
        1.  Szabad memória
        2.  Memória lap/mp
        3.  Memória lap out/mp
    4.  Lemez 
        1.  Lemez sebessége olvasott kb/s – egyes lemezenként 
        2.  Lemezolvasások/mp – egyes lemezenként
        3.  Lemez sebessége olvasott ms/olvasási – egyes lemezenként
        4.  Lemezre írási kb/s – egyes lemezenként 
        5.  Lemezírás/mp-ben – egyes lemezenként
        6.  Lemez írási ms/olvasás – egyes lemezenként
    5.  Network (Hálózat) 
        1.  Hálózati csomag/mp
        2.  Hálózati csomagokat out/mp
        3.  Hálózati kb/s
        4.  Hálózati kb out/mp 
2.  Az adatok az áttelepítés után hajtsa végre az üzleti rendelkező teljesítésével Ön ellenőrző tesztek. Csak fogadja el az eredmények az eredeti forrás rendszerek esetében a megfelelőségi ellenőrzés eredménye
3.  Ellenőrizze a felületek működnek-e, és hogy más alkalmazások és az újonnan üzembe helyezett éles rendszerek közötti kommunikációhoz
4.  Ellenőrizze az átvitel és a javítás rendszer SAP tranzakció STM keresztül
5.  Hajtsa végre a adatbázisok biztonsági mentése után a rendszer akkor szabadul fel, éles környezetben
6.  Hajtsa végre a virtuális gép biztonsági mentéseinek a SAP alkalmazás réteg virtuális gépek után a rendszer akkor szabadul fel, éles környezetben
7.  SAP-rendszerek, amelyek nem részei a jelenlegi éles fázis, de kommunikálni az SAP-rendszereit, hogy ebben a fázisban éles áthelyezése az Azure-ba, a gazdagép neve puffer SM51 vissza kell állítania. Ebben a lépésben fogja távolíthatja el a régi gyorsítótárazott társított IP-az alkalmazás példányai az Azure-bA helyezte át a nevekkel  


## <a name="post-production"></a>Éles közzététele
Ebben a fázisban lényege figyelése, a üzemeltetési, és a rendszer felügyeletéről. Egy SAP szempontjából a szokásos feladatokat, amelyek végrehajtásához szükség volt a régi üzemeltetési helyet a alkalmazni. Szeretné az Azure adott feladatok a következők:

1. Magas díjszabási rendszerek Azure számlákat elemzése
2. A virtuális gép és a storage kiszolgálóoldali ár-teljesítmény hatékonyság optimalizálása
3. Optimalizálhatja a rendszerek leállíthatják idejét  


## <a name="next-steps"></a>További lépések
Tekintse át a dokumentációt:

- [Az Azure virtuális gépek tervezése és megvalósítása SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Az SAP NetWeaver számára az Azure virtuális gépek üzembe helyezése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Az SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

