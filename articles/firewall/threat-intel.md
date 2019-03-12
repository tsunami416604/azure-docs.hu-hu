---
title: Az Azure tűzfal fenyegetésfelderítés alapú szűrés
description: További tudnivalók az Azure-tűzfal threat intelligence szűrése
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 4ef9089c94d9e806cc519c4f8243cdcb7e73953a
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730527"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Azure tűzfal threat intelligence-alapú szűrés – nyilvános előzetes verzió

Threat intelligence szerinti szűrés a tűzfalat, hogy a riasztás és a forgalom, és az ismert kártékony IP-címek és tartományok esetén is engedélyezhető. The IP addresses and domains are sourced from the Microsoft Threat Intelligence feed. [Intelligent Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) Microsoft fenyegetésfelderítő működteti, és több szolgáltatást, többek között az Azure Security Center által használt.

![Tűzfal fenyegetések felderítése](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> Threat intelligence alapú szűrés jelenleg nyilvános előzetes verzióban és a egy előzetes szolgáltatói szerződést. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.  A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Threat intelligence szerinti szűrés engedélyezve van, ha a hozzárendelt szabályokat dolgozza fel előbb bármelyikét a NAT-szabályok, a hálózati szabályok és alkalmazás szabályokat. Az előzetes verzióban csak a legmagasabb megbízhatósági rekordokat is.

Jelentkezzen egy riasztást, amikor egy szabály akkor lesz kiváltva, vagy válassza ki a riasztást, és megtagadási mód választhatja.

Alapértelmezés szerint a threat intelligence szerinti szűrés riasztási módban van engedélyezve. Nem kapcsolja ki ezt a szolgáltatást, vagy módosíthatja a módot, amíg az Ön régiójában elérhetővé válik a portál felületén.

![Fenyegetések felderítése-alapú szűrési felületén](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Logs

A következő cikkből napló szerint jeleníti meg egy aktivált szabályhoz:

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

- **Kimenő tesztelés** – kimenő forgalom riasztások kell lennie egy ritka esemény, mivel azt jelenti, hogy a környezet biztonsága sérült. Segítségével teszt kimenő riasztások dolgozik, teljes tartománynév létrehozása egy tesztet, amely riasztást vált ki. Használat **testmaliciousdomain.eastus.cloudapp.azure.com** a kimenő tesztek számára.

- **Bejövő tesztelés** -kell riasztások megtekintéséhez a bejövő forgalom, ha a DNAT-szabályok úgy vannak konfigurálva, a tűzfalon. Ez igaz, akkor is, ha csak a meghatározott források engedélyezettek a DNAT-szabály és a forgalom ellenkező esetben a rendszer megtagadja. Azure-tűzfalon nem szükséges riasztás az összes ismert portot képolvasók; csak az ismert kártékony tevékenységek is végezhetnek képolvasók.

## <a name="next-steps"></a>További lépések

- Lásd: [Azure tűzfal Log Analytics-minták](log-analytics-samples.md)
- Ismerje meg, hogyan [üzembe helyezése és az Azure-tűzfalak konfigurálása](tutorial-firewall-deploy-portal.md)
- Tekintse át a [Microsoft Security fenyegetésfelderítési jelentés](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)