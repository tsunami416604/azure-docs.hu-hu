---
title: Címek keresése a Azure Maps Search szolgáltatás használatával | Microsoft Docs
description: Megtudhatja, hogyan keresheti meg a címeket a Azure Maps keresési szolgáltatás használatával
author: walsehgal
ms.author: v-musehg
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a41a811e9313f79c9c3165e02cb5eaa4353b65ab
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914468"
---
# <a name="find-an-address-using-the-azure-maps-search-service"></a>Címek keresése a Azure Maps Search szolgáltatással

A Maps Search szolgáltatás olyan REST API-k készlete, amelyek fejlesztők számára készültek címek, helyek, érdekes pontok, üzleti listák és egyéb földrajzi információk keresésére. A szolgáltatás egy földrajzi szélességet/hosszúságot rendel egy adott címnek, a Cross Streetnek, a földrajzi funkciónak vagy a POI-nak. A keresés által visszaadott szélességi és hosszúsági értékek más Maps-szolgáltatásokban, például az útvonalon és a forgalomban is használhatók paraméterekként.

Ebben a cikkben a következőket fogja elsajátítani:

* Címek keresése a [fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) használatával
* Címek keresése a tulajdonságok és koordináták mellett
* [Fordított címtartomány keresése](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) az utca-címek kereséséhez
* A Cross Street keresése a [névkeresési fordított Cross Street API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet) használatával

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a Maps Service API-kon bármilyen hívást lehessen kezdeményezni, szüksége van egy Maps-fiókra és egy kulcsra. A fiókok létrehozásával kapcsolatos információkért kövesse a [fiók kezelése](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) című témakör útmutatását, és kövesse az [elsődleges kulcs lekérése](./tutorial-search-location.md#getkey) a fiókhoz elsődleges előfizetési kulcs lekéréséhez című témakör lépéseit.

Ez a cikk a [Poster alkalmazást](https://www.getpostman.com/apps) használja a REST-hívások létrehozásához. Bármilyen, Ön által előnyben részesített API-fejlesztési környezetet használhat.

## <a name="using-fuzzy-search"></a>A fuzzy Search használata

A keresési szolgáltatás alapértelmezett API-je a [zavaros keresés](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) , amely akkor hasznos, ha nem tudja, hogy a felhasználói bemenetek hol találhatók a keresési lekérdezésekhez. Az API ötvözi a POI-kereséseket és a helymeghatározáshoz egy kanonikus "egysoros" kereséssel. Az API például képes kezelni bármilyen címek vagy POI-token kombinációjának bemeneteit. Az is lehet, hogy környezetfüggő pozícióval (lat./Lon. párosítva), teljes mértékben korlátozza a koordináta és a sugár, vagy általánosabban végrehajtja a földrajzi torzítási pontok nélkül.

A legtöbb keresési lekérdezés alapértelmezett `maxFuzzyLevel=1` értéke a teljesítmény elérése és a szokatlan eredmények csökkentése. Ez az alapértelmezett beállítás a lekérdezési paraméterben `maxFuzzyLevel=2` vagy `3`a (z) értékre való átadással igény szerint felülbírálható.

### <a name="search-for-an-address-using-fuzzy-search"></a>Címek keresése a fuzzy Search használatával

1. Nyissa meg a Poster alkalmazást, és kattintson az új | Hozzon létre egy újat, és válassza a **Get kérelem**lehetőséget. Adja meg a kérelem nevét a **fuzzy kereséshez**, válassza ki azt a gyűjteményt vagy mappát, ahová menteni szeretné, majd kattintson a **Save (Mentés**) gombra.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, és adja meg az API-végpont kérésének URL-címét.

    ![Fuzzy keresés](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Kérés URL-címe | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Authorization | Nincs hitelesítés |

    Az URL-cím alatt található **JSON** -attribútum meghatározza a válasz formátumát. Ez a cikk a könnyű használat és az olvashatóság érdekében JSON-t használ. A rendelkezésre álló válaszok formátumait a [Maps Functional API-referenciájának](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) **Get Search fuzzy** definíciójában találja.

3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket lekérdezésként vagy elérésiút-paraméterekként kíván használni a kérelem URL-címében:

    ![Fuzzy keresés](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Kulcs | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | előfizetés – kulcs | \<Az Azure Maps kulcsa\> |
    | query | Pizza |

4. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét.

    A "pizza" kétértelmű lekérdezési karakterlánca a "pizza" és az "étterem" kategóriába tartozó kategóriákat [eredményezett](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) . Minden eredmény az utca, a szélességi/hosszúsági értékek, a megtekintési port és a hely belépési pontjait adja vissza.
  
    Ennek a lekérdezésnek az eredményei eltérőek, nem egy adott hivatkozási helyhez kötve. A **countrySet** paraméterrel csak azokat az országokat/régiókat adhatja meg, amelyekhez az alkalmazásnak lefedettségre van szüksége, mivel az alapértelmezett viselkedés a teljes világon való keresés, ami esetleg szükségtelen eredményeket ad vissza.

5. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra:

    | Kulcs | Value |
    |------------------|-------------------------|
    | countrySet | USA |
  
    Az eredmények mostantól az országkód szerint vannak kötve, és a lekérdezés a Egyesült Államok pizzás éttermeit adja vissza.
  
    Egy adott hely eredményeinek megadásához lekérdezheti egy érdekes pontot, és a visszaadott szélességi és hosszúsági értékeket a meghívásban a fuzzy keresési szolgáltatásban lehet használni. Ebben az esetben a Search szolgáltatást használta a Seattle Space tű helyének visszaküldésére és a lat használatára. Lon. a keresés tájolására szolgáló értékek.
  
6. A params paraméterben adja meg a következő kulcs/érték párokat, majd kattintson a **Küldés**gombra:

    ![Fuzzy keresés](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Kulcs | Value |
    |-----|------------|
    | Lat | 47,620525 |
    | Lon | – 122,349274 |

## <a name="search-for-address-properties-and-coordinates"></a>Címek tulajdonságainak és koordinátáinak keresése

A keresési címek API-ban teljes vagy részleges lakcímet adhat át, és olyan választ kaphat, amely részletes címeket tartalmaz, például a helyhatóságot vagy az alosztást, valamint a szélességi és a hosszúsági pozíciót.

1. A Poster területen kattintson az **új kérelem** | **Get kérelem** elemre, és nevezze el a **keresett címeket**.
2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, adja meg az API-végponthoz tartozó kérelem URL-címét, és válasszon ki egy hitelesítési protokollt, ha van ilyen.

    ![Keresési címek](./media/how-to-search-for-address/address_search_url.png)
  
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Kérés URL-címe | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) |
    | Authorization | Nincs hitelesítés |

3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket lekérdezésként vagy elérésiút-paraméterekként kíván használni a kérelem URL-címében:
  
    ![Keresési címek](./media/how-to-search-for-address/address_search_params.png)
  
    | Kulcs | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | előfizetés – kulcs | \<Az Azure Maps kulcsa\> |
    | query | 400 Broad St, Seattle, WA 98109 |
  
4. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét.
  
    Ebben az esetben teljes címzési lekérdezést adott meg, és egyetlen eredményt kap a válasz törzsében.
  
5. A params paraméterben szerkessze a lekérdezési karakterláncot a következő értékre:
    ```plaintext
        400 Broad, Seattle
    ```

6. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra:

    | Kulcs | Value |
    |-----|------------|
    | Typeahead | true |

    A **typeahead** jelző azt jelzi, hogy a címek keresési API-ját részleges bemenetként kezeli a lekérdezés, és a prediktív értékek tömbjét adja vissza.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Házszám keresése fordított címek használatával

1. A Poster területen kattintson az **új kérelem** | **Get kérelem** elemre, és adja meg a **fordított címtartomány-keresés**nevet.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, és adja meg az API-végpont kérésének URL-címét.
  
    ![Fordított cím keresési URL-címe](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Kérés URL-címe | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Authorization | Nincs hitelesítés |
  
3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket lekérdezésként vagy elérésiút-paraméterekként kíván használni a kérelem URL-címében:
  
    ![Fordított címek keresési paramétereinek megadása](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Kulcs | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | előfizetés – kulcs | \<Az Azure Maps kulcsa\> |
    | query | 47.591180,-122,332700 |
  
4. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét.

    A válasz tartalmazza a Safeco mező legfontosabb információit.
  
5. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra:

    | Kulcs | Value |
    |-----|------------|
    | number | true |

    Ha a kérelemben a [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezési paramétert küldi a rendszer, a válasz az utca oldalát (bal/jobb oldali), valamint az adott szám eltolási pozícióját is tartalmazhatja.
  
6. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra:

    | Kulcs | Value |
    |-----|------------|
    | returnSpeedLimit | true |
  
    Ha a [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezési paraméter be van állítva, a rendszer a feladott sebességi korlátot adja vissza.

7. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra:

    | Kulcs | Value |
    |-----|------------|
    | returnRoadUse | true |

    Ha a [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezési paraméter be van állítva, a válasz a közúti használatot a fordított geocodes az utca szintjén adja vissza.

8. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra:

    | Kulcs | Value |
    |-----|------------|
    | roadUse | true |

    A fordított geocode-lekérdezés egy adott típusú közúti használatra korlátozható a [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezési paraméter használatával.
  
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>A Cross Street keresése fordított címekkel Cross Street Search használatával

1. A Poster területen kattintson az **új kérelem** | **Get kérelem** elemre, és nevezze **vissza a Cross Street Search kifejezésre**.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, és adja meg az API-végpont kérésének URL-címét.
  
    ![Fordított címek Cross Street Search](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Kérés URL-címe | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Authorization | Nincs hitelesítés |
  
3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket lekérdezésként vagy elérésiút-paraméterekként kíván használni a kérelem URL-címében:
  
    | Kulcs | Value |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | előfizetés – kulcs | \<Az Azure Maps kulcsa\> |
    | query | 47.591180,-122,332700 |
  
4. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét.

## <a name="next-steps"></a>További lépések

- Fedezze fel a [Azure Maps Search szolgáltatás](https://docs.microsoft.com/rest/api/maps/search) API dokumentációját.
