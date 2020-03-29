---
title: X-EC-Debug HTTP fejlécek az Azure CDN-szabályok motorhoz | Microsoft dokumentumok
description: Az X-EC-Debug hibakeresési gyorsítótár kérelem fejléce további információkat tartalmaz a kért eszközre alkalmazott gyorsítótár-házirendről. Ezek a fejlécek a Verizonra jellemzőek.
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
ms.date: 04/12/2018
ms.author: magattus
ms.openlocfilehash: dec753d7c891d226aa2e6d3efa993d8d24adfbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593835"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>X-EC-Debug HTTP-fejlécek az Azure CDN-szabálymotorhoz
A hibakeresési gyorsítótár-kérelem fejléce , `X-EC-Debug`további információt nyújt a kért eszközre alkalmazott gyorsítótár-házirendről. Ezek a fejlécek a **Verizon-termékekből származó Azure CDN Premium szolgáltatásra** vonatkoznak.

## <a name="usage"></a>Használat
A POP-kiszolgálókról a felhasználónak `X-EC-Debug` küldött válasz csak akkor tartalmazza a fejlécet, ha a következő feltételek teljesülnek:

- A [Debug Cache Response Headers szolgáltatás](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers) engedélyezve van a szabálymotoron a megadott kérelemhez.
- A megadott kérelem határozza meg a hibakeresési gyorsítótár válaszfejléceinek készletét, amelyek szerepelni fognak a válaszban.

## <a name="requesting-debug-cache-information"></a>Hibakeresési gyorsítótár adatainak kérése
A megadott kérelemben a következő irányelvek segítségével határozhatja meg a válaszban szereplő hibakeresési gyorsítótár adatait:

Kérelem fejléce | Leírás |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Gyorsítótár állapotkódja](#cache-status-code-information)
X-EC-Debug: x-ec-cache-távoli | [Gyorsítótár állapotkódja](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Cacheable](#cacheable-response-header)
X-EC-Debug: x-ec-cache-kulcs | [Gyorsítótár-kulcs](#cache-key-response-header)
X-EC-Debug: x-ec-cache-állapot | [Gyorsítótár állapota](#cache-state-response-header)

### <a name="syntax"></a>Szintaxis

A hibakeresési gyorsítótár válaszfejlécei a következő fejléc és a megadott direktívák kéréssel kérhetők a kérelemben:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Minta X-EC-Debug fejléc

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Gyorsítótár állapotkódjának adatai
Az X-EC-Debug válaszfejléc a következő irányelvek alapján képes azonosítani a kiszolgálót és azt, hogyan kezelte a választ:

Fejléc | Leírás
-------|------------
X-EC-Debug: x-ec-cache | Ez a fejléc minden alkalommal megjelenik, amikor a tartalom a CDN-en keresztül kerül átirányítva. Azonosítja a kérelmet teljesítő POP-kiszolgálót.
X-EC-Debug: x-ec-cache-távoli | Ez a fejléc csak akkor jelenik meg, ha a kért tartalom gyorsítótárazása egy forráspajzs-kiszolgálón vagy egy ADN-átjárókiszolgálón történt.

### <a name="response-header-format"></a>Válaszfejléc formátuma

Az X-EC-Debug fejléc a gyorsítótár állapotkódjának adatait a következő formátumban jelenti:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

A fenti válaszfejléc szintaxisában használt kifejezések a következők:
- StatusCode: Azt jelzi, hogy a kért tartalmat hogyan kezelte a CDN, amelyet egy gyorsítótár állapotkódja képvisel.
    
    A TCP_DENIED állapotkód a NONE helyett is jelenthető, ha a tokenalapú hitelesítés miatt jogosulatlan kérést megtagadnak. A NONE állapotkód azonban továbbra is használatos a gyorsítótár állapotjelentésének vagy a nyers naplóadatoknak a megtekintésekor.

- Platform: Azt a platformot jelzi, amelyen a tartalmat kérték. A következő kódok érvényesek erre a mezőre:

    Kód  | Platform
    ------| --------
    ECAcc | HTTP Nagy
    Ecs   | HTTP Kicsi
    Ecd   | Alkalmazáskézbesítési hálózat (ADN)

- POP: Azt a [POP-ot](cdn-pop-abbreviations.md) jelzi, amely a kérést kezelte. 

### <a name="sample-response-headers"></a>Mintaválasz fejlécei

A következő mintafejlécek gyorsítótár-állapotkód-információkat nyújtanak egy kérelemhez:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Gyorsítótárazható válaszfejléc
A `X-EC-Debug: x-ec-check-cacheable` válaszfejléc azt jelzi, hogy a kért tartalom gyorsítótárazható-e.

Ez a válaszfejléc nem jelzi, hogy a gyorsítótárazás megtörtént-e. Inkább azt jelzi, hogy a kérelem jogosult volt-e a gyorsítótárazásra.

### <a name="response-header-format"></a>Válaszfejléc formátuma

A `X-EC-Debug` válaszfejléc, amely azt jelzi, hogy egy kérés gyorsítótárazható-e, a következő formátumú:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

A fenti válaszfejléc szintaxisában használt kifejezés a következőképpen határozható meg:

Érték  | Leírás
-------| --------
IGEN    | Azt jelzi, hogy a kért tartalom gyorsítótárazásra jogosult volt.
NO     | Azt jelzi, hogy a kért tartalom nem volt jogosult gyorsítótárazásra. Ennek az állapotnak az alábbi okai lehetnek: <br /> - Ügyfél-specifikus konfiguráció: A fiókra vonatkozó konfiguráció megakadályozhatja, hogy a pop szerverek gyorsítótárazjanak egy eszközt. A Rules Engine például megakadályozhatja egy eszköz gyorsítótárazását, ha engedélyezi a Gyorsítótár megkerülését funkció a minősítő kérelmekhez.<br /> - Cache Response Headers: A kért eszköz cache-control és lejár fejlécek megakadályozhatja a POP-kiszolgálók cache azt.
Ismeretlen | Azt jelzi, hogy a kiszolgálók nem tudták felmérni, hogy a kért eszköz gyorsítótárazható-e. Ez az állapot általában akkor fordul elő, ha a kérés jogkivonat-alapú hitelesítés miatt megtagadva.

### <a name="sample-response-header"></a>Mintaválasz fejléce

A következő mintaválaszfejléc azt jelzi, hogy a kért tartalom gyorsítótárazható-e:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Cache-Key válaszfejléc
A `X-EC-Debug: x-ec-cache-key` válaszfejléc a kért tartalomhoz társított fizikai gyorsítótár-kulcsot jelzi. A fizikai gyorsítótár-kulcs egy olyan útvonalból áll, amely gyorsítótárazás céljából azonosítja az eszközt. Más szóval a kiszolgálók a gyorsítótárazott eszközelérési útnak megfelelően ellenőrzik az eszköz gyorsítótárazott verzióját a gyorsítótár-kulcs a szerint.

Ez a fizikai gyorsítótár-kulcs egy dupla perjellel (//) kezdődik, amelyet a tartalom (HTTP vagy HTTPS) kéréséhez használt protokoll követ. Ezt a protokollt a kért eszköz relatív elérési útja követi, amely a tartalom-hozzáférési ponttal kezdődik (például _/000001/_).

Alapértelmezés szerint a *HTTP-platformok szabványos gyorsítótár használatára*vannak konfigurálva, ami azt jelenti, hogy a gyorsítótárazási mechanizmus figyelmen kívül hagyja a lekérdezési karakterláncokat. Ez a konfigurációtípus megakadályozza, hogy a gyorsítótár-kulcs lekérdezési karakterlánc-adatokat is beírjon.

Ha egy lekérdezési karakterláncot rögzít a gyorsítótár-kulcsban, akkor a rendszer a kivonatmegfelelőjéhez konvertálja, majd a&lt;kért eszköz&gt;neve és a fájlkiterjesztés (például az eszköz kivonatértéke .html) közé szúrja be.

### <a name="response-header-format"></a>Válaszfejléc formátuma

A `X-EC-Debug` válaszfejléc a következő formátumban jelenti a gyorsítótárkulcs fizikai adatait:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Mintaválasz fejléce

A következő mintaválaszfejléc a kért tartalom fizikai gyorsítótár-kulcsát jelzi:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Gyorsítótár állapotválasz fejléce
A `X-EC-Debug: x-ec-cache-state` válaszfejléc a kért tartalom gyorsítótárállapotát jelzi a kérés időpontjában.

### <a name="response-header-format"></a>Válaszfejléc formátuma

A `X-EC-Debug` válaszfejléc a gyorsítótár állapotadatait a következő formátumban jelenti:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

A fenti válaszfejléc szintaxisában használt kifejezések a következők:

- MASeconds: A kért tartalom Cache-Control fejlécei által meghatározott maximális életkort (másodpercben) jelzi.

- MATimePeriod: A maximális életkori értéket (azaz a MASeconds-t) egy nagyobb egység (például napok) közelítő egyenértékére konvertálja. 

- UnixTime: A kért tartalom gyorsítótár-időbélyegét jelzi Unix-idő (más néven POSIX idő vagy Unix korszak). A gyorsítótár időbélyege azt a kezdő dátumot/időpontot jelzi, amelyből az eszköz TTL-je kiszámításra kerül. 

    Ha az eredeti kiszolgáló nem használ egy külső gyártótól származó HTTP-gyorsítótárazási kiszolgálót, vagy ha a kiszolgáló nem adja vissza az Age válaszfejlécet, akkor a gyorsítótár időbélyegzője mindig az eszköz beolvasásának vagy érvényesítésének dátuma/időpontja lesz. Ellenkező esetben a POP-kiszolgálók az Age mezőt használják az eszköz TTL értékének kiszámításához a következőképpen: Beolvasás/RevalidateDateTime - Kor.

- ddd, dd MMM yyyy HH:mm:ss GMT: A kért tartalom gyorsítótárának időbélyegét jelzi. További információkért tekintse meg a fenti UnixTime kifejezést.

- CASeconds: A gyorsítótár időbélyege óta eltelt másodpercek számát jelzi.

- RTSeconds: Azt a másodpercszámot adja meg, amelyből a gyorsítótárazott tartalom frissnek minősül. Ez az érték a következőképpen kerül kiszámításra: RTSeconds = max-age - cache age.

- RTTimePeriod: A fennmaradó TTL-értéket (azaz RTSeconds-t) egy nagyobb egység (például napok) közelítő megfelelőjéhez konvertálja.

- ExpiresSeconds: A `Expires` válaszfejlécben megadott dátum/idő előtt hátralévő másodpercek számát jelzi. Ha `Expires` a válaszfejléc nem szerepel a válaszban, akkor a kifejezés értéke *nincs*.

### <a name="sample-response-header"></a>Mintaválasz fejléce

A következő mintaválaszfejléc a kért tartalom gyorsítótárállapotát jelzi a kérés időpontjában:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

