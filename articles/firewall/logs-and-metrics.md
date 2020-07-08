---
title: Azure Firewall naplók és mérőszámok áttekintése
description: Az Azure Firewall tűzfalnaplókkal monitorozható. Az Azure Firewall-erőforrásokon végzett műveletek tevékenységnaplókkal is naplózhatók.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76315031"
---
# <a name="azure-firewall-logs-and-metrics"></a>Az Azure Firewall naplói és metrikái

Az Azure Firewall tűzfalnaplókkal monitorozható. Az Azure Firewall-erőforrásokon végzett műveletek tevékenységnaplókkal is naplózhatók.

Ezen naplók egy része a portálról érhető el. A naplók elküldhetők [Azure monitor naplókba](../azure-monitor/insights/azure-networking-analytics.md), tárhelyre és Event Hubsre, és Azure monitor naplókban vagy különböző eszközök, például az Excel és a Power bi használatával is elemezhetők.

A metrikák egyszerűek, és a közel valós idejű forgatókönyvek révén hasznosak lehetnek a riasztások és a gyors problémák észlelése érdekében.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

 Az Azure Firewallhoz az alábbi diagnosztikai naplók érhetők el:

* **Alkalmazásszabályok naplója**

   Az alkalmazási szabály naplóját a rendszer a Storage-fiókba, az esemény-hubokba továbbítja, és/vagy csak akkor továbbítja Azure Monitor naplókba, ha minden egyes Azure Firewall engedélyezte. Minden új kapcsolat, amely megegyezik egy konfigurált alkalmazásszabállyal, az elfogadott/letiltott kapcsolatra vonatkozó naplóbejegyzést eredményez. Az adatokat a rendszer JSON formátumban naplózza, az alábbi példához látható módon:

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

   A hálózati szabály naplóját a rendszer a Storage-fiókba, az Event hubokba továbbítja, és/vagy csak akkor továbbítja Azure Monitor naplókba, ha minden egyes Azure Firewall engedélyezte. Minden új kapcsolat, amely megegyezik egy konfigurált hálózati szabállyal, az elfogadott/letiltott kapcsolatra vonatkozó naplót eredményez. Az adatokat a rendszer JSON formátumban naplózza, az alábbi példához látható módon:

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
* **Azure monitor naplók**: a rendszer a Azure monitor naplókat az alkalmazás általános valós idejű figyelésére, vagy trendek keresésére használja.

## <a name="activity-logs"></a>Tevékenységnaplók

   A tevékenységnaplók bejegyzéseit alapértelmezés szerint gyűjti a rendszer, ezeket az Azure Portalon tekintheti meg.

   Az Azure- [tevékenység naplóinak](../azure-resource-manager/management/view-activity-logs.md) (korábbi nevén operatív naplók és naplók) használatával megtekintheti az Azure-előfizetéséhez benyújtott összes műveletet.

## <a name="metrics"></a>Mérőszámok

A Azure Monitor mérőszámai olyan numerikus értékek, amelyek egy adott rendszer bizonyos aspektusait írják le egy adott időpontban. A metrikák gyűjtése percenként történik, és a rendszer a riasztásokhoz is hasznos, mivel azok gyakran mintavételezésre kerülnek. A riasztások gyorsan és viszonylag egyszerű logikával is elindíthatók.

A Azure Firewall a következő metrikák érhetők el:

- **Alkalmazási szabályok – találatok** száma – az alkalmazás szabályának megtalálása.

    Egység: darabszám

- **Hálózati szabályok találatok** száma – az a szám, amikor egy hálózati szabály találatot észlelt.

    Egység: darabszám

- **Feldolgozott adatforgalom** – a tűzfalon áthaladó adatmennyiség.

    Egység: bájtok

- **Tűzfal** állapota – a tűzfal állapotát jelzi a SNAT-port rendelkezésre állása alapján.

    Egység: százalék

   Ez a metrika két dimenzióval rendelkezik:
  - Állapot: a lehetséges értékek állapota *kifogástalan*, *csökkentett teljesítményű*, *sérült*.
  - Ok: a tűzfal megfelelő állapotának okát jelzi. 

     Ha SNAT-portokat használ > 95%-ot, akkor a rendszer kimeríti őket, és az állapota 50%, állapot =**csökkentett teljesítményű** , ok =**SNAT-port**. A tűzfal megtartja a forgalom feldolgozását, és a meglévő kapcsolatokat nem érinti. Előfordulhat azonban, hogy az új kapcsolatokat nem lehet időnként létrehozni.

     Ha SNAT-portokat használ < 95%-ot, akkor a tűzfal kifogástalannak minősül, és a rendszer 100%-ként jeleníti meg az állapotot.

     Ha nem jelentettek SNAT-portok használatát, az állapot 0%-ként jelenik meg. 

- **SNAT-port kihasználtsága** – a tűzfal által használt SNAT-portok százalékos aránya.

    Egység: százalék

   Ha több nyilvános IP-címet ad hozzá a tűzfalhoz, további SNAT portok érhetők el, ami csökkenti a SNAT-portok kihasználtságát. Emellett, ha a tűzfal a különböző okokból (például CPU vagy átviteli sebesség) is kibővíthető, további SNAT-portok is elérhetővé válnak. Így gyakorlatilag a SNAT-portok kihasználtságának adott százaléka lemerülhet anélkül, hogy nyilvános IP-címeket adna hozzá, csak azért, mert a szolgáltatás ki van bővítve. A tűzfalon elérhető portok növeléséhez a rendelkezésre álló nyilvános IP-címek számát közvetlenül is szabályozhatja. Azonban nem vezérelheti közvetlenül a tűzfal skálázását. A SNAT-portok jelenleg csak az első öt nyilvános IP-címhez lesznek hozzáadva.   


## <a name="next-steps"></a>További lépések

- A Azure Firewall naplók és mérőszámok figyelésének megismeréséhez tekintse meg az [oktatóanyag: Azure Firewall naplók figyelése](tutorial-diagnostics.md)című témakört.

- Ha többet szeretne megtudni a Azure Monitor metrikákkal kapcsolatban, tekintse meg a [Azure monitor mérőszámait](../azure-monitor/platform/data-platform-metrics.md).
