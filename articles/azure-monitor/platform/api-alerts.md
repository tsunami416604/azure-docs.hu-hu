---
title: A Log Analytics riasztási REST API használata
description: A Log Analytics riasztási REST API lehetővé teszi, hogy riasztásokat hozzon létre és kezeljen a Log Analytics, amely a Log Analytics része.  Ez a cikk az API részleteit és számos példát tartalmaz a különböző műveletek végrehajtásához.
ms.subservice: logs
ms.topic: conceptual
ms.date: 07/29/2018
ms.openlocfilehash: a85dad2ba638505233e5df769e55fa5bd7b8dafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665000"
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Riasztási szabályok létrehozása és kezelése a Log Analytics szolgáltatásban rest API-val 

A Log Analytics riasztási REST API lehetővé teszi a riasztások létrehozását és kezelését a Log Analytics.The Log Analytics Alert REST API allows you to create and manage alerts in Log Analytics.  Ez a cikk az API részleteit és számos példát tartalmaz a különböző műveletek végrehajtásához.

> [!IMPORTANT]
> Amint [azt korábban bejelentette,](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/)a [PowerShell cmdlet](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-powershell) *2019.* **only** [REST API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) [Azure Resource Mananger Template](../../azure-monitor/platform/alerts-log.md#managing-log-alerts-using-azure-resource-template) Az ügyfelek könnyedén válthatnak a régebbi munkaterületek [riasztási szabálykezelésének előnyben részesített eszközeiközött,](../../azure-monitor/platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) hogy az Azure Monitor scheduledQueryRules alapértelmezettként használjanak, és számos [új előnyt szerezzenek,](../../azure-monitor/platform/alerts-log-api-switch.md#benefits-of-switching-to-new-azure-api) például a natív PowerShell-parancsmagok használatát, a szabályokban megnövelt visszatekintési időszakot, a szabályok létrehozását külön erőforráscsoportban vagy előfizetésben és még sok másban.

A Log Analytics Search REST API RESTful, és az Azure Resource Manager REST API-n keresztül érhető el. Ebben a dokumentumban talál példákat, ahol az API-t powershell-parancssorból éri el az [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely egyszerűsíti az Azure Resource Manager API meghívását. Az ARMClient és a PowerShell használata a Log Analytics keresési API elérésének számos lehetősége egyike. Ezekkel az eszközökkel használhatja a RESTful Azure Resource Manager API-t a Log Analytics-munkaterületek hívásaihoz, és keresési parancsokat hajthat végre bennük. Az API JSON formátumban adja ki a keresési eredményeket, lehetővé téve a keresési eredmények különböző módon történő használatát programozott módon.

## <a name="prerequisites"></a>Előfeltételek
Jelenleg riasztások csak akkor hozhatók létre a Mentett keresés a Log Analytics.  További információt a [Naplókeresés REST API-jában](../../azure-monitor/log-query/log-query-overview.md) talál.

## <a name="schedules"></a>Ütemezések
A mentett keresésnek egy vagy több ütemezése lehet. Az ütemezés határozza meg, hogy milyen gyakran fusson a keresés, és milyen időintervallumban azonosítsa a feltételeket.
Az ütemezések tulajdonságai az alábbi táblázatban vannak.

| Tulajdonság | Leírás |
|:--- |:--- |
| Intervallum |A keresés futtatásának gyakran. Percekben mérve. |
| QueryTimeSpan |Az az időintervallum, amely alatt a feltételek kiértékelése történik. Egyenlőnek vagy nagyobbnak kell lennie az Intervallumértéknél. Percekben mérve. |
| Verzió |A használt API-verzió.  Jelenleg ezt mindig 1-re kell állítani. |

Vegyünk például egy 15 perces időközzel és 30 perces időintervallummal rendelkező eseménylekérdezést. Ebben az esetben a lekérdezés 15 percenként futna, és egy riasztás aktiválódna, ha a feltételek továbbra is 30 perces időtartamon keresztül igaz ra oldódnak fel.

### <a name="retrieving-schedules"></a>Ütemezések beolvasása
A Get metódus segítségével a mentett keresés összes ütemezését lekérheti.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules?api-version=2015-03-20

A Get metódus t ütemezési azonosítóval egy adott ütemezés beolvasásához a mentett kereséshez.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20

A következő egy minta választ egy ütemtervet.

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
Új ütemezés létrehozásához használja az Egyedi ütemezési azonosítóval rendelkező Put metódust.  Két ütemezés nem rendelkezhet ugyanazzal az azonosítóval, még akkor sem, ha különböző mentett keresésekhez vannak társítva.  Amikor ütemezést hoz létre a Log Analytics konzolon, egy GUID jön létre az ütemezésazonosítóhoz.

> [!NOTE]
> A Log Analytics API-val létrehozott összes mentett keresés, ütemezés és művelet nevének kisbetűsnek kell lennie.

    $scheduleJson = "{'properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'true' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson

### <a name="editing-a-schedule"></a>Ütemezés szerkesztése
Az ütemezés módosításához használja az Elhelyezés metódust egy meglévő ütemezési azonosítóval ugyanahhoz a mentett kereséshez; példában az ütemezés alatt le van tiltva. A kérelem törzsének tartalmaznia kell az *ütemterv etag-jét.*

      $scheduleJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A49.8074679Z'\""','properties': { 'Interval': 15, 'QueryTimeSpan':15, 'Enabled':'false' } }"
      armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/mynewschedule?api-version=2015-03-20 $scheduleJson


### <a name="deleting-schedules"></a>Ütemezéstörlése
Ütemezés törléséhez használja a Törlés metódust ütemezéssel.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}?api-version=2015-03-20


## <a name="actions"></a>Műveletek
Egy ütemezés több műveletet is végrehajthat. Egy művelet meghatározhat egy vagy több végrehajtandó folyamatot, például egy e-mail küldését vagy egy runbook indítását, vagy meghatározhat egy küszöbértéket, amely meghatározza, hogy a keresés eredménye mikor felel meg bizonyos feltételeknek.  Egyes műveletek határozzák meg mindkettőt, hogy a folyamatok végrehajtása a küszöbérték elérésekor.

Az alábbi táblázatban minden művelet tulajdonságai vannak.  A különböző típusú riasztások különböző további tulajdonságokkal rendelkeznek, amelyeket az alábbiakban ismertetünk.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |A művelet típusa.  Jelenleg a lehetséges értékek riasztás és webhook. |
| `Name` |A riasztás megjelenítendő neve. |
| `Version` |A használt API-verzió.  Jelenleg ezt mindig 1-re kell állítani. |

### <a name="retrieving-actions"></a>Műveletek beolvasása

A Get metódus segítségével az ütemezéshez szükséges összes műveletet lekérheti.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search  ID}/schedules/{Schedule ID}/actions?api-version=2015-03-20

A get metódus t a műveletazonosítóval egy adott művelet beolvasásához egy ütemezéshez.

    armclient get /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/actions/{Action ID}?api-version=2015-03-20

### <a name="creating-or-editing-actions"></a>Műveletek létrehozása és szerkesztése
Új művelet létrehozásához használja az Elhelyezés metódust egy olyan műveletazonosítóval, amely az ütemezésegyedi.  Amikor létrehoz egy műveletet a Log Analytics konzolon, a műveletazonosító guid-ja a műveletazonosítóhoz.

> [!NOTE]
> A Log Analytics API-val létrehozott összes mentett keresés, ütemezés és művelet nevének kisbetűsnek kell lennie.

Az ütemezés módosításához használja az Elhelyezés metódust egy meglévő műveletazonosítóval ugyanahhoz a mentett kereséshez.  A kérelem törzsének tartalmaznia kell az ütemterv etag-jét.

Az új művelet létrehozásának kérésformátuma művelettípusonként eltérő lehet, így ezek a példák az alábbi szakaszokban találhatók.

### <a name="deleting-actions"></a>Műveletek törlése

Művelet törléséhez használja a Törlés metódust a műveletazonosítóval.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Riasztási műveletek
Az ütemezésnek egy és csak egy riasztási művelettel kell rendelkeznie.  A riasztási műveletek az alábbi táblázat egy vagy több szakaszával rendelkeznek.  Mindegyik részletesen ismerteti az alábbiakban.

| Section | Leírás | Használat |
|:--- |:--- |:--- |
| Küszöbérték |A művelet futtatásának feltételei.| Minden riasztáshoz szükséges, mielőtt vagy azt az Azure-ra kiterjesztenék. |
| Severity |A riasztás aktiváláskor történő besorolására használt címke.| Minden riasztáshoz szükséges, mielőtt vagy azt az Azure-ra kiterjesztenék. |
| Elnyomják |Lehetőség az értesítések riasztási leállítására. | Minden riasztáshoz nem kötelező, mielőtt vagy azt követően kibővítik az Azure-ra. |
| Műveletcsoportok |Az Azure ActionGroup azonosítói, ahol a szükséges műveletek vannak megadva, például – e-mailek, KIS-és közép-európai szolgáltatások, hanghívások, webhookok, automation runbookok, ITSM-összekötők stb.| A riasztások azure-ra való kiterjesztését követően szükséges|
| Műveletek testreszabása|Az ActionGroup egyes műveletek szabványos kimenetének módosítása| Minden riasztáshoz választható, miután a riasztások ki vannak terjesztve az Azure-ra. |

### <a name="thresholds"></a>Küszöbértékek
A riasztási műveletnek egy és csak egy küszöbértéket kell rendelkeznie.  Ha egy mentett keresés eredménye megegyezik a kereséshez társított művelet küszöbértékével, akkor a művelet bármely más folyamata fut.  Egy művelet is tartalmazhat csak egy küszöbértéket, így más típusú műveletek, amelyek nem tartalmaznak küszöbértékeket.

A küszöbértékek tulajdonságai az alábbi táblázatban vannak.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Operator` |Operátor a küszöbérték-összehasonlításhoz. <br> gt = Nagyobb, mint <br> lt = kisebb, mint |
| `Value` |A küszöbérték értéke. |

Vegyünk például egy 15 perces, 30 perces időintervallumú és 10-nél nagyobb küszöbértékű eseménylekérdezést. Ebben az esetben a lekérdezés 15 percenként futna, és egy riasztás aktiválódna, ha 10 30 perces időtartamon keresztül létrehozott eseményt ad vissza.

A következőkben egy mintaválasz egy művelet csak egy küszöbértéket.  

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

Az Egyedi műveletazonosítóval rendelkező Put metódus sal új küszöbérték-műveletet hozhat létre egy ütemezéshez.  

    $thresholdJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

Az Ütemezés küszöbértékének módosításához használja az Elhelyezés metódust egy meglévő műveletazonosítóval.  A kérelem törzsének tartalmaznia kell a cselekvés etagját.

    $thresholdJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdJson

#### <a name="severity"></a>Severity
A Log Analytics lehetővé teszi a riasztások kategóriákba sorolását, hogy egyszerűbb kezelés és osztályozás legyen lehetővé. A riasztás súlyossága meghatározott: tájékoztató, figyelmeztetés és kritikus. Ezek az Azure Alerts normalizált súlyossági skálájára vannak leképezve:

|Log Analytics súlyossági szintje  |Azure-riasztások súlyossági szintje  |
|---------|---------|
|`critical` |Sev 0|
|`warning` |Sev 1|
|`informational` | Sev 2|

A következőkben egy mintaválaszt egy művelet csak egy küszöbértéket és súlyosságát. 

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

Az Egyedi műveletazonosítóval rendelkező Put metódus segítségével hozzon létre egy új műveletet súlyosságú ütemezéshez.  

    $thresholdWithSevJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

Az Ütemezés súlyossági műveletének módosításához használja az Elhelyezés metódust egy meglévő műveletazonosítóval.  A kérelem törzsének tartalmaznia kell a cselekvés etagját.

    $thresholdWithSevJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdWithSevJson

#### <a name="suppress"></a>Elnyomják
A Log Analytics alapú lekérdezési riasztások minden alkalommal, amikor a küszöbérték teljesülnek vagy túllépik. A lekérdezésben szereplő logika alapján ez azt eredményezheti, hogy a riasztás időközönként aktiválódik, és így az értesítések is folyamatosan elküldésre kerülnek. Az ilyen forgatókönyv megelőzése érdekében a felhasználó beállíthatja a Letiltás beállítást, amely arra utasítja a Log Analytics-et, hogy várjon egy meghatározott ideig, mielőtt a riasztási szabály második alkalommal értesítést küld. Tehát, ha elnyomja van beállítva 30 percig; majd a riasztás az első alkalommal kigyullad, és értesítéseket küld konfigurálva. De ezután várjon 30 percet, mielőtt a riasztási szabály értesítésének újra használatba kerül. A köztes időszakban a riasztási szabály továbbra is futni fog – csak a Log Analytics letiltja az értesítést a megadott időre, függetlenül attól, hogy a riasztási szabály hány alkalommal aktiválódik ebben az időszakban.

A Log Analytics riasztási szabály tulajdonságának letiltása a *Szabályozás* érték és az *DurationInMinutes* érték használatával történik.

A következőkben egy mintaválasz egy olyan művelethez, amelynek csak egy küszöbértéke, súlyossága és letiltása tulajdonsággal rendelkezik.

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

Az Egyedi műveletazonosítóval rendelkező Put metódus segítségével hozzon létre egy új műveletet súlyosságú ütemezéshez.  

    $AlertSuppressJson = "{'properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

Az Ütemezés súlyossági műveletének módosításához használja az Elhelyezés metódust egy meglévő műveletazonosítóval.  A kérelem törzsének tartalmaznia kell a cselekvés etagját.

    $AlertSuppressJson = "{'etag': 'W/\"datetime'2016-02-25T20%3A54%3A20.1302566Z'\"','properties': { 'Name': 'My Threshold', 'Version':'1','Severity': 'critical', 'Type':'Alert', 'Throttling': { 'DurationInMinutes': 30 },'Threshold': { 'Operator': 'gt', 'Value': 10 } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myalert?api-version=2015-03-20 $AlertSuppressJson

#### <a name="action-groups"></a>Műveletcsoportok
Az Azure-ban minden riasztást, használja a műveletcsoport, mint a műveletek kezelésére szolgáló alapértelmezett mechanizmus. A műveletcsoporttal egyszer megadhatja a műveleteket, majd a műveletcsoportot több riasztáshoz társíthatja – az Azure-ban. Anélkül, hogy szükség lenne, hogy újra és újra kijelentsd ugyanazokat a tetteket. A műveletcsoportok több műveletet is támogatnak – többek között e-maileket, SMS-t, hanghívást, ITSM-kapcsolatot, Automation Runbookot, Webhook URI-t stb. 

Azon felhasználók számára, akik kiterjesztették a riasztásokat az Azure-ba – az ütemezésnek mostmár át kell adnia a műveletcsoport adatait a küszöbértékkel együtt, hogy riasztást hozhat létre. E-mail részleteket, Webhook URL-címeket, Runbook Automation részleteket, és egyéb műveletek, meg kell határozni az oldalon egy műveletcsoport először a riasztás létrehozása előtt; létrehozhat [műveletcsoportot az Azure Monitorból](../../azure-monitor/platform/action-groups.md) a Portálon, vagy használhatja a [Műveletcsoport API-t.](https://docs.microsoft.com/rest/api/monitor/actiongroups)

Ha műveletcsoport társítását szeretné hozzáadni egy riasztáshoz, adja meg a műveletcsoport egyedi Azure Resource Manager-azonosítóját a riasztásdefinícióban. A minta illusztrációja az alábbiakban található:

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

Az Elhelyezés módszer egyedi műveletazonosítóval társíthatja a már meglévő műveletcsoportot egy ütemezéshez.  Az alábbi minta a használatot szemlélteti.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Az Ütemezéshez társított műveletcsoport módosításához használja az Elhelyezés metódust egy meglévő műveletazonosítóval.  A kérelem törzsének tartalmaznia kell a cselekvés etagját.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': { 'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'] } } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

#### <a name="customize-actions"></a>Műveletek testreszabása
Alapértelmezés szerint a műveleteket a szabványos sablont és az értesítések formátumát kell követnie. A felhasználó azonban testre szabhat bizonyos műveleteket, még akkor is, ha azokat műveletcsoportok vezérlik. Jelenleg az e-mail tárgya és a Webhook-tartalom testreszabása lehetséges.

##### <a name="customize-e-mail-subject-for-action-group"></a>E-mail tárgyának testreszabása műveletcsoporthoz
Alapértelmezés szerint a riasztások e-mail ben `<AlertName>` `<WorkspaceName>`a következő: Értesítési értesítés a rendszerhez. De ez testreszabható, így konkrét szavakat vagy címkéket - lehetővé teszi, hogy könnyen foglalkoztatszűrő szabályokat a Beérkezett üzenetek mappában. A testre szabott e-mail fejléc részleteit kell küldeni együtt ActionGroup részleteket, mint az alábbi minta.

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

Az Elhelyezés módszer egyedi műveletazonosítóval társíthatja a már meglévő műveletcsoportot az ütemezés testreszabásához.  Az alábbi minta a használatot szemlélteti.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Az Ütemezéshez társított műveletcsoport módosításához használja az Elhelyezés metódust egy meglévő műveletazonosítóval.  A kérelem törzsének tartalmaznia kell a cselekvés etagját.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

##### <a name="customize-webhook-payload-for-action-group"></a>Webhook-tartalom testreszabása a műveletcsoporthoz
Alapértelmezés szerint a műveletcsoporton keresztül a naplóelemzési webhook rögzített struktúrával rendelkezik. De lehet testreszabni a JSON hasznos teher speciális támogatott változók használatával, hogy megfeleljen a webhook-végpont követelményeinek. További információt a [Webhook-művelet a naplóriasztási szabályokért](../../azure-monitor/platform/alerts-log-webhook.md)című témakörben talál. 

A webhook-részletek testreszabása az ActionGroup részleteivel együtt kell elküldenie, és a műveletcsoporton belül megadott összes Webhook URI-ra lesz alkalmazva; mint az alábbi mintában.

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

Az Elhelyezés módszer egyedi műveletazonosítóval társíthatja a már meglévő műveletcsoportot az ütemezés testreszabásához.  Az alábbi minta a használatot szemlélteti.

    $AzNsJson = "{'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup'], 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}'} } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson

Az Ütemezéshez társított műveletcsoport módosításához használja az Elhelyezés metódust egy meglévő műveletazonosítóval.  A kérelem törzsének tartalmaznia kell a cselekvés etagját.

    $AzNsJson = "{'etag': 'datetime'2017-12-13T10%3A52%3A21.1697364Z'\"', 'properties': { 'Name': 'test-alert', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 12 },'Severity': 'critical', 'AzNsNotification': {'GroupIds': ['subscriptions/1234a45-123d-4321-12aa-123b12a5678/resourcegroups/my-resource-group/providers/microsoft.insights/actiongroups/test-actiongroup']}, 'CustomEmailSubject': 'Azure Alert fired','CustomWebhookPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}' } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/myAzNsaction?api-version=2015-03-20 $AzNsJson


## <a name="next-steps"></a>További lépések

* A [REST API segítségével naplókereséseket hajthat végre](../../azure-monitor/log-query/log-query-overview.md) a Log Analytics szolgáltatásban.
* További információ a [naplóriasztásokról az Azure-figyelőben](../../azure-monitor/platform/alerts-unified-log.md)
* [Naplóriasztási szabályok létrehozása, szerkesztése és kezelése az Azure-figyelőben](../../azure-monitor/platform/alerts-log.md)

