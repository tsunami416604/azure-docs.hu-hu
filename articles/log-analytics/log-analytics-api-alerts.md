---
title: "OMS napló Analytics riasztási REST API használatával"
description: "A napló Analytics riasztási REST API-t kezelheti a riasztásokat az Operations Management Suite (OMS) része Naplóelemzési teszi lehetővé.  Ez a cikk ismerteti az API-t, és néhány példa a másik műveletet hajt végre."
services: log-analytics
documentationcenter: 
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
ms.openlocfilehash: 5ce72ffef4394bf3bbe39fa420c4fcaa965ae35c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="create-and-manage-alert-rules-in-log-analytics-with-rest-api"></a>Hozzon létre, és a REST API-val Naplóelemzési riasztási szabályok kezelése
A napló Analytics riasztási REST API lehetővé teszi, hogy hozhat létre és kezelheti a riasztásokat az Operations Management Suite (OMS).  Ez a cikk ismerteti az API-t, és néhány példa a másik műveletet hajt végre.

A napló Analytics Search REST API RESTful, és az Azure Resource Manager REST API-n keresztül érhető el. Ebben a dokumentumban található példák az elérését az API-t a PowerShell parancssori használatával [ARMClient](https://github.com/projectkudu/ARMClient), egy nyílt forráskódú parancssori eszköz, amely leegyszerűsíti az Azure Resource Manager API meghívása. A ARMClient és a PowerShell használata a napló Analytics Search API eléréséhez számos lehetőség. Ezekkel az eszközökkel Azure Resource Manager RESTful API-hívások indítása az OMS-munkaterület, és rajtuk keresési parancsok végrehajtása használhatja. Az API-t kimeneti fog keresési eredmények Önnek JSON formátumban, hogy lehetővé teszi a programozott módon használja a keresési eredmények között számos különböző módja.

## <a name="prerequisites"></a>Előfeltételek
Jelenleg riasztásokat csak hozhatja létre a Naplóelemzési mentett keresés.  Olvassa el a [napló Search REST API](log-analytics-log-search-api.md) további információt.

## <a name="schedules"></a>Ütemezések
A mentett kereséseket rendelkezhet egy vagy több ütemezés. Az ütemezés határozza meg, hogy milyen gyakran a keresés futtatása és a időtartam alatt, amelyben a feltétel azonosítja.
Ütemezések a jellemzőkkel rendelkezik, az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| időköz |Milyen gyakran fut a keresést. Percben értendő. |
| queryTimeSpan |Az időtartam, amely a feltétel kiértékelése. Időköz nagyobbnak vagy azzal egyenlőnek kell lennie. Percben értendő. |
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
| Név |Megjelenítési nevet a riasztáshoz. |
| Verzió |A használt API-verzió.  Jelenleg ez beállítása mindig 1. |

### <a name="retrieving-actions"></a>Műveletek végrehajtása
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
A művelet azonosítójú a Delete metódus segítségével törölheti a műveletet.

    armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Subscription ID}/schedules/{Schedule ID}/Actions/{Action ID}?api-version=2015-03-20

### <a name="alert-actions"></a>Értesítési műveletek
Ütemezés rendelkeznie kell egy műveletet.  Értesítési műveletek rendelkezik legalább egy, a következő táblázat a részeket.  A további részletek az alábbi leírása.

| A szakasz | Leírás |
|:--- |:--- |
| Küszöbérték |A művelet futtatásakor feltételeit. |
| EmailNotification |E-mail küldés több címzettnek. |
| Szervizkiszolgáló |Elindít egy forgatókönyvet az Azure Automation sikertelen bejelentkezési kísérletet azonosított probléma elhárításához. |

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

#### <a name="email-notification"></a>E-mail értesítések
Értesítő e-mailt küldjön egy vagy több címzett.  A tulajdonságok az alábbi táblázatban tartalmaznak.

| Tulajdonság | Leírás |
|:--- |:--- |
| Címzettek |E-mail címek listája. |
| Tárgy |Az e-mail tárgyát. |
| Melléklet |Mellékletek jelenleg nem támogatottak, ezért ez a "None" értéke mindig lesz. |

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

Szervizelt tulajdonságot tartalmazhatja az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| RunbookName |A runbook neve. Ennek egyeznie kell a közzétett runbookok konfigurálva az Automation-megoldás az OMS-munkaterület az automation-fiókban. |
| WebhookUri |A webhook URI Azonosítóját. |
| A lejárati |A lejárati dátum és idő, a webhook.  Ha a webhook egy lejárati nem rendelkezik, majd ez lehet bármely érvényes jövőbeni dátum. |

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

### <a name="webhook-actions"></a>Webhookműveletek
Webhookműveletek egy folyamat megkezdéséhez hívja az egy URL-cím és a nem kötelezően kell küldeni a hasznos adatok között.  Ezek hasonlóak szervizelési műveletek kivételével ezek webhookokkal, amely az Azure Automation-runbook eltérő folyamatok indít el a célja.  A további lehetőséget, hogy a hasznos adatok között a távoli folyamat küldendő is biztosítanak.

Webhookműveletek nem rendelkezik a küszöbérték, de ehelyett hozzá kell adni egy ütemezést, amely egy riasztási műveletek a küszöbértéket.  Összes futni fog, amikor a küszöbérték elérése több Webhook műveleteket adhat hozzá.

Webhookműveletek tulajdonságot tartalmazhatja az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| WebhookUri |Az e-mail tárgyát. |
| customPayload |A webhook küldendő egyéni hasznos.  A formátum a tartalma a webhook megfelelő függ. |

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

#### <a name="create-or-edit-a-webhook-action"></a>Hozzon létre vagy egy webhook művelet szerkesztése
Az egyedi művelet Azonosítójú a Put metódust segítségével hozzon létre egy új webhook műveletet ütemezés szerint.  Az alábbi példa hoz létre egy Webhook műveletet és egy műveletet a küszöbértéket, hogy a webhook aktiválódik, amikor lépheti túl a küszöbértéket, a mentett keresés eredménye.

    $thresholdAction = "{'properties': { 'Name': 'My Threshold', 'Version':'1', 'Type':'Alert', 'Threshold': { 'Operator': 'gt', 'Value': 10 } }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mythreshold?api-version=2015-03-20 $thresholdAction

    $webhookAction = "{'properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

A webhook művelet ütemezés módosításához használja egy meglévő azonosítójú művelet a Put metódust.  A kérelem törzse tartalmaznia kell a művelet az etag.

    $webhookAction = "{'etag': 'W/\"datetime'2016-02-26T20%3A25%3A00.6862124Z'\"','properties': {'Type': 'Webhook', 'Name': 'My Webhook", 'WebhookUri': 'https://oaaswebhookdf.cloudapp.net/webhooks?token=VrkYTKlhk%2fc%2bKBP', 'CustomPayload': '{\"field1\":\"value1\",\"field2\":\"value2\"}', 'Version': 1 }"
    armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace Name}/savedSearches/{Search ID}/schedules/{Schedule ID}/actions/mywebhookaction?api-version=2015-03-20 $webhookAction

## <a name="next-steps"></a>Következő lépések
* Használja a [REST API napló keresés](log-analytics-log-search-api.md) a Naplóelemzési.

