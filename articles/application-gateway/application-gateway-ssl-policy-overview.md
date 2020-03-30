---
title: TLS-szabályzat – áttekintés az Azure Application Gateway alkalmazáshoz
description: Megtudhatja, hogyan konfigurálhatja a TLS-szabályzatot az Azure Application Gateway alkalmazásátjáróhoz, és hogyan csökkentheti a titkosítási és visszafejtési terhelést egy háttérkiszolgáló-farmról.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 871cb930e867002d8af1e7755de27d4873327543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257376"
---
# <a name="application-gateway-tls-policy-overview"></a>Alkalmazásátjáró TLS-házirend – áttekintés

Az Azure Application Gateway használatával központosíthatja a TLS/SSL tanúsítványkezelést, és csökkentheti a titkosítást és a visszafejtési terhelést egy háttérkiszolgáló-farmról. Ez a központi TLS-kezelés lehetővé teszi a szervezeti biztonsági követelményeknek megfelelő központi TLS-házirend megadását is. Ez segít a megfelelőségi követelményeknek, valamint a biztonsági irányelveknek és az ajánlott eljárásoknak való megfelelésben.

A TLS-házirend magában foglalja a TLS protokoll verzió, valamint a titkosítási csomagok és a titkosítások tls-kézfogás során történő használatának sorrendjét. Az Application Gateway két mechanizmust kínál a TLS-házirend szabályozására. Használhat előre definiált vagy egyéni házirendet.

## <a name="predefined-tls-policy"></a>Előre definiált TLS-házirend

Az Application Gateway három előre definiált biztonsági házirendet rendelkezik. Az átjárót a házirendek bármelyikével konfigurálhatja a megfelelő biztonsági szint eléréséhez. A házirendnevek et a konfigurálás éve és hónapja szerint kell eljegyzetelni. Minden házirend különböző TLS protokollverziókat és titkosítási csomagokat kínál. Azt javasoljuk, hogy a legújabb TLS-házirendek segítségével biztosítsa a legjobb TLS-biztonságot.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolitika20150501

|Tulajdonság  |Érték  |
|---|---|
|Név     | AppGwSslPolitika20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Alapértelmezett| Igaz (ha nincs megadva előre definiált házirend) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Tulajdonság  |Érték  |
|   ---      |  ---       |
|Név     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Alapértelmezett| False (Hamis) |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Tulajdonság  |Érték  |
|---|---|
|Név     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Alapértelmezett| False (Hamis) |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Egyéni TLS-házirend

Ha egy előre definiált TLS-házirendet kell konfigurálni a követelményeknek, meg kell határoznia a saját egyéni TLS-házirendet. Az egyéni TLS-házirenddel teljes mértékben szabályozhatja a támogatandó minimális TLS protokollverziót, valamint a támogatott titkosítási csomagokat és azok prioritási sorrendjét.
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL protokollverziók

* Az SSL 2.0 és 3.0 alapértelmezés szerint minden alkalmazásátjáróesetében le van tiltva. Ezek a protokollverziók nem konfigurálhatók.
* Az egyéni TLS-házirend lehetővé teszi, hogy az alábbi három protokoll közül bármelyiket kiválassza az átjáró minimális TLS protokollverziójaként: TLSv1_0, TLSv1_1 és TLSv1_2.
* Ha nincs megadva TLS-házirend, mindhárom protokoll (TLSv1_0, TLSv1_1 és TLSv1_2) engedélyezve van.

### <a name="cipher-suites"></a>Rejtjelező lakosztályok

Az Application Gateway a következő titkosítási csomagokat támogatja, amelyekből kiválaszthatja az egyéni házirendet. A titkosítási csomagok sorrendje határozza meg a TLS-egyeztetés során a prioritási sorrendet.


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

> [!NOTE]
> A kapcsolathoz használt TLS titkosítási csomagok szintén a használt tanúsítvány típusától függenek. Az ügyfél és az alkalmazás átjáró kapcsolatok, a használt titkosítási csomagok alapján az alkalmazás átjáró figyelője kiszolgálói tanúsítványok. A háttérkészlet-kapcsolatok alkalmazásátjárójában a használt titkosítási csomagok a háttérkészlet-kiszolgálókon lévő kiszolgálói tanúsítványok típusától függenek.

## <a name="known-issue"></a>Ismert probléma
Az Application Gateway v2 jelenleg nem támogatja a következő titkosításokat:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>További lépések

Ha meg szeretné tudni, hogyan konfigurálhat TLS-házirendet, olvassa el [a TLS-házirend-verziók és titkosítási csomagok konfigurálása az Application Gateway alkalmazásban című témakört.](application-gateway-configure-ssl-policy-powershell.md)
