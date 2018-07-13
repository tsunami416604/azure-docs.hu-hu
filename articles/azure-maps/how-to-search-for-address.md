---
title: Keresse meg egy címet az Azure Maps keresési szolgáltatással hogyan |} A Microsoft Docs
description: Ismerje meg, hogyan keresse meg egy címet az Azure Maps Search szolgáltatással
author: dsk-2015
ms.author: dkshir
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 8b7d2119e1eef8532c30b0a45ae2684493462277
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990013"
---
# <a name="how-to-find-an-address-using-the-azure-maps-search-service"></a>Hogyan találhatja meg egy címet az Azure Maps search szolgáltatással

A Maps keresési szolgáltatás egy egy RESTful API-k, címek, helyek, érdeklődés, cégjegyzékeket és egyéb földrajzi adatok pontok kereséséhez fejlesztők számára készült. A szolgáltatás a szélességi és hosszúsági koordinátákkal rendel egy adott cím, a közötti utca, a földrajzi funkció vagy a hasznos helyekre vezető útvonalak (KOORDINÁTÁIIG). A keresés által visszaadott szélességi és hosszúsági értékeket más Maps szolgáltatások, például útvonal-és forgalmi paramétereiben használható.

## <a name="prerequisites"></a>Előfeltételek

Bármely hívásokat a Maps szolgáltatás API-k, szüksége van egy Maps-fiók és a kulcsot. Hozzon létre egy fiókot, és a egy kulcs lekérése kapcsolatos tudnivalókat lásd: [az Azure Maps-fiók és kulcsok kezelése](how-to-manage-account-keys.md).

Ez a cikk a [Postman alkalmazás](https://www.getpostman.com/apps) hozhat létre REST-hívások. Minden API fejlesztési környezetet, amely igény szerint is használhatja. 


## <a name="using-fuzzy-search"></a>Az intelligens keresés használata

A search szolgáltatás az alapértelmezett API [intelligens keresés](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy), amely kezeli a cím vagy KOORDINÁTÁIIG tokenek tetszőleges kombinációját bemenetei. A search API a canonical "Egysoros keresés", és akkor hasznos, ha nem ismeri a felhasználói bevitelek, egy keresési lekérdezést. Az intelligens keresés API KOORDINÁTÁIIG keresési és geokódolási kombinációját is. Az API-t is képes súlyozott környezetfüggő pozícióhoz (LAT/maga. párosítsa), teljes mértékben korlátozott koordináta és radius, vagy minden olyan földrajzi rögzítési pontjának hordozóeltolást nélkül általában végrehajtható.

A legtöbb keresési lekérdezések alapértelmezés szerint a "maxFuzzyLevel = 1' teljesítményt és csökkentheti a szokatlan eredményeket. Ez az alapértelmezett a lekérdezési paraméter megadásával kérelmenként szükség szerint felülbírálható "maxFuzzyLevel = 2" vagy "3".

### <a name="search-for-an-address-using-fuzzy-search"></a>Az intelligens keresés a cím keresése

1. Nyissa meg a Postman alkalmazást, kattintson az új |} Hozzon létre új, és válassza ki **GET kérelem**. Adjon meg egy kérelem **intelligens keresés**, válasszon ki egy gyűjtemény vagy a mappát, mentse, majd kattintson a **mentése**.

2. A jelentéskészítő lapon válassza ki a **első** HTTP-metódust, és adja meg a kérelem URL-címe az API-végpont.

    ![Intelligens keresést ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Lekérdezés URL-címe | https://atlas.microsoft.com/search/fuzzy/json? |
    | Engedélyezés | Nincs hitelesítés |

    A **json** az URL-címet az attribútum határozza meg, a válasz formátumban. Ez a cikk teljes json a könnyű használat és az olvashatóságot használ. Megtalálhatja a rendelkezésre álló válasz formátumok a **keresés intelligens lekérése** definíciója a [Maps funkcionális API-referencia] (https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Kattintson a **paraméterei**, és adja meg a következő kulcs / érték párok lekérdezés vagy elérési út paramétereiben a kérelem URL-cím használata:

    ![Intelligens keresést ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Kulcs | Érték |
    |------------------|-------------------------|
    | API-verzió | 1.0 |
    | Előfizetés-kulcs | \<az Azure Maps-kulcs\> |
    | lekérdezés | kétpizzás |

4. Kattintson a **küldése** , és tekintse át a válasz törzse. 

    A nem egyértelmű lekérdezési karakterlánc a "kétpizzás" érdeklődés (KOORDINÁTÁIIG) eredmény 10 pontos alá tartozó, a "kétpizzás" és "éttermi" kategóriák hibát adott vissza. Minden eredményt adja vissza egy utca, házszám, szélességi / hosszúsági értékeket, megtekintheti a port és a hely elérésére.
    
    Az eredmények ebben a lekérdezésben nem kötődik bármely adott hivatkozás helyére a különböző. Használhatja a **countrySet** paraméterrel adja meg a csak az országok, amelynek az alkalmazásnak kell lefedettség, mivel alapértelmezés szerint kereshet az egész világ, potenciálisan a szükségtelen eredményt adnak vissza.

5. Adja hozzá a következő kulcs / érték-pár, a **paraméterei** szakaszt, és kattintson a **küldése**:

    | Kulcs | Érték |
    |------------------|-------------------------|
    | countrySet | USA |
    
    Az eredmények most határolt az országkódot, és a lekérdezés visszaadja az kétpizzás éttermek az Egyesült Államokban.
    
    Ahhoz, hogy egy adott helyen található-orientált eredményeket, lekérdezheti egy hasznos hely megkereséséhez és az intelligens keresés szolgáltatás hívása a visszaadott szélességi és hosszúsági értékeket használja. Ebben az esetben a keresési szolgáltatás segítségével a Seattle terület tű helyét fogja visszaadni, és használja a LAT / maga. Ha a keresés értékeket.
    
4. Paraméterei, adja meg a következő kulcs / érték párok, és kattintson a **küldése**:

    ![Intelligens keresést ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Kulcs | Érték |
    |-----|------------|
    | szél | 47.62039 |
    | maga a merevlemez | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Cím tulajdonságai és koordináták keresése 

Egy teljes vagy részleges utca, házszám át a keresés cím API, és a szélességi és hosszúsági részletes cím tulajdonságait, például település vagy felosztása, valamint a Helyzetbeállító értékeket tartalmazó választ kapnak.

1. Kattintson a Postman **új kérelem** | **GET kérelem** , és nevezze el **cím keresési**.
2. A jelentéskészítő lapon válassza ki a **első** HTTP-metódust, adja meg a kérelem URL-címe az API-végpont, és válassza ki egy engedélyezési protokollt, ha van ilyen.

    ![Cím keresése ](./media/how-to-search-for-address/address_search_url.png)
    
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Lekérdezés URL-címe | https://atlas.microsoft.com/search/address/json? |
    | Engedélyezés | Nincs hitelesítés |

2. Kattintson a **paraméterei**, és adja meg a következő kulcs / érték párok lekérdezés vagy elérési út paramétereiben a kérelem URL-cím használata:
    
    ![Cím keresése ](./media/how-to-search-for-address/address_search_params.png)
    
    | Kulcs | Érték |
    |------------------|-------------------------|
    | API-verzió | 1.0 |
    | Előfizetés-kulcs | \<az Azure Maps-kulcs\> |
    | lekérdezés | 400 széles körű St, Seattle, WA 98109 |
    
3. Kattintson a **küldése** , és tekintse át a válasz törzse. 
    
    Ebben az esetben a megadott egy teljes cím lekérdezést, és egyetlen eredményt kap, a válasz törzsében. 
    
4. Paraméterei szerkeszteni a lekérdezési karakterlánc a következő értékre:
    ```
        400 Broad, Seattle
    ```

5. Adja hozzá a következő kulcs / érték-pár, a **paraméterei** szakaszt, és kattintson a **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | typeahead | true |

    A **typeahead** jelző arra utasítja a cím Search API, ha a lekérdezés egy részleges bemenetként, és prediktív értékek tömbjét adja vissza.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Keresse meg a cím-keresési fordított utca, házszám
1. Kattintson a Postman **új kérelem** | **GET kérelem** , és nevezze el **fordított cím-keresési**.

2. A jelentéskészítő lapon válassza ki a **első** HTTP-metódust, és adja meg a kérelem URL-címe az API-végpont.
    
    ![Fordított keresés URL-címe ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Lekérdezés URL-címe | https://atlas.microsoft.com/search/address/reverse/json? |
    | Engedélyezés | Nincs hitelesítés |
    
2. Kattintson a **paraméterei**, és adja meg a következő kulcs / érték párok lekérdezés vagy elérési út paramétereiben a kérelem URL-cím használata:
    
    ![Fordított cím keresési paraméterek ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Kulcs | Érték |
    |------------------|-------------------------|
    | API-verzió | 1.0 |
    | Előfizetés-kulcs | \<az Azure Maps-kulcs\> |
    | lekérdezés | 47.59093,-122.33263 |
    
3. Kattintson a **küldése** , és tekintse át a válasz törzse. 
    
    A válasz a KOORDINÁTÁIIG bejegyzés tartalmazza a Safeco Field egy "stadiont" koordinátáiig kategóriába. 
    
4. Adja hozzá a következő kulcs / érték-pár, a **paraméterei** szakaszt, és kattintson a **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | szám | true |

    Ha a [szám](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paramétert a kérés érkezik, a válasz tartalmazhatja az utca (balra vagy jobbra), és egy számot eltolási pozíciót oldalán.
    
5. Adja hozzá a következő kulcs / érték-pár, a **paraméterei** szakaszt, és kattintson a **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | spatialKeys | true |

    Ha a [spatialKeys](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter értéke, a válasz szellemi tulajdont képező térinformatikai kulcsot tartalmazza egy adott helyre vonatkozóan.

6. Adja hozzá a következő kulcs / érték-pár, a **paraméterei** szakaszt, és kattintson a **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | returnSpeedLimit | true |
    
    Ha a [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter értéke, a válaszban visszaadandó, a közzétett sebességkorlátozás.

7. Adja hozzá a következő kulcs / érték-pár, a **paraméterei** szakaszt, és kattintson a **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | returnRoadUse | true |

    Ha a [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter értéke, a válasz adja vissza az utca szintjén reversegeocodes a út használata tömb.

8. Adja hozzá a következő kulcs / érték-pár, a **paraméterei** szakaszt, és kattintson a **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | roadUse | true |

    A geocode fordított lekérdezés egy adott típusú út használata használatával korlátozhatja a [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter.
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Keresse meg a közötti street fordított cím közötti utca keresés használata

1. Kattintson a Postman **új kérelem** | **GET kérelem** , és nevezze el **cím fordított Street keresési Adatbázisközi**.

2. A jelentéskészítő lapon válassza ki a **első** HTTP-metódust, és adja meg a kérelem URL-címe az API-végpont.
    
    ![Cím közötti Street keresési fordított ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Lekérdezés URL-címe | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Engedélyezés | Nincs hitelesítés |
    
3. Kattintson a **paraméterei**, és adja meg a következő kulcs / érték párok lekérdezés vagy elérési út paramétereiben a kérelem URL-cím használata:
    
    | Kulcs | Érték |
    |------------------|-------------------------|
    | API-verzió | 1.0 |
    | Előfizetés-kulcs | \<az Azure Maps-kulcs\> |
    | lekérdezés | 47.59093,-122.33263 |
    
4. Kattintson a **küldése** , és tekintse át a válasz törzse. 

## <a name="next-steps"></a>További lépések
- Fedezze fel a [az Azure Maps keresési szolgáltatás](https://docs.microsoft.com/rest/api/maps/search) API-dokumentáció 
