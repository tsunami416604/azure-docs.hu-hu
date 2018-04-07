---
title: Az Azure-alapú helyszolgáltatás (előzetes verzió) keresési szolgáltatással cím keresése |} Microsoft Docs
description: Megtudhatja, hogyan keresse meg az Azure-alapú helyszolgáltatás (előzetes verzió) – keresés szolgáltatást segítségével
services: location-based-services
author: kgremban
ms.author: kgremban
ms.date: 11/29/2017
ms.topic: article
ms.service: location-based-services
ms.openlocfilehash: f8b886607fe0915396a659593cd5910a271fca93
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-find-an-address-using-the-azure-location-based-services-preview-search-service"></a>Hol találhatók az Azure-alapú helyszolgáltatás (előzetes verzió) keresési szolgáltatás segítségével
A keresési szolgáltatás egy egy RESTful API-k, címek, a helyek, a pontokat iránt érdeklődik, üzleti listák és egyéb földrajzi információk keresése fejlesztők számára készült. A keresési szolgáltatás a szélesség/hosszúsági egy adott cím, alhálózatok közötti utca, földrajzi funkció vagy információs jel (POI) rendeli. A keresési szolgáltatás API-k által visszaadott szélességi és hosszúsági értékeket más Azure-alapú helyszolgáltatás például az útvonal és a forgalom áramlását API-k paramétereiben használható.

## <a name="prerequisites"></a>Előfeltételek
* Telepítse a [Postman app](https://www.getpostman.com/apps).

* Rendelkezik egy Azure-alapú helyszolgáltatás fiókot és kulcsot. Fiók létrehozása és egy kulcs lekérése kapcsolatos tudnivalókat lásd: [kezelése az Azure-alapú helyszolgáltatás fiókja és -kulcsok](how-to-manage-account-keys.md). 

## <a name="using-fuzzy-search"></a>Intelligens egyeztetésű keresési

A keresési szolgáltatás az alapértelmezett API az intelligens egyeztetésű keresési, cím és a POI-jogkivonatok tetszőleges kombinációjából bemenetek kezeli. Ez a keresés API a kanonikus "egysoros keresési", és akkor hasznos, ha a felhasználói bevitel keresési lekérdezés nem ismeri. Az intelligens egyeztetésű keresési API-JÁNAK POI-keresés és a geokódolás. Az API-t is képes súlyozott környezetfüggő pozícióhoz (LAT/maga. párosítsa), teljes mértékben korlátozza a koordináta és a RADIUS-, vagy a rögzítési pont hordozóeltolást földrajzi nélkül általában végrehajtható.

A legtöbb keresési lekérdezések alapértelmezés szerint a "maxFuzzyLevel = 1" szerezzen a teljesítmény és szokatlan eredmények. Ez az alapértelmezett történő a következő lekérdezésparaméter a kérelmenként szükség szerint felülbírálható "maxFuzzyLevel = 2" vagy "3".

### <a name="search-for-an-address-using-fuzzy-search"></a>Az intelligens egyeztetésű keresési cím keresése

1. Nyissa meg a Postman alkalmazást, kattintson az új |} Hozzon létre új, és válassza **GET kérés**. Adjon meg egy kérelem **intelligens egyeztetésű keresési**, válassza ki a gyűjtemény vagy mappába menti, és kattintson a **mentése**.

    További információkért tekintse meg a Postman kérelmek dokumentációját.

2. A jelentéskészítő lapon jelölje be a **beolvasása** HTTP-metódus, és adja meg a kérelem URL-címet a API-végpontot.

    ![Intelligens egyeztetésű keresési ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Paraméter | Ajánlott érték |
    |---------------|------------------------------------------------|
    | HTTP-metódus | GET |
    | Lekérdezés URL-címe | https://atlas.microsoft.com/search/fuzzy/json? |
    | Engedélyezés | Nincs hitelesítés |

    A **json** URL-címet attribútum meghatározza, hogy a válasz formátumát. Ez a cikk teljes json és olvashatóság érdekében használja. Megtalálhatja a rendelkezésre álló válasz formátumok a **keresési intelligens beolvasása** a [hely alapú szolgáltatások funkcionális API-referencia] definíciója (https://docs.microsoft.com/rest/api/location-based-services/search/getsearchfuzzy).

3. Kattintson a **paraméterei**, és írja be a következő kulcs-érték párok lekérdezés vagy az elérési út paraméter, a kérelem URL-címében használni:

    ![Intelligens egyeztetésű keresési ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Kulcs | Érték |
    |------------------|-------------------------|
    | API-verzió | 1.0 |
    | Előfizetés-kulcs | \<az Azure-alapú helyszolgáltatás kulcs\> |
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

Egy teljes vagy részleges utca, házszám átadása a keresési cím API-t, és részletes cím tulajdonságait, például település vagy felosztása, valamint a pozicionális értékeket tartalmaz a szélességi és hosszúsági választ kapnak.

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
    | Előfizetés-kulcs | \<az Azure-alapú helyszolgáltatás kulcs\> |
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
    | Előfizetés-kulcs | \<az Azure-alapú helyszolgáltatás kulcs\> |
    | lekérdezés | 47.59093,-122.33263 |
    
3. Kattintson a **küldése** és tekintse át az adott válasz törzse. 
    
    Egy "stadium" poi-kategóriát a válasz tartalmazza a POI bejegyzést Safeco mező. 
    
4. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | szám | true |

    Ha a [szám](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezésparaméter a kérelem nem jut, a válasz előfordulhat, hogy tartalmazza a utca (balra vagy jobbra), és az eltolás pozíciója, hogy a kívánt oldalán.
    
5. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | spatialKeys | true |

    Ha a [spatialKeys](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter értéke, a válasz egy adott hely védett földrajzi térbeli kulcsfontosságú adatokat tartalmaz.

6. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | returnSpeedLimit | true |
    
    Ha a [returnSpeedLimit](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter értéke, a válasz visszaadása, a közzétett Sebességkorlát.

7. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | returnRoadUse | true |

    Ha a [returnRoadUse](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter értéke, a választ ad vissza, a közúti használható tömb reversegeocodes utca szinten.

8. Adja hozzá a következő kulcs / érték pár, hogy a **paraméterei** szakaszt, és kattintson **küldése**:

    | Kulcs | Érték |
    |-----|------------|
    | roadUse | true |

    A fordított geocode lekérdezés egy adott típusú közúti használata használatával korlátozhatja a [roadUse](https://docs.microsoft.com/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) lekérdezési paraméter.
    
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
    | Előfizetés-kulcs | \<az Azure-alapú helyszolgáltatás kulcs\> |
    | lekérdezés | 47.59093,-122.33263 |
    
4. Kattintson a **küldése** és tekintse át az adott válasz törzse. 

## <a name="next-steps"></a>További lépések
- Megismerkedhet a [Azure hely alapú Serices Search szolgáltatás](https://docs.microsoft.com/rest/api/location-based-services/search) API-JÁNAK dokumentációja 
