---
title: "Biztonságos ügyfél API-k az API Management - Azure API Management tanúsítványhitelesítés |} Microsoft Docs"
description: "Megtudhatja, hogyan biztosíthat biztonságos hozzáférést a az ügyféltanúsítványok API-k"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 10cc1daf89212635243cbfe8f7b598a567d0f7c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Az API Management tanúsítványhitelesítés biztonságossá tétele a ügyfél API-k

API-kezelés lehetővé teszi a biztonságos hozzáférés a API-k (azaz ügyfél API-kezelés) ügyfél-tanúsítványok használatával. Jelenleg ellenőrizheti a kívánt értékkel ügyféltanúsítvány ujjlenyomata. Ellenőrizheti az ujjlenyomatot, az API Management feltöltött meglévő tanúsítványokkal szemben.  

További információ a háttér-szolgáltatás, az API-k (azaz API Management háttér-) ügyfél-tanúsítványok használatával biztonságossá tétele: [Tanúsítványalapú hitelesítés biztonságossá tétele a háttér-szolgáltatásaihoz ügyfél használatával](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)

## <a name="checking-the-expiration-date"></a>A lejárati dátum ellenőrzése

Alább házirendek beállítható úgy, hogy ellenőrizze, hogy ha a tanúsítvány lejárt-e:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>A kibocsátó és tulajdonos ellenőrzése

Alább házirendek beállítható úgy, hogy ellenőrizze a kibocsátó és egy ügyfél-tanúsítvány tulajdonosának:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>Az ujjlenyomat ellenőrzése

Alább házirendek beállítható úgy, hogy ellenőrizze az ügyféltanúsítvány ujjlenyomata:

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>A tanúsítványokkal szemben ujjlenyomat ellenőrzése fel van töltve az API Management

A következő példa bemutatja, hogyan API Management feltöltött tanúsítványokkal szemben ügyféltanúsítvány ujjlenyomata kereséséhez: 

```
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>Következő lépés

*  [Tanúsítványalapú hitelesítés biztonságossá tétele a háttér-szolgáltatásaihoz ügyfél használatával](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)
*  [Tanúsítványok feltöltéséről](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)

