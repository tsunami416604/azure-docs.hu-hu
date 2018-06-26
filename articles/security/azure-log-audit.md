---
title: Azure-naplózás és a naplózás |} Microsoft Docs
description: További tudnivalók arról, hogyan használja a naplózási adatokat az alkalmazással kapcsolatos részletes dcu.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: e4144ca0d87abda3d9f8de47e56af59d0e4af312
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938366"
---
# <a name="azure-logging-and-auditing"></a>Azure-naplózás

Azure konfigurálható biztonsági naplózás és a naplózási beállításokat, hogy könnyen azonosíthassa a biztonsági házirendeket és mechanizmusokat hiányosságait széles köréről biztosít. A cikkből megtudhatja, létrehozásakor, összegyűjtése és elemzése az Azure-platformon futó szolgáltatások biztonsági naplókat.

> [!Note]
> Bizonyos ajánlások ebben a cikkben előfordulhat, hogy megnövekedett adat-, hálózati vagy számítási erőforrások használatát eredményezi, és növelheti a licencek vagy előfizetések költségeit.

## <a name="types-of-logs-in-azure"></a>Az Azure-ban naplók típusait
A felhőalapú alkalmazásokhoz összetettek, sok áthelyezése alkotórészek is. Naplók adja meg az adatok megőrzése érdekében az alkalmazások lépéseivel. Naplók segítséget nyújt a korábbi hibákat hibaelhárítása, illetve hogy megakadályozzák a lehetőségeket kínál a meglévők közül. És segíthetnek az alkalmazások teljesítményéről vagy karbantartási követelmények, vagy, amelyek egyébként kézi beavatkozás műveletek automatizálásához.

Az Azure naplói szerint vannak kategóriába be a következő esetében:
* **Ellenőrzés/management-naplók** Azure Resource Manager LÉTREHOZNI, UPDATE és DELETE műveletek ismertetik. További információkért lásd: [Azure tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Adatok sík naplók** része az Azure erőforrás-használat, keletkező kapcsolatos adatok megadása. Az ilyen típusú napló többek között a Windows esemény rendszer, biztonság, és alkalmazás jelentkezik be egy virtuális gép (VM) és a [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) konfigurált Azure figyelő keresztül.

* **A feldolgozott események** elemzett eseményeket/riasztásokat feldolgozott kapcsolatos adatok megadása az Ön nevében. Példa ilyen típusú [Azure Security Center riasztásait](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) ahol [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) feldolgozása és az előfizetés elemzése és tömör biztonsági riasztások biztosít.

Az alábbi táblázat a legfontosabb típusú az Azure-ban elérhető naplók:

| Napló kategória | Napló típusa | Használat | Integráció |
| ------------ | -------- | ------ | ----------- |
|[Tevékenység-naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Az Azure Resource Manager-erőforrás vezérlő-vezérlősík események|   Az erőforrást az előfizetésében a végrehajtott műveletek betekintést nyújt.|    REST-API-t [Azure figyelője](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure-beli diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|A következő előfizetés erőforrásait Azure Resource Manager működésével kapcsolatos gyakori adatok|    Maga az erőforrás által végrehajtott műveletek betekintést nyújt.| Az Azure figyelő [adatfolyam](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Azure AD Reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Naplók és jelentések | Jelentések felhasználói bejelentkezési tevékenységek és a rendszer tevékenység szereplő felhasználók és csoportok kezelése.|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtuális gépek és felhőszolgáltatások](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-quick-collect-azurevm)|A Windows Eseménynapló szolgáltatás és a Linux rendszernaplójába bejegyzett|    Rendszeradatok és a virtuális gépek a naplózási adatokat rögzíti, és átviszi az adatok az Ön által választott tárolási figyelembe.|   Windows (Windows Azure diagnosztikai használatával [[ÜVEGVATTA](https://docs.microsoft.com/azure/azure-diagnostics)] tárolási) és az Azure-figyelő Linux|
|[Az Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Tárolási naplózási metrikák adatokat biztosít a tárfiók|Tárolni kívánt nyomkövetési kérelmek betekintést nyújt, elemzi a használati trendeket és diagnosztizálja a tárfiók problémákat.|   REST API-t vagy a [ügyféloldali kódtár](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Hálózati biztonsági csoport (NSG) folyamat Naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON formátumban, bejövő és kimenő forgalom szabály alapon jeleníti meg.|IP-bemenő és kimenő forgalom keresztül hálózati biztonsági csoport részletes információit jeleníti meg.|[Az Azure hálózati figyelőt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Naplók, kivételeket és egyéni diagnosztika|   Itt az alkalmazásteljesítmény-figyelés (APM) szolgáltatás webfejlesztőknek, több platformon.| REST API-t [a Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Adatok feldolgozása / biztonsági riasztások|    Azure Security Center riasztásait, Azure Naplóelemzés riasztások|   Biztosítja a biztonsági adatokat és a riasztásokat.|  REST API-k, JSON|

### <a name="activity-logs"></a>Tevékenységnaplók
[Az Azure tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) Észreveheti az olyan erőforrást az előfizetésében a végrehajtott műveletek. Tevékenységi naplóit volt korábbi nevén "naplófájlok" vagy "működési logs", mert a jelentendő [vezérlő-vezérlősík események](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) az előfizetésekhez. 

Tevékenység súgó meghatározhatja, hogy naplózza a "mi, ki, és mikor" az írási műveletek (Ez azt jelenti, PUT, POST és törlése). Tevékenység is naplózza a művelet és az egyéb kapcsolódó tulajdonságainak állapotának tisztában súgó. Tevékenység naplói nem tartalmazzák (GET) olvasási műveletek.

A cikkben PUT, POST és DELETE tekintse meg az írási műveleteket műveletnaplóban tartalmaz arról, hogy az erőforrások. Használhatja például a tevékenységi naplóit hiba található a problémák elhárításakor van, vagy hogyan a szervezet egy felhasználó módosította a következő erőforrás figyelésére.

![Tevékenység napló diagramja](./media/azure-log-audit/azure-log-audit-fig1.png)

Beolvasható események műveletnaplóban az Azure portál használatával [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), PowerShell-parancsmagok és [Azure figyelő REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Tevékenységi naplóit 19 napos az adatmegőrzés idővel rendelkeznek.

Egy tevékenység napló esemény integrációs feladatokhoz:

* [Hozzon létre egy e-mailben vagy webhook riasztást az tevékenység naplóesemény által elindított](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Az eseményközpontok felé adatfolyamként](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) egy külső szolgáltatás vagy az egyéni elemzési megoldások, például a Power bi szempontjából.

* Elemezze a Power bi segítségével a [Power bi tartalomcsomag](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Mentse a tárfiók archív vagy manuális ellenőrzést](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Napló-profilok segítségével megadhatja a megőrzési ideje (nap).

* Lekérdezi, és tekintse meg az Azure-portálon.

* Lekérdezése a PowerShell-parancsmag, az Azure parancssori felület vagy a REST API-t.

* A műveletnapló napló profilokkal való exportálás [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Használhatja a storage-fiókok vagy [event hub névtér](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) a rendszer ugyanazt az előfizetést, a napló van kibocsátó. A megfelelő személy, aki konfigurálja a beállítást kell [szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) mindkét előfizetéshez elérésére.

### <a name="azure-diagnostics-logs"></a>Azure-beli diagnosztikai naplók
Az Azure diagnosztikai naplók gazdag, gyakori, hogy az erőforrás működésével kapcsolatos adatokat az erőforrás által kibocsátott. Ezek a naplók tartalmának erőforrástípusok szerint változik. Például [Windows rendszer-eseménynaplói](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) diagnosztikai naplók kategória vannak a virtuális gépekhez, és [blob, table és a várólista naplók](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) kategóriája diagnosztikai naplókat a storage-fiókok. Diagnosztikai naplók eltérnek a tevékenységi naplóit, amely a erőforrást az előfizetésében végrehajtott műveletek betekintést biztosít.

![Az Azure diagnosztikai naplók diagramok](./media/azure-log-audit/azure-log-audit-fig2.png)

Az Azure diagnosztikai naplók több konfigurációs beállítások, például az Azure-portálon PowerShell, az Azure parancssori felület vagy a REST API-t kínál.

**Integrációs feladatokhoz**

* Mentse azokat egy [tárfiók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) naplózási vagy manuális ellenőrzést. A diagnosztikai beállítások segítségével megadhatja a megőrzési ideje (nap).

* [Az adatfolyamot őket az event hubs a](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) szempontjából egy külső szolgáltatás vagy az egyéni elemzési megoldások, például a [Power bi](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Elemezheti őket a [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Támogatott szolgáltatások, a diagnosztikai naplók és a támogatott kategóriák erőforrásonként séma írja be a következőket:**


| Szolgáltatás | Séma- és dokumentáció | Erőforrás típusa | Kategória |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Naplóelemzési terheléselosztóhoz (előzetes verzió)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers| LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Network Security Groups (Hálózati biztonsági csoportok)|[A hálózati biztonsági csoportok Naplóelemzési](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Az Alkalmazásátjáró diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Key Vault naplóinak](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Engedélyezése és a keresési forgalom Analytics használata](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[A Data Lake Store belépési diagnosztikai naplók](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Naplózás<br>Kérelmek|
|Azure Data Lake Analytics|[A Data Lake Analytics belépési diagnosztikai naplók](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Naplózás<br>Kérelmek|
|Azure Logic Apps|[Logic Apps B2B egyéni követési séma](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch diagnosztikai naplók](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Az Azure Automation szolgáltatáshoz](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Eseménynaplók hubok diagnosztika](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Feladat diagnosztikai naplók](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Futtatási<br>Szerzői műveletek|
|Azure Service Bus|[A Service Bus diagnosztikai naplók](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Jelentéskészítés az Azure Active Directoryban
Azure Active Directory (Azure AD) biztonsági, naplózási és tevékenységjelentéseket biztosít a felhasználó címtár tartalmazza. A [az Azure AD naplózási jelentés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) könnyebb legyen azonosítani, hogy a felhasználó az Azure AD-példányban történt a privilegizált műveletekhez. Kiemelt műveletek közé tartoznak az illetéktelen módosítások (például a szerepkör létrehozása vagy a jelszó alaphelyzetbe állítása), változó Csoportházirend konfigurálásának (például jelszóházirendek) vagy a könyvtárat (például tartomány-összevonási beállítások módosításait) konfigurációs módosításait.

A jelentések az esemény nevét, a művelet, a cél erőforráson, a módosítás dátuma és időpontja (UTC) által érintett felhasználó adja meg a naplórekordot. Felhasználók naplózási események lekérhesse az Azure AD használatával a [Azure-portálon](https://portal.azure.com/)leírtak szerint [a naplófájlok megtekintése](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

A befoglalt jelentések az alábbi táblázatban felsorolt:

| Biztonsági jelentések | Tevékenységjelentések | Naplózási jelentések |
| :--------------- | :--------------- | :------------ |
|Bejelentkezések ismeretlen forrásokról| Alkalmazáshasználat: összegzés| Címtárnaplózási jelentés|
|Több hibát követő bejelentkezések|  Alkalmazáshasználat: részletes||
|Bejelentkezések különböző földrajzi régiókból|    Alkalmazás irányítópultja||
|Bejelentkezések gyanús tevékenységeket mutató IP-címekkel|   Alkalmazás-kiépítési hibák||
|Rendszertelen bejelentkezési tevékenység|    Egyéni felhasználói eszközök||
|Bejelentkezések potenciálisan fertőzött eszközökről|   Egyéni felhasználói tevékenység||
|Rendellenes bejelentkezési tevékenységet mutató felhasználók| Csoporttevékenység-jelentés||
||Jelszó-változtatási regisztrációs Tevékenységjelentés||
||Jelszó-visszaállítási tevékenység|||

A jelentésekben megjelenő adatok az alkalmazások, például a biztonsági adatai és az esemény felügyeleti SIEM-rendszerekről, naplózási és üzleti intelligencia eszközök hasznos lehet. Az Azure AD-jelentéskészítés API-k REST-alapú API-kon keresztül biztosítják az adatok szoftveres hozzáférését. Ezek hívása [API-k](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) különböző programozási nyelveket és eszközökön.

Az Azure AD naplózási jelentés események kerülnek 180 napig tart.

> [!Note]
> A jelentés adatmegőrzési kapcsolatos további információkért lásd: [az Azure AD-jelentés adatmegőrzési](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Ha érdekli, megtartja a naplózási események hosszabb, a Reporting API használatával rendszeresen lekéréses [naplózása](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) külön adattárba.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Virtuális gép naplófájlokat, amelyek Azure Diagnostics
[Az Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics) a képesség, amely lehetővé teszi a telepített alkalmazás diagnosztikai adatok gyűjtésére Azure-ban. A diagnosztika bővítmény bármelyik forrásokból is használhatja. A rendszer jelenleg támogatott [Azure cloud service webes és feldolgozói szerepkörök](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Virtuális gép naplófájlokat, amelyek Azure Diagnostics](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineshttpsazuremicrosoftcomdocumentationlearning-pathsvirtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Az Azure virtuális gépek](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) Microsoft Windows rendszerű és [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

A virtuális gépen Azure Diagnostics engedélyezheti a következő módszerek valamelyikével:

* [Visual Studio segítségével az Azure virtuális gépek követése](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Állítsa be az Azure Diagnostics távolról Azure virtuális géphez](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Állítsa be az Azure virtuális gépeken diagnosztika a PowerShell használatával](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Figyelés és diagnosztika Windows virtuális gép létrehozása Azure Resource Manager-sablon használatával](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics
[Az Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) naplózza, és adja meg a tárfiók metrikák adatait. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat. Storage Analytics naplózási érhető el a [Azure Blob, az Azure Queue és az Azure Table storage szolgáltatások](https://docs.microsoft.com/azure/storage/storage-introduction). Storage Analytics egy társzolgáltatás sikeres és sikertelen kérelmekkel kapcsolatos részletes információkat naplózza.

Ezeket az információkat egyes kérelmeket a figyelheti és egy tárolási szolgáltatással kapcsolatos problémák diagnosztizálásához használható. Kérelmek is be vannak jelentkezve a legjobb alapul. Naplóbejegyzéseket jönnek létre, csak ha nincs a szolgáltatási végpont ellen. Például ha a tárfiók tevékenység a blob végpontja, de nem a tábla- vagy várólista végpont, csak a Blob storage szolgáltatás tartozó naplók jönnek létre.

Tárolási analitika használatához engedélyezze külön-külön minden egyes figyelni kívánt szolgáltatás. Azt a engedélyezheti a [Azure-portálon](https://portal.azure.com/). További információkért lásd: [figyelése az Azure portálon tárfiók](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Tárolási analitika programozott módon a REST API vagy ügyfélkódtár keresztül is engedélyezheti. A szolgáltatás tulajdonságainak beállítása művelet segítségével tárolási analitika külön-külön engedélyezni az egyes szolgáltatásokhoz.

Az összesített adatok tárolását a jól ismert blob (naplózás), és jól ismert táblában (a metrika), amelynek a Blob storage szolgáltatás és a Table storage szolgáltatás API-k eléréséhez.

Tárolási analitika 20-terabájt (TB) maximális rendelkezzen, amely független a tárfiók teljes korlátját tárolt adatok mennyisége. Minden naplója [blokkblobokat](https://docs.microsoft.com/azure/storage/storage-analytics) $logs nevű tároló, amely automatikusan megtörténik a tárolási analitika engedélyezte a tárfiók.

> [!Note]
> * További információ a számlázással és az adatmegőrzési házirendek: [tárolási analitika és számlázási](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Információ a tárfiókok korlátairól további információkért lásd: [Azure Storage méretezhetőségi és Teljesítménycélok](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Tárolási analitika naplózza a következő típusú hitelesített vagy névtelen kérelmeket:

| Hitelesített  | Névtelen|
| :------------- | :-------------|
| Sikeres kérelmei | Sikeres kérelmei |
|Sikertelen kérelmek, beleértve az időtúllépés, a sávszélesség-szabályozás, hálózati, engedélyezési és egyéb hibák | A közös hozzáférésű jogosultságkód, például és a sikertelen kérelmek használatával |
| A közös hozzáférésű jogosultságkód, például és a sikertelen kérelmek használatával |Az ügyfél és kiszolgáló egyaránt időtúllépési hibák |
|   Az analitikai adatok kérelmek |    Sikertelen GET kérelmek 304 (nem módosított). Hibakód: |
| Tárolási analitika, például a napló létrehozásakor vagy törlésekor, kérelmét a rendszer nem naplózza. A naplózott adatok teljes listáját részletes ismertetését lásd: [tárolási analitika naplózott műveletek és az állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) és [tárolási analitika naplóformátumban](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Minden más sikertelen névtelen kérelmek nem naplózza a rendszer. A naplózott adatok teljes listáját részletes ismertetését lásd: [tárolási analitika naplózott műveletek és az állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) és [tárolási analitika naplóformátumban](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Az Azure hálózati naplók
Hálózati naplózás és figyelés az Azure-ban átfogó, és ismerteti a két kategóriába sorolhatók:

* [Hálózati figyelő](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher): forgatókönyv-alapú hálózatfigyelési által biztosított szolgáltatások a hálózati figyelőt. A szolgáltatás része a csomagrögzítéssel, a következő ugrás, az IP-adatfolyam győződjön meg arról, biztonsági csoport megtekintése, NSG folyamata naplókat. Forgatókönyv szintű figyelési jeleníti meg egy végpontok közötti hálózati erőforrások ellentétben egyes hálózati erőforrás-figyelése.

* [Erőforrás-figyelés](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring): erőforrás-szintű figyelése magában foglalja, négy szolgáltatások diagnosztikai naplók, metrikák, hibaelhárítási és erőforrás állapota. Ezek a szolgáltatások a hálózati erőforrás szintjén épülnek.

![Az Azure hálózati naplók](./media/azure-log-audit/azure-log-audit-fig4.png)

Hálózati figyelőt olyan regionális szolgáltatás, amely lehetővé teszi, hogy figyelése és diagnosztizálása szintjén feltételek egy hálózati forgatókönyv, hogy, és az Azure-ból. Hálózati diagnosztika és a képi megjelenítés eszközök is elérhetők a hálózati figyelőt segítenek megérteni, diagnosztizálása és információt kaphat a hálózathoz, az Azure-ban.

### <a name="network-security-group-flow-logging"></a>Hálózati biztonsági csoport folyamata naplózás

[NSG folyamata naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) szolgáltatása, tekintse meg IP-bemenő és kimenő forgalom keresztül egy NSG-t használó hálózati figyelőt. A folyamat naplók JSON formátumban vannak megírva, és megjelenítése:
* Kimenő és bejövő forgalom szabály alapon.
* A hálózati adapterre alkalmazza a folyamatot.
* a folyamat 5 rekordos információ: a forrás vagy a cél IP-, a cél- és port és a protokoll.
* Hogy a forgalom lett engedélyezett vagy megtagadott.

Bár a folyamat célja NSG-ket naplózza, akkor nem jelennek ugyanúgy, mint a többi naplófájlt. Attribútumfolyam naplók csak egy tárfiókon belül tárolják.

Az azonos adatmegőrzési, amely a többi naplófájlt láthatók folyamata naplók vonatkozik. Naplók beállítható az 1 nap – 365 nap adatmegőrzési rendelkezik. Ha nincs beállítva adatmegőrzési szabály, a naplók megőrzése korlátlan időre szól.

**Diagnosztikai naplók**

Rendszeres és önkéntes események hálózati erőforrások által létrehozott és bejelentkezett a storage-fiókok, és az event hubs vagy Naplóelemzési küld. A naplók erőforrás állapotának betekintést. Azokat az eszközöket, például a Power BI és a Naplóelemzési lehet megtekinteni. Diagnosztikai naplók megtekintéséhez, lásd: [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Diagnosztikai naplók](./media/azure-log-audit/azure-log-audit-fig5.png)

Diagnosztikai naplók érhetők el [terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), útvonalakat, és [Alkalmazásátjáró](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Hálózati figyelőt biztosít a diagnosztikai naplók megtekintése. Ez a nézet diagnosztikai naplózás támogató minden hálózati erőforrások tartalmazza. Ebben a nézetben engedélyezése, és tiltsa le a hálózati erőforrások egyszerűen és gyorsan.


A korábban említett naplózási képességek mellett a hálózati figyelőt jelenleg a következő képességekkel rendelkezik:
- [Topológia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): a különböző csatlakozás és a hálózati erőforrások az erőforráscsoportban egymáshoz rendelését megjelenítő hálózati szintű nézetét jeleníti meg.

- [Változó csomagrögzítéssel](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): mindkét virtuális gép csomagadatok rögzíti. Sokoldalúsága speciális szűrési beállítások és finomhangolási vezérlők, például idő - és méretének korlátozása beállításait adja meg. A csomagadatok a blob-tárolóban, vagy a helyi lemezen tárolható *.cap* fájlformátum.

* [IP-adatfolyam ellenőrzési](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): ellenőrzi, hogy egy csomag engedélyezett vagy megtagadott folyamata adatokat 5 rekordos csomag paraméterek (Ez azt jelenti, cél IP-cím forrás IP-címe, Célport, Forrásport és protokoll) alapján. Ha a csomag megtagadta a biztonsági csoport, a szabály és a csoportot, amely a csomag megtagadva ad vissza.

* [Következő Ugrás](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): meghatározza, hogy a következő ugrás a csomagok továbbítása az Azure-hálózat hálóban, így felderítheti az esetleges nincs megfelelően konfigurálva a felhasználó által definiált útvonalak.

* [Biztonsági csoport megtekintése](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): lekérdezi a hatékony és alkalmazott biztonsági szabályokat, amelyek érvényesek a virtuális gép.

* [Virtuális hálózati átjáró és a kapcsolat hibaelhárítási](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): virtuális hálózati átjárók és kapcsolatok kapcsolatos hibaelhárítás elősegítéséhez.

* [Előfizetési korlátozásait a hálózati](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits): lehetővé teszi a hálózati erőforrás-használati korlátozások megtekintéséhez.

### <a name="application-insights"></a>Application Insights

[Az Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető APM szolgáltatás webfejlesztőknek, több platformon. Segítségével élő webalkalmazások figyeléséhez. Automatikusan észleli a teljesítményanomáliákat. Hatékony elemzőeszközöket tartalmaz, amelyek segítenek a problémák felismerésében, és annak a megértésében, hogy a felhasználók mire használják ténylegesen az alkalmazást.

Célja, hogy az Application Insights segítségével folyamatosan a teljesítményük és használhatóságuk javításában.

Az alkalmazások számos különböző platformokról, többek között a .NET, Node.js és J2EE, hogy ezek a helyben tárolt vagy a felhőben működik. Integrálható a DevOps folyamat, és rendelkezik a csatlakozási pontok és a különböző fejlesztői eszközök.

![Application Insights diagramja](./media/azure-log-audit/azure-log-audit-fig6.png)

A fejlesztőcsapatoknak készült Application Insights segít az adott alkalmazás teljesítményének megismerésében, valamint abban, hogy a felhasználók miként használják az alkalmazást. A szolgáltatás az alábbiakat figyeli:

* **Kérelem sebességét, a válaszidőt és a hiba díjszabás**: megtudhatja, mely lapok töltődnek legnépszerűbb, mely napszakokban, és ahol a felhasználók is. Megtekintheti, hogy mely lapok teljesítenek a legjobban. Ha a válaszidő és a hiba díjszabás Ugrás magas, ha nincsenek további kérelmeket, lehetséges, hogy resourcing probléma.

* **Függőség sebességét, a válaszidőt és a hiba díjszabás**: megtudhatja, hogy külső szolgáltatások lassítja meg.

* **Kivételek**: elemezheti az összesített statisztikák, vagy válasszon olyan specifikus példányai, és elemezze a veremkivonatot és a kapcsolódó kérések. A kiszolgálói és a böngészői kivételekről egyaránt készül jelentés.

* **Lapmegtekintések és a betöltés teljesítmény**: a felhasználók böngészőjének jelentések lekérése.

* **AJAX-hívások**: weblap sebességét, a válaszidőt és a hiba díjszabás beolvasása.

* **Felhasználók és munkamenetek száma**.

* **Teljesítményszámlálók**: adatok beolvasása a Windows vagy Linux rendszerű kiszolgáló gépek, például a Processzor, memória, és a hálózati használati.

* **Diagnosztika gazdagép**: adatokat lekérni a Docker- vagy Azure.

* **Diagnosztikai nyomkövetési naplóit**: adatokat lekérni az alkalmazást úgy, hogy a nyomkövetési események kivizsgált kérelmeket.

* **Egyéni események és metrikák**: az ügyfél vagy kiszolgáló kódot, nyomon követheti az üzleti eseményeket, például az elemek eladott vagy nyert játékok írást saját kezűleg adatok beolvasása.

A következő táblázat és integrációs feladatokhoz ismerteti:

| Integrációs forgatókönyv | Leírás |
| --------------------- | :---------- |
|[Alkalmazás-hozzárendelés](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Az alkalmazás összetevői, alapvető metrikákkal és riasztásokkal.||
|[Diagnosztika például keresés adatokat keressen](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Események keresése és szűrése, például kérések, kivételek, függőségi hívások, naplókivonatok és lapmegtekintések.||
|[Összesített adatai a Metrikaböngészőben](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Összesített adatok – például kérés- és hibaarányok, valamint kivételek, válaszidők és lapbetöltési idők – böngészése, szűrése és szegmentálása.||
|[Irányítópultok](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Különböző erőforrásokból származó adatokat fűzhet össze és oszthat meg másokkal. Kiváló megoldás több összetevőből álló alkalmazások, valamint csoportszobában való folyamatos megjelenítés esetén.||
|[Live Metrics Stream](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|Új buildverzió telepítésekor közel valós idejű teljesítménymutatókat figyelhet, és meggyőződhet arról, hogy minden megfelelő módon működik-e.||
|[Elemzés](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|A hatékony lekérdezési nyelvnek köszönhetően válaszokat kaphat az alkalmazás teljesítményére és használatára vonatkozó legégetőbb kérdésekre.||
|[Automatikus és manuális riasztások](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Automatikus riasztások alkalmazkodnak hozzá az alkalmazás normál mintákat keressen az telemetriai adatokat, és által kiváltott, amikor szükség van a szokásos mintát kívül. Riasztásokat állíthat be az egyéni vagy normál metrikák adott szintjeire is.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|A kód teljesítményadatainak megjelenítéséhez. A hívásláncokból a kódba ugorhat.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrálhatja a használati metrikákat más üzleti intelligenciával.||
|[REST API](https://dev.applicationinsights.io/)|Kód írásával lekérdezéseket futtathat a metrikákhoz és a nyers adatokhoz kapcsolódóan.||
|[Folyamatos exportálás](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Amikor megérkeznek, tömeges Storage nyers adatainak exportálását.||

### <a name="azure-security-center-alerts"></a>Azure Security Center riasztásait
A fenyegetésészlelés az Azure Security Center automatikusan biztonsági információk begyűjtése az Azure-erőforrások, a hálózati és az összekapcsolt partnermegoldások működik. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket. A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására. További információkért lásd: [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Az Azure Security Center diagramja](./media/azure-log-audit/azure-log-audit-fig7.png)

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A nagyméretű eredményeket vonatkozik és [gépi tanulás](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) események kiértékelni a teljes felhőháló különböző technológiákat. Ezzel a módszerrel nem lehet azonosításához manuális módszer segítségével, és támadásokat fejlődéséhez előrejelzésére szolgáló észlel. Ezek a biztonsági elemzések a következők:

* **Integrált fenyegetésfelderítési adataival**: a Microsoft-termékek és a szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft biztonsági válasz Center (MSRC), a külső hírcsatornák globális fenyegetésfelderítési adataival alkalmazásával ismert ezeken keresi.

* **Viselkedéselemzés**: kártékony felderítéséhez ismert mintákat vonatkozik.

* **Anomáliadetektálás**: tömörítés funkció használatával létrehozza a korábbi alapvető statisztikai adatainak összegyűjtése. Jelzi a meghatározott alapkonfigurációktól való olyan eltéréseket, amelyek potenciális támadásként értelmezhetők.

Számos biztonsági műveletek és incidensekre adott reakciók csapatok támaszkodnak SIEM megoldás triaging és biztonsági riasztások kivizsgálásának kiindulási pontként. Az Azure napló integráció szinkronizálhatja a Security Center riasztásait és a virtuális gép biztonsági eseményeket, az Azure diagnosztikai és a naplózási naplók közel valós időben a Naplóelemzési vagy SIEM-megoldás által gyűjtött.

## <a name="log-analytics"></a>Log Analytics 

A Naplóelemzési egy olyan szolgáltatás, amely összegyűjti és elemzi az adatokat, a felhőben lévő erőforrások által generált és a helyszíni környezetben segít az Azure-ban. Biztosít a valós idejű elemzések integrált keresés és egyéni irányítópultok segítségével könnyen elemezni több millió rekordot a számítási feladatok és a kiszolgálók fizikai helytől függetlenül.

![Napló Analytics diagramja](./media/azure-log-audit/azure-log-audit-fig8.png)

A Log Analytics center van a Naplóelemzési munkaterület, amely az Azure-ban üzemeltetett. A Naplóelemzési adott adatokat gyűjti a munkaterületen csatlakoztatott adatforrások adatforrások konfigurálásával és a megoldások hozzáadása az előfizetéséhez. Adatforrások és az egyes létrehozása különböző megoldásokat bejegyzéstípusokat, külön-külön tulajdonságkészletbe. De források és megoldások továbbra is elemezhetők együtt a munkaterület lekérdezésekben. Ez a funkció lehetővé teszi az azonos eszközök és módszerek segítségével számos különböző forrásokból gyűjtött adatokra dolgozhat.

Csatlakoztatott adatforrások, a számítógépeket és más erőforrásokat, amelyek létrehozzák a Naplóelemzési által összegyűjtött adatokat. Források a telepített ügynökök tartalmazhatnak [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) és [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) közvetlenül csatlakozó számítógépek, illetve az ügynökök [System Center Operations Manager csatlakoztatott felügyeleti csoport](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). A Naplóelemzési is gyűjthet adatokat egy [Azure storage-fiók](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Adatforrások](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) minden csatlakoztatott forrásból gyűjtött adatok különböző típusú. Adatforrások eseményekre és [teljesítményadatokat](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) a [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) és a Linux-ügynököt, például adatforrások mellett [IIS-napló](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) és [egyéniszövegesnaplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Konfigurálhatja az összegyűjteni kívánt egyes adatforrásokat, és a konfiguráció automatikusan érvénybe lép minden egyes csatlakoztatott forráson.

Négy módja van [gyűjteni a naplókat és az Azure-szolgáltatások metrikáját](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):
* Az Azure Diagnostics közvetlen szolgáltatáshoz (**diagnosztika** az alábbi táblázatban)

* Az Azure storage szolgáltatáshoz Azure Diagnostics (**tárolási** az alábbi táblázatban)

* Az Azure szolgáltatások összekötők (**összekötő** az alábbi táblázatban)

* Az adatgyűjtésre és majd visszaküldeni az adatok Naplóelemzési (az alábbi táblázatban és szolgáltatásokról, amelyek nem szerepelnek a listán az üres cellák) parancsfájlok

| Szolgáltatás | Erőforrás típusa | Logs | Mérőszámok | Megoldás |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnosztika|Diagnosztika|    [Az Azure alkalmazás](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [átjáró elemzés](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Összekötő|  Összekötő|  [Az Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Connector (előzetes verzió)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Azure Automation-fiók| Microsoft.Automation/<br>AutomationAccounts|    Diagnosztika||       [További információ](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Azure Batch-fiókok|  Microsoft.Batch/<br>batchAccounts|  Diagnosztika|    Diagnosztika||
|Klasszikus cloud services csomag||       Storage||       [További információ](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>fiókok|       Diagnosztika|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>fiókok|   Diagnosztika|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>fiókok|   Diagnosztika|||
|Az Azure Event Hubs névtér| Microsoft.EventHub/<br>névterek|  Diagnosztika|    Diagnosztika||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnosztika||
|Azure Key Vault|   Microsoft.KeyVault/<br>tárolók|  Diagnosztika  || [Key Vault-elemzés](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnosztika|||
|Azure Logic Apps|  Microsoft.Logic/<br>Munkafolyamatok|  Diagnosztika|    Diagnosztika||
||Microsoft.Logic/<br>integrationAccounts||||
|Network Security Groups (Hálózati biztonsági csoportok)|   Microsoft.Network/<br>networksecuritygroups|Diagnosztika||   [Azure hálózati biztonsági csoport elemzés](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Helyreállítási tárolók|   Microsoft.RecoveryServices/<br>tárolók|||[Az Azure Recovery Services Analytics (előzetes verzió)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Szolgáltatások keresése|   Microsoft.Search/<br>searchServices|    Diagnosztika|    Diagnosztika||
|Service Bus-névtér| Microsoft.ServiceBus/<br>névterek|    Diagnosztika|Diagnosztika|    [Service Bus Analytics (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric Analytics (előzetes verzió)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>kiszolgálók /<br>adatbázisok||       Diagnosztika||
||Microsoft.Sql/<br>kiszolgálók /<br>elasticPools||||
|Storage|||         Szkript| [Az Azure Storage Analytics (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure-alapú virtuális gépek|    Microsoft.Compute/<br>virtuális gép|  Mellék|  Mellék||
||||Diagnosztika||
|Virtuálisgép-méretezési csoportok|    Microsoft.Compute/<br>virtuális gép    ||Diagnosztika||
||Microsoft.Compute/<br>virtualMachineScaleSets /<br>virtuális gép||||
|Web server farms|Microsoft.Web/<br>serverfarms||   Diagnosztika
|Webhelyek|  Microsoft.Web/<br>webhelyek ||      Diagnosztika|    [További információ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>helyek /<br>tárolóhelyek|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>A helyszíni SIEM-rendszerekről napló integrációja
A [Azure napló integrációs](https://www.microsoft.com/download/details.aspx?id=53324), integrálható nyers naplók az Azure erőforrások közül a helyszíni SIEM-rendszerben.

![Napló integrációs diagramja](./media/azure-log-audit/azure-log-audit-fig9.png)

Napló integráció az Azure diagnostics gyűjti össze a Windows virtuális gépek, a Azure tevékenységi naplóit, az Azure Security Center riasztásait és az Azure-erőforrás a szolgáltató naplóiban találhatók. Ez az integráció a egy új, egységesített irányítópult minden eszköz, kínál, hogy azok a rendszer a helyszínen vagy a felhőben, így meg tudja-e összesíteni, összefüggéseket, elemzése és biztonsági események riasztás.

Napló integrációs jelenleg támogatja az Azure tevékenységi naplóit integrálását, Windows eseménynaplóiban keresse meg a Windows virtuális gépek az Azure-előfizetés, az Azure Security Center riasztásait, az Azure diagnosztikai naplók és az Azure AD a naplók.

| Napló típusa | A Naplóelemzési JSON (Splunk ArcSight és az IBM QRadar) támogatása |
| :------- | :-------------------------------------------------------- |
|Az Azure AD naplózási naplói|   Igen|
|Tevékenységnaplók| Igen|
|A Security Center riasztásait |Igen|
|Diagnosztikai naplók (erőforrás-naplók)|  Igen|
|VM-naplók|   Igen, és nem keresztül JSON keresztül továbbított események|

[Ismerkedés az Azure napló integrációs](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): Ez az oktatóanyag bemutatja, hogyan telepítése Azure napló integrációs és integrálása az Azure storage naplókat, Azure tevékenységi naplóit, az Azure Security Center riasztásait és az Azure AD naplók.

A SIEM integrációs feladatokhoz:

* [Partner konfigurációs lépések](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Ebben a blogbejegyzésben bemutatja, hogyan használható Splunk, HP ArcSight és az IBM QRadar partneri megoldások Azure napló-integráció konfigurálása.

* [Az Azure napló integrációs gyakran ismételt kérdések](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): Ez a cikk Azure napló integrációs kapcsolatos kérdésekre ad választ.

* [A Security Center riasztásait integrálása Azure napló integrációs](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): Ez a cikk ismerteti a Security Center riasztásait, virtuális gép biztonsági eseményeket az Azure diagnosztikai naplók, által gyűjtött szinkronizálása és Azure auditnaplókat a Naplóelemzési vagy a SIEM megoldás.

## <a name="next-steps"></a>További lépések

- [A vizsgálati és naplózási](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): adatok védelme a láthatóság fenntartásával, és gyorsan időben történő biztonsági riasztásokra való reagálásról.

- [Biztonsági naplózás és a napló gyűjteményhez az Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Győződjön meg arról, hogy az Azure-példányokon vannak-e a megfelelő biztonsági és naplózási naplók gyűjtésére a beállítások kényszerítése.

- [Webhelycsoport naplózási beállításainak konfigurálása](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Ha egy rendszergazdát, beolvasása, az egyes felhasználók műveletek előzményeit és az egy adott dátumtartományon belül alatt végrehajtott műveleteket előzményeit. 

- [A naplófájlban keresse az Office 365 biztonsági és megfelelőségi központ](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): az Office 365 biztonsági és megfelelőségi központ használatával egyesített naplójának keresheti ki és az Office 365 szervezet felhasználói és rendszergazdai tevékenység megtekintéséhez.


