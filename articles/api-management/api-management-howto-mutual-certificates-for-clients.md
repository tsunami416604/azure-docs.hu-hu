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
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 5427c4050b6b70c18da7a1899d16e448c41e81c6
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427335"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Hogyan teheti biztonságossá az ügyfél API-k az API Management a Tanúsítványalapú hitelesítés

Az API Management lehetővé teszi, hogy a biztonságos hozzáférés az API-k (azaz, és az API Management ügyfél) ügyféltanúsítványok használatát. Bejövő tanúsítvány érvényesítéséhez, és ellenőrizze a tanúsítvány tulajdonságainak kívánt értékeket a házirend-kifejezések használatával szemben.

További információ a háttér-szolgáltatás API-ügyféltanúsítványok (azaz, API Management háttérrendszer) használatával való hozzáférés biztonságossá tétele: [ügyfél háttérszolgáltatások biztonságossá Tanúsítványalapú hitelesítés](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Értesítések fogadásához és a használat rétegében az ügyféltanúsítványokat először engedélyezni kell a "Kérelem ügyféltanúsítvány" beállítás az "Egyéni tartományok" panelen, ahogy az alábbi.

![Ügyfél-tanúsítvány igénylésének lépései](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>A kibocsátó és tulajdonos ellenőrzése

Szabályzatok alábbi beállítható úgy, hogy ellenőrizze a kibocsátó és a egy ügyfél-tanúsítvány tulajdonosának meg:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Ellenőrzési tanúsítványok visszavonási lista használatának letiltása `context.Request.Certificate.VerifyNoRevocation()` helyett `context.Request.Certificate.Verify()`.
> Ügyfél-tanúsítványa egy önaláírt, ha a legfelső szintű (vagy köztes) kell lennie a hitelesítésszolgáltató tanúsítványa(i) [feltöltött](api-management-howto-ca-certificates.md) és az API Management a `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` működjön.

## <a name="checking-the-thumbprint"></a>Az ujjlenyomat ellenőrzése

Szabályzatok alábbi beállítható úgy, hogy ellenőrizze az ügyféltanúsítvány ujjlenyomata:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Ellenőrzési tanúsítványok visszavonási lista használatának letiltása `context.Request.Certificate.VerifyNoRevocation()` helyett `context.Request.Certificate.Verify()`.
> Ügyfél-tanúsítványa egy önaláírt, ha a legfelső szintű (vagy köztes) kell lennie a hitelesítésszolgáltató tanúsítványa(i) [feltöltött](api-management-howto-ca-certificates.md) és az API Management a `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` működjön.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Az API Management feltöltött egy ujjlenyomatot tanúsítványokkal szemben ellenőrzése

Az alábbi példa bemutatja, hogyan ellenőrizheti és az API Management feltöltött tanúsítványok elleni ügyféltanúsítvány ujjlenyomata:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Ellenőrzési tanúsítványok visszavonási lista használatának letiltása `context.Request.Certificate.VerifyNoRevocation()` helyett `context.Request.Certificate.Verify()`.
> Ügyfél-tanúsítványa egy önaláírt, ha a legfelső szintű (vagy köztes) kell lennie a hitelesítésszolgáltató tanúsítványa(i) [feltöltött](api-management-howto-ca-certificates.md) és az API Management a `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` működjön.

> [!TIP]
> Ügyfél tanúsítvány holtpont hiba leírt [cikk](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) többféleképpen manifest magát, például kérelmek rögzítéséhez kérelem eredménye `403 Forbidden` után időtúllépés következik, állapotkód: `context.Request.Certificate` van `null`. Ezt a hibát általában észleli `POST` és `PUT` kérések a tartalom hossza körülbelül 60 KB-os vagy nagyobb.
> Elkerülése érdekében bekövetkezése probléma kapcsolja be az "Egyéni tartományok" panelen, ahogy az alábbi kívánt állomásnevek "Egyeztetés ügyféltanúsítvány" beállítást. Ez a funkció nem érhető el a használat rétegében.

![Ügyféltanúsítvány egyeztetése](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>További lépések

-   [Hogyan teheti biztonságossá a háttérszolgáltatások ügyfél Tanúsítványalapú hitelesítés](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Tanúsítványok feltöltése](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
