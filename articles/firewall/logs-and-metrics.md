---
title: Az Azure Tűzfal naplóinak és mutatóinak áttekintése
description: Az Azure Firewall tűzfalnaplókkal monitorozható. Az Azure Firewall-erőforrásokon végzett műveletek tevékenységnaplókkal is naplózhatók.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/22/2020
ms.author: victorh
ms.openlocfilehash: 89c6700d5df3bcef1332121c3cf7d8f720fe054c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76315031"
---
# <a name="azure-firewall-logs-and-metrics"></a>Az Azure Firewall naplói és metrikái

Az Azure Firewall tűzfalnaplókkal monitorozható. Az Azure Firewall-erőforrásokon végzett műveletek tevékenységnaplókkal is naplózhatók.

Ezen naplók egy része a portálról érhető el. A naplók elküldhetők az [Azure Monitor naplóiba](../azure-monitor/insights/azure-networking-analytics.md), a Storage-ba és az Event Hubs-ba, és elemezhetők az Azure Monitor naplóiban vagy különböző eszközökkel, például az Excelés a Power BI segítségével.

A metrikák könnyűek, és közel valós idejű forgatókönyveket támogatnak, így hasznosak a riasztáshoz és a gyors problémaészleléshez.

## <a name="diagnostic-logs"></a>Diagnosztikai naplók

 Az Azure Firewallhoz az alábbi diagnosztikai naplók érhetők el:

* **Alkalmazásszabályok naplója**

   Az alkalmazásszabály-napló egy tárfiókba kerül, az Eseményközpontokba streamelt és/vagy az Azure Monitor naplóiba csak akkor kerül elküldésre, ha engedélyezte azt az egyes Azure-tűzfalakhoz. Minden új kapcsolat, amely megegyezik egy konfigurált alkalmazásszabállyal, az elfogadott/letiltott kapcsolatra vonatkozó naplóbejegyzést eredményez. Az adatokat a rendszer JSON formátumban naplózza, az alábbi példához látható módon:

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

   A hálózati szabály naplója egy tárfiókba kerül, az Eseményközpontokba streamelt és/vagy az Azure Monitor naplóiba csak akkor kerül elküldésre, ha engedélyezte azt az egyes Azure-tűzfalakhoz. Minden új kapcsolat, amely megegyezik egy konfigurált hálózati szabállyal, az elfogadott/letiltott kapcsolatra vonatkozó naplót eredményez. Az adatokat a rendszer JSON formátumban naplózza, az alábbi példához látható módon:

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
* **Azure Monitor naplók:** Az Azure Monitor naplók a legjobb általános valós idejű figyelése az alkalmazás, vagy a trendek.

## <a name="activity-logs"></a>Tevékenységnaplók

   A tevékenységnaplók bejegyzéseit alapértelmezés szerint gyűjti a rendszer, ezeket az Azure Portalon tekintheti meg.

   [Az Azure-tevékenységnaplók](../azure-resource-manager/management/view-activity-logs.md) (korábbi nevén működési naplók és naplónaplók) segítségével megtekintheti az Azure-előfizetésbe küldött összes műveletet.

## <a name="metrics"></a>Mérőszámok

Metrikák az Azure Monitor numerikus értékek, amelyek leírják a rendszer egy adott időpontban néhány szempontból. Metrikák percenként gyűjti, és hasznos riasztási, mert gyakran mintavételezhetők. A riasztás tviszonylag egyszerű logikával gyorsan ellehet tüzelni.

Az Azure Firewall számára a következő metrikák érhetők el:

- **Alkalmazásszabályok találati száma** – Az alkalmazásszabály találatának száma.

    Egység: darabszám

- **Hálózati szabályok találati száma** – A hálózati szabályok találatának száma.

    Egység: darabszám

- **Feldolgozott adatok** – a tűzfalon áthaladó adatok mennyisége.

    Egység: bájt

- **Tűzfal állapota** – A tűzfal állapotát jelzi az SNAT-port rendelkezésre állása alapján.

    Egység: százalék

   Ennek a mutatónak két dimenziója van:
  - Állapot: A lehetséges értékek: *Kifogástalan,* *Degraded*, *UnHealthy*.
  - Ok: A tűzfal megfelelő állapotának okát jelzi. 

     Ha az SNAT-portokat 95%-> használja, akkor azok kimerültek, és az állapot 50% állapot =**Degradálva** és ok=**SNAT port**. A tűzfal folyamatosan dolgozza fel a forgalmat, és a meglévő kapcsolatokat ez nem érinti. Előfordulhat azonban, hogy az új kapcsolatok nem jönnek létre szakaszosan.

     Ha az SNAT-portokat 95%-< használja, akkor a tűzfal kifogástalannak minősül, és az állapot 100%.

     Ha a rendszer nem jelent SNAT-portok használatát, az állapot 0% lesz. 

- **SNAT-port kihasználtsága** – A tűzfal által használt SNAT-portok százalékos aránya.

    Egység: százalék

   Ha több nyilvános IP-címet ad hozzá a tűzfalhoz, több SNAT-port érhető el, csökkentve ezzel az SNAT-portok kihasználtságát. Továbbá, ha a tűzfal különböző okok miatt (például CPU vagy átviteli kapacitás) horizontális felskáláz, további SNAT-portok is elérhetővé válnak. Így hatékonyan, egy adott százaléka SNAT portok kihasználtsága leállhat anélkül, hogy nyilvános IP-címek hozzáadása, csak azért, mert a szolgáltatás horizontálisan. Közvetlenül szabályozhatja a rendelkezésre álló nyilvános IP-címek számát a tűzfalon elérhető portok számának növeléséhez. De nem tudja közvetlenül ellenőrizni a tűzfal méretezését. Jelenleg az SNAT-portok csak az első öt nyilvános IP-címhez kerülnek hozzáadásra.   


## <a name="next-steps"></a>További lépések

- Az Azure Firewall-naplók és -metrikák figyelése az [Oktatóanyag: Az Azure tűzfal naplóinak figyelése.Learn](tutorial-diagnostics.md)how to monitor by Azure Firewall logs and metrics, see Tutorial: Monitor Azure Firewall logs.

- Ha többet szeretne megtudni az Azure Monitor metrikákról, olvassa [el a Metrikák az Azure Monitorban.](../azure-monitor/platform/data-platform-metrics.md)
