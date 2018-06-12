---
title: Szabályozhatja az Azure CDN szolgáltatás használata a szabályok gyorsítótárazással gyorsítótárazásának |} Microsoft Docs
description: Gyorsítótárazás szabályok CDN vagy módosíthatja a gyorsítótár lejárati alapértelmezés, globális és a feltételek, például az egy URL-elérési út és fájlnév-kiterjesztések használhatja.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: v-deasim
ms.openlocfilehash: 4095ed763de378a673908d033d87b2aa6d72f13c
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35260006"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Vezérlő Azure CDN szolgáltatás használata a szabályok gyorsítótárazással gyorsítótárazásának működése

> [!NOTE] 
> Gyorsítótárazás szabályok csak esetén érhetők el **Azure CDN Standard verizon** és **Azure CDN Standard Akamai** profilok. A **verizon Azure CDN Premium** -profilok kell használnia a [Azure CDN szabálymotor](cdn-rules-engine.md) a a **kezelése** hasonló funkciókat a portálon.
 
Az Azure Content Delivery Network (CDN) kétféleképpen szabályozhatja, hogy a gyorsítótárba: 

- Szabályok gyorsítótárazása: Ez a cikk ismerteti, hogyan használható tartalomkézbesítési hálózat (CDN) gyorsítótárazás szabályok beállítása vagy módosítása a gyorsítótár lejárati alapértelmezés globálisan és egyéni feltételeknek, például az egy URL-cím elérési út és fájlnév kiterjesztése. Az Azure CDN két gyorsítótárazási szabálytípust biztosít:

   - Globális gyorsítótárazási szabályok: Beállíthat egy globális gyorsítótárazási szabályt mindegyik végponthoz a profiljában, amely a végpontra küldött összes kérelmet érinti. A globális gyorsítótárazási szabály felülbírálja az összes HTTP-gyorsítótárazási irányelv fejlécét, ha be van állítva.

   - Egyéni gyorsítótárazási szabályok: Beállíthat egy vagy több globális gyorsítótárazási szabályt minden egyes végponthoz a profiljában. Az egyéni gyorsítótárazási szabályok meghatározott elérési utaknak és fájlkiterjesztéseknek felelnek meg, a feldolgozásuk sorrendben történik, és felülbírálják a globális gyorsítótárazási szabályt, ha az be van állítva. 

- Lekérdezési karakterláncok gyorsítótárazása: beállíthatja, hogyan kezeli az Azure CDN a lekérdezési karakterláncokat tartalmazó kérelmek gyorsítótárazását. További információ: [vezérlő Azure CDN a lekérdezési karakterláncok gyorsítótárazásának](cdn-query-string.md). Ha a fájl nem gyorsítótárazható, a lekérdezési karakterlánc gyorsítótárazása nincs hatása, gyorsítótárazás szabályok és a CDN alapértelmezett viselkedés alapján.

További információ az alapértelmezett viselkedést és a gyorsítótárazást irányelv fejlécek: [gyorsítótárazás működése](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Azure CDN gyorsítótárazási szabályok használata

1. Nyissa meg az Azure-portálon, válassza ki a CDN-profilt, majd válasszon ki egy végpontot.

2. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

   ![CDN-gyorsítótárszabályok gomb](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Megjelenik a **Gyorsítótárszabályok** lap.

   ![CDN-gyorsítótárazási szabályok lap](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Viselkedés beállítások gyorsítótárazása
A globális és egyéni gyorsítótárazási szabályok, adja meg a következő **gyorsítótárazásának** beállítások:

- **Gyorsítótár megkerülése**: nem gyorsítótárazzák, és figyelmen kívül hagyja a megadott forrás gyorsítótár-irányelv fejlécek.

- **Bírálja felül**: figyelmen kívül hagyja a megadott forrás gyorsítótár-irányelv fejlécek; a megadott gyorsítótárazás időtartama használja helyette.

- **Állítsa be, ha hiányoznak**: elfogadása származási által biztosított gyorsítótár-irányelv fejlécek, ha vannak ilyenek; ellenkező esetben használja a megadott gyorsítótárazás időtartama.

![Globális gyorsítótárszabályok](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Egyéni gyorsítótárszabályok](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Gyorsítótár elévülési ideje
A globális és egyéni gyorsítótárazási szabályokat nap, óra, perc és másodpercben a gyorsítótár lejárati időtartam adhat meg:

- Az a **felülbírálása** és **állítja be, ha hiányzik az** **gyorsítótárazásának** beállításait, az érvényes gyorsítótár időtartamok 0 másodperc és 366 nap közötti tartományba esik. Az érték 0 másodperc a CDN a tartalmakat gyorsítótárazza, de kell kísérelje az eredeti kiszolgálóra az egyes kérelmek meg újra érvényesítését.

- Az a **gyorsítótár megkerülése** beállítás módosításához a gyorsítótárazás időtartama automatikusan értéke 0 másodperc, és nem módosítható.

## <a name="custom-caching-rules-match-conditions"></a>Feltételek egyeznek egyéni szabályra érvényesek.

Egyéni gyorsítótár szabályok két egyező feltételek érhetők el:
 
- **Elérési út**: Ez az állapot megegyezik a elérési utat az URL-cím, kivéve a nevét, és támogatja a helyettesítő karakter es szimbólum (\*). Például _/myfile.html_, _/az/mappát / *_, és _/my/images/*.jpg_. A hossza legfeljebb 260 karakter lehet.

- **Bővítmény**: Ez a feltétel egyezik a kért fájl fájlnévkiterjesztése. Megadhatja vesszővel tagolt fájl kiterjesztéseket az egyeztetéshez. Például _.jpg_, _.mp3_, vagy _.png_. Bővítmények maximális száma 50 és bővítmény karakterek maximális száma: 16. 

## <a name="global-and-custom-rule-processing-order"></a>A globális és egyéni szabály feldolgozási sorrendben
A globális és egyéni gyorsítótárazási szabályok feldolgozása a következő sorrendben:

- Globális gyorsítótárazási szabályok felülbírálják az alapértelmezett CDN gyorsítótárazási viselkedés (HTTP-fejléc gyorsítótár-irányelv beállításait). 

- Egyéni gyorsítótárazási szabályok elsőbbséget élveznek globális gyorsítótárazási szabályokkal, ahol azok érvényesek. Egyéni gyorsítótárazási szabályok feldolgozása sorrendben fentről lefelé. Ez azt jelenti, hogy egy kérelem mindkét feltételnek megfelel, ha a lista alján szabályok elsőbbséget élveznek szabályokat a lista tetején. Ezért helyezze pontosabb szabályok alacsonyabb a listában.

**Példa**:
- Globális gyorsítótárszabályt: 
   - Gyorsítótárazásának: **felülbírálása**
   - Gyorsítótár lejárati időtartam: 1 nap

- Egyéni szabály #1 gyorsítótárazása:
   - Feltételének: **elérési útja**
   - Más értékkel egyezik:   _/otthoni / *_
   - Gyorsítótárazásának: **felülbírálása**
   - Gyorsítótár lejárati időtartam: 2 nap

- Egyéni gyorsítótárazás #2. szabály:
   - Feltételének: **bővítmény**
   - Más értékkel egyezik: _.html_
   - Gyorsítótárazásának: **állítja be, ha hiányzik**
   - Gyorsítótár lejárati időtartam: 3 nap

Ha ezek a szabályok vannak beállítva, a kérelem  _&lt;végpont állomásnevéhez&gt;_.azureedge.net/home/index.html eseményindítók egyéni gyorsítótárazás szabály #2, melynek értéke: **állítja be, ha a hiányzó** és 3 nap. Ezért ha a *index.html* fájl `Cache-Control` vagy `Expires` HTTP-fejlécek, figyelembe venni azok; ellenkező esetben ezek a fejlécek nincsenek beállítva, ha a fájlt a rendszer gyorsítótárazza a 3 nap.

> [!NOTE] 
> A szabály módosítása előtt gyorsítótárba helyezett fájlok karbantartása az eredeti gyorsítótár időtartama beállítás. A gyorsítótár időtartamok alaphelyzetbe állításához kell [a fájl törlése](cdn-purge-endpoint.md). 
>
> Az Azure CDN konfigurációs módosítások eltarthat egy ideig, a hálózaton belüli propagálásához: 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Azure CDN Standard verizon** -profilok propagálása általában befejezi 10 perc múlva.  
>

## <a name="see-also"></a>Lásd még

- [A gyorsítótárazás működése](cdn-how-caching-works.md)
- [Oktatóanyag: Azure CDN gyorsítótárazási szabályainak beállítása](cdn-caching-rules-tutorial.md)
