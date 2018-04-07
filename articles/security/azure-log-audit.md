---
title: Azure-naplózás és a naplózás |} Microsoft Docs
description: További tudnivalók arról, hogyan használja a naplózási adatokat az alkalmazással kapcsolatos részletes dcu.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: c82b56cdf0fc2cb288986cf8fbf43c2dab5eacb6
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="azure-logging-and-auditing"></a>Az Azure naplózása és naplózás
## <a name="introduction"></a>Bevezetés
### <a name="overview"></a>Áttekintés
Segít a jelenlegi és jövőbeli Azure-ügyfél megismeréséhez és a különböző biztonsági képességeivel érhető el, és az Azure platformra körülvevő, Microsoft fejlesztett ki több tanulmányok, biztonsági áttekintése, ajánlott eljárások és Ellenőrzőlisták. A témakörök között körének és mélysége, és rendszeresen frissülnek. Ez a dokumentum a sorozat része a következő absztrakt szakaszban foglaltak szerint.
### <a name="azure-platform"></a>Azure Platform
Azure olyan nyitott és rugalmas felhőszolgáltatási platform, amely támogatja a operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök legszélesebb kiválasztása.

Megteheti például a következőt:
-   Futtassa a Linux-tárolók Docker-integráció.

-   JavaScript, Python, .NET, PHP, Java és Node.js-alkalmazások létrehozása

-   Build vissza-végpontok iOS, Android és Windows eszközökhöz.

Azure nyilvános felhőjében services támogatja a fejlesztők milliói technológiát és informatikai szakemberek számára már alapulnak, és megbízható.

Létrehozása vagy áttelepítése informatikai eszközök, a felhőbeli szolgáltató meg vannak támaszkodva az alkalmazások és a szolgáltatások és a biztonsági a felhő alapú eszközök kezelésére adathordozóira vezérlők adatok védelme érdekében, hogy szervezete képességek.

Az Azure infrastruktúráját úgy tervezték, hogy képes legyen ügyfelek millióit egyidejűleg kiszolgáló alkalmazásokat üzemeltetni, és olyan megbízható alapot nyújtson, amely megfelel a vállalatok biztonsági igényeinek. Számos konfigurálható biztonsági beállítással is rendelkezik, amelyek testreszabásával a különböző környezetek egyedi követelményeinek megfelelő biztonsági megoldások alakíthatók ki. Ez a dokumentum segítségével megfelel a fenti követelményeknek.

### <a name="abstract"></a>Absztrakt
Naplózás és a biztonsággal kapcsolatos eseményeket, és a kapcsolódó riasztásokat naplózása egy hatékony data protection stratégiában fontos összetevői. A biztonsági naplók és jelentések elektronikus rekord gyanús tevékenységek és észlelni a lehetséges, hogy sikeres vagy megkísérelt külső behatolást vagy a biztonság a hálózati, valamint a belső támadásokkal jelző minták segítséget nyújtanak. Naplózás segítségével felhasználói tevékenység, a dokumentum előírásoknak való megfelelés figyelése, hajtsa végre a törvényszéki elemzések végrehajtásakor, és több. Riasztások biztosítanak az azonnali értesítések biztonsági események bekövetkezésekor.

Microsoft Azure-szolgáltatások és termékek biztosít konfigurálható biztonsági naplózás és a naplózási beállítások segítségével azonosíthatja a biztonsági házirendeket és mechanizmusokat hiányosságait, és oldja meg ezeket hézagok problémák megelőzése érdekében. Microsoft-szolgáltatások biztosítják az egyes (és néhány esetben az összes) az alábbiak közül: központi monitorozása, naplózási és elemzési rendszerek folyamatos egyrészt; időszerű riasztások; és, hogyan kezelheti a nagy mennyiségű információ eszközök és szolgáltatások által létrehozott jelentéseket.

Microsoft Azure naplóadatokat biztonsági incidens és az esemény felügyeleti SIEM-rendszerek elemzés céljából exportálhatja és külső naplózási megoldásokkal integrálható.

E tanulmány bevezetője létrehozásakor, gyűjtése és elemzése az Azure-platformon futó szolgáltatások biztonsági naplókat, és segíthet biztonsági betekintést nyerhet az Azure-környezetekhez. Ez a dokumentum körét korlátozódik, alkalmazások és szolgáltatások beépített és az Azure-ban telepített.

> [!Note]
> Bizonyos ajánlások található a megnövekedett adat-, hálózati vagy számítási erőforrások használatát eredményezi, és növelheti a licencek vagy előfizetések költségeit.

## <a name="types-of-logs-in-azure"></a>Az Azure-ban naplók típusait
Sok áthelyezése alkotórészek összetettek a felhőalapú alkalmazásokhoz. Naplók adja meg, győződjön meg arról, hogy az alkalmazás marad fel adatokat és kifogástalan állapotban futnak. Emellett segít, hogy ki a lehetséges problémák stave és a múltbeli kiépítettektől eltérő hibakeresést. Naplózási adatok segítségével emellett az alkalmazással kapcsolatos átfogó megismerésében. Ezt az információt nyújt segítséget az alkalmazások teljesítményének vagy karbantartási követelmények, vagy, amelyek egyébként kézi beavatkozás műveletek automatizálására.

Azure kiterjedt naplózás minden Azure Service eredményez. Ezek a naplók szerint vannak kategóriába, ezek a típusok fő szerint:
-   **Ellenőrzés/management-naplók** adhat az Azure Resource Manager LÉTREHOZNI, UPDATE és DELETE műveletek láthatósága. [Az Azure tevékenységi naplóit](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) példa az ilyen típusú napló.

-   **Adatok sík naplók** adhat az Azure-Erőforrás kihasználtsága részeként kiváltott események láthatósága. Az ilyen típusú napló többek között a Windows-esemény rendszer, biztonság, és alkalmazás jelentkezik be egy virtuális gép és a [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) Azure figyelő konfigurálva


-   **A feldolgozott események** elemzett eseményeket/riasztásokat feldolgozott információkat az Ön nevében. Példa ilyen típusú [Azure Security Center riasztásait](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) ahol [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) feldolgozása és az előfizetés elemzése és tömör biztonsági riasztások

A következő táblázat felsorolja az Azure-ban elérhető naplók legfontosabb típusú.

| Napló kategória | Napló típusa | A tanúsítványalgoritmusok | Integráció |
| ------------ | -------- | ------ | ----------- |
|[Tevékenység-naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Az Azure Resource Manager-erőforrás vezérlő-vezérlősík események|   Adja meg az erőforrást az előfizetésében a végrehajtott műveletek betekintést.| REST-API & [Azure figyelője](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Az Azure diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|a következő előfizetés erőforrásait Azure Resource Manager működésével kapcsolatos gyakori adatok| Adja meg, hogy az erőforrás maga végrehajtott műveletek betekintést| Azure Monitor, [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Az AAD-jelentés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Naplók és jelentések|Felhasználói bejelentkezési tevékenységek & rendszer tevékenység szereplő felhasználók és csoportok kezelése|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtuális gép & Cloud Services csomag](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)|Windows-Eseménynapló és a Linux rendszernaplójába bejegyzett|    Rendszeradatok és a virtuális gépek a naplózási adatokat rögzíti, és átviszi az adatok az Ön által választott tárolási figyelembe.|   Windows használatával [ÜVEGVATTA](https://docs.microsoft.com/azure/azure-diagnostics) (a Windows Azure diagnosztikai tárolási) és a Linux Azure a Teljesítményfigyelőben|
|[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Tárolási naplózási és mérőszámok-adatokat biztosít a storage-fiók|Betekintést nyújt tárolni kívánt nyomkövetési kérelmek elemezheti a használati trendeket, és a storage-fiók problémák elemzéséhez.|    REST API-t vagy a [ügyféloldali kódtár](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[NSG-t (hálózati biztonsági csoport) folyamat Naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON formátumban jeleníti meg a bejövő és kimenő forgalom / szabály alapon és|IP-bemenő és kimenő forgalom keresztül a hálózati biztonsági csoportok adatainak megtekintése|[Hálózati figyelőt](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Naplók, kivételeket és egyéni diagnosztika|    Alkalmazásteljesítmény-felügyeleti (APM) alkalmazásszolgáltatás webfejlesztőknek, több platformon.| REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Adatok feldolgozása / biztonsági riasztás| Az Azure Security Center riasztást, a napló Analytics riasztás|   Biztonsági adatokat és a riasztásokat.|   REST API-k, JSON|

### <a name="activity-log"></a>Tevékenységnapló
A [Azure tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), az erőforrást az előfizetésében a végrehajtott műveletek betekintést nyújt. A műveletnapló korábban hívták "Naplófájlok" vagy "Működési Logs", mivel a rendszer jelzi [vezérlő-vezérlősík események](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) az előfizetésekhez. A tevékenység-naplót használó, meghatározhatja a "mi, ki, és mikor" az esetleges írási műveleteket (PUT, POST, Törlés) végzett az erőforrást az előfizetésében. A művelet és az egyéb kapcsolódó tulajdonságainak állapotának értelmezni is lehet. A műveletnapló nem tartalmaz (GET) olvasási műveletek.

Itt PUT, POST, törölje az összes az írási műveletek tevékenységnapló tartalmaz arról, hogy az erőforrások hivatkozik. Használhatja például a tevékenységi naplóit található hiba esetén végzett hibaelhárításhoz vagy hogyan a szervezet egy felhasználó módosította a következő erőforrás figyelésére.

![Tevékenységnapló](./media/azure-log-audit/azure-log-audit-fig1.png)


Események kérhetnek le az Azure portál használatával tevékenységnapló [CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), PowerShell-parancsmagok és [Azure figyelő REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Tevékenységi naplóit rendelkezik 19 napos adatmegőrzési időtartam.

Integrációs feladatokhoz
-   [E-mailben vagy webhook riasztás létrehozása, amely elindítja a tevékenységnapló esemény.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   [Az Eseményközpontok felé adatfolyamként](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) egy külső szolgáltatás vagy az egyéni elemzési megoldások, például a Power bi szempontjából.

-   A Power bi használatával elemezze a [Power bi tartalomcsomag.](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)

-   [Mentse a Tárfiók archív vagy manuális ellenőrzést.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log) Megadhatja a napló-profilok használatával megőrzési időtartama (napokban).

-   Lekérdezi, és tekintse meg az Azure-portálon.

-   Lekérdezése a PowerShell-parancsmag, a parancssori felületen vagy a REST API-t.

-   A műveletnapló napló profilokkal való exportálás [Analytics jelentkezzen](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Használhatja a storage-fiókok vagy [event hub névtér](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) a rendszer egy kibocsátás bejelentkezési ugyanahhoz az előfizetéshez. A beállítás konfiguráló felhasználónak rendelkeznie kell a megfelelő [RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) mindkét előfizetéshez való hozzáférés
### <a name="azure-diagnostic-logs"></a>Az Azure diagnosztikai naplók
Az Azure diagnosztikai naplók erőforrás a művelet részletes, gyakori adatokat biztosító erőforrás által kibocsátott. Ezek a naplók tartalmának függ a erőforrástípus (például [Windows rendszer-eseménynaplói](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)diagnosztikai napló egy kategóriát vannak a virtuális gépek és [blob, table és a várólista naplók](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) diagnosztikai naplókat a további kategóriája Storage-fiókok) és erőforrást az előfizetésében a végrehajtott műveletek betekintést nyújt tevékenységnapló különböznek.

![Az Azure diagnosztikai naplók](./media/azure-log-audit/azure-log-audit-fig2.png)

Az Azure diagnosztikai naplókat kínál több konfigurációs beállítások, amely, Azure-portálon PowerShell, a parancssori felület (CLI) és a REST API használatával.

**Integrációs feladatokhoz**
-   Mentse azokat egy [Tárfiók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) naplózási vagy manuális ellenőrzést. A diagnosztikai beállítások használatával megőrzési időtartama (napokban) is megadhat.

-   [Az adatfolyamot őket a Event Hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) adatfeldolgozást egy külső szolgáltatás vagy az egyéni elemzési megoldások többek között a [Power bi.](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

-   Elemezheti őket a [Naplóelemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

**Támogatott szolgáltatások, a diagnosztikai naplók és a támogatott kategóriák erőforrás típusonkénti séma**


| Szolgáltatás | Séma & Docs | Erőforrás típusa | Kategória |
| ------- | ------------- | ------------- | -------- |
|Load Balancer| [Naplóelemzési az Azure Load Balancer (előzetes verzió)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|    LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|Network Security Groups (Hálózati biztonsági csoportok)|[Naplóelemzés hálózati biztonsági csoportokhoz](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|Application Gateway átjárók|[Az Alkalmazásátjáró diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|
|Key Vault|[Az Azure Key Vault naplózása](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Engedélyezése és a keresési forgalom Analytics használata](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Data Lake Store|[Diagnosztikai naplók az Azure Data Lake Store elérése](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|Naplózás|
|Data Lake Analytics|[Az Azure Data Lake Analytics diagnosztikai naplóinak elérése](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts|Naplózás|
|||Microsoft.DataLakeAnalytics/accounts|Kérelmek|
|||Microsoft.DataLakeStore/accounts|Kérelmek|
|Logic Apps|[Logic Apps B2B egyéni követési séma](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|WorkflowRuntime|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Azure Batch|[Azure Batch diagnosztikai naplózás](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Az Azure Automation szolgáltatáshoz](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|Event Hubs|[Az Azure Event Hubs diagnosztikai naplók](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|Stream Analytics|[Diagnosztikai naplók feladat](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|Futtatási|
|||Microsoft.StreamAnalytics/streamingjobs|Szerzői műveletek|
|Service Bus|[Az Azure Service Bus diagnosztikai naplók](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Az Azure Active Directory Reporting
Az Azure Active Directory (Azure AD) biztonsági, naplózási és tevékenységjelentéseket biztosít a címtárához. A [Azure Active Directory naplózási jelentés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) segítségével az ügyfelek azonosításához, amely az Azure Active Directoryban történt a privilegizált műveletekhez. A privilegizált műveletekhez tartalmazza az illetéktelen módosítások (például a szerepkör létrehozása vagy a jelszó alaphelyzetbe állítása), változó Csoportházirend konfigurálásának (például jelszóházirendek) és (például tartomány-összevonási beállításai módosításának) directory konfigurációjának módosításai.

A jelentések az esemény nevét, a szereplő ki hajtotta végre a műveletet, a cél erőforráson hatással a módosítás dátuma és időpontja (UTC) adja meg a naplórekordot. Az ügyfelek képesek naplózási események listájának beolvasása az Azure Active Directory keresztül a [Azure-portálon](https://portal.azure.com/)leírtak szerint [megtekintése a vizsgálati naplókban](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). A benne foglalt jelentések listája:

| Biztonsági jelentések | Tevékenységjelentések | Naplózási jelentések |
| :--------------- | :--------------- | :------------ |
|Bejelentkezések ismeretlen forrásokról| Alkalmazáshasználat: összegzés| Címtárnaplózási jelentés|
|Több hibát követő bejelentkezések|  Alkalmazáshasználat: részletes||
|Bejelentkezések különböző földrajzi régiókból|    Alkalmazás irányítópultja||
|Bejelentkezések gyanús tevékenységeket mutató IP-címekkel|   Alkalmazás-kiépítési hibák||
|Rendszertelen bejelentkezési tevékenység|    Egyéni felhasználói eszközök||
|Bejelentkezések potenciálisan fertőzött eszközökről|   Egyéni felhasználói tevékenység||
|Rendellenes bejelentkezési tevékenységet mutató felhasználók| Csoporttevékenység-jelentés||
||Jelszó-visszaállítási regisztrációs tevékenységjelentés||
||Jelszó-visszaállítási tevékenység|||

Ezeket az adatokat az alkalmazások, például a SIEM rendszerek, naplózási és üzleti intelligencia eszközök hasznos lehet. Az Azure AD-jelentéskészítés API-k REST-alapú API-kon keresztül biztosítják az adatok szoftveres hozzáférését. Ezek hívása [API-k](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) különböző programozási nyelveket és eszközökön.

Az Azure AD naplózási jelentés események kerülnek 180 napig tart.

> [!Note]
> A jelentésekre megőrzési kapcsolatos további információkért lásd: [Azure Active Directory jelentés adatmegőrzési szabályai.](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)

Az ügyfelek a naplóesemények tárolásához hosszabb megőrzési ideig iránt érdeklődik, a Reporting API segítségével rendszeresen lekéréses [naplózása](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) külön adattárba.

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>Virtuális gép naplók Azure Diagnostics használatával
[Az Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics) a képesség, amely lehetővé teszi a telepített alkalmazás diagnosztikai adatok gyűjtésére Azure-ban. Számos különböző forrásokból származó használja a diagnosztika bővítményét. A rendszer jelenleg támogatott [Azure Cloud Service webes és feldolgozói szerepkörök](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me),

![Virtuális gép naplók Azure Diagnostics használatával](./media/azure-log-audit/azure-log-audit-fig3.png)

[Az Azure virtuális gépek](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) Microsoft Windows rendszerű és [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview).

Engedélyezheti a Azure diagnosztikai egy virtuális gép használata a következő:

-   Tekintse meg a Visual Studio használatával [nyomkövetési Azure virtuális gépeken való használata a Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

-   [Egy Azure virtuális gép távolról az Azure Diagnostics beállítása](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [Azure virtuális gépeken diagnosztika beállítása a PowerShell használatával](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [Windows virtuális gép létrehozása a figyelési és -diagnosztika Azure Resource Manager-sablon](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics
[Az Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) elvégzi a naplózási, és adja meg a tárfiók metrikák adatait. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat. Storage Analytics naplózási érhető el a [Blob, Queue és Table szolgáltatások.](https://docs.microsoft.com/azure/storage/storage-introduction) Storage Analytics egy társzolgáltatás sikeres és sikertelen kérelmekkel kapcsolatos részletes információkat naplózza.

Ezeket az információkat egyes kérelmeket a figyelheti és egy tárolási szolgáltatással kapcsolatos problémák diagnosztizálásához használható. Kérelmek is be vannak jelentkezve a legjobb alapul. Naplóbejegyzéseket jönnek létre, csak ha nincs a szolgáltatási végpont ellen. Például ha a tárfiók tevékenység rendelkezik-e a Blob végpontja, de nem a tábla- vagy várólista végpontját csak naplózza vonatkozó a Blob szolgáltatás jön létre.

A tárolási analitika használatához engedélyeznie kell azt egyedileg minden egyes figyelni kívánt szolgáltatás. Engedélyezheti a a [Azure-portálon](https://portal.azure.com/); további információkért lásd: [figyelése egy tárfiókot az Azure portálon.](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) Tárolási analitika programozott módon a REST API vagy ügyfélkódtár keresztül is engedélyezheti. A szolgáltatás tulajdonságainak beállítása művelet segítségével tárolási analitika külön-külön engedélyezni az egyes szolgáltatásokhoz.

Az összesített adatok tárolását a jól ismert blob (naplózás), és jól ismert táblában (a metrika), előfordulhat, hogy elérhetők, a Blob és Table szolgáltatások API-k használatával.

Tárolási analitika rendelkezzen, amely független a tárfiók teljes korlátját tárolt adatok mennyisége a 20-TB-os korlátot. Minden naplója [blokkblobokat](https://docs.microsoft.com/azure/storage/storage-analytics) $logs nevű tároló, amely jönnek létre automatikusan tárolási analitika engedélyezve van a tárfiók.

> [!Note]
> A számlázással és az adatmegőrzési házirendek további információkért lásd: [tárolási analitika és számlázási.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)
>
> [!Note]
> A tárfiókok korlátai további információkért lásd: [Azure Storage méretezhetőségi és teljesítménycéloknak.](https://docs.microsoft.com/azure/storage/storage-scalability-targets)

A következő típusú hitelesített vagy névtelen kérelmeket a rendszer naplózza.



| Hitelesített  | Névtelen|
| :------------- | :-------------|
| Sikeres kérelmei | Sikeres kérelmei |
|Sikertelen kérelmek, beleértve az időtúllépés, a sávszélesség-szabályozás, hálózati, engedélyezési és egyéb hibák | Egy közös hozzáférésű Jogosultságkód (SAS), például és a sikertelen kérelmek használatával |
| Egy közös hozzáférésű Jogosultságkód (SAS), például és a sikertelen kérelmek használatával |Az ügyfél és kiszolgáló egyaránt időtúllépési hibák |
|   Az analitikai adatok kérelmek |    Sikertelen GET kérelmek 304 (nem módosított). Hibakód: |
| Tárolási analitika, például a napló létrehozásakor vagy törlésekor, kérelmét a rendszer nem naplózza. A naplózott adatok teljes listáját részletes ismertetését lásd: a [Storage Analytics naplózott műveletekkel és az állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) és [Storage Analytics naplóformátumban](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) témaköröket. | Minden más sikertelen névtelen kérelmek nem naplózza a rendszer. A naplózott adatok teljes listáját részletes ismertetését lásd: a [Storage Analytics naplózott műveletekkel és az állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) és [Storage Analytics naplóformátumban](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Az Azure hálózati naplók
Hálózati naplózás és figyelés az Azure-ban átfogó, és ismerteti a két kategóriába sorolhatók:

-   [Hálózati figyelő](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) -forgatókönyv-alapú hálózatfigyelési által biztosított szolgáltatások a hálózati figyelőt. A szolgáltatás része a csomagrögzítéssel, a következő ugrás, az IP-adatfolyam győződjön meg arról, biztonsági csoport megtekintése, NSG folyamata naplókat. Forgatókönyv szintű figyelési jeleníti meg egy végpontok közötti hálózati erőforrások ellentétben egyes hálózati erőforrás-figyelése.

-   [Erőforrás-figyelés](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring) -erőforrás-szintű figyelés négy szolgáltatások diagnosztikai naplók, metrikák, hibaelhárítási és erőforrás állapota áll. Ezek a szolgáltatások a hálózati erőforrás szintjén épülnek.

![Az Azure hálózati naplók](./media/azure-log-audit/azure-log-audit-fig4.png)

Hálózati figyelőt olyan regionális szolgáltatás, amely lehetővé teszi, hogy figyelése és diagnosztizálása szintjén feltételek egy hálózati forgatókönyv, hogy, és az Azure-ból. Hálózati diagnosztika és a képi megjelenítés eszközök is elérhetők a hálózati figyelőt segítenek megérteni, diagnosztizálása és információt kaphat a hálózathoz, az Azure-ban.

**NSG Flow naplózási** -folyamat naplókat a hálózati biztonsági csoportok lehetővé teszik a engedélyezett vagy megtagadott a csoport biztonsági szabályai forgalmi naplók rögzítése. A folyamat naplók JSON formátumban vannak megírva, és a kimenő és bejövő forgalom alapon egy szabályt, a hálózati adapter a folyamat vonatkozik, a folyamat (forrás vagy a cél IP-, forrás vagy a cél Port protokoll), 5 rekordos információk megjelenítése, és ha a forgalom lett engedélyez vagy tilt.

### <a name="network-security-group-flow-logging"></a>Hálózati biztonsági csoport folyamata naplózás

[Hálózati biztonsági csoport folyamata naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) azon szolgáltatása, amely lehetővé teszi, hogy tekintse meg a hálózati biztonsági csoporton keresztül bemenő és kimenő IP-forgalom hálózati figyelőt. A folyamat naplók JSON formátumban vannak megírva, és a kimenő és bejövő forgalom alapon egy szabályt, a hálózati adapter a folyamat vonatkozik, a folyamat (forrás vagy a cél IP-, forrás vagy a cél Port protokoll), 5 rekordos információk megjelenítése, és ha a forgalom lett engedélyez vagy tilt.

Attribútumfolyam naplózza a cél hálózati biztonsági csoportok, amíg azok nem azonos a többi naplófájlt jelenik meg. Attribútumfolyam naplók csak egy tárfiókon belül tárolják.

Az azonos megőrzési házirendek, a többi naplófájlt látott folyamata naplók vonatkoznak. Naplók rendelkezik egy megőrzési házirend – 365 nap beállítható az 1 nap. Ha nincs beállítva adatmegőrzési szabály, a naplók megőrzése korlátlan időre szól.

**Diagnosztikai naplók**

Rendszeres és önkéntes események hálózati erőforrások által létrehozott, és a storage-fiókok, egy Eseményközpontba vagy Naplóelemzési küldött bejelentkezve. Ezek a naplók erőforrás állapotának betekintést. Ezek a naplók eszközöket, például a Power BI és a Naplóelemzési tekintheti meg. Diagnosztikai naplók megtekintése megismeréséhez látogasson el [Naplóelemzési.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)

![Diagnosztikai naplók](./media/azure-log-audit/azure-log-audit-fig5.png)

Diagnosztikai naplók érhetők el [terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), útvonalakat, és [Alkalmazásátjáró.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

Hálózati figyelőt biztosít a diagnosztikai naplók megtekintése. Ez a nézet az összes hálózati erőforrások, amelyek támogatják a diagnosztikai naplózás tartalmazza. Ebben a nézetben engedélyezése, és tiltsa le a hálózati erőforrások egyszerűen és gyorsan.


Megelőző naplózási képességeket, valamint a hálózati figyelőt jelenleg a következő képességekkel rendelkezik:
- [Topológia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) -a különböző csatlakozás és egy erőforráscsoportban található hálózati erőforrások egymáshoz rendelését hálózati szintű nézetét jeleníti meg.

- [Változó csomagrögzítéssel](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) -csomagadatok mindkét virtuális gép rögzíti. Speciális szűrési beállítások, például fel idő beállítása és korlátozások méretezés finomhangolható vezérlők meg a indít. A csomagadatok a blob-tárolóban, vagy a helyi lemezen .cap formátumban tárolható.

-   [IP-folyamata ellenőrzi](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) -ellenőrzést, ha egy csomag engedélyezett vagy megtagadott folyamata adatokat 5 rekordos csomag paraméterek (cél IP-címe, forrás IP-címe, Célport, Forrásport és protokoll) alapján. Ha a csomag megtagadta a biztonsági csoport, a szabály és a csoportot, amely a csomag megtagadva ad vissza.

-   [Következő Ugrás](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) -határozza meg a következő ugrás a csomagok továbbítása az Azure hálózati háló, így lehetővé teszi bármely diagnosztizálása nincs megfelelően konfigurálva a felhasználó által definiált útvonalak.

-   [Biztonsági csoport megtekintése](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) -a hatékony és alkalmazott biztonsági szabályokat, amelyek érvényesek a virtuális gép lekérdezi.

-   [Virtuális hálózati átjáró és a kapcsolat hibaelhárítási](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) -lehetővé teszi a virtuális hálózati átjárók és kapcsolatok hibáinak elhárítása.

-   [Előfizetési korlátozásait a hálózati](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits) -lehetővé teszi a hálózati erőforrás-használati korlátozások megtekintéséhez.

### <a name="application-insight"></a>Application insights

[Az Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek, több platformon. Az élő webalkalmazásának figyelésére használhatja. Automatikus észlelése a teljesítményanomáliákat. Hatékony elemzőeszközöket tartalmaz, amelyek segítenek a problémák felismerésében, és annak a megértésében, hogy a felhasználók mire használják ténylegesen az alkalmazást.

 Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

 Sokféle platform alkalmazásaival együttműködik, ilyen többek között a .NET, a Node.js és a J2EE platform helyszíni vagy felhőbeli tárolás esetén is. Integrálható a devOps folyamat, és a csatlakozási pontok különböző fejlesztői eszközök rendelkezik.

![Application insights](./media/azure-log-audit/azure-log-audit-fig6.png)

A fejlesztőcsapatoknak készült Application Insights segít az adott alkalmazás teljesítményének megismerésében, valamint abban, hogy a felhasználók miként használják az alkalmazást. A szolgáltatás az alábbiakat figyeli:

-   **Kérések sebessége, válaszidők és hibaarányok** – megtudhatja, hogy mely lapok, mely napszakokban a legnépszerűbbek, és hol találhatók a felhasználók. Megtekintheti, hogy mely lapok teljesítenek a legjobban. Ha több kérés esetén a válaszidők és a hibaarányok értéke megnő, valószínűleg erőforrás-gazdálkodási hibáról van szó.

-   **Függőségi értékek, válaszidők és hibaarányok** – megtudhatja, hogy mely külső szolgáltatások okoznak lassulást.

-   **Kivételek** - elemezheti az összesített statisztikák, vagy válasszon olyan specifikus példányai, és elemezze a veremkivonatot és a kapcsolódó kérések. A kiszolgálói és a böngészői kivételekről egyaránt készül jelentés.

-   **Lapmegtekintések és betöltési teljesítmény** – a felhasználói böngészők jelentése alapján készül.

-   Weblapokról származó **AJAX-hívások** – értékek, válaszidők és hibaarányok.

-   **Felhasználók és munkamenetek száma**.

-   Windows vagy Linux rendszerű kiszolgálói gépekről származó **teljesítményszámlálók**, például processzor-, memória- és hálózathasználat.

-   Dockerből vagy Azure-ból származó **gazdadiagnosztika**.

-   Alkalmazásból származó **nyomkövetési naplók diagnosztikája** – megállapíthatja a nyomkövetési események és a kérések korrelációját.

-   **Egyéni események és metrikák**, amelyeket saját kezűleg helyezhet el az ügyfél vagy a kiszolgáló kódjában, és ezáltal üzleti eseményeket követhet (például eladott tételek vagy megnyert játékok).

**Integrációs feladatokhoz és a leírás listája:**

| Integrációs feladatokhoz | Leírás |
| --------------------- | :---------- |
|[Alkalmazás-hozzárendelés](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Az alkalmazás összetevői, alapvető metrikákkal és riasztásokkal.||
|[Diagnosztikai keresési például adatok](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Események keresése és szűrése, például kérések, kivételek, függőségi hívások, naplókivonatok és lapmegtekintések.||
|[Összesített adatai a Metrikaböngészőben](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Összesített adatok – például kérés- és hibaarányok, valamint kivételek, válaszidők és lapbetöltési idők – böngészése, szűrése és szegmentálása.||
|[Irányítópultok](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Különböző erőforrásokból származó adatokat fűzhet össze és oszthat meg másokkal. Kiváló megoldás több összetevőből álló alkalmazások, valamint csoportszobában való folyamatos megjelenítés esetén.||
|[Live Metrics Stream](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|Új buildverzió telepítésekor közel valós idejű teljesítménymutatókat figyelhet, és meggyőződhet arról, hogy minden megfelelő módon működik-e.||
|[Elemzés](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|A hatékony lekérdezési nyelvnek köszönhetően válaszokat kaphat az alkalmazás teljesítményére és használatára vonatkozó legégetőbb kérdésekre.||
|[Automatikus és manuális riasztások](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Az automatikus riasztások alkalmazkodnak az alkalmazás normál telemetriai mintáihoz, és a riasztásokat a szokásos mintán kívüli események váltják ki. Riasztásokat állíthat be az egyéni vagy normál metrikák adott szintjeire is.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|A kódban tekintheti meg a teljesítményadatokat. A hívásláncokból a kódba ugorhat.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrálhatja a használati metrikákat más üzleti intelligenciával.||
|[REST API](https://dev.applicationinsights.io/)|Kód írásával lekérdezéseket futtathat a metrikákhoz és a nyers adatokhoz kapcsolódóan.||
|[Folyamatos exportálás](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Amikor megérkeznek, tömeges Storage nyers adatainak exportálását.||

### <a name="azure-security-center-alerts"></a>Azure Security Center riasztásait
[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) automatikusan gyűjti, elemzi és integrálja az Azure-erőforrások, a hálózati és az összekapcsolt partneri megoldások, például tűzfal és az endpoint protection megoldások, hamis valós fenyegetések észlelése és naplóadatait figyelmeztetéséket. A Security Centerben megtekinthető a rangsorolt biztonsági riasztások listája, ezenkívül a probléma gyors vizsgálatára vonatkozó információk és a támadás elhárításával kapcsolatos javaslatok is megjelennek.

A Security Center fenyegetésészlelése úgy működik, hogy automatikusan összegyűjti a biztonsági információkat az Azure-erőforrásokból, a hálózatból és a csatlakoztatott partneri megoldásokból. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket. A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására.

![Azure Security Center](./media/azure-log-audit/azure-log-audit-fig7.png)

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A nagyméretű eredményeket és [gépi tanulás](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák alkalmazott események kiértékelni között a teljes felhőháló – lehetetlen manuális módszer segítségével, és előrejelzésére szolgáló észlelése a támadások alakulását. Ezek a biztonsági elemzések a következők:

-   **Integrált fenyegetésfelderítési adataival:** ismert ezeken keresi a Microsoft-termékek és a szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft biztonsági válasz Center (MSRC), a külső hírcsatornák globális fenyegetésfelderítési adataival alkalmazásával.

-   **Viselkedéselemzés:** kártékony felderítéséhez ismert mintákat vonatkozik.

-   **Anomáliadetektálás:** tömörítés funkció használatával létrehozza a korábbi alapvető statisztikai adatainak összegyűjtése. Jelzi a meghatározott alapkonfigurációktól való olyan eltéréseket, amelyek potenciális támadásként értelmezhetők.


Számos biztonsági műveletek és incidensekre adott reakciók csapatok támaszkodnak biztonsági adatai és az esemény felügyeleti SIEM-megoldás triaging és biztonsági riasztások kivizsgálásának kiindulási pontként. Az Azure naplóelemzés integráció az ügyfelek szinkronizálása a Security Center riasztásait és a virtuális gép biztonsági eseményeket, a naplóelemzési vagy SIEM-megoldás közel valós idejű Azure Diagnostics és az Azure-beli Auditnaplók által gyűjtött.


## <a name="log-analytics"></a>Log Analytics

A Naplóelemzési egy olyan szolgáltatás, amely összegyűjti és elemzi az adatok a felhőben lévő erőforrások által generált segít az Azure-ban és a helyszíni környezetben. Ez lehetővé teszi az integrált keresés és egyéni irányítópultok segítségével könnyen elemezni több millió rekordot a számítási feladatok és a kiszolgálók fizikai helytől függetlenül valós idejű elemzése.

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

A Log Analytics center van a Naplóelemzési munkaterület, amely az Azure felhőben szolgáltatott. Adatgyűjtés a munkaterületre csatlakoztatott adatforrások konfigurálása adatforrások és a megoldások hozzáadása az előfizetéséhez. Adatforrások és a megoldások egyes létrehozza a különböző típusok saját tulajdonságok rendelkeznie, de előfordulhat, hogy továbbra is elemezheti együtt lekérdezések a munkaterületre. Ez lehetővé teszi, hogy ugyanazokat az eszközöket és módszereket használva dolgozzon a különböző források által gyűjtött különböző típusú adatokkal.

A csatlakoztatott források azok a számítógépek és egyéb erőforrások, amelyek Log Analytics által összegyűjtött adatokat generálnak. Ilyen lehet például a telepített ügynökök [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) és [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) közvetlenül csatlakozó számítógépek vagy az ügynökök [System Center Operations Manager csatlakoztatott felügyeleti csoport.](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents) A Naplóelemzési is gyűjthet adatokat [az Azure storage.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)

[Adatforrások](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) alatt az egyes csatlakoztatott forrásokból gyűjtött adatok különböző fajtáit értjük. Ez magában foglalja az események és [teljesítményadatokat](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) a [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) és adatforrások, például mellett a Linux-ügynökök [IIS-napló](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs), és [egyéni szöveges naplók.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs) Konfigurálhatja az összegyűjteni kívánt egyes adatforrásokat, és a konfiguráció automatikusan érvénybe lép minden egyes csatlakoztatott forráson.

A négy különböző módon [naplókat és az Azure-szolgáltatások metrikáját gyűjtése:](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)
1.  Az Azure diagnostics (diagnosztika a következő táblázatban) szolgáltatáshoz közvetlen

2.  Az Azure storage (az alábbi táblázatban tároló) szolgáltatáshoz Azure diagnostics

3.  Az Azure-szolgáltatásokhoz (Összekötők a következő táblázatban) összekötők

4.  Az adatgyűjtésre és majd visszaküldeni az adatok Naplóelemzési (az alábbi táblázatban és szolgáltatásokról, amelyek nem szerepelnek a listán az üres), parancsfájlok

| Szolgáltatás | Erőforrás típusa | Logs | Mérőszámok | Megoldás |
| :------ | :------------ | :--- | :------ | :------- |
|Alkalmazásátjárók|  Microsoft.Network/<br>applicationGateways|  Diagnosztika|Diagnosztika|    [Az Azure alkalmazás](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [átjáró elemzés](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Összekötő|  Összekötő|  [Az Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Connector (előzetes verzió)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Automation-fiókok|   Microsoft.Automation/<br>AutomationAccounts|    Diagnosztika||       [További információ](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Batch-fiókok|    Microsoft.Batch/<br>batchAccounts|  Diagnosztika|    Diagnosztika||
|Klasszikus cloud services csomag||       Tárolás||       [További információ](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Kognitív szolgáltatások|    Microsoft.CognitiveServices/<br>fiókok|       Diagnosztika|||
|A Data Lake analytics|   Microsoft.DataLakeAnalytics/<br>fiókok|   Diagnosztika|||
|A Data Lake store|   Microsoft.DataLakeStore/<br>fiókok|   Diagnosztika|||
|Eseményközpont-névtér|   Microsoft.EventHub/<br>Névterek|  Diagnosztika|    Diagnosztika||
|IoT Hubok|  Microsoft.Devices/<br>IotHubs||     Diagnosztika||
|Key Vault| Microsoft.KeyVault/<br>Tárolók|  Diagnosztika  || [KeyVault Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Terheléselosztók|    Microsoft.Network/<br>loadBalancers|    Diagnosztika|||
|Logic Apps|    Microsoft.Logic/<br>Munkafolyamatok|  Diagnosztika|    Diagnosztika||
||Microsoft.Logic/<br>integrationAccounts||||
|Network Security Groups (Hálózati biztonsági csoportok)|   Microsoft.Network/<br>networksecuritygroups|Diagnosztika||   [Azure hálózati biztonsági csoport elemzés](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Helyreállítási tárolók|   Microsoft.RecoveryServices/<br>Tárolók|||[Az Azure Recovery Services Analytics (előzetes verzió)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Szolgáltatások keresése|   Microsoft.Search/<br>searchServices|    Diagnosztika|    Diagnosztika||
|Service Bus-névtér| Microsoft.ServiceBus/<br>Névterek|    Diagnosztika|Diagnosztika|    [Service Bus Analytics (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Tárolás||    [Service Fabric Analytics (előzetes verzió)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>adatbázisok||       Diagnosztika||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Tárolás|||         Szkript| [Az Azure Storage Analytics (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Virtuális gépek|  Microsoft.Compute/<br>virtuális gép|  Mellék|  Mellék||
||||Diagnosztika||
|Virtuális gépek méretezési csoportok|   Microsoft.Compute/<br>virtuális gép    ||Diagnosztika||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtuális gép||||
|Web Server farms|Microsoft.Web/<br>serverfarms||   Diagnosztika
|Webhelyek| Microsoft.Web/<br>webhelyek ||      Diagnosztika|    [További információ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>helyek /<br>tárolóhelyek|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>A helyszíni SIEM-rendszerekről napló integrációja
[Azure naplóelemzés integrációs](https://www.microsoft.com/download/details.aspx?id=53324) lehetővé teszi, hogy az Azure-erőforrások a nyers napló, a helyszíni integrálhatja **biztonsági adatai és az esemény felügyeleti SIEM-rendszerek**.

![Napló-integráció](./media/azure-log-audit/azure-log-audit-fig9.png)

Azure naplóelemzés integrációs Azure Diagnostics gyűjti össze a Windows (ÜVEGVATTA) virtuális gépeket, Azure tevékenységi naplóit, az Azure Security Center riasztásokat, és az Azure erőforrás-szolgáltató naplózza. Ez az integráció biztosítja, új, egységesített irányítópult minden Ez az eszköz, a helyszínen vagy a felhőben, így összesíteni, összefüggéseket, elemezheti, és a biztonsági események riasztás.



Azure Security Center riasztásait, az Azure diagnosztikai naplók és az Azure Active Directory-naplók, Azure naplóelemzés integrációs jelenleg támogatja az Azure tevékenységi naplóit, a Windows eseménynaplót a Windows virtuális gépként az Azure-előfizetéshez integrálását.

| Napló típusa | A naplóelemzési JSON (Splunk, ArcSight, Qradar) támogatása |
| :------- | :-------------------------------------------------------- |
|Az AAD-naplók|    igen|
|Tevékenységnaplók| Igen|
|ASC riasztások |Igen|
|Diagnosztikai naplók (erőforrás-naplók)|  Igen|
|VM-naplók|   Igen keresztül továbbított események, és nem JSON keresztül|


Az alábbi táblázat ismerteti, a napló kategória és a SIEM-integráció részletei.

[Ismerkedés az Azure naplóelemzés integrációs](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started) - oktatóanyag bemutatja, hogyan telepítése az Azure naplóelemzés integráció és integrálása az Azure ÜVEGVATTA storage naplókat, Azure tevékenységi naplóit, az Azure Security Center riasztásait és az Azure Active Directory naplók.

Integrációs feladatokhoz

-   [Partner konfigurációs lépések](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – ebben a blogbejegyzésben bemutatja, hogyan használható Splunk, HP ArcSight és az IBM QRadar partneri megoldások Azure naplóelemzés-integráció konfigurálása.

-   [Gyakori kérdések (GYIK) integrációs Azure naplóelemzés](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) -Ez gyakran ismételt kérdések Azure naplóelemzés integrációs kapcsolatos kérdésekre ad választ.

-   [A Security Center integrálása az Azure-ral riasztások jelentkezzen integrációs](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) – Ez a dokumentum azt ismerteti, hogyan szinkronizálása a Security Center riasztásait, együtt a virtuális gép biztonsági eseményeket a naplóelemzési az Azure Diagnostics és az Azure-beli Auditnaplók által gyűjtött vagy SIEM-megoldás.

## <a name="next-steps"></a>További lépések

- [Audit és naplózás](https://www.microsoft.com/trustcenter/security/auditingandlogging)

Adatok védelme a láthatóság fenntartásával, és gyorsan időben történő biztonsági riasztásokra való reagálásról

- [A biztonsági naplózás és a naplófájl naplózási Azure-ban](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)

Ki kell kényszerítenie az Azure-példányokon győződjön meg arról, hogy milyen beállításokat a megfelelő biztonsági gyűjtenek, és auditnaplókat.

- [Webhelycsoport naplózási beállításainak konfigurálása](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

Egy rendszergazdát egy kérheti le az adott felhasználó által végrehajtott műveletek előzményeit és az egy adott dátumtartományon belül alatt végrehajtott műveleteket előzményeit is lekérhet. 

- [A naplófájlban keresse az Office 365 biztonsági és megfelelőségi központ](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

Egy használhatja az Office 365 biztonsági és megfelelőségi központ a felhasználó és rendszergazda tevékenységének megtekintéséhez az Office 365 szervezet egyesített napló.


