---
title: Az Azure Application Gateway újdonságai
description: Ismerje meg az Azure Application Gateway újdonságait, például a legújabb kiadási megjegyzéseket, az ismert problémákat, a hibajavításokat, az elavult funkciókat és a közelgő módosításokat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 1cfa2ab1d8bf096b1821f7473d9808801b47c547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371171"
---
# <a name="whats-new-in-azure-application-gateway"></a>Az Azure Application Gateway újdonságai

Az Azure Application Gateway folyamatosan frissül. A legújabb fejleményekkel való naprakészen maradás érdekében ez a cikk az ön számára a következőkről nyújt tájékoztatást:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók

## <a name="new-features"></a>Új funkciók

|Szolgáltatás  |Leírás  |Hozzáadás dátuma  |
|---------|---------|---------|
| Felhasználó által definiált útvonalak (UDR) a v2 (előzetes verzió) |A felhasználó által definiált útvonalak mostantól az Application Gateway v2 skus egyes forgatókönyvei ben támogatottak. További információt az [Application Gateway konfigurációjának áttekintése című témakörben talál.](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet) |2020. március |
|Az affinitáscookie-változások |Ha a cookie-alapú affinitás engedélyezve van, az Application Gateway a meglévő ApplicationGatewayAffinity cookie mellett egy másik azonos *cookie-t, az ApplicationGatewayAffinity-t* is befecskendezi. *Az ApplicationGatewayAffinityCORS* további két attribútumot adott hozzá (*SameSite=None; Biztonságos*), így a ragadós munkamenetek még a kereszt-eredetű kérelmek. További információt [az Application Gateway cookie-alapú affinitása](configuration-overview.md#cookie-based-affinity) című témakörben talál. |2020. február |
|A szonda fejlesztései |Az Application Gateway v2 Termékváltozat egyéni mintavételi fejlesztéseivel egyszerűsítettük a [mintavételi konfigurációt,](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)megkönnyítettük [az igény szerinti háttérállapot-teszteket,](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) és [további diagnosztikai információkat](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) adtunk hozzá a háttérrendszer állapotával kapcsolatos problémák elhárításához.  |2019. október |
|További mutatók |A következő új metrikákat adtuk hozzá az Application Gateway v2 Termékváltozat figyeléséhez: [Időzítéssel kapcsolatos metrikák](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), háttérválaszok állapota, Fogadott bájtok, Elküldött bájtok, Ügyfél TLS protokoll és aktuális számítási egységek. Lásd: [Az Application Gateway V2 termékváltozat által támogatott metrikák.](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku) |2019. augusztus |
|WAF egyéni szabályok |Az Application Gateway WAF_v2 mostantól támogatja az egyéni szabályok létrehozását. Lásd: [Application Gateway egyéni szabályok](custom-waf-rules-overview.md). |2019. június |
|Automatikus skálázás, zónaredundancia, statikus VIP támogatás GA |A v2 Termékváltozat általános elérhetősége, amely támogatja az automatikus skálázást, a zónaredundanciát, a teljesítmény növelését, a statikus VIP-ket, a Key Vaultot, a fejléc újraírását. Lásd: [Application Gateway automatikus skálázási dokumentáció](application-gateway-autoscaling-zone-redundant.md). |2019. április |
|Key Vault-integráció |Az Application Gateway mostantól támogatja a Key Vaulttal való integrációt (nyilvános előzetes verzióban) a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok esetében. Lásd: [SSL-megszüntetés key vault-tanúsítványokkal.](key-vault-certs.md) |2019. április |
|Fejléc CRUD/Átírás     |Most már átírhatja a HTTP-fejléceket. [További információ: Hozzon létre egy alkalmazásátjárót, és írja át a HTTP-fejléceket.](tutorial-http-header-rewrite-powershell.md)|2018. december|
|WAF konfigurációs és kizárási lista     |További lehetőségeket adtunk hozzá a WAF konfigurálásához és a hamis pozitív értékek csökkentéséhez. További információt a [Webalkalmazás tűzfala kérelmek méretkorlátai és kizárási listái című témakörben talál.](application-gateway-waf-configuration.md)|2018. december|
|Automatikus skálázás, zónaredundancia, statikus VIP támogatás      |A v2 termékváltozat számos fejlesztést, például automatikus skálázás, jobb teljesítmény, és így tovább. További információ: [Mi az Azure Application Gateway?](overview.md)|2018. szeptember|
|Kapcsolatkiürítés     |A kapcsolat kiürítése lehetővé teszi, hogy kecsesen távolítsa el a tagokat a háttérkészletekből. További információ: [Connection draining](features.md#connection-draining).|2018. szeptember|
|Egyéni hibalapok     |Az egyéni hibaoldalak segítségével a többi webhely formátumán belül létrehozhat egy hibaoldalt. Ennek engedélyezéséhez olvassa el az [Alkalmazásátjáró egyéni hibalapjainak létrehozása című témakört.](custom-error.md)|2018. szeptember|
|Mérőszámok fejlesztései     |Továbbfejlesztett metrikákkal jobban megtekintheti az alkalmazásátjáró állapotát. A metrikák engedélyezése az Application Gateway, lásd: [Back-end állapot, diagnosztikai naplók és metrikák az Application Gateway.](application-gateway-diagnostics.md)|2018. június|

## <a name="next-steps"></a>További lépések

Az Azure Application Gateway alkalmazásátjáróról a [Mi az Azure Application Gateway?](overview.md)
