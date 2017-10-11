---
title: "Az Operations Management Suite (OMS) áttekintése | Microsoft Docs"
description: "A Microsoft Operations Management Suite (OMS) a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a helyszíni és a felhőalapú infrastruktúra kezelésében és védelmében.  Ez a cikk az OMS értékét mutatja be, azonosítja az OMS különböző szolgáltatásait és ajánlatait, valamint a részletes tartalmukra mutató hivatkozásokat tartalmaz."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.openlocfilehash: 452dd602387db6db04ca87f6834c9e8606185484
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="what-is-operations-management-suite-oms"></a>Mi az az Operations Management Suite (OMS)?
A cikk az Operations Management Suite (OMS) csomagot mutatja be, és az üzleti értékének, a benne található szolgáltatásoknak és felügyeleti megoldásoknak, valamint a különböző szolgáltatásokhoz és megoldásokhoz kapcsolódó ajánlatoknak a rövid áttekintését tartalmazza.  Az egyes szolgáltatások és megoldások telepítését és használatát ismertető részletes dokumentációra mutató hivatkozásokat is tartalmaz.

## <a name="from-on-premises-to-the-cloud"></a>Helyszínről a felhőbe
A Microsoft már régóta kínál termékeket a vállalati környezetek felügyeletéhez.  2007-ben több termék összevonásával jött létre a System Center felügyeleti termékcsomag.  Ezek közé tartozott a szoftverterjesztési és -készletezési szolgáltatásokat nyújtó Configuration Manager, a rendszerek és alkalmazások proaktív figyelését biztosító Operations Manager, a manuális folyamatokat automatizáló runbookokat tartalmazó Orchestrator, valamint a kritikus fontosságú adatok biztonsági mentésére és helyreállítására szolgáló Data Protection Manager.

Ahogy egyre több számítási erőforrást helyeznek át a felhőbe, a System Center-termékek is egyre több felhőalapú szolgáltatást biztosítanak, ilyen például az Operations Manager és az Azure-ban erőforrásokat kezelő Orchestrator.  Ezek azonban még mindig alapvetően helyszíni megoldásoknak készültek, és így jelentős befektetéseket igényel a helyszíni felügyeleti környezet üzembe helyezése és karbantartása.  A felhő nyújtotta lehetőségek teljes kihasználása és a jövőbeli alkalmazások támogatása érdekében egy új felügyeleti megközelítés vált szükségessé.

## <a name="introducing-operations-management-suite"></a>Bemutatkozik az Operations Management Suite
Az Operations Management Suite (más néven OMS) olyan felügyeleti szolgáltatások gyűjteménye, amelyek kialakítása már a kezdetektől a felhőben történt.  Helyszíni erőforrások üzembe helyezése és kezelése helyett az OMS-összetevők teljes mértékben az Azure-ban futnak.  Minimális konfigurációt igényelnek, és akár percek alatt használatba vehetők.  

- **Minimális költségű és összetettségű üzembe helyezés.**  Mivel az OMS összes összetevőjének és adatának tárolása az Azure-ban történik, a rendszer rövid idő alatt beüzemelhető a helyszíni összetevőkkel járó költségek és összetettség nélkül.
- **Felhőszinteknek megfelelő méretezhetőség.**  Nem kell aggódnia, hogy olyan számítási erőforrásokért kellene fizetnie, amelyekre nincs szüksége, vagy hogy kifogyna a tárhelyből, mivel a felhőben csak a tényleges használat alapján kell fizetnie, és a használat azonnal méretezhető a szükséges terhelésnek megfelelően.  Először kezdheti mindössze néhány erőforrás kezelésével, majd vertikálisan felskálázhatja a rendszert a teljes környezetnek megfelelően.
- **Kihasználhatja a legújabb szolgáltatások nyújtotta előnyöket.**  Az OMS-szolgáltatások funkciói folyamatosan bővülnek és frissülnek.  Frissítések telepítésének szükségessége nélkül férhet hozzá a legújabb funkciókhoz.
- **Integrált szolgáltatások.**  Jóllehet az OMS-szolgáltatások mindegyike önmagában is jelentős értéket képvisel, egymással együttműködve összetett felügyeleti forgatókönyveket is képesek kiszolgálni.  Az Azure Automation egyik runbookja például levezényelhet egy feladatátvételi folyamatot az Azure Site Recoveryvel, majd az információkat a Log Analyticsba naplózva riasztást hozhat létre.
- **Globális ismeretek.**  Az OMS felügyeleti megoldási folyamatosan hozzáférnek a legfrissebb információkhoz.  A biztonsági és auditálási megoldás például fenyegetéselemzést hajthat végre a világszerte észlelt fenyegetésekkel kapcsolatos adatok felhasználásával.
- **Hozzáférés bárhonnan.**  A felügyeleti környezetet bárhonnan elérheti egy böngészővel.  Az OMS alkalmazást az okostelefonjára telepítve bármikor rendelkezésére állnak a figyelési adatok.

### <a name="is-it-just-for-the-cloud"></a>Mindez csak a felhőben használható?
Annak ellenére, hogy az OMS-szolgáltatások a felhőben futnak, ez nem jelenti azt, hogy ne tudnák hatékonyan felügyelni a helyszíni környezetet.  Ha telepít egy ügynököt az adatközpont bármelyik Windows vagy Linux rendszerű számítógépére, az ügynök elküldi az adatokat a Log Analyticsnak, ahol az adatok elemezhetők a felhőalapú vagy helyszíni szolgáltatásokból gyűjtött adatokkal együtt.  Az Azure Backup és az Azure Site Recovery használatával a felhőt használhatja a helyszíni erőforrások biztonsági mentésére és magas rendelkezésre állásának biztosítására is.  
A felhőben futó runbookok általában nem férnek hozzá a helyszíni erőforrásokhoz, ha azonban egy vagy több gépre ügynököt telepít, az ügynökök futtathatják a runbookokat az adatközpontban.  Amikor elindít egy runbookot, csak meg kell adnia, hogy a felhőben vagy a helyi feldolgozón szeretné futtatni azt.

## <a name="hybrid-management-with-system-center"></a>Hibrid felügyelet a System Centerrel
Ha már rendelkezik egy meglévő System Center-telepítéssel, az összetevőket az OMS-szolgáltatásokkal integrálva hibrid megoldást alakíthat ki a helyszíni és felhőalapú környezet számára, kihasználva az egyes termékek sajátos képességei nyújtotta előnyöket.  A meglévő Operations Manager felügyeleti csoportot a Log Analyticshez csatlakoztatva elemezheti a felügyelt ügynököket felhőben.  A meglévő biztonsági mentési folyamatot a Data Protection Managerrel használva az adatokról a felhőben készíthet biztonsági másolatot.  


## <a name="oms-services"></a>OMS-szolgáltatások
Az OMS legfontosabb funkcióit az Azure-ban futó szolgáltatások biztosítják.  Mindegyik szolgáltatás egy adott felügyeleti funkciót biztosít, és a szolgáltatások kombinálásával különféle felügyeleti forgatókönyvek valósíthatók meg.

|| Szolgáltatás | Leírás |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | Figyeli és elemzi a különféle erőforrások, köztük a fizikai és virtuális gépek rendelkezésre állását és teljesítményét. |
| ![Azure Automation](media/operations-management-suite-overview/icon-automation.png) | Automatizálás | Automatizálja a manuális folyamatokat, és érvényesíti a fizikai és virtuális gépekre vonatkozóan megadott konfigurációkat. |
| ![Azure Backup](media/operations-management-suite-overview/icon-backup.png) | Biztonsági mentés | A kritikus fontosságú adatok biztonsági mentését és visszaállítását végzi. |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | Site Recovery | Biztosítja a kritikus fontosságú alkalmazások magas rendelkezésre állását. |

### <a name="log-analytics"></a>Log Analytics
A [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) figyelési szolgáltatásokat biztosít az OMS számára a felügyelt erőforrások adatainak egy központi tárházba gyűjtésével.  Ezek az adatok lehetnek események, teljesítményadatok vagy az API segítségével biztosított egyéni adatok. Az összegyűjtésüket követően az adatok használhatók riasztáshoz, elemzéshez vagy exportáláshoz.  Ezzel a módszerrel konszolidálhatja a különféle forrásokból származó adatokat, így az Azure-szolgáltatásokból származó adatok egyesíthetők a meglévő helyszíni környezet adataival.  Továbbá egyértelműen elkülöníti az adatok gyűjtését az adatokon végzett műveletektől, így az összes művelet végrehajtható a különféle adatokon.  

![A Log Analytics áttekintése](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>Adatok gyűjtése
Számos különféle módon juttathatja el az adatokat a tárházba a Log Analytics általi elemzés céljából.

- **Windows vagy Linux rendszerű számítógépek és virtuális gépek.**  A Microsoft Monitoring Agentet azon [Windows](../log-analytics/log-analytics-windows-agents.md) és [Linux](../log-analytics/log-analytics-linux-agents.md) rendszerű számítógépekre vagy virtuális gépekre telepítse, amelyekről adatokat szeretne gyűjteni.  Az ügynök automatikusan letölti a Log Analyticsből a konfigurációt, amely meghatározza, hogy mely eseményeket és teljesítményadatokat kell gyűjtenie.  Az Azure Portal segítségével egyszerűen telepítheti az ügynököt az Azure-ban futó virtuális gépekre.  Ha rendelkezik meglévő Operations Manager-környezettel, a felügyeleti csoportot csatlakoztathatja a Log Analyticshez, és automatikusan elindíthatja az adatok gyűjtését az összes meglévő ügynökről.
- **Azure-szolgáltatások.**  A Log Analytics telemetriát gyűjt a tárházba az [Azure Diagnostics és az Azure Monitoring szolgáltatásból](../log-analytics/log-analytics-azure-storage.md), így figyelheti az Azure-erőforrásokat.
- **Adatgyűjtő API.**  A Log Analytics [REST API-jával bármelyik ügyfélből feltölthet adatokat](../log-analytics/log-analytics-data-collector-api.md).  Így harmadik felek alkalmazásaiból is gyűjthet adatokat, illetve megvalósíthat egyéni felügyeleti forgatókönyveket is.  Gyakori módszer egy runbook használata az Azure Automationben az adatok gyűjtésére, majd a Data Collector API használata az adatoknak a tárházba való írására.

#### <a name="reporting-and-analyzing-data"></a>Jelentéskészítés és az adatok elemzése
A Log Analytics egy hatékony lekérdezési nyelvet biztosít a tárházban tárolt adatok kinyeréséhez.  Mivel a forrásokból származó adatok tárolása rekordként történik, egyetlen lekérdezésben akár több forrásból származó adatot is elemezhet.
  
Az alkalmi elemzések mellett a Log Analytics több módszert is biztosít a jelentések létrehozására és a lekérdezésből származó adatok elemzésére.

- **Nézetek és irányítópultok.**  A [nézetek](../log-analytics/log-analytics-view-designer.md) és az [irányítópultok](../log-analytics/log-analytics-dashboards.md) segítségével megjelenítheti a lekérdezések eredményeit a portálon.  A felügyeleti megoldások általában olyan nézeteket tartalmaznak, amelyek a megoldásból származó adatokat elemzik.  Létrehozhat saját, egyéni nézeteket is az adatok elemzéséhez, és azokat elérhetővé teheti egyéni portálján keresztül.
- **Exportálás.**  Bármelyik lekérdezés eredményét exportálhatja, így annak elemzését a Log Analyticsen kívül is elvégezheti.  Akár rendszeres exportálást is ütemezhet a rendkívül hatékony megjelenítési és elemzési képességeket biztosító [Power BI-ba](../log-analytics/log-analytics-powerbi.md).
- **Naplókeresési API.**  A Log Analytics [REST API-jával bármelyik ügyfélből gyűjthet adatokat](../log-analytics/log-analytics-log-search-api.md).  Így programozható módon dolgozhat a tárházban gyűjtött adatokkal, vagy elérheti azokat más figyelési eszközből is.

#### <a name="alerting"></a>Riasztások kezelése
A Log Analytics képes [proaktívan riasztani](../log-analytics/log-analytics-alerts.md) Önt, vagy helyesbítő műveleteket végrehajtani, ha problémát észlel.  Ahogy a Log Analytics összes többi elemzése, ez is naplókeresés segítségével hajtható végre.  A keresés rendszeres ütemezés szerint fut, és a rendszer riasztást küld, ha a keresés eredménye megfelel bizonyos feltételeknek.

![Log Analytics-riasztások](media/operations-management-suite-overview/overview-alerts.png)

Amellett, hogy a rendszer létrehoz egy riasztásrekordot a Log Analytics-tárházban, a riasztások az alábbi műveleteket is végrehajthatják.

- **E-mail-cím.**  E-mail küldésével a rendszer proaktívan értesíti egy észlelt problémáról.
- **Forgatókönyv.**  A Log Analytics riasztása elindíthat egy runbookot az Azure Automationben.  Ez általában az észlelt probléma elhárításának céljából történik.  Ha a hiba az Azure-ban vagy egy másik felhőben lépett fel, a runbook indítható a felhőben, ha pedig egy fizikai vagy virtuális gépen, akkor a helyi ügynökön.
- **Webhook.**  A riasztások webhookot is elindíthatnak, és átadni annak a naplókeresés eredményéből származó adatokat.  Ez lehetővé teszi a külső szolgáltatásokkal, például az alternatív riasztási rendszerekkel való integrációt, vagy helyesbítő műveletek végrehajtásának megkísérlését egy külső webhely esetén.

### <a name="azure-automation"></a>Azure Automation
Az [Azure Automation](http://azure.microsoft.com/documentation/services/automation) folyamatautomatizálási és konfigurációkezelési képességeket biztosít az OMS számára.  Automatizálja a manuális folyamatokat, és segít érvényesíteni a fizikai és virtuális gépekre vonatkozóan megadott konfigurációkat.  

#### <a name="process-automation"></a>Folyamatautomatizálás
Az Azure Automation PowerShell-szkripteken vagy PowerShell-munkafolyamatokon alapuló [runbookok](../automation/automation-runbook-types.md) használatával automatizálja a manuális folyamatokat.  Emellett a runbookokat támogató adategységeket is tartalmaz, például a több runbook között megosztható változókat, vagy hitelesítő adatokat és kapcsolatokat, amelyek lehetővé teszik olyan titkosított adatok tárolását, amelyekre a runbookoknak szükségük lehet a hitelesítéshez.
A runbookok folyamatautomatizálási képességeket biztosítanak a csomagban található többi szolgáltatás számára.  Mivel a többi szolgáltatás mindegyike elérhető a PowerShell használatával vagy a REST API-n keresztül, létrehozhat runbookokat olyan feladatok végrehajtására is, mint a felügyeleti adatok gyűjtése a Log Analyticsben vagy egy biztonsági mentés inicializálása az Azure Backuppal.

##### <a name="accessing-resources"></a>Erőforrások elérése
Mivel a runbookok a PowerShellen alapulnak, a PowerShell-parancsmagokkal elérhető erőforrások bármelyikét képesek kezelni.  Amikor [betölt egy modult](../automation/automation-integration-modules.md) az Automation-fiókba, az a fiókban található összes runbook számára elérhetővé válik. 
 
Amikor egy runbook a felhőben fut, a felhőből elérhető összes erőforráshoz hozzáférhet.  Ezek lehetnek az Azure-előfizetésben vagy egy másik felhőben, például az Amazon Web Servicesben (AWS) található erőforrások, vagy egy REST API-n keresztül elérhető szolgáltatások.  A felhőben lévő runbookok nem hitelesítő adatokkal futnak, de használhatnak olyan Automation-adategységeket, mint a hitelesítő adatok, kapcsolatok és tanúsítványok az elért erőforrások felé történő hitelesítéshez.

Az adatközpontban található erőforrások valószínűleg nem lesznek elérhetők a felhőben futó runbookból.  Azonban telepíthet egy vagy több [hibrid runbook-feldolgozót](../automation/automation-hybrid-runbook-worker.md) az adatközpontban, amelyeken keresztül futtathatja a helyi erőforrásokhoz hozzáférést igénylő runbookokat.  Amikor elindít egy runbookot, meg kell adnia, hogy a felhőben vagy egy adott feldolgozón fusson.

![Azure Automation-runbookok](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>Runbook indítása
A runbookok [számos módszerrel elindíthatók](../automation/automation-starting-a-runbook.md), így több különféle felügyeleti forgatókönyvben használhatók.  

- **Azure Portal.**  Más Azure-szolgáltatásokhoz hasonlóan az Azure Automation is felügyelhető az Azure Portalon.  A runbookok indítása mellett importálhat is runbookokat, vagy létrehozhatja sajátjait.
- **Ütemezés.**  A runbookokat ütemezheti rendszeres időközönként történő futtatásra.  Így automatikusan ismételheti a rendszeres felügyeleti folyamatokat, vagy gyűjthet adatokat a Log Analyticsbe.
- **PowerShell és API.**  A runbookok elindításához és a szükséges paraméteradatok nekik történő átadásához PowerShell-parancsmag vagy az Azure Automation REST API-ja is használható.  
- **Webhook.**  Webhook létrehozható bármely olyan runbookhoz, amely lehetővé teszi a webhook indítását külső alkalmazásokból vagy webhelyekről.
- **Log Analytics-riasztás.**  A Log Analytics riasztása automatikusan elindíthat egy runbookot a riasztás által azonosított probléma elhárításának megkísérlése céljából.

#### <a name="configuration-management"></a>Konfigurációkezelés
A [PowerShell célállapot-konfiguráló szolgáltatása (DSC)](../automation/automation-dsc-overview.md) egy felügyeleti platform a Windows PowerShellben, amely lehetővé teszi a fizikai és virtuális gépek konfigurációjának telepítését és kényszerítését.  Az Azure Automation felügyeli a DSC-konfigurációkat, és egy lekéréses kiszolgálót biztosít a felhőben, amelyet az ügynökök elérhetnek a szükséges konfigurációk lekéréséhez.

![Azure Automation DSC](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Azure Backup és Azure Site Recovery
Az Azure Backup és az Azure Site Recovery az üzletmenet-folytonosság fenntartásához és a vészhelyreállítás végrehajtásához nyújtanak segítséget.  Mindkettő olyan szolgáltatásokat tartalmaz, amelyek segítségével biztosítható, hogy az alkalmazások a leállások alkalmával is rendelkezésre álljanak, majd a rendszerek újraindulásával visszaálljanak a normál működésre.  Mindkét szolgáltatás hozzájárul a szervezet számára meghatározott helyreállításipont-célkitűzések (RPO) és helyreállításiidő-célkitűzések (RTO) betartásához. Az RPO azt az időkorlátot adja meg, amelyen belül az adatok elérhetetlensége még elfogadható a leállások során, míg az RTO azt az időkorlátot, ameddig a szolgáltatások vagy alkalmazások elérhetetlensége fogadható el.

#### <a name="azure-backup"></a>Azure Backup
Az [Azure Backup](http://azure.microsoft.com/documentation/services/backup) az adatok biztonsági mentését és helyreállítását végző szolgáltatásokat biztosít az OMS számára.  Védelmet biztosít az alkalmazásadatok számára, valamint évekig megőrzi őket minden tőkebefektetés nélkül és minimális működési költségek mellett.  Képes biztonsági másolatot készíteni a fizikai és a virtuális Windows kiszolgálókról, valamint az olyan alkalmazások számítási feladatairól, mint az SQL Server és a SharePoint.  A System Center Data Protection Manager (DPM) is használhatja arra, hogy a redundancia és a hosszú távú tárolás biztosítása érdekében a védett adatokat az Azure szolgáltatásba replikálja.

Az Azure Backup védett adatainak tárolása egy meghatározott földrajzi régióban elhelyezkedő biztonságimásolat-tárolóban történik. Az adatok ugyanazon a régión belül replikálódnak, és a tároló típusától függően a nagyobb rugalmasság érdekében egy másik régióban is replikálódhatnak.

Az Azure Backup három alapvető alkalmazási helyzetben használható.

- **Windows rendszerű gép Azure Backup-ügynökkel.** Fájlok és mappák biztonsági mentése bármely Windows-kiszolgálóról vagy -ügyfélről közvetlenül az Azure Backup-tárolóba.<br><br>![Windows rendszerű gép Azure Backup-ügynökkel](media/operations-management-suite-overview/overview-backup-01.png)
- **System Center Data Protection Manager (DPM) vagy Microsoft Azure Backup Server.** Lehetővé teszi a DPM vagy a Microsoft Azure Backup Server használatát az alkalmazások, például az SQL és a SharePoint munkaterhelései mellett fájlok és mappák biztonsági másolatának elkészítésére helyi tárba, majd ezek replikálását az Azure-beli biztonsági mentési tárba. Windows és Linux rendszerű virtuális gépek támogatása Hyper-V-n vagy VMware-en.<br><br>![System Center Data Protection Manager (DPM) vagy Microsoft Azure Backup Server](media/operations-management-suite-overview/overview-backup-02.png)
- **Azure Virtual Machine Extensions.** Az Azure-ban futó Windows vagy Linux rendszerű virtuális gépek biztonsági mentése az Azure Backup-tárolóba.<br><br>![Azure Virtual Machine Extensions](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
Az [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) a helyszíni virtuális és fizikai gépeknek az Azure-ba vagy egy másodlagos helyre való replikálásának vezénylésével biztosítja az üzletmenet-folytonosságot. Ha az elsődleges hely nem érhető el, a rendszer feladatátvételt végez a másodlagos helyre, így a felhasználók tovább dolgozhatnak, majd amikor a rendszer újra működőképessé válik, az elsődleges hely visszaveszi a feladatokat. 

Az Azure Site Recovery magas rendelkezésre állást biztosít a kiszolgálók és alkalmazások számára.  A helyszíni Hyper-V virtuális gépek, VMware virtuális gépek és fizikai Windows/Linux kiszolgálók replikálásával, feladatátvételével és helyreállításával segít a vállalatnak az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításában. A gépek replikálhatók egy másodlagos adatközpontba, illetve arra is van lehetőség, hogy a gépek Azure szolgáltatásba replikálása révén kiterjessze az adatközpontot. A Site Recovery is egyszerű feladatátvételt és helyreállítási lehetőségeket biztosít a számítási feladatok számára. Integrálható az olyan vészhelyreállítási mechanizmusokkal, mint például az SQL Server AlwaysOn, valamint helyreállítási terveket kínál a több számítógépen rétegzett számítási feladatok egyszerű feladatátvételéhez.

Az Azure Site Recovery három alapvető replikációs helyzetben használható.

- **Hyper-V virtuális gépek replikálása.**  Ha a Hyper-V virtuális gépek felügyelete VMM-felhőkben történik, replikálást végezhet másodlagos adatközpontba vagy az Azure Storage-ba. A replikálás az Azure-ba biztonságos internetkapcsolaton keresztül történik. A replikálás a másodlagos adatközpontba a helyi hálózaton keresztül történik.  Ha a Hyper-V virtuális gépet nem a VMM felügyeli, csak az Azure Storage-ba végezhet replikálást. A replikálás az Azure-ba biztonságos internetkapcsolaton keresztül történik.<br><br>![Hyper-V virtuális gépek replikálása](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **VMware virtuális gépek replikálása.**  A VMware virtuális gépeket VMware-t futtató másodlagos adatközpontba vagy az Azure Storage-ba replikálhatja. A replikálás az Azure-ba történhet helyek közötti VPN-en, illetve Azure ExpressRoute-on keresztül vagy biztonságos internetkapcsolaton át. A másodlagos adatközpontba a replikálás az InMage Scout adatcsatornáján keresztül történik.<br><br>![VMware virtuális gépek replikálása](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **A fizikai Windows- és Linux-kiszolgálók replikálása.**  A fizikai kiszolgálókat replikálhatja másodlagos adatközpontokba vagy az Azure Storage-ba. A replikálás az Azure-ba történhet helyek közötti VPN-en, illetve Azure ExpressRoute-on keresztül vagy biztonságos internetkapcsolaton át. A másodlagos adatközpontba a replikálás az InMage Scout adatcsatornáján keresztül történik. Az Azure Site Recovery olyan OMS-megoldással rendelkezik, amely megjelenít néhány statisztikát, de az Azure portált kell használnia minden művelethez.<br><br>![A fizikai Windows- és Linux-kiszolgálók replikálása](media/operations-management-suite-overview/overview-siterecovery-physical.png)


A Site Recovery a metaadatokat meghatározott földrajzi Azure-régióban elhelyezkedő tárolókban tárolja. A Site Recovery szolgáltatás nem tárol replikált adatokat.

## <a name="management-solutions"></a>Felügyeleti megoldások
A [felügyeleti megoldások](operations-management-suite-solutions.md) olyan előre összeállított logikakészletek, amelyek egy adott felügyeleti forgatókönyvet valósítanak meg az OMS egy vagy több szolgáltatásának használatával.  A Microsoft és partnerei által kínált különböző megoldások egyszerűen hozzáadhatók az Azure-előfizetéshez, és így növelhető az OMS-sel kapcsolatos befektetéseinek értéke.  Partnerként létrehozhat saját megoldásokat az alkalmazások és szolgáltatások támogatására, amelyeket az Azure Marketplace vagy gyorsindítási sablonok segítségével biztosíthat a felhasználók számára.

A további funkciót több szolgáltatás használatával biztosító megoldásra jó példa a [Frissítéskezelés megoldás](oms-solution-update-management.md).  Ez a megoldás a Log Analytics Windows és Linux rendszerhez készített ügynökével gyűjt adatokat az egyes ügynökökön szükségessé vált frissítésekkel kapcsolatban.  Az adatokat beírja a Log Analytics-tárházba, ahol azok egy belső irányítópult segítségével elemezhetők.  Üzemelő példány létrehozásakor az Azure Automation runbookjai használhatók a szükséges frissítések telepítéséhez.  A teljes folyamatot a portálon felügyelheti, és nem kell foglalkoznia a mögöttes részletekkel.

![Megoldás](media/operations-management-suite-overview/overview-solution.png)

A legtöbb megoldás végrehajthat egyet vagy többet az alábbi műveletek közül.

- További információ gyűjtése.  A Log Analytics különféle adatokat gyűjt az ügyfelekről és szolgáltatásokból, például eseményeket és teljesítményadatokat.  A felügyeleti megoldás a más adatforrásokból nem elérhető további információkat gyűjthet, gyakran Azure Automation-runbookok használatával.
- A gyűjtött adatok további elemzése.  A felügyeleti megoldások irányítópultokat és nézeteket tartalmaznak, amelyek az adatok elemzését és megjelenítését biztosítják.  Ezek előre meghatározott naplókeresésekre hivatkoznak vissza, amelyek segítségével lefúrhat a részletes adatokba.  Emellett végrehajthatnak elemzéseket a tárházba korábban gyűjtött adatokon, például amikor a biztonsági események között olyan mintákat keresnek, amelyek fenyegetésre utalhatnak.
- Funkció hozzáadása.  A Microsoft által biztosított egyes megoldások a magszolgáltatások képességeire építve további funkciókat tehetnek elérhetővé.  A Szolgáltatástérkép például egy saját konzolt biztosít a kiszolgáló- és folyamatfüggőségek valós idejű felderítéséhez és leképezéséhez.
A Microsoft és partnerei rendszeresen bővítik új megoldásokkal az OMS-t, így folyamatosan növelheti a befektetésének értékét.  A Microsoft megoldásait az OMS-portálon található Solutions Catalog segítségével keresheti meg és telepítheti, illetve a Microsoft és a partnerek megoldásainak keresésére és telepítésére az Azure Portalon elérhető Azure Marketplace is használható.  

![Megoldástár](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>Következő lépések
* További tudnivalók a [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) szolgáltatásról.
* További tudnivalók az [Azure Automation](../automation/automation-intro.md) szolgáltatásról.
* További tudnivalók az [Azure Backup](http://azure.microsoft.com/documentation/services/backup) szolgáltatásról.
* További tudnivalók az [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) szolgáltatásról.
* Ismerje meg a különböző OMS-ajánlatokban [elérhető megoldásokat](../log-analytics/log-analytics-add-solutions.md). 

