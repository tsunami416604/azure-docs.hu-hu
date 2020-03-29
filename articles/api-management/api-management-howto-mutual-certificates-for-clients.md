---
title: Biztonságos API-k az API-k hitelesítésével az API Managementben
titleSuffix: Azure API Management
description: Az API-k ügyféltanúsítványokkal való biztonságos hozzáférésének védelme
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
ms.openlocfilehash: 8c1d126f01580574a83850e63945aa7e513eaeda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76713149"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API-k biztonságossá tétele ügyféltanúsítvány-alapú hitelesítéssel az API Managementben

Az API Management lehetővé teszi az API-k (azaz ügyfél és API Management) ügyféltanúsítványok használatával történő biztonságos elérését. A házirend-kifejezések segítségével ellenőrizheti a bejövő tanúsítványokat, és ellenőrizheti a tanúsítványok tulajdonságait a kívánt értékekkel szemben.

Az API-k háttérszolgáltatásához való hozzáférés ügyféltanúsítványok (azaz az API-kezelés háttérrendszerhez) használatával történő biztosításáról a [Háttérszolgáltatások védelme az ügyféltanúsítvány-hitelesítés használatával című](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) témakörben található.

> [!IMPORTANT]
> A Fejlesztői, Alapszintű, Standard vagy Prémium szintű csomagokHTTP/2-es verzióján keresztüli ügyféltanúsítványok fogadásához és ellenőrzéséhez be kell kapcsolnia az "Ügyféltanúsítvány egyeztetése" beállítást az "Egyéni tartományok" panelen az alábbi módon.

![Ügyféltanúsítvány egyeztetése](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> A Felhasználás i. szintben lévő ügyféltanúsítványok fogadásához és ellenőrzéséhez be kell kapcsolnia az "Ügyféltanúsítvány kérése" beállítást az "Egyéni tartományok" panelen az alábbiak szerint.

![Ügyféltanúsítvány kérése](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>A kibocsátó és a tárgy ellenőrzése

Az alábbi házirendek beállíthatók úgy, hogy ellenőrizzék az ügyféltanúsítvány kibocsátóját és tulajdonosát:

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
> A tanúsítvány-visszavonási lista `context.Request.Certificate.VerifyNoRevocation()` használatának letiltása a `context.Request.Certificate.Verify()`helyett.
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) hitelesítésszolgáltatói `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` tanúsítvány(oka)t fel kell [tölteni az](api-management-howto-ca-certificates.md) API Management szolgáltatásba, és működni enem szükséges.

## <a name="checking-the-thumbprint"></a>Az ujjlenyomat ellenőrzése

Az alábbi házirendek beállíthatók az ügyféltanúsítvány ujjlenyomatának ellenőrzésére:

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
> A tanúsítvány-visszavonási lista `context.Request.Certificate.VerifyNoRevocation()` használatának letiltása a `context.Request.Certificate.Verify()`helyett.
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) hitelesítésszolgáltatói `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` tanúsítvány(oka)t fel kell [tölteni az](api-management-howto-ca-certificates.md) API Management szolgáltatásba, és működni enem szükséges.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Ujjlenyomat ellenőrzése az API Management szolgáltatásba feltöltött tanúsítványokkal szemben

A következő példa bemutatja, hogyan ellenőrizheti az ügyféltanúsítvány ujjlenyomatát az API Management szolgáltatásba feltöltött tanúsítványokkal szemben:

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
> A tanúsítvány-visszavonási lista `context.Request.Certificate.VerifyNoRevocation()` használatának letiltása a `context.Request.Certificate.Verify()`helyett.
> Ha az ügyféltanúsítvány önaláírt, a legfelső szintű (vagy köztes) hitelesítésszolgáltatói `context.Request.Certificate.Verify()` `context.Request.Certificate.VerifyNoRevocation()` tanúsítvány(oka)t fel kell [tölteni az](api-management-howto-ca-certificates.md) API Management szolgáltatásba, és működni enem szükséges.

> [!TIP]
> A [cikkben](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) ismertetett ügyféltanúsítvány-holtpont-probléma többféleképpen is megnyilvánulhat, például a kérelmek lefagyása, a kérelmek `403 Forbidden` állapotkódot eredményeznek az időtúllépés után, `context.Request.Certificate` a `null`. Ez a probléma `POST` `PUT` általában körülbelül 60 KB vagy annál nagyobb tartalomhosszúságú és -kérelmeket érint.
> A probléma előfordulásának megakadályozása érdekében kapcsolja be az "Ügyféltanúsítvány egyeztetése" beállítást a kívánt állomásnevekhez az "Egyéni tartományok" panelen az alábbi módon. Ez a funkció nem érhető el a Felhasználás i.

![Ügyféltanúsítvány egyeztetése](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>További lépések

-   [Háttérszolgáltatások védelme az ügyféltanúsítvány-hitelesítés használatával](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [Tanúsítványok feltöltése](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
