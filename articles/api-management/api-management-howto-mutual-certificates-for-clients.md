---
title: Biztonságos API-t az ügyfél Tanúsítványalapú hitelesítés, az API Management – Azure API Management |} A Microsoft Docs
description: Ismerje meg, hogyan biztonságos hozzáférés az API-t az ügyféltanúsítványok
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: b2d8a194abb5a5fe7d9c06cb9ef10bb0af58124a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870164"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Hogyan teheti biztonságossá az ügyfél API-k az API Management a Tanúsítványalapú hitelesítés

Az API Management lehetővé teszi, hogy a biztonságos hozzáférés az API-k (azaz, és az API Management ügyfél) ügyféltanúsítványok használatát. Jelenleg egy kívánt értékkel ügyféltanúsítvány ujjlenyomata ellenőrizheti. Ellenőrizheti az ujjlenyomatot, és az API Management feltöltött meglévő tanúsítványok ellen is.  

További információ a háttér-szolgáltatáshoz (azaz, API Management háttér-) ügyfél-tanúsítványok használata API-hozzáférés biztonságossá tétele: [ügyfél háttérszolgáltatások biztonságossá Tanúsítványalapú hitelesítés](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="checking-the-expiration-date"></a>A lejárati dátum ellenőrzése

Szabályzatok alábbi beállítható úgy, hogy ellenőrizze, hogy ha a tanúsítvány lejárt:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>A kibocsátó és tulajdonos ellenőrzése

Szabályzatok alábbi beállítható úgy, hogy ellenőrizze a kibocsátó és a egy ügyfél-tanúsítvány tulajdonosának meg:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != 'trusted-issuer' || context.Request.Certificate.SubjectName != 'expected-subject-name')" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Az ujjlenyomat ellenőrzése

Szabályzatok alábbi beállítható úgy, hogy ellenőrizze az ügyféltanúsítvány ujjlenyomata:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != 'desired-thumbprint')" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Az API Management feltöltött egy ujjlenyomatot tanúsítványokkal szemben ellenőrzése

Az alábbi példa bemutatja, hogyan ellenőrizheti és az API Management feltöltött tanúsítványok elleni ügyféltanúsítvány ujjlenyomata: 

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Következő lépés

*  [Hogyan teheti biztonságossá a háttérszolgáltatások ügyfél Tanúsítványalapú hitelesítés](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [Tanúsítványok feltöltése](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)

