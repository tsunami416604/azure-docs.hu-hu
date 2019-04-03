---
title: Oktatóanyag – Az Azure Firewall naplóinak és metrikáinak monitorozása
description: Eben az oktatóanyagban megismerheti az Azure Firewall naplóinak és metrikáinak engedélyezését és kezelését.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: 10837730bea17f98083f456ec4c9fb0d7567af57
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877261"
---
# <a name="tutorial-monitor-azure-firewall-logs-and-metrics"></a>Oktatóanyag: Azure tűzfal-naplók és metrikák figyelése

Az Azure Firewall tűzfalnaplókkal monitorozható. Az Azure Firewall-erőforrásokon végzett műveletek tevékenységnaplókkal is naplózhatók. A metrikákkal teljesítményszámlálókat tekinthet meg a portálon. 

Ezen naplók egy része a portálról érhető el. Lehet küldeni a naplókat [naplózza az Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), Storage és az Event Hubs és elemezheti az Azure Monitor naplóira, vagy a különböző eszközök, például az Excel és a Power bi-ban.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Naplózás engedélyezése az Azure Portalon
> * Naplózás engedélyezése a PowerShell-lel
> * A tevékenységnapló megtekintése és elemzése
> * A hálózati szabályok és alkalmazásszabályok naplóinak megtekintése és elemzése
> * Metrikák megtekintése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt olvassa el az [Azure Firewall naplói és metrikáit](logs-and-metrics.md) ismertető cikket az Azure Firewallhoz elérhető diagnosztikai naplók és metrikák áttekintéséhez.


## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Diagnosztikai naplózás engedélyezése az Azure Portalon

A diagnosztikai naplózás bekapcsolása után eltarthat néhány percig, amíg az adatok megjelennek a naplókban. Ha nem jelennek meg azonnal az adatok, tekintse meg a naplókat néhány perc múlva.

1. Az Azure Portalon nyissa meg a tűzfal erőforráscsoportját, és kattintson a tűzfalra.
2. A **figyelés**, kattintson a **diagnosztikai beállítások**.

   Az Azure Firewallhoz két szolgáltatásspecifikus napló érhető el:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Az adatok gyűjtésének elkezdéséhez kattintson a **Diagnosztika bekapcsolása** elemre.
4. A **Diagnosztikai beállítások** lap megadja a diagnosztikai naplók beállításait. 
5. Ebben a példában az Azure Monitor-naplókat a naplók tárolja, ezért írjon be **tűzfal a log analytics** neve.
6. A munkaterület konfigurálásához kattintson a **Küldés a Log Analyticsnek** elemre. Eseményközpontot és tárfiókot is használhat a diagnosztikai naplók mentésére.
7. A **Log Analytics**területen kattintson a**Konfigurálás** elemre.
8. A Log Analytics-munkaterületek oldalon kattintson az **Új munkaterület létrehozása** elemre.
9. A **Log Analytics-munkaterület** lapon az új **Log Analytics-munkaterület** nevének írja be a **firewall-oms** nevet.
10. Válassza ki az előfizetést, használja a meglévő tűzfal erőforráscsoportot (**Test-FW-RG**), válassza az **USA keleti régiója** helyet, majd válassza az **Ingyenes** tarifacsomagot.
11. Kattintson az **OK** gombra.
   ![A konfigurációs folyamat indítása][1] OMS-munkaterülete mostantól nevezzük Log Analytics-munkaterületeket.  
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

* **Azure-eszközök**: A tevékenységnapló használatával az Azure PowerShell-lel, az Azure CLI, az Azure REST API vagy az Azure Portalon lévő információk lekéréséhez. Az egyes módszerek részletes útmutatóit a [Resource Managerrel végzett tevékenységművelet](../azure-resource-manager/resource-group-audit.md) című cikkben találja.
* **Power BI**: Ha még nem rendelkezik egy [Power BI](https://powerbi.microsoft.com/pricing) fiók próbálhatja ki ingyenesen. A [Power BI-hoz készült Azure Activity Logs-tartalomcsomaggal](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) olyan előre konfigurált irányítópultokkal elemezheti az adatokat, amelyeket eredeti formájukban vagy testre szabva is használhat.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>A hálózati szabályok és alkalmazásszabályok naplóinak megtekintése és elemzése

[Az Azure Monitor naplóira](../azure-monitor/insights/azure-networking-analytics.md) a számláló és Eseménynapló fájlokat gyűjt. A Log Analytics vizualizációkat és hatékony keresési lehetőségeket is tartalmaz a naplók elemzéséhez.

Azure-tűzfal a log analytics lekérdezések mintát, lásd: [tűzfal az Azure log analytics minták](log-analytics-samples.md).

A Storage-fiókjához is csatlakozhat, és lekérheti a hozzáférés- és teljesítménynaplók JSON-naplóbejegyzéseit. A letöltött JSON-fájlokat átalakíthatja CSV-fájlokká, és ezeket megtekintheti az Excelben, Power BI-ban vagy bármely más adatvizualizációs eszközben.

> [!TIP]
> Ha ismeri a Visual Studiót, illetve C#-állandók és -változók módosításának alapfogalmait, használja a GitHubról elérhető [naplókonvertáló eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter).

## <a name="view-metrics"></a>Metrikák megtekintése
Nyisson meg egy Azure Firewallt, és a **Monitorozás** területen kattintson a **Metrikák** elemre. Az elérhető értékeket a **METRIKÁK** legördülő listában találja.

## <a name="next-steps"></a>További lépések

Most, hogy konfigurálta a tűzfal a naplókat, áttekintheti az Azure Monitor naplóira szeretné megtekinteni az adatokat.

> [!div class="nextstepaction"]
> [Rendszerben a hálózatfigyelési megoldások az Azure Monitor naplóira](../azure-monitor/insights/azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
