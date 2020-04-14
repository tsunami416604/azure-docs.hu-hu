---
title: Az Azure CDN-gyorsítótárazási viselkedésének szabályozása gyorsítótárazási szabályokkal | Microsoft dokumentumok
description: A CDN-gyorsítótárazási szabályok segítségével beállíthatja vagy módosíthatja a gyorsítótár alapértelmezett lejárati viselkedését globálisan és feltételekkel, például URL-elérési úttal és fájlkiterjesztésekkel.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: allensu
ms.openlocfilehash: 874ec75fb9173b6cee50bf8880510464fa13e9d2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254240"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Az Azure CDN gyorsítótárazási viselkedésének vezérlése gyorsítótárszabályokkal

> [!NOTE] 
> A gyorsítótárazási szabályok csak a **Verizon azure CDN Standard és** az **Akamai-profilokból származó Azure CDN Standard esetén** érhetők el. A **Microsoft-profilokból származó Azure CDN** esetében a **Verizon-profilokból származó Azure CDN Premium** [standard szabálymotorját](cdn-standard-rules-engine-reference.md) kell használnia, hasonló funkciókhoz a [Verizon Premium-szabályok motorját](cdn-rules-engine.md) kell **használnia** a Kezelés portálon.
 
Az Azure Content Delivery Network (CDN) kétféleképpen szabályozhatja a fájlok gyorsítótárazásának módját: 

- Gyorsítótárazási szabályok: Ez a cikk azt ismerteti, hogyan használhatja a tartalomkézbesítési hálózat (CDN) gyorsítótárazási szabályait a gyorsítótár alapértelmezett lejárati viselkedésének globális és egyéni feltételekkel történő beállítására vagy módosítására, például URL-elérési úttal és fájlkiterjesztéssel. Az Azure CDN két gyorsítótárazási szabálytípust biztosít:

   - Globális gyorsítótárazási szabályok: Beállíthat egy globális gyorsítótárazási szabályt mindegyik végponthoz a profiljában, amely a végpontra küldött összes kérelmet érinti. A globális gyorsítótárazási szabály felülbírálja az összes HTTP-gyorsítótárazási irányelv fejlécét, ha be van állítva.

   - Egyéni gyorsítótárazási szabályok: Beállíthat egy vagy több globális gyorsítótárazási szabályt minden egyes végponthoz a profiljában. Az egyéni gyorsítótárazási szabályok meghatározott elérési utaknak és fájlkiterjesztéseknek felelnek meg, a feldolgozásuk sorrendben történik, és felülbírálják a globális gyorsítótárazási szabályt, ha az be van állítva. 

- Lekérdezési karakterlánc-gyorsítótárazás: Beállíthatja, hogy az Azure CDN hogyan kezeli a lekérdezési karakterláncokkal rendelkező kérelmek gyorsítótárazását. További információt az [Azure CDN-gyorsítótárazási viselkedése lekérdezési karakterláncokkal című témakörben talál.](cdn-query-string.md) Ha a fájl nem gyorsítótárazható, a lekérdezési karakterlánc gyorsítótárazási beállításának nincs hatása a gyorsítótárazási szabályok és a CDN alapértelmezett viselkedése alapján.

Az alapértelmezett gyorsítótárazási viselkedésről és a direktívafejlécekről a [Gyorsítótárazás működése című](cdn-how-caching-works.md)témakörben olvashat. 


## <a name="accessing-azure-cdn-caching-rules"></a>Az Azure CDN gyorsítótárazási szabályainak elérése

1. Nyissa meg az Azure Portalon, válasszon ki egy CDN-profilt, majd válasszon ki egy végpontot.

2. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

   ![CDN-gyorsítótárszabályok gomb](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Megjelenik a **Gyorsítótárszabályok** lap.

   ![CDN-gyorsítótárazási szabályok lap](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>A gyorsítótárazás viselkedési beállításai
Globális és egyéni gyorsítótárazási szabályok esetén a következő **gyorsítótárazási viselkedési** beállításokadhatók meg:

- **Gyorsítótár megkerülése**: Ne gyorsítótárazási és figyelmen kívül hagyd az origináltal biztosított gyorsítótár-irányelv fejléceit.

- **Felülbírálás**: Az origó által biztosított gyorsítótár időtartamának figyelmen kívül hagyása; használja a megadott gyorsítótár időtartamát. Ez nem bírálja felül a gyorsítótár-vezérlést: nincs gyorsítótár.

- **Állítsa be, ha hiányzik**: Honor által biztosított gyorsítótár-direktíva fejlécek, ha vannak ilyenek; ellenkező esetben használja a megadott gyorsítótár időtartamát.

![Globális gyorsítótárszabályok](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Egyéni gyorsítótárszabályok](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Gyorsítótár lejárati időtartama
Globális és egyéni gyorsítótárazási szabályok esetén megadhatja a gyorsítótár lejárati időtartamát napokban, órákban, percekben és másodpercekben:

- A **felülbírálás és** a **Beállítás beállítás esetén** a **gyorsítótárazás viselkedési** beállításai 0 másodperc és 366 nap között mozognak. 0 másodpercig a CDN gyorsítótárazza a tartalmat, de minden kérelmet újra kell érvényesítenie az eredeti kiszolgálóval.

- A **Gyorsítótár megkerülése** beállítás esetén a gyorsítótár időtartama automatikusan 0 másodpercre van állítva, és nem módosítható.

## <a name="custom-caching-rules-match-conditions"></a>Az egyéni gyorsítótárazási szabályok megfelelnek a feltételeknek

Egyéni gyorsítótár-szabályok esetén két egyezési feltétel áll rendelkezésre:
 
- **Elérési út**: Ez a feltétel megegyezik az URL elérési útjának,\*kivéve a tartománynevet, és támogatja a helyettesítő karakter szimbólumot ( ). Például _a /myfile.html_, _a /my/folder/*_ és _a /my/images/*.jpg_. A maximális hossz 260 karakter.

- **Kiterjesztés**: Ez a feltétel megegyezik a kért fájl kiterjesztésével. Megadhat egy listát a vesszővel tagolt fájlkiterjesztések, hogy megfeleljen. Például _.jpg_, _.mp3_vagy _.png_. A bővítmények maximális száma 50, a kiterjesztésenkéntleg legfeljebb 16 karakter lehet. 

## <a name="global-and-custom-rule-processing-order"></a>Globális és egyéni szabályfeldolgozási sorrend
A globális és egyéni gyorsítótárazási szabályok feldolgozása a következő sorrendben lesz:

- A globális gyorsítótárazási szabályok elsőbbséget élveznek az alapértelmezett CDN-gyorsítótárazási viselkedéssel szemben (HTTP cache-directive fejlécbeállítások). 

- Az egyéni gyorsítótárazási szabályok elsőbbséget élveznek a globális gyorsítótárazási szabályokkal szemben, ahol érvényesek. Az egyéni gyorsítótárazási szabályok feldolgozása fentről lefelé haladva lesz feldolgozva. Ez azt illeti, ha egy kérelem mindkét feltételnek megfelel, a lista alján lévő szabályok elsőbbséget élveznek a lista tetején lévő szabályokkal szemben. Ezért a konkrétabb szabályokat alacsonyabban kell elhelyeznie a listában.

**Példa**:
- Globális gyorsítótárazási szabály: 
   - Gyorsítótárazási viselkedés: **Felülbírálás**
   - Gyorsítótár lejárati ideje: 1 nap

- Egyéni gyorsítótárazási szabály #1:
   - Egyezési feltétel: **Elérési út**
   - Egyezési érték: _/home/*_
   - Gyorsítótárazási viselkedés: **Felülbírálás**
   - Gyorsítótár lejárati ideje: 2 nap

- Egyéni gyorsítótárazási szabály #2:
   - Egyezési feltétel: **Kiterjesztés**
   - Egyezési érték: _.html_
   - Gyorsítótárazási viselkedés: **Beállítás, ha hiányzik**
   - Gyorsítótár lejárati ideje: 3 nap

Ha ezek a szabályok be vannak állítva, a _ &lt;végpont állomásnév&gt;_.azureedge.net/home/index.html aktiválása egyéni gyorsítótárazási szabály #2, amely a következők: **Beállítás, ha hiányzik,** és 3 nap. Ezért ha az *index.html* `Expires` fájl http-fejléccel rendelkezik, `Cache-Control` a rendszer tiszteletben tartja őket; ellenkező esetben, ha ezek a fejlécek nincsenek beállítva, a fájl 3 napig gyorsítótárazódik.

> [!NOTE] 
> A szabály módosítása előtt gyorsítótárazott fájlok megőrzik az eredeti gyorsítótár időtartamának beállítását. A gyorsítótár időtartamának visszaállításához meg kell [tisztítania a fájlt](cdn-purge-endpoint.md). 
>
> Az Azure CDN konfigurációs módosításai eltarthat egy ideig a hálózaton keresztül iszporkolódás során: 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Verizon-profilokból származó Azure CDN Standard** esetében a propagálás általában 10 perc alatt befejeződik.  
>

## <a name="see-also"></a>Lásd még

- [A gyorsítótárazás működése](cdn-how-caching-works.md)
- [Oktatóanyag: Azure CDN gyorsítótárazási szabályainak beállítása](cdn-caching-rules-tutorial.md)
