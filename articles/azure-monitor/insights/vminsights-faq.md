---
title: A virtuális gépek (előzetes verzió) gyakran ismételt kérdések az Azure Monitor |} A Microsoft Docs
description: A virtuális gépek az Azure Monitor egy olyan megoldás, az Azure-ban, amely egyesíti az állapotának és teljesítményének figyelése az Azure virtuális gép operációs rendszerének, valamint alkalmazás-összetevők és az egyéb erőforrások függőségeinek automatikus felderítése, és feltérképezi a közötti kommunikációt őket. Ez a cikk gyakori kérdésekre ad választ.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: magoedte
ms.openlocfilehash: 32f2833b4c1ba77564d5388bc080a7cb32d90201
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243773"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Az Azure Monitor-beli virtuális gépek (előzetes verzió) gyakran ismételt kérdések
A Microsoft FAQ az összetevővel kapcsolatos gyakori kérdésekre az Azure Monitor-beli virtuális gépek listája. Ha a megoldásról a további kérdése van, lépjen a [fórum](https://feedback.azure.com/forums/34192--general-feedback) és felteheti kérdéseit. Egy kérdést gyakran ismételt, amikor hozzáadjuk ehhez a cikkhez, hogy gyorsan és könnyen megtalálhatók.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Lehetőségeket felvétele egy meglévő munkaterületet?
Ha a virtuális gépek már kapcsolódik egy Log Analytics-munkaterületet, továbbra is munkaterületet használja, ha bevezetése az Azure monitornak a virtuális gépek esetében megadtuk a felsorolt támogatott régiók egyikében [Itt](vminsights-onboard.md#prerequisites).

Amikor végzett előkészítési műveletekhez, konfigurálnánk teljesítményszámlálóit a munkaterületet, amely újraindítja a virtuális gépek mindegyikének adatokról szóló jelentéseket küldeni a munkaterületre való gyűjtésére a virtuális gépek megjelenítése és elemzése az Azure monitorban információit.  Ennek eredményeképpen a virtuális gépeket a kijelölt munkaterülethez kapcsolódó összes teljesítményadatokat megjelenik.  Az egészségügyi és térkép funkciók csak engedélyezve vannak a virtuális gépek, amelyek a megadott felvétele.

További információ a teljesítményszámlálók számlálók engedélyezve vannak, tekintse meg a [bevezetési](vminsights-onboard.md) cikk.

## <a name="can-i-onboard-to-a-new-workspace"></a>Lehetőségeket felvétele egy új munkaterületet? 
Ha a virtuális gépek jelenleg nem csatlakoznak a meglévő Log Analytics-munkaterületet, létre kell hozzon létre egy új munkaterületet, az adatok tárolásához.  Egy új alapértelmezett munkaterületet automatikusan történik, ha konfigurál egy Azure virtuális Gépen az Azure Monitor-beli virtuális gépek az Azure Portalon keresztül.

Ha használja a parancsprogramfájlon alapuló módszert választja, ezeket a lépéseket ismertetjük a [bevezetési](vminsights-onboard.md) cikk. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Mit tegyek, ha a virtuális gépem egy létező munkaterülethez már jelent?
Már a virtuális gépekről származó adatokat gyűjt, ha lehetséges, hogy már konfigurálta a azt a jelentésadatok meglévő Log Analytics-munkaterülethez.  Mindaddig, amíg a munkaterület a támogatott régiók valamelyikében van, a már meglévő munkaterületet az Azure Monitor engedélyezheti a virtuális gépek.  Ha már használja a munkaterületet nem a támogatott régiók egyikében, nem kell üzembe helyezni az Azure monitornak a virtuális gépek jelenleg.  Aktívan dolgozunk további régiók támogatása.

>[!NOTE]
>A munkaterület-e kíván üzembe helyezni őket az Azure Monitor-beli virtuális gépek összes virtuális gépet, amely a munkaterületnek érintő teljesítményszámlálóit konfigurálja azt. A teljesítményszámlálók konfigurációjától a munkaterületre vonatkozó további információért tekintse meg a [dokumentáció](../../azure-monitor/platform/data-sources-performance-counters.md). A virtuális gépek az Azure Monitor konfigurált számlálók kapcsolatos információkért tekintse meg a [előkészítési dokumentáció](vminsights-onboard.md#performance-counters-enabled).  

## <a name="why-did-my-vm-fail-to-onboard"></a>A virtuális gépem miért volt sikertelen üzembe helyezni?
Ha az Azure Portalon egy Azure virtuális Gépen végzett előkészítési műveletekhez, a következő lépések történnek:

* Alapértelmezett Log Analytics-munkaterület jön létre, ha, amely lehetőséget választotta.
* A teljesítményszámlálók kijelölt munkaterülethez van konfigurálva. Ha ezt a lépést nem sikerül, megfigyelheti, hogy adatokat a teljesítmény-diagramok és táblázatok némelyike nem jelennek meg a virtuális gép előkészítése. A dokumentált PowerShell-parancsfájl futtatásával is megoldásához [Itt](vminsights-onboard.md#enable-with-powershell).
* A Log Analytics-ügynök telepítve van egy VM-bővítmény használata az Azure virtuális gépek esetén szükség rá.  
* A virtuális gépek térkép függőségi ügynök az Azure Monitor-bővítmény használata Azure virtuális gépeken telepítve van, ha szükség rá.  
* Az állapotfigyelő szolgáltatást támogató Azure figyelő-összetevőkkel vannak konfigurálva, ha szükséges, és a virtuális gép van beállítva a jelentés egészségügyi adatok.

A előkészítése során ellenőrizzük állapotát az egyes való visszatéréshez értesítési állapotot, a portálon a fentiek közül. A munkaterület és az ügynök telepítésének konfigurációja általában 5-10 percet vesz igénybe. A portálon figyelési és adatok megtekintése egy további 5-10 percet vehet igénybe.  

Ha bevezetési kezdeményezett, és tekintse meg a virtuális Gépet kell előkészíteni elérhetőségére, lehetővé teszi a virtuális gép a folyamat akár 30 percig. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>I csak engedélyezve van az Azure Monitor-beli virtuális gépek, miért jelenik meg az állapotfigyelő szolgáltatás által figyelt valamennyi virtuális gépek?
Az állapotfigyelő szolgáltatás engedélyezve van, akkor is, ha a művelet egyetlen virtuális gép indításakor a Log Analytics-munkaterülethez kapcsolódó összes virtuális gép.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Módosíthatja az ütemezését, mikor állapotára vonatkozó feltételek kiértékeli a feltételt?
Nem, az adott időszakban és gyakoriságát tartalmazza állapotára vonatkozó feltételek nem módosítható ebben a kiadásban. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Letilthatja a állapotára vonatkozó feltételek figyelésére nincs szükség egy adott feltételt?
Ebben a kiadásban nem lehet letiltani a állapotára vonatkozó feltételek.

## <a name="are-the-health-alert-severities-configurable"></a>Az egészségügyi konfigurálható a riasztás súlyossági vannak?  
Riasztás súlyossága állapota nem módosítható, akkor is csak engedélyezhető vagy letiltható. Néhány a riasztás súlyossági frissítése emellett az állapotára vonatkozó feltételek állapota alapján. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Ha e egy adott állapotára vonatkozó feltételek újrakonfigurálnia, azt hatóköre egy adott példányt?  
Egészségügyi feltétel példányok beállítás módosítása esetén az összes állapotfigyelő feltételek példányok az Azure virtuális gépen azonos típusú módosítják. Például ha a küszöb a lemez szabad tárolóhely állapotának feltétel példány megfelelő logikai lemez C: módosítanak, ezt a küszöbértéket vonatkozik az összes többi logikai lemez, amely felderítésének és figyelésének a virtuális gép ugyanazon a.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Az állapotfigyelő szolgáltatás logikai processzorral és magok figyelése?
Nem, az egyes processzor és a logikai processzor szint állapotára vonatkozó feltételek nem tartalmazza a Windows, hatékonyan a CPU nyomás az Azure virtuális gép számára elérhető logikai CPU teljes száma alapján kiértékelheti, hogy alapértelmezés szerint csak a teljes CPU-kihasználtság figyel. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Összes állapotfigyelő konfigurálható feltételek küszöbértékek vannak?  
Küszöbértékek állapotára vonatkozó feltételek, amelyek Windows virtuális gép cél nem módosítható, mert a saját állapotokat vannak beállítva, hogy *futó* vagy *elérhető*. Az állapot a lekérdezésénél a [számítási feladatok a figyelő API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components), megjeleníti a *ÖsszehasonlítóOperátor* értékét **LessThan** vagy **GreaterThan** együtt egy *küszöbérték* értékét **4** a szolgáltatás vagy az entitás ha:
   - DNS-ügyfél szolgáltatásának állapota – szolgáltatás nem fut. 
   - DHCP-ügyfél szolgáltatásának állapota – szolgáltatás nem fut. 
   - RPC szolgáltatás állapota – szolgáltatás nem fut. 
   - Windows tűzfal szolgáltatásának állapota – szolgáltatás nem fut.
   - Windows Eseménynapló szolgáltatás állapota – szolgáltatás nem fut. 
   - Kiszolgálói szolgáltatás állapota – szolgáltatás nem fut. 
   - Windows távoli felügyeleti szolgáltatásának állapota – a szolgáltatás nem fut. 
   - Fájlrendszer hibája vagy -sérülés – logikai lemez nem érhető el.

A következő Linux állapotára vonatkozó feltételek küszöbértékek nem módosítható, azok állapotát már be van állítva mert *igaz*. Állapotát jeleníti meg a *ÖsszehasonlítóOperátor* értékkel **LessThan** és *küszöbérték* értékét **1** a lekérdezéskor a Számítási feladatok figyelés API az entitás a környezettől függően:
   - Logikai lemez állapota – a logikai lemez nem online / érhető el
   - Lemez állapota – a lemez nem online / érhető el
   - Hálózati Adapter állapotát - hálózati adapter le van tiltva.

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Hogyan módosítani az állapotfigyelő szolgáltatás a riasztások?
Riasztási szabályok esetében minden egyes állapotfigyelő feltétel megadott nem jelennek meg az Azure Portalon. Engedélyezheti vagy letilthatja a Szolgáltatásállapot-riasztás kizárólag a szabály a [számítási feladatok a figyelő API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Emellett nem lehet hozzárendelni egy [Azure Monitor műveletcsoport](../../azure-monitor/platform/action-groups.md) health-riasztások az Azure Portalon. Az értesítési beállítás API segítségével csak műveletcsoport, amint egy szolgáltatásállapot-riasztás aktiválódik aktiválását konfigurálhatja. Jelenleg a virtuális gépek elleni a Műveletcsoportok rendelhet, hogy az összes *állapotriasztások* ugyanazt a Műveletcsoportok ellen a virtuális gép eseményindító aktiválva. Ellentétben a hagyományos Azure-riasztások, és nincs külön művelet minden szolgáltatásállapot-riasztási szabály a csoport. Emellett a csak adja meg az e-mailben vagy SMS-értesítések konfigurált Műveletcsoportok health-riasztások előállítása esetén támogatottak. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Virtuális gép nem látható a teljesítménydiagramok néhány vagy minden olyan adatok
Ha a teljesítményadatokat a lemez táblában vagy a teljesítmény diagramok némelyike nem jelenik meg, akkor a teljesítményszámlálók nem konfigurálható a munkaterületen. Futtassa a következő [PowerShell-parancsprogram](vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Miben különbözik az Azure Monitor szolgáltatás virtuális gépek térkép a Service Map?
Virtuális gépek térkép funkció az Azure Monitor Service Map alapul, de rendelkezik a következő eltérésekkel:

* A térképnézet elérhetők a virtuális gép paneljéről és az Azure Monitor alatt az Azure Monitor-beli virtuális gépek.
* A kapcsolatok a térképen kattintható és kapcsolati metrika adatainak megjelenítése a kiszolgálóoldali panelen a kiválasztott kapcsolathoz tartozó.
* Van egy új API-t, amellyel az jobb támogatása érdekében a maps összetettebb térképek létrehozása.
* Figyelt virtuális gépek mostantól szerepelnek az ügyfél csoport csomópontot, és a fánkdiagramot az időarány, amíg a figyelt vs nem monitorozott virtuális gépek megjeleníti a csoportban.  Is használható, ha ki van bontva, a csoport a gépek szűréséhez.
* Figyelt virtuális gépek mostantól beletartoznak a port csoport csomópontok, és a fánkdiagramot az időarány, amíg nem figyelt gépeket figyelt vs megjeleníti a csoportban.  Is használható, ha ki van bontva, a csoport a gépek szűréséhez.
* A térkép stílus egységesebb Alkalmazástérkép az Application insightsból is frissítve lett.
* A kiszolgálóoldali panelek frissítve lett-e, de még nem rendelkezik a teljes körű integráció a Szolgáltatástérkép - frissítéskezelés, változáskövetés, biztonsági és ügyfélszolgálat is támogatott. 
* Leképezni a csoportok és számítógépek kiválasztására vonatkozó beállítás frissítve lett, és már támogatja az előfizetések, erőforráscsoportok, Azure-beli virtuálisgép-méretezési csoportok és a Cloud services.
* Az Azure Monitor szolgáltatás virtuális gépek térkép nem hozhat létre új Service Map gépcsoportok.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Miért jelennek meg saját teljesítménydiagramok pontozott vonal?
Ez akkor fordulhat elő, a több okból is.  Azokban az esetekben, ahol az adatok gyűjtésének eseményáramlási kimaradást azt jelzik a pontozott, a sorokat.  Ha módosította a teljesítményszámlálókkal, engedélyezve van az adatok mintavételi gyakoriság (az alapértelmezett beállítás a adatgyűjtéshez 60 másodpercenként), pontozott vonal a diagramon látható, ha úgy dönt, hogy a diagram keskeny időtartományt, és a mintavételi gyakoriság kevesebb, mint a gyűjtő mérete a diagramon használt (például a mintavételi gyakoriság 10 percenként pedig a diagramon az egyes gyűjtők 5 perc).  Megtekintéséhez szélesebb körű időtartomány kiválasztása eredményezhetik a diagram sorokat pontok helyett a folytonos vonal ebben az esetben jelennek meg.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Csoportok támogatottak az Azure Monitor szolgáltatással a virtuális gépek?
Igen, a csoportok alapján az előfizetés, erőforráscsoport, a megjelenítéséhez a virtuális gépekről gyűjtött információkat függőségi ügynök telepítése után virtuálisgép-méretezési csoportokban, és felhőszolgáltatásokat fejleszthet.  Ha már használja a Service Map és gépcsoportok hozott létre, ezeket is megjelenik.  Ha a munkaterület megtekintésekor hozott számítógépcsoportok is megjelennek a csoportok szűrőt. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hogyan tekinthető meg a részleteket az Előfeltételek a 95. percentilis vonal aggregált teljesítménydiagramok?
Alapértelmezés szerint a lista rendezett mutatni, a virtuális gépek, amelyek esetén a 95. percentilis a kiválasztott metrika a legmagasabb érték kivételével a rendelkezésre álló memória diagram, amely a gépek az 5. percentilis legalacsonyabb értékét jeleníti meg.  A diagramra kattintva megnyílik a **felső N lista** a megfelelő a kiválasztott metrika tartalmazó nézetet.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hogyan a térkép szolgáltatás kezeli ismétlődő IP-címek különböző virtuális hálózatok és alhálózatok között?
Ha meg vannak duplikálásához IP-címtartományok vagy virtuális gépek vagy az Azure-beli virtuálisgép-méretezési csoportokban alhálózatok és virtuális hálózatok, virtuális gépeket a térképen helytelen információk megjelenítése az Azure Monitor okozhat. Ez egy ismert probléma az, és vizsgálja az beállításokat, amelyekkel javíthatja a felhasználói élményt.

## <a name="does-map-feature-support-ipv6"></a>A szolgáltatás támogatja az IPv6 nem leképezés?
Térkép szolgáltatás jelenleg csak támogatja IPv4 és IPv6-támogatás vizsgálja. IPv4, IPv6 belül bújtatott is támogatja.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Ha egy erőforráscsoportot, vagy más nagy csoport esetén egy térkép betölteni a térkép is nehézkes megtekintése
Fejlesztéseket hajtottunk végre térképhez nagy és összetett konfigurációk kezelésére, miközben tisztában vagyunk vele egy térkép is sok csomópontokat, a kapcsolatok és a csomópont fürtként működik.  Kötelességünknek tekintjük, folyamatos támogatás bővítése érdekében növelése érdekében.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Miért nem a hálózati diagram a Teljesítmény lapon máshogy néznek ki, mint a hálózati diagram az Azure virtuális gépek – Áttekintés oldalon?

Az Áttekintés oldal egy Azure virtuális gép a gazdagép mérési tevékenység a Vendég virtuális Gépen alapuló diagramot jelenít meg.  A hálózati diagram az Azure virtuális gépek áttekintése a hálózati forgalom, számlázunk csak megjeleníti.  Ez nem tartalmazza a virtuális hálózatok közötti forgalmat.  Az adatok és a virtuális gépek az Azure Monitor látható diagramokat adatait a Vendég virtuális Gépen alapul, és a hálózati diagram, amely a virtuális Gépre, beleértve a virtuális hálózatok közötti kimenő és bejövő összes TCP/IP-forgalmat jeleníti meg.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>A korlátozás, ha a Log Analytics ingyenes díjszabással vagyok?
Ha már konfigurálta az Azure Monitor egy Log Analytics munkaterület használatával a *ingyenes* tarifacsomagban van, az Azure Monitor tartozó virtuális gépek térkép funkció csak öt csatlakoztatott gépek támogatni fogja a munkaterülethez csatlakozik. Ha rendelkezik egy ingyenes munkaterülethez csatlakoztatott öt virtuális gépek, válassza le a virtuális gépek közül, és később fogja összekapcsolni egy új virtuális Gépet, az új virtuális gép nem felügyelt és a térkép oldalon megjelennek.  

Ez a feltétel alapján meg kell adnia a a **próbálja ki most** beállítást, nyissa meg a virtuális Gépet, majd válassza **Insights (előzetes verzió)** a bal oldali panelen, még akkor is, miután a virtuális gépen már telepítették.  Azonban nem kéri a beállításokkal, akkor általában fordulhat elő, ha a virtuális gép nem lett előkészítve az Azure Monitor-beli virtuális gépek. 

## <a name="next-steps"></a>További lépések
Felülvizsgálat [előkészítése az Azure Monitor-beli virtuális gépek](vminsights-onboard.md) követelmények és módszerek ahhoz, hogy a virtuális gépek figyelése.
