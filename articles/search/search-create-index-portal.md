---
title: Azure Search index létrehozása Azure Portal-Azure Search
description: Megtudhatja, hogyan hozhat létre indexet a Azure Searchhoz egy beépített portál-index Designer használatával.
manager: nitinme
author: heidisteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: heidist
ms.openlocfilehash: 4abef5a3030643d4c7b91d2911f350190972f1eb
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "71937271"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>Azure Search index létrehozása a portálon

Azure Search tartalmaz egy beépített index-tervezőt a portálon, amely a prototípusok esetében hasznos, vagy a Azure Search szolgáltatásban futtatott [keresési indexet](search-what-is-an-index.md) hoz létre. Az eszköz a séma kialakításához használatos. A definíció mentésekor az üres index Azure Search-ban teljes mértékben kialakul. A kereshető tartalommal való betöltés akár Önnek is megfelel.

Az index tervezője csak egyetlen megközelítés az index létrehozásához. Azt is megteheti, hogy létrehoz és betölt egy indexet az [adat importálása varázslóval](search-get-started-portal.md). A varázsló csak a saját maga által létrehozott indexekkel működik. Programozott módon létrehozhat egy indexet a [.net](search-create-index-dotnet.md) vagy a [Rest](search-create-index-rest-api.md) API-k használatával.

## <a name="start-index-designer"></a>Index tervező elindítása

1. Jelentkezzen be a [Azure Portalra](https://portal.azure.com), és nyissa meg a szolgáltatás irányítópultját. A gyorselérési sávon a **Minden szolgáltatás** elemre kattintva kereshet az aktuális előfizetésben meglévő „keresési szolgáltatásokat”. 

2. Kattintson az **index hozzáadása** hivatkozásra az oldal tetején található parancssáv alatt.

   ![Index hivatkozás hozzáadása a parancssáv](media/search-create-index-portal/add-index.png "Index hivatkozás hozzáadása a parancssáv")

3. Adjon nevet az Azure Search-indexnek. Az indexek nevei az indexelési és lekérdezési műveletekben vannak hivatkozva. Az index neve része lesz az indexhez való kapcsolódáshoz és az Azure Search REST API-ban HTTP-kérelmek küldéséhez használt végpont URL-címének.

   * Kezdje betűvel.
   * Csak kisbetűket, számjegyeket vagy kötőjeleket ("-") használjon.
   * A név ne legyen 60 karakternél hosszabb.

## <a name="add-fields"></a>Mezők hozzáadása

Az index összeállításához tartozik egy *Mezőkollekció*, amely az indexben kereshető adatok körét határozza meg. A mezők gyűjteménye teljes egészében meghatározza a feltöltött dokumentumok szerkezetét. A mezők gyűjteménye kötelező és opcionális mezőket tartalmaz, amelyek neve és beírása a mező használatának módját meghatározó index-attribútumokkal.

1. Mezők hozzáadásával teljes mértékben megadhatja a feltölteni kívánt dokumentumokat, és beállíthatja, hogy az egyes [adatok milyen típusúak](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) legyenek. Ha például a dokumentumok egy *szállodai azonosítót*, a *Hotel-Name*, a *címeket*, a *várost*és a *régiót*tartalmazzák, hozzon létre egy megfelelő mezőt az indexben. Az attribútumok beállításával kapcsolatos segítségért tekintse át az [alábbi szakaszban található tervezési útmutatót](#design) .

1. Ha a bejövő adatértékek hierarchikus jellegűek, a sémának tartalmaznia kell a beágyazott struktúrákat jelképező [összetett típusokat](search-howto-complex-data-types.md) . A beépített mintavételi adatkészlet, a hotelek és az összetett típusok illusztrálása egy olyan címen (több almezőt tartalmaz), amely egy-az-egyhez kapcsolattal rendelkezik az egyes szolgáltatásokkal, valamint a szobák összetett gyűjteménye, ahol több szoba van társítva az egyes szolgáltatásokhoz. 

1. Adjon meg egy EDM. String típusú *kulcsot* . A kulcs mező minden Azure Search-indexhez kötelező, és sztringnek (string) kell lennie. A mező értékének egyedi módon kell azonosítania az egyes dokumentumokat. A mező neve alapértelmezés szerint *id*, de az [elnevezési szabályok](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) betartása mellett át is nevezhető. Ha például a mezők gyűjteménye tartalmazza a *Hotel-ID-* t, válassza ki a kulcsot a kulcshoz. 

1. Attribútumok beállítása az egyes mezőknél. Az index tervezője kizár minden olyan attribútumot, amely érvénytelen az adattípushoz, de nem javasolja, hogy mit tartalmazzon. Tekintse át a következő szakaszban található útmutatást, amelyből megismerheti, hogy az attribútumok mire vonatkoznak.

    Az Azure Search API dokumentációja egy egyszerű *szálloda*-indexet használó kódpéldákat is tartalmaz. Az alábbi képernyőképen látható az index definíciója, beleértve az index definíciójában megadott francia nyelvű elemzőt is, amelyet a portálon az eljárás gyakorlatának megfelelően újra létrehozhat.

    ![A Hotels bemutató indexe](media/search-create-index-portal/field-definitions.png "A Hotels bemutató indexe")

1. Ha elkészült, kattintson a **Létrehozás** gombra az index mentéséhez és létrehozásához.

<a name="design"></a>

## <a name="set-attributes"></a>Attribútumok beállítása

Új mezők bármikor megadhatók, a meglévő meződefiníciók azonban az index élettartamára rögzülnek. A fejlesztők ezért általában egyszerű indexek létrehozására vagy ötletek kipróbálására használják a portált, vagy a portál oldalain néznek utána egy beállításnak. Egy index rendszeres kiegészítése hatékonyabb az index újraépítését megkönnyítő kódalapú megközelítéssel.

Az index mentése előtt elemzők és javaslattevők lesznek a mezőkhöz rendelve. A létrehozásakor ne felejtse el nyelvi elemzőket vagy mutatókat hozzáadni a tárgymutató-definícióhoz.

A sztring típusú mezők gyakran mint **Searchable** (Kereshető) és **Retrievable** (Lekérdezhető)vannak megjelölve. A keresési eredmények szűkítésére használt mezők **Sortable** (Rendezhető), **Filterable** (Szűrhető) és **Facetable** (Kategorizálható) tulajdonsággal is rendelkeznek.

A mezőtulajdonságok határozzák meg egy mező használati módját, például hogy használva lesz-e teljes szöveges keresésben, kategorizált navigációban, rendezési műveletekben stb. Az alábbi táblázat külön ismerteti az egyes tulajdonságokat.

|Attribútum|Leírás|  
|---------------|-----------------|  
|**searchable**|Kereshető – Teljes szöveges keresésre alkalmas, az indexelés során lexikai elemzés, például szavakra bontás végezhető rajta. Ha egy kereshető mező értékének például a „sunny day” szöveget adja meg, akkor az két különálló tokenre lesz bontva: „sunny” és „day”. További részletek az [A teljes szöveges keresés működése](search-lucene-query-architecture.md) című cikkben.|  
|**filterable**|Szűrhető – A **$filter** lekérdezések hivatkoznak rá. Az `Edm.String` vagy `Collection(Edm.String)` típusú szűrhető mezők nem lesznek szavakra bontva, ezért összehasonlítások csak pontos egyezésekre végezhetők. Ha egy ilyen mező értékének például a „sunny day” szöveget adja meg, akkor a `$filter=f eq 'sunny'` keresés nem talál egyezést, a `$filter=f eq 'sunny day'` viszont igen. |  
|**sortable**|Rendezhető – A rendszer alapértelmezés szerint érték szerint rendezi a találatokat, de a dokumentumok mezői alapján végzett rendezés is konfigurálható. A `Collection(Edm.String)` típusú mezőkhöz nem adható meg a **sortable** tulajdonság. |  
|**facetable**|Kategorizálható – Általában akkor használják, ha a keresési eredményeket a kategóriánkénti találatok számával együtt kell bemutatni (például szállodák egy adott városban). Ez a lehetőség az `Edm.GeographyPoint` típusú mezők esetén nem használható. A **filterable** (szűrhető), **sortable** (rendezhető) vagy **facetable** (kategorizálható) tulajdonsággal rendelkező `Edm.String` típusú mezők hossza legfeljebb 32 kilobájt lehet. Részletek az [Index létrehozása (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) című cikkben.|  
|**key**|Kulcs – Az indexen belüli dokumentumok egyedi azonosítója. Kulcsmezőként egyetlen `Edm.String` típusú mezőt kell megadni.|  
|**retrievable**|Lekérdezhető – Megadja, hogy a mező visszaadható-e egy keresési eredményben. Ez akkor hasznos, ha egy mező (például *nyereség*) szűrésre, rendezésre vagy értékelésre szolgál, de a végfelhasználó számára nem kell megjelennie. Ennek a tulajdonságnak az értéke `key` tulajdonságú mezők esetén csak `true` lehet.|  

## <a name="next-steps"></a>Következő lépések

Miután létrehozott egy Azure Search-indexet, folytathatja a következő lépéssel, ami a [kereshető adatok feltöltése az indexbe](search-what-is-data-import.md).

Azt is megteheti, hogy [mélyebben megtekinti az indexeket](search-what-is-an-index.md). Az indexek a Mezőkollekción kívül megadják az elemzőket, javaslattevőket, értékelési profilokat és CORS-beállításokat is. A portál lapokra osztott oldalakat kínál a leggyakrabban használt elemek: a mezők, elemzők és javaslattevők megadására. Más elemek létrehozására vagy módosítására a REST API vagy a .NET SDK használható.

## <a name="see-also"></a>Lásd még:

 [A teljes szöveges keresés működése](search-lucene-query-architecture.md)  
 [Keresési szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

