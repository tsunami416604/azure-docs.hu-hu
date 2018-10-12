---
title: Szabályozhatja az Azure CDN gyorsítótárazási viselkedésének gyorsítótár-szabályokkal |} A Microsoft Docs
description: CDN gyorsítótárazási szabályok használatával vagy módosíthatja az alapértelmezett gyorsítótár lejárati viselkedés, globálisan és a feltételek, például egy URL-cím elérési út és fájlnév-kiterjesztések.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 10275b2938ce66a2816b1d4a5589a5e88ee22e80
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093918"
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Az Azure CDN gyorsítótárazási viselkedésének gyorsítótár-szabályokkal vezérlése

> [!NOTE] 
> Gyorsítótárazási szabályok csak esetén érhetők el **Azure CDN Standard verizon** és **Azure CDN Akamai Standard** profilok. A **verizon Azure CDN Premium** profilok kell használnia a [Azure CDN szabálymotorral](cdn-rules-engine.md) a a **kezelés** portál a hasonló funkciókat.
 
Az Azure Content Delivery Network (CDN) szabályozhatja, hogy a gyorsítótárba két lehetőséget kínál: 

- Gyorsítótár-szabályokkal: Ez a cikk azt ismerteti, hogyan használható content delivery network (CDN) gyorsítótár-szabályokkal vagy módosíthatja az alapértelmezett gyorsítótár lejárati viselkedés globálisan és az egyéni feltételek, például egy URL-cím elérési út és fájlnév kiterjesztéssel. Az Azure CDN két gyorsítótárazási szabálytípust biztosít:

   - Globális gyorsítótárazási szabályok: Beállíthat egy globális gyorsítótárazási szabályt mindegyik végponthoz a profiljában, amely a végpontra küldött összes kérelmet érinti. A globális gyorsítótárazási szabály felülbírálja az összes HTTP-gyorsítótárazási irányelv fejlécét, ha be van állítva.

   - Egyéni gyorsítótárazási szabályok: Beállíthat egy vagy több globális gyorsítótárazási szabályt minden egyes végponthoz a profiljában. Az egyéni gyorsítótárazási szabályok meghatározott elérési utaknak és fájlkiterjesztéseknek felelnek meg, a feldolgozásuk sorrendben történik, és felülbírálják a globális gyorsítótárazási szabályt, ha az be van állítva. 

- Lekérdezési karakterláncok gyorsítótárazása: hogyan kezeli az Azure CDN a lekérdezési karakterláncot tartalmazó kérelmek gyorsítótárazási módosíthatja. További információ: [Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal vezérlése](cdn-query-string.md). Ha a fájl nem gyorsítótárazható, a lekérdezési karakterlánc gyorsítótárazási beállítás nem befolyásolja, gyorsítótárazási szabályokat és a CDN alapértelmezett viselkedés alapján.

Alapértelmezett gyorsítótárazási viselkedésének és gyorsítótárazás irányelv fejlécek kapcsolatos információkért lásd: [gyorsítótárazás működése](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Az Azure CDN gyorsítótárazási szabályok elérése

1. Nyissa meg az Azure Portalon, válassza ki a CDN-profil, és válasszon ki egy végpontot.

2. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

   ![CDN-gyorsítótárszabályok gomb](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   Megjelenik a **Gyorsítótárszabályok** lap.

   ![CDN-gyorsítótárazási szabályok lap](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Gyorsítótárazási viselkedés beállítások
A globális és egyéni gyorsítótárszabályok, adja meg a következő **gyorsítótárazási viselkedésének** beállítások:

- **Gyorsítótár megkerülése**: nem gyorsítótárazzák, és figyelmen kívül hagyja a forrás által biztosított gyorsítótárirányelv-fejlécek.

- **Felülbírálás**: hagyja figyelmen kívül a forrás által biztosított gyorsítótárirányelv-fejlécek; helyette használja a megadott gyorsítótárazás időtartama.

- **Beállítás hiányzó érték esetén**: Honor forrás által biztosított gyorsítótárirányelv-fejlécek, ha azok léteznek; ellenkező esetben használja a megadott gyorsítótárazás időtartama.

![Globális gyorsítótárszabályok](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Egyéni gyorsítótárszabályok](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Gyorsítótár elévülési ideje
A globális és egyéni gyorsítótárszabályok megadhatja a gyorsítótár lejárati időtartama a nap, óra, perc és másodperc:

- Az a **felülbírálása** és **beállítás hiányzó érték esetén** **gyorsítótárazási viselkedésének** beállításai, érvényes gyorsítótár időtartamok 366 napot és az 0 másodperc közötti tartományba esik. Az érték 0 másodperc a CDN gyorsítótárazza a tartalmat, de kell kísérelje a forráskiszolgáló az egyes kérések meg újra érvényesítését.

- Az a **gyorsítótár megkerülése** beállítást, a gyorsítótárazás időtartama automatikusan 0 másodperc értékre van állítva, és nem módosítható.

## <a name="custom-caching-rules-match-conditions"></a>Egyéni gyorsítótárazási szabályok egyezési feltételei

Egyéni gyorsítótárazási szabályok két egyezési feltételei érhetők el:
 
- **Elérési út**: Ez az állapot megegyezik az elérési útját az URL-címet, kivéve a tartomány nevét, és támogatja a helyettesítő karakter és szimbólum (\*). Ha például _/myfile.html_, _/saját/mappa / *_, és _/my/images/*.jpg_. A hossza legfeljebb 260 karakter hosszúságú lehet.

- **Bővítmény**: Ez a feltétel egyezik a kért fájl fájlkiterjesztése. Megadhatja azon fájlkiterjesztések vesszővel elválasztott megfelelően listáját. Ha például _.jpg_, _.mp3_, vagy _.png_. Bővítmények maximális száma érték az 50, a bővítmény karakterek maximális száma pedig 16. 

## <a name="global-and-custom-rule-processing-order"></a>A globális és egyéni szabály feldolgozási sorrendben
A globális és egyéni gyorsítótárazási szabályok feldolgozása a következő sorrendben:

- Globális gyorsítótárazási szabályok felülbírálják az alapértelmezett CDN gyorsítótárazási viselkedésének (HTTP-fejléc gyorsítótárirányelv-beállítások). 

- Egyéni gyorsítótárszabályok elsőbbséget élveznek a globális gyorsítótárazási szabályokat, ahol azok érvényesek. Egyéni gyorsítótárazási szabályok feldolgozása sorrendben felülről lefelé történik. Azt jelenti Ha a kérelem megfelel mindkét feltétel, a lista alján szabályok elsőbbséget élveznek szabályokat a lista tetején. Ezért helyezze részletesebb szabályozás alacsonyabb a listában.

**Példa**:
- Globális gyorsítótárazási szabály: 
   - Gyorsítótárazási viselkedésének: **felülbírálása**
   - Gyorsítótár elévülési ideje: 1 nap

- Az egyéni szabály #1 gyorsítótárazást:
   - Az állapotot meg: **elérési útja**
   - Érték egyezik:   _/home / *_
   - Gyorsítótárazási viselkedésének: **felülbírálása**
   - Gyorsítótár elévülési ideje: 2 nap

- Egyéni gyorsítótárazási #2. szabály:
   - Az állapotot meg: **bővítmény**
   - Érték egyezik: _.html_
   - Gyorsítótárazási viselkedésének: **beállítás hiányzó érték esetén**
   - Gyorsítótár elévülési ideje: 3 nap

Ha ezek a szabályok vannak beállítva, a kérelem  _&lt;végpont gazdaneve&gt;_.azureedge.net/home/index.html eseményindítók egyedi gyorsítótárazás szabály #2, amely van beállítva: **beállítás hiányzó érték esetén** és 3 nap. Ezért ha a *index.html* fájl `Cache-Control` vagy `Expires` HTTP-fejléceket, figyelembe véve azok; ellenkező esetben, ha nincsenek beállítva ezek a fejlécek, a fájl tárolja a rendszer 3 napig.

> [!NOTE] 
> Fájlok, amelyek lettek gyorsítótárazva, mielőtt a szabályt úgy módosítják a forrás cache időtartama beállítás karbantartása. A gyorsítótár időtartamok alaphelyzetbe állításához kell [végleges törlése a fájl](cdn-purge-endpoint.md). 
>
> Az Azure CDN konfigurációs módosítások eltarthat egy ideig a hálózaton belüli propagálásához: 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Azure CDN Standard verizon** profilok propagálása általában befejezi 10 perc múlva.  
>

## <a name="see-also"></a>Lásd még

- [A gyorsítótárazás működése](cdn-how-caching-works.md)
- [Oktatóanyag: Azure CDN gyorsítótárazási szabályainak beállítása](cdn-caching-rules-tutorial.md)
