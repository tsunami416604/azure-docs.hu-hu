---
title: Azure Firewall veszélyforrások intelligencia-konfigurációja
description: Megtudhatja, hogyan konfigurálhatja a fenyegetés-alapú szűrést a Azure Firewall szabályzatban, hogy riasztást kapjon, és megtagadja a forgalmat az ismert kártékony IP-címekre és tartományokra.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: a663c5f3bcf3492c4a9bc74fe93c6ed6a86137ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530641"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Azure Firewall veszélyforrások intelligencia-konfigurációja

A fenyegetésekkel kapcsolatos intelligencián alapuló szűrés beállítható úgy, hogy az Azure Firewall házirend riasztást küldjön, és megtagadja a forgalmat az ismert kártékony IP-címekre és tartományokra. Az IP-címek és -tartományok forrása a Microsoft veszélyforrás-felderítési hírcsatornája. A [intelligens biztonsági gráf](https://www.microsoft.com/security/operations/intelligence) a Microsoft fenyegetésekkel kapcsolatos intelligenciát, és több szolgáltatás, például Azure Security Center használatát is használja.<br>

Ha beállította a veszélyforrások felderítésére alapuló szűrést, a rendszer a társított szabályokat a NAT-szabályok, a hálózati szabályok vagy az alkalmazási szabályok előtt dolgozza fel.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Veszélyforrások hírszerzési szabályzata":::

## <a name="threat-intelligence-mode"></a>Fenyegetés intelligencia üzemmód

Dönthet úgy, hogy csak riasztást tud naplózni egy szabály indításakor, vagy választhatja a riasztási és a megtagadási módot.

Alapértelmezés szerint a fenyegetési intelligencia-alapú szűrés riasztási módban van engedélyezve.

## <a name="allowed-list-addresses"></a>Engedélyezési lista címei

Az engedélyezett IP-címek listáját beállíthatja úgy, hogy a fenyegetési intelligencia ne szűrje a megadott címeket, tartományokat vagy alhálózatokat.



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

## <a name="next-steps"></a>Következő lépések

- A [Microsoft biztonsági intelligencia jelentésének](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) áttekintése