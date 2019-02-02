---
title: Azure-naplózás és vizsgálat |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja fel a naplózási adatok révén mélyrehatóan elemezheti az alkalmazással kapcsolatos információk.
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
ms.date: 01/14/2019
ms.author: TomSh
ms.openlocfilehash: 93370b927f825752b8ce2b5e9d4ca94b3a4e0536
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55664182"
---
# <a name="azure-logging-and-auditing"></a>Azure-naplózás

Az Azure konfigurálható biztonsági vizsgálati és naplózási segít azonosítani a biztonsági házirendeket és mechanizmusokat megalkotni hiányosságok lehetőségek széles tárházát biztosítja. Ez a cikk ismerteti a generálása, összegyűjtése és az Azure-ban üzemeltetett szolgáltatások biztonsági naplóinak elemzése.

> [!Note]
> Ez a cikk az egyes javaslatok előfordulhat, hogy megnövekedett adat-, hálózati vagy számítási erőforrás-használatot eredményez, és növelje a licencek vagy előfizetések költségeit.

## <a name="types-of-logs-in-azure"></a>Az Azure-ban naplók típusait

A felhőalapú alkalmazások olyan összetett, a részek. Naplók nyújtanak adatokat annak érdekében, hogy az alkalmazások működőképességét. Naplók segítségével hibaelhárítása a korábbi hibákat, vagy megakadályozhatják, hogy a potenciális azokat. És javíthatja az alkalmazás teljesítménye vagy Karbantarthatóság, vagy ellenkező esetben manuális beavatkozást igénylő műveletek automatizálása segíthetnek.

Az Azure-naplók a következő típusú kategóriákba:
* **Ellenőrzés/felügyeleti naplók** Azure Resource Manager LÉTREHOZNI, frissítési és törlési műveleteket ismertetik. További információkért lásd: [Azure-Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Adatok adatsík naplók** , részben Azure-erőforrások használatára kiváltott események kapcsolatos adatok megadása. Az ilyen típusú log példák a Windows esemény rendszer, biztonság, és a egy virtuális gépet (VM) az alkalmazásnaplók, és a [diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) konfigurált Azure monitoron keresztül.

* **Események feldolgozása** elemzett eseményeket/riasztásokat feldolgozott kapcsolatos adatok megadása az Ön nevében. Példa az ilyen típusú [Azure Security Center riasztásainak](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) ahol [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) feldolgozása és elemzése az előfizetés és tömör biztonsági riasztásokat biztosít.

Az alábbi táblázat a legfontosabb típusú Azure-ban elérhető naplók:

| Naplókategória | Napló típusa | Használat | Integráció |
| ------------ | -------- | ------ | ----------- |
|[Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Az Azure Resource Manager-erőforrások vezérlősík események|   Az előfizetés erőforrásain végrehajtott műveletek betekintést nyújt.|    – REST API-t, [az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Azure-beli diagnosztikai naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Az előfizetés Azure Resource Manager-erőforrások vonatkozó gyakori adatokkal|    Biztosítja magán az erőforrás által végrehajtott műveletekkel kapcsolatos információk.| Az Azure Monitor [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Azure AD Reporting](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Naplók és jelentések | Felhasználói bejelentkezési tevékenységek jelentések és rendszertevékenység információk a felhasználók és csoportok kezelése.|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Virtuális gépek és felhőszolgáltatások](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Windows Eseménynapló szolgáltatás és a Linux Rendszernaplójából|  Rendszer és a virtuális gépek naplózása adatait rögzíti, és átviszi az adatokat tetszőleges storage-fiókra.|   Windows (Windows Azure Diagnostics használatával [[WAD](https://docs.microsoft.com/azure/azure-diagnostics)] tárolási) és a Linux az Azure monitorban|
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|A Storage naplózási, mérőszámadatokat biztosít a storage-fiók|Nyomkövetési kérelmek betekintést nyújt, elemzi a használati trendeket és a storage-fiókkal kapcsolatos problémák diagnosztizálása.|   REST API vagy a [ügyféloldali kódtár](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[Hálózati biztonsági csoport (NSG) folyamatnaplóit](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|JSON-formátumban, bejövő és kimenő forgalom a szabályonkénti jeleníti meg|Bejövő és kimenő IP-forgalom – a hálózati biztonsági csoport információit jeleníti meg.|[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Naplók, kivételek és egyéni diagnosztika|   Itt az alkalmazásteljesítmény-figyelési (APM) szolgáltatás webfejlesztőknek, több platformon.| REST API-t [Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|Adatok feldolgozása és a biztonsági riasztások|    Az Azure Security Center riasztásainak, Azure Log Analytics-riasztásokkal|   Biztonsági információkat és riasztásokat biztosít.|  REST API-k, JSON|

### <a name="activity-logs"></a>Tevékenységnaplók

[Azure-Tevékenységnaplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) az előfizetésében erőforrásokon végrehajtott műveletekkel kapcsolatos információk találhatók meg. Tevékenységnaplók is korábbi nevén "naplófájlok" vagy "műveleti naplók," jelentést, mert [vezérlősík események](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) az előfizetésekhez. 

Tevékenységnaplók meghatározhatja, hogy segítséget a "mit, ki és mikor" az írási műveletek (PUT, közzététele és törlése). A Tevékenységnaplók is segítenek megérteni, hogy a művelet és az egyéb releváns tulajdonságok állapotát. Tevékenységnaplók az olvasási (GET) műveleteket nem tartalmazzák.

Ebben a cikkben PUT, POST és DELETE tekintse meg az írási műveleteket egy tevékenységnapló tartalmazza az erőforrásokon. Használhatja például a tevékenységnaplókat, hibát talál problémák elhárításakor Ön vagy egy a szervezet felhasználói hogyan módosították az erőforrásokat figyelésére.

![Tevékenységdiagram napló](./media/azure-log-audit/azure-log-audit-fig1.png)

Beolvasható események egy tevékenységnapló használatával az Azure Portalon [Azure CLI-vel](https://docs.microsoft.com/azure/storage/storage-azure-cli), PowerShell-parancsmagok és [Azure Monitor REST API](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). A Tevékenységnaplók 90 napos adatmegőrzési időszakot rendelkezik.

Egy tevékenységnapló eseményéhez integrációs forgatókönyvek:

* [Hozzon létre egy e-mailben vagy webhook riasztást egy tevékenységnapló eseményéhez által aktivált](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Az eseményközpontok felé, Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) egy külső szolgáltatás vagy az egyéni elemzési megoldással, például a Power bi támogatunk.

* Elemezheti a Power bi használatával a [Power bi-tartalomcsomag](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Mentse azt egy tárfiókot, archiválási vagy manuális ellenőrzést](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). Naplóprofilok használatával a megőrzési időtartam (napban) is megadhat.

* Lekérdezés, és megtekintheti azokat az Azure Portalon.

* PowerShell-parancsmag, az Azure CLI vagy a REST API-n keresztül lekérdezéseket futtathat rajta.

* A naplóprofilok, a tevékenységnapló exportálása [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Használhatja a storage-fiók vagy [eseményközpont-névtér](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) ez nem ugyanahhoz az előfizetéshez tartozik, mint amelyet az előfizetéseknek a naplót. A megfelelő személy, aki konfigurálja a beállítást kell [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) mindkét előfizetéshez való hozzáférés.

### <a name="azure-diagnostics-logs"></a>Azure-beli diagnosztikai naplók

Azure-beli diagnosztikai naplók projektsablon által biztosított a gazdag, gyakori adatait a műveletet az erőforrás erőforrás által kibocsátott. Ezek a naplók a tartalom erőforrás típusa szerint változó. Például [Windows rendszer-eseménynaplói](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) vannak a diagnosztikai naplók egy kategóriát a virtuális gépek esetében, és [blob, tábla és üzenetsor-naplók](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) kategóriába sorolhatók diagnosztikai naplók tárfiókok esetében. Diagnosztikai naplók különböznek a tevékenységnaplóktól, amelyek az előfizetésében erőforrásokon végrehajtott műveletekkel kapcsolatos információk találhatók meg.

![Azure diagnostics-naplók diagramok](./media/azure-log-audit/azure-log-audit-fig2.png)

Azure-beli diagnosztikai naplók több konfigurációs beállítások, például az Azure portal, PowerShell, az Azure CLI és a REST API-t kínálnak.

**Integrációs forgatókönyvek**

* Menti azokat egy [tárfiók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) naplózási vagy manuális ellenőrzést. A megőrzési időtartam (napban) is megadhat a diagnosztikai beállítások használatával.

* [Stream őket az event hubs szolgáltatásba](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) támogatunk egy külső szolgáltatás vagy az egyéni elemzési megoldás, mint például [Power bi](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Elemezheti a [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Támogatott szolgáltatások, a diagnosztikai naplók és a támogatott naplókategóriák erőforrásonként séma írja be a következőket:**


| Szolgáltatás | Séma- és dokumentáció | Erőforrás típusa | Kategória |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [A log Analytics Load Balancer (előzetes verzió)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers| LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|Network Security Groups (Hálózati biztonsági csoportok)|[Naplóelemzés hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Azure Application Gateway|[Az Application Gateway diagnosztikai naplózás](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Azure Key Vault|[Key Vault-naplók](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Azure Search|[Engedélyezéséről és használatáról a forgalmi elemzések keresése](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Azure Data Lake Store|[A Data Lake Store diagnosztikai naplók elérése](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Naplózás<br>Kérelmek|
|Azure Data Lake Analytics|[A Data Lake Analytics diagnosztikai naplók elérése](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Naplózás<br>Kérelmek|
|Azure Logic Apps|[Logic Apps B2B egyéni követési séma](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|Kontejner Typu<br>IntegrationAccountTrackingEvents|
|Azure Batch|[Az Azure diagnosztikai naplók a Batchben](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Azure Automation|[Naplóelemzés az Azure Automation](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Azure Event Hubs|[Event Hubs – diagnosztikai naplók](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Feladat-diagnosztikai naplók](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Futtatási<br>Szerzői műveletek|
|Azure Service Bus|[A Service Bus-diagnosztikai naplók](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Jelentéskészítés az Azure Active Directoryban

Az Azure Active Directory (Azure AD) biztonsági, a tevékenység és a egy felhasználó címtár auditálási jelentéseket tartalmazza. A [az Azure AD naplózási jelentés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) segítségével azonosíthatja, hogy a felhasználó Azure AD-példányt a következő privilegizált műveletek. Privilegizált műveletek közé tartozik a jogosultságszint-emelés (például a szerepkör létrehozása vagy a jelszó alaphelyzetbe állítása), változó szabályzatkonfiguráció (például jelszó-szabályzatokat), és változásainak a könyvtár-konfiguráció (például tartomány összevonási beállításainak módosítása).

A jelentések az esemény nevét, a felhasználó hajtotta végre a műveletet, a célként megadott erőforrás érinti a változás és dátuma és időpontja (UTC) adja meg a naplórekordot. Felhasználók is naplóesemények listájának beolvasása az Azure ad-n keresztül a [az Azure portal](https://portal.azure.com/)leírtak szerint [az auditnaplók megtekintésére](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

A csomagban foglalt jelentések az alábbi táblázatban láthatók:

| Biztonsági jelentések | Tevékenységjelentések | Naplózási jelentések |
| :--------------- | :--------------- | :------------ |
|Bejelentkezések ismeretlen forrásokról| Alkalmazáshasználat: összegzés| Címtárnaplózási jelentés|
|Több hibát követő bejelentkezések|  Alkalmazáshasználat: részletes||
|Bejelentkezések különböző földrajzi régiókból|    Alkalmazás irányítópultja||
|Bejelentkezések gyanús tevékenységeket mutató IP-címekkel|   Alkalmazás-kiépítési hibák||
|Rendszertelen bejelentkezési tevékenység|    Egyéni felhasználói eszközök||
|Bejelentkezések potenciálisan fertőzött eszközökről|   Egyéni felhasználói tevékenység||
|Rendellenes bejelentkezési tevékenységet mutató felhasználók| Csoporttevékenység-jelentés||
||Jelszó-visszaállítási regisztrációs Tevékenységjelentés||
||Jelszó-visszaállítási tevékenység|||

Ezek a jelentések adatait az alkalmazások, például biztonságiadat- és eseménykezelés (SIEM) rendszerek, naplózási és üzletiintelligencia-eszközök hasznos lehet. Az Azure AD-jelentéskészítés API-k REST-alapú API-kon keresztül biztosítják az adatok szoftveres hozzáférését. Ezek segítségével meghívhatja [API-k](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) különböző programozási nyelveket és az eszközök.

Az Azure AD naplózási jelentés eseményei 180 napig maradnak meg.

> [!Note]
> A jelentés adatmegőrzési kapcsolatos további információkért lásd: [az Azure AD-jelentések adatmegőrzési szabályzatában](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Érdekli a naplózási események hosszabb megőrzése, ha használható a Reporting API rendszeresen lekéréses [események naplózása](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) egy külön tárolóba.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Virtuális gépek naplóinak, amely az Azure diagnosztikai szolgáltatásával

[Az Azure Diagnostics](https://docs.microsoft.com/azure/azure-diagnostics) a képesség, amely lehetővé teszi diagnosztikai adatgyűjtés egy telepített alkalmazást az Azure-ban. A diagnosztikai bővítmény bármelyik forrásokból is használhatja. A rendszer jelenleg támogatott [Azure felhőalapú szolgáltatás webes és feldolgozói szerepkörök](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Virtuális gépek naplóinak, amely az Azure diagnosztikai szolgáltatásával](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineslearnpathsdeploy-a-website-with-azure-virtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Az Azure virtual machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/) Microsoft Windows rendszerű és [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)

Azure Diagnostics a következő tevékenységek végrehajtásával engedélyezheti a virtuális gépen:

* [A Visual Studio használatával nyomon követése az Azure-beli virtuális gépek](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Azure Diagnostics távolról beállítása egy Azure virtuális gépen](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Azure-beli virtuális gépeken diagnosztika beállítása a PowerShell használatával](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Windows virtuális gép létrehozása figyelési és diagnosztikai funkciókkal Azure Resource Manager-sablon használatával](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Storage Analytics

[Az Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) naplózza, és mérőszámadatokat biztosít egy tárfiókot. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat. A Storage Analytics naplózási érhető el a [Azure Blob, az Azure Queue és Azure Table storage szolgáltatás](https://docs.microsoft.com/azure/storage/storage-introduction). A Storage Analytics naplók sikeres és sikertelen kérések kapcsolatos részletes információk egy tárolási szolgáltatásba.

Ezt az információt az egyes kérelmek figyelése és a egy storage szolgáltatással kapcsolatos problémák diagnosztizálásához használható. Kérelmek van bejelentkezve a képességeinkhez mérten biztosítjuk. Naplóbejegyzések jönnek létre, csak ha a szolgáltatásvégpont kérelmekre. Például ha a tárfiók a blob végpontja, de nem a tábla vagy üzenetsor végpontjait, csak a Blob storage szolgáltatás beállításokhoz tartozó naplók jönnek létre.

A Storage Analytics használatához engedélyezze külön-külön mindegyik figyelni kívánt szolgáltatás. A engedélyezheti a [az Azure portal](https://portal.azure.com/). További információkért lásd: [monitorozása az Azure Portal tárfiók](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). A Storage Analytics automatizáltan a REST API-t vagy az ügyféloldali kódtár is engedélyezheti. A szolgáltatás tulajdonságainak beállítása műveletet használja ahhoz, hogy a Storage Analytics külön-külön az egyes szolgáltatások.

Az összesített adatokat tárolja egy jól ismert blob (naplózás) és a jól ismert táblák (a metrikákat), amely a Blob storage szolgáltatás és a Table storage szolgáltatás API-k használatával érhető el.

A Storage Analytics esetében a 20-terabájt (TB), amely független a tárfiók teljes maximális tárolt adatok mennyisége. Az összes napló vannak tárolva [blokkblobok](https://docs.microsoft.com/azure/storage/storage-analytics) $logs nevű tárolóban, amely automatikusan létrejön, amikor engedélyezi a Storage Analytics egy storage-fiókot.

> [!Note]
> * Számlázással és az adatmegőrzési házirendek kapcsolatos további információkért lásd: [Storage Analytics és a számlázás](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Információ a tárfiókok korlátairól további információkért lásd: [Azure Storage méretezhetőségi és teljesítménycéljai](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

A Storage Analytics naplók a következő típusú hitelesített és névtelen kérések:

| Hitelesített  | Névtelen|
| :------------- | :-------------|
| Sikeres kérelmek | Sikeres kérelmek |
|Sikertelen kérelmek, beleértve az időkorlát, a szabályozás, hálózati, engedélyezési és egyéb hibák | Közös hozzáférésű jogosultságkódot, beleértve a sikeres és sikertelen kérések-kérések |
| Közös hozzáférésű jogosultságkódot, beleértve a sikeres és sikertelen kérések-kérések |Az ügyfél és kiszolgáló egyaránt időtúllépési hibák |
|   Elemzési adatok kérelmeket |    Sikertelen GET kérelmek 304 (nem módosított). Hibakód: |
| Kérést hoz létre a Storage Analytics, például a napló a létrehozás vagy törlés, a program nem naplózza. A naplózott adatok teljes listáját a dokumentált [Storage Analytics naplózott műveletek és az állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) és [Storage Analytics naplóformátum](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Az összes többi sikertelen névtelen kérelmek nem jelentkezett. A naplózott adatok teljes listáját a dokumentált [Storage Analytics naplózott műveletek és az állapotüzenetek](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) és [Storage Analytics naplóformátum](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Az Azure hálózati naplók

A hálózati naplózás és figyelés az Azure-ban átfogó, és két tág kategóriába ismerteti:

* [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Forgatókönyv-alapú hálózatfigyelési biztosítunk a Network Watcher funkciókat. A szolgáltatás része, csomagrögzítés, következő ugrási, IP-folyamat ellenőrzése, a biztonsági csoport nézet NSG-Folyamatnaplók. Forgatókönyv szintű monitorozása biztosítja a teljes körű képet szakembereket egyes hálózati erőforrások monitorozása a hálózati erőforrásokhoz.

* [Erőforrások monitorozása](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring): Erőforrás-szolgáltatói figyelése magában foglalja a négy funkciók, diagnosztikai naplók, metrikák, hibaelhárítási és a resource health. Ezek a szolgáltatások beépített a hálózati erőforrás szintjén.

![Az Azure hálózati naplók](./media/azure-log-audit/azure-log-audit-fig4.png)

A Network Watcher egy regionális szolgáltatás, amely lehetővé teszi a figyelési és diagnosztizálási tevékenységet végezhet hálózati forgatókönyvek szintjén, a, és az Azure-ból. Hálózati diagnosztikai és vizualizációs eszközök a Network Watcherrel elérhető segítenek megérteni, diagnosztizálása és betekintést nyerhet az Azure-ban a hálózaton.

### <a name="network-security-group-flow-logging"></a>Hálózati biztonsági csoportforgalom naplózása

[NSG-Folyamatnaplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) érhetők el, hogy egy NSG-n keresztül bejövő és kimenő IP-forgalom kapcsolatos információk megtekintéséhez használhatja a Network Watcher. A folyamat-naplók JSON formátumban íródtak, és megjelenítése:
* A szabályonkénti kimenő és bejövő forgalom.
* A hálózati Adaptert, amely a folyamat vonatkozik.
* a folyamat 5-ször több információt: a forrás vagy cél IP-cím, a forrás vagy cél port és a protokoll.
* Hogy a forgalom engedélyezett vagy tiltott.

Bár a Folyamatnaplók cél NSG-k, azok nem jelennek ugyanúgy, mint a többi naplófájlt. Forgalmi naplók tárolják, csak a tárfiókon belül.

Az azonos adatmegőrzési házirendek, amelyek a többi naplók láthatók Folyamatnaplók vonatkoznak. Naplókat kell egy adatmegőrzési szabályt, amely lehet 1 nap – 365 nap. Ha nincs beállítva adatmegőrzési szabály, a naplók megőrzése korlátlan időre szól.

**Diagnosztikai naplók**

Rendszeres és spontán események hálózati erőforrások által létrehozott és a storage-fiókok naplózza, és küldött egy eseményközpontba és a Log Analytics. A naplókban az erőforrás állapotának betekintést nyújtson. Azok az eszközök, például a Power bi-ban és a Log Analytics is megtekinthetők. Diagnosztikai naplók megtekintése kapcsolatban lásd: [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Diagnosztikai naplók](./media/azure-log-audit/azure-log-audit-fig5.png)

Diagnosztikai naplók érhetők el [terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), útvonalakat, és [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

A Network Watcher biztosít a diagnosztikai naplók megtekintése. Ez a nézet az összes hálózati erőforrások, amelyek támogatják a diagnosztikai naplózás tartalmazza. Ebben a nézetben engedélyezheti és letilthatja a hálózati erőforrásokat, gyorsan és kényelmesen.


A korábban említett naplózási képességek mellett a Network Watcher jelenleg a következő képességekkel rendelkezik:
- [Topológia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): Lehetővé teszi a hálózati szintű, amely megjeleníti az egyes csatlakozás és a egy erőforráscsoportba tartozó hálózati erőforrások egymáshoz rendelését.

- [Változó csomagrögzítés](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Rögzíti a csomagadatok adataikkal egy virtuális gépet. Maximálja a sokoldalúságot speciális szűrési beállítások és finomhangolási vezérlők, például idő - és méretének korlátozása beállításait, adja meg. A csomag adatokat tárolhatja a blob-tárolóban, vagy a helyi lemezen lévő *.cap* fájlformátum.

* [IP-folyamat ellenőrzése](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): Ellenőrzi, hogy egy csomag engedélyezett vagy tiltott (azt jelenti, cél IP-cím, forrásoldali IP-cím, Célport, forrásoldali portszám és protokoll) folyamat információk 5-ször több csomag paraméterei alapján. A csomagot a rendszer megtagadja egy biztonsági csoport is, ha a szabály és a csoportot, amely a csomagot ad vissza.

* [A következő Ugrás](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): Bármely helytelenül konfigurált felhasználó által megadott útvonalak diagnosztizálhatja, hogy meghatározza, hogy a következő ugrás a csomagok irányítása az Azure-beli hálózati hálóban.

* [Biztonsági csoport nézet](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): A hatékony és alkalmazott biztonsági szabályok, amelyek érvényesek a virtuális gép beolvasása.

* [Virtuális hálózati átjáró és a kapcsolat hibaelhárítási](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Segít a virtuális hálózati átjárók és kapcsolatok hibaelhárítása.

* [Hálózati előfizetési korlátozásait](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits): Lehetővé teszi, hogy a hálózati erőforrás-használati korlátozások megtekintése.

### <a name="application-insights"></a>Application Insights

[Az Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) egy bővíthető APM szolgáltatás webfejlesztőknek, több platformon. Ezzel élő webalkalmazások figyeléséhez. Automatikusan felismeri a teljesítményanomáliákat. Hatékony elemzőeszközöket tartalmaz, amelyek segítenek a problémák felismerésében, és annak a megértésében, hogy a felhasználók mire használják ténylegesen az alkalmazást.

Az Application Insights célja folyamatosan javíthassa a teljesítményt és a használhatóságot.

Az alkalmazások számos különböző platformokról, többek között a .NET, Node.js és a j2ee-alapú, akár a helyszínen üzemeltetett, vagy a felhőben működik. Ez a DevOps folyamattal integrálható, és rendelkezik kapcsolódási ponttal és a különböző fejlesztői eszközök.

![Application Insights-diagram](./media/azure-log-audit/azure-log-audit-fig6.png)

A fejlesztőcsapatoknak készült Application Insights segít az adott alkalmazás teljesítményének megismerésében, valamint abban, hogy a felhasználók miként használják az alkalmazást. A szolgáltatás az alábbiakat figyeli:

* **Kérelem értékek, válaszidők és hibaarányok**: Ismerje meg, hogy mely lapok töltődnek legnépszerűbb, mely napszakokban, és hol találhatók a felhasználók. Megtekintheti, hogy mely lapok teljesítenek a legjobban. Ha a válaszidők és hibaarányok go magas, ha vannak további kérelmek, előfordulhat, hogy problémába resourcing.

* **Függőségi értékek, válaszidők és hibaarányok**: Ismerje meg, hogy külső szolgáltatások okoznak lassulást.

* **Kivételek**: Elemezheti az összesített statisztikákat, vagy konkrét példányok, és részletesen a hívásláncot és a kapcsolódó kéréseket. A kiszolgálói és a böngészői kivételekről egyaránt készül jelentés.

* **Lapmegtekintések és betöltési teljesítmény**: Jelentések lekérése a felhasználók böngészőinek.

* **AJAX-hívások**: Weblap-értékek, válaszidők és hibaarányok beolvasása.

* **Felhasználók és munkamenetek száma**.

* **Teljesítményszámlálók**: Adatok lekérése a Windows vagy Linux rendszerű kiszolgálói gépekről származó, például a Processzor, memória és a hálózati forgalom.

* **Gazdadiagnosztika**: Adatok lekérése a Docker vagy az Azure.

* **Diagnosztikai nyomkövetési naplók**: Adatok beolvasása az alkalmazásból, hogy a nyomkövetési események összehasonlíthatja a kérésekkel.

* **Egyéni események és mérőszámok**: Adatok, amelyeket Ön írt az ügyfél vagy kiszolgáló kódjában, és ezáltal üzleti eseményeket követhet, például eladott tételek vagy megnyert játékok beolvasása.

A következő táblázat sorolja fel, és ismerteti az integrációs forgatókönyveket:

| Integrációs forgatókönyve | Leírás |
| --------------------- | :---------- |
|[Alkalmazástérkép](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Az alkalmazás összetevői, alapvető metrikákkal és riasztásokkal.||
|[Diagnosztikai keresés például adatok](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Események keresése és szűrése, például kérések, kivételek, függőségi hívások, naplókivonatok és lapmegtekintések.||
|[Összesített adatok Metrikaböngészője](https://docs.microsoft.com/azure/azure-monitor/app/metrics-explorer)|Összesített adatok – például kérés- és hibaarányok, valamint kivételek, válaszidők és lapbetöltési idők – böngészése, szűrése és szegmentálása.||
|[Irányítópultok](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Különböző erőforrásokból származó adatokat fűzhet össze és oszthat meg másokkal. Kiváló megoldás több összetevőből álló alkalmazások, valamint csoportszobában való folyamatos megjelenítés esetén.||
|[Live Metrics Stream](https://docs.microsoft.com/azure/azure-monitor/app/live-stream)|Új buildverzió telepítésekor közel valós idejű teljesítménymutatókat figyelhet, és meggyőződhet arról, hogy minden megfelelő módon működik-e.||
|[Elemzés](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|A hatékony lekérdezési nyelvnek köszönhetően válaszokat kaphat az alkalmazás teljesítményére és használatára vonatkozó legégetőbb kérdésekre.||
|[Automatikus és manuális riasztások](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Automatikus értesítést az alkalmazás normál telemetriai utaló alkalmazkodni és jelzik, ha valami kívül a szokásos minta. Riasztásokat állíthat be az egyéni vagy normál metrikák adott szintjeire is.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|A kód teljesítményadatainak megjelenítéséhez. A hívásláncokból a kódba ugorhat.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Integrálhatja a használati metrikákat más üzleti intelligenciával.||
|[REST API](https://dev.applicationinsights.io/)|Kód írásával lekérdezéseket futtathat a metrikákhoz és a nyers adatokhoz kapcsolódóan.||
|[Folyamatos exportálás](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Tárolási, amikor megérkeznek a nyers adatok tömeges exportálását.||

### <a name="azure-security-center-alerts"></a>Az Azure Security Center-riasztások

Az Azure Security Center fenyegetésészlelése úgy, hogy automatikusan összegyűjti a biztonsági információkat az Azure-erőforrások, a hálózat és a csatlakoztatott partnermegoldások működik. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket. A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására. További információkért lásd: [az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Az Azure Security Center diagramja](./media/azure-log-audit/azure-log-audit-fig7.png)

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A nagy mennyiségű adat vívmányok vonatkozik, és [gépi tanulás](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák ki az események között a teljes felhőalapú hálóban. Ily módon a lehetetlen volna azonosítani manuális módszerekkel és a támadások fejlődésének előrejelzésére szolgáló észlel. Ezek a biztonsági elemzések a következők:

* **Integrált fenyegetésészlelési intelligencia**: Megkeresi az ismert, a Microsoft-termékek és szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft Security Response Center (MSRC), és külső hírcsatornák globális fenyegetésészlelési intelligencia alkalmazásával.

* **Viselkedéselemzés**: Ismert minták alapján deríti fel a rosszindulatú működést alkalmazza.

* **anomáliadetektálás**: Statisztikai adatokon alapuló profilok használatával összeállít egy kiinduló alapkonfigurációt. Jelzi a meghatározott alapkonfigurációktól való olyan eltéréseket, amelyek potenciális támadásként értelmezhetők.

Számos biztonsági műveletek és az incidensmegoldási csapat támaszkodik egy SIEM-megoldás kiindulási pontként sorolása és kivizsgálása biztonsági riasztásokat. Az Azure Log Integration szinkronizálhatja a Security Center riasztásainak és a virtuális gép biztonsági eseményekről, az Azure diagnosztikai és vizsgálati naplókat, a közel valós időben a Log Analytics vagy az SIEM-megoldás által összegyűjtött.

## <a name="log-analytics"></a>Log Analytics

A log Analytics szolgáltatása az Azure-ban, amely segít összegyűjteni és elemezni az adatokat, amely a felhőbeli erőforrások által létrehozott és a helyszíni környezetekben. Biztosít a valós idejű elemzések által integrált keresés és az egyéni irányítópultok segítségével azonnal elemezze a rekordok millióit, a számítási feladatok és a kiszolgálók, függetlenül azok fizikai helyétől.

![Log Analytics-diagram](./media/azure-log-audit/azure-log-audit-fig8.png)

A Log Analytics középpontjában az a Log Analytics-munkaterület, amely az Azure-ban. A log Analytics gyűjti az adatokat a munkaterületen a csatlakoztatott források által az adatforrások konfigurálása és megoldások hozzáadása az előfizetéséhez. Adatforrások és megoldások létrehozni különböző rekordtípusok, mindegyik a saját tulajdonságkészlettel. Azonban az adatforrások és megoldások továbbra is elemezhetők együtt a munkaterület lekérdezésekben. Ez a funkció lehetővé teszi, hogy ugyanazokat az eszközöket és módszereket a különböző forrásokból gyűjtött adatok különböző működjön.

A csatlakoztatott források azok a számítógépek és más erőforrások, amelyek a Log Analytics által összegyűjtött adatokat generálnak. Források ilyenek lehetnek például a telepített [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) és [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) közvetlenül csatlakozó számítógépek vagy az ügynökök [csatlakoztatott System Center Operations Manager felügyeleti csoport](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). A log Analytics is gyűjthet adatokat a egy [Azure storage-fiók](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Adatforrások](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) vannak az egyes csatlakoztatott forrásokból gyűjtött adatok típusait. Adatforrások többek között az események és [teljesítményadatok](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) a [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) és a Linux-ügynököktől, forrásokat, mint [IIS-naplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) és [egyéni szövegnaplók](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Konfigurálhatja az összegyűjteni kívánt egyes adatforrásokat, és a konfiguráció automatikusan érvénybe lép minden egyes csatlakoztatott forráson.

Többféleképpen négy [gyűjtése a naplók és mérőszámok az Azure-szolgáltatásokhoz](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage):

* A Log Analyticshez való közvetlen az Azure Diagnostics (**diagnosztikai** az alábbi táblázatban)

* A Log Analytics az Azure storage-bA az Azure Diagnostics (**tárolási** az alábbi táblázatban)

* Összekötők az Azure-szolgáltatásokhoz (**összekötő** az alábbi táblázatban)

* Parancsfájlok, összegyűjtése és majd ezeket az adatokat a Log analyticsbe (üres cellákat az alábbi táblázatban és -szolgáltatásokhoz, amelyek nem szerepelnek a listán)

| Szolgáltatás | Erőforrás típusa | Logs | Mérőszámok | Megoldás |
| :------ | :------------ | :--- | :------ | :------- |
|Azure Application Gateway| Microsoft.Network/<br>applicationGateways|  Diagnosztika|Diagnosztika|    [Az Azure Application](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Alkalmazásátjáró-elemzési](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Összekötő|  Összekötő|  [Az Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [összekötő (előzetes verzió)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Azure Automation-fiókok| Microsoft.Automation/<br>AutomationAccounts|    Diagnosztika||       [További információ](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Az Azure Batch-fiókok|  Microsoft.Batch/<br>batchAccounts|  Diagnosztika|    Diagnosztika||
|Klasszikus a cloud services||       Storage||       [További információ](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>fiókok|       Diagnosztika|||
|Azure Data Lake Analytics| Microsoft.DataLakeAnalytics/<br>fiókok|   Diagnosztika|||
|Azure Data Lake Store| Microsoft.DataLakeStore/<br>fiókok|   Diagnosztika|||
|Azure Event Hub namespace| Microsoft.EventHub/<br>névterek|  Diagnosztika|    Diagnosztika||
|Azure IoT Hub| Microsoft.Devices/<br>IotHubs||     Diagnosztika||
|Azure Key Vault|   Microsoft.KeyVault/<br>tárolók|  Diagnosztika  || [Key Vault-elemzés](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Diagnosztika|||
|Azure Logic Apps|  Microsoft.Logic/<br>A munkafolyamatok|  Diagnosztika|    Diagnosztika||
||Microsoft.Logic/<br>integrationAccounts||||
|Network Security Groups (Hálózati biztonsági csoportok)|   Microsoft.Network/<br>networksecuritygroups|Diagnosztika||   [Azure-beli hálózati biztonsági csoport analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Helyreállítási tárak|   Microsoft.RecoveryServices/<br>tárolók|||[Az Azure Recovery Services-Analytics (előzetes verzió)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Szolgáltatások keresése|   Microsoft.Search/<br>searchServices|    Diagnosztika|    Diagnosztika||
|Service Bus-névtér| Microsoft.ServiceBus/<br>névterek|    Diagnosztika|Diagnosztika|    [Service Bus-elemzés (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Storage||    [Service Fabric-elemzés (előzetes verzió)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>kiszolgálók /<br>adatbázisok||       Diagnosztika||
||Microsoft.Sql/<br>kiszolgálók /<br>elasticPools||||
|Storage|||         Szkript| [Az Azure Storage Analytics (előzetes verzió)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Azure-alapú virtuális gépek|    Microsoft.Compute/<br>virtuális gép|  Mellék|  Mellék||
||||Diagnosztika||
|Virtuálisgép-méretezési csoportok|    Microsoft.Compute/<br>virtuális gép    ||Diagnosztika||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtuális gép||||
|Webkiszolgálófarmok|Microsoft.Web/<br>serverfarms||   Diagnosztika
|Webhelyek|  Microsoft.Web/<br>Helyek ||      Diagnosztika|    [További információ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>webhelyek /<br>tárhelyek|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>A helyszíni SIEM rendszerekbe a napló integrációja

Az Azure Log Integration integrálható nyers naplók az Azure-erőforrások, a helyi SIEM-rendszerbe (információk és az Információbiztonsági rendszer). 2018. június 27. AzLog letöltések letiltottuk. Mi a teendő mozgatása előre tekintse át a hozzászólás útmutatást [SIEM-eszközök integrálása az Azure monitor](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

![Napló integrációja diagramja](./media/azure-log-audit/azure-log-audit-fig9.png)

Napló integrációja az Azure diagnostics gyűjti össze a Windows virtuális gépek, Azure-Tevékenységnaplók, az Azure Security Center riasztásainak és Azure-erőforrás a szolgáltató naplóiban találhatók. Ez az integráció, hogy azok a helyszínen vagy a felhőben, így összesítheti, összekapcsolását, elemzése és a biztonsági eseményekhez riasztási kínál az eszközök, az egységesített irányítópult.

Napló integrációja jelenleg támogatja az Azure-Tevékenységnaplók integrációja, a Windows-eseménynaplók Windows virtuális gépek az Azure-előfizetéssel, az Azure Security Center riasztásait, Azure-beli diagnosztikai naplók és az Azure AD-naplók.

| Napló típusa | A log Analytics JSON (Splunk ArcSight és az IBM QRadar) támogatása |
| :------- | :-------------------------------------------------------- |
|Az Azure AD-vizsgálati naplók|   Igen|
|Tevékenységnaplók| Igen|
|A Security Center-riasztások |Igen|
|Diagnosztikai naplók (erőforrás-naplók)|  Igen|
|Virtuális gépek naplóinak|   Igen, és nem a JSON keresztül továbbított események|

[Ismerkedés az Azure Log Integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started): Ez az oktatóanyag végigvezeti az Azure Log Integration telepítése, és integrálja az Azure storage-ból naplók, Azure-Tevékenységnaplók, az Azure Security Center riasztásainak és az Azure AD auditnaplóinak.

SIEM integrációs forgatókönyvek:

* [Partner-konfigurációs lépések](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): Ebben a blogbejegyzésben bemutatja, hogyan konfigurálja az Azure Log Integration partnermegoldások Splunk, HP ArcSight és az IBM QRadar dolgozhat.

* [Gyakori kérdések az Azure Log Integration](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq): Ez a cikk az Azure Log Integration kapcsolatos kérdésekre ad választ.

* [A Security Center riasztásainak integrálása az Azure Log Integration](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration): Ez a cikk ismerteti, hogyan lehet szinkronizálni a Security Center riasztásait, virtuális gép biztonsági események Azure-beli diagnosztikai naplók, által gyűjtött, és az Azure vizsgálati naplók a Log Analytics vagy az SIEM-megoldással.

## <a name="next-steps"></a>További lépések

- [A vizsgálati és naplózási](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview): Láthatóság fenntartásával, és gyorsan válaszol a megfelelő időben a biztonsági riasztások az adatok védelmére.

- [Biztonsági naplózás és a napló gyűjtemény Azure-ban](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/): Győződjön meg arról, hogy az Azure-példányain a megfelelő biztonsági és vizsgálati naplókat gyűjti ezeket a beállításokat kényszeríthetnek.

- [Egy naplózási beállítások konfigurálása](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US): Ha Ön egy helygyűjtemény rendszergazdája, beolvasni az egyes felhasználók műveletek előzményeit és a egy adott dátumtartományban alatt végrehajtott műveleteket előzményeit. 

- [Naplókeresés az Office 365 biztonsági és megfelelőségi központban](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US): Az Office 365 Security & Compliance Centerben használatával egységes Naplókeresés, és megtekintheti a felhasználói és rendszergazdai tevékenységeket a szervezet Office 365-höz.


