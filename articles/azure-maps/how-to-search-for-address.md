---
title: Hely keresése Azure Maps Search Services használatával | Microsoft Azure térképek
description: Ebből a cikkből megtudhatja, hogyan kereshet meg egy helyet a helymeghatározáshoz és fordított helymeghatározáshoz Microsoft Azure Maps Search Service használatával.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3b5da7eab9cff5c5e051fc4d5ab7ff582a95c20d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899231"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Hely keresése Azure Maps Search Services használatával

A Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) olyan REST API-k készlete, amelyek segítségével a fejlesztők megkereshetik a címeket, a helyeket, az üzleti listákat név vagy kategória alapján, valamint egyéb földrajzi információkat. A hagyományos helymeghatározáshoz támogatása mellett a szolgáltatások a földrajzi szélesség és a hosszúságok alapján is visszafordítják a címeket és a geocode. A keresés által visszaadott szélességi és hosszúsági értékek paraméterekként használhatók más Azure Maps-szolgáltatásokban, például az [útvonal](https://docs.microsoft.com/rest/api/maps/route) -és [időjárási](https://docs.microsoft.com/rest/api/maps/weather) szolgáltatásokban.

Ebben a cikkben a következőket fogja elsajátítani:

* Címek szélességi és hosszúsági koordinátáinak lekérdezése (geocode) a [Search-címek API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) használatával
* Keresse meg a keresett címeket vagy érdekes pontokat (POI) a [fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) használatával
* Címek keresése a tulajdonságok és koordináták mellett
* [Fordított címtartomány-keresés](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) a koordináta helyének az utca címeként való lefordításához
* A Cross Street keresése a [névkeresési fordított Cross Street API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet) használatával

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához először létre kell hoznia egy Azure Maps fiókot, és le kell kérnie a Maps-fiók előfizetési kulcsát. Kövesse a [fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) egy Azure Maps fiók előfizetésének létrehozásához című témakör utasításait, és kövesse az [elsődleges kulcs lekérése](quick-demo-map-app.md#get-the-primary-key-for-your-account) a fiók elsődleges kulcsának lekérése című szakasz lépéseit. A Azure Maps-hitelesítéssel kapcsolatos további információkért lásd: a [Azure Maps hitelesítés kezelése](./how-to-manage-authentication.md).

Ez a cikk a [Poster alkalmazást](https://www.getpostman.com/apps) használja a REST-hívások létrehozásához. Bármilyen, Ön által előnyben részesített API-fejlesztési környezetet használhat.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Címek szélességének és hosszúságának kérése (helymeghatározáshoz)

Ebben a példában a Azure Maps [keresési címek API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) -t használjuk, hogy az utca címeit a szélességi és a hosszúsági koordinátákra alakítsa át. Átadhatja az API-nak teljes vagy részleges lakcímét, és olyan választ kaphat, amely olyan részletes címeket tartalmaz, mint például az utca, az irányítószám, az ország/régió, valamint a szélességi és a hosszúsági pozícióbeli értékek.

Ha a geocode címei vannak, a [keresési cím KÖTEGELT API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) használatával egyetlen API-hívásban is elküldheti a lekérdezések kötegét.

1. A Poster területen kattintson az **új kérelem** | **Get kérelem** és a név it- **címek keresése**elemre.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, adja meg az API-végponthoz tartozó kérelem URL-címét, és válasszon ki egy hitelesítési protokollt, ha van ilyen.

![Keresési címek](./media/how-to-search-for-address/address_search_url.png)

| Paraméter | Ajánlott érték |
|---------------|------------------------------------------------| 
| HTTP-metódus | GET |
| Kérés URL-címe | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Engedélyezés | Nincs hitelesítés |

3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket lekérdezésként vagy elérésiút-paraméterekként kíván használni a kérelem URL-címében: 

![Keresési címek](./media/how-to-search-for-address/address_search_params.png) 

| Jelmagyarázat | Value (Díj) | 
|------------------|-------------------------| 
| API-Version | 1.0 | 
| előfizetés – kulcs | \<a Azure Maps kulcsot\> | 
| lekérdezés | 400 Broad St, Seattle, WA 98109 | 

4. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét. 

Ebben az esetben teljes címzési lekérdezést adott meg, és egyetlen eredményt kap a válasz törzsében. 

5. A params paraméterben szerkessze a lekérdezési karakterláncot a következő értékre: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra: 

| Jelmagyarázat | Value (Díj) | 
|-----|------------| 
| typeahead | igaz | 

A **typeahead** jelző azt jelzi, hogy a címek keresési API-ját részleges bemenetként kezeli a lekérdezés, és a prediktív értékek tömbjét adja vissza.

## <a name="using-fuzzy-search-api"></a>A fuzzy Search API használata

Azure Maps a[ fuzzy Search API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) -t ajánlott használni, ha nem tudja, mi a felhasználói bemenet egy keresési lekérdezéshez. Az API összekapcsolja az érdeklődési pont (POI) keresését és a helymeghatározáshoz egy canonical "single-line Search" kifejezéssel. Az API például képes kezelni bármilyen címek vagy POI-token kombinációjának bemeneteit. Az is lehet, hogy környezetfüggő pozícióval (lat./Lon. párosítva), teljes mértékben korlátozza a koordináta és a sugár, vagy általánosabban végrehajtja a földrajzi torzítási pontok nélkül.

A legtöbb keresési lekérdezés alapértelmezett értéke `maxFuzzyLevel=1` a teljesítmény eléréséhez és a szokatlan eredmények csökkentéséhez. Ez az alapértelmezett beállítás felülbírálható igény szerint, ha a lekérdezési paramétert `maxFuzzyLevel=2` vagy `3`átadja.

### <a name="search-for-an-address-using-fuzzy-search"></a>Címek keresése a fuzzy Search használatával

1. Nyissa meg a Poster alkalmazást, és kattintson az új | Hozzon létre egy újat, és válassza a **Get kérelem**lehetőséget. Adja meg a kérelem nevét a **fuzzy kereséshez**, válassza ki azt a gyűjteményt vagy mappát, ahová menteni szeretné, majd kattintson a **Save (Mentés**) gombra.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, és adja meg az API-végpont kérésének URL-címét.

    ![Fuzzy keresés](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Kérés URL-címe | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Engedélyezés | Nincs hitelesítés |

    Az URL-cím alatt található **JSON** -attribútum meghatározza a válasz formátumát. Ez a cikk a JSON-t használja a könnyű használat és az olvashatóság érdekében. A rendelkezésre álló válaszok formátumait a [Maps Functional API-referenciájának](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) **Get Search fuzzy** definíciójában találja.

3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket lekérdezésként vagy elérésiút-paraméterekként kíván használni a kérelem URL-címében:

    ![Fuzzy keresés](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Jelmagyarázat | Value (Díj) |
    |------------------|-------------------------|
    | API-Version | 1.0 |
    | előfizetés – kulcs | \<a Azure Maps kulcsot\> |
    | lekérdezés | pizza |

4. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét.

    A "pizza" nem egyértelmű lekérdezési karakterlánca a "pizza" és az "étterem" kategóriákban 10 hasznos [eredményt](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) adott vissza (POI). Minden eredmény az utca, a szélességi és a hosszúsági értékeket, a portok megtekintését és a hely belépési pontjait adja vissza.
  
    Ennek a lekérdezésnek az eredményei eltérőek, nem egy adott hivatkozási helyhez kötve. A **countrySet** paraméter használatával csak azokat az országokat/régiókat adhatja meg, amelyeknek az alkalmazásának lefedettségre van szüksége. Az alapértelmezett viselkedés a teljes világon való keresés, ami felesleges eredményeket ad vissza.

5. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra:

    | Jelmagyarázat | Value (Díj) |
    |------------------|-------------------------|
    | countrySet | Egyesült Államok |
  
    Az eredmények mostantól az országkód szerint vannak kötve, és a lekérdezés a Egyesült Államok pizzás éttermeit adja vissza.
  
    Egy adott hely eredményeinek megadásához lekérdezheti egy érdekes pontot, és a visszaadott szélességi és hosszúsági értékeket a meghívásban a fuzzy keresési szolgáltatásban lehet használni. Ebben az esetben a Search szolgáltatást használta a Seattle Space tű helyének visszaküldésére és a lat használatára. Lon. a keresés tájolására szolgáló értékek.
  
6. A params paraméterben adja meg a következő kulcs/érték párokat, majd kattintson a **Küldés**gombra:

    ![Fuzzy keresés](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Jelmagyarázat | Value (Díj) |
    |-----|------------|
    | Lat | 47,620525 |
    | Lon | – 122,349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Házszám keresése fordított címek használatával

Azure Maps a [keresési címek fordított API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) -val való lefordítása segít egy koordináta (például: 37,786505,-122,3862) egy ember által értelmezhető utcai címnek való lefordításában. Ez leggyakrabban olyan alkalmazások nyomon követéséhez szükséges, amelyekben az eszközről vagy eszközről érkező GPS-hírcsatornát kap, és tudni szeretné, hogy milyen címen található a koordináta.
Ha a geocode több koordináta-hellyel rendelkezik, a [post Search reverse batch API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) használatával egyetlen API-hívással küldhet le lekérdezési köteget.


1. A Poster területen kattintson az **új kérelem** | **Get kérelem** elemre, és adja meg a **fordított címtartomány-keresés**nevet.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, és adja meg az API-végpont kérésének URL-címét.
  
    ![Fordított cím keresési URL-címe](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Kérés URL-címe | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Engedélyezés | Nincs hitelesítés |
  
3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket lekérdezésként vagy elérésiút-paraméterekként kíván használni a kérelem URL-címében:
  
    ![Fordított címek keresési paramétereinek megadása](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Jelmagyarázat | Value (Díj) |
    |------------------|-------------------------|
    | API-Version | 1.0 |
    | előfizetés – kulcs | \<a Azure Maps kulcsot\> |
    | lekérdezés | 47.591180,-122,332700 |
  
4. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét.

    A válasz tartalmazza a Safeco mező legfontosabb információit.
  
5. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra:

    | Jelmagyarázat | Value (Díj) |
    |-----|------------|
    | szám | igaz |

    Ha a kérelemben a [Number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezési paramétert küldi a rendszer, akkor a válasz az utca oldalát (balra vagy jobbra), valamint az adott szám eltolási pozícióját is tartalmazhatja.
  
6. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra:

    | Jelmagyarázat | Value (Díj) |
    |-----|------------|
    | returnSpeedLimit | igaz |
  
    Ha a [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezési paraméter be van állítva, a válasz a közzétett sebesség korlátot adja vissza.

7. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra:

    | Jelmagyarázat | Value (Díj) |
    |-----|------------|
    | returnRoadUse | igaz |

    Ha a [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezési paraméter be van állítva, a válasz a közúti használatot a fordított geocodes az utca szintjén adja vissza.

8. Adja hozzá a következő kulcs/érték párokat a **params (paraméterek** ) szakaszhoz, majd kattintson a Send ( **Küldés**) gombra:

    | Jelmagyarázat | Value (Díj) |
    |-----|------------|
    | roadUse | igaz |

    A fordított geocode-lekérdezést egy adott típusú útra korlátozhatja a [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezési paraméter használatával.
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>A Cross Street keresése fordított címekkel Cross Street Search használatával

1. A Poster területen kattintson az **új kérelem** | **Get kérelem** elemre, és nevezze **vissza a Cross Street Search kifejezésre**.

2. A Builder (szerkesztő) lapon válassza **a http lekérése** módszert, és adja meg az API-végpont kérésének URL-címét.
  
    ![Fordított címek Cross Street Search](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Kérés URL-címe | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Engedélyezés | Nincs hitelesítés |
  
3. Kattintson a **Paraméterek**elemre, és adja meg a következő kulcs/érték párokat, amelyeket lekérdezésként vagy elérésiút-paraméterekként kíván használni a kérelem URL-címében:
  
    | Jelmagyarázat | Value (Díj) |
    |------------------|-------------------------|
    | API-Version | 1.0 |
    | előfizetés – kulcs | \<a Azure Maps kulcsot\> |
    | lekérdezés | 47.591180,-122,332700 |
  
4. Kattintson a **Küldés** gombra, és tekintse át a válasz törzsét.

## <a name="next-steps"></a>Következő lépések

- Fedezze fel a [Azure Maps Search szolgáltatás](https://docs.microsoft.com/rest/api/maps/search) API dokumentációját.
- További információ az [ajánlott eljárásokról](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search).