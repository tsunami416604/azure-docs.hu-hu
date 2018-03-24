---
title: OMS napló Analytics riasztási REST API használatával
description: A napló Analytics riasztási REST API-t kezelheti a riasztásokat az Operations Management Suite (OMS) része Naplóelemzési teszi lehetővé.  Ez a cikk ismerteti az API-t, és néhány példa a másik műveletet hajt végre.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 628ad256-7181-4a0d-9e68-4ed60c0f3f04
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ed0ac6e2041ef503470f7317a5736deecd1d2b8f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Hozzon létre, és a REST API-val Naplóelemzési riasztási szabályok kezelése
A napló Analytics riasztási REST API lehetővé teszi, hogy hozhat létre és kezelheti a riasztásokat az Operations Management Suite (OMS).  Ez a cikk ismerteti az API-t, és néhány példa a másik műveletet hajt végre.

A napló Analytics Search REST API RESTful, és az Azure Resource Manager REST API-n keresztül érhető el. Ebben a dokumentumban található példák az elérését az API-t a PowerShell parancssori használatával [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása. A ARMClient és a PowerShell használata a napló Analytics Search API eléréséhez számos lehetőség. Ezekkel az eszközökkel használhatja az Azure Resource Manager RESTful API-hívások indítása az OMS-munkaterület, és rajtuk keresési parancsok végrehajtása. Az API-t kimeneti fog keresési eredmények Önnek JSON formátumban, hogy lehetővé teszi a programozott módon használja a keresési eredmények között számos különböző módja.

## <a name="prerequisites"></a>Előfeltételek
Jelenleg riasztásokat csak hozhatja létre a Naplóelemzési mentett keresés.  Olvassa el a [napló Search REST API](log-analytics-log-search-api.md) további információt.

## <a name="schedules"></a>Ütemezések
A mentett kereséseket rendelkezhet egy vagy több ütemezés. Az ütemezés határozza meg, hogy milyen gyakran a keresés futtatása és a időtartam alatt, amelyben a feltétel azonosítja.
Ütemezések a jellemzőkkel rendelkezik, az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| Időköz |Milyen gyakran fut a keresést. Percben értendő. |
| QueryTimeSpan |Az időtartam, amely a feltétel kiértékelése. Időköz nagyobbnak vagy azzal egyenlőnek kell lennie. Percben értendő. |
| Verzió |A használt API-verzió.  Jelenleg ez beállítása mindig 1. |

Vegye figyelembe például egy esemény lekérdezés Timespan érték 30 perc és 15 perces időközönként. Ebben az esetben a lekérdezés futna 15 percenként, és egy riasztás akkor váltódik ki, ha a feltétel igaz over feloldani továbbra is a 30 perces span.

### <a name="retrieving-schedules"></a>Ütemezés lekérése
A mentett keresések összes ütemezés a Get metódus használatával.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

A Get metódus használatával az ütemezés Azonosítójú egy meghatározott ütemezést, a mentett keresés.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

Az alábbiakban az ütemezett mintát választ.

```json
{
    "value": [{
        "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace/savedSearches/0f0f4853-17f8-4ed1-9a03-8e888b0d16ec/schedules/a17b53ef-bd70-4ca4-9ead-83b00f2024a8",
        "etag": "W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\"",
        "properties": {
            "Interval": 15,
            "QueryTimeSpan": 15
        }
    }]
}
```

### <a name="creating-a-schedule"></a>Ütemezés létrehozása
Az ütemezés egyedi Azonosítójú a Put metódust használatával hozzon létre egy új ütemezést.  Ne feledje, hogy két ütemezések nem ugyanazzal az Azonosítóval akkor is, ha különböző társított mentett kereséseket.  Egy ütemezést az OMS-konzolon, GUID van hozza létre a ütemezés azonosítóját.

> [!NOTE]
> A nevet minden mentett keresések, ütemezéseihez és napló Analytics API-val létrehozott kisbetűs kell lennie.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Az ütemezés módosítása
Használja a Put metódust egy meglévő ütemezés azonosítójú ugyanazon mentett keresés ütemezésnek.  A kérelem törzse tartalmaznia kell az ütemezés etag.

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Active':'true' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Ütemezés törlése
Ütemezés törléséhez használja a Delete metódus ütemezés-azonosítóval.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Műveletek
Ütemezés rendelkezhet több művelet. Művelet határozhatnak meg egy vagy több folyamat végrehajtásához, például egy levél küldése vagy a runbook indítása, vagy azt határozhatnak meg, amely azt határozza meg, ha a keresési eredmények bizonyos feltételeknek megfelelő küszöbértéket.  Bizonyos műveleteket határozza meg az is, hogy a folyamatok úgy hajtja végre, a küszöbérték elérése.

Minden művelet a következő táblázat a jellemzőkkel rendelkezik.  Különböző típusú riasztások különböző további tulajdonságokat, amelyek az alábbiakban található rendelkezik.

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus |A művelet típusát.  Jelenleg a lehetséges értékei a riasztás és Webhook. |
| Name (Név) |Megjelenítési nevet a riasztáshoz. |
| Verzió |A használt API-verzió.  Jelenleg ez beállítása mindig 1. |

### <a name="retrieving-actions"></a>Műveletek végrehajtása

> [!NOTE]
> 2018. április 23. verziótól munkaterület minden riasztás automatikusan kiterjesztik Azure. Egy felhasználó önkéntesen kezdeményezhet kibővítése értesítések az Azure-bA 2018. április 23 előtt. További információkért lásd: [kiterjesztése értesítések az Azure az OMS Szolgáltatáshoz](../monitoring-and-diagnostics/monitoring-alerts-extend.md). A felhasználók számára, amelyek értesítések az Azure-bA műveletek most már Azure művelet csoportokban szabályozza. A munkaterület és a riasztások bővítve lettek az Azure-ba, amikor beolvasni, vagy adja hozzá a műveletek használatával a [művelet csoport API](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

A Get metódus használatával ütemezett összes műveletet.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

A Get metódus használatával a művelet azonosítójú ütemezett művelet.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Létrehozásával vagy szerkesztésével műveletek
A Put metódust használja az ütemezés hozzon létre egy új tevékenység egyedi azonosítójú művelet.  Egy műveletet hoz létre az OMS-konzolon, ha egy GUID van-e a művelet azonosítóját.

> [!NOTE]
> A nevet minden mentett keresések, ütemezéseihez és napló Analytics API-val létrehozott kisbetűs kell lennie.

Használja a Put metódust ugyanazon mentett keresés meglévő művelet azonosítója az ütemezésnek.  A kérelem törzse tartalmaznia kell az ütemezés etag.

A kérelem formátuma új művelet létrehozásához művelettípus függ, ezek a példák az alábbi szakaszokban a.

### <a name="deleting-actions"></a>Műveletek törlése

> [!NOTE]
> 2018. április 23. verziótól munkaterület minden riasztás automatikusan kiterjesztik Azure. Egy felhasználó önkéntesen kezdeményezhet kibővítése értesítések az Azure-bA 2018. április 23 előtt. További információkért lásd: [kiterjesztése értesítések az Azure az OMS Szolgáltatáshoz](../monitoring-and-diagnostics/monitoring-alerts-extend.md). A felhasználók számára, amelyek értesítések az Azure-bA műveletek most már Azure művelet csoportokban szabályozza. A munkaterület és a riasztások bővítve lettek az Azure-ba, amikor beolvasni, vagy adja hozzá a műveletek használatával a [művelet csoport API](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

A művelet azonosítójú a Delete metódus segítségével törölheti a műveletet.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Értesítési műveletek
Ütemezés rendelkeznie kell egy műveletet.  Értesítési műveletek rendelkezik legalább egy, a következő táblázat a részeket.  A további részletek az alábbi leírása.

| Section | Leírás | Használat |
|:--- |:--- |:--- |
| Küszöbérték |A művelet futtatásakor feltételeit.| Szükséges minden egyes riasztás előtt vagy után bővítve lettek az Azure-bA. |
| Súlyosság |A riasztás kiváltásakor osztályozva címkéje.| Szükséges minden egyes riasztás előtt vagy után bővítve lettek az Azure-bA. |
| Műveletcsoportok |Azure ActionGroup, ahol a szükséges műveletek vannak megadva, az azonosítók például - e-mailek, SMSs, hanghívások, Webhookokkal, Automation-forgatókönyveket, ITSM összekötők, stb.| Szükséges, ha riasztások bővítve lettek az Azure-bA|
| Műveletek testreszabása|A select műveletek ActionGroup a standard kimenet módosítása| Minden riasztás esetén nem kötelező használható után riasztások bővítve lettek az Azure-bA. |
| EmailNotification |E-mail küldés több címzettnek. | Nem kötelező megadni, ha a riasztások bővítve lettek az Azure-bA|
| Szervizelés |Elindít egy forgatókönyvet az Azure Automation sikertelen bejelentkezési kísérletet azonosított probléma elhárításához. |Nem kötelező megadni, ha a riasztások bővítve lettek az Azure-bA|
| Webhook Actions | Riasztások, adatok leküldése JSON-ként kívánt szolgáltatás |Nem kötelező megadni, ha a riasztások bővítve lettek az Azure-bA|

> [!NOTE]
> 2018. április 23. verziótól munkaterület minden riasztás automatikusan kiterjesztik Azure. Egy felhasználó önkéntesen kezdeményezhet kibővítése értesítések az Azure-bA 2018. április 23 előtt. További információkért lásd: [kiterjesztése értesítések az Azure az OMS Szolgáltatáshoz](../monitoring-and-diagnostics/monitoring-alerts-extend.md).

#### <a name="thresholds"></a>Küszöbértékek
Egy műveletet rendelkeznie kell egy küszöbértéket.  A mentett keresés eredményei felel meg a küszöbérték, hogy a keresés társított művelet, amikor az adott művelet egyéb folyamatok futnak.  Egy műveletet is tartalmazhat, csak a küszöbértéket, hogy más típusú, amelyek nem tartalmazzák a küszöbértékek műveletekhez használható.

Küszöbértékek az alábbi táblázatban a jellemzőkkel rendelkezik.

| Tulajdonság | Leírás |
|:--- |:--- |
| Operátor |A küszöbérték összehasonlító operátort. <br> gt = nagyobb mint <br> lt = kisebb, mint |
| Érték |Értéke a küszöbérték. |

Vegye figyelembe például 15 perc, 30 perc Timespan és egy nagyobb, mint 10 küszöbértéket időközzel eseménylekérdezési. Ebben az esetben a lekérdezés futna 15 percenként, és a riasztás akkor váltódik ki, ha 10 keresztül a 30 perces span létrehozott események által visszaadott.

Az alábbiakban látható egy minta válasz csak a küszöbérték a művelet.  

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

Az egyedi művelet Azonosítójú a Put metódust segítségével hozzon létre egy új küszöbérték műveletet ütemezés szerint.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Használja a Put metódust egy meglévő azonosítójú művelet egy ütemezés küszöbérték műveletet.  A kérelem törzse tartalmaznia kell a művelet az etag.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Súlyosság
Naplóelemzési lehetővé teszi a riasztások kategóriákba ahhoz, hogy egyszerűbb kezelés és osztályozás besorolását. A riasztás súlyosságát definiálva van: információs, figyelmeztetési és a kritikus. Ezek a normalizált súlyossági méretezésének Azure riasztásokat van leképezve:

|Naplózási Analytics súlyossági szint  |Az Azure riasztások súlyossági szint  |
|---------|---------|
|Kritikus |SEV 0|
|Figyelmeztetés |Sev 1|
|Tájékoztató | Sev 2|

Az alábbiakban látható egy minta válasz csak egy küszöbérték és a súlyosság művelet. 

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

Az egyedi művelet Azonosítójú a Put metódust használatával hozzon létre egy új ütemezést műveletet súlyossága.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Használja a Put metódust egy meglévő azonosítójú művelet egy ütemezés súlyossági műveletet.  A kérelem törzse tartalmaznia kell a művelet az etag.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="action-groups"></a>Műveletcsoportok
Összes riasztás Azure, a kezelési műveletek alapértelmezett mechanizmusaként művelet csoportot használjon. Művelet csoporttal egyszer adja meg a végrehajtandó műveleteket, és társíthatja a művelet több riasztás - csoport Azure között. Nem szükséges, ismételten deklarálható többször ugyanazokat a műveleteket. A művelet csoportok több műveletek – például az e-mailek, SMS, hang hívja, ITSM kapcsolat, Automation-Runbook, Webhook URI támogatja. 

A felhasználó ki van bővítve a riasztások az Azure - ütemezés szerint most már rendelkezik küszöbértéket, riasztást létrehozni, valamint átadott részletei művelet. E-mail részleteit, Webhook URL-címek, Runbook-automatizálási részleteit és más műveleteket kell lenniük oldal definiált egy művelet csoport előtt először létre riasztást; létrehozhat egy [művelet csoport Azure figyelő](../monitoring-and-diagnostics/monitoring-action-groups.md) a portál vagy [művelet csoport API](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Vegyen fel művelet csoport-hozzárendelést egy riasztást, adja meg az egyedi Azure Resource Manager csoport azonosítója, a művelet a riasztás definíciójának. Egy minta ábra lejjebb tekinthetők meg:

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
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

A Put metódust használja az egyedi művelet Azonosítójú ütemezett már meglévő művelet csoporthoz hozzárendelni.  A következő egy minta ábra használati.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Egy művelet csoporthoz tartozó ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzse tartalmaznia kell a művelet az etag.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Műveletek testreszabása
Alapértelmezett műveletek hajtsa végre a szabványos és az értesítések formátuma. De felhasználói testre szabhatja bizonyos műveleteket, még akkor is, ha azok művelet csoportok szabályozzák. Testreszabási jelenleg E-mail tárgysora és Webhook hasznos.

##### <a name="customize-e-mail-subject-for-action-group"></a>E-Mail tárgya művelet csoport testreszabása
Alapértelmezés szerint az e-mail tárgyát riasztások van: riasztási értesítés <AlertName> a <WorkspaceName>. Azonban ez testre szabható, így szavakat vagy címkék - lehetővé teszi könnyedén alkalmazni az Állapotszűrő szabályok a Beérkezett üzenetek is. A Testreszabás e-mail fejléc részletek kell küldeniük ActionGroup részleteit, ahogy az alábbi minta együtt.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Az egyedi művelet Azonosítójú a Put metódust segítségével testreszabási ütemezett művelet már meglévő csoport társítani.  A következő egy minta ábra használati.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Egy művelet csoporthoz tartozó ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzse tartalmaznia kell a művelet az etag.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Testre szabhatja a Webhook hasznos művelet csoport
Alapértelmezés szerint a webhook naplóelemzési művelet csoport keresztül küldött struktúrája a rögzített. De egy szabhatja testre a JSON-adattartalmat adott változókkal követelményeinek megfelelően a webhook végpont támogatott. További információkért lásd: [Webhook műveleti napló riasztási szabályok](../monitoring-and-diagnostics/monitor-alerts-unified-log-webhook.md). 

A Testreszabás webhook kell együtt ActionGroup részletek küldéséhez és fog alkalmazandó összes Webhook URI meg a művelet csoport; belül ahogy az alábbi minta.

     "etag": "W/\"datetime'2017-12-13T10%3A52%3A21.1697364Z'\"",
      "properties": {
        "Type": "Alert",
        "Name": "test-alert",
        "Description": "I need to put a descriptio here",
        "Threshold": {
          "Operator": "gt",
          "Value": 12
        },
        "AzNsNotification": {
          "GroupIds": [
            "/subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup"
          ]
          "CustomWebhookPayload": "{\"field1\":\"value1\",\"field2\":\"value2\"}",
          "CustomEmailSubject": "Azure Alert fired"
        },
        "Severity": "critical",
        "Version": 1
      },

Az egyedi művelet Azonosítójú a Put metódust segítségével testreszabási ütemezett művelet már meglévő csoport társítani.  A következő egy minta ábra használati.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Egy művelet csoporthoz tartozó ütemezés módosításához használja a Put metódust egy meglévő azonosítójú művelet.  A kérelem törzse tartalmaznia kell a művelet az etag.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="email-notification"></a>Értesítés e-mailben
Értesítő e-mailt küldjön egy vagy több címzett.  A tulajdonságok az alábbi táblázatban tartalmaznak.

> [!NOTE]
> 2018. április 23. verziótól munkaterület minden riasztás automatikusan kiterjesztik Azure. Egy felhasználó önkéntesen kezdeményezhet kibővítése értesítések az Azure-bA 2018. április 23 előtt. További információkért lásd: [kiterjesztése értesítések az Azure az OMS Szolgáltatáshoz](../monitoring-and-diagnostics/monitoring-alerts-extend.md). A felhasználók számára, amelyek értesítések az Azure-bA például az e-mailben értesítést most vezérelt Azure művelet csoportokban. A munkaterület és a riasztások bővítve lettek az Azure-ba, amikor beolvasni, vagy adja hozzá a műveletek használatával a [művelet csoport API](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).
   

| Tulajdonság | Leírás |
|:--- |:--- |
| Címzettek |E-mail címek listája. |
| Tárgy |Az e-mail tárgyát. |
| Melléklet |Mellékletek jelenleg nem támogatottak, ezért ez mindig lesz értéke "None." |

Az alábbiakban látható egy minta választ, a küszöbérték az e-mail értesítési művelet.  

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

Az egyedi művelet Azonosítójú a Put metódust segítségével hozzon létre egy új e-mail műveletet ütemezés szerint.  Az alábbi példa e-mailben értesítést a küszöbérték hoz létre, a levelezési küldi, ha a mentett keresési eredmények lépheti túl a küszöbértéket.

    $emailJson = "{'properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

Használja a Put metódust egy meglévő azonosítójú művelet ütemezés e-mail művelet.  A kérelem törzse tartalmaznia kell a művelet az etag.

    $emailJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'MyEmailAction', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'EmailNotification': {'Recipients': ['recipient1@contoso.com', 'recipient2@contoso.com'], 'Subject':'This is the subject', 'Attachment':'None'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myemailaction?api-version=2015-03-20 $emailJson

#### <a name="remediation-actions"></a>Szervizelési műveletek
Szervizelt próbál kijavítja a hibát, a riasztás által azonosított Azure Automation forgatókönyv indítása.  A runbook egy szervizelési művelet szerepel a webhook létrehozása kell, és adja meg az URI a WebhookUri tulajdonság.  Ez a művelet az OMS-konzollal létrehozásakor egy új webhook automatikusan létrejön a runbookhoz.

> [!NOTE]
> 2018. április 23. verziótól munkaterület minden riasztás automatikusan kiterjesztik Azure. Egy felhasználó önkéntesen kezdeményezhet kibővítése értesítések az Azure-bA 2018. április 23 előtt. További információkért lásd: [kiterjesztése értesítések az Azure az OMS Szolgáltatáshoz](../monitoring-and-diagnostics/monitoring-alerts-extend.md). A felhasználók számára, amelyek értesítések az Azure-bA például a runbook használatával szervizelési műveletek most már Azure művelet csoportokban szabályozza. A munkaterület és a riasztások bővítve lettek az Azure-ba, amikor beolvasni, vagy adja hozzá a műveletek használatával a [művelet csoport API](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).

Szervizelt tulajdonságot tartalmazhatja az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| RunbookName |A runbook neve. Ennek egyeznie kell a közzétett runbookok konfigurálva az Automation-megoldás az OMS-munkaterület az automation-fiókban. |
| WebhookUri |A webhook URI Azonosítóját. |
| Lejárat |A lejárati dátum és idő, a webhook.  Ha a webhook egy lejárati nem rendelkezik, majd ez lehet bármely érvényes jövőbeni dátum. |

Az alábbiakban látható egy mintaválasz szervizelési művelethez a küszöbértéket.

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

Az egyedi művelet Azonosítójú a Put metódust segítségével hozzon létre egy új szervizelési művelet ütemezések.  A következő példa egy szervizelés küszöbértéket hoz létre, a runbook indítását, amikor a mentett keresési eredmények lépheti túl a küszöbértéket.

    $remediateJson = "{'properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

Használja a Put metódust egy meglévő azonosítójú művelet egy ütemezés javítási műveletet.  A kérelem törzse tartalmaznia kell a művelet az etag.

    $remediateJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Type':'Alert', 'Name': 'My Remediation Action', 'Version':'1', 'Threshold': { 'Operator': 'gt', 'Value': 10 }, 'Remediation': {'RunbookName': 'My-Runbook', 'WebhookUri':'https://s1events.azure-automation.net/webhooks?token=4jCibOjO3w4W2Cfg%2b2NkjLYdafnusaG6i8tnP8h%2fNNg%3d', 'Expiry':'2018-02-25T18:27:20Z'} }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myremediationaction?api-version=2015-03-20 $remediateJson

#### <a name="example"></a>Példa
Az alábbiakban látható egy teljes példa egy új e-mail-riasztások létrehozásához.  Ezzel létrehoz egy új ütemezést tartalmazó egy küszöbértéket és e-mailek művelet együtt.

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

#### <a name="webhook-actions"></a>Webhookműveletek
Webhookműveletek egy folyamat megkezdéséhez hívja az egy URL-cím és a nem kötelezően kell küldeni a hasznos adatok között.  Ezek hasonlóak szervizelési műveletek kivételével ezek webhookokkal, amely az Azure Automation-runbook eltérő folyamatok indít el a célja.  A további lehetőséget, hogy a hasznos adatok között a távoli folyamat küldendő is biztosítanak.

> [!NOTE]
> 2018. április 23. verziótól munkaterület minden riasztás automatikusan kiterjesztik Azure. Egy felhasználó önkéntesen kezdeményezhet kibővítése értesítések az Azure-bA 2018. április 23 előtt. További információkért lásd: [kiterjesztése értesítések az Azure az OMS Szolgáltatáshoz](../monitoring-and-diagnostics/monitoring-alerts-extend.md). A felhasználók számára, amelyek értesítések az Azure-bA például az Webhook most Azure művelet csoportokban szabályozza. A munkaterület és a riasztások bővítve lettek az Azure-ba, amikor beolvasni, vagy adja hozzá a műveletek használatával a [művelet csoport API](https://docs.microsoft.com/en-us/rest/api/monitor/actiongroups).


Webhookműveletek nem rendelkezik a küszöbérték, de ehelyett hozzá kell adni egy ütemezést, amely egy riasztási műveletek a küszöbértéket.  

Az alábbiakban látható egy mintaválasz webhook műveletet és egy társított műveletet a küszöbértéket.

    {
        "__metadata": {},
        "value": [
            {
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/72884702-acf9-4653-bb67-f42436b342b4",
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
                "id": "subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/bwren/savedSearches/2d1b30fb-7f48-4de5-9614-79ee244b52de/schedules/b80f5621-7217-4007-b32d-165d14377093/Actions/90a27cf8-71b7-4df2-b04f-54ed01f1e4b6",
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

##### <a name="create-or-edit-a-webhook-action"></a>Hozzon létre vagy egy webhook művelet szerkesztése
Az egyedi művelet Azonosítójú a Put metódust segítségével hozzon létre egy új webhook műveletet ütemezés szerint.  Az alábbi példa hoz létre egy Webhook műveletet és egy műveletet a küszöbértéket, hogy a webhook aktiválódik, amikor lépheti túl a küszöbértéket, a mentett keresés eredménye.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

A webhook művelet ütemezés módosításához használja egy meglévő azonosítójú művelet a Put metódust.  A kérelem törzse tartalmaznia kell a művelet az etag.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction


## <a name="next-steps"></a>További lépések
* Használja a [REST API napló keresés](log-analytics-log-search-api.md) a Naplóelemzési.
* További tudnivalók [riasztások jelentkezzen be azure riasztások](../monitoring-and-diagnostics/monitor-alerts-unified-log.md)

