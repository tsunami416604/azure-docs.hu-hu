---
title: Hely keresése az Azure Maps keresési szolgáltatásaival | Microsoft Azure Maps
description: Ebből a cikkből megtudhatja, hogyan kereshet egy helyet a Microsoft Azure Térkép keresőszolgáltatása segítségével a geokódoláshoz és a fordított geokódoláshoz.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335429"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Hely keresése az Azure Maps keresési szolgáltatásaival

Az Azure Maps [keresési szolgáltatás](https://docs.microsoft.com/rest/api/maps/search) egy sor RESTful API-k célja, hogy segítse a fejlesztőket, hogy keressen címek, helyek, üzleti listák név vagy kategória, és egyéb földrajzi információkat. A hagyományos geokódolás támogatása mellett a szolgáltatások a földrajzi kód címeket és a keresztutcákat is megfordíthatják a szélességi és hosszúsági fokok alapján. A keresés által visszaadott szélességi és hosszúsági értékek paraméterekként használhatók más Azure Maps-szolgáltatásokban, például [az Útvonal-](https://docs.microsoft.com/rest/api/maps/route) és időjárás-szolgáltatásokban. [Weather](https://docs.microsoft.com/rest/api/maps/weather)

Ebben a cikkben megtudhatja, hogyan:

* Szélességi és hosszúsági koordináták kérése egy címhez (geokód cím helye) a [Search Address API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) használatával
* Cím vagy érdekes pont (POI) keresése a [Fuzzy Search API használatával](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* Cím keresése tulajdonságokkal és koordinátákkal együtt
* Fordított [cím keresése a](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) koordináta helyének utcanévre történő fordításához
* Keresztutca keresése a [Search Address Reverse Cross Street API használatával](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)

## <a name="prerequisites"></a>Előfeltételek

A cikkben leírt lépések végrehajtásához először létre kell hoznia egy Azure Maps-fiókot, és le képezheti a fiók-előfizetési kulcsot. Kövesse a [Fiók létrehozása](quick-demo-map-app.md#create-an-account-with-azure-maps) című útmutató utasításait az Azure Maps-fiók-előfizetés létrehozásához, és kövesse az elsődleges kulcs [beszerezése](quick-demo-map-app.md#get-the-primary-key-for-your-account) lépéseit a fiók elsődleges kulcsának lekérnie. Az Azure Maps hitelesítéssel kapcsolatos további tudnivalókról az [Azure Maps hitelesítésének kezelése](./how-to-manage-authentication.md)című témakörben talál.

Ez a cikk a [Postman alkalmazást](https://www.getpostman.com/apps) használja rest-hívások létrehozásához. Bármilyen API-fejlesztői környezetet használhat, amelyet szeretne.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Kérelem szélességi és hosszúsági egy cím (geokódolás)

Ebben a példában az Azure Maps [keresési cím beérkezése API-t](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) használunk az utcacím szélességi és hosszúsági koordinátákká alakításához. Teljes vagy részleges utcacímet adhat át az API-nak, és olyan választ kaphat, amely részletes címtulajdonságokat tartalmaz, például az utcát, az irányítószámot és az országot/régiót, valamint a helymeghatározási értékeket a szélességi és hosszúsági fokon.

Ha van egy sor címeket geokód, használhatja [A keresés könyvelése cím batch API-t](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) küldeni egy köteg lekérdezések egyetlen API-hívás.

1. A Postman alkalmazásban kattintson az **Új kérelem** | **get kérésére,** és nevezze el **címkeresésnek.**

2. A Szerkesztő lapon válassza a **GET** HTTP metódust, adja meg az API-végpont kérelem URL-címét, és válasszon ki egy engedélyezési protokollt, ha van ilyen.

![Címkeresése](./media/how-to-search-for-address/address_search_url.png)

| Paraméter | Ajánlott érték |
|---------------|------------------------------------------------| 
| HTTP method | GET |
| Kérés URL-címe | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| Engedélyezés | Nincs hitelesítés |

3. Kattintson **a Params**gombra, és írja be a következő kulcs/érték párokat, amelyeket lekérdezési vagy elérési út paraméterekként szeretne használni a kérelem URL-címében: 

![Címkeresése](./media/how-to-search-for-address/address_search_params.png) 

| Kulcs | Érték | 
|------------------|-------------------------| 
| api-verzió | 1.0 | 
| előfizetés-kulcs | \<az Azure Maps kulcs\> | 
| lekérdezés | 400 Széles St, Seattle, WA 98109 | 

4. Kattintson **a Küldés** gombra, és tekintse át a választörzset. 

Ebben az esetben megadott egy teljes címlekérdezést, és egyetlen eredményt kapott a választörzsben. 

5. A Params alkalmazásban a következő értékre szerkesztheti a lekérdezési karakterláncot: 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. Adja hozzá a következő kulcs/ érték párt a **Params** szakaszhoz, és kattintson a **Küldés**gombra: 

| Kulcs | Érték | 
|-----|------------| 
| typeahead (típuselőre) | igaz | 

A **typeahead** jelző azt mondja az Address Search API-t, hogy a lekérdezést részleges bemenetként kezelje, és egy sor prediktív értéket adjon vissza.

## <a name="using-fuzzy-search-api"></a>A Fuzzy Search API használata

Az Azure Maps[ Fuzzy Search API-t](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) akkor ajánlott használni, ha nem tudja, hogy a felhasználói bemenetek mit jelentenek a keresési lekérdezéshez. Az API egyesíti az érdekes (POI) keresést és a geokódolást egy gyűjtő "egysoros keresésben". Az API például bármely cím- vagy POI-tokenkombináció bemeneteit képes kezelni. Kontextuális pozícióval (lat./lon) is súlyozható. páros), teljes mértékben koordináta és sugár korlátozza, vagy általánosabban, geotorzító rögzítési pont nélkül hajtják végre.

A legtöbb keresési `maxFuzzyLevel=1` lekérdezés alapértelmezés szerint teljesítményre tesz szert, és csökkenti a szokatlan eredményeket. Ez az alapértelmezett hiba kérésenként felülbírálható a lekérdezési paraméter `maxFuzzyLevel=2` vagy `3`a átadásával.

### <a name="search-for-an-address-using-fuzzy-search"></a>Cím keresése a Fuzzy Search használatával

1. A Postman alkalmazás megnyitása az Új | Hozzon létre újat, és válassza **a GET kérés lehetőséget.** Adja meg az **intelligens keresés**kérése nevét, jelölje ki azt a gyűjteményt vagy mappát, amelybe menteni szeretné, majd kattintson a Mentés **gombra.**

2. A Szerkesztő lapon válassza a **GET** HTTP metódust, és adja meg az API-végpont kérelem URL-címét.

    ![Fuzzy keresés](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | Kérés URL-címe | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | Engedélyezés | Nincs hitelesítés |

    Az URL elérési út **json** attribútuma határozza meg a válasz formátumát. Ez a cikk a json-t használja a könnyű használat és olvashatóság érdekében. A rendelkezésre álló válaszformátumokat a [Térképek funkcionális API-hivatkozás](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) **Keresés fuzzy** definíciójában találja.

3. Kattintson **a Params**gombra, és írja be a következő kulcs/érték párokat, amelyeket lekérdezési vagy elérési út paraméterekként szeretne használni a kérelem URL-címében:

    ![Fuzzy keresés](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Kulcs | Érték |
    |------------------|-------------------------|
    | api-verzió | 1.0 |
    | előfizetés-kulcs | \<az Azure Maps kulcs\> |
    | lekérdezés | Pizza |

4. Kattintson **a Küldés** gombra, és tekintse át a választörzset.

    A "pizza" kétértelmű lekérdezési [karakterlánca](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) 10 érdekes pontot (POI) adott vissza mind a "pizza", mind az "étterem" kategóriában. Minden eredmény egy utcacímet, szélességi és hosszúsági értékeket, nézetportot és belépési pontokat ad vissza a helyhez.
  
    Az eredmények változatosak ehhez a lekérdezéshez, nem kötődnek egyetlen hivatkozási helyhez sem. A **countrySet** paraméter rel megadhatja, hogy csak azok az országok/régiók, amelyekre az alkalmazásnak fedezetre van szüksége. Az alapértelmezett viselkedés az, hogy keressen az egész világon, potenciálisan visszatérő felesleges eredményeket.

5. Adja hozzá a következő kulcs/ érték párt a **Params** szakaszhoz, és kattintson a **Küldés**gombra:

    | Kulcs | Érték |
    |------------------|-------------------------|
    | countrySet | USA |
  
    Az eredményeket most az országkód határolja, és a lekérdezés az Egyesült Államokban található pizzaéttermeket adja vissza.
  
    Egy hely találatainak biztosításához lekérdezhet egy érdekes pontot, és használhatja a visszaadott szélességi és hosszúsági értékeket az Intelligens keresés szolgáltatás hívásában. Ebben az esetben a Keresési szolgáltatással visszaadhatja a Seattle Space Needle helyét, és a lat-ot használta. - Lon. értékeket a keresés tájolásához.
  
6. A Params alkalmazásban írja be a következő kulcs/ értékpárokat, és kattintson a **Küldés**gombra:

    ![Fuzzy keresés](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Kulcs | Érték |
    |-----|------------|
    | Lat | 47.620525 |
    | Lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>Utcanév keresése fordított címkereséssel

Az Azure Maps [keresési cím fordított API-jának befordítása]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) segít a koordináta (például: 37.786505, -122.3862) ember érthető utcacímké fordítása. Leggyakrabban erre olyan nyomon követési alkalmazásoknál van szükség, ahol GPS-hírcsatornát kap az eszközről vagy eszközről, és szeretné tudni, hogy a koordináta melyik címe található.
Ha rendelkezik egy sor koordináta-helyeket a geokód visszafordításához, a [Keresési cím utáni fordított köteg API-val](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) egyetlen API-hívásban küldhet lekérdezések kötegét.


1. A Postman alkalmazásban kattintson az **Új kérelem** | **get kérésére,** és nevezze **el fordított címkeresésnek.**

2. A Szerkesztő lapon válassza a **GET** HTTP metódust, és adja meg az API-végpont kérelem URL-címét.
  
    ![Fordított címkeresési URL-cím](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | Kérés URL-címe | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | Engedélyezés | Nincs hitelesítés |
  
3. Kattintson **a Params**gombra, és írja be a következő kulcs/érték párokat, amelyeket lekérdezési vagy elérési út paraméterekként szeretne használni a kérelem URL-címében:
  
    ![Címkeresési paraméterek megfordítása](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Kulcs | Érték |
    |------------------|-------------------------|
    | api-verzió | 1.0 |
    | előfizetés-kulcs | \<az Azure Maps kulcs\> |
    | lekérdezés | 47.591180,-122.332700 |
  
4. Kattintson **a Küldés** gombra, és tekintse át a választörzset.

    A válasz tartalmazza a Safeco Field legfontosabb címadatait.
  
5. Adja hozzá a következő kulcs/ érték párt a **Params** szakaszhoz, és kattintson a **Küldés**gombra:

    | Kulcs | Érték |
    |-----|------------|
    | szám | igaz |

    Ha a [számlekérdezési](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) paramétert a kéréssel együtt küldi el, a válasz tartalmazhatja az utca oldalát (Balra vagy Jobbra) és egy eltolási pozíciót is a számhoz.
  
6. Adja hozzá a következő kulcs/ érték párt a **Params** szakaszhoz, és kattintson a **Küldés**gombra:

    | Kulcs | Érték |
    |-----|------------|
    | returnSpeedLimit | igaz |
  
    Ha a [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezési paraméter be van állítva, a válasz a feladott sebességkorlátot adja vissza.

7. Adja hozzá a következő kulcs/ érték párt a **Params** szakaszhoz, és kattintson a **Küldés**gombra:

    | Kulcs | Érték |
    |-----|------------|
    | visszaRoadUse | igaz |

    Ha a [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezési paraméter be van állítva, a válasz az utcai szintű fordított geokódok úthasználati tömbjét adja vissza.

8. Adja hozzá a következő kulcs/ érték párt a **Params** szakaszhoz, és kattintson a **Küldés**gombra:

    | Kulcs | Érték |
    |-----|------------|
    | útHasználata | igaz |

    A fordított geokód lekérdezést egy adott típusú útra korlátozhatja a [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) lekérdezési paraméter használatával.
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Keresztutca keresése fordított cím keresztutcai kereséssel

1. A Postman, kattintson **az Új kérelem** | **GET kérelmet,** és nevezze **meg reverse address Cross Street Search**.

2. A Szerkesztő lapon válassza a **GET** HTTP metódust, és adja meg az API-végpont kérelem URL-címét.
  
    ![Fordított cím Cross Street Search](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | Kérés URL-címe | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | Engedélyezés | Nincs hitelesítés |
  
3. Kattintson **a Params**gombra, és írja be a következő kulcs/érték párokat, amelyeket lekérdezési vagy elérési út paraméterekként szeretne használni a kérelem URL-címében:
  
    | Kulcs | Érték |
    |------------------|-------------------------|
    | api-verzió | 1.0 |
    | előfizetés-kulcs | \<az Azure Maps kulcs\> |
    | lekérdezés | 47.591180,-122.332700 |
  
4. Kattintson **a Küldés** gombra, és tekintse át a választörzset.

## <a name="next-steps"></a>További lépések

- Ismerje meg az [Azure Maps Search Service REST API dokumentációját.](https://docs.microsoft.com/rest/api/maps/search)
- Ismerje meg [az Azure Maps keresési szolgáltatás ajánlott eljárásokat,](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search) és hogyan optimalizálhatja a lekérdezéseket.
