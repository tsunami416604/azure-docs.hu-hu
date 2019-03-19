---
title: Verizon-specifikus HTTP-fejléceket az Azure CDN szabálymotorral |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure CDN szabálymotorral Verizon-specifikus HTTP-fejlécek használata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: 7ce845fb272cea1d621e8ccc18203e3a071e8c29
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57992010"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Az Azure CDN szabálymotorral verizon-specifikus HTTP-fejlécek

A **verizon Azure CDN Premium** termékek, amikor HTTP-kérést küld a forráskiszolgáló a jelenléti pontok (POP) kiszolgáló adhat hozzá egy vagy több fenntartott fejlécek (vagy a proxy speciális fejlécek) az ügyfél kérésében a jelenléti pontra irányíthatja. Ezek a fejlécek vannak a standard kapott fejlécek továbbítás mellett. Standard kérelemfejlécek kapcsolatos információkért lásd: [mezők kérelem](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Ha azt szeretné, hogy fenntartott fejléc ezek közül az Azure CDN (Content Delivery Network) POP kérelem hozzá lehet adni a forráskiszolgáló, létre kell hoznia egy szabályt az a [Proxy speciális fejlécek funkció](cdn-rules-engine-reference-features.md#proxy-special-headers) a szabályok motorban. Ez a szabály a kizárása a el kívánja távolítani az alapértelmezett listát a fejlécek mezőben fejlécek fejléc. Ha engedélyezte a [gyorsítótár válaszfejlécek hibakeresési funkció](cdn-rules-engine-reference-features.md#debug-cache-response-headers), adja hozzá a szükséges `X-EC-Debug` fejlécek. 

Például távolítsa el a `Via` fejléc, a szabály a fejlécek mezőt tartalmaznia kell az alábbi lista a fejlécek: *X-továbbított-, X-továbbított-protokoll, X-gazdagép, X-Midgress, X-átjáró-List, X-EK-neve, a gazdagép*. 

![A fejlécek speciális szabály](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

A következő táblázat ismerteti a fejlécek, a Verizon CDN összes jelenléti Pontjára, a kérelem által hozzáadott:

Kérelem fejléce | Leírás | Példa
---------------|-------------|--------
[Via](#via-request-header) | Azonosítja a POP-kiszolgálót, amely használ proxyt az eredeti kiszolgálóra irányuló kérelem. | HTTP/1.1 ECS (dca/1A2B)
X-továbbított-számára | Azt jelzi, hogy a kérelmező IP-címet.| 10.10.10.10
X-Forwarded-Proto | Azt jelzi, hogy a kérelem protokoll. | http
X-Host | Azt jelzi, hogy a kérésben szereplő állomásnév. | cdn.mydomain.com
X-Midgress | Jelzi, hogy a kérés küldése a proxyn keresztül további CDN kiszolgálón keresztül volt-e. Például a POP-kiszolgáló – forrás pajzs kiszolgáló vagy a jelenléti pontra Irányíthatja kiszolgáló ADN átjáró kiszolgálón. <br />Csak akkor, ha a forgalom midgress kerül sor a fejléc szerepel a kérelemben. Ebben az esetben a fejléc értéke 1 jelzi, hogy a kérés küldése a proxyn keresztül további CDN kiszolgálón keresztül volt-e.| 1
[Gazdagép](#host-request-header) | Azonosítja a gazdagép és a portot, előfordulhat, hogy hol található a kért tartalmat. | marketing.mydomain.com:80
[X-átjáró-List](#x-gateway-list-request-header) | ADN: Egy ügyfél kezdőpontja rendelt ADN átjárókiszolgálók feladatátvételi listája azonosítja. <br />Forrás pajzs: Azt jelzi, hogy egy ügyfél kezdőpontja rendelt pajzs szerverek készletét. | `icn1,hhp1,hnd1`
X-EC-_&lt;name&gt;_ | Kérelemfejlécek kezdődő *X-EK* (például X-EK-címke, [X-EK-Debug](cdn-http-debug-headers.md)) a CDN által használatra fenntartva.| waf-production

## <a name="via-request-header"></a>Kérelem fejléce keresztül
A formátumot, amelyen keresztül a `Via` kérelem fejléce POP kiszolgálót azonosít, nulláról indulva a következő szintaxist:

`Via: Protocol from Platform (POP/ID)` 

A kifejezések szintaxisát a következőképpen vannak definiálva:
- Protokoll: Azt jelzi, hogy a protokollt (például HTTP/1.1) verzióját használja a proxy a kérelmet. 

- Platform: Azt jelzi, hogy a platformot, amelyre a tartalmat kért. Ez a mező a következő kód érvényesek: 

    Kód | Platform
    -----|---------
    ECAcc | Nagy HTTP
    ECS   | HTTP kicsi
    ECD   | Alkalmazásszolgáltatási hálózat (ADN)

- POP: Azt jelzi, hogy a [POP](cdn-pop-abbreviations.md) , amely kezeli a kérelmet. 

- Azonosító: Csak belső használatra.

### <a name="example-via-request-header"></a>A példában-n keresztül a kérelem fejléce

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Gazdagép-kérelem fejléce
A POP-kiszolgálókat a rendszer felülírja a `Host` fejléc mindkét alábbi feltétel teljesülése esetén:
- A kért tartalmat a forrása egy ügyfél eredeti kiszolgálóra.
- A megfelelő ügyfél kezdőpontja HTTP-állomásfejlécet beállítás nem üres.

A `Host` kérelem fejléce felül lesznek írva, hogy a HTTP-állomásfejlécet beállításban megadott értéket.
Ha az ügyfél kezdőpontja HTTP-állomásfejlécet beállítás üres, majd a `Host` a kérelmező által küldött kérelem fejléce a rendszer az ügyfél eredeti kiszolgálóra továbbítja.

## <a name="x-gateway-list-request-header"></a>X-átjáró-List-kérelem fejléce
A POP-kiszolgáló hozzáadása/felülírja az "X-átjáró-List-kérelem fejléce a következő feltételek bármelyikének teljesülésekor:
- A kérelem a ADN platform mutat.
- Van egy, a forrás pajzs szolgáltatás által védett ügyfél forrás kiszolgálóra továbbítja a kérést.

