---
title: Az Azure Firewall fenyegetésintelligencia-alapú szűrése
description: A fenyegetésfelderítésen alapuló szűrés engedélyezhető a tűzfal számára, hogy figyelmeztesse és megtagadja az ismert rosszindulatú IP-címekről és tartományokról érkező/azokba irányuló forgalmat.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168673"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Az Azure Firewall fenyegetésintelligencia-alapú szűrése

A fenyegetésfelderítésen alapuló szűrés engedélyezhető a tűzfal számára, hogy figyelmeztesse és megtagadja az ismert rosszindulatú IP-címekről és tartományokról érkező/azokba irányuló forgalmat. Az IP-címek és tartományok a Microsoft Threat Intelligence hírcsatornából származnak. [Az Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) a Microsoft fenyegetésekkel kapcsolatos intelligenciáját működteti, és több szolgáltatás, köztük az Azure Security Center is használja.

![Tűzfal fenyegetésintelligencia](media/threat-intel/firewall-threat.png)

Ha engedélyezte a fenyegetésfelderítésen alapuló szűrést, a kapcsolódó szabályok feldolgozása a NAT-szabályok, hálózati szabályok vagy alkalmazásszabályok bármelyike előtt lesz.

Választhat, hogy csak naplózza a riasztást, ha egy szabály aktiválódik, vagy választhat riasztási és megtagadási módban.

Alapértelmezés szerint a fenyegetésfelderítésen alapuló szűrés riasztási módban engedélyezve van. Ezt a funkciót nem kapcsolhatja ki, és nem módosíthatja a módot, amíg a portálfelület elérhetővé nem válik az Ön régiójában.

![Fenyegetésintelligencia-alapú szűrési portál felülete](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Naplók

A következő naplórészlet egy aktivált szabályt jelenít meg:

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

- **Kimenő tesztelés** – A kimenő forgalmi riasztások nak ritka jelenségnek kell lenniük, mivel ez azt jelenti, hogy a környezet biztonsága sérült. A kimenő riasztások működésének teszteléséhez egy teszt teljes tartományna van, amely riasztást vált ki. Használja **testmaliciousdomain.eastus.cloudapp.azure.com** a kimenő tesztekhez.

- **Bejövő tesztelés** – A bejövő forgalomra vonatkozó riasztások várhatóak, ha a DNST-szabályok konfigurálva vannak a tűzfalon. Ez akkor is igaz, ha csak bizonyos források engedélyezettek a DNAT-szabályban, és a forgalom egyébként megtagadva. Az Azure Firewall nem minden ismert portszkennerre figyelmeztet; csak olyan képolvasókon, amelyekről ismert, hogy rosszindulatú tevékenységet is folytatnak.

## <a name="next-steps"></a>További lépések

- Tekintse meg [az Azure tűzfalnaplózási elemzési mintáit](log-analytics-samples.md)
- Az [Azure-tűzfal üzembe helyezése és konfigurálása](tutorial-firewall-deploy-portal.md)
- A [Microsoft biztonsági intelligencia jelentésének áttekintése](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)