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
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: 80f90f1788e798261f77bb7a4147763e7ca6cec0
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946504"
---
# <a name="azure-logging-and-auditing"></a>Azure-naplózás

Az Azure a konfigurálható biztonsági naplózási és naplózási lehetőségek széles skáláját biztosítja, amelyek segítségével azonosíthatja a biztonsági szabályzatokban és mechanizmusokban mutatkozó réseket. Ez a cikk az Azure-ban üzemeltetett szolgáltatások biztonsági naplóinak generálását, gyűjtését és elemzését ismerteti.

> [!Note]
> A cikkben szereplő bizonyos javaslatok megnövelheti az adatok, a hálózat vagy a számítási erőforrások használatát, és növelheti a licenc-vagy előfizetési költségeket.

## <a name="types-of-logs-in-azure"></a>Az Azure-beli naplók típusai

A Felhőbeli alkalmazások összetett, számos mozgó részből állnak. A naplók biztosítják az alkalmazások működésének megtartását. A naplók segítenek a múltbeli problémák megoldásában, illetve a lehetséges lehetőségek megelőzésében. Emellett segíthetnek az alkalmazások teljesítményének vagy karbantartásának javításában, vagy automatizálni azokat a műveleteket, amelyek egyébként manuális beavatkozást igényelnek.

Az Azure-naplók a következő típusokra vannak kategorizálva:
* A **vezérlési/felügyeleti naplók** a Azure Resource Manager létrehozási, frissítési és törlési műveleteivel kapcsolatos információkat biztosítanak. További információ: Azure- [tevékenységek naplói](../../azure-monitor/platform/activity-logs-overview.md).

* **Az adatsíkok naplói** az Azure-erőforrások használatának részeként kiváltott eseményekről nyújtanak információt. Ilyen típusú napló például a Windows-eseményrendszer, a biztonság és az alkalmazás naplófájljai egy virtuális gépen (VM) és a Azure Monitoron keresztül konfigurált [diagnosztikai naplókon](../../azure-monitor/platform/diagnostic-logs-overview.md) .

* A **feldolgozott események** információt nyújtanak az Ön nevében feldolgozott elemzett eseményekről/riasztásokról. Ilyen típusú példák olyan [Azure Security Center riasztások](../../security-center/security-center-managing-and-responding-alerts.md) , ahol a [Azure Security Center](../../security-center/security-center-intro.md) feldolgozta és elemezte az előfizetését, és rövid biztonsági riasztásokat biztosít.

A következő táblázat az Azure-ban elérhető naplók legfontosabb típusait sorolja fel:

| Naplókategória | Napló típusa | Használat | Integráció |
| ------------ | -------- | ------ | ----------- |
|[Tevékenységnaplók](../../azure-monitor/platform/activity-logs-overview.md)|Az Azure Resource Manager-erőforrásokon futó események vezérlése|  Betekintést nyújt az előfizetése erőforrásaiban végrehajtott műveletekre.|    REST API, [Azure monitor](../../azure-monitor/platform/activity-logs-overview.md)|
|[Azure-beli diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md)|Az előfizetésben Azure Resource Manager erőforrások működésével kapcsolatos gyakori információk|  Betekintést nyújt az erőforrás által végrehajtott műveletekre.| Azure Monitor, [stream](../../azure-monitor/platform/diagnostic-logs-overview.md)|
|[Azure AD Reporting](../../active-directory/reports-monitoring/overview-reports.md)|Naplók és jelentések | A felhasználók és csoportok kezelésével kapcsolatos felhasználói bejelentkezési tevékenységek és rendszertevékenység-információk jelentése.|[Graph API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[Virtual Machines és Cloud Services](../../azure-monitor/learn/quick-collect-azurevm.md)|A Windows Eseménynapló szolgáltatás és a Linux syslog|  Rögzíti a rendszerinformációkat és a naplózási adatait a virtuális gépeken, és átviszi azokat egy tetszőleges Storage-fiókba.|   Windows (Windows Azure Diagnostics [[wad](../../monitoring-and-diagnostics/azure-diagnostics.md)] Storage) és Linux használata a Azure monitor|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|A Storage-naplózási szolgáltatás metrikákat biztosít a Storage-fiókhoz|Betekintést nyújt a nyomkövetési kérelmekbe, elemzi a használati trendeket, és diagnosztizálja a Storage-fiókkal kapcsolatos problémákat.|   REST API vagy az [ügyféloldali kódtár](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Hálózati biztonsági csoport (NSG) folyamatábrái](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON-formátum, a kimenő és a bejövő folyamatok megjelenítése a szabályok alapján|Egy hálózati biztonsági csoporton keresztül megjeleníti a bejövő és kimenő IP-forgalomra vonatkozó információkat.|[Azure-Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Alkalmazás-betekintés](../../azure-monitor/app/app-insights-overview.md)|Naplók, kivételek és egyéni diagnosztika|  Egy Application Performance monitoring (APM) szolgáltatást biztosít több platformon futó webes fejlesztőknek.| REST API, [Power bi](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Adatfeldolgozási/biztonsági riasztások|    Riasztások Azure Security Center Azure Monitor naplók riasztásai|    Biztonsági információkat és riasztásokat biztosít.|  REST API-k, JSON|

### <a name="activity-logs"></a>Tevékenységnaplók

Az [Azure-tevékenység naplói](../../azure-monitor/platform/activity-logs-overview.md) betekintést nyújtanak az előfizetése erőforrásaiban végrehajtott műveletekre. A tevékenységi naplók korábban "naplók" vagy "operatív naplók" néven ismertek, mert az előfizetések esetében jelentést jelentenek a [vezérlési sík eseményeiről](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) . 

A Tevékenységnaplók segítségével meghatározható az írási műveletek (azaz a PUT, a POST vagy a DELETE) "mi, ki és mikor". A Tevékenységnaplók segítenek megérteni a művelet állapotát és az egyéb kapcsolódó tulajdonságokat is. A tevékenységi naplók nem tartalmaznak olvasási (GET) műveleteket.

Ebben a cikkben a PUT, POST és DELETE kifejezés az összes olyan írási műveletre vonatkozik, amelyet a tevékenység naplója az erőforrásokon tartalmaz. Például a tevékenységek naplói segítségével hibát kereshet a hibák elhárítása során, vagy megfigyelheti, hogy a szervezet felhasználója hogyan módosított egy erőforrást.

![Tevékenységi napló diagramja](./media/log-audit/azure-log-audit-fig1.png)

Az eseményeket a Azure Portal, az [Azure CLI](../../storage/common/storage-azure-cli.md), a PowerShell-parancsmagok és a [Azure monitor REST API](../../azure-monitor/platform/rest-api-walkthrough.md)használatával kérheti le. A Tevékenységnaplók 90 napos adatmegőrzési időtartammal rendelkeznek.

A tevékenység napló eseményének integrációs forgatókönyvei:

* [Hozzon létre egy olyan e-mailt vagy webhook-riasztást, amelyet egy tevékenység naplójának eseménye aktivál](../../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md).

* Egy külső szolgáltatás vagy egyéni elemzési megoldás (például a PowerBI) betöltéséhez [továbbítsa azt egy Event hub](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) számára.

* Elemezze a PowerBI-ben a [PowerBI Content Pack csomag](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)használatával.

* [Mentse azt egy Storage-fiókba archiválás vagy manuális ellenőrzés](../../azure-monitor/platform/archive-activity-log.md)céljából. A megőrzési időt (nap) megadhatja a log profilok használatával.

* Lekérdezés és megtekintés a Azure Portalban.

* Lekérdezés a PowerShell-parancsmag, az Azure CLI vagy a REST API használatával.

* A műveletnapló [Azure monitor naplókba](../../log-analytics/log-analytics-queries.md)való exportálásához jelentkezzen be.

Használhat olyan Storage-fiókot vagy [Event hub](../../event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-capture.md) -névteret, amely nem ugyanabban az előfizetésben található, mint a naplót kibocsátó. Bárki, aki konfigurálja a beállítást, rendelkeznie kell a megfelelő [szerepköralapú hozzáférés-vezérléssel (RBAC)](../../role-based-access-control/role-assignments-portal.md) mindkét előfizetéshez.

### <a name="azure-diagnostics-logs"></a>Azure-beli diagnosztikai naplók

Az Azure Diagnostics-naplókat egy olyan erőforrás bocsátja ki, amely gazdag és gyakori adatmennyiséget biztosít az adott erőforrás működéséről. Ezek a naplók a tartalom erőforrás típusa szerint változó. A Windows- [események](../../azure-monitor/platform/data-sources-windows-events.md) rendszernaplói például a virtuális gépek diagnosztikai naplói kategóriái, valamint a [blob-, tábla-és üzenetsor-naplók](../../storage/common/storage-monitor-storage-account.md) a tárolási fiókok diagnosztikai naplófájljainak kategóriái. A diagnosztikai naplók eltérnek a tevékenység naplóitól, amelyek betekintést nyújtanak az előfizetésben lévő erőforrásokon végrehajtott műveletekre.

![Azure Diagnostics-naplók diagramjai](./media/log-audit/azure-log-audit-fig2.png)

Az Azure Diagnostics-naplók több konfigurációs lehetőséget is kínálnak, például a Azure Portal, a PowerShell, az Azure CLI és a REST API.

**Integrációs forgatókönyvek**

* A naplózáshoz vagy a manuális ellenőrzéshez mentse őket egy [Storage](../../azure-monitor/platform/archive-diagnostic-logs.md) -fiókba. Megadhatja a megőrzési időt (napokban) a diagnosztikai beállítások használatával.

* [Továbbíthatja őket az Event hubokba](../../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) egy külső szolgáltatás vagy egyéni analitikai megoldás (például [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)) betöltéséhez.

* Elemezze azokat [Azure monitor naplókkal](../../log-analytics/log-analytics-queries.md).

**Támogatott szolgáltatások, a diagnosztikai naplók sémája és az erőforrástípus által támogatott naplózási kategóriák**


| Szolgáltatás | Séma és dokumentáció | Erőforrás típusa | Category |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Load Balancer (előzetes verzió) Azure Monitor naplófájljai](../../load-balancer/load-balancer-monitor-log.md)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Network Security Groups (Hálózati biztonsági csoportok)|[Hálózati biztonsági csoportok naplóinak Azure Monitor](../../virtual-network/virtual-network-nsg-manage-log.md)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Diagnosztikai naplózás a Application Gatewayhoz](../../application-gateway/application-gateway-diagnostics.md)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Naplók Key Vault](../../key-vault/key-vault-logging.md)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[A keresési Traffic Analytics engedélyezése és használata](../../search/search-traffic-analytics.md)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[Hozzáférés a diagnosztikai naplókhoz Data Lake Store](../../data-lake-store/data-lake-store-diagnostic-logs.md)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Naplózás<br>Kérelmek|
|Azure Data Lake Analytics|[Hozzáférés a diagnosztikai naplókhoz Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Naplózás<br>Kérelmek|
|Azure Logic Apps|[Logic Apps B2B egyéni követési séma](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Diagnosztikai naplók Azure Batch](../../batch/batch-diagnostics.md)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Azure Automation Azure Monitor naplófájljai](../../automation/automation-manage-send-joblogs-log-analytics.md)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Diagnosztikai naplók Event Hubs](../../event-hubs/event-hubs-diagnostic-logs.md)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Feladatok diagnosztikai naplói](../../stream-analytics/stream-analytics-job-diagnostic-logs.md)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Végrehajtás<br>Szerzői műveletek|
|Azure Service Bus|[A Service Bus diagnosztikai naplói](../../service-bus-messaging/service-bus-diagnostic-logs.md)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Jelentéskészítés az Azure Active Directoryban

Azure Active Directory (Azure AD) biztonsági, tevékenység-és naplózási jelentéseket tartalmaz a felhasználó címtárában. Az [Azure ad-naplózási jelentés](../../active-directory/active-directory-reporting-azure-portal.md) segít azonosítani a felhasználó Azure ad-példányában bekövetkezett privilegizált műveleteket. A Kiemelt jogosultságú műveletek közé tartoznak a jogosultságszint-emelési változások (például a szerepkörök létrehozása vagy a jelszó alaphelyzetbe állítása), a házirend-konfigurációk (például a jelszóházirend) módosítása vagy a címtár konfigurációjának módosítása (például a tartományi összevonási beállítások változásai).

A jelentések megadja az esemény neve, a műveletet végrehajtó felhasználó, a módosítás által érintett cél erőforrás, valamint a dátum és idő (UTC) szerinti naplózási rekordot. A felhasználók a [naplók megtekintése](../../active-directory/reports-monitoring/overview-reports.md)című témakörben leírtak szerint lekérhetik az Azure ad naplózási eseményeinek listáját a [Azure Portal](https://portal.azure.com/)használatával. 

A mellékelt jelentések az alábbi táblázatban láthatók:

| Biztonsági jelentések | Tevékenységjelentések | Naplózási jelentések |
| :--------------- | :--------------- | :------------ |
|Bejelentkezések ismeretlen forrásokról| Alkalmazáshasználat: összegzés| Címtárnaplózási jelentés|
|Több hibát követő bejelentkezések|  Alkalmazáshasználat: részletes||
|Bejelentkezések különböző földrajzi régiókból|    Alkalmazás irányítópultja||
|Gyanús tevékenységeket mutató IP-címekről indított bejelentkezések|   Alkalmazás-kiépítési hibák||
|Rendszertelen bejelentkezési tevékenység|    Egyéni felhasználói eszközök||
|Bejelentkezések potenciálisan fertőzött eszközökről|   Egyéni felhasználói tevékenység||
|Rendellenes bejelentkezési tevékenységet mutató felhasználók| Csoporttevékenység-jelentés||
||Jelszó-visszaállítási tevékenységre vonatkozó jelentés||
||Jelszó-visszaállítási tevékenység||

A jelentésekben szereplő adatok hasznosak lehetnek az alkalmazások, például a biztonsági információk és az eseménykezelő (SIEM) rendszerek, a naplózás és az üzleti intelligencia eszközei számára. Az Azure AD-jelentéskészítés API-k REST-alapú API-kon keresztül biztosítják az adatok szoftveres hozzáférését. Ezeket az API- [kat](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) különböző programozási nyelveken és eszközökön hívhatja.

Az esemény-megőrzés az Azure AD-naplózási jelentésben a bérlőhöz társított licenc típusától függően 7-90 nap múlva változhat. 

> [!Note]
> További információ a jelentések megőrzéséről: [Azure ad-jelentés](../../active-directory/reports-monitoring/reference-reports-data-retention.md)adatmegőrzési szabályzatai.

Ha továbbra is szeretné megőrizni a naplózási eseményeket, a jelentéskészítési API használatával rendszeresen lehívhatja a [naplózási eseményeket](../../active-directory/active-directory-reporting-activity-audit-logs.md) egy különálló adattárba.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Azure Diagnostics használó virtuális gépek naplófájljai

A [Azure Diagnostics](../../monitoring-and-diagnostics/azure-diagnostics.md) az Azure-on belüli képesség, amely lehetővé teszi a diagnosztikai adatgyűjtést egy telepített alkalmazáson. A diagnosztikai bővítményt több forrásból is használhatja. Jelenleg támogatott az [Azure Cloud Service webes és feldolgozói szerepkörei](../../cloud-services/cloud-services-choose-me.md).

![Azure Diagnostics használó virtuális gépek naplófájljai](./media/log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabricservice-fabricservice-fabric-overviewmd"></a>Microsoft Windows és [Service Fabric](../../service-fabric/service-fabric-overview.md) rendszerű Azure-beli [virtuális gépek](/learn/paths/deploy-a-website-with-azure-virtual-machines/)

A következő módokon engedélyezheti a Azure Diagnostics a virtuális gépen:

* [A Visual Studio használata Azure-beli virtuális gépek nyomon követéséhez](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Azure Diagnostics távoli beállítása Azure-beli virtuális gépen](../../virtual-machines/virtual-machines-dotnet-diagnostics.md)

* [Diagnosztika beállítása az Azure Virtual Machines szolgáltatásban a PowerShell használatával](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Windows rendszerű virtuális gép létrehozása figyelési és diagnosztikai szolgáltatásokkal Azure Resource Manager sablon használatával](../../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics

A [Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) naplózza és metrikai adatokat biztosít egy Storage-fiókhoz. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat. Storage Analytics naplózás elérhető az [Azure Blob, az Azure üzenetsor és az Azure Table Storage szolgáltatásokhoz](../../storage/common/storage-introduction.md). Storage Analytics a tárolási szolgáltatás sikeres és sikertelen kéréseivel kapcsolatos részletes információkat naplózza.

Ezeket az információkat az egyes kérések figyelésére, valamint a Storage szolgáltatással kapcsolatos problémák diagnosztizálására használhatja. A kéréseket a rendszer a lehető legjobb módon naplózza. A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás-végpontra irányuló kérések történnek. Ha például egy Storage-fiók egy tevékenységgel rendelkezik a blob-végponton, de nem a tábla vagy a várólista végpontjában, akkor csak a blob Storage szolgáltatáshoz tartozó naplók jönnek létre.

A Storage Analytics használatához a figyelni kívánt szolgáltatásokhoz egyenként engedélyezze azt. Engedélyezheti a Azure Portalban. [](https://portal.azure.com/) További információ: [Storage-fiók figyelése a Azure Portalban](../../storage/common/storage-monitor-storage-account.md). Storage Analytics programozott módon is engedélyezheti a REST API vagy az ügyféloldali kódtár használatával. A szolgáltatás tulajdonságainak beállítása művelettel engedélyezheti a Storage Analytics egyenként az egyes szolgáltatásokhoz.

Az összesített adatokat egy jól ismert blobban (naplózáshoz) és jól ismert táblákban (a metrikák esetében) tárolják, amelyek a blob Storage szolgáltatás és a Table Storage szolgáltatás API-jai használatával érhetők el.

A Storage Analytics 20 terabájt (TB) korláttal rendelkezik a Storage-fiókhoz tartozó teljes korláttól független tárolt adat mennyiségétől függően. Az összes napló egy $logs nevű tárolóban található [blokk blobokban](../../storage/common/storage-analytics.md) van tárolva, amely automatikusan létrejön, amikor engedélyezi a Storage Analytics a Storage-fiókhoz.

> [!Note]
> * A számlázási és adatmegőrzési szabályzatokkal kapcsolatos további információkért lásd: [Storage Analytics és számlázás](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * További információ a Storage-fiókok korlátairól: az [Azure Storage skálázhatósági és teljesítménybeli céljai](../../storage/common/storage-scalability-targets.md).

Storage Analytics naplózza a következő típusú hitelesített és névtelen kérelmeket:

| Hitelesített  | Névtelen|
| :------------- | :-------------|
| Sikeres kérelmek | Sikeres kérelmek |
|Sikertelen kérések, beleértve az időtúllépést, a szabályozást, a hálózatot, az engedélyezést és az egyéb hibákat | Közös hozzáférésű aláírást használó kérelmek, beleértve a sikertelen és sikeres kérelmeket |
| Közös hozzáférésű aláírást használó kérelmek, beleértve a sikertelen és sikeres kérelmeket |Időtúllépési hibák az ügyfél és a kiszolgáló esetében |
|   Elemzési adatkérések |    Sikertelen GET kérelmek a 304 hibakódmal (nincs módosítva) |
| A Storage Analytics saját maga által kezdeményezett kérelmeket (például a naplózás létrehozását vagy törlését) nem naplózza a rendszer. A naplózott adatok teljes listáját [Storage Analytics naplózott műveletek és állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) , valamint [Storage Analytics naplózási formátum](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)dokumentálja. | Az összes többi sikertelen névtelen kérelmet nem naplózza a rendszer. A naplózott adatok teljes listáját [Storage Analytics naplózott műveletek és állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) , valamint [Storage Analytics naplózási formátum](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)dokumentálja. |

### <a name="azure-networking-logs"></a>Azure hálózati naplók

A hálózati naplózás és az Azure monitorozása átfogó, és két széles kategóriára terjed ki:

* [Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md): A forgatókönyv-alapú hálózati figyelés a Network Watcher szolgáltatásaiban érhető el. Ez a szolgáltatás magában foglalja a csomagok rögzítését, a következő ugrást, az IP-forgalom ellenőrzését, a biztonsági csoport nézetét, a NSG folyamat naplóit. A forgatókönyvek szintjének figyelése lehetővé teszi a hálózati erőforrások teljes körű megtekintését az egyes hálózati erőforrások figyelésével szemben.

* [Erőforrás-figyelés](../../network-watcher/network-watcher-monitoring-overview.md): Az erőforrás-szint figyelése négy funkciót, diagnosztikai naplókat, metrikákat, hibaelhárítást és erőforrás-állapotot tartalmaz. Ezek a szolgáltatások a hálózati erőforrás szintjén vannak felépítve.

![Azure hálózati naplók](./media/log-audit/azure-log-audit-fig4.png)

Network Watcher egy regionális szolgáltatás, amely lehetővé teszi az Azure-ban, a-ben és az-ban lévő hálózati forgatókönyvek szintjének figyelését és diagnosztizálását. A Network Watcher elérhető hálózati diagnosztikai és vizualizációs eszközök segítségével megismerheti, diagnosztizálhatja és elemezheti a hálózatát az Azure-ban.

### <a name="network-security-group-flow-logging"></a>Hálózati biztonsági csoport adatfolyamának naplózása

A [NSG flow-naplók](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) a Network Watcher funkciói, amelyek segítségével megtekintheti a bejövő és kimenő IP-forgalomra vonatkozó információkat egy NSG keresztül. A rendszer JSON formátumban írja be ezeket a folyamatokat, és megjeleníti a következőket:
* A kimenő és a bejövő folyamatok egy-egy szabály alapján.
* Az a hálózati adapter, amelyre a folyamat vonatkozik.
* 5 rekordos információ a folyamatról: a forrás vagy a cél IP-címe, a forrás vagy a cél port, valamint a protokoll.
* Azt jelzi, hogy a forgalom engedélyezett vagy tiltott.

Bár a flow naplózza a célként megadott NSG, azok nem ugyanúgy jelennek meg, mint a többi napló. A flow-naplók tárolása csak egy Storage-fiókon belül történik.

A többi naplóban megjelenő adatmegőrzési szabályzatok a folyamat naplófájljaira is érvényesek. A naplók adatmegőrzési szabályzata 1 nap és 365 nap között állítható be. Ha nincs beállítva adatmegőrzési szabály, a naplók megőrzése korlátlan időre szól.

**Diagnosztikai naplók**

Az időszakos és spontán eseményeket a hálózati erőforrások és a bejelentkezett tárolási fiókok hozzák létre, és az esemény-központba vagy Azure Monitor naplókba küldik. A naplók betekintést nyújtanak egy erőforrás állapotára. Ezek az eszközök, például a Power BI és a Azure Monitor naplókban is megtekinthetők. A diagnosztikai naplók megtekintésével kapcsolatos további információkért lásd: [Azure monitor naplók](../../azure-monitor/insights/azure-networking-analytics.md).

![Diagnosztikai naplók](./media/log-audit/azure-log-audit-fig5.png)

A diagnosztikai naplók a [Load Balancer](../../load-balancer/load-balancer-monitor-log.md), a [hálózati biztonsági csoportok](../../virtual-network/virtual-network-nsg-manage-log.md), az útvonalak és a [Application Gateway](../../application-gateway/application-gateway-diagnostics.md)számára érhetők el.

A Network Watcher diagnosztikai naplókat jelenít meg. Ez a nézet a diagnosztikai naplózást támogató összes hálózati erőforrást tartalmazza. Ebből a nézetből kényelmesen és gyorsan engedélyezheti és tilthatja le a hálózati erőforrásokat.


A korábban említett naplózási képességek mellett Network Watcher jelenleg a következő képességekkel rendelkezik:
- [Topológia](../../network-watcher/view-network-topology.md): Olyan hálózati szintű nézetet biztosít, amely megjeleníti az erőforráscsoport hálózati erőforrásai közötti különböző kapcsolatokat és társításokat.

- [Változó csomagok rögzítése](../../network-watcher/network-watcher-packet-capture-overview.md): Rögzíti a csomagokat a virtuális gépekről és azokból. A speciális szűrési beállítások és a finomhangolási vezérlők, például az idő-és méret korlátozási beállításai, a sokoldalúságot biztosítják. A csomagok tárolása blob-tárolóban vagy a helyi lemezen *. Cap* fájlformátumban lehetséges.

- [IP-forgalom ellenőrzése](../../network-watcher/network-watcher-ip-flow-verify-overview.md): Ellenőrzi, hogy a csomag engedélyezett vagy megtagadott-e a flow-információk 5 rekordos csomagjainak paraméterei (azaz a cél IP-címe, a forrás IP-címe, a célport, a forrásport és a protokoll) alapján. Ha egy biztonsági csoport megtagadja a csomagot, a rendszer visszaadja a csomagot és a csoportot, amely megtagadja a csomagokat.

- [Következő ugrás](../../network-watcher/network-watcher-next-hop-overview.md): Meghatározza a következő ugrást az Azure hálózati hálóban továbbított csomagok esetében, hogy a nem konfigurált, felhasználó által megadott útvonalakat is diagnosztizálja.

- [Biztonsági csoport nézete](../../network-watcher/network-watcher-security-group-view-overview.md): A virtuális gépen alkalmazott hatályos és alkalmazott biztonsági szabályok beolvasása.

- [Virtuális hálózati átjáró és a kapcsolatok hibaelhárítása](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): Segítséget nyújt a virtuális hálózati átjárók és a kapcsolatok hibakeresésében.

- [Hálózati előfizetés korlátai](../../network-watcher/network-watcher-monitoring-overview.md): Lehetővé teszi a hálózati erőforrás-használat korlátozásait.

### <a name="application-insights"></a>Application Insights

Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) egy bővíthető APM-szolgáltatás több platformon futó webes fejlesztőknek. Az élő webalkalmazások figyelésére használható. Automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket. Hatékony elemzőeszközöket tartalmaz, amelyek segítenek a problémák felismerésében, és annak a megértésében, hogy a felhasználók mire használják ténylegesen az alkalmazást.

Application Insights úgy lett kialakítva, hogy segítsen folyamatosan javítani a teljesítményt és a használhatóságot.

Számos platformon használható, többek között a .NET, a Node. js és a Java EE, függetlenül attól, hogy a helyszínen vagy a felhőben vannak tárolva. Integrálva van a DevOps-folyamattal, és a különböző fejlesztői eszközökkel rendelkező összekötő pontokat tartalmaz.

![Application Insights diagram](./media/log-audit/azure-log-audit-fig6.png)

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
|[Alkalmazás-hozzárendelés](../../azure-monitor/app/app-map.md)|Az alkalmazás összetevői, alapvető metrikákkal és riasztásokkal.|
|[Diagnosztikai keresés a példányok adatait](../../azure-monitor/app/diagnostic-search.md)| Események keresése és szűrése, például kérések, kivételek, függőségi hívások, naplókivonatok és lapmegtekintések.|
|[Metrikaböngésző összesített adatokat](../../azure-monitor/app/metrics-explorer.md)|Összesített adatok – például kérés- és hibaarányok, valamint kivételek, válaszidők és lapbetöltési idők – böngészése, szűrése és szegmentálása.|
|[Irányítópultok](../../azure-monitor/app/overview-dashboard.md)|Különböző erőforrásokból származó adatokat fűzhet össze és oszthat meg másokkal. Kiváló megoldás több összetevőből álló alkalmazások, valamint csoportszobában való folyamatos megjelenítés esetén.|
|[Live Metrics Stream](../../azure-monitor/app/live-stream.md)|Új buildverzió telepítésekor közel valós idejű teljesítménymutatókat figyelhet, és meggyőződhet arról, hogy minden megfelelő módon működik-e.|
|[Elemzés](../../azure-monitor/app/analytics.md)|A hatékony lekérdezési nyelvnek köszönhetően válaszokat kaphat az alkalmazás teljesítményére és használatára vonatkozó legégetőbb kérdésekre.|
|[Automatikus és manuális riasztások](../../azure-monitor/app/alerts.md)|Az automatikus riasztások alkalmazkodnak az alkalmazás normál telemetria, és akkor aktiválódik, ha a szokásos mintázaton kívül van valami. Riasztásokat állíthat be az egyéni vagy normál metrikák adott szintjeire is.|
|[Visual Studio](../../azure-monitor/app/visual-studio.md)|A kódban megtekintheti a teljesítményadatokat. A hívásláncokból a kódba ugorhat.|
|[Power BI](../../azure-monitor/app/export-power-bi.md)|Integrálhatja a használati metrikákat más üzleti intelligenciával.|
|[REST API](https://dev.applicationinsights.io/)|Kód írásával lekérdezéseket futtathat a metrikákhoz és a nyers adatokhoz kapcsolódóan.|
|[Folyamatos exportálás](../../azure-monitor/app/export-telemetry.md)|Nyers adattárolás tömeges exportálása a tárolóba a megérkezéskor.|

### <a name="azure-security-center-alerts"></a>Riasztások Azure Security Center

Azure Security Center fenyegetések észlelése úgy működik, hogy automatikusan összegyűjti az Azure-erőforrások, a hálózat és a csatlakoztatott partneri megoldások biztonsági információit. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket. A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására. További információ: [Azure Security Center](../../security-center/security-center-intro.md).

![Azure Security Center diagram](./media/log-audit/azure-log-audit-fig7.png)

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. Áttörést jelent a nagyméretű adatmennyiség és a [gépi tanulási](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák esetében, hogy kiértékelje az eseményeket a teljes felhőalapú hálóban. Ily módon észleli azokat a fenyegetéseket, amelyek a manuális megközelítések és a támadások alakulásának előrejelzésével nem lennének azonosíthatók. Ezek a biztonsági elemzések a következők:

* **Integrált veszélyforrások felderítése**: A Microsoft-termékek és-szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft Security Response Center (MSRC) és a külső hírcsatornák globális fenyegetési intelligenciának alkalmazásával ismert hibás szereplőkre keres.

* **Viselkedési elemzés**: Az ismert mintákat alkalmazza a kártékony viselkedés felderítésére.

* **Anomáliák észlelése**: Statisztikai profilkészítést használ egy korábbi alapterv létrehozásához. Jelzi a meghatározott alapkonfigurációktól való olyan eltéréseket, amelyek potenciális támadásként értelmezhetők.

Számos biztonsági művelet és incidens-válasz egy SIEM-megoldásra támaszkodik a osztályozásakor és a biztonsági riasztások kivizsgálása szempontjából. A Azure Log Integration segítségével szinkronizálhatja Security Center riasztásokat és a virtuális gépek biztonsági eseményeit, amelyeket az Azure Diagnostics és a naplók összegyűjtenek, a Azure Monitor-naplók vagy SIEM-megoldás közel valós időben.

## <a name="azure-monitor-logs"></a>Azure Monitor-naplók

Azure Monitor a naplók egy Azure-szolgáltatás, amely segít összegyűjteni és elemezni az erőforrások által a felhőben és a helyszíni környezetekben létrehozott adatokat. Az integrált keresés és az egyéni irányítópultok segítségével valós idejű elemzéseket biztosít, így a fizikai helyüktől függetlenül akár több millió rekordot is elemezheti az összes munkaterhelésben és kiszolgálón.

![Azure Monitor naplók diagramja](./media/log-audit/azure-log-audit-fig8.png)

Azure Monitor naplók középpontjában az Azure-ban üzemeltetett Log Analytics munkaterület. Azure Monitor naplók adatokat gyűjtenek a munkaterületen a csatlakoztatott forrásokból az adatforrások konfigurálásával és a megoldások az előfizetéshez való hozzáadásával. Az egyes adatforrások és megoldások különböző bejegyzéstípusokat hoznak létre, amelyek mindegyike saját tulajdonságokkal rendelkezik. A forrásokat és a megoldásokat azonban továbbra is elemezheti a munkaterületre irányuló lekérdezésekben. Ez a funkció lehetővé teszi, hogy ugyanazokat az eszközöket és metódusokat használja a különböző forrásokból gyűjtött különféle adatokkal való munkához.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A csatlakoztatott források azok a számítógépek és egyéb erőforrások, amelyek a Azure Monitor naplók által gyűjtött adatokat eredményezik. A források tartalmazhatják azokat a [Windows](../../log-analytics/log-analytics-agent-windows.md) -és [Linux](../../log-analytics/log-analytics-quick-collect-linux-computer.md) -számítógépeken telepített ügynököket, amelyek közvetlenül vagy [egy csatlakoztatott System Center Operations Manager felügyeleti csoportban](../../azure-monitor/platform/om-agents.md)lévő ügynökökkel csatlakoznak. Azure Monitor naplók adatokat is gyűjthetnek egy [Azure Storage](../../azure-monitor/platform/diagnostic-logs-stream-log-store.md)-fiókból.

[](../../azure-monitor/platform/agent-data-sources.md) Az adatforrások az egyes csatlakoztatott forrásokból gyűjtött különböző típusú adatok. A források a [Windows](../../azure-monitor/platform/data-sources-windows-events.md) -és Linux-ügynököktől származó eseményeket és [teljesítményadatokat](../../azure-monitor/platform/data-sources-performance-counters.md) , valamint az [IIS](../../azure-monitor/platform/data-sources-iis-logs.md) -naplókat és az [egyéni szöveges naplókat](../../azure-monitor/platform/data-sources-custom-logs.md)is tartalmazzák. Konfigurálhatja az összegyűjteni kívánt egyes adatforrásokat, és a konfiguráció automatikusan érvénybe lép minden egyes csatlakoztatott forráson.

[Az Azure-szolgáltatásokhoz négy módon gyűjthet naplókat és mérőszámokat](../../azure-monitor/platform/diagnostic-logs-stream-log-store.md):

* Azure Diagnostics a közvetlen Azure Monitor naplókba (**diagnosztika** a következő táblázatban)

* Azure Diagnostics az Azure Storage-ba Azure Monitor naplókba (**tárolás** a következő táblázatban)

* Összekötők az Azure-szolgáltatásokhoz (**összekötő** a következő táblázatban)

* Az adatok gyűjtésére és közzétételére szolgáló parancsfájlok Azure Monitor naplókba (a következő táblázatban szereplő üres cellák és a nem felsorolt szolgáltatások esetében)

| Szolgáltatás | Erőforrás típusa | Logs | Mérőszámok | Megoldás |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnosztika|Diagnosztika|    [Azure-alkalmazás](../../azure-monitor/insights/azure-networking-analytics.md) [Átjáró-elemzés](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor)|
|Application Insights||     Összekötő|  Összekötő|  [Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Összekötő (előzetes verzió)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Fiókok Azure Automation| Microsoft.Automation/<br>AutomationAccounts|    Diagnosztika||       [További információ](../../automation/automation-manage-send-joblogs-log-analytics.md)|
|Fiókok Azure Batch|  Microsoft.Batch/<br>batchAccounts|  Diagnosztika|    Diagnosztika||
|Klasszikus a cloud services||       Storage||       [További információ](../../azure-monitor/platform/azure-storage-iis-table.md)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>fiókok|       Diagnosztika|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>fiókok|   Diagnosztika|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>fiókok|   Diagnosztika|||
|Azure Event hub-névtér| Microsoft.EventHub/<br>névterek|  Diagnosztika|    Diagnosztika||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnosztika||
|Azure Key Vault|   Microsoft.KeyVault/<br>boltívek|  Diagnosztika  || [Key Vault-elemzés](../../azure-monitor/insights/azure-key-vault.md)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnosztika|||
|Azure Logic Apps|  Microsoft.Logic/<br>munkafolyamatok|  Diagnosztika|    Diagnosztika||
||Microsoft.Logic/<br>integrationAccounts||||
|Network Security Groups (Hálózati biztonsági csoportok)|   Microsoft.Network/<br>networksecuritygroups|Diagnosztika||   [Azure hálózati biztonsági csoport elemzése](../../azure-monitor/insights/azure-networking-analytics.md#azure-application-gateway-and-network-security-group-analytics)|
|Helyreállítási tárak|   Microsoft.RecoveryServices/<br>boltívek|||[Az Azure Recovery Services-Analytics (előzetes verzió)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Szolgáltatások keresése|   Microsoft.Search/<br>searchServices|    Diagnosztika|    Diagnosztika||
|Service Bus-névtér| Microsoft.ServiceBus/<br>névterek|    Diagnosztika|Diagnosztika|    [Service Bus-elemzés (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric-elemzés (előzetes verzió)](../../service-fabric/service-fabric-diagnostics-oms-setup.md)|
|SQL (v12)| Microsoft.Sql/<br>kiszolgálók<br>databases||       Diagnosztika||
||Microsoft.Sql/<br>kiszolgálók<br>elasticPools||||
|Storage|||         Szkript| [Az Azure Storage Analytics (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure Virtual Machines|    Microsoft.Compute/<br>virtualMachines|  Mellék|  Mellék||
||||Diagnosztika||
|Virtuálisgép-méretezési csoportok|    Microsoft.Compute/<br>virtualMachines    ||Diagnosztika||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Webkiszolgáló-farmok|Microsoft.Web/<br>kiszolgálófarmok||   Diagnosztika
|webhely|  Microsoft.Web/<br>webhelyek ||      Diagnosztika|    [További információ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>helyek<br>bővítőhely||||


## <a name="log-integration-with-on-premises-siem-systems"></a>A helyszíni SIEM-rendszerekkel való integráció naplózása

A Azure Log Integration segítségével integrálhatja az Azure-erőforrásokból származó nyers naplókat a helyszíni SIEM-rendszerével (biztonsági információk és eseménykezelő rendszer). A AzLog letöltése a 2018. június 27-én le lett tiltva. Útmutatás a további lépések áttekintéséhez: az [Azure monitor használata az Siem-eszközökkel való integráláshoz](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

![Log integrációs diagram](./media/log-audit/azure-log-audit-fig9.png)

A log Integration összegyűjti az Azure Diagnostics szolgáltatást a Windows rendszerű virtuális gépekről, az Azure-Tevékenységnaplók, a Azure Security Center riasztások és az Azure erőforrás-szolgáltató naplóiból. Ez az integráció egy egységes irányítópultot biztosít az összes eszközhöz, akár a helyszínen, akár a felhőben, így összesítheti, korrelálhatja, elemezheti és riasztást készíthet a biztonsági eseményekről.

A log Integration jelenleg a Windows rendszerű virtuális gépekről az Azure-előfizetéssel, Azure Security Center riasztásokkal, az Azure Diagnostics-naplókkal és az Azure AD-naplókkal való integrációját támogatja.

| Napló típusa | A JSON-t támogató naplók Azure Monitor (splunk, ArcSight és IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Azure AD-naplók|   Igen|
|Tevékenységnaplók| Igen|
|Riasztások Security Center |Igen|
|Diagnosztikai naplók (erőforrás-naplók)|  Igen|
|VIRTUÁLIS gépek naplói|   Igen, továbbított eseményeken keresztül, és nem a JSON-n keresztül|

[A Azure log Integration első lépései](azure-log-integration-get-started.md): Ez az oktatóanyag végigvezeti a Azure Log Integration telepítésének és a naplók Azure Storage-ba való integrálásának, az Azure-Tevékenységnaplók, a Azure Security Center riasztások és az Azure AD-naplók integrálásának lépésein.

Az SIEM integrációs forgatókönyvei:

* [Partneri konfiguráció lépései](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Ez a blogbejegyzés bemutatja, hogyan konfigurálhatja a Azure Log Integrationt a partneri megoldások splunk, a HP ArcSight és az IBM QRadar való együttműködéshez.

* [Azure log Integration gyakori kérdések](azure-log-integration-faq.md): Ez a cikk a Azure Log Integrationával kapcsolatos kérdésekre ad választ.

* [Security Center riasztások integrálása Azure log Integrationekkel](../../security-center/security-center-export-data-to-siem.md): Ez a cikk azt ismerteti, hogyan lehet szinkronizálni Security Center riasztásokat, az Azure Diagnostics-naplók által gyűjtött virtuális gépek biztonsági eseményeit, valamint az Azure-naplókat a Azure Monitor-naplókkal vagy SIEM-megoldásokkal.

## <a name="next-steps"></a>További lépések

- [Naplózás és naplózás](management-monitoring-overview.md): Az adatvédelmet úgy biztosíthatja, hogy megtartja a láthatóságot, és gyorsan reagál a biztonsági riasztásokra.

- [Biztonsági naplózás és naplózási naplók az Azure-on belül](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Ezen beállítások betartatásával győződjön meg arról, hogy az Azure-példányok megfelelő biztonsági és naplózási naplókat gyűjtenek.

- [Webhelycsoport naplózási beállításainak konfigurálása](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Ha Ön webhelycsoport-rendszergazda, kérje le az egyes felhasználói műveletek előzményeit, valamint az adott dátumtartomány által végrehajtott műveletek előzményeit. 

- [Keresse meg a naplót az Office 365 biztonsági és megfelelőségi központban](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Az Office 365 Biztonsági és megfelelőségi központ használatával kereshet az egyesített naplóban, és megtekintheti az Office 365-szervezet felhasználói és rendszergazdai tevékenységeit.


