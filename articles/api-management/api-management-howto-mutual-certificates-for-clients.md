---
title: API-k biztonságossá tétele az API Management-ben az ügyféltanúsítvány-alapú hitelesítés használatával
titleSuffix: Azure API Management
description: Ismerje meg, hogyan védheti meg az API-kat az Ügyféltanúsítványok használatával
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 85eeaaa052604c3198ca2ab8988f9e7a77e2a63d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430654"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API-k biztonságossá tétele ügyféltanúsítvány-alapú hitelesítés használatával API Management

A API Management az Ügyféltanúsítványok használatával biztonságossá teheti az API-khoz (például az ügyféltől a API Managementig) való hozzáférést. Érvényesítheti a bejövő tanúsítványokat, és ellenőrizheti a tanúsítvány tulajdonságait a kívánt értékekkel a házirend-kifejezések használatával.

További információ az API-k az Ügyféltanúsítványok használatával történő elérésének biztosításáról (azaz API Management a háttérrendszer számára): a [háttérbeli szolgáltatások biztonságossá tétele ügyféltanúsítvány-alapú hitelesítés használatával](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

> [!IMPORTANT]
> Az ügyféltanúsítványok a használati szinten való fogadásához és ellenőrzéséhez először be kell kapcsolni az "ügyfél-tanúsítvány igénylése" beállítást az "egyéni tartományok" panelen az alábbi ábrán látható módon.

![Ügyféltanúsítvány kérése](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>A kiállító és a tárgy ellenőrzése

Az alábbi házirendek konfigurálhatók a kiállító és az ügyféltanúsítvány tárgyának vizsgálatához:

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
> A tanúsítvány-visszavonási lista ellenőrzésének letiltásához használja a `context.Request.Certificate.VerifyNoRevocation()` `context.Request.Certificate.Verify()`helyett.
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (oka) t fel kell [tölteni](api-management-howto-ca-certificates.md) API Managementre `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` a működéshez.

## <a name="checking-the-thumbprint"></a>Az ujjlenyomat ellenőrzése

Az alábbi házirendek konfigurálhatók az ügyféltanúsítvány ujjlenyomatának vizsgálatához:

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
> A tanúsítvány-visszavonási lista ellenőrzésének letiltásához használja a `context.Request.Certificate.VerifyNoRevocation()` `context.Request.Certificate.Verify()`helyett.
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (oka) t fel kell [tölteni](api-management-howto-ca-certificates.md) API Managementre `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` a működéshez.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Ujjlenyomat ellenőrzése a API Management feltöltött tanúsítványokkal szemben

Az alábbi példa bemutatja, hogyan ellenőrizhető az ügyféltanúsítvány ujjlenyomata a API Managementba feltöltött tanúsítványokkal szemben:

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
> A tanúsítvány-visszavonási lista ellenőrzésének letiltásához használja a `context.Request.Certificate.VerifyNoRevocation()` `context.Request.Certificate.Verify()`helyett.
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (oka) t fel kell [tölteni](api-management-howto-ca-certificates.md) API Managementre `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` a működéshez.

> [!TIP]
> A jelen [cikkben](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) ismertetett ügyféltanúsítvány-alapú holtpont-probléma többféleképpen is megnyilvánulhat, például a kérések befagyasztása után `403 Forbidden` állapotkódot eredményez, `context.Request.Certificate` `null`. Ez a probléma általában a `POST` és a `PUT` a tartalom hossza körülbelül 60KB vagy nagyobb.
> Ha meg szeretné akadályozni, hogy ez a probléma ne jelenjen meg, kapcsolja be az "ügyfél-tanúsítvány egyeztetése" beállítást az "egyéni tartományok" panelen az alábbi ábrán látható módon. Ez a funkció nem érhető el a felhasználási szinten.

![Ügyféltanúsítvány egyeztetése](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>Következő lépések

-   [Háttérbeli szolgáltatások biztonságossá tétele ügyféltanúsítvány-alapú hitelesítés használatával](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Tanúsítványok feltöltése](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
