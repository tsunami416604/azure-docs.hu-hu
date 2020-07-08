---
title: Az adatüzemek figyelése Azure Monitor használatával
description: Megtudhatja, hogyan használhatja a Azure Monitort a/Azure Data Factory folyamatok figyeléséhez, ha engedélyezi a diagnosztikai naplókat a Data Factory információi alapján.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 2c9bb4bbf52c968afe267bfa3e2b8d6dae980833
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801600"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Data Factory figyelése és riasztása Azure Monitor használatával

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Felhőbeli alkalmazások összetettek és számos mozgó részből állnak. A figyelők segítségével biztosítható, hogy az alkalmazások kifogástalan állapotban maradjanak és fussanak. A figyelők segítenek elkerülni a lehetséges problémákat és a múltbeli hibák elhárítását is. A monitorozási adataival részletes elemzéseket nyerhet az alkalmazásairól. Ez az információ segít az alkalmazások teljesítményének és karbantartásának javításában. Emellett segít automatizálni azokat a műveleteket, amelyek egyébként manuális beavatkozást igényelnek.

A Azure Monitor a legtöbb Azure-szolgáltatás alapszintű infrastruktúrájának mérőszámait és naplóit biztosítja. Az Azure diagnosztikai naplókat egy erőforrás bocsátja ki, és az adott erőforrás működésével kapcsolatos gazdag és gyakori információkat biztosít. Azure Data Factory (ADF) diagnosztikai naplókat írhat a Azure Monitor. A szolgáltatás hét perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

További részletekért lásd: [Azure monitor áttekintése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Azure Data Factory mérőszámok és folyamat-futtatási adatok megőrzése

A Data Factory csak 45 napig tárolja az adatfeldolgozási folyamatokat. Akkor használja a Azure Monitor, ha továbbra is meg szeretné őrizni az adott adatelérési időt. A monitor segítségével több különböző célpont számára is átirányíthatja a diagnosztikai naplókat az elemzéshez.

* **Storage-fiók**: a diagnosztikai naplókat a naplózáshoz vagy a manuális ellenőrzéshez mentse egy Storage-fiókba. A diagnosztikai beállítások segítségével megadhatja a megőrzési időt napokban.
* **Event hub**: a naplók továbbítása az Azure Event Hubsba. A naplók bekerülnek egy partneri szolgáltatásba vagy egy egyéni analitikai megoldásba, például a Power BIba.
* **Log Analytics**: a naplók elemzése log Analyticsokkal. A Azure Monitor Data Factory integrációja a következő esetekben hasznos:
  * Összetett lekérdezéseket szeretne írni egy Data Factory által a figyelésre közzétett mérőszámok gazdag készletén. A lekérdezésekhez egyéni riasztásokat is létrehozhat.
  * Az adatüzemek között szeretne figyelni. Több adatgyárból is átirányíthat adatait egyetlen figyelő munkaterületre.

Használhat olyan Storage-fiókot vagy Event-hub névteret is, amely nem szerepel a naplókat kibocsátó erőforrás előfizetésében. A beállítást konfiguráló felhasználónak megfelelő szerepköralapú hozzáférés-vezérlési (RBAC) hozzáféréssel kell rendelkeznie mindkét előfizetéshez.

## <a name="configure-diagnostic-settings-and-workspace"></a>Diagnosztikai beállítások és munkaterület konfigurálása

Hozzon létre vagy adjon hozzá diagnosztikai beállításokat az adatai-előállítóhoz.

1. A portálon lépjen a figyelés elemre. Válassza a **Beállítások**  >  **diagnosztikai beállítások**lehetőséget.

1. Válassza ki azt az adatelőállítót, amelyhez diagnosztikai beállítást kíván beállítani.

1. Ha a kiválasztott adatgyárban nem találhatók beállítások, a rendszer kéri, hogy hozzon létre egy beállítást. Kattintson **a diagnosztika bekapcsolása**elemre.

   ![Diagnosztikai beállítás létrehozása, ha nem léteznek beállítások](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Ha vannak meglévő beállítások az adatgyárban, megjelenik az adatgyárban már konfigurált beállítások listája. Válassza a **diagnosztikai beállítás hozzáadása**lehetőséget.

   ![Diagnosztikai beállítás hozzáadása, ha a beállítások léteznek](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Adja meg a beállítás nevét, válassza a **küldés log Analytics**lehetőséget, majd válasszon ki egy munkaterületet **log Analytics munkaterületről**.

    * Az _Azure-diagnosztika_ módban a diagnosztikai naplók beáramlanak a _AzureDiagnostics_ táblába.

    * _Erőforrás-specifikus_ módban a diagnosztikai naplók Azure Data Factory áramlanak a _ADFActivityRun_, a _ADFPipelineRun_, a _ADFTriggerRun_, a _ADFSSISIntegrationRuntimeLogs_, a _ADFSSISPackageEventMessageContext_ _, a ADFSSISPackageEventMessages,_ a _ADFSSISPackageExecutableStatistics_, a _ADFSSISPackageExecutionComponentPhases_és a _ADFSSISPackageExecutionDataStatistics_ táblákba.

      Kiválaszthatja a számítási feladatokhoz kapcsolódó különböző naplókat, amelyeket elküldhet Log Analytics táblákba. Ha például nem használ SQL Server Integration Services (SSIS), nem kell kiválasztania a SSIS-naplókat. Ha be szeretné jelentkezni a SSIS Integration Runtime (IR) indítási/leállítási/karbantartási műveleteit, akkor kiválaszthatja az SSIS IR-naplókat. Ha csak T-SQL-n keresztül hívja meg a SSIS-csomagok végrehajtását, akkor csak a SSIS-csomagokat választhatja ki. Ha a SSIS-csomagok végrehajtását a SSIS-csomagok végrehajtása az ADF-folyamatokban művelettel kezdeményezi, akkor az összes naplót kiválaszthatja.

    * Ha a _AllMetrics_lehetőséget választja, az ADF-entitások számának/méretének, tevékenység/folyamat/trigger futtatásának mérőszámai, INTEGRATION RUNTIME (IR) CPU-kihasználtság/memória/csomópontok száma/várólistája, valamint a SSIS-csomagok végrehajtásának és a SSIS IR indítási/leállítási műveletek lesznek elérhetők a riasztások figyeléséhez/előléptetéséhez.

   ![Adja meg a beállításokat, és válasszon egy log-Analytics-munkaterületet](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Mivel az Azure-tábla nem rendelkezhet több mint 500 oszloppal **, javasoljuk, hogy az** _erőforrás-specifikus módot_válassza. További információ: [log Analytics ismert korlátozások](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

1. Kattintson a **Mentés** gombra.

Néhány pillanat elteltével az új beállítás megjelenik az adat-előállító beállításainak listájában. A rendszer a diagnosztikai naplókat az adott munkaterületre továbbítja, amint új esemény-adatforrások jönnek létre. Akár 15 percig is eltarthat egy esemény kibocsátása, és amikor megjelenik a Log Analyticsban.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Azure Data Factory Analytics-megoldás telepítése az Azure Marketplace-ről

Ez a megoldás a Data Factory általános állapotának összegzését jeleníti meg, és lehetővé teszi a részletek részletezését és a váratlan viselkedési minták hibakeresését. Gazdag, a mezőkből származó nézetekből betekintést nyerhet a legfontosabb feldolgozásba, többek között a következőket:

* A adatfeldolgozó-folyamat, a tevékenység és az trigger futtatásának áttekintése
* A adat-előállító tevékenység-futtatási lehetőség típus szerint
* Az adatfeldolgozó felső folyamatának összefoglalása, tevékenységi hibák

1. Nyissa meg az **Azure Marketplace**-t, válassza az **elemzési** szűrő lehetőséget, és keressen rá **Azure Data Factory Analytics (előzetes verzió)** elemre.

   ![Nyissa meg az "Azure Marketplace" kifejezést, írja be az "elemzési szűrő" kifejezést, és válassza a "Azure Data Factory Analytics (előzetes verzió)" lehetőséget.](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. A **Azure Data Factory Analytics (előzetes verzió)** részletei

   ![A "Azure Data Factory Analytics (előzetes verzió)" részletei](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. Válassza a **Létrehozás** lehetőséget, majd hozza létre vagy válassza ki a **log Analytics munkaterületet**.

   ![Új megoldás létrehozása](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory metrikák figyelése

A Azure Data Factory Analytics telepítése a kiválasztott Log Analytics munkaterület munkafüzetek szakaszában hozza létre az alapértelmezett nézeteket. Ennek eredményeképpen a következő metrikák válnak elérhetővé:

* ADF-futtatások – 1) a folyamat futása Data Factory
* ADF-futtatások – 2) az adattényező által futtatott tevékenységek
* ADF-futtatások – 3) a triggerek az adattényező szerint futnak
* ADF-hibák – 1) az első 10 folyamat hibája Data Factory
* ADF-hibák – 2) az első 10 tevékenység fut Data Factory
* ADF-hibák – 3) az első 10 kiváltó hibát Data Factory
* ADF-statisztika – 1) a tevékenység típus szerint fut
* ADF-statisztika – 2) trigger-futtatások típus szerint
* ADF-statisztika – 3) a folyamat maximális futási időtartama

![A "munkafüzetek (előzetes verzió)" és a "AzureDataFactoryAnalytics" nevű ablak kiemelve](media/data-factory-monitor-oms/monitor-oms-image6.png)

Megjelenítheti az előző mérőszámokat, megtekintheti a mérőszámok mögötti lekérdezéseket, szerkesztheti a lekérdezéseket, riasztásokat hozhat létre, és egyéb műveleteket is végrehajthat.

![A folyamat futtatásának grafikus ábrázolása a adat-előállítóban "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> A Azure Data Factory Analytics (előzetes verzió) diagnosztikai naplókat küld az _erőforrás-specifikus_ célhelyekre. A következő táblázatokból is írhat lekérdezéseket: _ADFPipelineRun_, _ADFTriggerRun_és _ADFActivityRun_.

## <a name="data-factory-metrics"></a>Data Factory metrikák

A monitor segítségével megtekintheti az Azure-beli számítási feladatok teljesítményét és állapotát. A monitorozási adatok legfontosabb típusa a metrika, amelyet a teljesítményszámláló is nevez. A metrikákat a legtöbb Azure-erőforrás kibocsátja. A figyelő számos módszert biztosít ezen mérőszámok konfigurálásához és felhasználásához a figyeléshez és a hibaelhárításhoz.

Íme néhány a Azure Data Factory 2. verziójában kibocsátott mérőszámok közül:

| **Metrika**                           | **Metrika megjelenítendő neve**                  | **Egység** | **Összesítés típusa** | **Leírás**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCanceledRuns                 | A megszakított tevékenység metrikákat futtat           | Darabszám    | Összesen                | Egy percen belül megszakított tevékenység-futtatások teljes száma. |
| ActivityFailedRuns                   | Sikertelen tevékenység-futtatási metrikák             | Darabszám    | Összesen                | A meghiúsult tevékenység-futtatások teljes száma egy percen belül. |
| ActivitySucceededRuns                | A sikeres tevékenység metrikákat futtat          | Darabszám    | Összesen                | Az egy percen belül sikeres tevékenység-futtatások teljes száma. |
| PipelineCanceledRuns                 | A megszakított folyamat metrikákat futtat           | Darabszám    | Összesen                | Egy percen belül megszakított folyamat-futtatások teljes száma. |
| PipelineFailedRuns                   | Sikertelen folyamat-futtatási metrikák             | Darabszám    | Összesen                | A meghiúsult folyamat-futtatások teljes száma egy percen belül. |
| PipelineSucceededRuns                | A folyamat sikeresen futtatja a metrikákat          | Darabszám    | Összesen                | A perceken belül sikeres folyamat-futtatások teljes száma. |
| TriggerCanceledRuns                  | Megszakított trigger-futtatási metrikák            | Darabszám    | Összesen                | Egy percen belül megszakított trigger-futtatások teljes száma. |
| TriggerFailedRuns                    | Sikertelen trigger-futtatási metrikák              | Darabszám    | Összesen                | A sikertelen trigger-futtatások teljes száma egy percen belül. |
| TriggerSucceededRuns                 | A sikeres trigger metrikákat futtat           | Darabszám    | Összesen                | Egy percen belül sikeresen elindított trigger-futtatások teljes száma. |
| SSISIntegrationRuntimeStartCanceled  | SSIS IR indítási metrikák megszakítva           | Darabszám    | Összesen                | A percenként megszakított SSIS IR-indítások teljes száma. |
| SSISIntegrationRuntimeStartFailed    | Nem sikerült SSIS az IR indítási metrikákat             | Darabszám    | Összesen                | A SSIS összes olyan IR elindítása, amely egy percen belül meghiúsult. |
| SSISIntegrationRuntimeStartSucceeded | Sikeres SSIS IR indítási metrikák          | Darabszám    | Összesen                | A SSIS-integrációs modul teljes száma, amely egy percen belül sikeres volt. |
| SSISIntegrationRuntimeStopStuck      | Beragadt SSIS IR-leállítási metrikái               | Darabszám    | Összesen                | Egy percen belül beragadt SSIS IR-megállók teljes száma. |
| SSISIntegrationRuntimeStopSucceeded  | Sikeres SSIS IR-leállítási metrikák           | Darabszám    | Összesen                | Egy percen belül sikeresen elvégezhető SSIS-állomások teljes száma. |
| SSISPackageExecutionCanceled         | SSIS-csomag végrehajtási mérőszámai megszakítva  | Darabszám    | Összesen                | A percenként megszakított SSIS-csomagok végrehajtásának teljes száma. |
| SSISPackageExecutionFailed           | Sikertelen SSIS-csomag végrehajtási metrikái    | Darabszám    | Összesen                | Egy percen belül sikertelen SSIS-csomagok végrehajtásának teljes száma. |
| SSISPackageExecutionSucceeded        | Sikeres SSIS-csomag végrehajtási metrikái | Darabszám    | Összesen                | Egy percen belül sikeres SSIS-csomagok végrehajtásának teljes száma. |

A metrikák eléréséhez hajtsa végre az [Azure monitor adatplatformon](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)megjelenő utasításokat.

> [!NOTE]
> A rendszer csak a befejezett, az aktivált tevékenységek és a folyamat futási eseményeit bocsátja ki. Folyamatban, a homokozó/hibakeresési futtatások **nincsenek kibocsátva** . Másfelől a SSIS-csomag végrehajtásának összes eseménye ki van vezetve, beleértve azokat is, amelyek a kész, folyamatban van, és a T-SQL-on keresztül, a SSMS/SQL Server Agent/egyéb kijelölt eszközökön, illetve a SSIS-csomagok végrehajtásához használt eseményindító/homokozó/hibakeresési műveletek során meghívásra kerülnek az ADF-folyamatokban.

## <a name="data-factory-alerts"></a>Riasztások Data Factory

Jelentkezzen be a Azure Portalba, és válassza a riasztások **figyelése**lehetőséget a  >  **Alerts** riasztások létrehozásához.

![Riasztások a portál menüjében](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Riasztások létrehozása

1. Új riasztás létrehozásához válassza az **+ új riasztási szabály** lehetőséget.

    ![Új riasztási szabály](media/monitor-using-azure-monitor/alerts_image4.png)

1. Adja meg a riasztási feltételt.

    > [!NOTE]
    > Ügyeljen rá, hogy a **szűrés erőforrás típusa** legördülő listában válassza az **összes** lehetőséget.

    !["A riasztási feltétel meghatározása" > "válassza a cél" lehetőséget, amely megnyitja az "erőforrás kiválasztása" panelt. ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["A riasztási feltétel meghatározása" > "feltételek hozzáadása", amely megnyitja a "jel logikai beállítása" panelt.](media/monitor-using-azure-monitor/alerts_image6.png)

    !["A jel típusának konfigurálása" panel](media/monitor-using-azure-monitor/alerts_image7.png)

1. Adja meg a riasztás részleteit.

    ![Riasztás részletei](media/monitor-using-azure-monitor/alerts_image8.png)

1. A műveleti csoport definiálása.

    ![Szabály létrehozása a Kiemelt "új műveleti csoporttal"](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Új műveleti csoport létrehozása](media/monitor-using-azure-monitor/alerts_image10.png)

    ![E-mailek, SMS-cím, leküldéses és hangvezéreltség konfigurálása](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Műveleti csoport definiálása](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Diagnosztikai naplók beállítása a Azure Monitor használatával REST API

### <a name="diagnostic-settings"></a>Diagnosztikai beállítások

Diagnosztikai beállítások használatával konfigurálhatja a nem számítási erőforrások diagnosztikai naplóit. Az erőforrás-vezérlő beállításai az alábbi funkciókkal rendelkeznek:

* Megadják a diagnosztikai naplók elküldéseinak helyét. Ilyenek például az Azure Storage-fiók, az Azure Event hub vagy a monitoring-naplók.
* Ezek határozzák meg, hogy mely naplózási kategóriákat küldik el a rendszer.
* Azt határozzák meg, hogy mennyi ideig kell megőrizni az egyes naplók kategóriáit egy Storage-fiókban.
* A nulla napos megőrzés azt jelenti, hogy a naplók örökre meg lesznek őrizve. Ellenkező esetben az érték tetszőleges számú nap lehet, 1-től 2 147 483 647-ig.
* Ha adatmegőrzési házirend van beállítva, de a naplók tárolása egy Storage-fiókban le van tiltva, az adatmegőrzési szabályzatok nem lépnek érvénybe. Ez a feltétel például akkor fordulhat elő, ha csak a Event Hubs vagy a figyelési naplók lehetőség van kiválasztva.
* Az adatmegőrzési szabályzatok naponta lesznek alkalmazva. A tartomány napjai közötti határ éjfélkor, az egyezményes világidő (UTC) szerint történik. A nap végén a rendszer törli az adatmegőrzési házirenden túli napokat. Ha például egy nap adatmegőrzési szabályzattal rendelkezik, a rendszer a tegnapi naptól kezdve törli a naplókat.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Diagnosztikai naplók engedélyezése a Azure Monitor használatával REST API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Diagnosztikai beállítás létrehozása vagy frissítése a figyelő REST API

##### <a name="request"></a>Kérés

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Fejlécek

* Cserélje le a `{api-version}` elemet a `2016-09-01` kérdésre.
* Cserélje le annak az `{resource-id}` erőforrásnak az azonosítóját, amelynek a diagnosztikai beállításait szerkeszteni kívánja. További információ: [erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Állítsa be a fejlécet a következőre: `Content-Type` `application/json` .
* Állítsa be az engedélyezési fejlécet a Azure Active Directory (Azure AD) által kapott JSON webes tokenre. További információ: [kérelmek hitelesítése](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Törzs

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Tulajdonság | Típus | Description |
| --- | --- | --- |
| **storageAccountId** |Sztring | Annak a Storage-fióknak az erőforrás-azonosítója, amelyhez diagnosztikai naplókat szeretne küldeni. |
| **serviceBusRuleId** |Sztring | Annak a Service-Bus-névtérnek az azonosítója, amelyben a Event Hubs létre kívánja hozni a stream diagnosztikai naplóihoz. A szabály AZONOSÍTÓjának formátuma `{service bus resource ID}/authorizationrules/{key name}` .|
| **workspaceId** | Összetett típus | Metrikus idő típusú gabona és adatmegőrzési szabályzatok tömbje. A tulajdonság értéke üres. |
|**mutatókat**| A folyamat által a meghívott folyamatnak átadni kívánt folyamat paramétereinek értéke| Egy JSON-objektum, amely a paraméterek nevét az argumentum értékére képezi. |
| **naplók**| Összetett típus| Az erőforrástípus diagnosztikai-naplózási kategóriájának neve. Az erőforráshoz tartozó diagnosztikai naplók listájának beszerzéséhez hajtsa végre a diagnosztikai beállítások beolvasása műveletet. |
| **Kategória**| Sztring| Naplózási kategóriák és adatmegőrzési szabályzatok tömbje. |
| **timeGrain** | Sztring | A metrikák részletessége ISO 8601 időtartam formátumban rögzítve. A tulajdonság értékének a értéket kell tartalmaznia `PT1M` , amely egy percet ad meg. |
| **engedélyezve**| Logikai | Meghatározza, hogy engedélyezve van-e a metrika vagy a napló kategóriájának gyűjteménye ehhez az erőforráshoz. |
| **retentionPolicy**| Összetett típus| A metrika vagy a napló kategóriájának adatmegőrzési szabályát ismerteti. Ez a tulajdonság csak a Storage-fiókok esetében használatos. |
|**nap**| Int| A metrikák vagy naplók megtartására szolgáló napok száma. Ha a tulajdonság értéke 0, a naplók örökre megmaradnak. Ez a tulajdonság csak a Storage-fiókok esetében használatos. |

##### <a name="response"></a>Válasz

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>A figyelő diagnosztikai beállításaival kapcsolatos információk beolvasása REST API

##### <a name="request"></a>Kérés

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Fejlécek

* Cserélje le a `{api-version}` elemet a `2016-09-01` kérdésre.
* Cserélje le annak az `{resource-id}` erőforrásnak az azonosítóját, amelynek a diagnosztikai beállításait szerkeszteni kívánja. További információ: [erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Állítsa be a fejlécet a következőre: `Content-Type` `application/json` .
* Állítsa be az engedélyezési fejlécet egy olyan JSON webes tokenre, amelyet az Azure AD-ből kapott. További információ: [kérelmek hitelesítése](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Válasz

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
További információ: [diagnosztikai beállítások](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Naplók és események sémája

### <a name="monitor-schema"></a>Séma figyelése

#### <a name="activity-run-log-attributes"></a>Tevékenység – napló attribútumainak futtatása

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Tulajdonság | Típus | Leírás | Példa |
| --- | --- | --- | --- |
| **Szintű** |Sztring | A diagnosztikai naplók szintje. A tevékenységek futtatására szolgáló naplók esetében állítsa a tulajdonság értékét 4 értékre. | `4` |
| **correlationId** |Sztring | Egy adott kérelem nyomon követésére szolgáló egyedi azonosító. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **idő** | Sztring | Az esemény időpontja a TimeSpan UTC formátumban `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Sztring| A tevékenység futtatásának azonosítója. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Sztring| A folyamat futtatásának azonosítója. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Sztring | Az adatfeldolgozó erőforráshoz társított azonosító. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategória**| Sztring | A diagnosztikai naplók kategóriája. Állítsa a tulajdonság értékét értékre `ActivityRuns` . | `ActivityRuns` |
|**szint**| Sztring | A diagnosztikai naplók szintje. Állítsa a tulajdonság értékét értékre `Informational` . | `Informational` |
|**operationName**| Sztring | Az állapottal rendelkező tevékenység neve. Ha a tevékenység a kezdő szívverés, a tulajdonság értéke `MyActivity -` . Ha a tevékenység a záró szívverés, a tulajdonság értéke `MyActivity - Succeeded` . | `MyActivity - Succeeded` |
|**pipelineName**| Sztring | A folyamat neve. | `MyPipeline` |
|**activityName**| Sztring | A tevékenység neve. | `MyActivity` |
|**Start**| Sztring | A tevékenység kezdési időpontja TimeSpan UTC formátumban fut. | `2017-06-26T20:55:29.5007959Z`|
|**végén**| Sztring | A tevékenység befejezési időpontja TimeSpan UTC formátumban fut. Ha a diagnosztikai napló azt mutatja, hogy egy tevékenység megkezdődött, de még nem fejeződött be, a tulajdonság értéke `1601-01-01T00:00:00Z` . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Folyamat – naplózási attribútumok futtatása

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Tulajdonság | Típus | Leírás | Példa |
| --- | --- | --- | --- |
| **Szintű** |Sztring | A diagnosztikai naplók szintje. A tevékenységek futtatására szolgáló naplók esetében állítsa a tulajdonság értékét 4 értékre. | `4` |
| **correlationId** |Sztring | Egy adott kérelem nyomon követésére szolgáló egyedi azonosító. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **idő** | Sztring | Az esemény időpontja a TimeSpan UTC formátumban `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Sztring| A folyamat futtatásának azonosítója. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Sztring | Az adatfeldolgozó erőforráshoz társított azonosító. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategória**| Sztring | A diagnosztikai naplók kategóriája. Állítsa a tulajdonság értékét értékre `PipelineRuns` . | `PipelineRuns` |
|**szint**| Sztring | A diagnosztikai naplók szintje. Állítsa a tulajdonság értékét értékre `Informational` . | `Informational` |
|**operationName**| Sztring | A folyamat neve az állapotával együtt. A folyamat futásának befejeződése után a tulajdonság értéke `Pipeline - Succeeded` . | `MyPipeline - Succeeded`. |
|**pipelineName**| Sztring | A folyamat neve. | `MyPipeline` |
|**Start**| Sztring | A tevékenység kezdési időpontja TimeSpan UTC formátumban fut. | `2017-06-26T20:55:29.5007959Z`. |
|**végén**| Sztring | A tevékenység befejezési időpontja TimeSpan UTC formátumban fut. Ha a diagnosztikai naplóban egy tevékenység megkezdődött, de még nem fejeződött be, a tulajdonság értéke `1601-01-01T00:00:00Z` .  | `2017-06-26T20:55:29.5007959Z` |
|**állapota**| Sztring | A folyamat futásának végső állapota. A lehetséges tulajdonságok értékei a következők: `Succeeded` és `Failed` . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Trigger – napló attribútumainak futtatása

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| Tulajdonság | Típus | Leírás | Példa |
| --- | --- | --- | --- |
| **Szintű** |Sztring | A diagnosztikai naplók szintje. A tevékenységek futtatására szolgáló naplók esetében állítsa a tulajdonság értékét 4 értékre. | `4` |
| **correlationId** |Sztring | Egy adott kérelem nyomon követésére szolgáló egyedi azonosító. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **idő** | Sztring | Az esemény időpontja a TimeSpan UTC formátumban `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Sztring| Az trigger futtatásának azonosítója. | `08587023010602533858661257311` |
|**resourceId**| Sztring | Az adatfeldolgozó erőforráshoz társított azonosító. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Kategória**| Sztring | A diagnosztikai naplók kategóriája. Állítsa a tulajdonság értékét értékre `PipelineRuns` . | `PipelineRuns` |
|**szint**| Sztring | A diagnosztikai naplók szintje. Állítsa a tulajdonság értékét értékre `Informational` . | `Informational` |
|**operationName**| Sztring | Az trigger neve a végső állapottal, amely azt jelzi, hogy az trigger sikeresen kilőtte-e. Ha a szívverés sikeres volt, a tulajdonság értéke `MyTrigger - Succeeded` . | `MyTrigger - Succeeded` |
|**triggerName**| Sztring | Az trigger neve. | `MyTrigger` |
|**triggerType**| Sztring | Az trigger típusa. A lehetséges tulajdonságok értékei a következők: `Manual Trigger` és `Schedule Trigger` . | `ScheduleTrigger` |
|**triggerEvent**| Sztring | Az eseményindító eseménye. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**Start**| Sztring | A trigger égetésének kezdési időpontja TimeSpan UTC formátumban. | `2017-06-26T20:55:29.5007959Z`|
|**állapota**| Sztring | Az a végső állapot, amely azt jelzi, hogy az trigger sikeresen kirúgták-e. A lehetséges tulajdonságok értékei a következők: `Succeeded` és `Failed` . | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>SSIS integrációs modul naplózási attribútumai

Ezek a SSIS Integration Runtime (IR) indítási/leállítási/karbantartási műveleteinek naplózási attribútumai/tulajdonságai.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Tulajdonság                   | Típus   | Leírás                                                   | Példa                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **idő**                   | Sztring | Az esemény időpontja UTC formátumban:`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Sztring | A SSIS IR-műveletének neve                            | `Start/Stop/Maintenance` |
| **Kategória**               | Sztring | A diagnosztikai naplók kategóriája                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | Sztring | Egy adott művelet nyomon követésére szolgáló egyedi azonosító             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | Sztring | Az ADF neve                                          | `MyADFv2` |
| **integrationRuntimeName** | Sztring | A SSIS IR neve                                      | `MySSISIR` |
| **szint**                  | Sztring | A diagnosztikai naplók szintje                                  | `Informational` |
| **resultType**             | Sztring | A SSIS IR-művelet eredménye                          | `Started/InProgress/Succeeded/Failed` |
| **üzenetet**                | Sztring | A SSIS IR-művelet kimeneti üzenete                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | Sztring | Az ADF-erőforrás egyedi azonosítója                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>SSIS-események környezeti naplójának attribútumai

Ezek a SSIS-csomag végrehajtása által a SSIS IR-ben generált esemény-üzenetekre vonatkozó naplózási attribútumok/tulajdonságok. Hasonló információkat közvetítenek, mint a [SSIS Catalog (SSISDB) esemény-üzenet környezeti táblázata/nézete](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15) , amely a SSIS-csomagok számos tulajdonságának futásidejű értékeit jeleníti meg. Ezek akkor jönnek létre, amikor kiválasztja `Basic/Verbose` a naplózási szint lehetőséget, és hasznos a hibakereséshez/megfelelőség ellenőrzéséhez.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Tulajdonság                   | Típus   | Leírás                                                          | Példa                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **idő**                   | Sztring | Az esemény időpontja UTC formátumban:`YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Sztring | Ez a következőre van beállítva`YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **Kategória**               | Sztring | A diagnosztikai naplók kategóriája                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | Sztring | Egy adott művelet nyomon követésére szolgáló egyedi azonosító                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Sztring | Az ADF neve                                                 | `MyADFv2` |
| **integrationRuntimeName** | Sztring | A SSIS IR neve                                             | `MySSISIR` |
| **szint**                  | Sztring | A diagnosztikai naplók szintje                                         | `Informational` |
| **operationId**            | Sztring | Egy adott művelet nyomon követésére szolgáló egyedi azonosító a SSISDB-ben          | `1`(1 a SSISDB-ben nem tárolt csomagokkal kapcsolatos műveleteket jelzi) |
| **contextDepth**           | Sztring | Az esemény-üzenet környezetének mélysége                              | `0`(0 azt jelenti, hogy a csomag végrehajtásának elindulása előtt 1 jelzi a kontextust, amikor hiba történik, és a környezet a hiba miatt tovább nő) |
| **packagePath**            | Sztring | A Package objektum elérési útja az esemény üzenetének környezeti forrásaként      | `\Package` |
| **contextType**            | Sztring | A Package objektum típusa az esemény üzenetei környezetének forrásaként      | `60`( [további környezeti típusok](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15#remarks):) |
| **contextSourceName**      | Sztring | A Package objektum neve az esemény üzenetei környezetének forrásaként      | `MyPackage` |
| **contextSourceId**        | Sztring | A Package objektum egyedi azonosítója az esemény üzenetei környezetének forrásaként | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | Sztring | Az esemény üzenetének környezeti forrása csomag tulajdonságának neve   | `DelayValidation` |
| **Tulajdonságérték**          | Sztring | Az esemény üzenetének környezeti forrása csomag tulajdonságának értéke  | `False` |
| **resourceId**             | Sztring | Az ADF-erőforrás egyedi azonosítója                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>SSIS-üzenetek naplózási attribútumai

Ezek a SSIS-csomagok végrehajtásához a SSIS IR-ben generált Eseménynapló-attribútumok/-tulajdonságok. Hasonló információkat közvetítenek, mint az [SSISDB tábla/nézet](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-messages?view=sql-server-ver15) , amely az események részletes szövegét/metaadatait jeleníti meg. Minden naplózási szinten jönnek létre, kivéve a következőt: `None` .

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Tulajdonság                   | Típus   | Leírás                                                        | Példa                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **idő**                   | Sztring | Az esemény időpontja UTC formátumban:`YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Sztring | Ez a következőre van beállítva`YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **Kategória**               | Sztring | A diagnosztikai naplók kategóriája                                    | `SSISPackageEventMessages` |
| **correlationId**          | Sztring | Egy adott művelet nyomon követésére szolgáló egyedi azonosító                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Sztring | Az ADF neve                                               | `MyADFv2` |
| **integrationRuntimeName** | Sztring | A SSIS IR neve                                           | `MySSISIR` |
| **szint**                  | Sztring | A diagnosztikai naplók szintje                                       | `Informational` |
| **operationId**            | Sztring | Egy adott művelet nyomon követésére szolgáló egyedi azonosító a SSISDB-ben        | `1`(1 a SSISDB-ben nem tárolt csomagokkal kapcsolatos műveleteket jelzi) |
| **messageTime**            | Sztring | Az az idő, amikor az esemény üzenete UTC formátumban lett létrehozva          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | Sztring | Az üzenet típusa                                     | `70`( [további üzenetek típusai](https://docs.microsoft.com/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks)) |
| **messageSourceType**      | Sztring | Az esemény-üzenet forrásának típusa                              | `20`(lásd: [több üzenet típusú Forrástípus](https://docs.microsoft.com/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks)) |
| **üzenetet**                | Sztring | Az üzenet szövege                                     | `MyPackage:Validation has started.` |
| **packageName**            | Sztring | A végrehajtott csomagfájl neve                             | `MyPackage.dtsx` |
| **eventName**              | Sztring | A kapcsolódó futásidejű esemény neve                                 | `OnPreValidate` |
| **messageSourceName**      | Sztring | A csomag-összetevő neve az esemény üzenetének forrásaként         | `Data Flow Task` |
| **messageSourceId**        | Sztring | A csomag-összetevő egyedi azonosítója az esemény üzenetének forrásaként    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | Sztring | Az adatáramlási összetevő neve az esemény üzenetének forrásaként       | `SSIS.Pipeline` |
| **packagePath**            | Sztring | A Package objektum elérési útja az esemény üzenetének forrásaként            | `\Package\Data Flow Task` |
| **executionPath**          | Sztring | A szülő csomag teljes elérési útja a végrehajtott összetevőre            | `\Transformation\Data Flow Task`(Ez az elérési út az összetevők iterációját is rögzíti) |
| **Szálazonosító**               | Sztring | Az esemény üzenetének naplózásakor végrehajtott szál egyedi azonosítója | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>SSIS végrehajtható statisztikai napló attribútumai

Ezek a SSIS-csomag végrehajtása által a SSIS IR-ben generált végrehajtható statisztikák naplózási attribútumai/tulajdonságai, ahol a végrehajtható fájlok tárolók/feladatok a csomagok vezérlési folyamataiban. Hasonló információkat közvetítenek, mint a [SSISDB végrehajtható statisztikai tábla/nézet](https://docs.microsoft.com/sql/integration-services/system-views/catalog-executable-statistics?view=sql-server-ver15) , amely az egyes futó végrehajtható fájlok sorát jeleníti meg, beleértve az iterációkat is. Ezek a naplózási szinten keletkeznek `None` , kivéve, ha a feladat-szintű szűk keresztmetszetek/hibák azonosításához hasznosak.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Tulajdonság                   | Típus   | Leírás                                                      | Példa                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **idő**                   | Sztring | Az esemény időpontja UTC formátumban:`YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Sztring | Ez a következőre van beállítva`YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **Kategória**               | Sztring | A diagnosztikai naplók kategóriája                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | Sztring | Egy adott művelet nyomon követésére szolgáló egyedi azonosító                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Sztring | Az ADF neve                                             | `MyADFv2` |
| **integrationRuntimeName** | Sztring | A SSIS IR neve                                         | `MySSISIR` |
| **szint**                  | Sztring | A diagnosztikai naplók szintje                                     | `Informational` |
| **executionId**            | Sztring | Egy adott végrehajtás követésének egyedi azonosítója a SSISDB-ben      | `1`(1 a nem a SSISDB-ben tárolt csomagokhoz kapcsolódó végrehajtásokat jelenti.) |
| **executionPath**          | Sztring | A szülő csomag teljes elérési útja a végrehajtott összetevőre          | `\Transformation\Data Flow Task`(Ez az elérési út az összetevők iterációját is rögzíti) |
| **startTime**              | Sztring | Az az idő, amikor a végrehajtható fájl a végrehajtás előtti fázisba lép UTC formátumban  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | Sztring | Az az idő, amikor a végrehajtható fájl a végrehajtás utáni fázisba kerül UTC formátumban | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | Sztring | A végrehajtható fájl futási ideje (ezredmásodperc)                   | `1,125` |
| **executionResult**        | Sztring | A végrehajtható fájl futtatásának eredménye                                 | `0`(0 sikert jelez, 1 a hibát jelzi, a 2 a befejezést jelenti, a 3 pedig a megszakítást jelenti) |
| **executionValue**         | Sztring | A végrehajtható fájl futtatásával visszaadott felhasználó által megadott érték            | `1` |
| **resourceId**             | Sztring | Az ADF-erőforrás egyedi azonosítója                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>SSIS-végrehajtási összetevő fázisainak naplózási attribútumai

Ezek a SSIS-csomag végrehajtása által a SSIS IR-ben generált adatfolyam-összetevőkre vonatkozó, a futásidejű statisztikai adatok naplózási attribútumai/tulajdonságai. Hasonló információkat közvetítenek, mint a [SSISDB-végrehajtási összetevő szakasza tábla/nézet](https://docs.microsoft.com/sql/integration-services/system-views/catalog-execution-component-phases?view=sql-server-ver15) , amely az adatfolyam-összetevők által az összes végrehajtási fázisban töltött időt mutatja. Ezek akkor jönnek létre, amikor kiválasztja `Performance/Verbose` a naplózási szint lehetőséget, és hasznos az adatfolyam-végrehajtási statisztikák rögzítéséhez.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Tulajdonság                   | Típus   | Leírás                                                         | Példa                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **idő**                   | Sztring | Az esemény időpontja UTC formátumban:`YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | Sztring | Ez a következőre van beállítva`YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **Kategória**               | Sztring | A diagnosztikai naplók kategóriája                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | Sztring | Egy adott művelet nyomon követésére szolgáló egyedi azonosító                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | Sztring | Az ADF neve                                                | `MyADFv2` |
| **integrationRuntimeName** | Sztring | A SSIS IR neve                                            | `MySSISIR` |
| **szint**                  | Sztring | A diagnosztikai naplók szintje                                        | `Informational` |
| **executionId**            | Sztring | Egy adott végrehajtás követésének egyedi azonosítója a SSISDB-ben         | `1`(1 a nem a SSISDB-ben tárolt csomagokhoz kapcsolódó végrehajtásokat jelenti.) |
| **packageName**            | Sztring | A végrehajtott csomagfájl neve                              | `MyPackage.dtsx` |
| **Feladatnév**               | Sztring | A végrehajtott adatfolyam-feladat neve                                 | `Data Flow Task` |
| **subcomponentName**       | Sztring | Az adatfolyam-összetevő neve                                     | `Derived Column` |
| **fázis**                  | Sztring | A végrehajtási fázis neve                                         | `AcquireConnections` |
| **startTime**              | Sztring | Az az idő, amikor a végrehajtási fázis UTC formátumban indul el                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | Sztring | Az az idő, amikor a végrehajtási fázis UTC formátumban végződik                    | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**          | Sztring | Az adatfolyam-feladat végrehajtásának elérési útja                            | `\Transformation\Data Flow Task` |
| **resourceId**             | Sztring | Az ADF-erőforrás egyedi azonosítója                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>SSIS-végrehajtási adatok statisztikai naplójának attribútumai

Ezek a naplózási attribútumok/tulajdonságok az adatáramlási folyamatok egyes szakaszain keresztül, a felsőbb rétegtől az alárendelt összetevőkig, a SSIS-csomagok végrehajtásával a SSIS IR-n. Hasonló információkat közvetítenek, mint a [SSISDB-végrehajtási adatok statisztikájának táblázata/nézete](https://docs.microsoft.com/sql/integration-services/system-views/catalog-execution-data-statistics?view=sql-server-ver15) , amely az adatfolyam-feladatok által áthelyezett adatsorok számát mutatja. Ezek akkor jönnek létre, amikor kiválasztja `Verbose` a naplózási szint lehetőséget, és hasznos a számítási folyamat adatforgalmának eléréséhez.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Tulajdonság                     | Típus   | Leírás                                                        | Példa                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **idő**                     | Sztring | Az esemény időpontja UTC formátumban:`YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | Sztring | Ez a következőre van beállítva`YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **Kategória**                 | Sztring | A diagnosztikai naplók kategóriája                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | Sztring | Egy adott művelet nyomon követésére szolgáló egyedi azonosító                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | Sztring | Az ADF neve                                               | `MyADFv2` |
| **integrationRuntimeName**   | Sztring | A SSIS IR neve                                           | `MySSISIR` |
| **szint**                    | Sztring | A diagnosztikai naplók szintje                                       | `Informational` |
| **executionId**              | Sztring | Egy adott végrehajtás követésének egyedi azonosítója a SSISDB-ben        | `1`(1 a nem a SSISDB-ben tárolt csomagokhoz kapcsolódó végrehajtásokat jelenti.) |
| **packageName**              | Sztring | A végrehajtott csomagfájl neve                             | `MyPackage.dtsx` |
| **Feladatnév**                 | Sztring | A végrehajtott adatfolyam-feladat neve                                | `Data Flow Task` |
| **dataflowPathIdString**     | Sztring | Az adatfolyam elérési útjának nyomon követésére szolgáló egyedi azonosító                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | Sztring | Az adatfolyam elérési útjának neve                                         | `ADO NET Source Output` |
| **sourceComponentName**      | Sztring | Az adatokat küldő adatfolyam-összetevő neve                    | `SQLDB Table3` |
| **destinationComponentName** | Sztring | Az adatforgalom-összetevő neve, amely az adatfogadást fogadja                 | `Derived Column` |
| **rowsSent**                 | Sztring | A forrás-összetevő által eljuttatott sorok száma                        | `500` |
| **createdTime**              | Sztring | Az az idő, amikor a sorok értéke UTC formátumban lett beolvasva                | `2017-06-28T21:00:27.3534352Z` |
| **executionPath**            | Sztring | Az adatfolyam-feladat végrehajtásának elérési útja                           | `\Transformation\Data Flow Task` |
| **resourceId**               | Sztring | Az ADF-erőforrás egyedi azonosítója                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Log Analytics séma

A Log Analytics a következő kivételekkel örökli a sémát a Figyelőtől:

* Az egyes oszlopok nevének első betűje tőkésített. A "correlationId" oszlop neve például "CorrelationId" a következőben: Log Analytics.
* Nincs "level" oszlop.
* A dinamikus "tulajdonságok" oszlop megmarad a következő dinamikus JSON-blob típusként.

    | Azure Monitor oszlop | Log Analytics oszlop | Típus |
    | --- | --- | --- |
    | $. properties. UserProperties | UserProperties | Dinamikus |
    | $. properties. Széljegyzetek | Széljegyzetek | Dinamikus |
    | $. properties. Bemeneti | Bevitel | Dinamikus |
    | $. properties. Kimeneti | Kimenet | Dinamikus |
    | $. properties. Hiba. errorCode | ErrorCode | int |
    | $. properties. Hiba. üzenet | ErrorMessage | sztring |
    | $. properties. Hiba | Hiba | Dinamikus |
    | $. properties. Korábbi verzióknál | Korábbi verzióknál | Dinamikus |
    | $. properties. Paraméterek | Paraméterek | Dinamikus |
    | $.properties.SystemParameters | SystemParameters | Dinamikus |
    | $. properties. Címkék | Címkék | Dinamikus |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>SSIS-műveletek figyelése Azure Monitor

A SQL Server Integration Services-(SSIS-) számítási feladatok & váltásához a következőt támogató [Azure Data Factory (ADF) SSIS Integration Runtime (IR)](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) :

- Azure SQL Database kiszolgáló/felügyelt példány által üzemeltetett SSIS-katalógusba (SSISDB) telepített csomagok futtatása (projekt-telepítési modell)
- Az Azure SQL felügyelt példányai által üzemeltetett fájlrendszerbe, Azure Filesba vagy SQL Server adatbázisba (MSDB) telepített csomagok futtatása (csomag-telepítési modell)

A kiépítés után a [SSIS IR működési állapotát a Azure PowerShell vagy az ADF-portál **figyelő** központja segítségével ellenőrizheti](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime). A projekt-telepítési modellel a SSIS-csomag végrehajtási naplói a SSISDB belső tábláiban/nézeteiben vannak tárolva, így a rendszer lekérdezheti, elemezheti és vizuálisan megjelenítheti a kijelölt eszközök, például a SQL Server Management Studio (SSMS) segítségével. A csomag üzembe helyezési modelljével a SSIS-csomagok végrehajtási naplói a fájlrendszerben vagy Azure Filesban tárolhatók CSV-fájlként, amelyet még a lekérdezés, az elemzés és a vizuális megjelenítés előtt más kijelölt eszközökkel kell elemezni és feldolgozni.

A [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) integráció révén az SSIS IR-műveletekből és a SSIS-csomagok végrehajtásával létrehozott összes mérőszám és napló lekérdezhető, elemezhető és vizuálisan megjeleníthető a Azure Portalon, miközben a riasztások is megadhatók.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Diagnosztikai beállítások és munkaterület konfigurálása a SSIS-műveletekhez

Ha az SSIS IR-műveletek és a SSIS-csomagok végrehajtásához létrehozott összes mérőszámot és naplót el szeretné küldeni a Azure Monitorra, kövesse a [diagnosztikai beállítások és munkaterületek az ADF-hez való konfigurálásához szükséges](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#configure-diagnostic-settings-and-workspace)részletes útmutatót.

### <a name="ssis-operational-metrics"></a>SSIS működési mérőszámai

A SSIS operatív [mérőszámai](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics) olyan teljesítményszámlálók/numerikus értékek, amelyek a SSIS IR indítási/leállítási műveleteinek állapotát és a SSIS-csomagok végrehajtásának állapotát mutatják be egy adott időpontban. Ezek az [ADF-metrikák](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-metrics)részét képezik Azure monitorban, beleértve az ADF-entitások számának/méretének, tevékenység/folyamat/trigger futtatásának, valamint az IR CPU-kihasználtság/memória/csomópontok számát/várólistáját.

Ha a diagnosztikai beállításokat és a munkaterületet az ADF-hez Azure Monitor konfigurálja, akkor a _AllMetrics_ jelölőnégyzet bejelölésével az Azure Metrikaböngésző, az [Azure irányítópulton és a](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards) [közel valós idejű riasztások](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric)révén elérhetővé válik az [interaktív elemzéshez](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)használható operatív mérőszámok SSIS.

![Adja meg a beállításokat, és válasszon egy log-Analytics-munkaterületet](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>SSIS operatív riasztások

Ha riasztásokat szeretne felvenni az ADF-portálról az operatív metrikák SSIS, [válassza a **riasztások & mérőszámok** lapot az ADF- **figyelő** hubhoz, és kövesse a részletes útmutatást](https://docs.microsoft.com/azure/data-factory/monitor-visually#alerts).

![SSIS operatív riasztások előléptetése az ADF-portálról](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Ha riasztásokat szeretne felvenni a Azure Portal SSIS operatív mérőszámai közül, [válassza az Azure **monitor** hub **riasztások** lapját, és kövesse a részletes útmutatást](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-alerts).

![SSIS operatív riasztások növelése Azure Portal](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>SSIS operatív naplók

A SSIS operatív [naplói](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) a SSIS IR-műveletek és a SSIS-csomagok végrehajtása által generált események, amelyek elegendő kontextust/információt biztosítanak az észlelt problémákkal kapcsolatban, és amelyek a kiváltó okok elemzéséhez hasznosak. 

Ha a diagnosztikai beállításokat és a munkaterületet a Azure Monitor-on lévő ADF-hez konfigurálja, akkor kiválaszthatja a megfelelő SSIS operatív naplókat, és elküldheti azokat az Azure-Adatkezelőon alapuló Log Analyticsre, ahol a részletes [lekérdezési nyelv](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview), az [Azure-irányítópulton való megjelenítés és a](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards) [közel valós idejű riasztások](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)révén elérhetővé válik.

![Adja meg a beállításokat, és válasszon egy log-Analytics-munkaterületet](media/data-factory-monitor-oms/monitor-oms-image2.png)

A Azure Monitor és Log Analytics SSIS-csomag-végrehajtási naplói sémái és tartalmai ugyanúgy hasonlítanak egymáshoz, mint a belső táblák/nézetek SSISDB.

| Azure Monitor naplózási kategóriák          | Táblák Log Analytics                     | SSISDB belső táblák/nézetek              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

További információ a SSIS operatív napló attribútumairól/tulajdonságairól: [Azure monitor és log Analytics sémák az ADF-hez](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#schema-of-logs-and-events).

A kiválasztott SSIS-csomag-végrehajtási naplókat mindig a Log Analytics küldik a Meghívási módszertől függetlenül, például az Azure-kompatibilis SQL Server Data Tools (SSDT), a T-SQL használatával a SSMS/SQL Server Agent/egyéb kijelölt eszközökön, illetve a SSIS-csomag végrehajtásának trigger/sandbox/debug futtatásával az ADF-folyamatokban.

Ha a SSIS-csomag végrehajtási naplóit kérdezi le a naplók Analyticsben, akkor a OperationId/ExecutionId/CorrelationId tulajdonságok használatával csatlakozhat hozzájuk. A OperationId/ExecutionId mindig 1 értékre vannak állítva a SSISDB-ben **nem** tárolt csomagokhoz kapcsolódó összes művelet/végrehajtás esetén.

![SSIS-csomag-végrehajtási naplók lekérdezése Log Analytics](media/data-factory-monitor-oms/log-analytics-query.png)

## <a name="next-steps"></a>További lépések
[Folyamatok programozott figyelése és kezelése](monitor-programmatically.md)
