---
title: Az Azure Search-Maps szolgáltatással cím keresése |} Microsoft Docs
description: Megtudhatja, hogyan keresse meg az Azure-leképezések Search szolgáltatás segítségével
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1acb95af7b62641c371627d6250067f9c2eac99c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320764"
---
# <a name="how-to-find-an-address-using-the-azure-maps-search-service"></a>Hol találhatók a Azure Maps search szolgáltatás segítségével

A Maps – keresés szolgáltatást egy egy RESTful API-k, címek, a helyek, a pontokat iránt érdeklődik, üzleti listák és egyéb földrajzi információk keresése fejlesztők számára készült. A szolgáltatás a szélesség/hosszúsági egy adott cím, alhálózatok közötti utca, földrajzi funkció vagy információs jel (POI) rendeli. A keresés által visszaadott szélességi és hosszúsági értékeket egyéb Maps szolgáltatásokat, mint útvonal és paramétereiben használható.

## <a name="prerequisites"></a>Előfeltételek

A hívások a Maps szolgáltatás API-k, a Maps-fiókot és kulcsot kell. Fiók létrehozása és egy kulcs lekérése kapcsolatos tudnivalókat lásd: [Azure Maps fiókja és -kulcsok kezelése](how-to-manage-account-keys.md).

Ebben a cikkben az a [Postman app](https://www.getpostman.com/apps) hozhat létre REST-hívások. Használhat egyetlen előnyben részesített API fejlesztési környezetet. 


## <a name="using-fuzzy-search"></a>Intelligens egyeztetésű keresési

Az alapértelmezett az API a keresési szolgáltatás [intelligens egyeztetésű keresési](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy), amely kezeli a bemenetek tetszőleges kombinációjából cím vagy a POI-jogkivonatokat. Ez a keresés API a kanonikus "egysoros keresési", és akkor hasznos, ha a felhasználói bevitel keresési lekérdezés nem ismeri. Az intelligens egyeztetésű keresési API-JÁNAK POI-keresés és a geokódolás. Az API-t is képes súlyozott környezetfüggő pozícióhoz (LAT/maga. párosítsa), teljes mértékben korlátozza a koordináta és a RADIUS-, vagy a rögzítési pont hordozóeltolást földrajzi nélkül általában végrehajtható.

A legtöbb keresési lekérdezések alapértelmezés szerint a "maxFuzzyLevel = 1" szerezzen a teljesítmény és szokatlan eredmények. Ez az alapértelmezett történő a következő lekérdezésparaméter a kérelmenként szükség szerint felülbírálható "maxFuzzyLevel = 2" vagy "3".

### <a name="search-for-an-address-using-fuzzy-search"></a>Az intelligens egyeztetésű keresési cím keresése

1. Nyissa meg a Postman alkalmazást, kattintson az új |} Hozzon létre új, és válassza **GET kérés**. Adjon meg egy kérelem **intelligens egyeztetésű keresési**, válassza ki a gyűjtemény vagy mappába menti, és kattintson a **mentése**.

2. A jelentéskészítő lapon jelölje be a **beolvasása** HTTP-metódus, és adja meg a kérelem URL-címet a API-végpontot.

    ![Intelligens egyeztetésű keresési ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Lekérdezés URL-címe | https://atlas.microsoft.com/search/fuzzy/json? |
    | Engedélyezés | Nincs hitelesítés |

    A **json** URL-címet attribútum meghatározza, hogy a válasz formátumát. Ez a cikk teljes json és olvashatóság érdekében használja. Megtalálhatja a rendelkezésre álló válasz formátumok a **keresési intelligens beolvasása** a [Maps működési API-referencia] definíciója (https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

3. Kattintson a **paraméterei**, és írja be a következő kulcs-érték párok lekérdezés vagy az elérési út paraméter, a kérelem URL-címében használni:

    ![Intelligens egyeztetésű keresési ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Kulcs | Érték |
    |------------------|-------------------------|
    | API-verzió | 1.0 |
    | Előfizetés-kulcs | \<az Azure a Maps-kulcs\> |
    | lekérdezés | pizzaszósz |

4. Kattintson a **küldése** és tekintse át az adott válasz törzse. 

    A nem egyértelmű lekérdezési karakterlánc a "pizzaszósz" érdeklődési (POI) eredmények 10 pont alá tartozó "pizzaszósz" és "éttermi" kategóriák hibát adott vissza. Minden eredményt adja vissza egy utca, házszám, szélesség / hosszúsági értékeknek-portnak és a hely belépési pontok megtekintéséhez.
    
    Az eredmények változnak, ehhez a lekérdezéshez, különösen bárhova kötve. Használhatja a **countrySet** paraméterrel adhatja meg, amelynek az alkalmazás kell érvényességének, országok, az alapértelmezés lesz a teljes globális, potenciálisan visszaadó szükségtelen eredmények kereséséhez.

5. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |------------------|-------------------------|
    | countrySet | USA |
    
    Az eredmények most időpontjaihoz az országkódot, és a lekérdezés visszaadja pizzaszósz éttermekben az Amerikai Egyesült Államokban.
    
    Egy adott helyen készült találatokat adjon vissza, az érdeklődési pont lekérdezése, és a visszaadott szélességi és hosszúsági értékeket használni az intelligens egyeztetésű keresési szolgáltatás hívása. Ebben az esetben a keresési szolgáltatás ad vissza a budapesti terület tű helyét, és használja a LAT / maga. Ha a keresés értékeket.
    
4. A paraméterei, írja be a következő kulcs / érték párok, és kattintson a **küldése**:

    ![Intelligens egyeztetésű keresési ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Kulcs | Érték |
    |-----|------------|
    | LAT | 47.62039 |
    | maga a merevlemez | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Keresse meg és a cím tulajdonságai 

A teljes vagy részleges utca, házszám átadása a keresési cím API, és részletes cím tulajdonságait, például település vagy felosztása, valamint a pozicionális értékeket tartalmaz a szélességi és hosszúsági választ kapnak.

1. Postman, kattintson **új kérelem** | **GET kérés** és adjon neki nevet **cím keresési**.
2. A jelentéskészítő lapon jelölje be a **beolvasása** HTTP-metódus, adja meg a kérelem URL-címet a API-végpontot, és válassza ki a hitelesítési protokoll, ha van ilyen.

    ![Cím keresése ](./media/how-to-search-for-address/address_search_url.png)
    
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Lekérdezés URL-címe | https://atlas.microsoft.com/search/address/json? |
    | Engedélyezés | Nincs hitelesítés |

2. Kattintson a **paraméterei**, és írja be a következő kulcs-érték párok lekérdezés vagy az elérési út paraméter, a kérelem URL-címében használni:
    
    ![Cím keresése ](./media/how-to-search-for-address/address_search_params.png)
    
    | Kulcs | Érték |
    |------------------|-------------------------|
    | API-verzió | 1.0 |
    | Előfizetés-kulcs | \<az Azure a Maps-kulcs\> |
    | lekérdezés | 400 széles St, Seattle, WA 98109 |
    
3. Kattintson a **küldése** és tekintse át az adott válasz törzse. 
    
    Ebben az esetben megadott egy teljes címet lekérdezést, és az adott válasz törzse egyetlen eredmény fogadása. 
    
4. A paraméterei szerkesztheti a lekérdezési karakterláncban a következő értékre:
    ```
        400 Broad, Seattle
    ```

5. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | typeahead | true |

    A **typeahead** jelző utasítja a cím keresési API-JÁNAK, ha a lekérdezés egy részleges bemenetként és prediktív értékek tömbjét adja vissza.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Keresse meg a cím keresési fordított utca, házszám
1. Postman, kattintson **új kérelem** | **GET kérés** és adjon neki nevet **fordított cím keresési**.

2. A jelentéskészítő lapon jelölje be a **beolvasása** HTTP-metódus, és adja meg a kérelem URL-címet a API-végpontot.
    
    ![Fordított cím keresési URL-címe ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Lekérdezés URL-címe | https://atlas.microsoft.com/search/address/reverse/json? |
    | Engedélyezés | Nincs hitelesítés |
    
2. Kattintson a **paraméterei**, és írja be a következő kulcs-érték párok lekérdezés vagy az elérési út paraméter, a kérelem URL-címében használni:
    
    ![Fordított cím keresési paraméterek ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Kulcs | Érték |
    |------------------|-------------------------|
    | API-verzió | 1.0 |
    | Előfizetés-kulcs | \<az Azure a Maps-kulcs\> |
    | lekérdezés | 47.59093,-122.33263 |
    
3. Kattintson a **küldése** és tekintse át az adott válasz törzse. 
    
    Egy "stadium" poi-kategóriát a válasz tartalmazza a POI bejegyzést Safeco mező. 
    
4. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | szám | true |

    Ha a [szám](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezésparaméter a kérelem nem jut, a válasz előfordulhat, hogy tartalmazza a utca (balra vagy jobbra), és az eltolás pozíciója, hogy a kívánt oldalán.
    
5. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | spatialKeys | true |

    Ha a [spatialKeys](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter értéke, a válasz egy adott hely védett földrajzi térbeli kulcsfontosságú adatokat tartalmaz.

6. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | returnSpeedLimit | true |
    
    Ha a [returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter értéke, a válasz visszaadása, a közzétett Sebességkorlát.

7. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | returnRoadUse | true |

    Ha a [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter értéke, a választ ad vissza, a közúti használható tömb reversegeocodes utca szinten.

8. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | roadUse | true |

    A fordított geocode lekérdezés egy adott típusú közúti használata használatával korlátozhatja a [roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter.
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Keresse meg a keresztszűrés utca fordított cím határokon utca keresés

1. Postman, kattintson **új kérelem** | **GET kérés** és adjon neki nevet **cím fordított utca keresési Cross**.

2. A jelentéskészítő lapon jelölje be a **beolvasása** HTTP-metódus, és adja meg a kérelem URL-címet a API-végpontot.
    
    ![Cím határokon utca keresési fordított ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Lekérdezés URL-címe | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Engedélyezés | Nincs hitelesítés |
    
3. Kattintson a **paraméterei**, és írja be a következő kulcs-érték párok lekérdezés vagy az elérési út paraméter, a kérelem URL-címében használni:
    
    | Kulcs | Érték |
    |------------------|-------------------------|
    | API-verzió | 1.0 |
    | Előfizetés-kulcs | \<az Azure a Maps-kulcs\> |
    | lekérdezés | 47.59093,-122.33263 |
    
4. Kattintson a **küldése** és tekintse át az adott válasz törzse. 

## <a name="next-steps"></a>További lépések
- Megismerkedhet a [Azure Maps keresési szolgáltatás](https://docs.microsoft.com/rest/api/maps/search) API-JÁNAK dokumentációja 
