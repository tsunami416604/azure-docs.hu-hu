---
title: Azure CDN a Microsoft Standard Rules Engine Match feltételekkel | Microsoft Docs
description: Az Azure Content Delivery Network dokumentációja a Microsoft Standard Rules Engine Matching feltételeiről.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: a72452d37b152a9463a5aee0e199fd42ea852236
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615961"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-match-conditions"></a>Azure CDN a Microsoft szabványos szabályainak motorjának egyeztetési feltételei

Ez a cikk részletes leírást nyújt az Azure Content Delivery Network (CDN) a Microsoft [Standard Rules Engine](cdn-standard-rules-engine.md)szolgáltatásban elérhető megfelelő feltételeiről.

A szabály első része az egyeztetési feltételek halmaza. Mindegyik szabály legfeljebb 4 egyezési feltételsel rendelkezhet. Az egyeztetési feltétel azokat a kérelmeket azonosítja, amelyekhez a szabályban meghatározott műveletek lesznek végrehajtva. Ha több egyeztetési feltételt használ, a rendszer a és a Logic együttes használatával csoportosítja őket.

Az egyeztetési feltételt például a következőre használhatja:

- Egy adott IP-címről vagy országból/régióból generált kérelmek szűrése.
- Kérelmek szűrése fejléc-információk alapján.
- A mobil-vagy asztali eszközökről érkező kérések szűrése.

## <a name="match-conditions"></a>Egyezési feltételek

A következő egyeztetési feltételek használhatók. 

### <a name="device-type"></a>Eszköz típusa 

Az eszköz típusa egyeztetési feltétel azonosítja a mobil vagy az asztali eszköz által a tulajdonságok alapján küldött kérelmeket.  

**Kötelező mezők**

Művelet | Támogatott érték
---------|----------------
Egyenlő, nem egyenlő | Mobil, asztali


### <a name="http-version"></a>HTTP-verzió

A HTTP-verzió egyeztetése feltétel azonosítja a kérések HTTP-verziója alapján megjelenő kérelmeket.

**Kötelező mezők**

Művelet | Támogatott érték
---------|----------------
Egyenlő, nem egyenlő | 2,0, 1,1, 1,0, 0,9, összes


### <a name="request-cookies"></a>Cookie-k kérése

A kérelem cookie-k egyeztetése feltétel azonosítja a kérelmeket a bejövő kérelemben található cookie-információk alapján.

**Kötelező mezők**

Cookie neve | Művelet | Cookie értéke | Eset átalakítása
------------|----------|--------------|---------------
Sztring | [Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

Legfontosabb információk
- A helyettesítő karakterek, beleértve a csillagokat (*), nem támogatottak a cookie-nevek megadásakor, csak a pontos cookie-nevek felelnek meg az összehasonlításhoz.
- Ennek a megfeleltetési feltételnek a példányain csak egyetlen cookie-nevet lehet megadni.
- A cookie-nevek összehasonlítása a kis-és nagybetűk megkülönböztetése nélkül történik.
- Több cookie-értéket is megadhat, ha mindegyiket egyetlen szóközzel kell korlátozni. 
- A cookie-értékek kihasználhatják a helyettesítő karakteres értékeket.
- Ha nem ad meg helyettesítő karaktert, csak a pontos egyezés felel meg ennek a megfelelési feltételnek. Például az "érték" megadásakor a "value" értéknek kell megegyeznie, de nem "érték1". 

### <a name="post-argument"></a>Bejegyzés argumentuma

**Kötelező mezők**

Argumentum neve | Művelet | Argumentum értéke | Eset átalakítása
--------------|----------|----------------|---------------
Sztring | [Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

### <a name="query-string"></a>Lekérdezési sztring

A lekérdezési karakterlánc egyeztetési feltételei a megadott lekérdezési karakterlánc paramétert tartalmazó kérelmeket azonosítják. Ez a paraméter olyan értékre van beállítva, amely megfelel egy megadott mintának. A kérelem URL-címében szereplő lekérdezési karakterlánc paraméterei (például paraméter = érték) határozzák meg, hogy ez a feltétel teljesül-e. Ez a megfeleltetési feltétel a lekérdezési karakterlánc paraméterét azonosítja a nevével, és egy vagy több értéket fogad el a paraméter értékeként.

**Kötelező mezők**

Művelet | Lekérdezési sztring | Eset átalakítása
---------|--------------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

### <a name="remote-address"></a>Távoli címek

A távoli cím megegyezik a kérelmező helye vagy IP-címe alapján a kérelmeket azonosító feltételekkel.

**Kötelező mezők**

Művelet | Támogatott értékek
---------|-----------------
Bármelyik | N/A
Földrajzi egyezés | Országkódok
IP-egyeztetés | IP-címek (szóközzel elválasztva)
Nem | N/A
Nem földrajzi egyezés | Országkódok
Nem IP-egyeztetés | IP-címek (szóközzel elválasztva)

Legfontosabb információk:

- CIDR-jelölés használata.
- Több IP-címet és/vagy IP-címtartományt is megadhat, ha mindegyiket egyetlen szóközzel kell korlátozni. Például:
  - **IPv4-példa**: a 1.2.3.4 10.20.30.40 a 1.2.3.4 vagy a 10.20.30.40 címről érkező összes kérésnek megfelel.
  - **IPv6-példa**: a 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 a 1:2:3:4:5:6:7:8 vagy 10:20:30:40:50:60:70:80 címről érkező kérelmekre illeszkedik.
- Az IP-címterület szintaxisa az alapszintű IP-cím, amelyet egy perjel és az előtag mérete követ. Például:
  - **IPv4-példa**: a 5.5.5.64/26 a 5.5.5.64-en keresztül a 5.5.5.127-on keresztül érkező kérésekre illeszkedik.
  - **IPv6-példa**: a 1:2:3:/48 a 1:2:3:0:0:0:0:0 – 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF címen megjelenő kérelmekre illeszkedik.

### <a name="request-body"></a>A kérelem törzse

**Kötelező mezők**

Művelet | A kérelem törzse | Eset átalakítása
---------|--------------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

### <a name="request-header"></a>Kérelem fejléce

**Kötelező mezők**
Fejléc neve | Művelet | Fejléc értéke | Eset átalakítása
------------|----------|--------------|---------------
Sztring | [Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

### <a name="request-method"></a>Kérelem metódusa

**Kötelező mezők**

Művelet | Támogatott érték
---------|----------------
Egyenlő, nem egyenlő | LETÖLTÉS, KÖZZÉTÉTEL, PUT, TÖRLÉS, FEJ, BEÁLLÍTÁSOK, NYOMKÖVETÉS

Legfontosabb információk:

- A CDN-ben csak a GET kérelem metódusa hozhatja fel a gyorsítótárazott tartalmat. Minden más kérelmezési módszer a hálózaton keresztül történik. 

### <a name="request-protocol"></a>Kérelem protokollja

**Kötelező mezők**

Művelet | Támogatott érték
---------|----------------
Egyenlő, nem egyenlő | HTTP, HTTPS

### <a name="request-url"></a>Kérés URL-címe

**Kötelező mezők**

Művelet | Kérés URL-címe | Eset átalakítása
---------|-------------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

Legfontosabb információk:

- A kérelem URL-címének beírásakor győződjön meg róla, hogy a protokoll adatait tartalmazza. Például: "https://www. [SajátTartomány]. com "

### <a name="url-file-extension"></a>URL-fájlkiterjesztés

**Kötelező mezők**

Művelet | Mellék | Eset átalakítása
---------|-----------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

Legfontosabb információk:

- A bővítmények esetében ne tartalmazzon bevezető időszakot; a. html helyett például HTML-t használjon.

### <a name="url-file-name"></a>URL-fájl neve

**Kötelező mezők**

Művelet | Fájlnév | Eset átalakítása
---------|-----------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

Legfontosabb információk:

- Több fájl nevének megadásához külön szóközzel válassza el az egyes fájlneveket. 

### <a name="url-path"></a>URL-cím

**Kötelező mezők**

Művelet | Érték | Eset átalakítása
---------|-------|---------------
[Szabványos operátorok listája](#standard-operator-list) | Karakterlánc, int | Nincs átalakítás, kis-és nagybetűk között

Legfontosabb információk:

- A fájlnév érték kihasználhatja a helyettesítő karakteres értékeket. Például az egyes fájlnevek mintázata egy vagy több csillagból (*) állhat, ahol minden csillag egy vagy több karakterből áll.

## <a name="reference-for-rules-engine-match-conditions"></a>A szabályok motorjának egyeztetési feltételei

### <a name="standard-operator-list"></a>Szabványos operátorok listája

A normál operátorok listáját tartalmazó szabályok esetében a következő operátorok érvényesek:

- Bármelyik
- Egyenlő 
- Contains 
- Kezdete 
- Végződik 
- Kisebb mint
- Kisebb vagy egyenlő
- Nagyobb, mint
- Nagyobb vagy egyenlő
- Nem
- Nem tartalmazza
- Nem kezdődik 
- Nem végződik 
- Nem kisebb, mint
- Nem kisebb vagy egyenlő
- Nem nagyobb, mint
- Nem nagyobb vagy egyenlő

A numerikus operátorok (például a "kevesebb mint" vagy "nagyobb vagy egyenlő") esetében a felhasznált összehasonlítás a hossz alapján történik. Ebben az esetben az egyeztetési feltételben szereplő értéknek egész számnak kell lennie, és meg kell egyeznie az összehasonlítani kívánt hosszával. 

---

[Vissza a tetejére](#match-conditions)

</br>

## <a name="next-steps"></a>További lépések

- [Az Azure Content Delivery Network áttekintése](cdn-overview.md)
- [Szabálymotor-referencia](cdn-standard-rules-engine-reference.md)
- [Szabályok Engine-műveletek](cdn-standard-rules-engine-actions.md)
- [HTTPS kikényszerítés a standard szabályok motor használatával](cdn-standard-rules-engine.md)
