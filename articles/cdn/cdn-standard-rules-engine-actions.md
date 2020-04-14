---
title: Műveletek az Azure CDN szabványos szabálymotorjában | Microsoft dokumentumok
description: Az Azure Content Delivery Network (Azure CDN) szabványos szabálymotorjában végrehajtott műveletek referenciadokumentációja.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 29138b4fc6716ae5361cc4d7f97ceba41b90c2da
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259952"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Műveletek az Azure CDN szabványos szabálymotorjában

Az Azure Content Delivery Network (Azure CDN) [standard szabályok motorjában](cdn-standard-rules-engine.md) egy szabály egy vagy több egyezési feltételből és egy műveletből áll. Ez a cikk részletes leírást nyújt az Azure CDN standard szabálymotorjában használható műveletekről.

A szabály második része egy művelet. A művelet határozza meg a rendszer a kérelemtípusra alkalmazott viselkedést, amelyet egyezési feltétel vagy egyezési feltételek azonosítanak.

## <a name="actions"></a>Műveletek

A következő műveletek érhetők el az Azure CDN standard szabálymotorjában. 

### <a name="cache-expiration"></a>Gyorsítótár lejárata

Ezzel a művelettel felülírhatja a végpont élő (TTL) értékét a szabályok által megadott feltételeknek megfelelő kérelmek esetében.

#### <a name="required-fields"></a>Kötelező mezők

Gyorsítótár viselkedése |  Leírás              
---------------|----------------
Gyorsítótár megkerülése | Ha ez a beállítás be van jelölve, és a szabály megegyezik, a tartalom nem lesz gyorsítótárazva.
Felülbírálás | Ha ez a beállítás be van jelölve, és a szabály megegyezik, a forrásból visszaadott TTL-érték felülírja a műveletben megadott értéket.
Állítsa be, ha hiányzik | Ha ez a beállítás be van jelölve, és a szabály megegyezik, ha nem adott vissza TTL értéket az eredeti forrásból, a szabály a TTL értékét a műveletben megadott értékre állítja be.

#### <a name="additional-fields"></a>További mezők

Napok | Óra | Perc | Másodperc
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Gyorsítótárkulcs lekérdezési karakterlánca

Ezzel a művelettel a gyorsítótárkulcsot lekérdezési karakterláncok alapján módosíthatja.

#### <a name="required-fields"></a>Kötelező mezők

Viselkedés | Leírás
---------|------------
Belefoglalás | Ha ez a beállítás be van jelölve, és a szabály megegyezik, a paraméterekben megadott lekérdezési karakterláncok jelennek meg a gyorsítótárkulcs létrehozásakor. 
Minden egyedi URL gyorsítótárazása | Ha ez a beállítás be van jelölve, és a szabály megegyezik, minden egyedi URL-címnek saját gyorsítótárkulcsa van. 
Exclude | Ha ez a beállítás be van jelölve, és a szabály megegyezik, a paraméterekben megadott lekérdezési karakterláncok kilesznek zárva a gyorsítótárkulcs létrehozásakor.
Lekérdezési sztringek figyelmen kívül hagyása | Ha ez a beállítás be van jelölve, és a szabály megegyezik, a rendszer nem veszi figyelembe a lekérdezési karakterláncokat a gyorsítótárkulcs létrehozásakor. 

### <a name="modify-request-header"></a>Kérésfejléc módosítása

Ezzel a művelettel módosíthatja az eredeti eredetre küldött kérelmekben szereplő fejléceket.

#### <a name="required-fields"></a>Kötelező mezők

Műveletek | HTTP-fejléc neve | Érték
-------|------------------|------
Hozzáfűzés | Ha ez a beállítás be van jelölve, és a szabály megegyezik, a **Fejléc névben** megadott fejléc hozzáadódik a megadott értékkel rendelkező kéréshez. Ha a fejléc már jelen van, az érték hozzáfűzi a meglévő értéket. | Sztring
Felülírás | Ha ez a beállítás be van jelölve, és a szabály megegyezik, a **Fejléc névben** megadott fejléc hozzáadódik a megadott értékkel rendelkező kéréshez. Ha a fejléc már jelen van, a megadott érték felülírja a meglévő értéket. | Sztring
Törlés | Ha ez a beállítás be van jelölve, a szabály megegyezik, és a szabályban megadott fejléc jelen van, a fejléc törlődik a kérelemből. | Sztring

### <a name="modify-response-header"></a>Válaszfejléc módosítása

Ezzel a művelettel módosíthatja az ügyfeleknek visszaadott válaszokban található fejléceket.

#### <a name="required-fields"></a>Kötelező mezők

Műveletek | HTTP-fejléc neve | Érték
-------|------------------|------
Hozzáfűzés | Ha ez a beállítás be van jelölve, és a szabály megegyezik, a **fejléc névben** megadott fejléc a megadott **érték**kel egészül ki a válaszba. Ha a fejléc már jelen van, az **érték** hozzáfűzi a meglévő értéket. | Sztring
Felülírás | Ha ez a beállítás be van jelölve, és a szabály megegyezik, a **fejléc névben** megadott fejléc a megadott **érték**kel egészül ki a válaszba. Ha a fejléc már jelen van, az **Érték** felülírja a meglévő értéket. | Sztring
Törlés | Ha ez a beállítás be van jelölve, a szabály megegyezik, és a szabályban megadott fejléc jelen van, a fejléc törlődik a válaszból. | Sztring

### <a name="url-redirect"></a>URL-átirányítás

Ezzel a művelettel átirányíthatja az ügyfeleket egy új URL-címre. 

#### <a name="required-fields"></a>Kötelező mezők

Mező | Leírás 
------|------------
Típus | Válassza ki a válasz típusát, hogy visszatérjen a kérelmező: Found (302), Moved (301), Ideiglenes átirányítás (307) és állandó átirányítás (308).
Protocol (Protokoll) | Egyezési kérelem, HTTP, HTTPS.
Gazdanév | Válassza ki azt az állomásnevet, amelyhez a kérelmet át szeretné irányítani. Hagyja üresen a bejövő állomás megőrzéséhez.
Útvonal | Adja meg az átirányításban használandó elérési utat. Hagyja üresen a bejövő útvonal megőrzéséhez.  
Lekérdezési sztring | Adja meg az átirányításban használt lekérdezési karakterláncot. Hagyja üresen a bejövő lekérdezési karakterlánc megőrzéséhez. 
Töredék | Adja meg az átirányításban használandó töredéket. A bejövő töredék megőrzéséhez hagyja üresen a programot. 

Javasoljuk, hogy abszolút URL-t használjon. Relatív URL-cím használatával előfordulhat, hogy az Azure CDN URL-címeket egy érvénytelen elérési útra. 

### <a name="url-rewrite"></a>URL-átírás

Ezzel a művelettel átírhatja az eredete felé vezető úton lévő kérelem elérési útját.

#### <a name="required-fields"></a>Kötelező mezők

Mező | Leírás 
------|------------
Forrásminta | Adja meg a lecserélni nek megcserélni nek nevezett URL-elérési út forrásmintáját. Jelenleg a forrásminta előtag alapú egyezést használ. Az összes URL-útvonal egyeztetéséhez használjon perjelet (**/**) forrásminta-értékként.
Cél | Adja meg az újraírásban használandó célútvonalat. A célútvonal felülírja a forrásmintát.
Nem egyező görbe megőrzése | Ha **az Igen**beállítás van megállítva, a forrásminta hozzáfűzése után a fennmaradó elérési út az új célútvonalhoz. 

## <a name="next-steps"></a>További lépések

- [Az Azure CDN – áttekintés](cdn-overview.md)
- [A Standard szabálymotor referenciája](cdn-standard-rules-engine-reference.md)
- [Feltételek egyeztetése a Standard rules motorban](cdn-standard-rules-engine-match-conditions.md)
- [HTTPS kényszerítése a Standard szabálymotor használatával](cdn-standard-rules-engine.md)
