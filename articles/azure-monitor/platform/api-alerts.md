---
title: A Log Analytics riasztási REST API használatával
description: A Log Analytics riasztási REST API lehetővé teszi a riasztások létrehozását és kezelését Log Analyticsban, amely Log Analytics részét képezi.  Ez a cikk részletesen az API-val és néhány példa a különféle műveletek végezhetők.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: a85dad2ba638505233e5df769e55fa5bd7b8dafd
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665000"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Hozzon létre, és a Log Analytics REST API-val riasztási szabályok kezelése 

A Log Analytics Alert REST API lehetővé teszi, hogy hozhat létre, és a Log Analytics-riasztások kezelése.  Ez a cikk részletesen az API-val és néhány példa a különféle műveletek végezhetők.

> [!IMPORTANT]
> Amint azt [korábban bejelentettük](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/), a *2019. június 1* . után létrehozott log Analytics-munkaterület (ek) a riasztási szabályokat **csak** az Azure ScheduledQueryRules [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/), az [Azure Resource Mananger-sablon](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) és a PowerShell- [parancsmag](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell)használatával tudják kezelni. Az ügyfelek könnyedén [válthatnak a riasztási szabályok kezeléséhez](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) a régebbi munkaterületek számára, hogy Azure monitor scheduledQueryRules használják az alapértelmezettként, és számos [új előnyt](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) szerezzenek, mint például a natív PowerShell-parancsmagok használata, a szabályok megnövelt lookback időszaka, a szabályok létrehozása külön erőforráscsoport vagy előfizetés esetén, és még sok minden más.

A Log Analytics Search REST API RESTful és az Azure Resource Manager REST API-n keresztül érhető el. Ebben a dokumentumban olyan példákat talál, amelyekben az API egy PowerShell-parancssorból érhető el az [ARMClient](https://github.com/projectkudu/ARMClient)használatával, amely leegyszerűsíti a Azure Resource Manager API meghívását. ARMClient és a PowerShell használata a Log Analytics Search API eléréséhez számos lehetőség. Ezekkel az eszközökkel a REST-alapú Azure Resource Manager API Log Analytics-munkaterületek hívásokat, és végezze el a keresési parancsok azokon belül használhat. Az API-t fog keresési eredményeket is JSON formátumban, lehetővé téve, hogy programozott módon használja a keresési eredmények között számos különböző módon.

## <a name="prerequisites"></a>Előfeltételek
Jelenleg riasztások csak hozhatja létre a Log Analytics mentett keresést.  További információért tekintse meg a [naplóbeli keresés REST API](../../azure-monitor/log-query/log-query-overview.md) .

## <a name="schedules"></a>Ütemezések
Mentett keresés egy vagy több ütemezés is rendelkezhet. Az ütemezés határozza meg, hogy milyen gyakran a keresés Futtatás és az az időintervallum, amelyen a feltétel azonosítja.
Ütemezések a tulajdonságokkal rendelkeznek, az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| Intervallum |Milyen gyakran fut a keresés. Mért percek alatt. |
| QueryTimeSpan |Az időintervallum, amelyen a feltétel értékeli ki. Intervallum nagyobbnak vagy azzal egyenlőnek kell. Mért percek alatt. |
| Verzió |A használt API-verzió.  Jelenleg ez mindig meg kell 1-re. |

Vegyük példaként egy esemény-lekérdezést a 15 perces időközt, és a egy 30 perces időtartam. Ebben az esetben a lekérdezés 15 percenként fogja futtatni, és a riasztás akkor aktiválódik, ha a kritériumok továbbra is igaz over feloldani egy 30 perces időtartam.

### <a name="retrieving-schedules"></a>Ütemezések beolvasása
A Get metódust használatával lekérheti az összes ütemezésekkel egy mentett keresés.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

Egy ütemezés azonosítót a Get metódust használatával lekérheti az egy meghatározott ütemezést egy mentett keresés.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Következő egy mintaválaszt ütemezés szerint.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/sampleRG/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15,
            "Enabled": true,
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Ütemezés létrehozása
Egy ütemezés egyedi azonosítót a Put metódust használatával hozzon létre egy új ütemezést.  Két ütemterv nem rendelkezhet ugyanazzal az AZONOSÍTÓval, még akkor sem, ha különböző mentett keresésekhez vannak társítva.  A Log Analytics-konzolon létrehozott egy ütemezés, egy GUID jön létre a ütemezés azonosítóját.

> [!NOTE]
> A név minden mentett keresést, ütemezését és a Log Analytics API-val létrehozott műveleteket kisbetűs kell lennie.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Az ütemezés módosítása
A Put metódust meglévő ütemezett AZONOSÍTÓval használhatja ugyanazon mentett kereséshez az adott ütemterv módosításához; az alábbi példában az ütemterv le van tiltva. A kérelem törzsének tartalmaznia kell az ütemterv *ETAG* .

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Ütemezések törlése
Egy ütemezés azonosítójú a Delete metódus használatával törölheti az ütemezés.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Műveletek
Egy több művelet is lehet. Művelet egy vagy több folyamat végrehajtásához, például egy levelet küld, vagy a runbook indítása adhat meg, vagy azt adhat meg egy küszöbértéket, amely azt határozza meg, ha a keresési eredmények bizonyos feltételeknek megfelelő-e.  Bizonyos műveleteket meghatározzuk is, hogy a folyamatok hajtja végre, hogy a küszöbértéket.

Minden művelet a következő táblázatban tárolja a tulajdonságokat.  Riasztások különböző típusait rendelkezik másik további tulajdonságok, amelyek az alábbiakban tekintheti át.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |A művelet típusa.  Jelenleg a lehetséges értékek: riasztás és a Webhook. |
| `Name` |A riasztás megjelenítendő neve. |
| `Version` |A használt API-verzió.  Jelenleg ez mindig meg kell 1-re. |

### <a name="retrieving-actions"></a>Műveletek beolvasása

A Get metódust használatával lekérheti az összes művelet ütemezés.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

A művelet azonosítójú a Get metódust használatával lekérheti az egy adott művelet ütemezés.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Létrehozási és szerkesztési műveletek
A Put metódust használja az ütemezéshez hozhat létre egy új művelet egyedi azonosítójú művelet.  A Log Analytics-konzolon egy műveletet hoz létre, amikor egy GUID Azonosítót van-e a művelet azonosítóját.

> [!NOTE]
> A név minden mentett keresést, ütemezését és a Log Analytics API-val létrehozott műveleteket kisbetűs kell lennie.

Az ütemezés módosításához használja az ugyanazon mentett keresésnek egy meglévő művelet azonosítójú a Put metódust.  A kérelem törzsében tartalmaznia kell az etag címkéje az ütemezést.

Ezért ezekben a példákban vannak megadva, az alábbi szakaszok a művelet típusa eltérő a egy új művelet létrehozására vonatkozó kérés formátuma.

### <a name="deleting-actions"></a>Műveletek törlése

A művelet azonosítójú a Delete metódus használatával törölhet olyan műveleteket.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Riasztási műveletek
Ütemezés csak egy riasztás művelet kell rendelkeznie.  Riasztási műveletek rendelkezik legalább egy, az alábbi táblázatban a szakaszok.  Az egyes részletesebben az alábbi ismertetjük.

| Section | Leírás | Használat |
|:--- |:--- |:--- |
| Küszöbérték |Ha a művelet futtatása feltételeit.| Szükséges minden egyes riasztás előtt vagy után bővítve lettek az Azure-bA. |
| Severity |Adatvezérelt riasztás osztályozására szolgáló címkéje.| Szükséges minden egyes riasztás előtt vagy után bővítve lettek az Azure-bA. |
| Mellőzés |Lehetőség van a riasztásból értesítéseket leállítani. | Minden egyes riasztás esetén nem kötelező előtt vagy után bővítve lettek az Azure-bA. |
| Műveletcsoportok |Ahol szükséges műveletek meg van adva, az Azure ActionGroup azonosítói, például - e-mailek, SMSs, intézett Hanghívásokhoz, Webhookok, Automation-Runbookok, ITSM-összekötő, stb.| Ha a riasztások bővítve lettek az Azure-bA szükséges|
| Műveletek testreszabása|A normál a kimenetbe ActionGroup a select műveletek módosítása| Minden riasztás esetén nem kötelező használható után a riasztások bővítve lettek az Azure-bA. |

### <a name="thresholds"></a>Küszöbértékek
Riasztási művelet rendelkeznie kell egy és csakis egy küszöbértéket.  A mentett keresés eredménye megfelel a küszöbérték, hogy a keresés társított művelet, ha a művelet a bármely más folyamatok futnak.  Egy műveletet is tartalmazhat, csak egy küszöbértéket, hogy más típusú, amelyek nem tartalmazzák a küszöbértékek műveletekhez használható.

Küszöbértékek a tulajdonságokkal rendelkeznek, az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Operator` |A küszöbérték-összehasonlítás operátort. <br> gt = nagyobb, mint <br> lt = kisebb, mint |
| `Value` |Értéke a küszöbérték. |

Vegyük példaként egy esemény lekérdezés 15 perc, 30 perces Timespan és egy küszöbértéket, a 10-nél nagyobb időközzel. Ebben az esetben a lekérdezés 15 percenként fogja futtatni, és a riasztás akkor aktiválódik, ha a 10 események 30 perces időtartam létrehozott adott vissza.

Következő művelet csak egy küszöbértékkel egy mintaválasz.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Version": 1
    }

A művelet egyedi azonosítója a Put metódust használatával hozzon létre egy új küszöbérték műveletet ütemezés szerint.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Egy küszöbérték műveletet egy ütemezés módosításához használja egy meglévő azonosítójú művelet a Put metódust.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
A log Analytics lehetővé teszi, hogy a könnyebb felügyeletet és osztályozási kategóriákba a riasztások besorolása. A riasztás súlyosságát definiálva van: információs, figyelmeztetési és kritikus fontosságú. Ezek az Azure Alerts as normalizált súlyossági méretezése van leképezve:

|Log Analytics súlyossági szint  |Azure-riasztások súlyossági szint  |
|---------|---------|
|`critical` |SEV 0|
|`warning` |Sev 1|
|`informational` | Sev 2|

Következő csak egy küszöbét és súlyosságát a művelet egy mintaválasz. 

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Severity": "critical",
        "Version": 1    }

A művelet egyedi azonosítója a Put metódust használatával hozzon létre egy új művelet ütemezés súlyossági.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Egy súlyossági műveletet egy ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Mellőzés
A log Analytics-alapú riasztások aktiválódnak, minden alkalommal, amikor küszöbérték van elérte vagy túllépte a lekérdezést. A hallgatólagos a lekérdezésben logika alapján, emiatt előfordulhat, hogy az adott időközönként sorozata első aktivált riasztás, és így értesítést is küld a rendszer folyamatosan. Ilyen forgatókönyv megelőzése érdekében mellőzése beállítást a Log Analytics ezt kell várni egy sorba mennyi idő elteltével az értesítés aktiválódik, amikor a riasztási szabály beállítását. Tehát ha le van állítva; 30 percig Ezután a riasztás első alkalommal aktiválódik, és konfigurált értesítések küldése. De Várjon 30 percet, mielőtt újra használta a riasztási szabály értesítést. A köztes időszakban riasztási szabály továbbra is működni fog - csak értesítési van, amelyeket az Eseményszabályozás Log Analytics számára megadott időpontban, függetlenül attól, hogy hányszor a riasztási szabály aktiválva ebben az időszakban.

Log Analytics riasztási szabály tulajdonságának letiltása a *szabályozási* érték és a letiltási időszak használatával a *DurationInMinutes* érték használatával.

A következő példa egy olyan műveletre adott válasz, amely csak a küszöbértéket, a súlyosságot és a letiltási tulajdonságot követi

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My threshold action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Throttling": {
          "DurationInMinutes": 30
        },
        "Severity": "critical",
        "Version": 1    }

A művelet egyedi azonosítója a Put metódust használatával hozzon létre egy új művelet ütemezés súlyossági.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Egy súlyossági műveletet egy ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Műveletcsoportok
Az Azure-ban, az összeset műveletcsoport használja az alapértelmezett mechanizmusként műveletek kezelésére. A műveletcsoport adja meg a műveletet egyszer, és társíthatja a műveletcsoport több riasztás – az Azure-ban. Nem szükséges, ismételten deklarálja és újra ugyanazokat a műveleteket. Műveletcsoportok támogatja a több műveletek – például az e-mailben, SMS, hanghívás, az ITSM-kapcsolatot, Automation-Runbook, Webhook URI. 

Azok a felhasználók, akik kibővítették a riasztásokat az Azure-ba, a riasztás létrehozásához a küszöbértékkel együtt át kell adni a műveleti csoport részleteit. Az e-mailek adatait, a webhook URL-jeit, a Runbook Automation részleteit és az egyéb műveleteket a riasztás létrehozása előtt először a műveleti csoportba kell definiálni. Létrehozhat [műveleti csoportot Azure monitor](../../azure-monitor/platform/action-groups.md) a portálon, vagy használhatja a [műveleti csoport API](https://docs.microsoft.com/rest/api/monitor/actiongroups)-t.

Műveletcsoport társítása egy riasztás hozzáadásához adja meg az egyedi Azure Resource Manager-Azonosítót a műveletcsoport, a riasztás definíciójának. A minta ábra lejjebb:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
        },
        "Severity": "critical",
        "Version": 1
      },

Használ a Put metódust a művelet egyedi azonosítója már meglévő műveletcsoport ütemezés.  Az alábbiakban látható a használati minta ábrája.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Műveletcsoport hozzárendelt ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Műveletek testreszabása
Alapértelmezett műveletek kövesse a Normál sablon és az értesítések formátuma. De a felhasználó szabhatja bizonyos műveleteket, még akkor is, ha azok Műveletcsoportok által vezérelt. Testreszabás jelenleg az E-mail tárgyát és a Webhook hasznos adatai.

##### <a name="customize-e-mail-subject-for-action-group"></a>Testre szabhatja az E-Mail tárgyát műveletcsoport
Alapértelmezés szerint az e-mailek tárgya a riasztások esetében: riasztási értesítési `<AlertName>` `<WorkspaceName>`. Azonban ez testre szabható, így is szó vagy címkék –, hogy könnyedén alkalmazni az Állapotszűrő szabályok a Beérkezett üzenetek mappában. A Testreszabás e-mail részletei kell küldenie az alábbi példa a ActionGroup részleteivel együtt.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

A Put metódust használata a művelet egyedi azonosítója már meglévő műveletcsoport társítandó ütemezés testreszabása.  Az alábbiakban látható a használati minta ábrája.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Műveletcsoport hozzárendelt ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>A műveletcsoport testre szabhatja a Webhook hasznos adatai
Alapértelmezés szerint a log Analytics műveletcsoport küldött webhook struktúrája egy rögzített. Azonban egy adott változók támogatott, a webhook-végpontot követelményeinek segítségével testreszabhatja a JSON-adattartalmat. További információ: [webhook művelet a naplózási riasztási szabályokhoz](../../azure-monitor/platform/alerts-log-webhook.md). 

A Testreszabás webhook részletei kell küldeni ActionGroup részleteivel együtt, és alkalmazható az összes Webhook belül a műveletcsoport; a megadott URI az alábbi példa.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a description here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ],
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

A Put metódust használata a művelet egyedi azonosítója már meglévő műveletcsoport társítandó ütemezés testreszabása.  Az alábbiakban látható a használati minta ábrája.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Műveletcsoport hozzárendelt ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>Következő lépések

* A [REST API használatával hajthat végre naplóbeli keresést](../../azure-monitor/log-query/log-query-overview.md) a log Analytics.
* További tudnivalók a [naplózási riasztásokról az Azure monitorban](../../azure-monitor/platform/alerts-unified-log.md)
* A [naplók riasztási szabályainak létrehozása, szerkesztése és kezelése az Azure monitorban](../../azure-monitor/platform/alerts-log.md)

