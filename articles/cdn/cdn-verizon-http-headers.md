---
title: Verizon-specifikus HTTP-fejlécek Azure CDN szabálymotor |} Microsoft Docs
description: Ez a cikk ismerteti az Azure CDN szabálymotor Verizon-specifikus HTTP-fejlécek használata.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: v-deasim
ms.openlocfilehash: 1a4bb48efe2a91c85b773341bb38b0f3ce4c7d9b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517449"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Verizon-specifikus HTTP-fejlécek Azure CDN szabálymotor

A **verizon Azure CDN Premium** termékek, ha a HTTP-kérelem zajlik az eredeti kiszolgálóra a nyújtó jelenléti pontra (POP) kiszolgáló adhat hozzá egy vagy több fenntartott fejlécek (vagy proxy különleges fejlécekkel együtt) az ügyfél kérésében a POP-ra. Ezek a fejlécek olyan mellett az érkezett fejléc továbbítási szabvány. Standard kérelemfejléc kapcsolatos információkért lásd: [mezők kérelem](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields).

Ha meg szeretné akadályozni, hogy ezek fenntartott a fejlécek egyikét a (Content Delivery Network) Azure CDN POP-kérelem az eredeti kiszolgálóra való felvétel alatt, létre kell hoznia egy szabály a [Proxy különleges fejlécek szolgáltatás](cdn-rules-engine-reference-features.md#proxy-special-headers) a szabályok összetevőben. Ebben a szabályban kizárja a fejléc el szeretné távolítani a fejlécek mezőben fejlécek alapértelmezett listájáról. Ha engedélyezte a [Debug gyorsítótár válaszfejlécek szolgáltatás](cdn-rules-engine-reference-features.md#debug-cache-response-headers), vegye fel a szükséges `X-EC-Debug` fejléceket. 

Ahhoz például, hogy távolítsa el a `Via` fejléc, a szabály a fejlécek mező tartalmaznia kell az alábbi listán szereplő fejlécek: *X-továbbított- esetén az X-továbbított-protokoll, X-átjáró-lista X-állomás, X-Midgress, X-EK-neve, a gazdagép*. 

![Proxy különleges fejlécek szabály](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

A következő táblázat ismerteti a fejlécek, a Verizon CDN POP-ra a kérelemben szereplő hozzáadott:

Fejléc | Leírás | Példa
---------------|-------------|--------
[keresztül](#via-request-header) | Azonosítja a POP-kiszolgálót, amely küldése a proxyn keresztül a kérelem származási kiszolgálóhoz. | A HTTP/1.1 ECS (dca/1A2B)
X-továbbított-számára | Azt jelzi, hogy az igénylő IP-címet.| 10.10.10.10
X továbbított protokoll | Azt jelzi, hogy a kérelem protokoll. | http
X-állomás | Azt jelzi, hogy a kérésben szereplő állomásnév. | CDN.mydomain.com
X-Midgress | Azt jelzi, hogy a kérés küldése a proxyn keresztül további CDN kiszolgálón keresztül volt-e. Például egy kiszolgáló-forrás pajzs kiszolgáló POP vagy a POP-kiszolgáló ADN-átjárókiszolgáló. <br />Ezt a fejlécet bekerül a kérelem csak akkor, ha midgress forgalom kerül sor. Ebben az esetben a fejléc értéke 1 annak jelzésére, hogy a kérés küldése a proxyn keresztül további CDN kiszolgálón keresztül.| 1
[Gazdagép](#host-request-header) | A gazdagép és a port, előfordulhat, hogy hol található a kért tartalom azonosítja. | Marketing.mydomain.com:80
[X-átjáró – lista](#x-gateway-list-request-header) | ADN: Azonosítja egy ügyfél eredeti rendelt ADN átjárókiszolgálók feladatátvételi listája. <br />Forrás pajzs: rendelt egy ügyfél eredeti származási pajzs-kiszolgálók készlete jelzi. | `icn1,hhp1,hnd1`
X-EK -_&lt;neve&gt;_ | Kezdődő kérelemfejléc *X-EK* (például X-EK-címke, [X-EK-Debug](cdn-http-debug-headers.md)) a CDN foglalva.| waf éles

## <a name="via-request-header"></a>Kérelem fejléce
A formátum, amelyekkel a `Via` kérelem fejléc POP kiszolgálót azonosít meg a következő szintaxist:

`Via: Protocol from Platform (POP/ID)` 

A kifejezés, amely a szintaxist a következők:
- Protokoll: Azt jelzi, hogy a protokoll (például HTTP/1.1) verzióját használja proxyval a kérelmet. 

- Platform: Azt jelzi, hogy a platformot, amelyre a tartalmat kért. Ez a mező a következő kódokkal érvényesek: 
    Kód | Platform
    -----|---------
    ECAcc | Nagy HTTP
    ECS   | HTTP kicsi
    ECD   | Alkalmazásszolgáltatási hálózat (ADN)

- POP: Azt jelzi, a [POP](cdn-pop-abbreviations.md) , amely kezeli a kérelmet. 

- Azonosító: csak belső használatra.

### <a name="example-via-request-header"></a>Példa keresztül fejléc

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Állomás kérelem fejléce
A POP-kiszolgálókat a rendszer felülírja a `Host` fejléc mindkét alábbi feltétel teljesülése esetén:
- A kért tartalomhoz forrása egy ügyfél eredeti kiszolgálóra.
- A megfelelő felhasználói forrás HTTP állomásfejléc, nincs lehetőség üres.

A `Host` fejléc megfelelően a HTTP-állomásfejléc beállításban megadott értéket a rendszer felülírja.
Ha az ügyfél forrása HTTP állomásfejléc beállítás értéke üres, majd a `Host` a kérelmező által küldött kérelem fejléce a rendszer az ügyfél eredeti kiszolgálóra továbbítja.

## <a name="x-gateway-list-request-header"></a>X-átjáró-lista fejléc
A POP-kiszolgáló hozzáadása vagy felülírja az "X-átjáró-lista kérelem fejléce a következő feltételek bármelyikének teljesülésekor:
- A kérelem a ADN platform mutat.
- A kérelmet, amely az eredeti pajzs szolgáltatás által védett ügyfél eredeti kiszolgálóra továbbítja.

