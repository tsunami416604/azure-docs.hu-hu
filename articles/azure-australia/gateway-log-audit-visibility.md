---
title: Átjáró naplózása, naplózása és láthatósága az Azure Ausztráliában
description: Hogyan konfigurálhatja a naplózást, a naplózást és a láthatóságot az ausztráliai régiókban, hogy megfeleljen az ausztráliai kormányzati szabályzatok, rendeletek és jogszabályok követelményeinek.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 153b9d503dfece404455fbb7e8cb51c51686ec57
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824293"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Átjáró naplózása, naplózása és láthatósága az Azure Ausztráliában

A számítógépes biztonsági fenyegetések észlelése és reagálása egy rendszer működésével kapcsolatos adatok generálására, gyűjtésére és elemzésére támaszkodik.

A Microsoft beépített eszközökkel rendelkezik az Azure-ban az Azure-ban üzembe helyezett rendszerek biztonságának kezeléséhez, naplózáshoz és láthatósághoz. Létezik egy olyan hivatkozási architektúra is, amely összhangban van az ausztrál Cyber Security Center (ASCS) fogyasztói útmutatóval és az Information Security Manual (ISM) szándékával.

Az átjárók adatáramlás-vezérlési mechanizmusként működnek a hálózati rétegben, és az Open System Interconnect (OSI) modell magasabb rétegein is szabályozhatják az információkat. Az átjárók a biztonsági tartományok közötti adatforgalom szabályozásához és a külső hálózatokról való jogosulatlan hozzáférés megakadályozásához szükségesek. Mivel az átjárók kritikus fontosságúak a biztonsági tartományok közötti információáramlás szabályozásában, a meghibásodások, különösen a magasabb besorolások esetében, súlyos következményekkel járhatnak. Ilyenek például azok a robusztus mechanizmusok, amelyek riasztást biztosítanak a személyzetnek a számítógépes biztonsági incidenseket okozó helyzetekben, különösen fontosak az átjárók számára.

Az átjárók naplózási és riasztási képességeinek megvalósítása segíthet a számítógépes biztonsági incidensek észlelésében, a behatolások kísérletében és a szokatlan használati mintákban. Emellett az eseménynaplók egy külön biztonságos naplófájlban való tárolása növeli a támadók számára a naplózási információk törlésének nehézségét, hogy megsemmisítse a megcélozott Cyber-behatolás bizonyítékait.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Az ausztrál Cyber Security Center (ASCS) követelményei

A nemzetközösségi rendszerek általános biztonsági követelményeit a ASCS Information Security (ISM) kézikönyve határozza meg. Annak érdekében, hogy támogassa a Commonwealth-entitásokat az Azure-ban, a *ASCS fogyasztói útmutatója – Microsoft Azure a védett és a* *ASCS minősítési jelentésekben – a Microsoft Azure* publikációk részletesen ismertetik a következő kapcsolódó konkrét követelményeket Naplózás, naplózás és láthatóság:

1. A megosztott mögöttes Felhőbeli erőforrások használatából eredő kockázatok enyhítése érdekében a nemzetközösségi entitásoknak a Microsoft Azure biztosított képességek, például az Azure Security Center, a Azure Monitor, a Azure Policy és a Azure Advisor számára, hogy segítséget nyújtsanak az entitások végrehajtásához Az Azure-beli számítási feladatok valós idejű monitorozása

2. A ASCS azt is javasolja, hogy a nemzetközösségi szervezetek az összes kötelező biztonsági naplót a ASCS továbbítsák a teljes ausztráliai kormányzati monitorozáshoz

3. A kockázat enyhítése érdekében a nemzetközösségi entitásoknak az Azure-előfizetéseken belül kell konfigurálniuk:

    * Az Azure Security Center engedélyezése
    * Frissítés a standard szintre
    * A Microsoft monitoring Agent Automatikus kiépítés engedélyezése a támogatott Azure-beli virtuális gépeken
    * A biztonsági javaslatok és riasztások rendszeres felülvizsgálata, rangsorolása és enyhítése a Security Center irányítópultján

4. A kormányzati szerveknek engedélyeznie kell az Azure-előfizetésük naplózását és az események továbbítását a ASCS, így biztosítható, hogy a ASCS a jelen útmutatóval nem összhangban legyen. Az Azure Event Hubs lehetővé teszi külső naplók továbbítását a ASCS vagy a nemzetközösségi szervezet tulajdonában lévő helyi rendszerekhez

5. A nemzetközösségi entitásoknak az Azure-ban engedélyezett naplózást kell összehangolni az ISM-ben meghatározott követelményekkel

6. A Microsoft 90 napon belül megőrzi az Azure-beli naplókat. Az ügyfél entitásoknak naplózási archiválási rendszert kell alkalmazniuk annak érdekében, hogy a naplók megmaradjanak a NAA AFDA szükséges hét évig.

7. Az olyan Nemzetközösségi entitások, amelyek helyszíni vagy Azure-alapú biztonsági információk és eseménykezelő (SIEM) képességek mellett is továbbítják a naplókat a rendszereknek

8. A nemzetközösségi entitásoknak Network Watcher flow-naplókat kell alkalmazniuk a hálózati biztonsági csoportok (NSG) és a Virtual Machines számára. Ezeket a naplókat csak biztonsági naplókat tartalmazó dedikált Storage-fiókban kell tárolni, és a Storage-fiókhoz való hozzáféréshez szerepköralapú hozzáférés-vezérléssel kell gondoskodni

9. A nemzetközösségi entitásoknak ASCS felhasználói útmutatást kell alkalmazniuk ahhoz, hogy az Azure-munkaterhelések megfeleljenek az ISM-nek a naplózás és a figyelés céljára. A nemzetközösségi entitásoknak olyan Azure-képességeket is be kell jelentkezniük, amelyek segítik a ASCS, hogy valós idejű monitorozást, riasztást és naplókat kapjon az Azure-beli kormányzati használattal kapcsolatban

## <a name="architecture"></a>Architektúra

Az Azure-környezetbe beérkező és onnan távozó hálózati forgalom magabiztos megismeréséhez engedélyezni kell a szükséges naplózást az összetevők megfelelő készletén. Ez biztosítja a környezet teljes láthatóságát, és biztosítja a szükséges információkat az elemzés végrehajtásához.

![Azure monitoring-architektúra](media/visibility.png)

## <a name="components"></a>Összetevők

A fent látható architektúra különálló összetevőkből áll, amelyek biztosítják a naplózási források, a naplók gyűjtésének, a naplózás megőrzésének, a napló elemzésének vagy az incidensek válaszának függvényét. Ez az architektúra olyan egyedi összetevőket tartalmaz, amelyek jellemzően az internetről elérhető Azure-környezetekben vesznek részt.

|Funkciók|Összetevők|
|---|---|
|Naplózási források|<ul><li>Application Gateway</li><li>VPN Gateway</li><li>Azure Firewall</li><li>Hálózati virtuális berendezések</li><li>Azure Load Balancer</li><li>Virtuális gépek</li><li>Tartománynév-kiosztási rendszer (DNS) kiszolgálói</li><li>Syslog és/vagy log Collection kiszolgálók</li><li>NSG</li><li>Azure-tevékenységnapló</li><li>Azure diagnosztikai napló</li><li>Azure Policy</li></ul>|
|Naplógyűjtemény|<ul><li>Event Hubs</li><li>Network Watcher</li><li>Log Analytics</li></ul>|
|Napló megőrzése|<ul><li>Azure Storage</li></ul>|
|Naplóelemzés|<ul><li>Azure Security Center (ASC)</li><li>Azure Advisor</li><li>Log Analytics megoldások<ul><li>Forgalomelemzés</li><li>DNS Analytics (előzetes verzió)</li><li>Activity Log Analytics</li></ul></li><li>SIEM</li><li>ASCS</li></ul>|
|Incidensmegoldás|<ul><li>Azure-riasztások</li><li>Azure Automation</li></ul>|
|

Az architektúra úgy működik, hogy először generál naplókat a szükséges forrásokból, majd begyűjti őket a központosított adattárakba. Miután összegyűjtötte a naplókat, a következőket teheti:

* Az Azure Analysis Services által használt információk betekintést nyernek
* továbbítva a külső rendszerekre, vagy
* archiválja a tárterületet a hosszú távú megőrzés érdekében.

Az Analysis Tools által azonosított legfontosabb eseményekre vagy incidensekre való reagáláshoz a riasztások konfigurálhatók, az Automation pedig a proaktív felügyelethez és reagáláshoz szükséges műveletek elvégzéséhez lett kifejlesztve.

## <a name="general-guidance"></a>Általános útmutatás

A cikkben felsorolt összetevők megvalósításakor a következő általános útmutatás vonatkozik:

* A szolgáltatások rendelkezésre állásának ellenőrzése, annak biztosítása, hogy az összes adat a megengedett helyen belül maradjon, és üzembe helyezése az AU Central vagy az AU Central 2 alkalmazásban, a védett munkaterhelések első előnyben részesítése

* Tekintse meg az *Azure-ASCs minősítési jelentést – védett 2018-* es kiadvány az egyes szolgáltatások minősítési állapotáról, és végezze el az önértékelést a jelentésben nem szereplő bármely kapcsolódó összetevőn a *ASCs fogyasztói útmutatója szerint – VÉDETT Microsoft Azure*

* A jelen cikkben nem hivatkozott összetevők esetén a nemzetközösségi entitásoknak követniük kell a naplók generálására, rögzítésére, elemzésére és megőrzésére vonatkozó alapelveket.

* Azonosítsa és rangsorolja a naplózást, a naplózást és a láthatóságot a nagy értékű rendszereken, valamint az Azure-ban üzemeltetett rendszerekre vonatkozó összes hálózati bejövő és kimenő pontot

* Naplózza a naplókat, és minimalizálja a naplózási eszközök, például a Storage-fiókok, Log Analytics munkaterületek és Event Hubs példányainak számát.

* Rendszergazdai jogosultságok korlátozása szerepköralapú hozzáférés-vezérléssel

* Többtényezős hitelesítés (MFA) használata erőforrások Azure-beli felügyeletéhez vagy konfigurálásához

* A naplók több előfizetésben való központosított összevonásakor győződjön meg arról, hogy a rendszergazdák rendelkeznek a szükséges jogosultságokkal az egyes előfizetésekben

* Győződjön meg arról, hogy a hálózati kapcsolat és a szükséges proxybeállítások a Virtual Machineshoz, beleértve a hálózati virtuális berendezéseket (NVA), a naplózási kiszolgálókat és a DNS-kiszolgálókat, a szükséges Azure-szolgáltatásokhoz, például a Log Analytics munkaEvent Hubs területekhez való csatlakozáshoz és Storage

* A Microsoft monitoring Agent (MMA) konfigurálása a TLS 1,2-es verziójának használatára

* A követelmények megfelelőségének figyelése és érvényesítése Azure Policy használatával

* Titkosítás betartatása minden adattárházon, például a tárolón és az adatbázisokon

* Helyileg redundáns tárolás (LRS) és Pillanatképek használata a Storage-fiókok és a kapcsolódó adatmennyiségek rendelkezésre állásához

* Vegye fontolóra a Geo-redundáns tárolást (GRS) vagy a helyszíni tárterületet a vész-helyreállítási stratégiákkal való összehangoláshoz

|Resource|URL|
|---|---|
|Ausztrál szabályozási és szabályzatok megfelelőségi dokumentumai|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure-termékek – ausztráliai régiók és nem regionális|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Ausztrália – Közép-Ausztrália – Közép-2, Ausztrália – Kelet, Ausztrália – Délkelet](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Microsoft Azure Security and Audit log Management-tanulmány|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Microsoft monitoring Agent – konfiguráció|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)|
|

## <a name="component-guidance"></a>Összetevő-útmutatás

Ez a szakasz az egyes összetevők, valamint az általános naplózási, naplózási és láthatósági architektúrában betöltött szerepéről tartalmaz információkat. További hivatkozások érhetők el a hasznos erőforrások, például a dokumentáció, útmutatók és oktatóanyagok eléréséhez.

## <a name="log-sources"></a>Naplózási források

Az elemzés, a riasztások és a jelentéskészítés előtt elő kell állítani a szükséges naplókat. Az Azure-naplók a vezérlési/felügyeleti naplókba, az adatsíkok naplóiba és a feldolgozott eseményekre vannak kategorizálva.

|Type|Leírás|
|---|---|
|Vezérlési/felügyeleti naplók|A Azure Resource Manager műveletekkel kapcsolatos információk megadása|
|Adatsíkok naplói|Adja meg az Azure-erőforrások használatának részeként kiváltott eseményekkel kapcsolatos információkat, például a virtuális gépek naplófájljait és a Azure Monitor által elérhető diagnosztikai naplókat.|
|Feldolgozott események|Adjon meg információkat az Azure által feldolgozott elemzett eseményekről/riasztásokról, például ha a Azure Security Center a biztonsági riasztások biztosításához feldolgozta és elemezte az előfizetéseket|
|

### <a name="application-gateway"></a>Application Gateway

Az Azure Application Gateway az egyik lehetséges belépési pont egy Azure-környezetbe, így a webalkalmazásokkal kommunikáló bejövő kapcsolatokkal kapcsolatos információkat kell rögzítenie. Application Gateway a webalkalmazás-használattal kapcsolatos fontos információkat biztosíthat, valamint segítséget nyújt a számítógépes biztonsági incidensek észlelésében is. A Application Gateway metaadatokat küld a tevékenység naplójának és a diagnosztikai naplóknak Azure Monitor, ahol a Log Analytics használható, vagy egy Event hub-vagy Storage-fiókba terjeszthető.

|További források|Összekapcsolás|
|---|---|
|Application Gateway – dokumentáció|[https://docs.microsoft.com/azure/application-gateway/](https://docs.microsoft.com/azure/application-gateway/)|
|Application Gateway rövid útmutató|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)|
|

### <a name="vpn-gateway"></a>VPN Gateway

Az VPN Gateway egy lehetséges belépési pont az Azure-környezetbe irányuló kommunikáció széles körére, például a helyszíni környezettel és a felügyeleti forgalommal létesített kapcsolattal. A VPN-átjárók naplózása betekintést és nyomonkövethetőséget biztosít az Azure-környezethez kapcsolódó kapcsolatok számára. A naplózás lehetővé teszi a naplózást és az elemzést, valamint segítséget nyújt a kártékony vagy rendellenes kapcsolatok észleléséhez vagy kivizsgálásához. VPN Gateway naplókat a rendszer elküldje a Azure Monitor tevékenységi naplóba, ahol a Log Analytics vagy egy Event hub-vagy Storage-fiókba terjeszthetők.

|További források|Összekapcsolás|
|---|---|
|A VPN Gateway dokumentációja|[https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway)|
|Ausztráliai kormányzati VPN Gateway útmutató|[Azure VPN Gateway konfiguráció](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Azure Firewall

A Azure Firewall egy ellenőrzött kilépési pontot biztosít egy Azure-környezetből és a generált naplókból, amelyek a megkísérelt és sikeres kimenő kapcsolatokra vonatkozó információkat tartalmaznak, a naplózási stratégia fontos eleme. Ezek a naplók ellenőrizhetik, hogy a rendszerek a tervezett módon működnek-e, valamint segítséget nyújtanak a nem engedélyezett külső rendszerekhez való kapcsolódást megkísérlő kártékony kódok vagy szereplők észlelésében. Azure Firewall a naplókat a tevékenység naplóba és a diagnosztikai naplókba írja a Azure Monitorban, ahol a Log Analytics használható, vagy egy Event hub-vagy Storage-fiókba terjeszthető.

|További források|Összekapcsolás|
|---|---|
|Azure Firewall-dokumentáció|[https://docs.microsoft.com/azure/firewall/](https://docs.microsoft.com/azure/firewall)|
|Oktatóanyag: Azure Firewall naplók és metrikák figyelése|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)|
|

### <a name="network-virtual-appliances-nva"></a>Hálózati virtuális berendezések (NVA)

A NVA az Azure-ban natív módon elérhető biztonsági funkciók kiegészítésére használható. A NVA létrehozott naplók értékes erőforrások lehetnek a számítógépes biztonsági incidensek észlelése során, és az általános naplózási, auditálási és láthatósági stratégia kulcsfontosságú részét képezik. A naplók NVA való rögzítéséhez használja a Microsoft monitoring Agent (MMA) alkalmazást. Azon NVA esetében, amelyek nem támogatják az MMA telepítését, érdemes lehet syslog vagy más log Collection kiszolgálót használni a naplók továbbítására.

|További források|Összekapcsolás|
|---|---|
|Hálózati virtuális berendezések áttekintése|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|A NVA dokumentációja|Tekintse meg a gyártói dokumentációt a megfelelő NVA az Azure-ban való megvalósításáról|
|

### <a name="azure-load-balancer"></a>Azure Load Balancer

Azure Load Balancer naplók az Azure-ban üzembe helyezett rendszerekkel kapcsolatos kapcsolatok és használat hasznos információinak beszerzéséhez használatosak. Ez használható az állapot és a rendelkezésre állás figyelésére, de egy másik kulcsfontosságú összetevőt is biztosít a kommunikációs forgalomhoz szükséges betekintéshez, valamint a kártékony vagy rendellenes adatforgalmi minták észleléséhez. Azure Load Balancer naplókat a tevékenység naplójában és a diagnosztikai naplókban a Azure Monitorban, ahol Log Analytics lehet használni, vagy egy Event hub-vagy Storage-fiókba terjeszthető.

|További források|Összekapcsolás|
|---|---|
|Azure Load Balancer dokumentáció|[https://docs.microsoft.com/azure/load-balancer](https://docs.microsoft.com/azure/load-balancer)|
|Metrikák és Health Diagnostics standard Load Balancer|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)|
|

### <a name="virtual-machines"></a>Virtuális gépek

Virtual Machines olyan végpontok, amelyek hálózati kommunikációt küldenek és fogadnak, adatokat dolgoznak fel és szolgáltatásokat biztosítanak. Mivel a Virtual Machines képes az adattárolásra vagy a létfontosságú rendszerszolgáltatásokra, a megfelelő működés biztosítása és a számítógépes biztonsági incidensek észlelése kritikus fontosságú lehet. Virtual Machines gyűjthet különböző esemény-és naplózási naplókat, amelyek nyomon követhetik a rendszer működését és az adott rendszeren végrehajtott műveleteket. A Virtual Machines gyűjtött naplók a Microsoft monitoring Agent használatával továbbíthatók Log Analytics munkaterületre, ahol Azure Security Center és alkalmazható Log Analytics-megoldásokkal elemezhetők. A Virtual Machines közvetlenül vagy egy log Collection Serveren keresztül is integrálható közvetlenül az Azure Event Hubs vagy a SIEM használatával.

|További források|Összekapcsolás|
|---|---|
|Virtuális gépek|[https://docs.microsoft.com/azure/virtual-machines](https://docs.microsoft.com/azure/virtual-machines)|
|Adatgyűjtés Virtual Machines|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|
|A virtuális gép naplófájljainak továbbítása a Event Hubsba|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs)|
|

### <a name="domain-name-services-dns-servers"></a>Domain Name Services-(DNS-) kiszolgálók

A DNS-kiszolgáló naplófájljai a rendszerek belső vagy külső elérésére irányuló szolgáltatásokhoz kapcsolódó kulcsfontosságú információkat biztosítanak. A DNS-naplók rögzítése segíthet azonosítani a Cyber biztonsági incidenseket, és betekintést nyújt az incidens típusára és az esetlegesen érintett rendszerekre. A Microsoft Management agent (MMA) a DNS-kiszolgálókon használható a naplók továbbítására a Log Analytics DNS Analytics (előzetes verzió) való használatra.

|További források|Összekapcsolás|
|---|---|
|Virtuális hálózatok Azure-beli névfeloldása|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)|
|

### <a name="syslog-and-log-collection-servers"></a>Syslog és log Collection kiszolgálók

A hálózati virtuális készülékekről érkező naplók fogadásához, vagy a más rendszerekből származó egyéni biztonsági naplókhoz a SIEM-en belüli használatra szolgáló dedikált kiszolgálók üzembe helyezhetők az Azure virtuális hálózatok-ben. A syslog-naplók összegyűjthetők a syslog-kiszolgálókon, és az elemzéshez Log Analytics továbbítása is lehetséges. A naplózási kiszolgáló a központosított figyelési rendszerek vagy SIEM-hálózatok által használt naplózási összesítési és terjesztési képességek általános kifejezése. Ezek használatával egyszerűsítheti a hálózati architektúra és a biztonság, valamint a naplók szűrését és összesítését, mielőtt a központosított képességbe kerül.

|További források|Összekapcsolás|
|---|---|
|A Log Analytics Syslog-adatforrások|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)|
|Napló-gyűjtemény kiszolgálója|A figyeléssel és az SIEM architektúrával kapcsolatos részletekért tekintse meg a gyártói dokumentációt.|
|

### <a name="network-security-groups-nsgs"></a>Hálózati biztonsági csoportok (NSG)

A NSG az Azure-beli virtuális hálózatok felé és kívülre irányítja a forgalmat. A NSG szabályokat alkalmazhat az engedélyezett vagy megtagadott adatforgalomra, amely magában foglalja az Azure-on belüli, valamint az Azure és a külső hálózatok, például a helyszíni vagy az Internet közötti adatforgalmat. A NSG a virtuális hálózaton belüli alhálózatokra vagy az egyes hálózati adapterekre vonatkoznak. Az Azure-ban a rendszerbe beérkező és onnan távozó forgalom adatainak rögzítéséhez a NSG-naplók a Network Watcher NSG flow-naplók funkcióval engedélyezhetők. Ezek a naplók alapkonfigurációt alkotnak a rendszer szabványos működéséhez, és a Traffic Analytics adatforrása, amely részletesen ismerteti az Azure-ban üzemeltetett rendszerek forgalmi mintáit.

|További források|Összekapcsolás|
|---|---|
|A hálózati biztonsági csoport dokumentációja|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|A hálózati biztonsági csoportok flow-naplózásának bemutatása|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|
|Oktatóanyag: A virtuális gép felé irányuló és onnan érkező hálózati forgalom naplózása a Azure Portal használatával|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)|
|

### <a name="azure-activity-log"></a>Azure-tevékenységnapló

A Azure Monitor részét képező Azure-tevékenységi napló egy előfizetési napló, amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. A műveletnapló segíthet meghatározni a "mit, ki és mikor" az előfizetésben lévő erőforrásokra vonatkozó írási műveletekhez (PUT, POST , DELETE). A tevékenység naplója létfontosságú az Azure-környezetben végrehajtott konfigurációs változások nyomon követéséhez. Az Azure-tevékenység naplói Log Analytics megoldásokban automatikusan elérhetők, és az Event Hubs vagy az Azure Storage-ba is küldhetők feldolgozásra vagy megőrzésre.

|További források|Összekapcsolás|
|---|---|
|Az Azure Activity log dokumentációja|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|Az Azure-tevékenység naplójának továbbítása Event Hubs|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)|
|

### <a name="azure-diagnostic-log"></a>Azure diagnosztikai napló

Azure Monitor a diagnosztikai naplók olyan Azure-szolgáltatás által kibocsátott naplók, amelyek gazdag és gyakori adatmennyiséget biztosítanak a szolgáltatás működéséről. A diagnosztikai naplók részletesen betekintést nyújtanak egy erőforrás működéséhez, és számos olyan követelményt felhasználhatnak, mint például a naplózás vagy a hibaelhárítás. Az Azure diagnosztikai naplókat Log Analytics megoldásokban automatikusan elérhetők, és az Event Hubs vagy az Azure Storage-ba is küldhetők feldolgozásra vagy megőrzésre.

|További források|Összekapcsolás|
|---|---|
|Az Azure diagnosztikai napló dokumentációja|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|Diagnosztikai naplók támogatási szolgáltatásai|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)|
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy érvényesíti az erőforrások telepítésének szabályait, például a típust, a helyet és a konfigurációt. A Azure Policy konfigurálható úgy, hogy az erőforrások csak akkor legyenek telepíthetők, ha megfelelnek a követelményeknek. Azure Policy egy Azure-környezet integritásának fenntartására szolgáló alapvető összetevő. A Azure Policy kapcsolatos események naplózása az Azure-tevékenység naplójába történik, és automatikusan elérhetők Log Analytics megoldásokban való használatra, illetve az Event Hubs vagy az Azure Storage-ba történő feldolgozásra vagy megőrzésre.

|További források|Összekapcsolás|
|---|---|
|Az Azure Policy dokumentációja|[https://docs.microsoft.com/azure/governance/policy](https://docs.microsoft.com/azure/governance/policy)|
|Azure Policy-és Resource Manager-sablonok kihasználása az Azure-tervezetek használatával|[https://docs.microsoft.com/azure/governance/blueprints/overview](https://docs.microsoft.com/azure/governance/blueprints/overview)|
|

## <a name="log-collection"></a>Naplók gyűjtése

A Többnaplós forrásokból történő létrehozás után a naplókat központosított helyen kell tárolni a folyamatos hozzáférés és elemzés érdekében. Az Azure több módszert és lehetőséget biztosít a naplózási gyűjteményhez, amely a napló típusától és követelményeitől függően használható.

### <a name="event-hubs"></a>Event Hubs

Az Event hub célja, hogy összesítse a különböző forrásokhoz tartozó naplózási adatokat a terjesztéshez. Az Event hub szolgáltatásban a naplózási adatok a ASCS a megfelelőség és a tárolás a hosszú távú megőrzés érdekében továbbíthatók a SIEM-be.

|További források|Összekapcsolás|
|---|---|
|Event Hubs – dokumentáció|[https://docs.microsoft.com/azure/event-hubs](https://docs.microsoft.com/azure/event-hubs)|
|Útmutatás a Event Hubs és a külső eszközökhöz|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)|
|

### <a name="log-analytics"></a>Log Analytics

A Log Analytics Azure Monitor része, és a naplózási elemzéshez használatos. Log Analytics a munkaterületet használja tárolási mechanizmusként, ahol a naplózási adatai elérhetővé tehetők az Azure-ban elérhető különböző elemzési eszközök és megoldások számára. A Log Analytics az Azure-összetevők széles körét integrálja közvetlenül, és a Microsoft monitoring Agenten keresztül Virtual Machines.

|További források|Összekapcsolás|
|---|---|
|Log Analytics – dokumentáció|[https://docs.microsoft.com/azure/azure-monitor](https://docs.microsoft.com/azure/azure-monitor)|
|Oktatóanyag: Log Analyticsban lévő adatelemzés|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)|
|

### <a name="network-watcher"></a>Network Watcher

A ASCS a Network Watcher használatát javasolja az Azure-előfizetésekben lévő hálózati forgalom megismeréséhez és rögzítéséhez. A NSG flow-naplók biztosítják a Traffic Analytics megoldás bemenetét Log Analyticsban, amely az Azure-ban natív módon megnöveli a láthatóságot, az elemzést és a jelentéskészítést. A Network Watcher közvetlenül a Azure Portalból is biztosít a csomagok rögzítésének lehetőségét anélkül, hogy be kellene jelentkeznie a virtuális gépre. A csomagok rögzítése lehetővé teszi a csomagok rögzítési munkameneteinek létrehozását a virtuális gépekre irányuló és onnan érkező forgalom nyomon követésére.

|További források|Összekapcsolás|
|---|---|
|Network Watcher|[https://docs.microsoft.com/azure/network-watcher](https://docs.microsoft.com/azure/network-watcher)|
|A csomagok rögzítésének áttekintése|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)|
|

## <a name="log-retention"></a>Napló megőrzése

Az ausztrál kormányzati szervezetek számára az Azure-on belül rögzített naplókat az ausztráliai [adminisztratív functions Authority (AFDA)](http://www.naa.gov.au/information-management/records-authorities/types-of-records-authorities/AFDA/index.aspx)nemzeti levéltárának megfelelően meg kell őrizni, amely meghatározza, hogy a naplók akár hét évig is megmaradjanak.

|Napló helye|Megőrzési időszak|
|---|---|
|Azure-tevékenységnapló|Akár 90 nap|
|Log Analytics-munkaterület|Legfeljebb két év|
|Eseményközpont|Legfeljebb hét nap|
|

Az Ön felelőssége annak biztosítása, hogy a naplók archiválása megfelelő legyen a AFDA és egyéb jogalkotási követelmények betartásához.

### <a name="azure-storage"></a>Azure Storage

Az Azure Storage az Azure-beli hosszú távú adatmegőrzési naplók tárháza. Az Azure Storage használható az Azure-naplók archiválására, beleértve a Event Hubs, az Azure Activity log és az Azure diagnosztikai naplókat. A tárterületen tárolt adatok megőrzési ideje nulla értékre állítható, vagy megadható napok száma. A nulla nap megőrzése azt jelenti, hogy a naplók örökre megmaradnak, ellenkező esetben az érték tetszőleges számú nap lehet 1 és 2147483647 között.

|További források|Összekapcsolás|
|---|---|
|Azure Storage-dokumentáció|[https://docs.microsoft.com/azure/storage](https://docs.microsoft.com/azure/storage)|
|Az Azure Event Hubs az Azure Blob Storage vagy az Azure Data Lake Storage keresztül események rögzítése|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Oktatóanyag: Azure-metrikák és naplóadatok archiválása az Azure Storage használatával|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data)|
|Azure Storage-replikáció|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy)|
|BLOB pillanatképének létrehozása|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>Naplóelemzés

Miután létrehozta és elvégezte a központi helyen történő tárolását, a naplókat elemezni kell, hogy segítséget nyújtson a megkísérelt vagy sikeres biztonsági incidensek észlelésében. A biztonsági incidensek észlelésekor az Ügynökségnek képesnek kell lennie az ilyen incidensekre való reagálásra, valamint a fenyegetések nyomon követésére, tárolására és szervizelésére.

### <a name="azure-security-center-asc"></a>Azure Security Center (ASC)

Azure Security Center biztosítja az egységes biztonsági felügyeletet és az összetett veszélyforrások elleni védelmet. Azure Security Center alkalmazhat biztonsági házirendeket a munkaterhelések között, korlátozhatja a fenyegetéseket, és észlelheti és reagálhat a támadásokra. Azure Security Center az Azure-összetevők széles skáláján irányítópultokat és elemzéseket biztosít. A Azure Security Center használata követelményként van megadva a ASCS-fogyasztói útmutatóban.

|További források|Összekapcsolás|
|---|---|
|Az Azure Security Center dokumentációja|[https://docs.microsoft.com/azure/security-center](https://docs.microsoft.com/azure/security-center)|
|Gyors útmutató: Azure-előfizetés előkészítése a Security Center Standard csomagra|[https://docs.microsoft.com/azure/security-center/security-center-get-started](https://docs.microsoft.com/azure/security-center/security-center-get-started)|
|

### <a name="traffic-analytics"></a>Forgalomelemzés

A Traffic Analytics egy felhőalapú megoldás, amely láthatóságot biztosít az Azure-beli felhasználói és alkalmazási tevékenységekben. A Traffic Analytics elemzi Network Watcher NSG, hogy betekintést nyújtson az Azure-ba irányuló forgalomba. A Traffic Analytics a virtuális hálózatok közötti hálózati forgalomhoz kapcsolódó irányítópultok, jelentések, elemzések és az események megválaszolására szolgáló képességek biztosítására szolgál. Traffic Analytics jelentős átláthatóságot és segítséget nyújt a számítógépes biztonsági incidensek azonosításában és megoldásában.

|További források|Összekapcsolás|
|---|---|
|Traffic Analytics dokumentáció|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)|
|

### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor elemzi az erőforrás-konfigurációt és az egyéb adatforrásokat, hogy javaslatokat nyújtson a megoldások teljesítményének, biztonságának és magas rendelkezésre állásának javítására, miközben lehetőség van az Azure-kiadások általános költségeinek csökkentésére. Azure Advisor a ASCS ajánlja, és könnyen elérhető és részletes útmutatást nyújt az Azure-környezet konfigurálásához.

|További források|Összekapcsolás|
|---|---|
|Azure Advisor-dokumentáció|[https://docs.microsoft.com/azure/advisor](https://docs.microsoft.com/azure/advisor)|
|Ismerkedés az Azure Advisor szolgáltatással|[https://docs.microsoft.com/azure/advisor/advisor-get-started](https://docs.microsoft.com/azure/advisor/advisor-get-started)|
|

### <a name="dns-analytics-preview"></a>DNS Analytics (előzetes verzió)

A DNS Analytics egy Log Analytics-megoldás, amely összegyűjti, elemzi és korrelálja a Windows DNS elemzési és naplózási naplóit, valamint az egyéb kapcsolódó adatokat. DNS Analytics azonosítja azokat az ügyfeleket, akik kártékony tartományneveket, elavult erőforrásrekordokat, gyakran lekérdezett tartományneveket és beszédes DNS-ügyfeleket próbálnak megoldani. A DNS Analytics a DNS-kiszolgálókon és a dinamikus DNS-regisztráció meghibásodásakor is betekintést nyújt a kérések terhelésére. A DNS Analytics az Azure-környezetekben végrehajtott DNS-lekérdezésekhez kapcsolódó irányítópultok, jelentések, elemzések és az események megválaszolására szolgáló képességek biztosítására szolgál. DNS Analytics jelentős átláthatóságot és segítséget nyújt a számítógépes biztonsági incidensek azonosításában és megoldásában.

|További források|Összekapcsolás|
|---|---|
|DNS Analytics-dokumentáció|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)|
|

### <a name="activity-log-analytics"></a>Activity Log Analytics

A Activity Log Analytics egy Log Analytics megoldás, amellyel több Azure-előfizetésen keresztül elemezheti és keresheti az Azure-tevékenységek naplóját. Activity Log Analytics a teljes Azure-környezet erőforrásain végrehajtott műveletekhez kapcsolódó központosított irányítópultok, jelentések, elemzések és események megválaszolására szolgáló képességek biztosítására szolgál. Activity Log Analytics segítséget nyújthat a naplózás és a vizsgálat során.

|További források|Összekapcsolás|
|---|---|
|Összegyűjtheti és elemezheti a Log Analytics az Azure-Tevékenységnaplók|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)|
|

### <a name="security-information-and-event-management-siem"></a>Biztonsági információk és események kezelése (SIEM)

A SIEM egy olyan rendszer, amely központosított tárolást, naplózást és elemzést biztosít a biztonsági naplókhoz, és meghatározta az elemzési, jelentéskészítési és incidens-észlelési és reagálási naplózási és intelligens eszközök széles körének betöltésére szolgáló meghatározott mechanizmusokat. Az Azure-naplózási adatokat tartalmazó SIEM-képességekkel kiegészítheti az Azure-ban natív módon biztosított biztonsági képességeket. A nemzetközösségi entitások a konkrét követelményektől függően az Azure-ban, a helyszínen vagy a szolgáltatott szoftverként (SaaS) való Virtual Machines üzemeltetett SIEM-t is használhatnak.

|További források|Összekapcsolás|
|---|---|
|Azure Sentinel (előzetes verzió)|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|SIEM-dokumentáció|Tekintse meg a szolgáltatói dokumentációt SIEM architektúrához és útmutatáshoz|
|A Azure Monitor használata az SIEM-eszközökkel való integráláshoz|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>Ausztrál Cyber Security Center

Az Australian Cyber Security Center (ASCS) az ausztrál kormány vezető szerepet a nemzeti számítógépes biztonság terén. Az ausztrál kormány különböző számítógépes biztonsági képességeit is magában foglalhatja, hogy javítsa az ausztrál Közösség Cyber-rugalmasságát, és támogassa az ausztráliai gazdasági és társadalmi fellendülést a digitális korban. A ASCS azt ajánlja, hogy a Commonwealth-entitások az összes, a rendszer által létrehozott naplófájlt, eseményt és naplót továbbítsák a ASCS a teljes ausztráliai kormányzati monitorozáshoz.

|További források|Összekapcsolás|
|---|---|
|Ausztrál Cyber Security Center webhelye|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>Incidensmegoldás

A megfelelő naplók generálása, a központosított adattárakba való begyűjtése és az elemzés végrehajtása növeli a rendszerek megértését, és mechanizmusokat biztosít a számítógépes biztonsági incidensek észlelésére. Az incidensek vagy események észlelése után a következő lépés az, hogy reagáljon ezekre az eseményekre, és műveleteket hajtson végre a rendszer állapotának fenntartása, valamint a szolgáltatások és az adatok biztonságának védelme érdekében. Az Azure a szolgáltatások egy kombinációját biztosítja, hogy az esetlegesen előforduló eseményekhez hatékonyan reagáljon.

### <a name="azure-alerts"></a>Azure-riasztások

Az Azure-riasztások segítségével értesítheti a támogatási és biztonsági személyzetet az adott eseményekre adott válaszként. Ez lehetővé teszi, hogy a nemzetközösségi entitás proaktív módon válaszoljon a jelen cikkben felsorolt Analysis Services által kiváltott releváns események észlelésére.

|További források|Összekapcsolás|
|---|---|
|A Microsoft Azure riasztások áttekintése|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)|
|Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)|
|Eseményekre való válaszadás Azure Monitor-riasztásokkal|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)|
|

### <a name="azure-automation"></a>Azure Automation

Azure Automation lehetővé teszi a nemzetközösségi entitások számára, hogy az eseményekre adott válaszként aktiválják a műveleteket. Lehet, hogy a csomagok rögzítését elindíthatja Virtual Machineson, futtathat egy munkafolyamatot, leállíthatja vagy elindíthatja Virtual Machines vagy szolgáltatásait, illetve számos más feladatot. Az Automation gyors reagálást tesz lehetővé a riasztások manuális beavatkozása nélkül, így csökkentve az incidens vagy esemény válaszideje és súlyosságát.

|További források|Összekapcsolás|
|---|---|
|Azure Automation dokumentáció|[https://docs.microsoft.com/azure/automation](https://docs.microsoft.com/azure/automation)|
|Útmutató: Azure Automation runbook elindítására szolgáló riasztás használata|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)|
|

## <a name="next-steps"></a>További lépések

Tekintse át az [átjáró biztonságos távoli felügyeletével](gateway-secure-remote-administration.md) foglalkozó cikket, amely részletesen ismerteti az Azure-beli átjáró-környezetek biztonságos kezelését.
