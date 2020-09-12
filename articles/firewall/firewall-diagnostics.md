---
title: Az Azure Firewall naplóinak és metrikáinak monitorozása
description: Ebből a cikkből megtudhatja, hogyan engedélyezheti és kezelheti Azure Firewall-naplókat és-metrikákat.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/02/2020
ms.author: victorh
ms.openlocfilehash: 92fc4252dd52236e2cc4e8fdfdd2afa32059a721
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89376944"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Az Azure Firewall naplóinak és metrikáinak monitorozása

Az Azure Firewall tűzfalnaplókkal monitorozható. Az Azure Firewall-erőforrásokon végzett műveletek tevékenységnaplókkal is naplózhatók. A metrikákkal teljesítményszámlálókat tekinthet meg a portálon.

Ezen naplók egy része a portálról érhető el. A naplók elküldhetők [Azure monitor naplókba](../azure-monitor/insights/azure-networking-analytics.md), tárhelyre és Event Hubsre, és Azure monitor naplókban vagy különböző eszközök, például az Excel és a Power bi használatával is elemezhetők.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt olvassa el [Azure Firewall naplókat és mérőszámokat](logs-and-metrics.md) a Azure Firewall számára elérhető diagnosztikai naplók és metrikák áttekintéséhez.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Diagnosztikai naplózás engedélyezése az Azure Portalon

A diagnosztikai naplózás bekapcsolása után eltarthat néhány percig, amíg az adatok megjelennek a naplókban. Ha nem jelennek meg azonnal az adatok, tekintse meg a naplókat néhány perc múlva.

1. A Azure Portal nyissa meg a tűzfal erőforráscsoportot, és válassza ki a tűzfalat.
2. A **Monitorozás** területen kattintson a **Diagnosztikai beállítások** elemre.

   Az Azure Firewallhoz két szolgáltatásspecifikus napló érhető el:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Válassza a **diagnosztikai beállítás hozzáadása**lehetőséget. A **Diagnosztikai beállítások** lap megadja a diagnosztikai naplók beállításait.
5. Ebben a példában Azure Monitor naplók tárolja a naplókat, ezért írja be a **tűzfal log Analytics** nevet a névre.
6. A **napló**területen válassza a **AzureFirewallApplicationRule** és a **AzureFirewallNetworkRule** lehetőséget az alkalmazás-és hálózati szabályok naplóinak gyűjtéséhez.
7. Válassza a **küldés log Analytics** lehetőséget a munkaterület konfigurálásához.
8. Válassza ki előfizetését.
9. Kattintson a **Mentés** gombra.

## <a name="enable-logging-with-powershell"></a>Naplózás engedélyezése a PowerShell-lel

A tevékenységnaplózás automatikusan engedélyezve van minden Resource Manager-erőforráshoz. A diagnosztikai naplózást engedélyezni kell a naplókban elérhető adatok gyűjtésének megkezdéséhez.

A diagnosztikai naplózás engedélyezéséhez kövesse az alábbi lépéseket:

1. Jegyezze fel azon Storage-fiók erőforrás-azonosítóját, ahol a naplóadatokat tárolja. Ez az érték az alábbi formátumú: */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Storage/storageAccounts/ \<storage account name\> *.

   Az előfizetés bármilyen tárfiókját használhatja. Ezeket az információkat az Azure Portalon találhatja meg. Az információk az erőforrás **Tulajdonság** lapján érhetők el.

2. Jegyezze fel azon tűzfal erőforrás-azonosítóját, amelyhez engedélyezve van a naplózás. Ez az érték az alábbi formátumú: */Subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /providers/Microsoft.Network/azureFirewalls/ \<Firewall name\> *.

   Ezeket az információkat a portálon találhatja meg.

3. Engedélyezze a diagnosztikai naplózást az alábbi PowerShell-parancsmaggal:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>A diagnosztikai naplókhoz nincs szükség külön Storage-fiókra. A Storage hozzáférés- és teljesítménynaplózásra való használata szolgáltatási díjjal jár.

## <a name="view-and-analyze-the-activity-log"></a>A tevékenységnapló megtekintése és elemzése

A tevékenységnaplók adatainak megtekintéséhez és elemzéséhez használja az alábbi módszerek bármelyikét:

* **Azure-eszközök**: Információkat kérhet le a tevékenységnaplóból az Azure PowerShell-lel, az Azure CLI-vel, az Azure REST API-val vagy az Azure Portallal. Az egyes módszerek részletes útmutatóit a [Resource Managerrel végzett tevékenységművelet](../azure-resource-manager/management/view-activity-logs.md) című cikkben találja.
* **Power BI**: Ha még nem rendelkezik [Power BI](https://powerbi.microsoft.com/pricing)-fiókkal, ingyenesen kipróbálhatja. A [Power BI-hoz készült Azure Activity Logs-tartalomcsomaggal](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) olyan előre konfigurált irányítópultokkal elemezheti az adatokat, amelyeket eredeti formájukban vagy testre szabva is használhat.
* **Azure Sentinel**: Azure Firewall naplókat összekapcsolhatja az Azure Sentinel szolgáltatással, így megtekintheti a munkafüzetek naplófájljait, felhasználhatja egyéni riasztások létrehozására, és beépítheti azt a vizsgálat javítására. Az Azure Sentinel Azure Firewall adatösszekötője jelenleg nyilvános előzetes verzióban érhető el. További információ: [adatok Összekötése Azure Firewallról](../sentinel/connect-azure-firewall.md).

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>A hálózati szabályok és alkalmazásszabályok naplóinak megtekintése és elemzése

[Azure monitor naplók](../azure-monitor/insights/azure-networking-analytics.md) gyűjti a számláló-és az Eseménynapló-fájlokat. A Log Analytics vizualizációkat és hatékony keresési lehetőségeket is tartalmaz a naplók elemzéséhez.

Azure Firewall log Analytics-példákat lásd: [Azure Firewall log Analytics-minták](log-analytics-samples.md).

A Storage-fiókjához is csatlakozhat, és lekérheti a hozzáférés- és teljesítménynaplók JSON-naplóbejegyzéseit. A letöltött JSON-fájlokat átalakíthatja CSV-fájlokká, és ezeket megtekintheti az Excelben, Power BI-ban vagy bármely más adatvizualizációs eszközben.

> [!TIP]
> Ha ismeri a Visual Studiót, illetve C#-állandók és -változók módosításának alapfogalmait, használja a GitHubról elérhető [naplókonvertáló eszközöket](https://github.com/Azure-Samples/networking-dotnet-log-converter).

## <a name="view-metrics"></a>Metrikák megtekintése
Tallózással keresse meg az Azure Firewallt a **figyelés** **kiválasztása lapon**. Az elérhető értékeket a **METRIKÁK** legördülő listában találja.

## <a name="next-steps"></a>Következő lépések

Most, hogy beállította a tűzfalat a naplók összegyűjtésére, az adatok megtekintéséhez Azure Monitor naplókat is megtekintheti.

[Hálózati figyelési megoldások Azure Monitor naplókban](../azure-monitor/insights/azure-networking-analytics.md)
