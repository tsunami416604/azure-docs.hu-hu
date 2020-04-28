---
title: Azure Firewall fenyegetések felderítésére alapuló szűrés
description: A fenyegetésekkel kapcsolatos intelligencia-alapú szűrés engedélyezhető a tűzfal számára, hogy riasztást kapjon, és megtagadja a forgalmat az ismert kártékony IP-címekre és tartományokra.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74168673"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Azure Firewall fenyegetés intelligencia-alapú szűrés

A fenyegetésekkel kapcsolatos intelligencia-alapú szűrés engedélyezhető a tűzfal számára, hogy riasztást kapjon, és megtagadja a forgalmat az ismert kártékony IP-címekre és tartományokra. Az IP-címek és tartományok forrása a Microsoft Threat Intelligence-hírcsatorna. A [intelligens biztonsági gráf](https://www.microsoft.com/en-us/security/operations/intelligence) a Microsoft fenyegetésekkel kapcsolatos intelligenciát, és több szolgáltatás, például Azure Security Center használatát is használja.

![Tűzfal-veszélyforrások felderítése](media/threat-intel/firewall-threat.png)

Ha engedélyezte a fenyegetést jelentő intelligencia-alapú szűrést, a rendszer feldolgozza a társított szabályokat a NAT-szabályok, a hálózati szabályok vagy az alkalmazási szabályok előtt.

Dönthet úgy, hogy csak a riasztást naplózza egy szabály indításakor, vagy választhatja a riasztás és a Megtagadás módot is.

Alapértelmezés szerint a fenyegetési intelligencia-alapú szűrés riasztási módban van engedélyezve. Ezt a funkciót nem lehet kikapcsolni, vagy módosítani a módot, amíg a portál felülete elérhetővé nem válik a régiójában.

![Fenyegetésekkel kapcsolatos intelligencia-alapú szűrési portál felülete](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Naplók

A következő naplózási részlet egy aktivált szabályt mutat be:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Tesztelés

- **Kimenő tesztelés** – a kimenő forgalmi riasztásoknak ritka előfordulásnak kell lenniük, mivel ez azt jelenti, hogy a környezet biztonsága sérült. A kimenő riasztások tesztelésének segítése érdekében a rendszer létrehoz egy teljes tartománynevet, amely riasztást indít el. A kimenő tesztek **testmaliciousdomain.eastus.cloudapp.Azure.com** használata.

- **Bejövő tesztelés** – a rendszer a bejövő forgalomra vonatkozó riasztásokat jelenít meg, ha a TŰZFALon DNAT-szabályok vannak konfigurálva. Ez akkor is igaz, ha csak bizonyos források engedélyezettek a DNAT-szabályban, és a forgalom egyébként meg van tagadva. Azure Firewall az összes ismert porton nem jeleníti meg a riasztásokat; csak olyan lapolvasók esetében, amelyekről ismert, hogy rosszindulatú tevékenységet is folytatnak.

## <a name="next-steps"></a>További lépések

- Lásd: [Azure Firewall log Analytics minták](log-analytics-samples.md)
- Megtudhatja, hogyan [helyezhet üzembe és konfigurálhat egy Azure Firewall](tutorial-firewall-deploy-portal.md)
- A [Microsoft biztonsági intelligencia jelentésének](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) áttekintése