---
title: API-k biztonságossá tétele az API Management-ben az ügyféltanúsítvány-alapú hitelesítés használatával
titleSuffix: Azure API Management
description: Megtudhatja, hogyan védheti meg az API-kat az Ügyféltanúsítványok használatával. A bejövő tanúsítványok érvényesítéséhez házirend-kifejezéseket használhat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 4e5522c162e08f0257bd6f20b058bf8bb858cff3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099346"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API-k biztonságossá tétele ügyféltanúsítvány-alapú hitelesítéssel az API Managementben

A API Management az Ügyféltanúsítványok használatával biztonságossá teheti az API-khoz (például az ügyféltől a API Managementig) való hozzáférést. Érvényesítheti a bejövő tanúsítványokat, és ellenőrizheti a tanúsítvány tulajdonságait a kívánt értékekkel a házirend-kifejezések használatával.

További információ az API-k az Ügyféltanúsítványok használatával történő elérésének biztosításáról (azaz API Management a háttérrendszer számára): a [háttérbeli szolgáltatások biztonságossá tétele ügyféltanúsítvány-alapú hitelesítés használatával](./api-management-howto-mutual-certificates.md)

> [!IMPORTANT]
> Az ügyféltanúsítványok a fejlesztői, alapszintű, standard vagy prémium szinteken HTTP/2 protokollon keresztüli fogadásához és ellenőrzéséhez be kell kapcsolni az "egyéni tartományok" panel "ügyfél-tanúsítvány egyeztetése" beállítását az alábbi ábrán látható módon.

![Ügyféltanúsítvány egyeztetése](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Az ügyféltanúsítványok a használati szinten való fogadásához és ellenőrzéséhez be kell kapcsolni az "egyéni tartományok" panel "ügyfél-tanúsítvány kérése" beállítását az alábbi ábrán látható módon.

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
> A tanúsítvány-visszavonási lista használatának ellenőrzésének letiltása a `context.Request.Certificate.VerifyNoRevocation()` helyett `context.Request.Certificate.Verify()` .
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (oka) t fel kell [tölteni](api-management-howto-ca-certificates.md) a API Managementre `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` a működésre.

## <a name="checking-the-thumbprint"></a>Az ujjlenyomat ellenőrzése

Az alábbi házirendek konfigurálhatók az ügyféltanúsítvány ujjlenyomatának vizsgálatához:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> A tanúsítvány-visszavonási lista használatának ellenőrzésének letiltása a `context.Request.Certificate.VerifyNoRevocation()` helyett `context.Request.Certificate.Verify()` .
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (oka) t fel kell [tölteni](api-management-howto-ca-certificates.md) a API Managementre `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` a működésre.

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
> A tanúsítvány-visszavonási lista használatának ellenőrzésének letiltása a `context.Request.Certificate.VerifyNoRevocation()` helyett `context.Request.Certificate.Verify()` .
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány (oka) t fel kell [tölteni](api-management-howto-ca-certificates.md) a API Managementre `context.Request.Certificate.Verify()` és `context.Request.Certificate.VerifyNoRevocation()` a működésre.

> [!TIP]
> A jelen [cikkben](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) ismertetett ügyféltanúsítvány-alapú holtpont-probléma többféleképpen is megnyilvánulhat, például a kérések lefagyása után az időtúllépés után a kérések állapotkódot eredményeznek `403 Forbidden` `context.Request.Certificate` `null` . Ez a probléma általában `POST` a `PUT` tartalom hossza körülbelül 60KB vagy nagyobb.
> Ha meg szeretné akadályozni, hogy ez a probléma ne jelenjen meg, kapcsolja be az "ügyfél-tanúsítvány egyeztetése" beállítást a kívánt állomásnevek számára az "egyéni tartományok" panelen, ahogy az a jelen dokumentum első képében is látható. Ez a funkció nem érhető el a felhasználási szinten.


## <a name="next-steps"></a>Következő lépések

-   [Háttérbeli szolgáltatások biztonságossá tétele ügyféltanúsítvány-alapú hitelesítés használatával](./api-management-howto-mutual-certificates.md)
-   [Tanúsítványok feltöltése](./api-management-howto-mutual-certificates.md)
