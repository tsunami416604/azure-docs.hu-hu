---
title: "Az Azure API Management házirend minták |} Microsoft Docs"
description: "További tudnivalók a házirendek az Azure API Management használható."
services: api-management
documentationcenter: 
author: Juliako
manager: cflower
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: sample
ms.date: 10/31/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: e951eb47290a2d1b6f892f1ccf923f6ce0c1899c
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="api-management-policy-samples"></a>API-felügyeleti házirend minták

[Házirendek](api-management-howto-policies.md) vannak egy hatékony képesség, a rendszer, amely lehetővé teszi a közzétevőt úgy, hogy az API-t konfigurálással működésének módosításához. A házirendek utasítások gyűjteményei, amelyeket az API-k kérelmei és válaszai szerint egymást követően hajtanak végre. A következő táblázat minták mutató hivatkozásokat tartalmaz, és minden egyes minta rövid leírást.

|||
|---|---|
|**Bejövő házirendek**||
|[Engedélyezi a háttér-API megfelelő URL-címek összeállításához továbbított fejléc hozzáadása](./policies/set-header-to-enable-backend-to-construct-urls.md?toc=api-management/toc.json) |Bemutatja, hogyan kell a bejövő kérelem engedélyezi a háttér-API összeállítani a megfelelő URL-címeket adjon meg egy továbbított fejlécet.|
|[A korrelációs azonosító tartalmazó fejléc hozzáadása](./policies/add-correlation-id.md?toc=api-management/toc.json) |Bemutatja, hogyan kell hozzáadni a bejövő kérelem korrelációs Azonosítóra tartalmazó fejléc.|
|[-Képességeket adhat a háttérszolgáltatáshoz és gyorsítótárazza a választ](./policies/cache-response.md?toc=api-management/toc.json) |Bemutatja, hogyan-képességeket adhat a háttérszolgáltatáshoz. Például fogadja el a szélességi és hosszúsági a időjárási előrejelzés API helyett a hely nevét.|
|[Engedélyezi a hozzáférést a JWT jogcím alapján](./policies/authorize-request-based-on-jwt-claims.md?toc=api-management/toc.json) |Bemutatja, hogyan engedélyezze az API-k JWT jogcímei alapján meghatározott HTTP-metódus elérésére.|
|[Engedélyezi a hozzáférést a Google OAuth jogkivonat használatával](./policies/use-google-as-oauth-token-provider.md?toc=api-management/toc.json) |Bemutatja, hogyan a végpontokat, Google OAuth jogkivonat-szolgáltató használatával való hozzáférés engedélyezésére.|
|[Közös hozzáférésű Jogosultságkód és a kérés továbbítása az Azure storage létrehozása](./policies/generate-shared-access-signature.md?toc=api-management/toc.json) |Bemutatja, hogyan létrehozásához [közös hozzáférésű Jogosultságkód](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) kifejezésekkel, és továbbítja a kérelmet az Azure storage az átírást-uri-házirendet. |
|[Szerezze be az OAuth2-hozzáférési tokent az aad-ben, és továbbítja a háttér](./policies/use-oauth2-for-authorization.md?toc=api-management/toc.json) |Itt és az átjáró és a háttérkiszolgáló közötti hitelesítéshez OAuth2 használatának példája. Bemutatja, hogy az beszerzése olyan hozzáférési jogkivonatot az aad-ben, és továbbítja azt a háttérkiszolgálóra.|
|[A küldési kérelem házirenddel SAP-átjáróhoz beolvasása X-CSRF jogkivonat](./policies/get-x-csrf-token-from-sap-gateway.md?toc=api-management/toc.json) |Bemutatja, hogyan sok API-k által használt X-CSRF mintát végrehajtásához. Ebben a példában csak az SAP-átjáróhoz elő. |
|[A kérelem a törzs mérete alapján útvonal](./policies/route-requests-based-on-size.md?toc=api-management/toc.json) |Bemutatja, hogyan kell továbbítani a kérelmet a szervezetek mérete alapján.|
|[Kérelem környezeti információkat küldeni a háttérszolgáltatáshoz](./policies/send-request-context-info-to-backend-service.md?toc=api-management/toc.json) |Bemutatja, hogyan néhány környezeti információkat küldeni a háttérszolgáltatáshoz naplózás vagy feldolgozása.|
|[Állítsa be a válasz gyorsítótárazás időtartama](./policies/set-cache-duration.md?toc=api-management/toc.json) |Bemutatja, hogyan kell időtartamot válasz gyorsítótár maxAge értéke használatát a háttérrendszer által küldött Cache-Control-fejlécet.|
|**Kimenő házirendek**||
|[Válasz tartalom szűrése](./policies/filter-response-content.md?toc=api-management/toc.json) | Bemutatja, hogyan kell a válasz forgalma a kérelemhez társított termék alapuló elemeit adatok szűrésére.|
|**Hiba történt a házirendek**||
|[Hibák naplózása az Stackify](./policies/log-errors-to-stackify.md?toc=api-management/toc.json) |Bemutatja, hogyan hibák küldendő Stackify naplózási hiba naplózási házirend hozzáadása.|
