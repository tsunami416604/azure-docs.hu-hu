---
title: Az Azure Application Gateway újdonságai
description: Ismerje meg az Azure Application Gateway újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: b2e7b9f28c84b769fe64be82f91418cc2a7d634f
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044377"
---
# <a name="whats-new-in-azure-application-gateway"></a>Az Azure Application Gateway újdonságai

Az Azure Application Gateway frissítése folyamatosan történik. A legújabb fejleményekkel naprakészen tarthatja a cikket, amely a következő információkat tartalmazza:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók

## <a name="new-features"></a>ÚJ funkciók

|Szolgáltatás  |Leírás  |Közzététel dátuma  |
|---------|---------|---------|
|Mintavételi fejlesztések |A Application Gateway v2 SKU-ban az egyéni mintavételi fejlesztések segítségével egyszerűsítettük a mintavételi [konfigurációt](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku), az [igény szerinti háttér-tesztelést](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) és a háttér-állapottal kapcsolatos problémák megoldásához szükséges [további diagnosztikai információkat](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) . kérdések.  |Október 2019 |
|További mérőszámok |A következő új mérőszámok segítségével figyelheti a lekérdezéséhez Gateway v2 SKU-t: [időzítéssel kapcsolatos metrikák](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), háttérbeli válasz állapota, fogadott bájtok, bájtok, ügyfél TLS protokoll és aktuális számítási egységek. Lásd: [Application Gateway v2 SKU által támogatott metrikák](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |2019. augusztus |
|WAF egyéni szabályok |Application Gateway WAF_v2 mostantól támogatja az egyéni szabályok létrehozását. Lásd: [Application Gateway egyéni szabályok](custom-waf-rules-overview.md). |2019. június |
|Automatikus skálázás, zóna-redundancia, statikus VIP-támogatás GA |Általánosan elérhető a v2 SKU, amely támogatja az automatikus skálázást, a zóna-redundanciát, a teljesítmény növelését, a statikus VIP-címek, a Key Vault, a fejlécek újraírását Lásd: [Application Gateway automatikus skálázás dokumentációja](application-gateway-autoscaling-zone-redundant.md). |2019. április |
|Key Vault integráció |A Application Gateway mostantól támogatja a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok Key Vault (nyilvános előzetes verzióban) való integrálását. Lásd: [SSL-lezárás Key Vault tanúsítványokkal](key-vault-certs.md). |2019. április |
|A fejlécben szereplő szifilisz/újraírások     |Mostantól újraírhatók a HTTP-fejlécek. További információt az [oktatóanyag: Application Gateway létrehozása és a HTTP-fejlécek újraírása](tutorial-http-header-rewrite-powershell.md) című témakörben talál.|2018. december|
|WAF-konfiguráció és kizárási lista     |További lehetőségeket adtunk hozzá a WAF konfigurálásához és a téves pozitív riasztások csökkentéséhez. További információért lásd a [webalkalmazási tűzfalra vonatkozó kérelmek méretének korlátozásait és kizárási listáját](application-gateway-waf-configuration.md) .|2018. december|
|Automatikus skálázás, zóna-redundancia, statikus VIP-támogatás      |A v2 SKU számos fejlesztést tartalmaz, többek között az automatikus skálázást, a jobb teljesítményt és egyebeket. További információért lásd: [Mi az az Azure Application Gateway?](overview.md)|2018. szeptember|
|Kapcsolatkiürítés     |A kapcsolatok kiürítése lehetővé teszi, hogy szabályosan eltávolítsa a háttér-készletek tagjait. További információ: a [kapcsolatok kiürítése](overview.md#connection-draining).|2018. szeptember|
|Egyéni hibalapok     |Az egyéni hibaüzenetek oldalain a webhely többi részének formátumán belüli hibaüzenetet hozhat létre. Ennek engedélyezéséhez tekintse meg az [egyéni hibaüzenetek Application Gateway létrehozása](custom-error.md)című témakört.|2018. szeptember|
|Mérőszámok továbbfejlesztései     |Jobb áttekintést kaphat a Application Gateway állapotáról a továbbfejlesztett metrikákkal. A Application Gateway metrikáinak engedélyezéséhez tekintse meg a [háttér állapota, a diagnosztikai naplók és a metrikák Application Gatewayhoz](application-gateway-diagnostics.md)című témakört.|2018. június|

## <a name="next-steps"></a>Következő lépések

További információ az Azure Application Gatewayról: [Mi az az azure Application Gateway?](overview.md)
