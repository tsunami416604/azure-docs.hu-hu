---
title: Feltételek egyeztetése az Azure CDN szabványos szabálymotorjában | Microsoft dokumentumok
description: Az Azure Content Delivery Network (Azure CDN) szabványos szabályok motorjában az egyezési feltételekre vonatkozó referenciadokumentáció.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 425266e2a7ca42bb17ca598ddfc2f2b86591f32e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900181"
---
# <a name="match-conditions-in-the-standard-rules-engine-for-azure-cdn"></a>Feltételek egyeztetése az Azure CDN szabványos szabálymotorjában

Az Azure Content Delivery Network (Azure CDN) [standard szabályok motorjában](cdn-standard-rules-engine.md) egy szabály egy vagy több egyezési feltételből és egy műveletből áll. Ez a cikk részletes leírást ad az Azure CDN szabványos szabálymotorjában használható egyezési feltételekről.

A szabály első része egy egyezési feltétel vagy egyezési feltételek halmaza. Az Azure CDN standard szabálymotorjában minden szabály legfeljebb négy egyezési feltételt kaphat. Az egyezési feltétel azonosítja azokat a kérelmeket, amelyekhez meghatározott műveleteket hajtanak végre. Ha több egyezési feltételt használ, az egyezési feltételek az AND logika használatával vannak csoportosítva.

Az egyezési feltétel például a következőkre használható:

- A kérelmeket egy adott IP-cím, ország vagy régió alapján szűrheti.
- A kérelmek et fejlécadatok szerint szűrheti.
- A mobileszközökről vagy asztali eszközökről érkező kérések szűrése.

## <a name="match-conditions"></a>Feltételek egyeztetése

A következő egyezési feltételek állnak rendelkezésre az Azure CDN standard szabálymotorjában. 

### <a name="device-type"></a>Eszköz típusa 

Azonosítja a mobileszközről vagy asztali eszközről érkező kérelmeket.  

#### <a name="required-fields"></a>Kötelező mezők

Művelet | Támogatott értékek
---------|----------------
Egyenlő, Nem egyenlő | Mobil, asztali

### <a name="http-version"></a>HTTP-verzió

A kérelmeket a kérelem HTTP-verziója alapján azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | Támogatott értékek
---------|----------------
Egyenlő, Nem egyenlő | 2.0, 1.1, 1.0, 0.9, minden

### <a name="request-cookies"></a>Cookie-k kérése

A bejövő kérelemben cookie-információk alapján azonosítja a kérelmeket.

#### <a name="required-fields"></a>Kötelező mezők

Cookie neve | Művelet | Cookie-érték | Eset átalakítása
------------|----------|--------------|---------------
Sztring | [Szabványos operátori lista](#standard-operator-list) | Karakterlánc, Int | Nincs átalakítás, nagybetűs, kisbetűs

#### <a name="key-information"></a>Legfontosabb információk

- A cookie nevének megadásakor nem használhat\*helyettesítő értékeket (beleértve a csillagokat ( ); pontos cookie-nevet kell használnia.
- Az egyezési feltétel példányanként csak egy cookie-nevet adhat meg.
- A cookie-k nevének összehasonlítása nem érzékeny a kis- és nagybetűkre.
- Több cookie-érték megadásához használjon egyetlen szóközt az egyes cookie-értékek között. 
- A cookie-értékek kihasználhatják a helyettesítő karakteres értékeket.
- Ha nincs megadva helyettesítő karakter, csak egy pontos egyezés felel meg ennek az egyezési feltételnek. Az "Érték" érték például az "Érték" értéknek felel meg, az "Érték1" értéknek nem. 

### <a name="post-argument"></a>Argumentum feladása

A kérelemben használt POST-kérelem metódushoz definiált argumentumok alapján azonosítja a kérelmeket. 

#### <a name="required-fields"></a>Kötelező mezők

Argumentum neve | Művelet | Argumentum értéke | Eset átalakítása
--------------|----------|----------------|---------------
Sztring | [Szabványos operátori lista](#standard-operator-list) | Karakterlánc, Int | Nincs átalakítás, nagybetűs, kisbetűs

### <a name="query-string"></a>Lekérdezési sztring

Azonosítja azokat a kérelmeket, amelyek egy adott lekérdezési karakterlánc-paramétert tartalmaznak. Ez a paraméter egy adott mintának megfelelő értékre van beállítva. A lekérdezési karakterlánc paraméterei (például **parameter=value**) a kérelem URL-címében határozzák meg, hogy teljesül-e ez a feltétel. Ez az egyezési feltétel a lekérdezési karakterlánc paraméterét a neve alapján azonosítja, és egy vagy több értéket fogad el a paraméterértékhez.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | Lekérdezési sztring | Kis- és nagybetűk átalakítása
---------|--------------|---------------
[Szabványos operátori lista](#standard-operator-list) | Karakterlánc, Int | Nincs átalakítás, nagybetűs, kisbetűs

### <a name="remote-address"></a>Távoli cím

A kérelmeket a kérelmező helye vagy IP-címe alapján azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | Támogatott értékek
---------|-----------------
Bármelyik | N/A
Geo egyezés | Országhívószám
IP-egyezés | IP-cím (szóközre bontva)
Nem olyan, | N/A
Nem Geo match | Országhívószám
Nem IP-egyezés | IP-cím (szóközre bontva)

#### <a name="key-information"></a>Legfontosabb információk

- Cidr jelölés használata.
- Több IP-cím és IP-címblokk megadásához használjon egyetlen szóközt az értékek között:
  - **IPv4 példa:** *1.2.3.4 10.20.30.40* megfelel minden olyan kérésnek, amely vagy az 1.2.3.4 vagy a 10.20.30.40 címről érkezik.
  - **IPv6 példa:** *1:2:3:4:5:6:7:8 10:20:30:40:50:50:70:80* megfelel minden olyan kérésnek, amely az 1:2:3:4:5:5:6:7:8 vagy a 10:20:30:40:50:60:70:80 címről érkezik.
- Az IP-címblokk szintaxisa az alap IP-cím, amelyet egy perjel és az előtag mérete követ. Példa:
  - **IPv4 példa:** *Az 5.5.5.64/26* minden olyan kérésnek megfelel, amely az 5.5.5.64 és 5.5.5.127 közötti címekről érkezik.
  - **IPv6 példa:** *1:2:3:/48* megfelel minden olyan kérésnek, amely az 1:2:3:0:0:0:0:0:0:0 és 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff:ffff.0:0:0:0:0:0;

### <a name="request-body"></a>A kérés törzse

A kérelmeket a kérelem törzsében megjelenő konkrét szöveg alapján azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | A kérés törzse | Eset átalakítása
---------|--------------|---------------
[Szabványos operátori lista](#standard-operator-list) | Karakterlánc, Int | Nincs átalakítás, nagybetűs, kisbetűs

### <a name="request-header"></a>Kérelem fejléce

Azonosítja azokat a kérelmeket, amelyek egy adott fejlécet használnak a kérelemben.

#### <a name="required-fields"></a>Kötelező mezők

Fejléc neve | Művelet | Fejléc értéke | Eset átalakítása
------------|----------|--------------|---------------
Sztring | [Szabványos operátori lista](#standard-operator-list) | Karakterlánc, Int | Nincs átalakítás, nagybetűs, kisbetűs

### <a name="request-method"></a>Kérelem módszere

Azonosítja a megadott kérelemmetódust használó kérelmeket.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | Támogatott értékek
---------|----------------
Egyenlő, Nem egyenlő | GET, POST, FEL, TÖRLÉS, FEJ, LEHETŐSÉGEK, NYOMKÖVETÉS

#### <a name="key-information"></a>Legfontosabb információk

- Csak a GET-kérelem metódus képes gyorsítótárazott tartalmat létrehozni az Azure CDN-ben. Az összes többi kérelemmetódus a hálózaton keresztül történik. 

### <a name="request-protocol"></a>Kérelem protokoll

A megadott protokollt használó kérelmeket azonosítja.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | Támogatott értékek
---------|----------------
Egyenlő, Nem egyenlő | HTTP, HTTPS

### <a name="request-url"></a>Kérés URL-címe

Azonosítja azokat a kérelmeket, amelyek megfelelnek a megadott URL-címnek.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | Kérés URL-címe | Eset átalakítása
---------|-------------|---------------
[Szabványos operátori lista](#standard-operator-list) | Karakterlánc, Int | Nincs átalakítás, nagybetűs, kisbetűs

#### <a name="key-information"></a>Legfontosabb információk

- Ha ezt a szabályfeltételt használja, győződjön meg arról, hogy tartalmazza a protokolladatait. Például: *https://www.\<yourdomain\>.com*.

### <a name="url-file-extension"></a>URL-fájlkiterjesztés

Azonosítja azokat a kérelmeket, amelyek a megadott fájlkiterjesztést tartalmazzák a kérelmező URL-címfájlnevében.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | Mellék | Eset átalakítása
---------|-----------|---------------
[Szabványos operátori lista](#standard-operator-list) | Karakterlánc, Int | Nincs átalakítás, nagybetűs, kisbetűs

#### <a name="key-information"></a>Legfontosabb információk

- A kiterjesztéshez ne adjon meg kezdő időszakot; például használja *html* helyett *.html*.

### <a name="url-file-name"></a>URL-fájl neve

Azonosítja azokat a kérelmeket, amelyek tartalmazzák a megadott fájlnevet a kérelmező URL-címben.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | Fájlnév | Eset átalakítása
---------|-----------|---------------
[Szabványos operátori lista](#standard-operator-list) | Karakterlánc, Int | Nincs átalakítás, nagybetűs, kisbetűs

#### <a name="key-information"></a>Legfontosabb információk

- Több fájlnév megadásához különítse el az egyes fájlneveket egyetlen szóközrel. 

### <a name="url-path"></a>URL elérési útja

Azonosítja azokat a kérelmeket, amelyek tartalmazzák a megadott elérési utat a kérelmező URL-címben.

#### <a name="required-fields"></a>Kötelező mezők

Művelet | Érték | Kis- és nagybetűk átalakítása
---------|-------|---------------
[Szabványos operátori lista](#standard-operator-list) | Karakterlánc, Int | Nincs átalakítás, nagybetűs, kisbetűs

#### <a name="key-information"></a>Legfontosabb információk

- A fájlnévértékek kihasználhatják a helyettesítő karakter értékeket. Minden fájlnévminta egy vagy több csillagból (*) állhat, ahol mindegyik csillag egy vagy több karakterből álló sorozatnak felel meg.

## <a name="reference-for-rules-engine-match-conditions"></a>Hivatkozás a szabályokhoz motor egyezési feltételek

### <a name="standard-operator-list"></a>Szabványos operátori lista

A szabványos operátori listából értékeket elfogadó szabályok esetében a következő operátorok érvényesek:

- Bármelyik
- Egyenlő 
- Contains 
- Kezdődik 
- Végződések 
- Kisebb mint
- Kisebb vagy egyenlő
- Nagyobb, mint
- Nagyobb vagy egyenlő
- Nem olyan, mint
- Nem tartalmaz
- Nem kezdődik 
- Nem végződik 
- Nem kevesebb, mint
- Legalább egyvagy egyenlő
- Nem nagyobb, mint
- Nem nagyobb vagy egyenlő

Az olyan numerikus operátorok esetében, mint *a Kisebb és* *nagyobb, mint vagy egyenlő,* az alkalmazott összehasonlítás a hosszon alapul. Ebben az esetben az egyezési feltétel értékének egész számnak kell lennie, amely megegyezik az összehasonlítani kívánt hosszal. 

## <a name="next-steps"></a>További lépések

- [Az Azure CDN – áttekintés](cdn-overview.md)
- [A Standard szabálymotor referenciája](cdn-standard-rules-engine-reference.md)
- [Műveletek a Standard szabálymotorban](cdn-standard-rules-engine-actions.md)
- [HTTPS kényszerítése a Standard szabálymotor használatával](cdn-standard-rules-engine.md)
