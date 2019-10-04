---
title: Az Azure Application Gateway újdonságai
description: Ismerje meg az Azure Application Gateway újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 5ae7aa2dfbab5ff62f9ce56bc3a790294e369529
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686827"
---
# <a name="whats-new-in-azure-application-gateway"></a>Az Azure Application Gateway újdonságai

Az Azure Application Gateway frissítése folyamatosan történik. Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

- A legújabb kiadásaihoz.
- Ismert problémák
- Hibajavítások
- Elavult funkciók

## <a name="new-features"></a>Új funkciók

|Funkció  |Leírás  |Hozzáadás dátuma  |
|---------|---------|---------|
|További mérőszámok |A következő új mérőszámokat adtuk hozzá a lekérdezéséhez Gateway v2 SKU figyeléséhez: [Időzítéssel kapcsolatos metrikák](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), háttérbeli válasz állapota, fogadott bájtok, Elküldött bájtok, ügyfél TLS protokoll és aktuális számítási egységek. Lásd: [Application Gateway v2 SKU által támogatott metrikák](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Augusztus 2019 |
|WAF egyéni szabályok |Application Gateway WAF_v2 mostantól támogatja az egyéni szabályok létrehozását. Lásd: [Application Gateway egyéni szabályok](custom-waf-rules-overview.md). |Június 2019 |
|Automatikus skálázás, zóna-redundancia, statikus VIP-támogatás GA |Általánosan elérhető a v2 SKU, amely támogatja az automatikus skálázást, a zóna-redundanciát, a teljesítmény növelését, a statikus VIP-címek, a Key Vault, a fejlécek újraírását Lásd: [Application Gateway automatikus skálázás dokumentációja](application-gateway-autoscaling-zone-redundant.md). |Április 2019 |
|Key Vault integráció |A Application Gateway mostantól támogatja a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok Key Vault (nyilvános előzetes verzióban) való integrálását. Lásd: [SSL-lezárás Key Vault tanúsítványokkal](key-vault-certs.md). |Április 2019 |
|A fejlécben szereplő szifilisz/újraírások     |Mostantól újraírhatók a HTTP-fejlécek. Lásd [az oktatóanyagot: További információért hozzon létre egy Application Gateway-t, és írja át újra a HTTP-fejléceket @ no__t-0.|2018. december|
|WAF-konfiguráció és kizárási lista     |További lehetőségeket adtunk hozzá a WAF konfigurálásához és a téves pozitív riasztások csökkentéséhez. További információért lásd a [webalkalmazási tűzfalra vonatkozó kérelmek méretének korlátozásait és kizárási listáját](application-gateway-waf-configuration.md) .|2018. december|
|Automatikus skálázás, zóna-redundancia, statikus VIP-támogatás      |A v2 SKU számos fejlesztést tartalmaz, többek között az automatikus skálázást, a jobb teljesítményt és egyebeket. További információért lásd: [Mi az az Azure Application Gateway?](overview.md)|2018. szeptember|
|Kapcsolatok kiürítése     |A kapcsolatok kiürítése lehetővé teszi, hogy szabályosan eltávolítsa a háttér-készletek tagjait. További információ: a [kapcsolatok kiürítése](overview.md#connection-draining).|2018. szeptember|
|Egyéni hibalapok     |Az egyéni hibaüzenetek oldalain a webhely többi részének formátumán belüli hibaüzenetet hozhat létre. Ennek engedélyezéséhez tekintse meg az [egyéni hibaüzenetek Application Gateway létrehozása](custom-error.md)című témakört.|2018. szeptember|
|Mérőszámok továbbfejlesztései     |Jobb áttekintést kaphat a Application Gateway állapotáról a továbbfejlesztett metrikákkal. A Application Gateway metrikáinak engedélyezéséhez tekintse meg a [háttér állapota, a diagnosztikai naplók és a metrikák Application Gatewayhoz](application-gateway-diagnostics.md)című témakört.|2018. június|

## <a name="next-steps"></a>További lépések

További információ az Azure Application Gatewayról: [Mi az az azure Application Gateway?](overview.md)
