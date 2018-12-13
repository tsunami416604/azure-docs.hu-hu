---
title: Az Azure Monitor-beli virtuális gépek (előzetes verzió) – gyakori kérdések |} A Microsoft Docs
description: Az Azure Monitor-beli virtuális gépek (előzetes verzió) egy olyan megoldás, az Azure-ban, amely ötvözi az állapotának és teljesítményének figyeléséhez, az Azure virtuális gép operációs rendszerének. Automatikusan felderíti az alkalmazás-összetevők és az egyéb erőforrások függőségeket, és leképezi a köztük folyó kommunikációt. Ez a cikk gyakori kérdésekre ad választ.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: a97a7be0eaa8438a4df27b610106ec6ab9f60d30
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184391"
---
# <a name="azure-monitor-for-vms-preview-faq"></a>Az Azure Monitor-beli virtuális gépek (előzetes verzió) – gyakori kérdések
Ez a cikk a virtuális gépek az Azure Monitor kapcsolatos gyakori kérdésekre ad választ. Ha a megoldásról a további kérdése van, lépjen a [Azure vitafórumunkban](https://feedback.azure.com/forums/34192--general-feedback) és felteheti kérdéseit. Gyakori kérdések kéri, ha azt hozzá őket az ebben a cikkben található gyorsan és egyszerűen.

## <a name="can-i-deploy-vms-to-an-existing-workspace"></a>Telepíthetek virtuális gépeket egy meglévő munkaterületet?
Ha a virtuális gépek már kapcsolódik egy Log Analytics-munkaterületet, továbbra is használja a munkaterületet, ha telepítheti őket az Azure Monitor-beli virtuális gépek. A munkaterület léteznie kell az "Előfeltételek" szakaszában felsorolt támogatott régiók egyikében [-beli virtuális gépek (előzetes verzió) üzembe helyezése az Azure Monitor](vminsights-onboard.md#prerequisites).

Üzembe helyezés során az azt konfigurálja a munkaterület teljesítményszámlálóit. A művelet hatására a virtuális gépek, amelyek a munkaterületre vonatkozó információk gyűjtésére a jelentés adatainak megjelenítéséhez és elemzése az Azure Monitor-beli virtuális gépek. Ennek eredményeképpen látni fogja a kijelölt munkaterülethez csatlakozó virtuális gépek mindegyikének teljesítményadatokat. Az egészségügyi és térkép funkciók csak az üzembe helyezéshez megadott virtuális gépek esetében engedélyezve van.

Számlálók engedélyezett melyik teljesítményével kapcsolatos további információkért lásd: [-beli virtuális gépek (előzetes verzió) üzembe helyezése az Azure Monitor](vminsights-onboard.md).

## <a name="can-i-deploy-vms-to-a-new-workspace"></a>Telepíthetek virtuális gépeket egy új munkaterületet? 
Ha a virtuális gépek jelenleg nem csatlakoznak a meglévő Log Analytics-munkaterülethez, létre kell hozzon létre egy új munkaterületet, az adatok tárolásához. Létrehozhat egy által automatikusan konfigurálása egyetlen virtuális Gépet az Azure Monitor-beli virtuális gépek az Azure Portalon.

Ha a parancsprogramfájlon alapuló módszert használja, lásd: [-beli virtuális gépek (előzetes verzió) üzembe helyezése az Azure Monitor](vminsights-onboard.md). 

## <a name="what-can-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Mit tehetek, ha a virtuális gépem egy létező munkaterülethez már jelent?
Ha már a virtuális gépekről származó adatokat gyűjt, előfordulhat, hogy már konfigurálta a őket a jelentés adatai meglévő Log Analytics-munkaterülethez. Mindaddig, amíg a munkaterület a támogatott régiók valamelyikében van, a már meglévő munkaterületet az Azure Monitor engedélyezheti a virtuális gépek. Aktívan dolgozunk további régiók támogatása.

>[!NOTE]
>Teljesítményszámlálók a munkaterületet, amely hatással van az összes virtuális gépet, amely a munkaterületnek, üzembe helyezheti őket az Azure Monitor-beli virtuális gépek választotta-e a konfigurálja azt. "Konfigurálása teljesítményszámlálók" című szakaszában talál további információt a teljesítményszámlálók a munkaterületre vonatkozó konfigurációjától [a Log Analytics Windows és Linux rendszerű teljesítmény adatforrások](../../azure-monitor/platform/data-sources-performance-counters.md). A virtuális gépek az Azure Monitor konfigurált számlálók kapcsolatos információkért lásd: [-beli virtuális gépek (előzetes verzió) üzembe helyezése az Azure Monitor](vminsights-onboard.md). 

## <a name="why-did-my-vm-deployment-fail"></a>Miért volt sikertelen a virtuális gép üzembe helyezésének?
Amikor telepít egy Azure virtuális Gépen az Azure Portalról, az alábbi események történnek:

* Alapértelmezett Log Analytics-munkaterület jön létre, ha, amely lehetőséget választotta.
* A teljesítményszámlálók a kijelölt munkaterület vannak konfigurálva. Ha ezt a lépést nem sikerül, adatok néhány teljesítmény diagramok és táblázatok ne jelenjen meg az üzembe helyezett virtuális gép számára. A probléma megoldásához "Engedélyezése a PowerShell" szakaszában ismertetett PowerShell-parancsfájl futtatásával [-beli virtuális gépek (előzetes verzió) üzembe helyezése az Azure Monitor](vminsights-onboard.md#enable-with-powershell).
* A Log Analytics-ügynök telepítve van az Azure virtuális gépekhez egy Virtuálisgép-bővítménnyel, ha ez szükséges. 
* Az Azure Monitor, virtuális gépek térkép függőségi ügynök telepítve van az Azure virtuális gépekhez kiterjesztésű, ha ez szükséges. 
* Azure Monitor-összetevőket, amelyek támogatják az állapotfigyelő szolgáltatás vannak konfigurálva, ha szükséges, és a virtuális gép van beállítva a jelentés egészségügyi adatok.

Üzembe helyezés során a azt a állapotának minden az előző lépések és térjen vissza egy értesítési állapotot, a portálon. A munkaterület és az ügynök telepítésének konfigurációja általában 5-10 percet vesz igénybe. Az Azure Portalon figyelési és adatok megtekintése egy további 5-10 percet vehet igénybe. 

Ha a központi telepítés kezdeményezett, és tekintse meg az üzeneteket, amely azt jelzi, hogy a virtuális Gépet kell üzembe helyezni, lehetővé teszik a virtuális gép a folyamat akár 30 percig. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Virtuális gép nem látható a teljesítménydiagramok néhány vagy minden olyan adatok
A lemez vagy a teljesítmény-diagramok a teljesítményadatok nem jelenik meg, ha a teljesítményszámlálók nem lehet konfigurálni a munkaterület. A probléma megoldásához futtassa a PowerShell-parancsfájlt, amely a "Engedélyezése a PowerShell" szakaszban ismertetett [-beli virtuális gépek (előzetes verzió) üzembe helyezése az Azure Monitor](vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-the-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Miben különbözik a virtuális gépek térkép a szolgáltatás az Azure Monitor Service Map?
Virtuális gépek térkép funkció az Azure Monitor Service Map alapul, de rendelkezik a következő eltérésekkel:

* A virtuális gép panelről, és az Azure Monitor alatt az Azure Monitor-beli virtuális gépek is hozzáférhet a térképnézet.
* A kapcsolatok a térképen kattintható és kapcsolati metrika adatait jelenítse meg az oldal panel.
* Új API segítségével, így jobban elősegítve a maps összetettebb térképek létrehozása.
* Figyelt virtuális gépek vannak, most az ügyfél csoport csomópontot, és a fánkdiagram diagram a figyelt a nem monitorozott virtuális gépek arányát jeleníti meg. Ha ki van bontva, a csoport is szűrheti a gépek listáját.
* Figyelt virtuális gépek mostantól a port csoport csomópontok, és a fánkdiagram figyelt a nem figyelt gépeket arányát jeleníti meg. Ha ki van bontva, a csoport is szűrheti a gépek listáját.
* A térkép stílus több konzisztens az Azure Application insights az Alkalmazástérkép frissítve lett.
* A kiszolgálóoldali panelek frissítve lett-e, de még nem rendelkezik a Service Map támogató Integrációk teljes készlete: A frissítéskezelés, változáskövetés, biztonsági és ügyfélszolgálat. 
* Frissítve lett való leképezéséhez a csoportok és számítógépek kiválasztására vonatkozó beállítás. Az előfizetések, erőforráscsoportok, Azure-beli virtuálisgép-méretezési csoportok és a Cloud services mostantól támogatja.
* Az Azure Monitor szolgáltatás virtuális gépek térkép nem hozhat létre új Service Map gépcsoportok. 

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Miért jelennek meg saját teljesítménydiagramok pontozott vonal?

Teljesítménydiagramok megjelenítése helyett az alábbi esetekben folytonos vonal pontozott vonal:
* Előfordulhat, hogy az adatgyűjtés kimaradást. 

* Az adat-mintavételezés alapértelmezett érték 60 másodpercenként. Ha úgy dönt, hogy a diagram keskeny időtartományt, és a mintavételi gyakoriság nem éri el a gyűjtő mérete a diagramon használt pontozott vonal jelenhet meg. Tegyük fel, úgy döntött, egy 10 perces mintavételi gyakoriság és a diagramon az egyes gyűjtők érték 5 perc. Ebben az esetben megtekintéséhez szélesebb körű időtartomány kiválasztása eredményezhetik a diagram sorokat pontok helyett a folytonos vonal jelenik meg.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Csoportok támogatottak az Azure Monitor szolgáltatással a virtuális gépek?
Igen, a csoportok alapján az előfizetés, erőforráscsoport, a megjelenítéséhez a virtuális gépekről gyűjtött információkat függőségi ügynök telepítése után virtuálisgép-méretezési csoportokban, és felhőszolgáltatásokat fejleszthet. Ha az eddig használt Service Map és gépcsoportok hozott létre, ezeket a csoportokat is megjelenik. Ha az Ön által létrehozott őket a munkaterületet nézi számítógépcsoportok is megjelennek a csoportok szűrő. 

## <a name="how-can-i-display-the-details-about-whats-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hogyan tudom megjeleníteni a részletek kapcsolatos előfeltételek a 95. percentilis vonal aggregált teljesítménydiagramok?
Alapértelmezés szerint a lista mutatni, a virtuális gépek, amelyek esetén a 95. percentilis a kiválasztott metrika a legmagasabb érték van rendezve. Kivétel ez alól a **rendelkezésre álló memória** diagram, amely a gépek a minden ötödik PERCENTILIS legalacsonyabb értékét jeleníti meg. Válassza ki a diagram megnyitása a **felső N lista** a megfelelő a kiválasztott metrika tartalmazó nézetet.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-various-virtual-networks-and-subnets"></a>Hogyan a térkép szolgáltatás kezeli ismétlődő IP-címek különböző virtuális hálózatok és alhálózatok között?
Ha van-e IP-címtartományok duplikálásához a virtuális gépek vagy az Azure-beli virtuálisgép-méretezési csoportok alhálózatok és virtuális hálózatok között, helytelen adatok megjelenítéséhez a virtuális gépek térkép a szolgáltatás az Azure Monitor okozhat. Sajnáljuk, a probléma, és vizsgálja az beállítások élményének növelése érdekében.

## <a name="does-the-map-feature-support-ipv6"></a>A térkép funkció támogatja az IPv6-alapú?
A térkép funkció jelenleg csak az IPv4 támogatja, és vizsgálja az IPv6-támogatás. IPv4, IPv6 belül bújtatott is támogatja.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-why-is-the-map-difficult-to-view"></a>A térkép egy erőforráscsoportot, vagy más nagy csoport betöltésekor miért a térkép is nehézkes megtekintéséhez?
Továbbfejlesztettük a térkép funkció nagy és összetett konfigurációk kezelésére, bár tisztában vagyunk vele egy térkép rendelkezhet számos csomópontokat, a kapcsolatok és a egy fürtként működő csomópontok. Kötelességünknek tekintjük, folyamatos támogatás bővítése érdekében növelése érdekében.  

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-from-the-network-chart-on-the-azure-vm-overview-page"></a>Miért nem a hálózati diagram a Teljesítmény lapon keresse meg a hálózati diagram az Azure virtuális gépek – Áttekintés oldalon eltérő?

Az Áttekintés oldal egy Azure virtuális gép a gazdagép mérési tevékenység a Vendég virtuális Gépen alapuló diagramot jelenít meg. A hálózati diagram az Azure virtuális gépek – Áttekintés lapon csak a hálózati forgalom, számlázunk jeleníti meg. A megjelenített virtuális hálózatok közötti forgalom nem tartalmazza. Az adat- és diagramok is látható, az Azure Monitor-beli virtuális gépek adatainak a Vendég virtuális Gépen alapul, és a hálózati diagram megjeleníti az összes TCP/IP forgalmat, amely rendelkezik a bejövő és kimenő a virtuális Gépre, beleértve a virtuális hálózatok közötti forgalom.

## <a name="what-are-the-limitations-of-the-log-analytics-free-pricing-plan"></a>Mik a korlátozások, a Log Analytics ingyenes tarifacsomagot?
Ha konfigurálta az Azure Monitor a Log Analytics-munkaterület használatával a *ingyenes* tarifacsomag, a virtuális gépek térkép a szolgáltatás az Azure Monitor a munkaterülethez csatlakozó csak öt gépeket támogatja. 

Például tegyük fel, hogy öt virtuális ingyenes munkaterülethez csatlakozik. Ha egy virtuális gép leválasztása, és később fogja összekapcsolni egy új, az új virtuális gép nem figyelt és a térkép oldalon megjelennek. Ebben a forgatókönyvben az új virtuális gép megnyitásakor kéri, hogy használja a **próbálja ki most** lehetőséget, majd válassza ki **Insights (előzetes verzió)** a bal oldali ablaktáblán, akkor is telepítve van a virtuális gépen. Azonban nem kéri a beállításokkal, általában lenne ha a virtuális gép nem a virtuális gépek az Azure monitornak telepíteni. 

## <a name="next-steps"></a>További lépések
A követelmények és engedélyezésének a virtuális gépek figyelése módszerekkel, tekintse át [-beli virtuális gépek (előzetes verzió) üzembe helyezése az Azure Monitor](vminsights-onboard.md).
