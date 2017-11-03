---
title: "Az Azure Application Gateway SSL-házirend áttekintése |} Microsoft Docs"
description: "További tudnivalók: hogyan Azure Application Gateway teszi lehetővé az SSL-házirend konfigurálása"
services: application gateway
documentationcenter: na
author: amsriva
manager: 
editor: 
tags: azure resource manager
ms.service: application gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.openlocfilehash: ec36af282bbfdc22ff88082412dd18cd2a85f245
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-ssl-policy-overview"></a>Alkalmazás átjáró SSL házirendek – áttekintés

Azure Application Gateway segítségével központosíthatja az SSL-tanúsítványok kezelését, valamint csökkentik a titkosítási és visszafejtési terhelést a háttér-kiszolgálófarm. A központi SSL kezelésére is lehetővé teszi egy központi SSL-házirend, amely a szervezet biztonsági követelményeinek megfelelő megadását. Ez segít felel meg a megfelelőségi követelményeket, valamint a biztonsági irányelveknek és ajánlott eljárások.

Az SSL-házirend tartalmazza az SSL protokoll verziója, valamint a titkosítási csomagok és a sorrendet, amelyben Rejtjelek egy SSL-kézfogás során használt irányítását. Alkalmazásátjáró SSL-házirend szabályozó két mechanizmust kínálja. Előre definiált szabályzattal vagy egyéni házirendet is használhatja.

## <a name="predefined-ssl-policy"></a>Előre definiált SSL-házirend

Alkalmazásátjáró három előre meghatározott biztonsági házirend tartozik. Az átjáró említett házirendjeinek a megfelelő szintű biztonságot segítségével konfigurálható. A házirend nevét által kiválasztott évhez és hónaphoz konfigurálva volt vannak feliratozva. Minden egyes házirend ajánlatok különböző SSL protokoll verziói és titkosító csomagok. Azt javasoljuk, hogy használja-e a legújabb SSL-házirendekkel ajánlott SSL védelme érdekében.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Tulajdonság  |Érték  |
|---|---|
|Név     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Alapértelmezett| Igaz (Ha nincs előre definiált szabályzattal meg van adva) |
|Titkosítási készletek     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
  ### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Tulajdonság  |Érték  |
|   ---      |  ---       |
|Név     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Alapértelmezett| False (Hamis) |
|Titkosítási készletek     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Tulajdonság  |Érték  |
|---|---|
|Név     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Alapértelmezett| False (Hamis) |
|Titkosítási készletek     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Egyéni SSL-házirend

Ha egy előre megadott SSL-házirend kell megadni a követelményeket, meg kell adnia a saját egyéni SSL-házirend. Egyéni SSL-házirend informatikai részleg teljes felügyeletet gyakorolhat a minimális SSL protokoll verziója támogatásához, valamint a támogatott titkosítócsomagok és a prioritásuk szerinti sorrendben történik.
 
### <a name="ssl-protocol-versions"></a>Az SSL protokoll verziói

* Az SSL 2.0 és 3.0 le vannak tiltva, alapértelmezés szerint minden alkalmazásátjárót. Ezen protokoll verziói esetében nem konfigurálhatók.
* Egyéni SSL-házirend felajánlja a lehetőséget, jelölje ki a következő három protokollok egyikét sem a minimális SSL protokoll verziója az átjáró: TLSv1_0 TLSv1_1 és TLSv1_2.
* Nincs SSL-házirend lett meghatározva, az összes három protokollok (TLSv1_0 TLSv1_1 és TLSv1_2) engedélyezve vannak-e.

### <a name="cipher-suites"></a>Titkosító csomagok

Alkalmazás-átjáró támogatja a következő titkosító csomagok, ahol kiválaszthatja az egyéni házirend. SSL-egyeztetést során titkosító csomag sorrendje határozza meg a prioritásuk szerinti sorrendben történik.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

## <a name="next-steps"></a>Következő lépések

Ha azt szeretné, további SSL-házirend konfigurálásának [konfigurálása az SSL-házirendet a meglévő Alkalmazásátjáró](application-gateway-configure-ssl-policy-powershell.md).
