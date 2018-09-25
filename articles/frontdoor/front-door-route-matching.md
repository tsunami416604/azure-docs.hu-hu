---
title: Azure bejárati ajtajának szolgáltatás - útválasztási szabály egyező figyelése |} A Microsoft Docs
description: Ez a cikk segít megérteni a melyik útválasztási szabály egy bejövő kérésnek használandó illeszkedése az Azure bejárati ajtajának szolgáltatás
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
ms.openlocfilehash: 23582215654ff2d5003fe611c7149ad760d72bc5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957040"
---
# <a name="how-front-door-matches-requests-to-a-routing-rule"></a>Kérelem-útválasztási szabály bejárati ajtajának illeszkedése

Után a kapcsolat létrehozása és a egy SSL-kézfogásra végzett, amikor egy kérelem hajtanak végre a egy bejárati ajtajának környezetben, az első dolog, amelyet bejárati ajtajának meghatározása az összes konfiguráció, mely megfelelő a kérelmet adott útválasztási szabályt és majd véve a megadott műveletet. A következő dokumentum ismerteti, hogyan bejárati ajtajának meghatározza, hogy mely útválasztó-konfiguráció használata a HTTP-kérés feldolgozása során.

## <a name="structure-of-a-front-door-route-configuration"></a>Egy bejárati ajtajának útválasztó-konfiguráció szerkezete
Egy bejárati ajtajának útválasztási szabály konfigurációja két fő részből áll: egy "bal oldali" és "jobb oldalán". A bejövő kéréseket a bal oldalon, az útvonal azt felel meg, míg a jobb oldali határozza meg, hogyan tudjuk feldolgozni a kérelmet.

### <a name="incoming-match-left-hand-side"></a>Bejövő match (bal oldal)
Az alábbi tulajdonságokat határozza meg, hogy a bejövő kérelem megfelel-e az útválasztási szabályt (vagy a bal oldalon):

* **A HTTP protokollok** (HTTP/HTTPS)
* **Gazdagépek** (például www.foo.com \*. bar.com)
* **Elérési utak** (például /\*, /users/\*, /file.gif)

Ezek a tulajdonságok vannak bontva ki belső úgy, hogy minden egyes kombinációjához protokoll-gazdagép-elérésiút lehetséges egyeznek meg.

### <a name="route-data-right-hand-side"></a>Útvonal adatai (a jobb oldalon)
A kérelem feldolgozása döntése attól függ, a gyorsítótárazás engedélyezve van-e, vagy nem az adott útvonal. Tehát egy gyorsítótárazott választ, a kérelem nem rendelkezünk, ha azt fogjuk továbbítja a kérést a konfigurált háttérkészletben a megfelelő háttérrendszerre.

## <a name="route-matching"></a>Irányíthatja a megfelelő
Ez a szakasz azt egy adott bejárati ajtajának útválasztási szabály hogyan egyeznie összpontosít. Az alapszintű elképzelés az, hogy azt mindig egyezik, a **legtöbb-specifikus megfelelő először** csak a "bal oldali", akik.  Hogy először felel meg a HTTP-protokoll előtérbeli gazdagépre, majd a az elérési út alapján.

### <a name="frontend-host-matching"></a>Megfelelő előtér-gazdagép
Előtérbeli gazdagépek egyeztetéséhez, amikor a logikai használjuk, az alábbi:

1. Keressen pontos egyezés a gazdagépen bármilyen útválasztás.
2. Nem pontos előtérbeli gazdagépek egyeznek, ha elutasítja a kérelmet, és a egy 400-as hibás kérelem hiba küldése.

Ez a folyamat további magyarázat: a nézzünk egy példa konfiguráció látható bejárati ajtajának útvonalak (csak a bal oldalon):

| Útválasztási szabály | Előtér-gazdagépek | Útvonal |
|-------|--------------------|-------|
| A | foo.contoso.com | /\* |
| B | foo.contoso.com | /Users/\* |
| C | www.Fabrikam.com, foo.adventure-works.com elemre  | /\*, /images/\* |

A következő bejövő kérelmek bejárati ajtajának lettek küldve, ha azok egyezni szemben az alábbi útválasztási szabályokat a fent:

| Bejövő előtér-gazdagép | Egyező útválasztási szabály |
|---------------------|---------------|
| foo.contoso.com | A, B |
| www.Fabrikam.com | C |
| Images.Fabrikam.com | 400. hiba: Hibás kérelem |
| foo.Adventure-Works.com elemre | C |
| contoso.com | 400. hiba: Hibás kérelem |
| www.Adventure-Works.com | 400. hiba: Hibás kérelem |
| www.northwindtraders.com | 400. hiba: Hibás kérelem |

### <a name="path-matching"></a>A megfelelő elérési útja
Az adott előtérbeli állomás meghatározása és a lehetséges útválasztási szabályok csak az üzemeltető előtér-útvonalakat a szűrés után bejárati ajtajának majd az útválasztási szabályokat a kérelem elérési útja alapján szűri. Egy hasonló logika előtérbeli gazdagépként használjuk:

1. Keresse meg az elérési úton pontos egyezés minden útválasztási szabály
2. Ha nincsenek pontos egyezés elérési utak, keressen egy helyettesítő karaktert tartalmazó elérési útja, amely megfelel az útválasztási szabályok
3. Ha nincs útválasztási szabályokat a megfelelő elérési útvonal található, majd elutasítja a kérelmet, és adja vissza egy 400-as: Hibás kérés hiba HTTP-választ.

>[!NOTE]
> Bármely nélkül egy helyettesítő karaktert tartalmazó elérési utak pontos egyezést elérési utak tekintendők. Akkor is, ha az elérési út perjellel végződik, akkor számít pontos egyezés továbbra is.

További magyarázat: a nézzünk egy másik hárompéldányos készletet példák:

| Útválasztási szabály | Előtér-gazdagép    | Útvonal     |
|-------|---------|----------|
| A     | www.contoso.com | /        |
| B     | www.contoso.com | /\*      |
| C     | www.contoso.com | / AB      |
| D     | www.contoso.com | /ABC     |
| E     | www.contoso.com | /ABC/    |
| F     | www.contoso.com | /ABC/\*  |
| G     | www.contoso.com | / abc/def |
| H     | www.contoso.com | /PATH/   |

A következő példa egyező tábla eredményezi, hogy a konfigurációban:

| Bejövő kérelem    | Egyeztetett útvonal |
|---------------------|---------------|
| www.contoso.com/            | A             |
| www.contoso.com/a           | B             |
| www.contoso.com/AB          | C             |
| www.contoso.com/ABC         | D             |
| www.contoso.com/abzzz       | B             |
| www.contoso.com/ABC/        | E             |
| www.contoso.com/ABC/d       | F             |
| www.contoso.com/ABC/DEF     | G             |
| www.contoso.com/ABC/defzzz  | F             |
| www.contoso.com/ABC/DEF/GHI | F             |
| www.contoso.com/Path        | B             |
| www.contoso.com/Path/       | H             |
| www.contoso.com/Path/ZZZ    | B             |

>[!WARNING]
> </br> Ha nincsenek a catch – minden pontos egyezést előtérbeli állomás útválasztási szabályok elérési útvonal (`/*`), akkor nem lesz minden útválasztási szabály egyezést.
>
> Példa konfiguráció látható:
>
> | Útválasztás | Gazdagép             | Útvonal    |
> |-------|------------------|---------|
> | A     | Profile.contoso.com | /API/\* |
>
> Egyező tábla:
>
> | Bejövő kérelem       | Egyeztetett útvonal |
> |------------------------|---------------|
> | Profile.domain.com/Other | Nincs. 400. hiba: Hibás kérelem |

### <a name="routing-decision"></a>Útválasztási döntés
Miután már összevetettük a bejárati ajtajának egyetlen útválasztási szabályt, majd válassza ki a kérés feldolgozása kell. Ha az egyeztetett útválasztási szabály bejárati ajtajának rendelkezik elérhető gyorsítótárazott válasz majd azonos lekérdezi szolgálja ki, az ügyfélnek. Ellenkező esetben a következő lépés, lekérdezi kiértékelése, hogy konfigurálta [URL-újraíró (egyéni, továbbító elérési út)](front-door-url-rewrite.md) az egyező útválasztás szabály vagy sem. Ha nincs definiált egyéni, továbbító elérési utat, majd továbbítja a kérést lekérdezi a konfigurált háttérkészletben, mert a megfelelő háttérrendszerre. Más esetben a kérelem elérési útját megfelelően frissül a [egyéni továbbítási útvonal](front-door-url-rewrite.md) meghatározott és majd továbbítsa a háttérrendszerhez.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozzon létre egy bejárati ajtajának](quickstart-create-front-door.md).
- Ismerje meg, [bejárati ajtajának működése](front-door-routing-architecture.md).
