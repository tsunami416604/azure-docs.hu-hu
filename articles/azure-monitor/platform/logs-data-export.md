---
title: Log Analytics munkaterület-adatexportálás Azure Monitorban (előzetes verzió)
description: Log Analytics adatexportálás lehetővé teszi a kijelölt táblák adatainak folyamatos exportálását a Log Analytics munkaterületről egy Azure Storage-fiókba vagy az Azure-Event Hubsba a gyűjtött adatok alapján.
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions
author: bwren
ms.author: bwren
ms.date: 10/13/2020
ms.openlocfilehash: 59febbac1a83e45c8b2bf9c233c3772f561eb111
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049841"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Log Analytics munkaterület-adatexportálás Azure Monitorban (előzetes verzió)
Log Analytics munkaterület-adatexportálás Azure Monitor lehetővé teszi, hogy folyamatosan exportálja a Log Analytics munkaterület kijelölt tábláiból származó adatokat egy Azure Storage-fiókba vagy az Azure-Event Hubsba az összegyűjtött adatok alapján. Ez a cikk részletesen ismerteti ezt a funkciót, valamint az adatexportálás konfigurálásának lépéseit a munkaterületeken.

## <a name="overview"></a>Áttekintés
Miután az adatexportálás be van állítva a Log Analytics munkaterületre, a munkaterület kiválasztott tábláiba eljuttatott új adategységeket a rendszer óránként vagy az Event hub-ba automatikusan exportálja a Storage-fiókba, közel valós időben.

![Az adatexportálás áttekintése](media/logs-data-export/data-export-overview.png)

A befoglalt táblákból származó összes adatok szűrő nélkül exportálhatók. Ha például egy adatexportálási szabályt állít be a *SecurityEvent* táblához, a rendszer a *SecurityEvent* táblába érkező összes, a konfigurációs időpontból kiinduló adattípust exportálja.


## <a name="other-export-options"></a>Egyéb exportálási lehetőségek
Log Analytics munkaterület-adatok exportálásával folyamatosan exportálhatja az adatok Log Analytics munkaterületről. Az egyes forgatókönyvekben az adatok exportálásának egyéb lehetőségei a következők:

- Ütemezett exportálás egy logikai alkalmazás használatával a log lekérdezésből. Ez hasonló az adatexportálási szolgáltatáshoz, de lehetővé teszi, hogy szűrt vagy összesített adatokat küldjön az Azure Storage-ba. Ez a metódus azonban a [naplózási lekérdezési korlátokra](../service-limits.md#log-analytics-workspaces)  vonatkozik, lásd: [log Analytics munkaterületről származó adatok archiválása az Azure Storage-ba a Logic App használatával](logs-export-logic-app.md).
- Egyszeri exportálás logikai alkalmazás használatával. [A Logic apps és a Power automatizálás Azure monitor naplók összekötője](logicapp-flow-connector.md)című témakörben talál.
- Egyszeri exportálás a helyi gépre PowerShell-parancsfájl használatával. Lásd: [meghívás-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="current-limitations"></a>Aktuális korlátozások

- A konfigurációt jelenleg csak parancssori felület vagy REST-kérelmek használatával lehet elvégezni. A Azure Portal vagy a PowerShell nem használható.
- A támogatott táblázatok jelenleg csak az alábbi (#supported-Angela) szakaszban meghatározottak. Ha az adatexportálási szabály nem támogatott táblát tartalmaz, akkor a művelet sikeres lesz, de a rendszer nem exportálja az adott táblára vonatkozó adatvesztést. Ha az adatexportálási szabály olyan táblát tartalmaz, amely nem létezik, akkor a hiba a * <tableName> munkaterületen nem létezik.*
- A Log Analytics munkaterület a következők kivételével bármely régióban lehet:
  - Észak-Svájc
  - Nyugat-Svájc
  - Azure Government-régiók
- A célként megadott Storage-fióknak vagy az Event hub-nek ugyanabban a régióban kell lennie, mint a Log Analytics munkaterületnek.
- Az exportálandó táblázatok neve nem lehet hosszabb 60 karakternél egy Storage-fióknál, és legfeljebb 47 karakterből állhat az Event hub-ban. A hosszú névvel rendelkező táblákat a rendszer nem exportálja.

> [!NOTE]
> Log Analytics az adatexportálás olyan hozzáfűzési blobként írja az adatot, amely jelenleg előzetes verzióban érhető el a Azure Data Lake Storage Gen2. A tárolóba való exportálás konfigurálása előtt meg kell nyitnia egy támogatási kérést. A kérelemhez használja a következő adatokat.
> - Problématípus: Technikai
> - Előfizetés: az Ön előfizetése
> - Szolgáltatás: Data Lake Storage Gen2
> - Erőforrás: az erőforrás neve
> - Összefoglalás: előfizetés-regisztráció kérése Log Analytics adatexportálásból származó adatok fogadásához.
> - Probléma típusa: kapcsolat
> - Probléma altípusa: kapcsolódási probléma

## <a name="data-completeness"></a>Az adatteljesség
Az adatexportálás továbbra is újra próbálkozik az adatok küldésével akár 30 percig, ha a cél nem érhető el. Ha a 30 perc elteltével sem érhető el, akkor a rendszer elveti az adatvesztést, amíg a célhely elérhetővé nem válik.

## <a name="cost"></a>Költség
Az adatexportálási szolgáltatáshoz jelenleg nem számítunk fel további díjakat. Az adatexportálás díjszabása a jövőben lesz bejelentve, és a számlázás megkezdése előtt megjelenő értesítés. Ha úgy dönt, hogy az adatexportálást a felmondási időszak után is használja, akkor a díjszabást a vonatkozó díjak alapján számítjuk fel.

## <a name="export-destinations"></a>Célhelyek exportálása

### <a name="storage-account"></a>Tárfiók
Az adatküldés óránként történik a Storage-fiókok számára. Az adatexportálási konfiguráció egy tárolót hoz létre a Storage *-* fiókban lévő összes táblához a (z) nevű tárolóban, amelyet a tábla neve követ. Például a tábla *SecurityEvent* egy *am-SecurityEvent*nevű tárolóba fog elküldeni.

A Storage-fiók blobjának elérési útja a következő: *WorkspaceResourceId =/Subscriptions/Subscription-ID/resourcegroups/ \<resource-group\> /providers/Microsoft.operationalinsights/workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d =/h =/m = \<two-digit numeric day\> \<two-digit 24-hour clock hour\> 00/PT1H.js*. Mivel a hozzáfűzési Blobok a Storage-ban lévő 50K-írásokra korlátozódnak, az exportált Blobok száma kiterjeszthető, ha a Hozzáfűzések száma magas. Ilyen esetben a Blobok elnevezési mintája PT1H_ #. JSON lesz, ahol a # a növekményes Blobok száma.

A Storage-fiók adatformátuma [JSON-vonal](diagnostic-logs-append-blobs.md). Ez azt jelenti, hogy az egyes rekordok egy sortöréssel vannak elválasztva, a külső rekordok tömbje és a JSON-rekordok közötti vesszők nélkül. 

[![Tárolási mintaadatok](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Log Analytics adatexportálás írási blobokat írhat a nem módosítható tárolási fiókokba, ha az időalapú adatmegőrzési házirendek engedélyezve vannak a *allowProtectedAppendWrites* beállításnál. Így új blokkokat írhat egy hozzáfűzési blobba, miközben megőrizheti a módosíthatatlansági védelmét és megfelelőségét. Lásd: a [védett hozzáfűzési Blobok írásának engedélyezése](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

### <a name="event-hub"></a>Eseményközpont
A rendszer közel valós időben küldi el az adatait az Event hub számára, mivel Azure Monitor. A rendszer minden olyan adattípushoz létrehoz egy Event hub *-* t, amelyet a név és a tábla neve után exportál. Például a *SecurityEvent* tábla egy *am-SecurityEvent*nevű Event hub számára fog eljuttatni. Ha azt szeretné, hogy az exportált adatai egy adott esemény központhoz jussanak, vagy ha olyan névvel rendelkezik, amely meghaladja az 47 karakteres korlátot, akkor megadhatja a saját Event hub-nevét, és exportálhatja az összes táblát.

Az exportált adatok mennyisége gyakran növekszik az idő múlásával, és az Event hub-méretezést növelni kell a nagyobb adatátviteli sebesség kezelésére, valamint a szabályozás és az adatkésés elkerülésére. A Event Hubs automatikus feltöltési funkciójának használatával automatikusan méretezheti és növelheti az átviteli egységek számát, és megfelel a használati igényeknek. A részletekért lásd: az [Azure Event Hubs átviteli egységek automatikus vertikális felskálázása](../../event-hubs/event-hubs-auto-inflate.md) .


## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek szükségesek, amelyeket a Log Analytics adatexportálás konfigurálása előtt kell végrehajtani.

- A Storage-fióknak és az Event hub-nek már létre kell hoznia, és a Log Analytics munkaterülettel azonos régióban kell lennie. Ha más Storage-fiókba kell replikálnia az adatait, az [Azure Storage-redundancia bármely beállítását](../../storage/common/storage-redundancy.md)használhatja.  
- A Storage-fióknak StorageV1 vagy StorageV2 kell lennie. A klasszikus tároló nem támogatott  
- Ha úgy állította be a Storage-fiókot, hogy engedélyezze a hozzáférést a kiválasztott hálózatokból, a Storage-fiók beállításaiban kivételt kell hozzáadnia, hogy a Azure Monitor a tárhelyre írjon.

## <a name="enable-data-export"></a>Az adatexportálás engedélyezése
A következő lépéseket kell végrehajtani a Log Analytics adatexportálás engedélyezéséhez. A következő részekben talál további részleteket.

- Erőforrás-szolgáltató regisztrálása.
- Megbízható Microsoft-szolgáltatások engedélyezése.
- Hozzon létre egy vagy több olyan adatexportálási szabályt, amely meghatározza az exportálandó táblákat és azok célját.

### <a name="register-resource-provider"></a>Erőforrás-szolgáltató regisztrálása
A következő Azure-erőforrás-szolgáltatónak regisztrálnia kell az előfizetéséhez Log Analytics adatexportálás engedélyezéséhez. 

- Microsoft. bepillantások

Ez az erőforrás-szolgáltató valószínűleg már regisztrálva lesz a legtöbb Azure Monitor felhasználó számára. Az ellenőrzéshez lépjen a Azure Portal **előfizetések** elemre. Válassza ki az előfizetését, majd kattintson az **erőforrás-szolgáltatók** elemre a menü **Beállítások** szakaszában. Keresse meg a **Microsoft. ininsights**szolgáltatást. Ha az állapota **regisztrálva**van, akkor már regisztrálva van. Ha nem, kattintson a **regisztrálás** gombra a regisztráláshoz.

Az erőforrás-szolgáltatót az [Azure Resource Providers és types](../../azure-resource-manager/management/resource-providers-and-types.md)című témakörben leírtak szerint is használhatja. A következő egy minta parancs a PowerShell használatával:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások engedélyezése
Ha úgy állította be a Storage-fiókot, hogy az engedélyezze a hozzáférést a kiválasztott hálózatokból, vegyen fel egy kivételt, amely lehetővé teszi, hogy Azure Monitor írni a fiókba. A Storage-fiókhoz tartozó **tűzfalak és virtuális hálózatok** **lehetőségnél válassza a megbízható Microsoft-szolgáltatások engedélyezése a Storage-fiókhoz**lehetőséget.

[![A Storage-fiók tűzfala és virtuális hálózatai](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)


### <a name="create-or-update-data-export-rule"></a>Adatexportálási szabály létrehozása vagy frissítése
Az adatexportálási szabályok határozzák meg az összes táblából vagy a táblák bizonyos tábláiból exportálandó adatok egyetlen célhelyre való exportálásának adatait. Több szabályt is létrehozhat, ha több célhelyre kell küldenie.

A következő parancs használatával hozzon létre egy adatexportálási szabályt egy Storage-fiókhoz a parancssori felület használatával.

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountId
```

A következő parancs használatával hozzon létre egy adatexportálási szabályt egy Event hub-hoz a parancssori felület használatával.

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesId
```

Az alábbi kérelem használatával hozzon létre egy adatexportálási szabályt a REST API használatával. A kérelemnek tulajdonosi jogkivonat-engedélyezést és Content Type Application/JSON-t kell használnia.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

A kérelem törzse megadja a táblák célhelyét. A következő egy minta törzs a Storage-fiók REST-kérelméhez.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": true
    }
}
```

Az alábbi példa egy Event hub REST-kérelmét követi.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}

```


## <a name="view-data-export-configuration"></a>Adatexportálási konfiguráció megtekintése
Az alábbi parancs használatával megtekintheti az adatexportálási szabály konfigurációját a parancssori felület használatával.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

Az alábbi kérelem használatával megtekintheti az adatexportálási szabály konfigurációját a REST API használatával. A kérelemnek tulajdonosi jogkivonat-hitelesítést kell használnia.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

## <a name="disable-an-export-rule"></a>Exportálási szabály letiltása
Az exportálási szabályok letilthatók, így leállíthatja az exportálást, ha nem kell megtartania az adott időszakra vonatkozó adatmegőrzési időt, például ha a teszt végrehajtása folyamatban van. Használja az alábbi parancsot egy adatexportálási szabály letiltásához a parancssori felület használatával.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

Az alábbi kérelem használatával tilthatja le az adatexportálási szabályt a REST API használatával. A kérelemnek tulajdonosi jogkivonat-hitelesítést kell használnia.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```

## <a name="delete-an-export-rule"></a>Exportálási szabály törlése
Az alábbi parancs használatával törölhet egy adatexportálási szabályt a parancssori felület használatával.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

Az alábbi kérelem használatával törölheti az adatexportálási szabályt a REST API használatával. A kérelemnek tulajdonosi jogkivonat-hitelesítést kell használnia.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

## <a name="view-all-data-export-rules-in-a-workspace"></a>A munkaterület összes adatexportálási szabályának megtekintése
A következő parancs használatával tekintheti meg a munkaterület összes adatexportálási szabályát a parancssori felület használatával.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

A következő kérelem használatával megtekintheti a munkaterület összes adatexportálási szabályát a REST API használatával. A kérelemnek tulajdonosi jogkivonat-hitelesítést kell használnia.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```

## <a name="unsupported-tables"></a>Nem támogatott táblák
Ha az adatexportálási szabály nem támogatott táblát tartalmaz, akkor a konfiguráció sikeres lesz, de a rendszer nem exportálja az adott táblára vonatkozó adatvesztést. Ha a tábla később támogatott, akkor a rendszer az adott időpontban exportálja az adatforrásokat.

Ha az adatexportálási szabály olyan táblát tartalmaz, amely nem létezik, akkor a hiba a * <tableName> munkaterületen nem létezik*.


## <a name="supported-tables"></a>Támogatott táblák
A támogatott táblázatok jelenleg az alább megadott értékekre korlátozódnak. A rendszer minden, a táblából származó adattal exportál, hacsak nincsenek korlátozások megadva. A rendszer frissíti a listát a további táblázatok támogatásával.


| Táblázat | Korlátozások |
|:---|:---|:---|
| AADDomainServicesAccountLogon | |
| AADDomainServicesAccountManagement | |
| AADDomainServicesDirectoryServiceAccess | |
| AADDomainServicesLogonLogoff | |
| AADDomainServicesPolicyChange | |
| AADDomainServicesPrivilegeUse | |
| ADAssessmentRecommendation | |
| ADFActivityRun | |
| ADFPipelineRun | |
| ADFTriggerRun | |
| ADReplicationResult | |
| ADSecurityAssessmentRecommendation | |
| ADTDigitalTwinsOperation | |
| ADTEventRoutesOperation | |
| ADTModelsOperation | |
| ADTQueryOperation | |
| ADXCommand | |
| ADXQuery | |
| AegDeliveryFailureLogs | |
| AegPublishFailureLogs | |
| Riasztás | A táblázatba tartozó egyes adatmennyiségek a Storage-fiókon keresztül kerülnek betöltésre. Ez a rész hiányzik az exportálási folyamatban. |
| ApiManagementGatewayLogs | |
| AppCenterError | |
| AppPlatformSystemLogs | |
| AppServiceAppLogs | |
| AppServiceAuditLogs | |
| AppServiceConsoleLogs | |
| AppServiceFileAuditLogs | |
| AppServiceHTTPLogs | |
| AppServicePlatformLogs | |
| AuditLogs | |
| AutoscaleEvaluationsLog | |
| AutoscaleScaleActionsLog | |
| AWSCloudTrail | |
| AzureAssessmentRecommendation | |
| AzureDevOpsAuditing | |
| BehaviorAnalytics | |
| BlockchainApplicationLog | |
| BlockchainProxyLog | |
| BlockchainProxyLog | |
| CommonSecurityLog | |
| CommonSecurityLog | |
| ComputerGroup | |
| ConfigurationData | Néhány adatot az exportáláshoz nem támogatott belső szolgáltatásokon keresztül kell bevezetni. Ez a rész hiányzik az exportálási folyamatban. |
| ContainerImageInventory | |
| ContainerInventory | |
| ContainerLog | |
| ContainerLog | |
| ContainerNodeInventory | |
| ContainerServiceLog | |
| CoreAzureBackup | |
| DatabricksAccounts | |
| DatabricksClusters | |
| DatabricksDBFS | |
| DatabricksInstancePools | |
| DatabricksJobs | |
| DatabricksNotebook | |
| DatabricksSecrets | |
| DatabricksSQLPermissions | |
| DatabricksSSH | |
| DatabricksWorkspace | |
| DnsEvents | |
| DnsInventory | |
| Dynamics365Activity | |
| Esemény | A táblázatba tartozó egyes adatmennyiségek a Storage-fiókon keresztül kerülnek betöltésre. Ez a rész hiányzik az exportálási folyamatban. |
| ExchangeAssessmentRecommendation | |
| ExchangeAssessmentRecommendation | |
| FailedIngestion | |
| FunctionAppLogs | |
| Szívverés | Támogatott | |
| HuntingBookmark | |
| InsightsMetrics | Néhány adatot az exportáláshoz nem támogatott belső szolgáltatásokon keresztül kell bevezetni. Ez a rész hiányzik az exportálási folyamatban. |
| IntuneAuditLogs | |
| IntuneOperationalLogs | |
| KubeEvents | |
| KubeHealth | |
| KubeMonAgentEvents | |
| KubeNodeInventory | |
| KubePodInventory | |
| KubeServices | |
| KubeServices | |
| McasShadowItReporting | |
| MicrosoftAzureBastionAuditLogs | |
| MicrosoftDataShareReceivedSnapshotLog | |
| MicrosoftDataShareSentSnapshotLog | |
| MicrosoftDataShareShareLog | |
| MicrosoftHealthcareApisAuditLogs | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorTestResult | |
| OfficeActivity | A O365-ből a LA-be a webhookok használatával betöltött egyes adatok. Ez a rész hiányzik az exportálási folyamatban. |
| Művelet | Néhány adatot az exportáláshoz nem támogatott belső szolgáltatásokon keresztül kell bevezetni. Ez a rész hiányzik az exportálási folyamatban. |
| Teljesítmény | Támogatott | |
| SCCMAssessmentRecommendation | | 
| SCOMAssessmentRecommendation | |
| SecurityAlert | |
| SecurityBaseline | |
| SecurityBaselineSummary | |
| SecurityDetection | |
| Biztonsági esemény | Támogatott | |
| Biztonsági incidens | |
| SecurityIoTRawEvent | |
| SecurityNestedRecommendation | |
| SecurityRecommendation | |
| SfBAssessmentRecommendation | |
| SfBOnlineAssessmentRecommendation | |
| SharePointOnlineAssessmentRecommendation | |
| SignalRServiceDiagnosticLogs | |
| SigninLogs | |
| SPAssessmentRecommendation | |
| SQLAssessmentRecommendation | |
| SucceededIngestion | |
| Rendszernapló |Részleges | A táblázatba tartozó egyes adatmennyiségek a Storage-fiókon keresztül kerülnek betöltésre. Ez a rész hiányzik az exportálási folyamatban. |
| ThreatIntelligenceIndicator | |
| Frissítés |Részleges | Néhány adatot az exportáláshoz nem támogatott belső szolgáltatásokon keresztül kell bevezetni. Ez a rész hiányzik az exportálási folyamatban. |
| UpdateRunProgress | |
| UpdateSummary | |
| Használat | |
| UserAccessAnalytics | |
| UserPeerAnalytics | |
| WindowsEvent | |
| WindowsFirewall | |
| WireData |Részleges | Néhány adatot az exportáláshoz nem támogatott belső szolgáltatásokon keresztül kell bevezetni. Ez a rész hiányzik az exportálási folyamatban. |
| WorkloadMonitoringPerf | |
| WorkloadMonitoringPerf | |
| WVDCheckpoints | |
| WVDConnections | |
| WVDErrors | |
| WVDFeeds | |
| WVDManagement | |

## <a name="next-steps"></a>Következő lépések

- [Az exportált adatok lekérdezése az Azure Adatkezelőból](azure-data-explorer-query-storage.md).
