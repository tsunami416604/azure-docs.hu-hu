---
title: X-EK-Debug HTTP-fejlécek az Azure CDN szabálymotorral |} A Microsoft Docs
description: Az X-EK-Debug hibakeresési gyorsítótár kérelem fejléce a gyorsítótár-szabályzat, amely a kért objektum érvényes további információkat tartalmaz. Ezek a fejlécek Verizon jellemzőek.
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
ms.date: 04/12/2018
ms.author: magattus
ms.openlocfilehash: 4ba42850ee28e2e212d9bc2b7b64be103218757c
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094224"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Az Azure CDN szabálymotorral X-EK-Debug HTTP-fejlécek
A hibakeresési gyorsítótár kérelem fejlécét, `X-EC-Debug`, a gyorsítótár-szabályzat, amely a kért objektum érvényes további információkat tartalmaz. Ezek a fejlécek csak az adott **verizon Azure CDN Premium** termékek.

## <a name="usage"></a>Használat
A felhasználó a POP-kiszolgálók által küldött válasz tartalmazza a `X-EC-Debug` fejléc csak a következő feltételek teljesülése esetén:

- A [gyorsítótár válaszfejlécek hibakeresési funkció](cdn-rules-engine-reference-features.md#debug-cache-response-headers) engedélyezve van a rules engine, az adott kérelemhez.
- A megadott kérelem hibakeresési gyorsítótár válaszfejlécek, melyeket fog szerepelni a válasz határozza meg.

## <a name="requesting-debug-cache-information"></a>Hibakeresési információk gyorsítótárazása kérése
A hibakeresési gyorsítótár adatai, a válaszban szereplő használja a megadott kérés a következő irányelveket:

Kérelem fejléce | Leírás |
---------------|-------------|
X-EK-Debug: x-EK-gyorsítótár | [Gyorsítótár-állapotkód:](#cache-status-code-information)
X-EK-Debug: x-EK-gyorsítótár-távoli | [Gyorsítótár-állapotkód:](#cache-status-code-information)
X-EK-Debug: x-EK – jelölőnégyzet-gyorsítótárazható | [Gyorsítótárazható](#cacheable-response-header)
X-EK-Debug: x-EK-gyorsítótár-key | [Gyorsítótár-kulcs](#cache-key-response-header)
X-EK-Debug: x-EK-gyorsítótár-állapot | [Gyorsítótár állapota](#cache-state-response-header)

### <a name="syntax"></a>Szintaxis

Hibakeresési fejlécek kérheti többek között a következő fejlécére, és a megadott irányelveket a kérésben gyorsítótár választ:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>X-EK-Debug minta fejléc

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Kód gyorsítótár állapotadatait.
Az X-EK-Debug válaszfejléc azonosíthatja a kiszolgáló és a válasz az alábbi irányelvek keresztül kezelésének módját:

Fejléc | Leírás
-------|------------
X-EK-Debug: x-EK-gyorsítótár | Ezt a fejlécet, amikor tartalmat továbbít a CDN jelentett. A kiszolgáló a kérelmet teljesítő webhelypéldány POP azonosítja.
X-EK-Debug: x-EK-gyorsítótár-távoli | Ez a fejléc jelentett csak akkor, ha a kért tartalom gyorsítótárazva lett egy forrás pajzs vagy egy ADN-átjáró kiszolgálón.

### <a name="response-header-format"></a>A válasz fejléce formátuma

Az X-EK-Debug fejléc jelentések kód állapotinformációk gyorsítótár a következő formátumban:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

A fenti válasz fejléce szintaxis használt kifejezések meghatározása a következő:
- StatusCode: Azt jelzi, hogyan lett kezeli a lekért tartalom a CDN, amely a gyorsítótár állapotkódot keresztül jelöli.
    
    TCP_DENIED állapotkód jelenthető helyett nincs, amikor egy jogosulatlan kérelem jogkivonat-alapú hitelesítés miatt megtagadva. Gyorsítótár állapotjelentések vagy a nyers log megtekintett használható azonban továbbra is a NONE állapotkódot.

- Platform: Azt jelzi, hogy a platformot, amelyre a tartalmat kért. Ez a mező a következő kód érvényesek:

    Kód  | Platform
    ------| --------
    ECAcc | Nagy HTTP
    ECS   | HTTP kicsi
    ECD   | Alkalmazásszolgáltatási hálózat (ADN)

- POP: Azt jelzi, hogy a [POP](cdn-pop-abbreviations.md) , amely kezeli a kérelmet. 

### <a name="sample-response-headers"></a>Minta válaszfejlécek

A következő minta fejlécek adja meg a gyorsítótár állapotát kód adatait egy kérelem:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Gyorsítótárazható válaszfejléc
A `X-EC-Debug: x-ec-check-cacheable` válaszfejléc azt jelzi, hogy a kért tartalmat sikerült rendelkezik lett gyorsítótárba kerüljenek-e.

A válaszfejléc nem látható, hogy gyorsítótárazás került sor. Azt jelzi, hogy jogosult a gyorsítótárazás volt-e a kérés.

### <a name="response-header-format"></a>A válasz fejléce formátuma

A `X-EC-Debug` válaszfejléc reporting kérést sikerült rendelkezik lett gyorsítótárba kerüljenek-e a következő formátumban van:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

A fenti válasz fejléce szintaxisban használt kifejezés a következőképpen van meghatározva:

Érték  | Leírás
-------| --------
IGEN    | Azt jelzi, hogy jogosult a gyorsítótárazás volt-e a kért tartalmat.
NEM     | Azt jelzi, hogy a kért tartalom gyorsítótárazása nem lehet áttelepíteni. Ez az állapot a következő okok egyike miatt lehet: <br /> -Ügyfél-specifikus konfigurációs: egy adott fiókra konfigurációs megakadályozhatja a pop-kiszolgálók egy eszköz gyorsítótárazás. Például Szabálymodult megakadályozhatja az eszköz a Mellőzés gyorsítótárának a feltételeknek megfelelő kérelmek engedélyezésével a gyorsítótárba.<br /> -Gyorsítótár válaszfejlécek: A kért objektum Expires és a Cache-Control fejléceket megakadályozhatja a POP-kiszolgálókat, gyorsítótárazás.
ISMERETLEN | Azt jelzi, hogy sikerült-e a kiszolgálók annak ellenőrzéséhez, hogy a kért objektum lett gyorsítótárazható. Ez az állapot általában akkor fordul elő, amikor a jogkivonat-alapú hitelesítés miatt a kérelem megtagadva.

### <a name="sample-response-header"></a>Minta válaszfejléc

A következő minta válasz fejléce azt jelzi, hogy a kért tartalmat sikerült rendelkezik lett gyorsítótárba kerüljenek-e:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Gyorsítótár-kulcs válaszfejléc
A `X-EC-Debug: x-ec-cache-key` válaszfejléc azt jelzi, hogy a kért tartalomhoz társított fizikai gyorsítótár kulcs. Egy elérési utat, amely az eszköz azonosítja a gyorsítótárazás céljára áll egy fizikai gyorsítótár-kulcsot. Más szóval a kiszolgálók ellenőrzi, hogy egy eszköz, az elérési út alapján egy gyorsítótárazott verziója a gyorsítótár-kulcs által meghatározott módon.

A fizikai gyorsítótár-kulcs dupla perjellel kezdődik (/ /) követi a szabadságigények a tartalom (HTTP vagy HTTPS) protokollt. Ezt a protokollt az azt követő relatív elérési útját a kért eszközhöz, amely elindítja a tartalom-hozzáférési ponttal rendelkező (például _/000001/_).

Alapértelmezés szerint HTTP-platformok használatára vannak konfigurálva *standard-gyorsítótár*, ami azt jelenti, hogy lekérdezési karakterláncok figyelmen kívül hagyja a gyorsítótárazást. Ezt a konfigurációtípust megakadályozza, hogy a gyorsítótár-kulcs lekérdezési karakterlánc adatokat is beleértve.

Egy lekérdezési karakterláncot a gyorsítótár-kulcs van rögzítve, ha annak kivonatoló megfelelő alakítani, és majd a kért objektum nevét és a fájl kiterjesztése közötti beillesztett (például az eszköz&lt;kivonat értéke&gt;.html).

### <a name="response-header-format"></a>A válasz fejléce formátuma

A `X-EC-Debug` válaszfejléc jelentések fizikai gyorsítótár-key-információkat a következő formátumban:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Minta válaszfejléc

A következő minta válasz fejléce azt jelzi, hogy a fizikai gyorsítótár-kulcs a kért tartalomhoz:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Gyorsítótár állapot válaszfejléc
A `X-EC-Debug: x-ec-cache-state` válaszfejléc kérték időpontjában a lekért tartalom gyorsítótár állapotát jelzi.

### <a name="response-header-format"></a>A válasz fejléce formátuma

A `X-EC-Debug` válaszfejléc jelentések állapot információk gyorsítótárazása a következő formátumban:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

A fenti válasz fejléce szintaxis használt kifejezések meghatározása a következő:

- MASeconds: A max-age (másodpercben) azt jelzi, ahogyan a Cache-Control fejléc a kért tartalmat.

- MATimePeriod: (Például, nap) alakítja hozzávetőleges felel meg egy nagyobb egységet (azaz MASeconds) max-age értékét. 

- UnixTime: Azt jelzi, hogy a gyorsítótár időbélyeg a lekért tartalom a Unix-ideje (más néven) POSIX idő- vagy UNIX rendszerű alapidőpont). A gyorsítótár időbélyeg azt jelzi, hogy a kezdő dátumot és időt, amelyből egy eszköz TTL fog számítani. 

    Ha a forráskiszolgáló nem használja a kiszolgálón, vagy ha a kiszolgáló nem ad vissza az életkor válaszfejléc gyorsítótárazás külső HTTP, a gyorsítótár időbélyeg lesz a dátum/idő amikor az eszköz beolvasni vagy újra érvényesítve. Ellenkező esetben a POP-kiszolgálókat az életkor mező kiszámításához használja az eszköz TTL a következő: lekérés/RevalidateDateTime - kor.

- nnn, NN ÓÓ GMT yyyy. mmm: azt jelzi, hogy a gyorsítótár időbélyegzője a kért tartalmat. További információkért tekintse át a fenti UnixTime kifejezés.

- CASeconds: Azt jelzi, hogy a gyorsítótár időbélyeg óta eltelt másodpercek számát.

- RTSeconds: Másodperc van hátra, amelynek a gyorsítótárazott tartalom minősülnek friss számát jelzi. Ez az érték számítása a következőképpen: RTSeconds max-age - = gyorsítótár élettartama.

- RTTimePeriod: A fennmaradó TTL-érték (azaz RTSeconds) alakítja át egy nagyobb egység hozzávetőleges megfelelője (például, nap).

- ExpiresSeconds: Azt jelzi, hogy a másodpercben megadott dátum/idő perc múlva a `Expires` válaszfejléc. Ha a `Expires` válaszfejléc nem tartalmazza a választ, akkor ez a kifejezés értéke *none*.

### <a name="sample-response-header"></a>Minta válaszfejléc

A következő minta válaszfejléc kérték időpontjában a lekért tartalom gyorsítótár állapotát jelzi:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

