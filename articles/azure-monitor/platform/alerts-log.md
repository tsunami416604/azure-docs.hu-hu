---
title: Létrehozása, megtekintése és kezelése log riasztások az Azure Monitor használatával
description: Az Azure Monitor használatával hozhat létre, megtekintése és kezelése az Azure-beli naplóriasztási szabály.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 1516410952dfdfef0a0ef12c450040d1e3fb8a27
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103763"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Létrehozása, megtekintése és kezelése az Azure Monitor használatával riasztások  

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan állítható be a riasztások felhasználói felületén belül az Azure portal riasztások. Riasztási szabály definíciója van három részből áll:
- Cél: Adott Azure-erőforrás, amely figyelni
- Feltételek: Adott feltétel, vagy logikai, amely jel látható, ha indíthat el művelet
- Művelet: Az értesítés - egyetlen külső eseményfogyasztó adott hívás küldött e-mailben, SMS, webhook stb.

Az előfizetési időszak **Naplóriasztások** írja le a riasztásokhoz, ahol jel alapuló egyéni lekérdezés [Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) vagy [Application Insights](../../azure-monitor/app/analytics.md). További információ funkciót, terminológiája és származó típust [Naplóriasztások – áttekintés](../../azure-monitor/platform/alerts-unified-log.md).

> [!NOTE]
> Népszerű naplóadatait [Azure Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md) is már elérhető az Azure monitorban metrika-platformon. A Részletek nézetben [naplók riasztási metrika](../../azure-monitor/platform/alerts-metric-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Riasztások kezelése az Azure Portalról

Részletes tovább lépésenkénti útmutató, amellyel az Azure portal felületén riasztások használatával.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>A riasztási szabály létrehozása az Azure portal használatával
1. Az a [portál](https://portal.azure.com/), jelölje be **figyelő** , és válassza ki a MONITOR terület - **riasztások**.  
    ![Monitorozás](media/alerts-log/AlertsPreviewMenu.png)

1. Válassza ki a **Új riasztási szabály** gombra kattintva hozzon létre egy új értesítés az Azure-ban.
    ![Riasztás hozzáadása](media/alerts-log/AlertsPreviewOption.png)

1. A riasztás létrehozása szakaszt a három részből álló mellett látható: *Riasztási feltétel megadása*, *riasztás részleteinek megadása*, és *definiálása műveletcsoport*.

    ![Szabály létrehozása](media/alerts-log/AlertsPreviewAdd.png)

1.  A riasztási feltétel megadása használatával a **erőforrás kiválasztása** hivatkozásra, és adja meg a cél erőforrás kijelölésével. Válassza ki a szűrő a _előfizetés_, _erőforrástípus_, és a szükséges _erőforrás_. 

    >[!NOTE]

    > A napló létrehozása a riasztás – ellenőrzése a **log** jel a kiválasztott erőforrás érhető el, mielőtt továbblépne.
    ![Erőforrás kiválasztása](media/alerts-log/Alert-SelectResourceLog.png)

 
1. *Naplóriasztások*: Győződjön meg arról **erőforrástípus** van egy analytics-forrásokhoz, például *Log Analytics* vagy *Application Insights* és típusa, jelezze **Log**, majd egyszer megfelelő **erőforrás** van kiválasztva, kattintson a *kész*. Következő az a **adja meg a feltételeket** érhető el, az erőforrás és a jel listából jel beállítások listájának megtekintése gombra **egyéni naplóbeli keresés** beállítást a kiválasztott log figyelő szolgáltatásokhoz, mint az *napló Analytics* vagy *az Application Insights*.

   ![Válasszon ki egy erőforrást – egyéni keresés](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Lista importálhat analytics-lekérdezéshez, mivel jel típusa – riasztások **Log (mentett lekérdezés)**, a fenti ábrán látható módon. Így a felhasználók tökéletes a lekérdezést az Analyticsben és mentheti későbbi használatra, a riasztások – olvashat részletesebben használatával érhető el lekérdezés mentése [a log analytics naplóbeli keresés használatával](../../azure-monitor/log-query/log-query-overview.md) vagy [megosztott lekérdezést az application insights szolgáltatásban Analytics](../../azure-monitor/log-query/log-query-overview.md). 

1.  *Naplóriasztások*: A kijelölt riasztási lekérdezés is fel kell tüntetni **keresési lekérdezés** mező; Ha a lekérdezés szintaxisa helytelen a mezőben az hiba vörös színnel jelenik meg. Ha a lekérdezés szintaxisa helyes - referenciaként előzményadatok a megadott lekérdezés látható gráfként kapcsolóval, hogy a Teljesítménybeállítások az időtartomány utolsó 6 óra múlt héten.

 ![Riasztási szabály konfigurálása](media/alerts-log/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > Előzményadatok vizualizációt a lekérdezés eredményében van-e idő – részletek csak látható. Ha a lekérdezés eredménye összegzett adatok vagy az adott oszlop értékének - azonos jelenik meg egyes számú rajzot.

    >  Az Application insights használatával Naplóriasztások Metrikamérés típusú, mely adott változó az adatok csoportosításához használatával megadhatja a **az összesített** lehetőséget; az alábbi képen szemléltetett módon:

    ![a beállítás az összesítés](media/alerts-log/aggregate-on.png)

1.  *Naplóriasztások*: A helyen, a Vizualizáció **riasztási logika** kiválaszthatók a megjelenített beállítások a feltétel, az összesítés, végül a küszöbértéket. Végül adja meg a logikai időt a megadott feltétel értékelése használatával **időszak** lehetőséget. Milyen gyakran fusson a riasztás a kiválasztásával együtt **gyakorisága**.
A **Naplóriasztások** riasztások alapjául is:
   - *Rekordok száma*: Riasztás akkor jön létre, ha a lekérdezés által visszaadott rekordok számát vagy nagyobb vagy kisebb, mint a megadott érték.
   - *Metrikus egység*: Riasztás jön létre, ha egyes *összesített érték* az eredmények között meghaladja a megadott küszöbérték és *szerint csoportosítva* választott érték. Az aktivista álláspontokkal riasztás a küszöbérték elérése esetén a választott időszakban hányszor. Összes incidens bármely kombinációjával adatszivárgás is megadhat az eredménykészletet vagy egymás utáni incidensek megkövetelése, hogy az illetéktelen behatolásokat fel kell a rendszer egymást követő minták között. Tudjon meg többet [riasztások és azok típusát](../../azure-monitor/platform/alerts-unified-log.md).


1. A második lépésben adja meg a riasztás a nevét a **riasztási szabály neve** mezőt, valamint egy **leírása** -a riasztás adatait részletezve és **súlyossági** értéket a a megadott beállításokat. Ezeket az adatokat az összes értesítő e-mailek, értesítések és az Azure Monitor által végzett leküldéses újra felhasználhatók. Ezenkívül felhasználó dönthet azonnal aktiválni a riasztási szabály létrehozása a megfelelő átállításával **engedélyezése a szabály létrehozásakor** lehetőséget.

    A **Naplóriasztások** csak néhány további funkcióval érhető el a riasztás részleteit:

    - **Riasztások mellőzésének időtartama**: Bekapcsolja a riasztási szabály letiltásra, amikor a szabályhoz tartozó műveleteket egy meghatározott ideig új riasztás létrehozása után le vannak tiltva. A szabály továbbra is fut, és riasztási rekordokat hoz létre a megadott feltétel teljesülése. Ami lehetővé teszi, ideje a probléma javításához ismétlődő műveletek futtatása nélkül.

        ![Naplóriasztásokra vonatkozó figyelmeztetések mellőzése](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Adjon meg egy mellőzése riasztási érték nagyobb, mint a riasztásból értesítéseket le lesz állítva, átfedés nélkül gyakorisága

1. A harmadik és egyben utolsó lépésként adja meg az esetleges **műveletcsoport** aktiválódik a riasztási szabály a riasztási feltétel teljesülése esetén kell. Válassza ki alert bármely meglévő műveletcsoport, vagy hozzon létre egy új művelet. A következők szerint kiválasztva műveletcsoportot, eseményindító Azure rendszer riasztás esetén: email(s) küldése, SMS(s) küldeni, webhook meghívása, javíthatja a használatával az Azure-forgatókönyvek, leküldéses az ITSM-eszközhöz, stb. Tudjon meg többet [Műveletcsoportok](action-groups.md).

    > [!NOTE]
    > Tekintse meg a [az Azure-előfizetési szolgáltatási korlátok](../../azure-subscription-service-limits.md) a Runbook által aktivált Azure action groups általi keresztül naplóriasztásokra vonatkozó is észleltünk adattartalmakat vonatkozó korlátozások

    A **Naplóriasztások** néhány további funkcióval érhető el az alapértelmezett műveletek felülbírálása:

    - **E-mailes értesítés**: Felülbírálások *e-mail tárgya* az e-mailben küldött műveletcsoport; Ha egy vagy több e-mail-művelet az említett műveleti csoport létezik. Az üzenet törzsét nem módosítható, és ez a mező **nem** e-mail-cím.
    - **Egyéni Json-adattartalom tartalmaznak**: Felülbírálja a webhook Műveletcsoportok; által használt JSON-ban Ha egy vagy több webhook-műveletek az említett műveletcsoport szerepel. Felhasználó úgy adhat meg a JSON társított műveletcsoport; konfigurált összes webhookok használandó formátumát a webhook-formátumokról további információkért lásd: [Naplóriasztásokra vonatkozó webhook művelettel](../../azure-monitor/platform/alerts-log-webhook.md). Nézet Webhook lehetőséget biztosítunk formátumú JSON-mintafájl adatairól használatával ellenőrizheti.

        ![Naplóriasztásokra vonatkozó művelet felülbírálások](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Ha érvényes az összes mező kitöltése és a zöld osztásjelek a **riasztási szabály létrehozása** gombra, ha rájuk kattint, és a egy riasztás jön létre az Azure Monitor - riasztásokat. A riasztások irányítópult minden riasztás is megtekinthetők.

    ![A szabály létrehozása](media/alerts-log/AlertsPreviewCreate.png)

    Néhány percen belül a riasztás aktív, és elindítja a fent leírtaknak megfelelően.

Felhasználók is fejeződik be, a saját elemzési lekérdezés [naplók elemzési lap az Azure Portalon](../../azure-monitor/log-query/portals.md#log-analytics-page
) és majd leküldeni a keresztül beállítva riasztás gomb - riasztás létrehozása, majd a következő utasításokat a 6. lépés és újabb verziók esetében a fenti oktatóanyagban.

 ![Log Analytics - riasztás beállítása](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Megtekinthet és az Azure Portalon riasztások kezelése

1. Az a [portál](https://portal.azure.com/), jelölje be **figyelő** , és válassza ki a MONITOR terület - **riasztások**.  

1. A **riasztások irányítópult** jelenik meg – viselkedésmintáit jelennek meg minden Azure Alerts (riasztások is beleértve) egy egyetlen táblára; beleértve minden példányát, ha a napló a riasztási szabály aktiválva van. További tudnivalókért lásd: [Riasztáskezelés](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Naplóriasztási szabály foglalja magában a felhasználó által megadott egyéni lekérdezés alapú logika és így olyan megoldott állapotban nélkül. Miatt, amely minden alkalommal, amikor a napló a riasztási szabályban megadott feltételek teljesülnek, akkor lép működésbe. 


1. Válassza ki a **szabályok kezelése** gombot a felső sávon, nyissa meg a szabály felügyeleti szakaszra - létrehozott összes riasztási szabályt listázó; többek között a riasztásokat, amelyek le vannak tiltva.
    ![ Riasztási szabályok kezelése](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Azure Resource-sablonnal riasztások kezelése
Riasztás hozható létre log jelenleg két különböző erőforrás sablonok használatával alapján, az analytics platform a riasztás van (vagyis) Log Analytics vagy az Application Insights alapjául.

Ezért az alábbi szakasz részletekkel szolgálnak Naplóriasztásokra vonatkozó erőforrás-sablon használatával minden egyes elemzési platform.

### <a name="azure-resource-template-for-log-analytics"></a>A Log Analytics az Azure Resource-sablon
Riasztások a Log Analytics, amely a rendszeres időközönkénti mentett keresést futtat riasztási szabályok hozzák létre. Ha a lekérdezés egyeztetés eredményei adott feltételeknek, létrejön egy riasztásbejegyzés, és a egy vagy több műveletek futnak. 

Mentett keresés és a Log analytics-riasztásokkal Log Analytics Resource-sablon dokumentációja a Log Analytics szakaszában érhetők el. További tudnivalókért tekintse meg, [hozzáadása a Log Analytics mentett keresések és a riasztások](../../azure-monitor/insights/solutions-resources-searches-alerts.md); mely szemléltető példák, valamint a séma részleteit tartalmazza.

### <a name="azure-resource-template-for-application-insights"></a>Az Application Insights az Azure Resource-sablon
Az Application Insights-erőforrások riasztás van valamilyen `Microsoft.Insights/scheduledQueryRules/`. Az erőforrástípus további információkért lásd: [Azure Monitor - ütemezett lekérdezési szabály API-referencia](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

Az alábbiakban található az struktúráját [ütemezett lekérdezési szabály létrehozása](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) erőforrás-sablont változókként minta adatkészlet alapján.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> És rejtett hivatkozás a célként megadott erőforrás címke mezőt kötelező kitölteni, igénybe veszik a [ütemezett lekérdezési szabály ](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) API-hívás vagy az erőforrás-sablon. 

A fenti json-mintaadatok menthető, ez a bemutató céljából (például:) sampleScheduledQueryRule.json és telepíthetők [Azure Resource Manager az Azure Portalon](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).


## <a name="managing-log-alerts-using-powershell-cli-or-api"></a>A PowerShell, a parancssori felület vagy az API riasztások kezelése
Jelenleg riasztás hozható létre napló alapú két különböző erőforrás-kezelő megfelelő API-k használatával, az analytics platform a riasztás van (vagyis) Log Analytics vagy az Application Insights alapjául.

Ezért a lenti Powershell vagy parancssori felület API segítségével riasztások a Log analytics platformonként meg adatokat.

### <a name="powershell-cli-or-api-for-log-analytics"></a>PowerShell, a parancssori felület vagy a Log Analytics API
A Log Analytics Alert REST API RESTful és az Azure Resource Manager REST API-n keresztül érhető el. Az API-t az így elérhető lesz egy PowerShell-parancssorból, és kimenete JSON formátumban, hogy a keresési eredmények az eredmények használatával számos különböző módon programozott módon.

Tudjon meg többet [létrehozása és kezelése a REST API-val a Log Analytics riasztási szabályai](../../azure-monitor/platform/api-alerts.md)köztük az API-t a Powershell elérése példái.

### <a name="powershell-cli-or-api-for-application-insights"></a>PowerShell, CLI vagy az Application Insights API
[Az Azure Monitor - ütemezett lekérdezési szabály API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) egy REST API-t, és teljes mértékben kompatibilisek az Azure Resource Manager REST API-val. Ezért azt is használható a Powershell használatával a Resource Manager egy parancsmagjához, valamint az Azure CLI-n keresztül.

A használati minta erőforrás sablon korábban bemutatott (sampleScheduledQueryRule.json) Azure Resource Manager PowerShell-parancsmaggal keresztül alább látható a [erőforrás sablonszakasznak](#azure-resource-template-for-application-insights) :
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
A használati minta erőforrás sablon korábban bemutatott (sampleScheduledQueryRule.json) az Azure CLI Azure Resource Manager parancs-n keresztül alább látható a [erőforrás sablonszakasznak](#azure-resource-template-for-application-insights) :

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Sikeres művelet esetén 201-es állapot új riasztási szabály létrehozása ad vissza, vagy a 200-as vissza kell adni, ha egy meglévő riasztási szabályt módosítva lett.


  
## <a name="next-steps"></a>További lépések

* Ismerje meg [Naplóriasztások az Azure-riasztások](../../azure-monitor/platform/alerts-unified-log.md)
* Megismerheti [naplóriasztásokra vonatkozó Webhook-műveletek](../../azure-monitor/platform/alerts-log-webhook.md)
* Tudjon meg többet [Application Insights](../../azure-monitor/app/analytics.md)
* Tudjon meg többet [Log Analytics](../../azure-monitor/log-query/log-query-overview.md). 

