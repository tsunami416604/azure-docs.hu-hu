---
title: API-k biztonságossá tétele az API Management-Azure API Managementban az ügyféltanúsítvány-alapú hitelesítés használatával | Microsoft Docs
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
ms.openlocfilehash: 263f8495b9dbb0a1c5b3c54301b4b4deab425e31
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072363"
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
> A tanúsítvány-visszavonási lista használatának `context.Request.Certificate.VerifyNoRevocation()` ellenőrzésének `context.Request.Certificate.Verify()`letiltása a helyett.
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) hitelesítésszolgáltatói tanúsítvány (oka) [](api-management-howto-ca-certificates.md) t fel kell tölteni `context.Request.Certificate.Verify()` a `context.Request.Certificate.VerifyNoRevocation()` API Managementre és a működésre.

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
> A tanúsítvány-visszavonási lista használatának `context.Request.Certificate.VerifyNoRevocation()` ellenőrzésének `context.Request.Certificate.Verify()`letiltása a helyett.
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) hitelesítésszolgáltatói tanúsítvány (oka) [](api-management-howto-ca-certificates.md) t fel kell tölteni `context.Request.Certificate.Verify()` a `context.Request.Certificate.VerifyNoRevocation()` API Managementre és a működésre.

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
> A tanúsítvány-visszavonási lista használatának `context.Request.Certificate.VerifyNoRevocation()` ellenőrzésének `context.Request.Certificate.Verify()`letiltása a helyett.
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) hitelesítésszolgáltatói tanúsítvány (oka) [](api-management-howto-ca-certificates.md) t fel kell tölteni `context.Request.Certificate.Verify()` a `context.Request.Certificate.VerifyNoRevocation()` API Managementre és a működésre.

> [!TIP]
> A jelen [cikkben](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) ismertetett ügyféltanúsítvány-alapú holtpont-probléma többféleképpen is megnyilvánulhat, például a kérések lefagyása `403 Forbidden` `context.Request.Certificate` után az időtúllépés után `null`a kérések állapotkódot eredményeznek. Ez a probléma általában `POST` `PUT` a tartalom hossza körülbelül 60KB vagy nagyobb.
> Ha meg szeretné akadályozni, hogy ez a probléma ne jelenjen meg, kapcsolja be az "ügyfél-tanúsítvány egyeztetése" beállítást az "egyéni tartományok" panelen az alábbi ábrán látható módon. Ez a funkció nem érhető el a felhasználási szinten.

![Ügyféltanúsítvány egyeztetése](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>További lépések

-   [Háttérbeli szolgáltatások biztonságossá tétele ügyféltanúsítvány-alapú hitelesítés használatával](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Tanúsítványok feltöltése](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
