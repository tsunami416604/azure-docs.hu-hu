---
title: Azure API Management-szabályzatminták | Microsoft Docs
description: Ismerkedés az Azure API Management szolgáltatásban elérhető szabályzatokkal.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 3f1d9a96888732e6221722102a687e09f37333c0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="api-management-policy-samples"></a>API Management-szabályzatminták

A [szabályzat](api-management-howto-policies.md) a rendszer egyik kiemelkedő funkciója, amely lehetővé teszi a közzétevő számára, hogy konfigurálással módosítsa az API viselkedését. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A következő táblázatban hivatkozásokat talál a mintákhoz, valamint az egyes minták rövid leírását.

|||
|---|---|
|**Bejövő szabályzatok**||
|[Egy Forwarded fejléc hozzáadásával biztosíthatja, hogy a háttérrendszeri API megfelelő URL-címeket hozzon létre.](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) |Bemutatja, hogyan adhat hozzá Forwarded fejlécet egy bejövő kéréshez, hogy a háttérrendszeri API megfelelő URL-címeket hozzon létre.|
|[Korrelációs azonosítót tartalmazó fejléc hozzáadása](./policies/add-correlation-id.md?toc=api-management/toc.json) |Bemutatja, hogyan adhat hozzá egy korrelációs azonosítót tartalmazó fejlécet a bejövő kéréshez.|
|[Képességek hozzáadása egy háttérszolgáltatáshoz és a válasz gyorsítótárazása](./policies/cache-response.md?toc=api-management/toc.json) |Bemutatja, hogyan adhat hozzá képességeket egy háttérszolgáltatáshoz. Lehetővé teheti például, hogy egy időjárás-előrejelző API-ban a szélesség és hosszúság helyett elég legyen egy hely nevét megadni.|
|[Hozzáférés engedélyezése JWT-jogcímek alapján](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json) |Bemutatja, hogyan engedélyezheti az egyes HTTP-metódusokhoz való hozzáférést egy API-n JWT-jogcímek alapján.|
|[Hozzáférés engedélyezése Google OAuth-jogkivonatokkal](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json) |Bemutatja, hogyan engedélyezheti a végpontokhoz való hozzáférést a Google OAuth jogkivonat-szolgáltatóként való használatával.|
|[Közös hozzáférésű jogosultságkód létrehozása és kérések továbbítása az Azure-tárolóba.](./policies/generate-shared-access-signature.md?toc=api-management/toc.json) |Bemutatja, hogyan hozhat létre [közös hozzáférésű jogosultságkódot](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) kifejezések használatával, majd továbbíthatja a kérést az Azure-tárolóba a rewrite-uri szabályzattal. |
|[OAuth2 hozzáférési jogkivonat beszerzése az AAD-tól, és annak továbbítása a háttérrendszerbe](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json) |Bemutatja, hogyan használhatja az OAuth2-t az átjáró és a háttérrendszer közötti engedélyezési folyamathoz. Bemutatja, hogyan szerezheti be a hozzáférési jogkivonatot az AAD-tól, majd továbbíthatja azt a háttérrendszernek.|
|[X-CSRF-jogkivonat beszerzése az SAP-átjárótól a kérésküldési szabályzat használatával](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json) |Bemutatja, hogyan alkalmazhatja a számos API által használt X-CSRF-mintát. Ez a példa kifejezetten az SAP-átjáróra vonatkozik. |
|[Kérés átirányítása a törzs méretétől függően](./policies/route-requests-based-on-size.md?toc=api-management/toc.json) |Bemutatja, hogyan irányíthat át kéréseket a törzsük nagyságától függően.|
|[A kérés környezetére vonatkozó információk küldése a háttérszolgáltatásnak](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json) |Bemutatja, hogyan küldhet el egyes, a kérés környezetére vonatkozó információkat a háttérszolgáltatásba naplózásra és feldolgozásra.|
|[Válaszgyorsítótár időtartamának beállítása](./policies/set-cache-duration.md?toc=api-management/toc.json) |Bemutatja, hogyan állíthatja be a válaszgyorsítótár időtartamát a háttérrendszer által küldött Cache-Control fejléc maxAge értékét használva.|
|**Kimenő szabályzatok**||
|[Válasz tartalmának szűrése](./policies/filter-response-content.md?toc=api-management/toc.json) | Bemutatja, hogyan szűrhet ki adatelemeket a válasz hasznos adatai közül a kéréshez kapcsolódó termék alapján.|
|**Hiba esetén életbe lépő szabályzatok**||
|[Hibák naplózása a Stackifyban](./policies/log-errors-to-stackify.md?toc=api-management/toc.json) |Bemutatja, hogyan hozhat létre egy hibanaplózási szabályzatot, amely a hibákat a Stackifyba küldi naplózásra.|
