---
title: Az Azure CDN szabálymotor X-EK-Debug HTTP-fejlécek |} Microsoft Docs
description: Az X-EK-Debug hibakeresési gyorsítótár kérelem fejléce a gyorsítótár-házirend hatálya alá a kért objektum további információkat tartalmaz. Ezek a fejlécek Verizon vonatkoznak.
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
ms.date: 04/12/2018
ms.author: v-deasim
ms.openlocfilehash: 3a99e322d81748c54585e7dd0eb06959bfeb9569
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517435"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Az Azure CDN szabálymotor X-EK-Debug HTTP-fejlécek
A hibakeresési gyorsítótár kérelemfejlécet `X-EC-Debug`, a gyorsítótár-házirend hatálya alá a kért objektum további információkkal szolgál. Ezek a fejlécek csak az adott **verizon Azure CDN Premium** termékek.

## <a name="usage"></a>Használat
A felhasználó a POP-kiszolgálók által küldött válasz tartalmazza a `X-EC-Debug` fejléc csak a következő feltételek teljesülése esetén:

- A [Debug gyorsítótár válaszfejlécek szolgáltatás](cdn-rules-engine-reference-features.md#debug-cache-response-headers) engedélyezve van a megadott kérelem szabályok motoron.
- A megadott kérelem a debug gyorsítótár válaszfejlécek a válaszban szereplő csoportját határozza meg.

## <a name="requesting-debug-cache-information"></a>A kért hibakeresési információk gyorsítótárazása
A megadott kérelem a következő irányelvek segítségével határozza meg a hibakeresési információk gyorsítótárazása a válaszban szereplő:

Fejléc | Leírás |
---------------|-------------|
X-EK-Debug: x-EK-gyorsítótár | [Gyorsítótár-állapotkód:](#cache-status-code-information)
X-EK-Debug: x-EK-gyorsítótár-távoli | [Gyorsítótár-állapotkód:](#cache-status-code-information)
X-EK-Debug: x-EK-ellenőrzés-kérelmeznék | [Kérelmeznék](#cacheable-response-header)
X-EK-Debug: x-EK-gyorsítótár-kulcs | [Gyorsítótár-kulcs](#cache-key-response-header)
X-EK-Debug: x-EK-gyorsítótár-állapot | [Gyorsítótár állapota](#cache-state-response-header)

### <a name="syntax"></a>Szintaxis

Hibakeresési gyorsítótári választ fejlécek kérheti a a kérelem a következő fejléc és a megadott irányelveket is beleértve:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>X-EK-Debug minta fejléc

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Gyorsítótár állapotinformáció-kód
Az X-EK-Debug válaszfejléc azonosíthatja a kiszolgáló és a válasz a következő irányelvek keresztül kezelésének módját:

Fejléc | Leírás
-------|------------
X-EK-Debug: x-EK-gyorsítótár | Ezt a fejlécet az elvártnak, amikor tartalmat továbbít a CDN-t. A kiszolgáló a kérelmet teljesítő webhelypéldány POP azonosítja.
X-EK-Debug: x-EK-gyorsítótár-távoli | Ezt a fejlécet Igen csak akkor, ha a kért tartalom egy eredeti pajzs kiszolgálóra vagy egy ADN átjárókiszolgáló gyorsítótárazva lett.

### <a name="response-header-format"></a>A válasz fejléc formátuma

Az X-EK-Debug fejléc jelentések kód állapotinformáció gyorsítótár a következő formátumban:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

A kifejezés, amely a fenti válasz fejléce szintaxist a következők:
- StatusCode: Azt jelzi, hogyan történt a kért tartalom kezeli a CDN, amelyen keresztül a gyorsítótár állapotkód képviseli.
    
    TCP_DENIED állapotkód helyett nincs jelenthető, amikor jogosulatlan kérelmet jogkivonat-alapú hitelesítés miatt megtagadva. A NONE állapotkód azonban továbbra is használható, ha a gyorsítótár állapotjelentések vagy nyers naplóadatok megtekintéséhez.

- Platform: Azt jelzi, hogy a platformot, amelyre a tartalmat kért. Ez a mező a következő kódokkal érvényesek:

    Kód  | Platform
    ------| --------
    ECAcc | Nagy HTTP
    ECS   | HTTP kicsi
    ECD   | Alkalmazásszolgáltatási hálózat (ADN)

- POP: Azt jelzi, a [POP](cdn-pop-abbreviations.md) , amely kezeli a kérelmet. 

### <a name="sample-response-headers"></a>A minta válaszfejlécek

A következő minta fejlécek információkkal gyorsítótár állapot kód kérelem számára:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Kérelmeznék válaszfejléc
A `X-EC-Debug: x-ec-check-cacheable` válaszfejléc azt jelzi, hogy a kért tartalom nem rendelkezik lett gyorsítótárba.

A válasz fejléce nem jelzi azt hogy gyorsítótárazás került sor. Ehelyett azt jelzi, hogy jogosult a gyorsítótárazás volt-e a kérelmet.

### <a name="response-header-format"></a>A válasz fejléc formátuma

A `X-EC-Debug` reporting, hogy a kérelem nem rendelkezik lett gyorsítótárba válaszfejléc a következő formátumban kell megadni:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

A fenti válasz fejléce szintaxis használt kifejezés a következők:

Érték  | Leírás
-------| --------
IGEN    | Azt jelzi, hogy a kért tartalom gyorsítótárazása jogosult.
NEM     | Azt jelzi, hogy a kért tartalom nem jogosult a gyorsítótárazáshoz. Ez az állapot a következő okok valamelyike okozhatja: <br /> -Ügyfél-konfigurációs: egy adott fiókjába konfigurációs előfordulhat, hogy a pop kiszolgálók a gyorsítótár egy eszköz. Például szabálymotor így előfordulhat, hogy az eszköz által a kérelmek jogosító áthidaló gyorsítótár funkció engedélyezése a gyorsítótárba.<br /> -Gyorsítótár válaszfejlécek: A kért eszköz Cache-Control és Expires fejléc megakadályozhatja a POP-kiszolgálók, gyorsítótárazás.
ISMERETLEN | Azt jelzi, hogy a kiszolgálók nem annak ellenőrzéséhez, hogy a kért eszköz kérelmeznék volt. Ez az állapot általában akkor fordul elő, amikor a jogkivonat-alapú hitelesítés miatt megtagadva a kérés.

### <a name="sample-response-header"></a>A minta válaszfejléc

A következő minta válaszfejléc azt jelzi, hogy a kért tartalom nem rendelkezik lett gyorsítótárba:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Gyorsítótár-kulcs válaszfejléc
A `X-EC-Debug: x-ec-cache-key` válaszfejléc jelzi a kért tartalomhoz társított fizikai gyorsítótár kulcs. A fizikai gyorsítótár kulcsának áll egy elérési utat, amely azonosítja az eszköz gyorsítótárazás céljára. Ez azt jelenti a kiszolgálók ellenőrzi, hogy egy eszköz elérési úttal megfelelően gyorsítótárazott verzió a gyorsítótár-kulcs által definiált konfigurációjának kialakításához.

A fizikai gyorsítótár-kulcs dupla perjellel kezdődik (/ /) lehet igényelni a tartalom (HTTP vagy HTTPS) protokoll követ. Ez a protokoll követi a relatív elérési út a kért eszközhöz, amely kezdődik-e a tartalom-hozzáférési pont (például _/000001/_).

Alapértelmezés szerint HTTP-platformok használatára van konfigurálva *standard-gyorsítótár*, ami azt jelenti, hogy lekérdezési karakterláncok figyelmen kívül hagyja a gyorsítótárazást. Az ilyen típusú konfigurációs megakadályozza, hogy a gyorsítótár-kulcs lekérdezési karakterlánc adatokat is beleértve.

A lekérdezési karakterlánc rögzíti a gyorsítótár-kulcsot, ha van kivonatoló megfelelőjével konvertálva, majd szúrja be a kért eszköz nevét és a fájl kiterjesztése között (például eszköz&lt;kivonat értéke&gt;.html).

### <a name="response-header-format"></a>A válasz fejléc formátuma

A `X-EC-Debug` válaszfejléc jelent fizikai gyorsítótárkulcshoz adatokat a következő formátumban:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>A minta válaszfejléc

A következő minta válaszfejléc azt jelzi, hogy a fizikai gyorsítótár-kulcsot a kért tartalomhoz:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Gyorsítótár állapot válaszfejléc
A `X-EC-Debug: x-ec-cache-state` válaszfejléc kérték időpontjában a kért tartalom gyorsítótár állapotát jelzi.

### <a name="response-header-format"></a>A válasz fejléc formátuma

A `X-EC-Debug` válaszfejléc jelentések gyorsítótár állapotadatait a következő formátumban:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

A kifejezés, amely a fenti válasz fejléce szintaxist a következők:

- MASeconds: A kért tartalom Cache-Control fejlécek által definiált jelzi (másodpercben) a maximális életkora.

- MATimePeriod: A maximális kora (Ez azt jelenti, hogy MASeconds) alakítja a nagyobb egység hozzávetőleges egyenértékű (például a nap). 

- UnixTime: Azt jelzi, a gyorsítótár időbélyeg a lekért tartalom Unix időben (más néven POSIX idő vagy a Unix epoch). A gyorsítótár időbélyeg azt jelzi, hogy a kezdő dátumot és időt, amelyből az eszköz TTL számítjuk fel. 

    Ha a forráskiszolgáló nem használja a kiszolgáló, vagy ha a kiszolgáló nem ad vissza a kor válaszfejléc gyorsítótárazás külső HTTP, a gyorsítótár időbélyeg lesz a dátum/idő amikor az eszköz le vagy újra. Ellenkező esetben a POP-kiszolgálók a kor mező kiszámításához használja az eszköz TTL az alábbiak szerint: lekérés/RevalidateDateTime - kor.

- nnn, nn hhh éééé óó: pp: GMT: azt jelzi, a gyorsítótár időbélyeg a lekért tartalom. További információkért lásd: a fenti UnixTime kifejezés.

- CASeconds: A gyorsítótár időbélyeg óta eltelt másodpercek számát jelöli.

- RTSeconds: Azt jelzi, hogy a fennmaradó másodpercek számát, amelynek a gyorsítótárazott tartalom minősülnek friss. Ez az érték kiszámítása a következőképpen történik: RTSeconds = max-kor - gyorsítótár élettartamát.

- RTTimePeriod: Konvertálja az fennmaradó TTL értéket (Ez azt jelenti, hogy RTSeconds) nagyobb egység hozzávetőleges egyenértékű (például a nap).

- ExpiresSeconds: Perc múlva a megadott dátum/idő másodpercben jelzi a `Expires` válaszfejlécet. Ha a `Expires` válaszfejléc a válaszban nem szerepelt, akkor ez a kifejezés értéke *nincs*.

### <a name="sample-response-header"></a>A minta válaszfejléc

A következő minta válaszfejléc kérték időpontjában a lekért tartalom gyorsítótár állapotát jelzi:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

