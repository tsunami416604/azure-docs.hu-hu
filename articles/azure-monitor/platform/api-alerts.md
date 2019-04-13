---
title: A Log Analytics riasztási REST API használatával
description: A Log Analytics Alert REST API lehetővé teszi, hogy hozhat létre, és a Log Analytics részét képező Log Analytics-riasztások kezelése.  Ez a cikk részletesen az API-val és néhány példa a különféle műveletek végezhetők.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/10/2018
ms.author: bwren
ms.openlocfilehash: bee64909c7f3b295691ef1cb1840424aa7e3fe49
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549712"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Hozzon létre, és a Log Analytics REST API-val riasztási szabályok kezelése
A Log Analytics Alert REST API lehetővé teszi, hogy hozhat létre, és a Log Analytics-riasztások kezelése.  Ez a cikk részletesen az API-val és néhány példa a különféle műveletek végezhetők.

A Log Analytics Search REST API RESTful és az Azure Resource Manager REST API-n keresztül érhető el. Ebben a dokumentumban talál példákat, az API-t szeretné elérni, egy PowerShell parancssori használatával [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása. ARMClient és a PowerShell használata a Log Analytics Search API eléréséhez számos lehetőség. Ezekkel az eszközökkel a REST-alapú Azure Resource Manager API Log Analytics-munkaterületek hívásokat, és végezze el a keresési parancsok azokon belül használhat. Az API-t fog keresési eredményeket is JSON formátumban, lehetővé téve, hogy programozott módon használja a keresési eredmények között számos különböző módon.

## <a name="prerequisites"></a>Előfeltételek
Jelenleg riasztások csak hozhatja létre a Log Analytics mentett keresést.  Olvassa el a [Log Search REST API](../../azure-monitor/log-query/log-query-overview.md) további információt.

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
Egy ütemezés egyedi azonosítót a Put metódust használatával hozzon létre egy új ütemezést.  Vegye figyelembe, hogy két ütemezések rendelkezhet ugyanazzal az Azonosítóval, még akkor is, ha azok különböző a mentett keresések.  A Log Analytics-konzolon létrehozott egy ütemezés, egy GUID jön létre a ütemezés azonosítóját.

> [!NOTE]
> A név minden mentett keresést, ütemezését és a Log Analytics API-val létrehozott műveleteket kisbetűs kell lennie.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Az ütemezés módosítása
A Put metódust használja egy meglévő ütemezés azonosítót ugyanazon mentett keresés adott ütemezésének; módosítása az alábbi példában az ütemterv le lett tiltva. A kérelem törzsében tartalmaznia kell a *etag* az ütemezés.

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

> [!NOTE]
> 2018. május 14., kezdve az összeset együtt a Log Analytics-munkaterületet az Azure nyilvános felhő példányát automatikusan kiterjesztik az Azure-bA. A felhasználó is önkéntesen riasztások kiterjesztésének kezdeményezése az Azure-ra 2018. május 14. előtt. További információkért lásd: [riasztások kiterjesztése az Azure Log Analytics](../../azure-monitor/platform/alerts-extend.md). A felhasználók számára, hogy a riasztások kiterjesztése az Azure-bA műveletek most már az Azure action groups általi szabályozza. Egy munkaterületet, és a riasztások ki vannak bővítve az Azure-ba, amikor beolvasni, vagy a műveletek hozzáadása a [műveleti csoport API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

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

> [!NOTE]
> 2018. május 14., kezdve az összeset együtt a Log Analytics-munkaterületet az Azure nyilvános felhő példányát automatikusan kiterjesztik az Azure-bA. A felhasználó is önkéntesen riasztások kiterjesztésének kezdeményezése az Azure-ra 2018. május 14. előtt. További információkért lásd: [riasztások kiterjesztése az Azure Log Analytics](../../azure-monitor/platform/alerts-extend.md). A felhasználók számára, hogy a riasztások kiterjesztése az Azure-bA műveletek most már az Azure action groups általi szabályozza. Egy munkaterületet, és a riasztások ki vannak bővítve az Azure-ba, amikor beolvasni, vagy a műveletek hozzáadása a [műveleti csoport API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

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
| EmailNotification |E-mail küldés több címzettnek. | Nem kötelező, ha a riasztások bővítve lettek az Azure-bA|
| Szervizelés |Runbook indítása az Azure Automationben azonosított probléma elhárításának megkísérlése céljából. |Nem kötelező, ha a riasztások bővítve lettek az Azure-bA|
| Webhook-műveletek | Riasztások, adatok leküldése a szolgáltatás kívánt JSON-fájlként |Nem kötelező, ha a riasztások bővítve lettek az Azure-bA|

> [!NOTE]
> 2018. május 14., kezdve az összeset együtt a Log Analytics-munkaterületet az Azure nyilvános felhő példányát automatikusan kiterjesztik az Azure-bA. A felhasználó is önkéntesen riasztások kiterjesztésének kezdeményezése az Azure-ra 2018. május 14. előtt. További információkért lásd: [riasztások kiterjesztése az Azure Log Analytics](../../azure-monitor/platform/alerts-extend.md).

#### <a name="thresholds"></a>Küszöbértékek
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

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Egy küszöbérték műveletet egy ütemezés módosításához használja egy meglévő azonosítójú művelet a Put metódust.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
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

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Egy súlyossági műveletet egy ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Mellőzés
A log Analytics-alapú riasztások aktiválódnak, minden alkalommal, amikor küszöbérték van elérte vagy túllépte a lekérdezést. A hallgatólagos a lekérdezésben logika alapján, emiatt előfordulhat, hogy az adott időközönként sorozata első aktivált riasztás, és így értesítést is küld a rendszer folyamatosan. Ilyen forgatókönyv megelőzése érdekében mellőzése beállítást a Log Analytics ezt kell várni egy sorba mennyi idő elteltével az értesítés aktiválódik, amikor a riasztási szabály beállítását. Tehát ha le van állítva; 30 percig Ezután a riasztás első alkalommal aktiválódik, és konfigurált értesítések küldése. De Várjon 30 percet, mielőtt újra használta a riasztási szabály értesítést. A köztes időszakban riasztási szabály továbbra is működni fog - csak értesítési van, amelyeket az Eseményszabályozás Log Analytics számára megadott időpontban, függetlenül attól, hogy hányszor a riasztási szabály aktiválva ebben az időszakban.

Riasztási szabály használatával van megadva a Log Analytics tulajdonságát mellőzése a *sávszélesség-szabályozási* érték és a Tiltási időtartam használatával *DurationInMinutes* értéket.

Következő egy minta válasz csak egy küszöbértéket súlyosság, a művelet, és mellőzheti tulajdonság

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

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Egy súlyossági műveletet egy ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Műveletcsoportok
Az Azure-ban, az összeset műveletcsoport használja az alapértelmezett mechanizmusként műveletek kezelésére. A műveletcsoport adja meg a műveletet egyszer, és társíthatja a műveletcsoport több riasztás – az Azure-ban. Nem szükséges, ismételten deklarálja és újra ugyanazokat a műveleteket. Műveletcsoportok támogatja a több műveletek – például az e-mailben, SMS, hanghívás, az ITSM-kapcsolatot, Automation-Runbook, Webhook URI. 

Felhasználók számára, akik ki van bővítve a riasztások az Azure-bA – ütemezés most rendelkezik küszöbértéket, riasztást létrehozni a együtt átadott műveletcsoport részletei. E-mail adatai, a Webhook URL-címek, Runbook-automatizálási részleteit és más műveletek, kell lennie; riasztás létrehozása előtt műveletcsoport ügyféloldali meghatározott létrehozhat egy [műveleti csoport az Azure Monitor](../../azure-monitor/platform/action-groups.md) a portálon vagy [műveleti csoport API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

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

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Műveletcsoport hozzárendelt ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Műveletek testreszabása
Alapértelmezett műveletek kövesse a Normál sablon és az értesítések formátuma. De a felhasználó szabhatja bizonyos műveleteket, még akkor is, ha azok Műveletcsoportok által vezérelt. Testreszabás jelenleg az E-mail tárgyát és a Webhook hasznos adatai.

##### <a name="customize-e-mail-subject-for-action-group"></a>Testre szabhatja az E-Mail tárgyát műveletcsoport
Alapértelmezés szerint a riasztások e-mailek tárgyához van: Figyelmeztető értesítés `<AlertName>` a `<WorkspaceName>`. Azonban ez testre szabható, így is szó vagy címkék –, hogy könnyedén alkalmazni az Állapotszűrő szabályok a Beérkezett üzenetek mappában. A Testreszabás e-mail részletei kell küldenie az alábbi példa a ActionGroup részleteivel együtt.

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

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Műveletcsoport hozzárendelt ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>A műveletcsoport testre szabhatja a Webhook hasznos adatai
Alapértelmezés szerint a log Analytics műveletcsoport küldött webhook struktúrája egy rögzített. Azonban egy adott változók támogatott, a webhook-végpontot követelményeinek segítségével testreszabhatja a JSON-adattartalmat. További információkért lásd: [naplóriasztási szabály a Webhook művelettel](../../azure-monitor/platform/alerts-log-webhook.md). 

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

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Műveletcsoport hozzárendelt ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>Értesítés e-mailben
E-mail-értesítések küldése egy vagy több címzett e-mail.  Azok a különféle tulajdonságokat tartalmaznak, az alábbi táblázatban.

> [!NOTE]
> 2018. május 14., kezdve az összeset együtt a Log Analytics-munkaterületet az Azure nyilvános felhő példányát automatikusan kiterjesztik az Azure-bA. A felhasználó is önkéntesen riasztások kiterjesztésének kezdeményezése az Azure-ra 2018. május 14. előtt. További információkért lásd: [riasztások kiterjesztése az Azure Log Analytics](../../azure-monitor/platform/alerts-extend.md). A felhasználók számára, hogy a riasztások kiterjesztése az Azure-bA például az e-mailben értesítést most már az Azure action groups általi szabályozza. Egy munkaterületet, és a riasztások ki vannak bővítve az Azure-ba, amikor beolvasni, vagy a műveletek hozzáadása a [műveleti csoport API](https://docs.microsoft.com/rest/api/monitor/actiongroups).
   

| Tulajdonság | Leírás |
|:--- |:--- |
| Címzettek |E-mail címek listáját. |
| Tárgy |Az e-mail tárgya. |
| Melléklet |A mellékletek jelenleg nem támogatottak, így ez mindig lesz a egy értéke "None." |

Következő egy e-mail-értesítési művelet egy küszöbértékkel mintaválaszt.  

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My email action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "EmailNotification": {
            "Recipients": [
                "recipient1@contoso.com",
                "recipient2@contoso.com"
            ],
            "Subject": "This is the subject",
            "Attachment": "None"
        },
        "Version": 1
    }

Egy új e-mail-művelet ütemezés létrehozásához használja a Put metódust a művelet egyedi azonosítója.  A következő példában létrehozunk egy küszöbértékkel e-mailben értesítést, így az e-mailt akkor küldi a rendszer, amikor a mentett keresés eredményei túllépik a küszöbértéket.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Ütemezett e-mail művelet módosításához használja a Put metódust egy már létező azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Szervizelési műveletek
Szervizelések el egy runbookot, amely megpróbálja a riasztás által azonosított probléma az Azure automationben.  Hozzon létre egy webhookot a runbookhoz, a szervizelési művelet használni kell, és adja meg az URI-t a WebhookUri tulajdonság.  Ez a művelet az Azure portal használatával hoz létre, amikor egy új webhook automatikusan létrejön a runbook.

> [!NOTE]
> 2018. május 14., kezdve az összeset együtt a Log Analytics-munkaterületet az Azure nyilvános felhő példányát automatikusan kiterjesztik az Azure-bA. A felhasználó is önkéntesen riasztások kiterjesztésének kezdeményezése az Azure-ra 2018. május 14. előtt. További információkért lásd: [riasztások kiterjesztése az Azure Log Analytics](../../azure-monitor/platform/alerts-extend.md). A felhasználók számára, hogy a riasztások kiterjesztése az Azure-bA például a runbook használata szervizelési műveletek most már az Azure action groups általi szabályozza. Egy munkaterületet, és a riasztások ki vannak bővítve az Azure-ba, amikor beolvasni, vagy a műveletek hozzáadása a [műveleti csoport API](https://docs.microsoft.com/rest/api/monitor/actiongroups).

Szervizelések tulajdonságait az alábbi táblázat tartalmazza.

| Tulajdonság | Leírás |
|:--- |:--- |
| RunbookName |A runbook neve. Ennek egyeznie kell egy közzétett runbook az automation-fiókban az Automation-megoldás, az a Log Analytics-munkaterületen konfigurált. |
| WebhookUri |A webhook URI Azonosítóját. |
| Lejárat |A lejárati dátum és idő, a webhook.  Ha a webhook nem rendelkezik egy lejárati, majd ez lehet bármely érvényes jövőbeli dátum. |

Következő egy mintaválasz egy küszöbértékkel szervizelési művelethez.

    "etag": "W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"",
    "properties": {
        "Type": "Alert",
        "Name": "My remediation action",
        "Threshold": {
            "Operator": "gt",
            "Value": 10
        },
        "Remediation": {
            "RunbookName": "My-Runbook",
            "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d",
            "Expiry": "2018-02-25T18:27:20"
            },
        "Version": 1
    }

Új javítási műveletet egy ütemezés létrehozásához használja a Put metódust a művelet egyedi azonosítója.  A következő példában létrehozunk egy javítási egy küszöbértékkel, így a runbook indítását, ha a mentett keresés eredményei túllépik a küszöbértéket.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Egy meglévő azonosítójú művelet a Put metódust használhat egy javítási műveletet egy ütemezés módosításához.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Példa
Következő, hozzon létre egy új e-mail-értesítés egy teljes példát.  Ez létrehoz egy új ütemezést, és a egy küszöbértéket és e-mailt tartalmazó műveletet.

    $subscriptionId = "3d56705e-5b26-5bcc-9368-dbc8d2fafbfc"
    $resourceGroup  = "MyResourceGroup"    
    $workspaceName    = "MyWorkspace"
    $searchId       = "MySearch"
    $scheduleId     = "MySchedule"
    $thresholdId    = "MyThreshold"
    $actionId       = "MyEmailAction"
    
    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/?api-version=2015-03-20 $scheduleJson
    
    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Severity':'Warning', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/savedSearches/$searchId/schedules/$scheduleId/actions/$actionId/?api-version=2015-03-20 $emailJson

#### <a name="webhook-actions"></a>Webhook-műveletek
Webhook-műveletek egy folyamat meghívása egy URL-címet és szükség esetén elküldendő hasznos megkezdéséhez.  Azok javítási műveletek hasonló azzal a különbséggel, nem tér ki rájuk, amelyek aktiválják előfordulhat, hogy az Azure Automation-runbookok eltérő folyamatok webhookok.  Ezenkívül tartalmaznak további lehetőséget kínál a hasznos kell továbbítani a távoli folyamat.

> [!NOTE]
> 2018. május 14., kezdve az összeset együtt a Log Analytics-munkaterületet az Azure nyilvános felhő példányát automatikusan kiterjesztik az Azure-bA. A felhasználó is önkéntesen riasztások kiterjesztésének kezdeményezése az Azure-ra 2018. május 14. előtt. További információkért lásd: [riasztások kiterjesztése az Azure Log Analytics](../../azure-monitor/platform/alerts-extend.md). A felhasználók számára, hogy a riasztások kiterjesztése az Azure-bA műveletek, például a Webhook mostantól az Azure action groups általi szabályozza. Egy munkaterületet, és a riasztások ki vannak bővítve az Azure-ba, amikor beolvasni, vagy a műveletek hozzáadása a [műveleti csoport API](https://docs.microsoft.com/rest/api/monitor/actiongroups).


Webhook-műveletek nem rendelkezik egy küszöbértéket, de ehelyett hozzá kell adni egy ütemezést, amely rendelkezik a riasztási művelet a küszöbértékkel.  

Következő webhook művelet és a egy küszöbértékkel kapcsolódó riasztási művelet a mintaválaszt.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"",
                "properties": {
                    "Type": "Webhook",
                    "Name": "My Webhook Action",
                    "WebhookUri": "https://oaaswebhookdf.cloudapp.net/webhooks?token=VfkYTIlpk%2fc%2bJBP",
                    "CustomPayload": "{\"fielld1\":\"value1\",\"field2\":\"value2\"}",
                    "Version": 1
                }
            },
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
                "etag": "W/\"datetime'2016-02-26T20%3A25%3A00.565204Z'\"",
                "properties": {
                    "Type": "Alert",
                    "Name": "Threshold for my webhook action",
                    "Threshold": {
                        "Operator": "gt",
                        "Value": 10
                    },
                    "Version": 1
                }
            }
        ]
    }

##### <a name="create-or-edit-a-webhook-action"></a>Hozzon létre vagy egy webhook művelettel szerkesztése
A művelet egyedi azonosítója a Put metódust használatával hozzon létre egy új webhook művelettel ütemezés.  Az alábbi példa létrehoz egy Webhook művelet és a riasztási művelet a küszöbértéket, hogy a webhook akkor aktiválódik, amikor a mentett keresés eredményei túllépik a küszöbértéket.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

Egy webhook művelettel ütemezés módosításához használja a Put metódust egy már létező azonosítójú művelet.  A kérelem törzsében tartalmaznia kell az etag címkéje a műveletet.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>További lépések
* Használja a [REST API-t, hajtsa végre a naplókeresések](../../azure-monitor/log-query/log-query-overview.md) a Log Analyticsben.
* Ismerje meg [naplóriasztások az azure-riasztások](../../azure-monitor/platform/alerts-unified-log.md)

