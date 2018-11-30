---
title: Azure-tűzfal naplók áttekintése
description: Ez a cikk az Azure-tűzfal diagnosztikai naplók áttekintése.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 0698f1dbc491781089ef94eec32f2a427fd3cca4
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422388"
---
# <a name="azure-firewall-logs"></a>Az Azure tűzfal-naplókon

Az Azure Firewall tűzfalnaplókkal monitorozható. Az Azure Firewall-erőforrásokon végzett műveletek tevékenységnaplókkal is naplózhatók.

Ezen naplók egy része a portálról érhető el. A naplók elküldhetők a [Log Analytics](../azure-monitor/insights/azure-networking-analytics.md), Storage és Event Hubs szolgáltatásokba, és elemezhetők a Log Analyticsben vagy más eszközökben, például az Excelben vagy a Power BI-ban.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

 Az Azure Firewallhoz az alábbi diagnosztikai naplók érhetők el:

* **Alkalmazásszabályok naplója**

   Az alkalmazásszabályok naplóját a rendszer egy tárfiókba menti, az Event Hubsba streameli és/vagy a Log Analyticsbe küldi, ha ezt minden Azure Firewall-tűzfal esetében engedélyezte. Minden új kapcsolat, amely megegyezik egy konfigurált alkalmazásszabállyal, az elfogadott/letiltott kapcsolatra vonatkozó naplóbejegyzést eredményez. Az adatokat a rendszer JSON formátumban naplózza, az alábbi példához látható módon:

   ```
   Category: application rule logs.
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
   Category: network rule logs.
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


## <a name="next-steps"></a>További lépések

Azure tűzfal-naplók és metrikák figyelése kapcsolatban lásd: [oktatóanyag: a figyelő Azure tűzfal-naplókon](tutorial-diagnostics.md).