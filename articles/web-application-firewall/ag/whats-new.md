---
title: Az Azure webalkalmazási tűzfal újdonságai
description: Ismerje meg az Azure webalkalmazási tűzfal újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 11212c931e38b1c6319c7c3554155d9d17a1080c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745145"
---
# <a name="whats-new-in-azure-web-application-firewall"></a>Az Azure webalkalmazási tűzfal újdonságai

Az Azure webalkalmazási tűzfal folyamatosan frissül. A legújabb fejleményekkel naprakészen tarthatja a cikket, amely a következő információkat tartalmazza:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók

## <a name="new-features"></a>Új funkciók

|Szolgáltatás  |Leírás  |Hozzáadás dátuma  |
|---------|---------|---------|
|Robot-elhárítási szabályrendszert (előzetes verzió)|Az Ön által választott CRS-szabály mellett engedélyezheti a bot-elhárítási szabályokat. | 2019. november |
|GeoDB-integráció (előzetes verzió)|Most létrehozhat olyan egyéni szabályokat, amelyek korlátozzák a forgalmat a származási ország/régió alapján. | 2019. november |
|WAF//URI-házirend (előzetes verzió)|A WAF-v2 mostantól támogatja a szabályzatok a figyelőkre való alkalmazását, valamint az elérésiút-alapú szabályokat is. Lásd: [WAF szabályzat létrehozása](create-waf-policy-ag.md). | 2019. november |
|WAF egyéni szabályok |A Application Gateway WAF_v2 mostantól támogatja az egyéni szabályok létrehozását. Lásd: [Application Gateway egyéni szabályok](custom-waf-rules-overview.md). |2019. június |
|WAF-konfiguráció és kizárási lista     |További lehetőségeket adtunk hozzá a WAF konfigurálásához és a téves pozitív riasztások csökkentéséhez. További információért lásd a [webalkalmazási tűzfalra vonatkozó kérelmek méretének korlátozásait és kizárási listáját](application-gateway-waf-configuration.md) .|2018. december|

## <a name="next-steps"></a>További lépések

A Application Gateway webalkalmazási tűzfallal kapcsolatos további információkért lásd: [Azure webalkalmazási tűzfal az azure Application Gateway](ag-overview.md).
