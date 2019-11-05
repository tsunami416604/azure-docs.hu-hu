---
title: Az Azure webalkalmazási tűzfal újdonságai
description: Ismerje meg az Azure webalkalmazási tűzfal újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 0e0e67bfb893e1bf141182e45ce4a49f5f6880ca
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73502246"
---
# <a name="whats-new-in-azure-web-application-firewall"></a>Az Azure webalkalmazási tűzfal újdonságai

Az Azure webalkalmazási tűzfal folyamatosan frissül. A legújabb fejleményekkel naprakészen tarthatja a cikket, amely a következő információkat tartalmazza:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók

## <a name="new-features"></a>ÚJ funkciók

|Szolgáltatás  |Leírás  |Közzététel dátuma  |
|---------|---------|---------|
|Robot-elhárítási szabályrendszert (előzetes verzió)|Az Ön által választott CRS-szabály mellett engedélyezheti a bot-elhárítási szabályokat. | November 2019 |
|GeoDB-integráció (előzetes verzió)|Most létrehozhat olyan egyéni szabályokat, amelyek korlátozzák a forgalmat a származási ország alapján. | November 2019 |
|WAF//URI-házirend (előzetes verzió)|A WAF-v2 mostantól támogatja a szabályzatok a figyelőkre való alkalmazását, valamint az elérésiút-alapú szabályokat is. Lásd: [WAF szabályzat létrehozása](create-waf-policy-ag.md). | November 2019 |
|WAF egyéni szabályok |Application Gateway WAF_v2 mostantól támogatja az egyéni szabályok létrehozását. Lásd: [Application Gateway egyéni szabályok](custom-waf-rules-overview.md). |2019. június |
|WAF-konfiguráció és kizárási lista     |További lehetőségeket adtunk hozzá a WAF konfigurálásához és a téves pozitív riasztások csökkentéséhez. További információért lásd a [webalkalmazási tűzfalra vonatkozó kérelmek méretének korlátozásait és kizárási listáját](application-gateway-waf-configuration.md) .|2018. december|

## <a name="next-steps"></a>További lépések

A Application Gateway webalkalmazási tűzfallal kapcsolatos további információkért lásd: [Azure webalkalmazási tűzfal az azure Application Gateway](ag-overview.md).
