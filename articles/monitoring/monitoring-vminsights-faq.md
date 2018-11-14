---
title: Az Azure Monitor-beli virtuális gépek (előzetes verzió) – gyakori kérdések |} A Microsoft Docs
description: Az Azure Monitor-beli virtuális gépek (előzetes verzió) egy olyan megoldás, az Azure-ban, amely egyesíti az állapotának és teljesítményének figyelése az Azure virtuális gép operációs rendszerének, valamint alkalmazás-összetevők és az egyéb erőforrások függőségeinek automatikus felderítése, és feltérképezi a kommunikációt közöttük. Ez a cikk gyakori kérdésekre ad választ.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: 7d65752787e65eeabf203f9bdc3ea11a1569f8a1
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613375"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Gyakori kérdések az Azure Monitor-beli virtuális gépek (előzetes verzió)
A Microsoft FAQ az összetevővel kapcsolatos gyakori kérdésekre az Azure Monitor-beli virtuális gépek listája. Ha a megoldásról a további kérdése van, lépjen a [fórum](https://feedback.azure.com/forums/34192--general-feedback) és felteheti kérdéseit. Egy kérdést gyakran ismételt, amikor hozzáadjuk ehhez a cikkhez, hogy gyorsan és könnyen megtalálhatók.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Lehetőségeket felvétele egy meglévő munkaterületet?
Ha a virtuális gépek már kapcsolódik egy Log Analytics-munkaterületet, továbbra is munkaterületet használja, ha bevezetése az Azure monitornak a virtuális gépek esetében megadtuk a felsorolt támogatott régiók egyikében [Itt](monitoring-vminsights-onboard.md#prerequisites).

Amikor végzett előkészítési műveletekhez, konfigurálnánk teljesítményszámlálóit a munkaterületet, amely újraindítja a virtuális gépek mindegyikének adatokról szóló jelentéseket küldeni a munkaterületre való gyűjtésére a virtuális gépek megjelenítése és elemzése az Azure monitorban információit.  Ennek eredményeképpen a virtuális gépeket a kijelölt munkaterülethez kapcsolódó összes teljesítményadatokat megjelenik.  Az egészségügyi és térkép funkciók csak engedélyezve vannak a virtuális gépek, amelyek a megadott felvétele.

További információ a teljesítményszámlálók számlálók engedélyezve vannak, tekintse meg a [bevezetési](monitoring-vminsights-onboard.md) cikk.

## <a name="can-i-onboard-to-a-new-workspace"></a>Lehetőségeket felvétele egy új munkaterületet? 
Ha a virtuális gépek jelenleg nem csatlakoznak a meglévő Log Analytics-munkaterületet, létre kell hozzon létre egy új munkaterületet, az adatok tárolásához.  Egy új alapértelmezett munkaterületet automatikusan történik, ha konfigurál egy Azure virtuális Gépen az Azure Monitor-beli virtuális gépek az Azure Portalon keresztül.

Ha használja a parancsprogramfájlon alapuló módszert választja, ezeket a lépéseket ismertetjük a [bevezetési](monitoring-vminsights-onboard.md) cikk. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Mit tegyek, ha a virtuális gépem egy létező munkaterülethez már jelent?
Már a virtuális gépekről származó adatokat gyűjt, ha lehetséges, hogy már konfigurálta a azt a jelentésadatok meglévő Log Analytics-munkaterülethez.  Mindaddig, amíg a munkaterület a támogatott régiók valamelyikében van, a már meglévő munkaterületet az Azure Monitor engedélyezheti a virtuális gépek.  Ha már használja a munkaterületet nem a támogatott régiók egyikében, nem kell üzembe helyezni az Azure monitornak a virtuális gépek jelenleg.  Aktívan dolgozunk további régiók támogatása.

>[!NOTE]
>A munkaterület-e kíván üzembe helyezni őket az Azure Monitor-beli virtuális gépek összes virtuális gépet, amely a munkaterületnek érintő teljesítményszámlálóit konfigurálja azt. A teljesítményszámlálók konfigurációjától a munkaterületre vonatkozó további információért tekintse meg a [dokumentáció](../log-analytics/log-analytics-data-sources-performance-counters.md). A virtuális gépek az Azure Monitor konfigurált számlálók kapcsolatos információkért tekintse meg a [előkészítési dokumentáció](monitoring-vminsights-onboard.md#performance-counters-enabled).  

## <a name="why-did-my-vm-fail-to-onboard"></a>A virtuális gépem miért volt sikertelen üzembe helyezni?
Ha az Azure Portalon egy Azure virtuális Gépen végzett előkészítési műveletekhez, a következő lépések történnek:

* Alapértelmezett Log Analytics-munkaterület jön létre, ha, amely lehetőséget választotta.
* A teljesítményszámlálók kijelölt munkaterülethez van konfigurálva. Ha ezt a lépést nem sikerül, megfigyelheti, hogy adatokat a teljesítmény-diagramok és táblázatok némelyike nem jelennek meg a virtuális gép előkészítése. A dokumentált PowerShell-parancsfájl futtatásával is megoldásához [Itt](monitoring-vminsights-onboard.md#enable-with-powershell).
* A Log Analytics-ügynök telepítve van egy VM-bővítmény használata az Azure virtuális gépek esetén szükség rá.  
* A virtuális gépek térkép függőségi ügynök az Azure Monitor-bővítmény használata Azure virtuális gépeken telepítve van, ha szükség rá.  
* Az állapotfigyelő szolgáltatást támogató Azure figyelő-összetevőkkel vannak konfigurálva, ha szükséges, és a virtuális gép van beállítva a jelentés egészségügyi adatok.

A előkészítése során ellenőrizzük állapotát az egyes való visszatéréshez értesítési állapotot, a portálon a fentiek közül.  A munkaterület és az ügynök telepítésének konfigurációja általában 5-10 percet vesz igénybe.  A portálon figyelési és adatok megtekintése egy további 5-10 percet vehet igénybe.  

Ha bevezetési kezdeményezett, és tekintse meg a virtuális Gépet kell előkészíteni elérhetőségére, lehetővé teszi a virtuális gép a folyamat akár 30 percig. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Virtuális gép nem látható a teljesítménydiagramok néhány vagy minden olyan adatok
Ha a teljesítményadatokat a lemez táblában vagy a teljesítmény diagramok némelyike nem jelenik meg, akkor a teljesítményszámlálók nem konfigurálható a munkaterületen. Futtassa a következő [PowerShell-parancsprogram](monitoring-vminsights-onboard.md#enable-with-powershell).

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
Felülvizsgálat [előkészítése az Azure Monitor-beli virtuális gépek](monitoring-vminsights-onboard.md) követelmények és módszerek ahhoz, hogy a virtuális gépek figyelése.
