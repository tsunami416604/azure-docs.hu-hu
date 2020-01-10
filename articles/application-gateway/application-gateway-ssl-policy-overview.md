---
title: Az SSL-szabályzat áttekintése az Azure Application Gateway
description: Ismerje meg, hogyan konfigurálhatja az SSL-szabályzatot az Azure Application Gatewayhoz, és csökkentheti a titkosítási és a visszafejtési terhelést a háttér-kiszolgálófarm használatával.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: fe70bd5994d835bdc2651a64d35c988ea38b8511
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770033"
---
# <a name="application-gateway-ssl-policy-overview"></a>Application Gateway SSL-házirend áttekintése

Az Azure Application Gateway segítségével központosíthatja az SSL-tanúsítványok kezelését, és csökkentheti a háttérbeli kiszolgálófarm titkosítási és visszafejtési terhelését. Ez a központosított SSL-kezelési szolgáltatás azt is lehetővé teszi, hogy megadhat egy központi SSL-házirendet, amely megfelel a szervezeti biztonsági követelményeknek. Ez segít a megfelelőségi követelmények, valamint a biztonsági irányelvek és a javasolt eljárások teljesítésében.

Az SSL-szabályzat magában foglalja az SSL protokoll verziószámát, valamint a titkosítási csomagokat, valamint azt is, hogy az SSL-kézfogás során milyen sorrendben használják a titkosítási csomagokat. Application Gateway két módszert kínál az SSL-házirendek szabályozására. Használhat előre definiált szabályzatot vagy egyéni szabályzatot is.

## <a name="predefined-ssl-policy"></a>Előre definiált SSL-házirend

Application Gateway három előre definiált biztonsági házirenddel rendelkezik. Ezen szabályzatok bármelyikével konfigurálhatja az átjárót a megfelelő szintű biztonság eléréséhez. A szabályzatok neveit az év és a hónap, amelyben konfigurálták. Az egyes házirendek különböző SSL protokoll-és titkosítási csomagokat biztosítanak. Javasoljuk, hogy a legújabb SSL-házirendeket használja a legjobb SSL-biztonság biztosításához.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Tulajdonság  |Value (Díj)  |
|---|---|
|Név     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Alapértelmezett| True (ha nincs megadva előre definiált házirend) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Tulajdonság  |Value (Díj)  |
|   ---      |  ---       |
|Név     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Alapértelmezett| Hamis |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Tulajdonság  |Value (Díj)  |
|---|---|
|Név     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Alapértelmezett| Hamis |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Egyéni SSL-házirend

Ha előre definiált SSL-szabályzatot kell konfigurálnia a követelményekhez, meg kell határoznia a saját egyéni SSL-szabályzatát. Egyéni SSL-szabályzattal teljes mértékben szabályozhatja az SSL protokoll minimális verziójának támogatását, valamint a támogatott titkosítási csomagokat és azok prioritási sorrendjét.
 
### <a name="ssl-protocol-versions"></a>SSL protokoll verziói

* Az SSL 2,0 és a 3,0 alapértelmezés szerint le van tiltva az összes Application Gateway átjáró esetében. A protokollok verziószáma nem konfigurálható.
* Az egyéni SSL-házirend lehetőséget ad a következő három protokoll valamelyikének kiválasztására az átjáró minimális SSL protokoll-verziójaként: TLSv1_0, TLSv1_1 és TLSv1_2.
* Ha nincs meghatározva SSL-házirend, mindhárom protokoll (TLSv1_0, TLSv1_1 és TLSv1_2) engedélyezve van.

### <a name="cipher-suites"></a>Titkosítási csomagok

Application Gateway a következő titkosítási csomagokat támogatja, amelyekről kiválaszthatja az egyéni házirendet. A titkosítási csomagok sorrendje határozza meg a prioritási sorrendet az SSL-egyeztetés során.


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
> A kapcsolathoz használt SSL-titkosítási csomagok a használt tanúsítvány típusától függően is érvényesek. Az ügyfél és az Application Gateway közötti kapcsolatok esetében a használt titkosítási csomagok az Application Gateway-figyelő kiszolgálói tanúsítványainak típusán alapulnak. Az Application Gateway és a háttérrendszer-készlet kapcsolatai között a használt titkosítási csomagok a háttérrendszer kiszolgálói tanúsítványainak típusától függenek.

## <a name="known-issue"></a>Ismert probléma
A Application Gateway v2 jelenleg nem támogatja a következő titkosítási műveleteket:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>Következő lépések

Ha meg szeretné ismerni az SSL-szabályzatok konfigurálását, tekintse meg az [SSL-házirend konfigurálása Application gatewayen](application-gateway-configure-ssl-policy-powershell.md)című témakört.
