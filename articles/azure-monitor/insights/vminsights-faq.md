---
title: Azure Monitor for VMs (előzetes verzió) – gyakori kérdések | Microsoft Docs
description: Válaszok az Azure-beli virtuális gépek állapotát és teljesítményét figyelő Azure Monitor for VMs gyakori kérdéseire az alkalmazás-összetevők és függőségeik automatikus felfedése és hozzárendelése mellett.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.openlocfilehash: fa47606112c562402a42bd5ca503ed2d9a311268
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400382"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Azure Monitor for VMs (előzetes verzió) – gyakori kérdések
A Microsoft gyakori kérdései a Azure Monitor for VMskal kapcsolatos gyakori kérdések listája. Ha további kérdései vannak a megoldással kapcsolatban, látogasson el a [vitafórumra](https://feedback.azure.com/forums/34192--general-feedback) , és tegye fel kérdéseit. Ha egy kérdést gyakran megkérdeznek, azt a cikkhez adja hozzá, hogy gyorsan és könnyen elérhető legyen.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Bejelentkezhetek egy meglévő munkaterületre?
Ha a virtuális gépek már csatlakoznak egy Log Analytics-munkaterülethez, akkor továbbra is használhatja ezt a munkaterületet Azure Monitor for VMsba való bevezetéskor, amennyiben az [itt](vminsights-enable-overview.md#prerequisites)felsorolt támogatott régiók egyikében található.

A bevezetéskor a munkaterülethez olyan teljesítményszámlálók vannak konfigurálva, amelyek az összes, a munkaterületnek adatokat jelentő virtuális gépet begyűjtik, hogy a megjelenítéshez és az elemzéshez Azure Monitor for VMs az adatokat.  Ennek eredményeképpen a kiválasztott munkaterülethez csatlakoztatott összes virtuális gép teljesítményadatait fogja látni.  Az állapot és a leképezés funkció csak a bevezetéshez megadott virtuális gépek esetében engedélyezett.

A teljesítményszámlálók engedélyezésével kapcsolatos további információkért tekintse meg az [Engedélyezés áttekintését ismertető](vminsights-enable-overview.md#performance-counters-enabled) cikket.

## <a name="can-i-onboard-to-a-new-workspace"></a>Bejelentkezhetek egy új munkaterületre? 
Ha a virtuális gépek jelenleg nem kapcsolódnak meglévő Log Analytics-munkaterülethez, létre kell hoznia egy új munkaterületet az adatai tárolásához. Az új alapértelmezett munkaterület létrehozása automatikusan történik, ha egyetlen Azure-beli virtuális gépet állít be Azure Monitor for VMs a Azure Portal keresztül.

Ha úgy dönt, hogy a parancsfájl-alapú módszert használja, ezeket a lépéseket a [Azure PowerShell vagy Resource Manager-sablon használatával foglalkozó Azure monitor for VMS engedélyezése (előzetes verzió)](vminsights-enable-at-scale-powershell.md) című cikk tartalmazza. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Mi a teendő, ha a virtuális gép már jelent egy meglévő munkaterületet?
Ha már begyűjti az adatait a virtuális gépekről, lehetséges, hogy már konfigurálta az adatok jelentését egy meglévő Log Analytics-munkaterületre.  Ha a munkaterület az egyik támogatott régióban található, akkor a Azure Monitor for VMst engedélyezheti a meglévő munkaterületen.  Ha a már használt munkaterület nem a támogatott régiókban található, akkor a Azure Monitor for VMs jelenleg nem fog tudni bejelentkezni.  Aktívan dolgozunk a további régiók támogatásán.

>[!NOTE]
>A munkaterülethez tartozó összes virtuális gépet érintő teljesítményszámlálókat a munkaterülethez konfiguráljuk, függetlenül attól, hogy Azure Monitor for VMsbe kívánja-e bevezetni őket. A munkaterülethez tartozó teljesítményszámlálók konfigurálásával kapcsolatos további információkért tekintse meg a [dokumentációt](../../azure-monitor/platform/data-sources-performance-counters.md). A Azure Monitor for VMshoz konfigurált számlálókkal kapcsolatos információkért tekintse meg a [Azure monitor for VMS engedélyezése](vminsights-enable-overview.md#performance-counters-enabled) című cikket.  

## <a name="why-did-my-vm-fail-to-onboard"></a>Miért nem sikerült bejelentkezni a virtuális gépre?
Amikor Azure-beli virtuális gépet telepít a Azure Portalból, a következő lépések történnek:

* Ha ez a beállítás be van jelölve, egy alapértelmezett Log Analytics munkaterület jön létre.
* A teljesítményszámlálók konfigurálva vannak a kiválasztott munkaterülethez. Ha ez a lépés meghiúsul, láthatja, hogy a teljesítmény-diagramok és a táblák némelyike nem jeleníti meg a bekészített virtuális gép adatait. Ezt az [itt](vminsights-enable-at-scale-powershell.md#enable-performance-counters)leírt PowerShell-szkript futtatásával javíthatja.
* A Log Analytics ügynök virtuálisgép-bővítmény használatával települ az Azure-beli virtuális gépekre, ha azt meg kell határozni.  
* A Azure Monitor for VMs Map-függőségi ügynök az Azure-beli virtuális gépeken bővítmény használatával van telepítve, ha azt meg kell határozni.  
* Azure Monitor az állapotfigyelő funkciót támogató összetevők konfigurálása szükséges, és a virtuális gép az állapotadatok jelentésére van konfigurálva.

A bevezetési folyamat során a fent ismertetett állapotot vizsgáljuk, hogy az értesítési állapotot a portálon vissza lehessen adni. A munkaterület és az ügynök telepítésének konfigurálása általában 5 – 10 percet vesz igénybe. A figyelési és egészségvédelmi információk megtekintése a portálon további 5 – 10 percet vesz igénybe.  

Ha kezdeményezte a bevezetést, és megtekinti, hogy a virtuális gépet be kell-e állítani, akkor akár 30 percet is igénybe vehet, amíg a virtuális gép elvégezheti a folyamatot. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Csak a Azure Monitor for VMs Engedélyeztem, Miért látok minden virtuális gépet az állapotfigyelő szolgáltatás által figyelt állapottal?
Az állapotfigyelő szolgáltatás minden olyan virtuális gép esetében engedélyezve van, amely a Log Analytics munkaterülethez van csatlakoztatva, még akkor is, ha a művelet egyetlen virtuális gépre van kezdeményezve.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Módosíthatom azt az időpontot, amikor az állapot feltételei kiértékelik a feltételt?
Nem, az időtartam és az állapot gyakorisága nem módosítható ezzel a kiadással. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Letilthatom az állapotra vonatkozó feltételeket olyan feltételnél, amelyet nem kell figyelni?
Ebben a kiadásban nem lehet letiltani az állapotra vonatkozó feltételeket.

## <a name="are-the-health-alert-severities-configurable"></a>Konfigurálható az állapot-riasztási tartomány?  
Az állapot riasztásának súlyossága nem módosítható, csak engedélyezhető vagy letiltható. Emellett néhány riasztási tagság az állapot feltételei alapján frissül. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Ha újrakonfiguráltam egy adott állapotra vonatkozó feltétel beállításait, hatóköre alkalmazható egy adott példányra?  
Ha módosítja az állapot-feltétel példányának bármelyik beállítását, akkor az Azure-beli virtuális gépen található azonos típusú összes állapotra vonatkozó feltétel módosítva lesz. Ha például a lemez szabad területének állapotára vonatkozó feltétel példánya a C logikai lemeznek felel meg, akkor ez a küszöbérték az azonos virtuális géphez felderített és felügyelt összes többi logikai lemezre vonatkozik.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Figyeli az állapotfigyelő szolgáltatás a logikai processzorokat és a magokat?
Nem, az egyes processzorok és a logikai processzorok szintjének állapotára vonatkozó feltételek nem szerepelnek a Windowsban, a rendszer alapértelmezés szerint csak a teljes CPU-kihasználtságot figyeli, hogy az Azure-beli virtuális gép számára elérhető logikai processzorok száma alapján hatékonyan kiértékelje a processzor terhelését. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Az összes állapotra vonatkozó feltétel küszöbértéke konfigurálható?  
A Windows rendszerű virtuális gépeket megcélozó állapotra vonatkozó feltételek küszöbértékei nem módosíthatók, mert az állapotuk a *Futtatás* vagy az *elérhető*állapotra van állítva. Amikor lekérdezi az állapotot a [munkaterhelés-figyelő API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)-ból, a **LessThan** vagy a **GreaterThan** *ÖsszehasonlítóOperátor* értékét a szolgáltatás vagy az entitás esetében a **4-es** *értékkel* jeleníti meg, ha:
   - A DNS-ügyfél Service Health – a szolgáltatás nem fut. 
   - DHCP-ügyfélszolgáltatás állapota – a szolgáltatás nem fut. 
   - RPC Service Health – a szolgáltatás nem fut. 
   - A Windows tűzfal szolgáltatás állapota – a szolgáltatás nem fut.
   - A Windows Eseménynapló szolgáltatás állapota – a szolgáltatás nem fut. 
   - Kiszolgáló szolgáltatás állapota – a szolgáltatás nem fut. 
   - A Windows távfelügyeleti szolgáltatás állapota – a szolgáltatás nem fut. 
   - Fájlrendszer hibája vagy sérülése – a logikai lemez nem érhető el.

A következő linuxos állapotra vonatkozó feltételek küszöbértékei nem módosíthatók, mert az állapotuk már *igaz*értékre van állítva. Az állapot a **LessThan** és az **1** . *küszöbértéket* tartalmazó *ÖsszehasonlítóOperátor* jeleníti meg, amikor a rendszer az entitáshoz tartozó munkaterhelés-figyelési API-ból kérdezi le, a környezettől függően:
   - Logikai lemez állapota – a logikai lemez nem online/elérhető
   - Lemez állapota – a lemez nem online állapotú/elérhető
   - Hálózati adapter állapota – a hálózati adapter le van tiltva

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Hogyan módosítja a Health szolgáltatásban található riasztásokat?
Az egyes állapot-kritériumokhoz definiált riasztási szabályok nem jelennek meg a Azure Portal. Csak a [munkaterhelés-figyelő API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)-ban engedélyezheti vagy tilthatja le az állapot riasztási szabályait. A Azure Portalban nem rendelhet [Azure monitor műveleti csoportot](../../azure-monitor/platform/action-groups.md) az állapottal kapcsolatos riasztásokhoz. Az értesítési beállítások API-val csak akkor állítható be, ha egy adott állapotra figyelmeztető riasztást indít el. Jelenleg a virtuális gépekhez hozzárendelheti a műveleti csoportokat, hogy az összes, a virtuális gépen indított *állapottal kapcsolatos riasztás* ugyanazzal a műveleti csoporttal legyen elindítva. A hagyományos Azure-riasztásokkal ellentétben az egyes állapot-riasztási szabályokhoz nem tartoznak külön műveleti csoportok. Emellett csak az e-mailek vagy SMS-értesítések küldésére konfigurált műveleti csoportok támogatottak az állapot-riasztások aktiválásakor. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Nem látok olyan információt, amely a virtuális gép teljesítmény-diagramjaiban található
Ha nem látja a teljesítményadatokat a lemez táblában vagy egyes teljesítmény-diagramoknál, akkor előfordulhat, hogy a teljesítményszámlálók nem konfigurálhatók a munkaterületen. A megoldáshoz futtassa a következő [PowerShell-szkriptet](vminsights-enable-at-scale-powershell.md#enable-with-powershell).

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Miben különbözik az Azure Monitor for VMs Map funkció a Service Maptól?
Az Azure Monitor for VMs-leképezési funkció Service Mapon alapul, de a következő különbségek vannak:

* A Térkép nézet a virtuális gép paneljéről, illetve a Azure Monitor alatt Azure Monitor for VMs érhető el.
* A térképen lévő kapcsolatok most már rákattintanak, és megjelenítik a kapcsolat metrikájának adatait a kijelölt kapcsolat oldalsó paneljén.
* Létezik egy új API, amely a térképek létrehozásához használható összetettebb térképek jobb támogatásához.
* A figyelt virtuális gépek már szerepelnek az ügyféloldali csoport csomópontban, és a fánk diagramon látható a csoportban lévő figyelt vs nem figyelt virtuális gépek aránya.  A csoport kibontásakor a számítógépek listájának szűrésére is használható.
* A figyelt virtuális gépek már szerepelnek a kiszolgáló portjának csomópontjaiban, és a fánk diagramon a figyelt és a nem figyelt gépek aránya látható a csoportban.  A csoport kibontásakor a számítógépek listájának szűrésére is használható.
* A Térkép stílusa úgy lett frissítve, hogy jobban konzisztens legyen az Application-adatokból származó app Map szolgáltatással.
* Az oldalsó panelek frissítve lettek, és nem rendelkeznek a Service Map-Update Management, a Change Tracking, a Security és a Service Desk által támogatott teljes integrációs készlettel. 
* A csoportok és gépek leképezésre való kiválasztásának lehetősége frissítve lett, és mostantól támogatja az előfizetéseket, az erőforráscsoportok, az Azure virtuálisgép-méretezési csoportokat és a Cloud Services szolgáltatást.
* Az új Service Map számítógépcsoportok nem hozhatók létre a Azure Monitor for VMs Map szolgáltatásban.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Miért mutatnak pontozott vonalakat a teljesítmény-diagramok?
Ez néhány ok miatt fordulhat elő.  Abban az esetben, ha az adatgyűjtésben hézag van, a vonalakat pontozott értékre ábrázoljuk.  Ha módosította az adatok mintavételi gyakoriságát az engedélyezett teljesítményszámlálók esetében (az alapértelmezett beállítás az adatok összegyűjtése 60 másodpercenként), akkor a diagramon szaggatott vonalakat láthat, ha a diagramhoz a keskeny időtartományt választja, és a mintavételi gyakoriság kisebb, mint a diagramon használt gyűjtő mérete (például a mintavételezési gyakoriság 10 percenként, a diagram minden gyűjtője pedig 5 perc).  Ha szélesebb időtartományt szeretne megtekinteni, akkor a diagram sorai nem pontokként, hanem folytonos vonalakként jelennek meg.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Támogatottak-e a csoportok Azure Monitor for VMs?
Igen, a függőségi ügynök telepítése után adatokat gyűjtünk a virtuális gépekről az előfizetés, az erőforráscsoport, a virtuálisgép-méretezési csoportok és a Cloud Services alapján.  Ha már használta a Service Map és létrehozott számítógép-csoportokat, ezek is megjelennek.  A számítógépcsoportok akkor is megjelennek a csoportok szűrőben, ha létrehozta őket a megtekintett munkaterülethez. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hogyan tekintse meg a 95. percentilis-sor az összesített teljesítményű diagramokon való vezetésének részleteit?
Alapértelmezés szerint a lista úgy van rendezve, hogy megjelenítse azokat a virtuális gépeket, amelyek a 95. percentilis legmagasabb értékkel rendelkeznek a kiválasztott metrika esetében, kivéve a rendelkezésre álló memória diagramot, amely az 5. percentilis legalacsonyabb értékkel rendelkező gépeket jeleníti meg.  A diagramra kattintva megnyílik a **legfelső N listanézet** nézet a megfelelő metrika kiválasztásával.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hogyan kezeli a Map szolgáltatás a duplikált IP-címeket különböző virtuális hálózatok és alhálózatokon?
Ha az IP-tartományokat virtuális gépek vagy Azure virtuálisgép-méretezési csoportok között duplikálja az alhálózatok és a virtuális hálózatok között, akkor Azure Monitor for VMs leképezés helytelen információk megjelenítésére vezethet. Ez egy ismert probléma, amely a tapasztalatok fejlesztését vizsgálja.

## <a name="does-map-feature-support-ipv6"></a>Támogatja az IPv6 a Map funkciót?
A Térkép funkció jelenleg csak az IPv4-t támogatja, és az IPv6 támogatását vizsgáljuk. Az IPv6-on belül bújtatott IPv4-t is támogatja.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Egy erőforráscsoport vagy más nagyméretű csoport térképének betöltésekor nehéz megtekinteni a térképet
Habár a nagy és összetett konfigurációk kezelésére tettük elérhetővé a térképet, rájövünk, hogy a térképen számos csomópont, kapcsolat és csomópont működik fürtként.  Elkötelezettek vagyunk a skálázhatóság növelésének támogatásával.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Miért különbözik a hálózati diagram a teljesítmény lapon, mint a hálózati diagram az Azure-beli virtuális gépek áttekintő oldalán?

Az Azure-beli virtuális gépek áttekintő lapja diagramokat jelenít meg a vendég virtuális gép tevékenységének mérése alapján.  Az Azure-beli virtuális gépek áttekintő hálózati diagramja csak a számlázásra kerülő hálózati forgalmat jeleníti meg.  Ez nem tartalmazza a vnet közötti forgalmat.  A Azure Monitor for VMs számára megjelenített adatok és diagramok a vendég virtuális gépről származó adatokon alapulnak, és a hálózati diagram megjeleníti a virtuális gép számára bejövő és kimenő összes TCP/IP-forgalmat, beleértve a vnet is.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hogyan mérjük a válaszidő a VMConnection tárolt és a kapcsolatok paneljén és a munkafüzetek között?

A válaszidő egy közelítés. Mivel nem használjuk az alkalmazás kódját, nem igazán tudjuk, hogy mikor kezdődik a kérelem, és mikor érkezik a válasz. Ehelyett megfigyeljük, hogy a rendszer egy kapcsolatban küldi az adatküldést, majd az adott kapcsolatban visszaérkező adatforrásokat. Az ügynök nyomon követi ezeket a küldési és fogadási kísérleteket, és párosítja őket: az elküldött sorok sorozata, amelyet a fogadások sorozata, a kérelem/válasz pároknak kell értelmezni. A műveletek közötti időzítés a válaszidő. Ez magában foglalja a hálózati késést és a kiszolgáló feldolgozási idejét is.

Ez a közelítés a kérelmek/válaszok alapjául szolgáló protokollok esetében jól működik: egyetlen kérelem érkezik a hálózatra, és egyetlen válasz érkezik. Ez a HTTP (S) esetén (csővezeték nélkül), de más protokollok esetében nem teljesül.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>A korlátozások vonatkoznak a Log Analytics ingyenes díjszabási csomagra?
Ha az *ingyenes* díjszabási csomaggal konfigurálta a Azure monitort egy log Analytics munkaterülettel, Azure monitor for VMS a Térkép funkció csak a munkaterülethez csatlakozó öt csatlakoztatott gépet fogja támogatni. Ha az ingyenes munkaterülethez öt virtuális gép csatlakozik, az egyik virtuális gép leválasztását követően később egy új virtuális gépet csatlakoztat, az új virtuális gép nem lesz figyelve, és a Térkép oldalon jelenik meg.  

Ebben az esetben a rendszer megkéri a **kipróbálás most** lehetőséget, amikor megnyitja a virtuális gépet, és kiválasztja az adatok **(előzetes verzió)** elemet a bal oldali ablaktáblán, még azután is, hogy már telepítette a virtuális gépre.  Azonban nem kell megadnia a beállításokat, mivel ez általában akkor fordul elő, ha a virtuális gép nem lett előkészítve a Azure Monitor for VMs. 

## <a name="next-steps"></a>Következő lépések
A virtuális gépek figyelésének engedélyezéséhez tekintse át a [Azure monitor for VMS engedélyezése](vminsights-enable-overview.md) a követelmények és módszerek megismeréséhez című témakört.
