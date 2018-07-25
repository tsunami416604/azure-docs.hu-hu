---
title: Oktatóanyag – Az Azure Firewall naplóinak monitorozása
description: Eben az oktatóanyagban megismerheti az Azure Firewall naplóinak engedélyezését és kezelését.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991905"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>Oktatóanyag: Az Azure Firewall naplóinak monitorozása

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Az Azure Firewallról szóló cikkek példái azt feltételezik, hogy már engedélyezve van az Azure Firewall nyilvános előzetes verziója. További információ: [Az Azure Firewall nyilvános előzetes verziójának engedélyezése](public-preview.md).

Az Azure Firewall tűzfalnaplókkal monitorozható. Az Azure Firewall-erőforrásokon végzett műveletek tevékenységnaplókkal is naplózhatók.

Ezen naplók egy része a portálról érhető el. A naplók elküldhetők a [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Storage és Event Hubs szolgáltatásokba, és elemezhetők a Log Analyticsben vagy más eszközökben, például az Excelben vagy a Power BI-ban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Naplózás engedélyezése az Azure Portalon
> * Naplózás engedélyezése a PowerShell-lel
> * A tevékenységnapló megtekintése és elemzése
> * A hálózati szabályok és alkalmazásszabályok naplóinak megtekintése és elemzése

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

 Az Azure Firewallhoz az alábbi diagnosztikai naplók érhetők el:

* **Alkalmazásszabályok naplója**

   Az alkalmazásszabályok naplóját a rendszer egy tárfiókba menti, az Event Hubsba streameli és/vagy a Log Analyticsbe küldi, ha ezt minden Azure Firewall-tűzfal esetében engedélyezte. Minden új kapcsolat, amely megegyezik egy konfigurált alkalmazásszabállyal, az elfogadott/letiltott kapcsolatra vonatkozó naplóbejegyzést eredményez. Az adatokat a rendszer JSON formátumban naplózza, az alábbi példához látható módon:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Hálózati szabályok naplója**

   A hálózati szabályok naplóját a rendszer egy tárfiókba menti, az Event Hubsba streameli és/vagy a Log Analyticsbe küldi, ha ezt minden Azure Firewall-tűzfal esetében engedélyezte. Minden új kapcsolat, amely megegyezik egy konfigurált hálózati szabállyal, az elfogadott/letiltott kapcsolatra vonatkozó naplót eredményez. Az adatokat a rendszer JSON formátumban naplózza, az alábbi példához látható módon:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

A naplók tárolásához három lehetőség közül választhat:

* **Storage-fiók**: A Storage-fiókok akkor a legmegfelelőbbek a naplók tárolására, ha a naplókat hosszabb ideig tárolják, és szükség esetén áttekintik őket.
* **Event Hubs-eseményközpont**: Az eseményközpontok ideális megoldások egyéb biztonsági információkkal és eseménykezelési (SEIM) eszközökkel való integrációhoz, amelyekkel az erőforrásokra vonatkozó riasztásokat kaphat.
* **Log Analytics**: A Log Analytics a legmegfelelőbb az alkalmazás általános valós idejű monitorozásához vagy trendek megtekintéséhez.

## <a name="activity-logs"></a>Tevékenységnaplók

   A tevékenységnaplók bejegyzéseit alapértelmezés szerint gyűjti a rendszer, ezeket az Azure Portalon tekintheti meg.

   Az [Azure-tevékenységnaplók](../azure-resource-manager/resource-group-audit.md) (korábban műveleti naplók és auditnaplók) használatával megtekintheti az Azure-előfizetésére elküldött összes műveletet.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Diagnosztikai naplózás engedélyezése az Azure Portalon

A diagnosztikai naplózás bekapcsolása után eltarthat néhány percig, amíg az adatok megjelennek a naplókban. Ha nem jelennek meg azonnal az adatok, tekintse meg a naplókat néhány perc múlva.

1. Az Azure Portalon nyissa meg a tűzfal erőforráscsoportját, és kattintson a tűzfalra.
2. A **Monitorozás** területen kattintson a **Diagnosztikai naplók** elemre.

   Az Azure Firewallhoz két szolgáltatásspecifikus napló érhető el:

   * Alkalmazásszabályok naplója
   * Hálózati szabályok naplója

3. Az adatok gyűjtésének elkezdéséhez kattintson a **Diagnosztika bekapcsolása** elemre.
4. A **Diagnosztikai beállítások** lap megadja a diagnosztikai naplók beállításait. 
5. Ebben a példában a Log Analytics tárolja a naplókat, ezért névként adja meg a következőt: **Firewall log analytics**.
6. A munkaterület konfigurálásához kattintson a **Küldés a Log Analyticsnek** elemre. Eseményközpontot és tárfiókot is használhat a diagnosztikai naplók mentésére.
7. A **Log Analytics**területen kattintson a**Konfigurálás** elemre.
8. Az OMS-munkaterületek oldalon kattintson az **Új munkaterület létrehozása** elemre.
9. A **Log Analytics-munkaterület** lapon az új **OMS-munkaterület** nevének írja be a **firewall-oms** értéket.
10. Válassza ki az előfizetést, használja a meglévő tűzfal erőforráscsoportot (**Test-FW-RG**), válassza az **USA keleti régiója** helyet, majd válassza az **Ingyenes** tarifacsomagot.
11. Kattintson az **OK** gombra.
   ![A konfigurációs folyamat indítása][1]
12. A **Naplók** területen kattintson az **AzureFirewallApplicationRule** és az **AzureFirewallNetworkRule** elemre az alkalmazásszabályok és hálózati szabályok naplóinak gyűjtéséhez.
   ![Diagnosztikai beállítások mentése][2]
13. Kattintson a **Save** (Mentés) gombra.

## <a name="enable-logging-with-powershell"></a>Naplózás engedélyezése a PowerShell-lel

A tevékenységnaplózás automatikusan engedélyezve van minden Resource Manager-erőforráshoz. A diagnosztikai naplózást engedélyezni kell a naplókban elérhető adatok gyűjtésének megkezdéséhez.

A diagnosztikai naplózás engedélyezéséhez kövesse az alábbi lépéseket:

1. Jegyezze fel azon Storage-fiók erőforrás-azonosítóját, ahol a naplóadatokat tárolja. Ez az érték a következő formát követi: */előfizetések/\<előfizetésazonosító\>/erőforráscsoportok/\<erőforráscsoport neve\>/szolgáltatások/Microsoft.Storage/storage-fiókok/\<storage-fiók neve\>*.

   Az előfizetés bármilyen tárfiókját használhatja. Ezeket az információkat az Azure Portalon találhatja meg. Az információk az erőforrás **Tulajdonság** lapján érhetők el.

2. Jegyezze fel azon tűzfal erőforrás-azonosítóját, amelyhez engedélyezve van a naplózás. Ez az érték a következő formát követi: */előfizetések/\<előfizetésazonosító\>/erőforráscsoportok/\<erőforráscsoport neve\>/szolgáltatások/Microsoft.Network/azureFirewalls/\<tűzfal neve\>*.

   Ezeket az információkat a portálon találhatja meg.

3. Engedélyezze a diagnosztikai naplózást az alábbi PowerShell-parancsmaggal:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>A diagnosztikai naplókhoz nincs szükség külön Storage-fiókra. A Storage hozzáférés- és teljesítménynaplózásra való használata szolgáltatási díjjal jár.

## <a name="view-and-analyze-the-activity-log"></a>A tevékenységnapló megtekintése és elemzése

A tevékenységnaplók adatainak megtekintéséhez és elemzéséhez használja az alábbi módszerek bármelyikét:

* **Azure-eszközök**: Információkat kérhet le a tevékenységnaplóból az Azure PowerShell-lel, az Azure CLI-vel, az Azure REST API-val vagy az Azure Portallal. Az egyes módszerek részletes útmutatóit a [Resource Managerrel végzett tevékenységművelet](../azure-resource-manager/resource-group-audit.md) című cikkben találja.
* **Power BI**: Ha még nem rendelkezik [Power BI](https://powerbi.microsoft.com/pricing)-fiókkal, ingyenesen kipróbálhatja. A [Power BI-hoz készült Azure Activity Logs-tartalomcsomaggal](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) olyan előre konfigurált irányítópultokkal elemezheti az adatokat, amelyeket eredeti formájukban vagy testre szabva is használhat.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>A hálózati szabályok és alkalmazásszabályok naplóinak megtekintése és elemzése

Az Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) a számlálónapló- és eseménynapló-fájlokat gyűjti. A Log Analytics vizualizációkat és hatékony keresési lehetőségeket is tartalmaz a naplók elemzéséhez.

A Storage-fiókjához is csatlakozhat, és lekérheti a hozzáférés- és teljesítménynaplók JSON-naplóbejegyzéseit. A letöltött JSON-fájlokat átalakíthatja CSV-fájlokká, és ezeket megtekintheti az Excelben, Power BI-ban vagy bármely más adatvizualizációs eszközben.

> [!TIP]
> Ha ismeri a Visual Studiót, illetve C#-állandók és -változók módosításának alapfogalmait, használja a GitHubról elérhető [naplókonvertáló eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter).


## <a name="next-steps"></a>További lépések

Most, hogy konfigurálta a tűzfalat a naplók gyűjtésére, megtekintheti az adatait a Log Analyticsben.

> [!div class="nextstepaction"]
> [Hálózatmonitorozási megoldások a Log Analyticsben](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
