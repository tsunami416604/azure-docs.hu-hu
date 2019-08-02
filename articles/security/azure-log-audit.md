---
title: Azure-naplózás és-naplózás | Microsoft Docs
description: Ismerje meg, hogy miként használhatók a naplózási információk az alkalmazással kapcsolatos mélyreható elemzésekhez.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: 6634b674037b48ff9ef9690144ef5274f31a6ea8
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227963"
---
# <a name="azure-logging-and-auditing"></a>Azure-naplózás

Az Azure a konfigurálható biztonsági naplózási és naplózási lehetőségek széles skáláját biztosítja, amelyek segítségével azonosíthatja a biztonsági szabályzatokban és mechanizmusokban mutatkozó réseket. Ez a cikk az Azure-ban üzemeltetett szolgáltatások biztonsági naplóinak generálását, gyűjtését és elemzését ismerteti.

> [!Note]
> A cikkben szereplő bizonyos javaslatok megnövelheti az adatok, a hálózat vagy a számítási erőforrások használatát, és növelheti a licenc-vagy előfizetési költségeket.

## <a name="types-of-logs-in-azure"></a>Az Azure-beli naplók típusai

A Felhőbeli alkalmazások összetett, számos mozgó részből állnak. A naplók biztosítják az alkalmazások működésének megtartását. A naplók segítenek a múltbeli problémák megoldásában, illetve a lehetséges lehetőségek megelőzésében. Emellett segíthetnek az alkalmazások teljesítményének vagy karbantartásának javításában, vagy automatizálni azokat a műveleteket, amelyek egyébként manuális beavatkozást igényelnek.

Az Azure-naplók a következő típusokra vannak kategorizálva:
* A **vezérlési/felügyeleti naplók** a Azure Resource Manager létrehozási, frissítési és törlési műveleteivel kapcsolatos információkat biztosítanak. További információ: Azure- [tevékenységek naplói](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Az adatsíkok naplói** az Azure-erőforrások használatának részeként kiváltott eseményekről nyújtanak információt. Ilyen típusú napló például a Windows-eseményrendszer, a biztonság és az alkalmazás naplófájljai egy virtuális gépen (VM) és a Azure Monitoron keresztül konfigurált [diagnosztikai naplókon](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) .

* A **feldolgozott események** információt nyújtanak az Ön nevében feldolgozott elemzett eseményekről/riasztásokról. Ilyen típusú példák olyan [Azure Security Center riasztások](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) , ahol a [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) feldolgozta és elemezte az előfizetését, és rövid biztonsági riasztásokat biztosít.

A következő táblázat az Azure-ban elérhető naplók legfontosabb típusait sorolja fel:

| Naplókategória | Napló típusa | Használat | Integráció |
| ------------ | -------- | ------ | ----------- |
|[Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Az Azure Resource Manager-erőforrásokon futó események vezérlése|   Betekintést nyújt az előfizetése erőforrásaiban végrehajtott műveletekre.|    REST API, [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure-beli diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Az előfizetésben Azure Resource Manager erőforrások működésével kapcsolatos gyakori információk|    Betekintést nyújt az erőforrás által végrehajtott műveletekre.| Azure Monitor, [stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Azure AD Reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Naplók és jelentések | A felhasználók és csoportok kezelésével kapcsolatos felhasználói bejelentkezési tevékenységek és rendszertevékenység-információk jelentése.|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtual Machines és Cloud Services](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|A Windows Eseménynapló szolgáltatás és a Linux syslog|  Rögzíti a rendszerinformációkat és a naplózási adatait a virtuális gépeken, és átviszi azokat egy tetszőleges Storage-fiókba.|   Windows (Windows Azure Diagnostics [[wad](https://docs.microsoft.com/azure/azure-diagnostics)] Storage) és Linux használata a Azure monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|A Storage-naplózási szolgáltatás metrikákat biztosít a Storage-fiókhoz|Betekintést nyújt a nyomkövetési kérelmekbe, elemzi a használati trendeket, és diagnosztizálja a Storage-fiókkal kapcsolatos problémákat.|   REST API vagy az [ügyféloldali kódtár](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Hálózati biztonsági csoport (NSG) folyamatábrái](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON-formátum, a kimenő és a bejövő folyamatok megjelenítése a szabályok alapján|Egy hálózati biztonsági csoporton keresztül megjeleníti a bejövő és kimenő IP-forgalomra vonatkozó információkat.|[Azure-Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Alkalmazás-betekintés](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Naplók, kivételek és egyéni diagnosztika|   Egy Application Performance monitoring (APM) szolgáltatást biztosít több platformon futó webes fejlesztőknek.| REST API, [Power bi](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Adatfeldolgozási/biztonsági riasztások|    Riasztások Azure Security Center Azure Monitor naplók riasztásai|    Biztonsági információkat és riasztásokat biztosít.|  REST API-k, JSON|

### <a name="activity-logs"></a>Tevékenységnaplók

Az [Azure-tevékenység naplói](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) betekintést nyújtanak az előfizetése erőforrásaiban végrehajtott műveletekre. A tevékenységi naplók korábban "naplók" vagy "operatív naplók" néven ismertek, mert az előfizetések esetében jelentést jelentenek a [vezérlési sík eseményeiről](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) . 

A Tevékenységnaplók segítségével meghatározható az írási műveletek (azaz a PUT, a POST vagy a DELETE) "mi, ki és mikor". A Tevékenységnaplók segítenek megérteni a művelet állapotát és az egyéb kapcsolódó tulajdonságokat is. A tevékenységi naplók nem tartalmaznak olvasási (GET) műveleteket.

Ebben a cikkben a PUT, POST és DELETE kifejezés az összes olyan írási műveletre vonatkozik, amelyet a tevékenység naplója az erőforrásokon tartalmaz. Például a tevékenységek naplói segítségével hibát kereshet a hibák elhárítása során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

![Tevékenységi napló diagramja](./media/azure-log-audit/azure-log-audit-fig1.png)

Az eseményeket a Azure Portal, az [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), a PowerShell-parancsmagok és a [Azure monitor REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough)használatával kérheti le. A Tevékenységnaplók 90 napos adatmegőrzési időtartammal rendelkeznek.

A tevékenység napló eseményének integrációs forgatókönyvei:

* [Hozzon létre egy olyan e-mailt vagy webhook-riasztást, amelyet egy tevékenység naplójának eseménye aktivál](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* Egy külső szolgáltatás vagy egyéni elemzési megoldás (például a PowerBI) betöltéséhez [továbbítsa azt egy Event hub](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) számára.

* Elemezze a PowerBI-ben a [PowerBI Content Pack csomag](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)használatával.

* [Mentse azt egy Storage-fiókba archiválás vagy manuális ellenőrzés](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log)céljából. A megőrzési időt (nap) megadhatja a log profilok használatával.

* Lekérdezés és megtekintés a Azure Portalban.

* Lekérdezés a PowerShell-parancsmag, az Azure CLI vagy a REST API használatával.

* A műveletnapló [Azure monitor naplókba](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)való exportálásához jelentkezzen be.

Használhat olyan Storage-fiókot vagy [Event hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) -névteret, amely nem ugyanabban az előfizetésben található, mint a naplót kibocsátó. Bárki, aki konfigurálja a beállítást, rendelkeznie kell a megfelelő [szerepköralapú hozzáférés-vezérléssel (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) mindkét előfizetéshez.

### <a name="azure-diagnostics-logs"></a>Azure-beli diagnosztikai naplók

Az Azure Diagnostics-naplókat egy olyan erőforrás bocsátja ki, amely gazdag és gyakori adatmennyiséget biztosít az adott erőforrás működéséről. Ezek a naplók a tartalom erőforrás típusa szerint változó. A Windows- [események](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) rendszernaplói például a virtuális gépek diagnosztikai naplói kategóriái, valamint a [blob-, tábla-és üzenetsor-naplók](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) a tárolási fiókok diagnosztikai naplófájljainak kategóriái. A diagnosztikai naplók eltérnek a tevékenység naplóitól, amelyek betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre.

![Azure Diagnostics-naplók diagramjai](./media/azure-log-audit/azure-log-audit-fig2.png)

Az Azure Diagnostics-naplók több konfigurációs lehetőséget is kínálnak, például a Azure Portal, a PowerShell, az Azure CLI és a REST API.

**Integrációs forgatókönyvek**

* A naplózáshoz vagy a manuális ellenőrzéshez mentse őket egy [Storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) -fiókba. Megadhatja a megőrzési időt (napokban) a diagnosztikai beállítások használatával.

* [Továbbíthatja őket az Event hubokba](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) egy külső szolgáltatás vagy egyéni analitikai megoldás (például [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)) betöltéséhez.

* Elemezze azokat [Azure monitor naplókkal](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Támogatott szolgáltatások, a diagnosztikai naplók sémája és az erőforrástípus által támogatott naplózási kategóriák**


| Szolgáltatás | Séma és dokumentáció | Erőforrás típusa | Category |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Load Balancer (előzetes verzió) Azure Monitor naplófájljai](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Network Security Groups (Hálózati biztonsági csoportok)|[Hálózati biztonsági csoportok naplóinak Azure Monitor](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Diagnosztikai naplózás a Application Gatewayhoz](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Naplók Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[A keresési Traffic Analytics engedélyezése és használata](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Hozzáférés a diagnosztikai naplókhoz Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Naplózás<br>Kérelmek|
|Azure Data Lake Analytics|[Hozzáférés a diagnosztikai naplókhoz Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Naplózás<br>Kérelmek|
|Azure Logic Apps|[Logic Apps B2B egyéni követési séma](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Diagnosztikai naplók Azure Batch](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Azure Automation Azure Monitor naplófájljai](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Diagnosztikai naplók Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Feladatok diagnosztikai naplói](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Végrehajtás<br>Szerzői műveletek|
|Azure Service Bus|[A Service Bus diagnosztikai naplói](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Jelentéskészítés az Azure Active Directoryban

Azure Active Directory (Azure AD) biztonsági, tevékenység-és naplózási jelentéseket tartalmaz a felhasználó címtárában. Az [Azure ad-naplózási jelentés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) segít azonosítani a felhasználó Azure ad-példányában bekövetkezett privilegizált műveleteket. A Kiemelt jogosultságú műveletek közé tartoznak a jogosultságszint-emelési változások (például a szerepkörök létrehozása vagy a jelszó alaphelyzetbe állítása), a házirend-konfigurációk (például a jelszóházirend) módosítása vagy a címtár konfigurációjának módosítása (például a tartományi összevonási beállítások változásai).

A jelentések megadja az esemény neve, a műveletet végrehajtó felhasználó, a módosítás által érintett cél erőforrás, valamint a dátum és idő (UTC) szerinti naplózási rekordot. A felhasználók a [naplók megtekintése](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)című témakörben leírtak szerint lekérhetik az Azure ad naplózási eseményeinek listáját a [Azure Portal](https://portal.azure.com/)használatával. 

A mellékelt jelentések az alábbi táblázatban láthatók:

| Biztonsági jelentések | Tevékenységjelentések | Naplózási jelentések |
| :--------------- | :--------------- | :------------ |
|Bejelentkezések ismeretlen forrásokról| Alkalmazáshasználat: összegzés| Címtárnaplózási jelentés|
|Több hibát követő bejelentkezések|  Alkalmazáshasználat: részletes||
|Bejelentkezések különböző földrajzi régiókból|    Alkalmazás irányítópultja||
|Bejelentkezések gyanús tevékenységeket mutató IP-címekkel|   Alkalmazás-kiépítési hibák||
|Rendszertelen bejelentkezési tevékenység|    Egyéni felhasználói eszközök||
|Bejelentkezések potenciálisan fertőzött eszközökről|   Egyéni felhasználói tevékenység||
|Rendellenes bejelentkezési tevékenységet mutató felhasználók| Csoporttevékenység-jelentés||
||Jelszó-visszaállítási tevékenységre vonatkozó jelentés||
||Jelszó-visszaállítási tevékenység||

A jelentésekben szereplő adatok hasznosak lehetnek az alkalmazások, például a biztonsági információk és az eseménykezelő (SIEM) rendszerek, a naplózás és az üzleti intelligencia eszközei számára. Az Azure AD-jelentéskészítés API-k REST-alapú API-kon keresztül biztosítják az adatok szoftveres hozzáférését. Ezeket az API- [kat](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) különböző programozási nyelveken és eszközökön hívhatja.

Az esemény-megőrzés az Azure AD-naplózási jelentésben a bérlőhöz társított licenc típusától függően 7-90 nap múlva változhat. 

> [!Note]
> További információ a jelentések megőrzéséről: [Azure ad-jelentés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)adatmegőrzési szabályzatai.

Ha továbbra is szeretné megőrizni a naplózási eseményeket, a jelentéskészítési API használatával rendszeresen lehívhatja a [naplózási eseményeket](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) egy különálló adattárba.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Azure Diagnostics használó virtuális gépek naplófájljai

A [Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics) az Azure-on belüli képesség, amely lehetővé teszi a diagnosztikai adatgyűjtést egy telepített alkalmazáson. A diagnosztikai bővítményt több forrásból is használhatja. Jelenleg támogatott az [Azure Cloud Service webes és feldolgozói szerepkörei](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Azure Diagnostics használó virtuális gépek naplófájljai](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>Microsoft Windows és [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) rendszerű Azure-beli [virtuális gépek](/learn/paths/deploy-a-website-with-azure-virtual-machines/)

A következő módokon engedélyezheti a Azure Diagnostics a virtuális gépen:

* [A Visual Studio használata Azure-beli virtuális gépek nyomon követéséhez](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Azure Diagnostics távoli beállítása Azure-beli virtuális gépen](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Diagnosztika beállítása az Azure Virtual Machines szolgáltatásban a PowerShell használatával](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Windows rendszerű virtuális gép létrehozása figyelési és diagnosztikai szolgáltatásokkal Azure Resource Manager sablon használatával](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics

A [Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) naplózza és metrikai adatokat biztosít egy Storage-fiókhoz. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat. Storage Analytics naplózás elérhető az [Azure Blob, az Azure üzenetsor és az Azure Table Storage szolgáltatásokhoz](https://docs.microsoft.com/azure/storage/storage-introduction). Storage Analytics a tárolási szolgáltatás sikeres és sikertelen kéréseivel kapcsolatos részletes információkat naplózza.

Ezeket az információkat az egyes kérések figyelésére, valamint a Storage szolgáltatással kapcsolatos problémák diagnosztizálására használhatja. A kéréseket a rendszer a lehető legjobb módon naplózza. A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás-végpontra irányuló kérések történnek. Ha például egy Storage-fiók egy tevékenységgel rendelkezik a blob-végponton, de nem a tábla vagy a várólista végpontjában, akkor csak a blob Storage szolgáltatáshoz tartozó naplók jönnek létre.

A Storage Analytics használatához a figyelni kívánt szolgáltatásokhoz egyenként engedélyezze azt. Engedélyezheti a Azure Portalban. [](https://portal.azure.com/) További információ: [Storage-fiók figyelése a Azure Portalban](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Storage Analytics programozott módon is engedélyezheti a REST API vagy az ügyféloldali kódtár használatával. A szolgáltatás tulajdonságainak beállítása művelettel engedélyezheti a Storage Analytics egyenként az egyes szolgáltatásokhoz.

Az összesített adatokat egy jól ismert blobban (naplózáshoz) és jól ismert táblákban (a metrikák esetében) tárolják, amelyek a blob Storage szolgáltatás és a Table Storage szolgáltatás API-jai használatával érhetők el.

A Storage Analytics 20 terabájt (TB) korláttal rendelkezik a Storage-fiókhoz tartozó teljes korláttól független tárolt adat mennyiségétől függően. Az összes napló egy $logs nevű tárolóban található [blokk blobokban](https://docs.microsoft.com/azure/storage/storage-analytics) van tárolva, amely automatikusan létrejön, amikor engedélyezi a Storage Analytics a Storage-fiókhoz.

> [!Note]
> * A számlázási és adatmegőrzési szabályzatokkal kapcsolatos további információkért lásd: [Storage Analytics és számlázás](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * További információ a Storage-fiókok korlátairól: az [Azure Storage skálázhatósági és teljesítménybeli céljai](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Storage Analytics naplózza a következő típusú hitelesített és névtelen kérelmeket:

| Hitelesített  | Névtelen|
| :------------- | :-------------|
| Sikeres kérések | Sikeres kérések |
|Sikertelen kérések, beleértve az időtúllépést, a szabályozást, a hálózatot, az engedélyezést és az egyéb hibákat | Közös hozzáférésű aláírást használó kérelmek, beleértve a sikertelen és sikeres kérelmeket |
| Közös hozzáférésű aláírást használó kérelmek, beleértve a sikertelen és sikeres kérelmeket |Időtúllépési hibák az ügyfél és a kiszolgáló esetében |
|   Elemzési adatkérések |    Sikertelen GET kérelmek a 304 hibakódmal (nincs módosítva) |
| A Storage Analytics saját maga által kezdeményezett kérelmeket (például a naplózás létrehozását vagy törlését) nem naplózza a rendszer. A naplózott adatok teljes listáját [Storage Analytics naplózott műveletek és állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) , valamint [Storage Analytics naplózási formátum](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)dokumentálja. | Az összes többi sikertelen névtelen kérelmet nem naplózza a rendszer. A naplózott adatok teljes listáját [Storage Analytics naplózott műveletek és állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) , valamint [Storage Analytics naplózási formátum](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)dokumentálja. |

### <a name="azure-networking-logs"></a>Azure hálózati naplók

A hálózati naplózás és az Azure monitorozása átfogó, és két széles kategóriára terjed ki:

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): A forgatókönyv-alapú hálózati figyelés a Network Watcher szolgáltatásaiban érhető el. Ez a szolgáltatás magában foglalja a csomagok rögzítését, a következő ugrást, az IP-forgalom ellenőrzését, a biztonsági csoport nézetét, a NSG folyamat naplóit. A forgatókönyvek szintjének figyelése lehetővé teszi a hálózati erőforrások teljes körű megtekintését az egyes hálózati erőforrások figyelésével szemben.

* [Erőforrás-figyelés](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Az erőforrás-szint figyelése négy funkciót, diagnosztikai naplókat, metrikákat, hibaelhárítást és erőforrás-állapotot tartalmaz. Ezek a szolgáltatások a hálózati erőforrás szintjén vannak felépítve.

![Azure hálózati naplók](./media/azure-log-audit/azure-log-audit-fig4.png)

Network Watcher egy regionális szolgáltatás, amely lehetővé teszi az Azure-ban, a-ben és az-ban lévő hálózati forgatókönyvek szintjének figyelését és diagnosztizálását. A Network Watcher elérhető hálózati diagnosztikai és vizualizációs eszközök segítségével megismerheti, diagnosztizálhatja és elemezheti a hálózatát az Azure-ban.

### <a name="network-security-group-flow-logging"></a>Hálózati biztonsági csoport adatfolyamának naplózása

A [NSG flow-naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) a Network Watcher funkciói, amelyek segítségével megtekintheti a bejövő és kimenő IP-forgalomra vonatkozó információkat egy NSG keresztül. A rendszer JSON formátumban írja be ezeket a folyamatokat, és megjeleníti a következőket:
* A kimenő és a bejövő folyamatok egy-egy szabály alapján.
* Az a hálózati adapter, amelyre a folyamat vonatkozik.
* 5 rekordos információ a folyamatról: a forrás vagy a cél IP-címe, a forrás vagy a cél port, valamint a protokoll.
* Azt jelzi, hogy a forgalom engedélyezett vagy tiltott.

Bár a flow naplózza a célként megadott NSG, azok nem ugyanúgy jelennek meg, mint a többi napló. A flow-naplók tárolása csak egy Storage-fiókon belül történik.

A többi naplóban megjelenő adatmegőrzési szabályzatok a folyamat naplófájljaira is érvényesek. A naplók adatmegőrzési szabályzata 1 nap és 365 nap között állítható be. Ha nincs beállítva adatmegőrzési szabály, a naplók megőrzése korlátlan időre szól.

**Diagnosztikai naplók**

Az időszakos és spontán eseményeket a hálózati erőforrások és a bejelentkezett tárolási fiókok hozzák létre, és az esemény-központba vagy Azure Monitor naplókba küldik. A naplók betekintést nyújtanak egy erőforrás állapotára. Ezek az eszközök, például a Power BI és a Azure Monitor naplókban is megtekinthetők. A diagnosztikai naplók megtekintésével kapcsolatos további információkért lásd: [Azure monitor naplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Diagnosztikai naplók](./media/azure-log-audit/azure-log-audit-fig5.png)

A diagnosztikai naplók a [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), a [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), az útvonalak és a [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)számára érhetők el.

A Network Watcher diagnosztikai naplókat jelenít meg. Ez a nézet a diagnosztikai naplózást támogató összes hálózati erőforrást tartalmazza. Ebből a nézetből kényelmesen és gyorsan engedélyezheti és tilthatja le a hálózati erőforrásokat.


A korábban említett naplózási képességek mellett Network Watcher jelenleg a következő képességekkel rendelkezik:
- [Topológia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Olyan hálózati szintű nézetet biztosít, amely megjeleníti az erőforráscsoport hálózati erőforrásai közötti különböző kapcsolatokat és társításokat.

- [Változó csomagok rögzítése](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Rögzíti a csomagokat a virtuális gépekről és azokból. A speciális szűrési beállítások és a finomhangolási vezérlők, például az idő-és méret korlátozási beállításai, a sokoldalúságot biztosítják. A csomagok tárolása blob-tárolóban vagy a helyi lemezen *. Cap* fájlformátumban lehetséges.

- [IP-forgalom ellenőrzése](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Ellenőrzi, hogy a csomag engedélyezett vagy megtagadott-e a flow-információk 5 rekordos csomagjainak paraméterei (azaz a cél IP-címe, a forrás IP-címe, a célport, a forrásport és a protokoll) alapján. Ha egy biztonsági csoport megtagadja a csomagot, a rendszer visszaadja a csomagot és a csoportot, amely megtagadja a csomagokat.

- [Következő ugrás](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Meghatározza a következő ugrást az Azure hálózati hálóban továbbított csomagok esetében, hogy a nem konfigurált, felhasználó által megadott útvonalakat is diagnosztizálja.

- [Biztonsági csoport nézete](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): A virtuális gépen alkalmazott hatályos és alkalmazott biztonsági szabályok beolvasása.

- [Virtuális hálózati átjáró és a kapcsolatok hibaelhárítása](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Segítséget nyújt a virtuális hálózati átjárók és a kapcsolatok hibakeresésében.

- [Hálózati előfizetés korlátai](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Lehetővé teszi a hálózati erőforrás-használat korlátozásait.

### <a name="application-insights"></a>Application Insights

Az [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető APM-szolgáltatás több platformon futó webes fejlesztőknek. Az élő webalkalmazások figyelésére használható. Automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket. Hatékony elemzőeszközöket tartalmaz, amelyek segítenek a problémák felismerésében, és annak a megértésében, hogy a felhasználók mire használják ténylegesen az alkalmazást.

Application Insights úgy lett kialakítva, hogy segítsen folyamatosan javítani a teljesítményt és a használhatóságot.

Számos platformon használható, többek között a .NET, a Node. js és a Java EE, függetlenül attól, hogy a helyszínen vagy a felhőben vannak tárolva. Integrálva van a DevOps-folyamattal, és a különböző fejlesztői eszközökkel rendelkező összekötő pontokat tartalmaz.

![Application Insights diagram](./media/azure-log-audit/azure-log-audit-fig6.png)

A fejlesztőcsapatoknak készült Application Insights segít az adott alkalmazás teljesítményének megismerésében, valamint abban, hogy a felhasználók miként használják az alkalmazást. A szolgáltatás az alábbiakat figyeli:

* **Kérelmek díjai, válaszideje és meghibásodási aránya**: Megtudhatja, hogy mely oldalak a legnépszerűbbek, milyen napszakok és hol vannak a felhasználók. Megtekintheti, hogy mely lapok teljesítenek a legjobban. Ha a válaszadási időpontok és a hibák aránya nagyobb kérelmek esetén magasra vált, lehetséges, hogy újrabeszerzési probléma merült fel.

* **Függőségi arányok, válaszidő és sikertelenség aránya**: Megtudhatja, hogy a külső szolgáltatások lelassulnak-e.

* **Kivételek**: Elemezze az összesített statisztikát, vagy válasszon ki konkrét példányokat, és részletezse a verem nyomkövetését és a kapcsolódó kérelmeket. A kiszolgálói és a böngészői kivételekről egyaránt készül jelentés.

* **Oldalletöltések és betöltési teljesítmény**: Jelentések beolvasása a felhasználói böngészőkből.

* **Ajax-hívások**: A weboldalak díjszabása, a válaszidő és a sikertelenség aránya.

* **Felhasználók és munkamenetek száma**.

* **Teljesítményszámlálók**: Adatok beszerzése Windows vagy Linux rendszerű gépekről, például CPU-, memória-és hálózati használatból.

* **Gazda diagnosztika**: Adatok lekérése a Docker vagy az Azure szolgáltatásból.

* **Diagnosztikai nyomkövetési naplók**: Adatok lekérése az alkalmazásból, hogy a nyomkövetési eseményeket a kérelmekkel tudja összekapcsolni.

* **Egyéni események és mérőszámok**: Az ügyfél vagy a kiszolgáló kódjában írt adatok lekérésével nyomon követheti az üzleti eseményeket, például az eladott elemeket vagy a megnyert játékokat.

Az alábbi táblázat az integrációs forgatókönyveket sorolja fel és ismerteti:

| Integrációs forgatókönyv | Leírás |
| --------------------- | :---------- |
|[Alkalmazás-hozzárendelés](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Az alkalmazás összetevői, alapvető metrikákkal és riasztásokkal.|
|[Diagnosztikai keresés a példányok adatait](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Események keresése és szűrése, például kérések, kivételek, függőségi hívások, naplókivonatok és lapmegtekintések.|
|[Metrikaböngésző összesített adatokat](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|Összesített adatok – például kérés- és hibaarányok, valamint kivételek, válaszidők és lapbetöltési idők – böngészése, szűrése és szegmentálása.|
|[Irányítópultok](https://docs.microsoft.com/azure/azure-monitor/app/overview-dashboard)|Különböző erőforrásokból származó adatokat fűzhet össze és oszthat meg másokkal. Kiváló megoldás több összetevőből álló alkalmazások, valamint csoportszobában való folyamatos megjelenítés esetén.|
|[Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|Új buildverzió telepítésekor közel valós idejű teljesítménymutatókat figyelhet, és meggyőződhet arról, hogy minden megfelelő módon működik-e.|
|[Elemzés](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|A hatékony lekérdezési nyelvnek köszönhetően válaszokat kaphat az alkalmazás teljesítményére és használatára vonatkozó legégetőbb kérdésekre.|
|[Automatikus és manuális riasztások](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Az automatikus riasztások alkalmazkodnak az alkalmazás normál telemetria, és akkor aktiválódik, ha a szokásos mintázaton kívül van valami. Riasztásokat állíthat be az egyéni vagy normál metrikák adott szintjeire is.|
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|A kódban megtekintheti a teljesítményadatokat. A hívásláncokból a kódba ugorhat.|
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrálhatja a használati metrikákat más üzleti intelligenciával.|
|[REST API](https://dev.applicationinsights.io/)|Kód írásával lekérdezéseket futtathat a metrikákhoz és a nyers adatokhoz kapcsolódóan.|
|[Folyamatos exportálás](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Nyers adattárolás tömeges exportálása a tárolóba a megérkezéskor.|

### <a name="azure-security-center-alerts"></a>Riasztások Azure Security Center

Azure Security Center fenyegetések észlelése úgy működik, hogy automatikusan összegyűjti az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások biztonsági információit. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket. A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására. További információ: [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Azure Security Center diagram](./media/azure-log-audit/azure-log-audit-fig7.png)

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. Áttörést jelent a nagyméretű adatmennyiség és a [gépi tanulási](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák esetében, hogy kiértékelje az eseményeket a teljes felhőalapú hálóban. Ily módon észleli azokat a fenyegetéseket, amelyek a manuális megközelítések és a támadások alakulásának előrejelzésével nem lennének azonosíthatók. Ezek a biztonsági elemzések a következők:

* **Integrált veszélyforrások felderítése**: A Microsoft-termékek és-szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft Security Response Center (MSRC) és a külső hírcsatornák globális fenyegetési intelligenciának alkalmazásával ismert hibás szereplőkre keres.

* **Viselkedési elemzés**: Az ismert mintákat alkalmazza a kártékony viselkedés felderítésére.

* **Anomáliák észlelése**: Statisztikai profilkészítést használ egy korábbi alapterv létrehozásához. Jelzi a meghatározott alapkonfigurációktól való olyan eltéréseket, amelyek potenciális támadásként értelmezhetők.

Számos biztonsági művelet és incidens-válasz egy SIEM-megoldásra támaszkodik a osztályozásakor és a biztonsági riasztások kivizsgálása szempontjából. A Azure Log Integration segítségével szinkronizálhatja Security Center riasztásokat és a virtuális gépek biztonsági eseményeit, amelyeket az Azure Diagnostics és a naplók összegyűjtenek, a Azure Monitor-naplók vagy SIEM-megoldás közel valós időben.

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

Azure Monitor a naplók egy Azure-szolgáltatás, amely segít összegyűjteni és elemezni az erőforrások által a felhőben és a helyszíni környezetekben létrehozott adatokat. Az integrált keresés és az egyéni irányítópultok segítségével valós idejű elemzéseket biztosít, így a fizikai helyüktől függetlenül akár több millió rekordot is elemezheti az összes munkaterhelésben és kiszolgálón.

![Azure Monitor naplók diagramja](./media/azure-log-audit/azure-log-audit-fig8.png)

Azure Monitor naplók középpontjában az Azure-ban üzemeltetett Log Analytics munkaterület. Azure Monitor naplók adatokat gyűjtenek a munkaterületen a csatlakoztatott forrásokból az adatforrások konfigurálásával és a megoldások az előfizetéshez való hozzáadásával. Az egyes adatforrások és megoldások különböző bejegyzéstípusokat hoznak létre, amelyek mindegyike saját tulajdonságokkal rendelkezik. A forrásokat és a megoldásokat azonban továbbra is elemezheti a munkaterületre irányuló lekérdezésekben. Ez a funkció lehetővé teszi, hogy ugyanazokat az eszközöket és metódusokat használja a különböző forrásokból gyűjtött különféle adatokkal való munkához.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

A csatlakoztatott források azok a számítógépek és egyéb erőforrások, amelyek a Azure Monitor naplók által gyűjtött adatokat eredményezik. A források tartalmazhatják azokat a [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) -és [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) -számítógépeken telepített ügynököket, amelyek közvetlenül vagy [egy csatlakoztatott System Center Operations Manager felügyeleti csoportban](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents)lévő ügynökökkel csatlakoznak. Azure Monitor naplók adatokat is gyűjthetnek egy [Azure Storage](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)-fiókból.

[](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) Az adatforrások az egyes csatlakoztatott forrásokból gyűjtött különböző típusú adatok. A források a [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) -és Linux-ügynököktől származó eseményeket és [teljesítményadatokat](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) , valamint az [IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) -naplókat és az [egyéni szöveges naplókat](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs)is tartalmazzák. Konfigurálhatja az összegyűjteni kívánt egyes adatforrásokat, és a konfiguráció automatikusan érvénybe lép minden egyes csatlakoztatott forráson.

[Az Azure-szolgáltatásokhoz négy módon gyűjthet naplókat és mérőszámokat](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):

* Azure Diagnostics a közvetlen Azure Monitor naplókba (**diagnosztika** a következő táblázatban)

* Azure Diagnostics az Azure Storage-ba Azure Monitor naplókba (**tárolás** a következő táblázatban)

* Összekötők az Azure-szolgáltatásokhoz (**összekötő** a következő táblázatban)

* Az adatok gyűjtésére és közzétételére szolgáló parancsfájlok Azure Monitor naplókba (a következő táblázatban szereplő üres cellák és a nem felsorolt szolgáltatások esetében)

| Szolgáltatás | Erőforrás típusa | Logs | Mérőszámok | Megoldás |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnosztika|Diagnosztika|    [Azure-alkalmazás](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics) [Átjáró-elemzés](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-analytics-solution-in-azure-monitor)|
|Application Insights||     Összekötő|  Összekötő|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Összekötő (előzetes verzió)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Fiókok Azure Automation| Microsoft.Automation/<br>automationAccounts|    Diagnosztika||       [További információ](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Fiókok Azure Batch|  Microsoft.Batch/<br>batchAccounts|  Diagnosztika|    Diagnosztika||
|Klasszikus a cloud services||       Storage||       [További információ](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>fiókok|       Diagnosztika|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>fiókok|   Diagnosztika|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>fiókok|   Diagnosztika|||
|Azure Event hub-névtér| Microsoft.EventHub/<br>névterek|  Diagnosztika|    Diagnosztika||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnosztika||
|Azure Key Vault|   Microsoft.KeyVault/<br>boltívek|  Diagnosztika  || [Key Vault-elemzés](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnosztika|||
|Azure Logic Apps|  Microsoft.Logic/<br>munkafolyamatok|  Diagnosztika|    Diagnosztika||
||Microsoft.Logic/<br>integrationAccounts||||
|Network Security Groups (Hálózati biztonsági csoportok)|   Microsoft.Network/<br>networksecuritygroups|Diagnosztika||   [Azure hálózati biztonsági csoport elemzése](https://docs.microsoft.com/azure/azure-monitor/insights/azure-networking-analytics#azure-application-gateway-and-network-security-group-analytics)|
|Helyreállítási tárak|   Microsoft.RecoveryServices/<br>boltívek|||[Az Azure Recovery Services-Analytics (előzetes verzió)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Szolgáltatások keresése|   Microsoft.Search/<br>searchServices|    Diagnosztika|    Diagnosztika||
|Service Bus-névtér| Microsoft.ServiceBus/<br>névterek|    Diagnosztika|Diagnosztika|    [Service Bus-elemzés (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric-elemzés (előzetes verzió)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>kiszolgálók<br>databases||       Diagnosztika||
||Microsoft.Sql/<br>kiszolgálók<br>elasticPools||||
|Storage|||         Szkript| [Az Azure Storage Analytics (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure-alapú virtuális gépek|    Microsoft.Compute/<br>VirtualMachines|  Mellék|  Mellék||
||||Diagnosztika||
|Virtuálisgép-méretezési csoportok|    Microsoft.Compute/<br>VirtualMachines    ||Diagnosztika||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>VirtualMachines||||
|Webkiszolgáló-farmok|Microsoft.Web/<br>kiszolgálófarmok||   Diagnosztika
|Webhelyek|  Microsoft.Web/<br>helyek ||      Diagnosztika|    [További információ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>helyek<br>bővítőhely||||


## <a name="log-integration-with-on-premises-siem-systems"></a>A helyszíni SIEM-rendszerekkel való integráció naplózása

A Azure Log Integration segítségével integrálhatja az Azure-erőforrásokból származó nyers naplókat a helyszíni SIEM-rendszerével (biztonsági információk és eseménykezelő rendszer). A AzLog letöltése a 2018. június 27-én le lett tiltva. Útmutatás a további lépések áttekintéséhez: az [Azure monitor használata az Siem-eszközökkel való integráláshoz](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

![Log integrációs diagram](./media/azure-log-audit/azure-log-audit-fig9.png)

A log Integration összegyűjti az Azure Diagnostics szolgáltatást a Windows rendszerű virtuális gépekről, az Azure-Tevékenységnaplók, a Azure Security Center riasztások és az Azure erőforrás-szolgáltató naplóiból. Ez az integráció egy egységes irányítópultot biztosít az összes eszközhöz, akár a helyszínen, akár a felhőben, így összesítheti, korrelálhatja, elemezheti és riasztást készíthet a biztonsági eseményekről.

A log Integration jelenleg a Windows rendszerű virtuális gépekről az Azure-előfizetéssel, Azure Security Center riasztásokkal, az Azure Diagnostics-naplókkal és az Azure AD-naplókkal való integrációját támogatja.

| Napló típusa | A JSON-t támogató naplók Azure Monitor (splunk, ArcSight és IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Azure AD-naplók|   Igen|
|Tevékenységnaplók| Igen|
|Riasztások Security Center |Igen|
|Diagnosztikai naplók (erőforrás-naplók)|  Igen|
|VIRTUÁLIS gépek naplói|   Igen, továbbított eseményeken keresztül, és nem a JSON-n keresztül|

[A Azure log Integration első lépései](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): Ez az oktatóanyag végigvezeti a Azure Log Integration telepítésének és a naplók Azure Storage-ba való integrálásának, az Azure-Tevékenységnaplók, a Azure Security Center riasztások és az Azure AD-naplók integrálásának lépésein.

Az SIEM integrációs forgatókönyvei:

* [Partneri konfiguráció lépései](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Ez a blogbejegyzés bemutatja, hogyan konfigurálhatja a Azure Log Integrationt a partneri megoldások splunk, a HP ArcSight és az IBM QRadar való együttműködéshez.

* [Azure log Integration gyakori kérdések](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): Ez a cikk a Azure Log Integrationával kapcsolatos kérdésekre ad választ.

* [Security Center riasztások integrálása Azure log Integrationekkel](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): Ez a cikk azt ismerteti, hogyan lehet szinkronizálni Security Center riasztásokat, az Azure Diagnostics-naplók által gyűjtött virtuális gépek biztonsági eseményeit, valamint az Azure-naplókat a Azure Monitor-naplókkal vagy SIEM-megoldásokkal.

## <a name="next-steps"></a>További lépések

- [Naplózás és naplózás](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): Az adatvédelmet úgy biztosíthatja, hogy megtartja a láthatóságot, és gyorsan reagál a biztonsági riasztásokra.

- [Biztonsági naplózás és naplózási naplók az Azure-on belül](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Ezen beállítások betartatásával győződjön meg arról, hogy az Azure-példányok megfelelő biztonsági és naplózási naplókat gyűjtenek.

- [Webhelycsoport naplózási beállításainak konfigurálása](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Ha Ön webhelycsoport-rendszergazda, kérje le az egyes felhasználói műveletek előzményeit, valamint az adott dátumtartomány által végrehajtott műveletek előzményeit. 

- [Keresse meg a naplót az Office 365 biztonsági és megfelelőségi központban](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Az Office 365 Biztonsági és megfelelőségi központ használatával kereshet az egyesített naplóban, és megtekintheti az Office 365-szervezet felhasználói és rendszergazdai tevékenységeit.


