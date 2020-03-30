---
title: Az Azure webalkalmazás-tűzfal újdonságai
description: Ismerje meg az Azure Web Application Firewall újdonságait, például a legújabb kiadási megjegyzéseket, az ismert problémákat, a hibajavításokat, az elavult funkciókat és a közelgő módosításokat.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: overview
ms.date: 10/24/2019
ms.author: victorh
ms.openlocfilehash: 0e0e67bfb893e1bf141182e45ce4a49f5f6880ca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73502246"
---
# <a name="whats-new-in-azure-web-application-firewall"></a>Az Azure webalkalmazás-tűzfal újdonságai

Az Azure webalkalmazás-tűzfal folyamatosan frissül. Ahhoz, hogy naprakész legyen a legújabb fejleményekkel, ez a cikk az ön számára tájékoztatást nyújt:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók

## <a name="new-features"></a>Új funkciók

|Szolgáltatás  |Leírás  |Hozzáadás dátuma  |
|---------|---------|---------|
|Bot mérséklési szabályok (előzetes verzió)|Engedélyezheti a bot mérséklési szabálykészlet, mellett a CRS szabálykészlet választja. | 2019. november |
|GeoDB-integráció (előzetes verzió)|Most antól létrehozhat egyéni szabályokat, amelyek származási ország szerint korlátozzák a forgalmat. | 2019. november |
|WAF webhelyenként/URI-nkénti házirend (előzetes verzió)|A WAF-v2 mostantól támogatja a házirend ek, valamint az útvonalalapú szabályok alkalmazását. Lásd: [WAF-házirend létrehozása](create-waf-policy-ag.md). | 2019. november |
|WAF egyéni szabályok |Az Application Gateway WAF_v2 mostantól támogatja az egyéni szabályok létrehozását. Lásd: [Application Gateway egyéni szabályok](custom-waf-rules-overview.md). |2019. június |
|WAF konfigurációs és kizárási lista     |További lehetőségeket adtunk hozzá a WAF konfigurálásához és a hamis pozitív értékek csökkentéséhez. További információt [a webalkalmazás-tűzfal kérelemméret- és kizárási listáiban](application-gateway-waf-configuration.md) talál.|2018. december|

## <a name="next-steps"></a>További lépések

Az Application Gateway webalkalmazás-tűzfaláról az Azure Web Application Firewall on Azure Application Gateway című [témakörben talál](ag-overview.md)további információt.
