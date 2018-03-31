---
title: Szabályozhatja az Azure CDN szolgáltatás használata a szabályok gyorsítótárazással gyorsítótárazásának |} Microsoft Docs
description: Gyorsítótárazás szabályok CDN vagy módosíthatja a gyorsítótár lejárati alapértelmezés, globális és a feltételek, például az egy URL-elérési út és fájlnév-kiterjesztések használhatja.
services: cdn
documentationcenter: ''
author: dksimpson
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 735978a0986b2b16b4f96faca78c06d798915002
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Vezérlő Azure CDN szolgáltatás használata a szabályok gyorsítótárazással gyorsítótárazásának működése

> [!NOTE] 
> Gyorsítótárazás szabályok csak esetén érhetők el **Verizon Standard Azure CDN** és **Akamai Standard Azure CDN**. A **Verizon Premium Azure CDN**, használhatja a [Azure CDN szabálymotor](cdn-rules-engine.md) a a **kezelése** hasonló funkciókat a portálon.
 
Az Azure Content Delivery Network (CDN) kétféleképpen szabályozhatja, hogy a gyorsítótárba: 

- Szabályok gyorsítótárazása: Ez a cikk ismerteti, hogyan használható tartalomkézbesítési hálózat (CDN) gyorsítótárazás szabályok beállítása vagy módosítása a gyorsítótár lejárati alapértelmezés globálisan és egyéni feltételeknek, például az egy URL-cím elérési út és fájlnév kiterjesztése. Az Azure CDN kétféle típusú szabályok gyorsítótárazás biztosítja:
   - Globális szabályok gyorsítótárazása: a profilban, amely hatással van a végpont összes kérelmet, beállíthatja a végpontok egy globális gyorsítótárszabályt. A globális gyorsítótárszabály felülbírálja a gyorsítótár-irányelv HTTP-fejlécek, ha beállítása.
   - Egyéni gyorsítótárazási szabályok: beállíthat egy vagy több egyéni szabályra érvényesek minden egyes végpont a profilban. Egyéni szabályok egyezés egyedi elérési utak fájlkiterjesztéseket, feldolgozása sorrendben történik, és globális gyorsítótárazási szabály felülbírálása gyorsítótárazást, ha be. 

- Lekérdezési karakterláncok gyorsítótárazása: beállíthatja, hogyan kezeli az Azure CDN a lekérdezési karakterláncokat tartalmazó kérelmek gyorsítótárazását. További információ: [vezérlő Azure CDN a lekérdezési karakterláncok gyorsítótárazásának](cdn-query-string.md). Ha a fájl nem gyorsítótárazható, a lekérdezési karakterlánc gyorsítótárazása nincs hatása, gyorsítótárazás szabályok és a CDN alapértelmezett viselkedés alapján.

További információ az alapértelmezett viselkedést és a gyorsítótárazást irányelv fejlécek: [gyorsítótárazás működése](cdn-how-caching-works.md).

## <a name="tutorial"></a>Oktatóanyag

Gyorsítótár-CDN hogyan szabályok:

1. Nyissa meg az Azure-portálon, válassza ki a CDN-profilt, majd válasszon ki egy végpontot.
2. A beállítások a bal oldali ablaktáblában jelölje ki a **szabályok gyorsítótárazás**.

   ![CDN-gyorsítótárazás szabályok gomb](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

3. Hozzon létre egy globális gyorsítótárszabályt az alábbiak szerint:
   1. A **szabályok gyorsítótárazás globális**, beállíthatja **lekérdezési sztringek gyorsítótárazásának** való **lekérdezési karakterláncok figyelmen kívül**.
   2. Állítsa be **gyorsítótárazásának** való **állítja be, ha a hiányzó**.
       
   3. A **gyorsítótár lejárati időtartam**, adja meg a 10 a **nap** mező.

       A globális gyorsítótárszabály érinti a végpont összes kérelem. Ez a szabály eleget tegyen a forrás-gyorsítótár-irányelv fejlécek, ha vannak ilyenek (`Cache-Control` vagy `Expires`); ellenkező esetben, ha nincsenek megadva, a gyorsítótár 10 napos értékre állítja. 

     ![Globális gyorsítótárszabályok](./media/cdn-caching-rules/cdn-global-caching-rules.png)

4. Hozzon létre egy egyéni gyorsítótárszabályt az alábbiak szerint:
    1. A **egyéni szabályok gyorsítótárazás**, beállíthatja **feltételének** való **elérési** és **más értékkel egyezik** való `/images/*.jpg`.
    2. Állítsa be **gyorsítótárazásának** való **felülbírálása** , és írja be a 30 a **nap** mező.
       
       Az egyéni gyorsítótárszabályt beállítja a gyorsítótárazás időtartama 30 napnyi bármely `.jpg` a képfájlok a `/images` mappában található a végpont. Felülbírálja a `Cache-Control` vagy `Expires` a forráskiszolgáló által küldött HTTP-fejléceket.

    ![Egyéni gyorsítótárszabályok](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

    
> [!NOTE] 
> A szabály módosítása előtt gyorsítótárba helyezett fájlok karbantartása az eredeti gyorsítótár időtartama beállítás. A gyorsítótár időtartamok alaphelyzetbe állításához kell [a fájl törlése](cdn-purge-endpoint.md). A **Azure CDN Verizon** végpontok, az akár 90 percig is tarthat a gyorsítótárazáshoz szabályok érvénybe léptetéséhez.

## <a name="reference"></a>Leírások

### <a name="caching-behavior-settings"></a>Viselkedés beállítások gyorsítótárazása
A globális és egyéni gyorsítótárazási szabályok, adja meg a következő **gyorsítótárazásának** beállítások:

- **Gyorsítótár megkerülése**: nem gyorsítótárazzák, és figyelmen kívül hagyja a megadott forrás gyorsítótár-irányelv fejlécek.
- **Bírálja felül**: figyelmen kívül hagyja a megadott forrás gyorsítótár-irányelv fejlécek; a megadott gyorsítótárazás időtartama használja helyette.
- **Állítsa be, ha hiányoznak**: elfogadása származási által biztosított gyorsítótár-irányelv fejlécek, ha vannak ilyenek; ellenkező esetben használja a megadott gyorsítótárazás időtartama.

### <a name="cache-expiration-duration"></a>Gyorsítótár elévülési ideje
A globális és egyéni gyorsítótárazási szabályokat nap, óra, perc és másodpercben a gyorsítótár lejárati időtartam adhat meg:

- Az a **felülbírálása** és **állítja be, ha hiányzik az** **gyorsítótárazásának** beállításait, az érvényes gyorsítótár időtartamok 0 másodperc és 366 nap közötti tartományba esik. Az érték 0 másodperc a CDN a tartalmakat gyorsítótárazza, de kell kísérelje az eredeti kiszolgálóra az egyes kérelmek meg újra érvényesítését.
- Az a **gyorsítótár megkerülése** beállítás módosításához a gyorsítótárazás időtartama automatikusan értéke 0 másodperc, és nem módosítható.

### <a name="custom-caching-rules-match-conditions"></a>Feltételek egyeznek egyéni szabályra érvényesek.

Egyéni gyorsítótár szabályok két egyező feltételek érhetők el:
 
- **Elérési út**: Ez az állapot megegyezik a elérési utat az URL-cím, kivéve a nevét, és támogatja a helyettesítő karakter es szimbólum (\*). Például `/myfile.html`, `/my/folder/*`, és `/my/images/*.jpg`. A hossza legfeljebb 260 karakter lehet.

- **Bővítmény**: Ez a feltétel egyezik a kért fájl fájlnévkiterjesztése. Megadhatja vesszővel tagolt fájl kiterjesztéseket az egyeztetéshez. Például `.jpg`, `.mp3`, vagy `.png`. Bővítmények maximális száma 50 és bővítmény karakterek maximális száma: 16. 

### <a name="global-and-custom-rule-processing-order"></a>A globális és egyéni szabály feldolgozási sorrendben
A globális és egyéni gyorsítótárazási szabályok feldolgozása a következő sorrendben:

- Globális gyorsítótárazási szabályok felülbírálják az alapértelmezett CDN gyorsítótárazási viselkedés (HTTP-fejléc gyorsítótár-irányelv beállításait). 

- Egyéni gyorsítótárazási szabályok elsőbbséget élveznek globális gyorsítótárazási szabályokkal, ahol azok érvényesek. Egyéni gyorsítótárazási szabályok feldolgozása sorrendben fentről lefelé. Ez azt jelenti, hogy egy kérelem mindkét feltételnek megfelel, ha a lista alján szabályok elsőbbséget élveznek szabályokat a lista tetején. Ezért helyezze pontosabb szabályok alacsonyabb a listában.

**Példa**:
- Globális gyorsítótárszabályt: 
   - Gyorsítótárazásának: **felülbírálása**
   - Gyorsítótár lejárati időtartam: 1 nap

- Egyéni szabály #1 gyorsítótárazása:
   - Feltételének: **elérési útja**
   - Más értékkel egyezik: `/home/*`
   - Gyorsítótárazásának: **felülbírálása**
   - Gyorsítótár lejárati időtartam: 2 nap

- Egyéni gyorsítótárazás #2. szabály:
   - Feltételének: **bővítmény**
   - Más értékkel egyezik: `.html`
   - Gyorsítótárazásának: **állítja be, ha hiányzik**
   - Gyorsítótár lejárati időtartam: 3 nap

Ha ezek a szabályok vannak beállítva, a kérelem `<endpoint>.azureedge.net/home/index.html` egyéni gyorsítótárazás szabály #2, melynek értéke eseményindítók: **állítja be, ha a hiányzó** és a 3 nap. Ezért ha a `index.html` fájl `Cache-Control` vagy `Expires` HTTP-fejlécek, figyelembe venni azok; ellenkező esetben ezek a fejlécek nincsenek beállítva, ha a fájlt a rendszer gyorsítótárazza a 3 nap.

