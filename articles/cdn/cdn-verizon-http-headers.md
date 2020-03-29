---
title: Verizon-specifikus HTTP-fejlécek az Azure CDN-szabálymotorhoz | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan használhatja a Verizon-specifikus HTTP-fejlécek az Azure CDN-szabálymotor.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: magattus
ms.openlocfilehash: a5881bea578f2791f8dc0d6e760fd15c6f47e435
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593249"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Verizon-specifikus HTTP-fejlécek az Azure CDN-szabálymotorhoz

A **Verizon-termékekből származó Azure CDN Premium** esetén, amikor egy HTTP-kérelmet küld a forráskiszolgálónak, a POP-kiszolgáló hozzáadhat egy vagy több fenntartott fejlécet (vagy proxy speciális fejléceket) az ügyfélkérelemben a POP-hoz. Ezek a fejlécek a kapott szabványos továbbítási fejléceken kívül találhatók. A szabványos kérelemfejlécekről a [Kérelem mezők című témakörben](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields)talál.

Ha meg szeretné akadályozni, hogy az azure-cdn (content delivery network) POP-kérelemben az egyik ilyen fenntartott fejléc et hozzáadja az eredeti kiszolgálóhoz, létre kell hoznia egy szabályt a [Proxy speciális fejlécek szolgáltatással](cdn-verizon-premium-rules-engine-reference-features.md#proxy-special-headers) a szabálymotorban. Ebben a szabályban zárja ki az eltávolítani kívánt fejlécet a fejlécmező fejléceinek alapértelmezett listájából. Ha engedélyezte a [Hibakeresési gyorsítótár válaszfejlécek szolgáltatását,](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers)győződjön meg aszükséges `X-EC-Debug` fejlécek hozzáadásáról. 

A `Via` fejléc eltávolításához például a szabály fejlécmezőjének a következő fejléclistát kell tartalmaznia: *X-Forwarded-For, X-Forwarded-Proto, X-Host, X-Midgress, X-Gateway-List, X-EC-Name, Host*. 

![Proxy speciális fejlécek szabály](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

Az alábbi táblázat a Verizon CDN POP által a kérelemben hozzáadható fejléceket ismerteti:

Kérelem fejléce | Leírás | Példa
---------------|-------------|--------
[Via](#via-request-header) | Azonosítja azt a POP-kiszolgálót, amely a kérelmet egy forráskiszolgálóra kérte. | HTTP/1.1 ECS (dca/1A2B)
X-Forwarded-For | A kérelmező IP-címét jelzi.| 10.10.10.10
X-Forwarded-Proto | A kérelem protokollját jelzi. | http
X-host | A kérelem állomásnevét jelzi. | cdn.mydomain.com
X-Midgress között | Azt jelzi, hogy a kérés egy további CDN-kiszolgálón keresztül lett-e proxied. Például egy POP-kiszolgáló-származási pajzs kiszolgáló vagy egy POP-kiszolgáló-ADN átjárókiszolgáló. <br />Ez a fejléc csak akkor kerül a kérelembe, ha midgress forgalom történik. Ebben az esetben a fejléc 1-re van állítva, jelezve, hogy a kérelem egy további CDN-kiszolgálón keresztül lett proxied.| 1
[Állomás](#host-request-header) | Azonosítja azt a portot és a portot, ahol a kért tartalom megtalálható. | marketing.mydomain.com:80
[X-Gateway-lista](#x-gateway-list-request-header) | ADN: Az ügyfél eredetéhez rendelt ADN-átjárókiszolgálók feladatátvételi listáját azonosítja. <br />Eredetvédő: Az ügyfél eredetéhez rendelt eredeti pajzskiszolgálók készletét jelzi. | `icn1,hhp1,hnd1`
X-EK-_&lt;név&gt;_ | Az *X-EC-vel* kezdődő kérelmek fejlécei (például X-EC-Tag, [X-EC-Debug)](cdn-http-debug-headers.md)a CDN számára vannak fenntartva.| waf-termelés

## <a name="via-request-header"></a>Kérésfejlécen keresztül
A `Via` kérelemfejléc POP-kiszolgálót azonosító formátumát a következő szintaxis határozza meg:

`Via: Protocol from Platform (POP/ID)` 

A szintaxisban használt kifejezések a következők:
- Protokoll: A protokoll (például HTTP/1.1) verzióját jelzi, amely a kérelem proxyja. 

- Platform: Azt a platformot jelzi, amelyen a tartalmat kérték. A következő kódok érvényesek erre a mezőre: 

    Kód | Platform
    -----|---------
    ECAcc | HTTP Nagy
    Ecs   | HTTP Kicsi
    Ecd   | Alkalmazáskézbesítési hálózat (ADN)

- POP: Azt a [POP-ot](cdn-pop-abbreviations.md) jelzi, amely a kérést kezelte. 

- Azonosító: Csak belső használatra.

### <a name="example-via-request-header"></a>Példa a kérelem fejlécén keresztül

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Állomáskérelem fejléce
A POP-kiszolgálók `Host` felülírják a fejlécet, ha mindkét alábbi feltétel teljesül:
- A kért tartalom forrása egy ügyfél eredetű kiszolgálója.
- A megfelelő ügyféleredet a HTTP-állomásfejléc beállítás nem üres.

A `Host` kérelemfejléc felülleszírva, hogy tükrözze a HTTP-állomásfejléc beállításban megadott értéket.
Ha az ügyfél forrásának HTTP-állomásfejléc-beállítása üres, akkor a `Host` kérelmező által elküldött kérelemfejléc továbbításra kerül az ügyfél származási kiszolgálójára.

## <a name="x-gateway-list-request-header"></a>X-Gateway-lista kérelem fejléce
A POP-kiszolgáló az alábbi feltételek bármelyike teljesülése esetén hozzáadja/felülírja az "X-Gateway-list" kérelemfejlécet:
- A kérelem az ADN platformra mutat.
- A kérést a rendszer továbbítja egy ügyfél származási kiszolgálójára, amelyet az Origin Shield szolgáltatás véd.

