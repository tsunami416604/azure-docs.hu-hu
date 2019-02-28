---
title: Azure-tűzfal naplók áttekintése
description: Ez a cikk az Azure-tűzfal diagnosztikai naplók áttekintése.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: c129c394f3d694b832722287027c1f9e58028a33
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957691"
---
# <a name="azure-firewall-logs"></a>Az Azure tűzfal-naplókon

Az Azure Firewall tűzfalnaplókkal monitorozható. Az Azure Firewall-erőforrásokon végzett műveletek tevékenységnaplókkal is naplózhatók.

Ezen naplók egy része a portálról érhető el. Lehet küldeni a naplókat [naplózza az Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), Storage és az Event Hubs és elemezheti az Azure Monitor naplóira, vagy a különböző eszközök, például az Excel és a Power bi-ban.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

 Az Azure Firewallhoz az alábbi diagnosztikai naplók érhetők el:

* **Alkalmazásszabályok naplója**

   A szabály napló mentett egy tárfiókot, az Event hubsba streamelt és/vagy az Azure Monitor naplóira küldött csak akkor, ha az egyes Azure tűzfal van engedélyezve. Minden új kapcsolat, amely megegyezik egy konfigurált alkalmazásszabállyal, az elfogadott/letiltott kapcsolatra vonatkozó naplóbejegyzést eredményez. Az adatokat a rendszer JSON formátumban naplózza, az alábbi példához látható módon:

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

   A hálózati szabály napló mentett egy tárfiókot, az Event hubsba streamelt és/vagy az Azure Monitor naplóira küldött csak akkor, ha az egyes Azure tűzfal van engedélyezve. Minden új kapcsolat, amely megegyezik egy konfigurált hálózati szabállyal, az elfogadott/letiltott kapcsolatra vonatkozó naplót eredményez. Az adatokat a rendszer JSON formátumban naplózza, az alábbi példához látható módon:

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

* **Storage-fiók**: Storage-fiókok legjobb használatosak a naplókhoz, amikor a naplók hosszabb ideig tárolja, és szükség esetén tekintse át.
* **Az Event hubs**: Az Event hubs olyan értesítéseket kaphat az erőforrások más biztonsági biztonságiadat- és eseménykezelés (SEIM) felügyeleti eszközökkel való integrálásának remek megoldást.
* **Az Azure Monitor naplóira**: Az Azure Monitor naplóira legjobban használható általános valós idejű figyelést az alkalmazás, vagy tekintse meg a trendeket.

## <a name="activity-logs"></a>Tevékenységnaplók

   A tevékenységnaplók bejegyzéseit alapértelmezés szerint gyűjti a rendszer, ezeket az Azure Portalon tekintheti meg.

   Az [Azure-tevékenységnaplók](../azure-resource-manager/resource-group-audit.md) (korábban műveleti naplók és auditnaplók) használatával megtekintheti az Azure-előfizetésére elküldött összes műveletet.


## <a name="next-steps"></a>További lépések

Azure tűzfal-naplók és metrikák figyelése kapcsolatban lásd: [oktatóanyag: Azure tűzfalnaplók figyelése](tutorial-diagnostics.md).