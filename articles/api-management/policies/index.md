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
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: b8f97bb27b3eb45c731b20ad51e35fde9b6a394a
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126900"
---
# <a name="api-management-policy-samples"></a>API Management-szabályzatminták

A [szabályzat](../api-management-howto-policies.md) a rendszer egyik kiemelkedő funkciója, amely lehetővé teszi a közzétevő számára, hogy konfigurálással módosítsa az API viselkedését. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A következő táblázatban hivatkozásokat talál a mintákhoz, valamint az egyes minták rövid leírását.

|                                                                                                                                                                      |                                                                                                                                                                                                                             |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Bejövő szabályzatok**                                                                                                                                                 |                                                                                                                                                                                                                             |
| [Egy Forwarded fejléc hozzáadásával biztosíthatja, hogy a háttérrendszeri API megfelelő URL-címeket hozzon létre.](./set-header-to-enable-backend-to-construct-urls.md) | Bemutatja, hogyan adhat hozzá Forwarded fejlécet egy bejövő kéréshez, hogy a háttérrendszeri API megfelelő URL-címeket hozzon létre.                                                                                                        |
| [Korrelációs azonosítót tartalmazó fejléc hozzáadása](./add-correlation-id.md)                                                             | Bemutatja, hogyan adhat hozzá egy korrelációs azonosítót tartalmazó fejlécet a bejövő kéréshez.                                                                                                                                        |
| [Képességek hozzáadása egy háttérszolgáltatáshoz és a válasz gyorsítótárazása](./cache-response.md)                                             | Bemutatja, hogyan adhat hozzá képességeket egy háttérszolgáltatáshoz. Lehetővé teheti például, hogy egy időjárás-előrejelző API-ban a szélesség és hosszúság helyett elég legyen egy hely nevét megadni.                                                                    |
| [Hozzáférés engedélyezése JWT-jogcímek alapján](./authorize-request-based-on-jwt-claims.md)                                              | Bemutatja, hogyan engedélyezheti az egyes HTTP-metódusokhoz való hozzáférést egy API-n JWT-jogcímek alapján.                                                                                                                                       |
| [Kérelmek hitelesítése külső hitelesítő használatával](./authorize-request-using-external-authorizer.md)                                                   | Bemutatja, hogyan használható külső hitelesítő az API-hozzáférés biztosításához.                                                                                                                                                               |
| [Hozzáférés engedélyezése Google OAuth-jogkivonatokkal](./use-google-as-oauth-token-provider.md)                                            | Bemutatja, hogyan engedélyezheti a végpontokhoz való hozzáférést a Google OAuth jogkivonat-szolgáltatóként való használatával.                                                                                                                                    |
| [IP-címek szűrése Application Gateway használata esetén](./filter-ip-addresses-when-using-appgw.md) | Bemutatja, hogyan lehet IP-re szűrni a szabályzatokban, ha az API Management-példányt egy Application Gateway használatával érik el.
| [Közös hozzáférésű jogosultságkód létrehozása és kérések továbbítása az Azure-tárolóba.](./generate-shared-access-signature.md)                  | Bemutatja, hogyan hozhat létre [közös hozzáférésű jogosultságkódot](../../storage/storage-dotnet-shared-access-signature-part-1.md) kifejezések használatával, majd továbbíthatja a kérést az Azure-tárolóba a rewrite-uri szabályzattal. |
| [OAuth2 hozzáférési jogkivonat beszerzése az AAD-tól, és annak továbbítása a háttérrendszerbe](./use-oauth2-for-authorization.md)                             | Bemutatja, hogyan használhatja az OAuth2-t az átjáró és a háttérrendszer közötti engedélyezési folyamathoz. Bemutatja, hogyan szerezheti be a hozzáférési jogkivonatot az AAD-tól, majd továbbíthatja azt a háttérrendszernek.                                                    |
| [X-CSRF-jogkivonat beszerzése az SAP-átjárótól a kérésküldési szabályzat használatával](./get-x-csrf-token-from-sap-gateway.md)                           | Bemutatja, hogyan alkalmazhatja a számos API által használt X-CSRF-mintát. Ez a példa kifejezetten az SAP-átjáróra vonatkozik.                                                                                                                           |
| [Kérés átirányítása a törzs méretétől függően](./route-requests-based-on-size.md)                                            | Bemutatja, hogyan irányíthat át kéréseket a törzsük nagyságától függően.                                                                                                                                                       |
| [A kérés környezetére vonatkozó információk küldése a háttérszolgáltatásnak](./send-request-context-info-to-backend-service.md)                    | Bemutatja, hogyan küldhet el egyes, a kérés környezetére vonatkozó információkat a háttérszolgáltatásba naplózásra és feldolgozásra.                                                                                                                                |
| [Válaszgyorsítótár időtartamának beállítása](./set-cache-duration.md)                                                                          | Bemutatja, hogyan állíthatja be a válaszgyorsítótár időtartamát a háttérrendszer által küldött Cache-Control fejléc maxAge értékét használva.                                                                                                             |
| **Kimenő szabályzatok**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Válasz tartalmának szűrése](./filter-response-content.md)                                                                         | Bemutatja, hogyan szűrhet ki adatelemeket a válasz hasznos adatai közül a kéréshez kapcsolódó termék alapján.                                                                                                        |
| **Hiba esetén életbe lépő szabályzatok**                                                                                                                                                |                                                                                                                                                                                                                             |
| [Hibák naplózása a Stackifyban](./log-errors-to-stackify.md)                                                                           | Bemutatja, hogyan hozhat létre egy hibanaplózási szabályzatot, amely a hibákat a Stackifyba küldi naplózásra.                                                                                                                                            |
