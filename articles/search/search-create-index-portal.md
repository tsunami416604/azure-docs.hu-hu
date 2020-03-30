---
title: Keresési index létrehozása az Azure Portalon
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan hozhat létre indexet az Azure Cognitive Search egy beépített portálindex-tervező használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f2e875c625431867e6e83cfd1e0b2c6d7a2781f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112846"
---
# <a name="create-an-azure-cognitive-search-index-in-the-portal"></a>Azure Cognitive Search-index létrehozása a portálon

Az Azure Cognitive Search tartalmaz egy beépített indextervezőt a portálon, amely hasznos a prototípusok számára, vagy létrehoz egy [keresési indexet](search-what-is-an-index.md) az Azure Cognitive Search szolgáltatásban. Az eszköz a sémaépítéshez használható. A definíció mentésekor egy üres index teljes mértékben kifejeződik az Azure Cognitive Search.When you save the definition, a empty index becomes fully expressed in Azure Cognitive Search. A kereshető tartalommal való betöltése csak önön múlik.

Az indextervező csak egy módszer az index létrehozásához. Másik lehetőségként létrehozhat és betölthet indexet az [Adatok importálása varázslóval.](search-get-started-portal.md) A varázsló csak saját maga által létrehozott indexekkel működik. Programozott módon a [.NET](search-create-index-dotnet.md) [vagy](search-create-index-rest-api.md) rest API-k használatával is létrehozhat indexet.

## <a name="start-index-designer"></a>Indextervező indítása

1. Jelentkezzen be a [Azure Portalra](https://portal.azure.com), és nyissa meg a szolgáltatás irányítópultját. A gyorselérési sávon a **Minden szolgáltatás** elemre kattintva kereshet az aktuális előfizetésben meglévő „keresési szolgáltatásokat”. 

2. Kattintson a lap tetején lévő parancssávon található **Tárgymutató hozzáadása** hivatkozásra.

   ![Index hivatkozás hozzáadása a parancssávban](media/search-create-index-portal/add-index.png "Index hivatkozás hozzáadása a parancssávban")

3. Nevezze el az Azure Cognitive Search indexét. Az indexnevekre az indexelési és lekérdezési műveletek hivatkoznak. Az index neve az indexhez való kapcsolódások és az Azure Cognitive Search REST API-ban HTTP-kérelmek küldéséhez használt végpont URL-címrészévé válik.

   * Kezdje betűvel.
   * Csak kisbetűket, számjegyeket vagy kötőjeleket ("-") használjon.
   * A név ne legyen 60 karakternél hosszabb.

## <a name="add-fields"></a>Mezők hozzáadása

Az index összeállításához tartozik egy *Mezőkollekció*, amely az indexben kereshető adatok körét határozza meg. A mezőgyűjtemény összesen meghatározza a külön feltöltött dokumentumok szerkezetét. A Fields gyűjtemény a mezők által használt és beírt kötelező és választható mezőket tartalmazza, amelyek indexattribútumai határozzák meg a mező használható módját.

1. Mezők hozzáadásával teljes mértékben megadhatja a feltöltendő dokumentumokat, és mindegyikhez bekell jelölni [e-beállításokat.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) Ha például a dokumentumok *egy hotelazonosítóból*, *a szállodanévből*, a *címből*, *a városból*és *a régióból*állnak, hozzon létre egy megfelelő mezőt mindegyikhez az indexben. Az [attribútumok](#design) beállításával kapcsolatos segítségért tekintse át az alábbi szakasz tervezési útmutatóját.

1. Ha a bejövő adatok hierarchikus jellegűek, a sémának [összetett típusokat](search-howto-complex-data-types.md) kell tartalmaznia a beágyazott struktúrák ábrázolására. A beépített minta adatkészlet, a Hotels, összetett típusokat mutat be egy olyan cím (több almezőt tartalmaz) használatával, amely egy-az-egyhez kapcsolatban áll az egyes hotelekkel, valamint egy Szobák komplex gyűjteményt, ahol minden szállodához több szoba van társítva. 

1. Adjon *key* meg egy Edm.String típusú kulcsmezőt. A kulcsmező minden Azure Cognitive Search indexhez kötelező, és karakterláncnak kell lennie. A mező értékeinek egyedileg kell azonosítaniuk az egyes bizonylatokat. A mező neve alapértelmezés szerint *id*, de az [elnevezési szabályok](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) betartása mellett át is nevezhető. Ha például a mezőgyűjtemény tartalmazza *a hotelazonosítót,* akkor ezt kell választania a kulcshoz. 

1. Állítsa be az attribútumokat az egyes mezőkön. Az indextervező kizárja az adattípushoz érvénytelen attribútumokat, de nem javasolja, hogy mit vegyen fel. Tekintse át a következő szakaszban található útmutatást, hogy megértse, mire szolgálnak az attribútumok.

    Az Azure Cognitive Search API dokumentációja egy egyszerű *szállodai* indexet tartalmazó kódpéldákat tartalmaz. Az alábbi képernyőképen láthatja az indexdefiníciót, beleértve az indexdefiníció során megadott francia nyelvi elemzőt, amelyet a portálon gyakorlógyakorlatként újra létrehozhat.

    ![Hotelek demó indexe](media/search-create-index-portal/field-definitions.png "Hotelek demó indexe")

1. Ha végzett, kattintson a **Létrehozás gombra** az index mentéséhez és létrehozásához.

<a name="design"></a>

## <a name="set-attributes"></a>Attribútumok beállítása

Új mezők bármikor megadhatók, a meglévő meződefiníciók azonban az index élettartamára rögzülnek. A fejlesztők ezért általában egyszerű indexek létrehozására vagy ötletek kipróbálására használják a portált, vagy a portál oldalain néznek utána egy beállításnak. Egy index rendszeres kiegészítése hatékonyabb az index újraépítését megkönnyítő kódalapú megközelítéssel.

Az index mentése előtt elemzők és javaslattevők lesznek a mezőkhöz rendelve. Ügyeljen arra, hogy a létrehozás során nyelvi elemzőket vagy javaslatajánlókat adjon hozzá az indexdefinícióhoz.

A sztring típusú mezők gyakran mint **Searchable** (Kereshető) és **Retrievable** (Lekérdezhető)vannak megjelölve. A keresési eredmények szűkítésére használt mezők **Sortable** (Rendezhető), **Filterable** (Szűrhető) és **Facetable** (Kategorizálható) tulajdonsággal is rendelkeznek.

A mezőtulajdonságok határozzák meg egy mező használati módját, például hogy használva lesz-e teljes szöveges keresésben, kategorizált navigációban, rendezési műveletekben stb. Az alábbi táblázat külön ismerteti az egyes tulajdonságokat.

|Attribútum|Leírás|  
|---------------|-----------------|  
|**searchable**|Kereshető – Teljes szöveges keresésre alkalmas, az indexelés során lexikai elemzés, például szavakra bontás végezhető rajta. Ha egy kereshető mező értékének például a „sunny day” szöveget adja meg, akkor az két különálló tokenre lesz bontva: „sunny” és „day”. További részletek az [A teljes szöveges keresés működése](search-lucene-query-architecture.md) című cikkben.|  
|**filterable**|Szűrhető – A **$filter** lekérdezések hivatkoznak rá. Az `Edm.String` vagy `Collection(Edm.String)` típusú szűrhető mezők nem lesznek szavakra bontva, ezért összehasonlítások csak pontos egyezésekre végezhetők. Ha egy ilyen mező értékének például a „sunny day” szöveget adja meg, akkor a `$filter=f eq 'sunny'` keresés nem talál egyezést, a `$filter=f eq 'sunny day'` viszont igen. |  
|**sortable**|Rendezhető – A rendszer alapértelmezés szerint érték szerint rendezi a találatokat, de a dokumentumok mezői alapján végzett rendezés is konfigurálható. A `Collection(Edm.String)` típusú mezőkhöz nem adható meg a **sortable** tulajdonság. |  
|**facetable**|Kategorizálható – Általában akkor használják, ha a keresési eredményeket a kategóriánkénti találatok számával együtt kell bemutatni (például szállodák egy adott városban). Ez a lehetőség az `Edm.GeographyPoint` típusú mezők esetén nem használható. A **filterable** (szűrhető), **sortable** (rendezhető) vagy **facetable** (kategorizálható) tulajdonsággal rendelkező `Edm.String` típusú mezők hossza legfeljebb 32 kilobájt lehet. Részletek az [Index létrehozása (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) című cikkben.|  
|**Kulcs**|Kulcs – Az indexen belüli dokumentumok egyedi azonosítója. Kulcsmezőként egyetlen `Edm.String` típusú mezőt kell megadni.|  
|**retrievable**|Lekérdezhető – Megadja, hogy a mező visszaadható-e egy keresési eredményben. Ez akkor hasznos, ha egy mező (például *nyereség*) szűrésre, rendezésre vagy értékelésre szolgál, de a végfelhasználó számára nem kell megjelennie. Ennek a tulajdonságnak az értéke `key` tulajdonságú mezők esetén csak `true` lehet.|  

## <a name="next-steps"></a>További lépések

Az Azure Cognitive Search index létrehozása után a következő lépésre léphet át: [kereshető adatok feltöltése az indexbe.](search-what-is-data-import.md)

Azt is megteheti, hogy [mélyebben megvizsgálja az indexeket.](search-what-is-an-index.md) Az indexek a Mezőkollekción kívül megadják az elemzőket, javaslattevőket, értékelési profilokat és CORS-beállításokat is. A portál lapokra osztott oldalakat kínál a leggyakrabban használt elemek: a mezők, elemzők és javaslattevők megadására. Más elemek létrehozására vagy módosítására a REST API vagy a .NET SDK használható.

## <a name="see-also"></a>Lásd még

 [A teljes szöveges keresés működése](search-lucene-query-architecture.md)  
 [Keresési szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

