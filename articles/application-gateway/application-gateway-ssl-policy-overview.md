---
title: Az Azure Application Gateway SSL-szabályzatának áttekintése | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure Application Gateway az SSL-szabályzatok konfigurálását
services: application gateway
documentationcenter: na
author: amsriva
manager: ''
editor: ''
tags: azure resource manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.openlocfilehash: 1710635f145136e564a2bad48d539f242c9bc228
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359912"
---
# <a name="application-gateway-ssl-policy-overview"></a>Application Gateway SSL-házirend áttekintése

Az Azure Application Gateway segítségével központosíthatja az SSL-tanúsítványok kezelését, és csökkentheti a háttérbeli kiszolgálófarm titkosítási és visszafejtési terhelését. Ez a központosított SSL-kezelési szolgáltatás azt is lehetővé teszi, hogy megadhat egy központi SSL-házirendet, amely megfelel a szervezeti biztonsági követelményeknek. Ez segít a megfelelőségi követelmények, valamint a biztonsági irányelvek és a javasolt eljárások teljesítésében.

Az SSL-szabályzat magában foglalja az SSL protokoll verziószámát, valamint a titkosítási csomagokat, valamint azt is, hogy az SSL-kézfogás során milyen sorrendben használják a titkosítási csomagokat. Application Gateway két módszert kínál az SSL-házirendek szabályozására. Használhat előre definiált szabályzatot vagy egyéni szabályzatot is.

## <a name="predefined-ssl-policy"></a>Előre definiált SSL-házirend

Application Gateway három előre definiált biztonsági házirenddel rendelkezik. Ezen szabályzatok bármelyikével konfigurálhatja az átjárót a megfelelő szintű biztonság eléréséhez. A szabályzatok neveit az év és a hónap, amelyben konfigurálták. Az egyes házirendek különböző SSL protokoll-és titkosítási csomagokat biztosítanak. Javasoljuk, hogy a legújabb SSL-házirendeket használja a legjobb SSL-biztonság biztosításához.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Tulajdonság  |Value  |
|---|---|
|Name (Név)     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Alapértelmezett| True (ha nincs megadva előre definiált házirend) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Tulajdonság  |Value  |
|   ---      |  ---       |
|Name (Név)     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Alapértelmezett| False (Hamis) |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Tulajdonság  |Value  |
|---|---|
|Name (Név)     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Alapértelmezett| False (Hamis) |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Egyéni SSL-házirend

Ha előre definiált SSL-szabályzatot kell konfigurálnia a követelményekhez, meg kell határoznia a saját egyéni SSL-szabályzatát. Egyéni SSL-szabályzattal teljes mértékben szabályozhatja az SSL protokoll minimális verziójának támogatását, valamint a támogatott titkosítási csomagokat és azok prioritási sorrendjét.
 
### <a name="ssl-protocol-versions"></a>SSL protokoll verziói

* Az SSL 2,0 és a 3,0 alapértelmezés szerint le van tiltva az összes Application Gateway átjáró esetében. A protokollok verziószáma nem konfigurálható.
* Az egyéni SSL-házirend lehetőséget ad a következő három protokoll valamelyikének kiválasztására az átjáró minimális SSL protokoll-verziójaként: TLSv1_0, TLSv1_1 és TLSv1_2.
* Ha nincs SSL-házirend meghatározva, mindhárom protokoll (TLSv1_0, TLSv1_1 és TLSv1_2) engedélyezve van.

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

## <a name="next-steps"></a>További lépések

Ha meg szeretné ismerni az SSL-szabályzatok konfigurálását, tekintse meg az [SSL-házirend konfigurálása Application gatewayen](application-gateway-configure-ssl-policy-powershell.md)című témakört.
