---
title: Azure bejárati ajtó – Útválasztási szabály egyeztetése figyelés | Microsoft dokumentumok
description: Ez a cikk segít megérteni, hogy az Azure Front Door hogyan egyezteti a bejövő kérelmekhez használandó útválasztási szabályt
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 420aa52293da14a0dfe8fbdfe681440ee4309e6b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878595"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Hogyan egyezteti a Bejárati ajtó az útválasztási szabályra vonatkozó kérelmeket?

A kapcsolat létrehozása és a TLS-kézfogás elvégzése után, amikor egy kérés egy bejárati ajtajához tartozó környezetben landol, az egyik első dolog, amelyet a Bejárati ajtó végez, az összes konfigurációból meghatároz, amely adott útválasztási szabály megfelel a kérésnek, majd a meghatározott művelet et. A következő dokumentum bemutatja, hogyan bejárati ajtó határozza meg, hogy melyik útvonal konfigurációt kell használni a HTTP-kérelmek feldolgozásakor.

## <a name="structure-of-a-front-door-route-configuration"></a>A bejárati ajtó útvonalkonfigurációjának szerkezete
A bejárati ajtó útválasztási szabálykonfigurációja két fő részből áll: egy "bal oldali" és egy "jobb oldali". A bejövő kérést az útvonal bal oldalához igazítjuk, míg a jobb oldal határozza meg, hogyan dolgozzuk fel a kérést.

### <a name="incoming-match-left-hand-side"></a>Bejövő mérkőzés (bal oldali)
A következő tulajdonságok határozzák meg, hogy a bejövő kérelem megegyezik-e az útválasztási szabállyal (vagy a bal oldalival):

* **HTTP protokollok** (HTTP/HTTPS)
* **Gazdagépek** (például\.www \*foo.com, .bar.com)
* **Elérési utak** (például\*/ ,\*/users/ , /file.gif)

Ezek a tulajdonságok belsőleg ki vannak bontva, így a Protokoll/állomás/elérési út minden kombinációja egy lehetséges egyezési halmaz.

### <a name="route-data-right-hand-side"></a>Útvonaladatok (jobb oldal)
A kérelem feldolgozásának döntése attól függ, hogy engedélyezve van-e a gyorsítótárazás az adott útvonalhoz. Így ha nem rendelkezik a gyorsítótárazott válasz a kérelemre, továbbítjuk a kérelmet a megfelelő háttérrendszer a konfigurált háttérkészletben.

## <a name="route-matching"></a>Útvonalegyeztetés
Ez a szakasz arra összpontosít, hogy hogyan egyezünk meg egy adott bejárati ajtó útválasztási szabályával. Az alapkoncepció az, hogy mindig a **legspecifikusabb mérkőzéshez** igazodunk, amely először csak a "bal oldalt" nézi.  Először http protokoll, majd frontend gazdagép, majd az elérési út alapján egyeztetjük.

### <a name="frontend-host-matching"></a>Előtér-állomás egyeztetése
Az előtér-állomások egyeztetésekor az alábbi logikát használjuk:

1. Keressen olyan útvonalat, amely pontosan egyezik a gazdagépen.
2. Ha nincs pontos előtér-állomásegyező, utasítsa el a kérelmet, és küldjön egy 400 hibás kérelem hibát.

Ennek a folyamatnak a további magyarázatához nézzünk meg egy példa konfigurációt a Bejárati ajtó útvonalairól (csak a bal oldalon):

| Útválasztási szabály | Előtér-állomások | Útvonal |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /users/\* |
| C# | www\.fabrikam.com, foo.adventure-works.com  | /\*, /images/\* |

Ha a következő bejövő kérelmeket küldték a Bejárati ajtónak, azok felülről a következő útválasztási szabályokkal egyeznek meg:

| Bejövő előtér-állomás | Egyező útválasztási szabály(ok) |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www\.fabrikam.com | C# |
| images.fabrikam.com | 400-as hiba: Hibás kérés |
| foo.adventure-works.com | C# |
| contoso.com | 400-as hiba: Hibás kérés |
| www\.adventure-works.com | 400-as hiba: Hibás kérés |
| www\.northwindtraders.com | 400-as hiba: Hibás kérés |

### <a name="path-matching"></a>Elérési út egyeztetése
Az adott előtér-állomás meghatározása és a lehetséges útválasztási szabályok szűrése csak az adott előtér-állomással rendelkező útvonalakra, a Bejárati ablak szűri az útválasztási szabályokat a kérelem elérési útja alapján. Hasonló logikát használunk, mint az előtér-gazdagépek:

1. Keresse meg a pontos egyezésű útválasztási szabályt az elérési úton
2. Ha nincs pontos egyezési elérési utak, keressen útválasztási szabályokat egy helyettesítő elérési úttal, amely megfelel
3. Ha nem található útválasztási szabály egy megfelelő elérési úttal, majd utasítsa el a kérelmet, és adjon vissza egy 400: Hibás kérelem hiba HTTP-válasz.

>[!NOTE]
> A helyettesítő karakter nélküli görbék pontos egyezésű görbéknek minősülnek. Még akkor is, ha az elérési út perjellel végződik, akkor is pontos egyezésnek tekinthető.

További magyarázatként nézzünk meg egy másik példát:

| Útválasztási szabály | Előtér-gazdagép    | Útvonal     |
|-------|---------|----------|
| A     | www\.contoso.com | /        |
| B     | www\.contoso.com | /\*      |
| C#     | www\.contoso.com | /ab      |
| D     | www\.contoso.com | /abc     |
| E     | www\.contoso.com | /abc/    |
| F     | www\.contoso.com | /abc/\*  |
| G     | www\.contoso.com | /abc/def |
| H     | www\.contoso.com | /path/   |

Tekintettel erre a konfigurációra, a következő példamegfelelő tábla eredménye:

| Bejövő kérelem    | Egyező útvonal |
|---------------------|---------------|
| www\.contoso.com/            | A             |
| www\.contoso.com/a           | B             |
| www\.contoso.com/ab          | C#             |
| www\.contoso.com/abc         | D             |
| www\.contoso.com/abzzz       | B             |
| contoso.com/abc/\.        | E             |
| www\.contoso.com/abc/d       | F             |
| www\.contoso.com/abc/def     | G             |
| www\.contoso.com/abc/defzzz  | F             |
| www\.contoso.com/abc/def/ghi | F             |
| www\.contoso.com/path        | B             |
| www\.contoso.com/path/       | H             |
| www\.contoso.com/path/zzz    | B             |

>[!WARNING]
> </br> Ha nincsenek útválasztási szabályok egy pontos egyezéses előtér-állomáshoz egy catch-all útvonalelérési úttal (`/*`), akkor nem lesz egyezés egyetlen útválasztási szabállyal sem.
>
> Példa konfiguráció:
>
> | Útválasztás | Gazdagép             | Útvonal    |
> |-------|------------------|---------|
> | A     | profile.contoso.com | /api/\* |
>
> Egyező tábla:
>
> | Bejövő kérelem       | Egyező útvonal |
> |------------------------|---------------|
> | profile.domain.com/other | Nincs. 400-as hiba: Hibás kérés |

### <a name="routing-decision"></a>Útválasztási döntés
Miután kiegyenlített egyetlen bejárati ajtó útválasztási szabály, majd meg kell választani, hogyan kell feldolgozni a kérelmet. Ha az egyező útválasztási szabály, bejárati ajtajának van egy gyorsítótárazott válasz áll rendelkezésre, majd ugyanez lesz kiszolgálva vissza az ügyfélnek. Ellenkező esetben a következő dolog, amely kiértékeli, hogy [konfigurálta-e az URL-újraírást (egyéni továbbítási útvonalat)](front-door-url-rewrite.md) az egyező útválasztási szabályhoz, vagy sem. Ha nincs megadva egyéni továbbítási elérési út, majd a kérelem a megfelelő háttérrendszerbe lesz továbbítva a konfigurált háttérkészletben, ahogy van. Különben a kérelem elérési útja frissül az [egyéni továbbítási elérési út](front-door-url-rewrite.md) nak megfelelően, majd továbbítva a háttérrendszerhez.

## <a name="next-steps"></a>További lépések

- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).
- A [Front Door működésének](front-door-routing-architecture.md) ismertetése.
