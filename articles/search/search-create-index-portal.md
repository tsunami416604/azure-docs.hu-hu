---
title: Azure Search-index létrehozása az Azure portal – Azure Search
description: Ismerje meg, hogyan tervezőkkel beépített portál index Azure Search-index létrehozása.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4bba8b41418dadad1b241d60ab0b7aeee4c046d7
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316709"
---
# <a name="how-to-create-an-azure-search-index-using-the-azure-portal"></a>Az Azure portal segítségével Azure Search-index létrehozása

Az Azure Search tartalmaz egy beépített index Lekérdezéstervező prototípusok hasznos, ha a portálon vagy létrehozni egy [search-index](search-what-is-an-index.md) az Azure Search szolgáltatás található. Az eszköz séma konstrukció szolgál. Amikor menti a definíció, üres index válik teljesen ki, a az Azure Search szolgáltatásban. Hogyan betöltésük kereshető adatok van szerint strukturálhatja.

Az index-Tervező csak egy indexet létrehozni csak egy megközelítés. Programozott módon, is létrehozhat egy indexet a [.NET](search-create-index-dotnet.md) vagy [REST](search-create-index-rest-api.md) API-k.

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy rendelkezik [Azure-előfizetéssel](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) és az [Azure Search szolgáltatással](search-create-service-portal.md).

## <a name="open-index-designer-and-name-an-index"></a>Index tervező megnyitása és a egy index neve

1. Jelentkezzen be a [Azure Portalra](https://portal.azure.com), és nyissa meg a szolgáltatás irányítópultját. A gyorselérési sávon a **Minden szolgáltatás** elemre kattintva kereshet az aktuális előfizetésben meglévő „keresési szolgáltatásokat”. 

2.  Kattintson az **Index hozzáadása** gombra a lap tetején található parancssávon.

3. Adjon nevet az Azure Search-indexnek. Indexelés és a lekérdezési műveletek hivatkozott index neve. Az index neve része lesz az indexhez való kapcsolódáshoz és az Azure Search REST API-ban HTTP-kérelmek küldéséhez használt végpont URL-címének.

   * Kezdje betűvel.
   * Csak kisbetűket, számjegyeket vagy kötőjeleket ("-") használjon.
   * A név ne legyen 60 karakternél hosszabb.

## <a name="define-the-fields-of-your-index"></a>Az index mezőinek definiálása

Az index összeállításához tartozik egy *Mezőkollekció*, amely az indexben kereshető adatok körét határozza meg. A mezők gyűjteménye érvényesítette, a külön feltöltött dokumentumok struktúráját adja meg. A mezők is tartoznak, szükséges és választható mezők neve és írta be az index attribútumainak, amelyek meghatározzák, hogy miként legyen használható a mező.

1. Nyissa meg a meződefiníciós panelt az **Index hozzáadása** panel **Mezők >** elemére kattintva. 

2. Fogadja el a generált Edm.String típusú *key* (kulcs) mezőt. A mező neve alapértelmezés szerint *id*, de az [elnevezési szabályok](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) betartása mellett át is nevezhető. A kulcs mező minden Azure Search-indexhez kötelező, és sztringnek (string) kell lennie.

3. Adja meg a feltölteni kívánt dokumentumokat teljesen leíró mezőket. Ha a dokumentumok tartalma *azonosító*, *szálloda neve*, *cím*, *város* és *régió*, akkor mindegyikhez hozza létre a megfelelő mezőt az indexben. A tulajdonságok beállításához [a lenti tervezési útmutató](#design) nyújt segítséget.

4. Belső használatra a szűrőkifejezésekben használt mezőket is hozzáadhat. A mezőtulajdonságok beállíthatók úgy, hogy kizárja a mezőket a keresési műveletekből.

5. Ha elkészült, kattintson az **OK** gombra az index mentéséhez és létrehozásához.

## <a name="tips-for-adding-fields"></a>Tippek a mezők hozzáadásához

A portálon az indexek létrehozása billentyűzetfüggő. A szükséges műveletek száma a következő munkafolyamat követésével csökkenthető:

1. Először építse fel a mezők listáját a nevek beírásával és az adattípusok beállításával.

2. Ez után az egyes tulajdonságok feletti jelölőmezőket használva egyszerre engedélyezheti az adott beállítást az összes mezőre, majd egyesével törölheti a jelöléseket az olyan mezők mellett, amelyekhez nem szükséges. A sztringmezők például általában kereshetők. Ilyen esetben érdemes bejelölni a **Retrievable** (Lekérdezhető) és **Searchable** (Kereshető) tulajdonságokat, hogy a keresési eredményben megjelenjen a mező tartalma és lehetséges legyen a mezőn a teljes szöveges keresés. 

<a name="design"></a>

## <a name="design-guidance-for-setting-attributes"></a>Tervezési útmutató a tulajdonságok beállításához

Új mezők bármikor megadhatók, a meglévő meződefiníciók azonban az index élettartamára rögzülnek. A fejlesztők ezért általában egyszerű indexek létrehozására vagy ötletek kipróbálására használják a portált, vagy a portál oldalain néznek utána egy beállításnak. Egy index rendszeres kiegészítése hatékonyabb az index újraépítését megkönnyítő kódalapú megközelítéssel.

Az index mentése előtt elemzők és javaslattevők lesznek a mezőkhöz rendelve. Mindenképpen kattintsa végig az összes lapot, hogy az index definíciójához megadja a nyelvi elemzőket vagy javaslattevőket.

A sztring típusú mezők gyakran mint **Searchable** (Kereshető) és **Retrievable** (Lekérdezhető)vannak megjelölve.

A keresési eredmények szűkítésére használt mezők **Sortable** (Rendezhető), **Filterable** (Szűrhető) és **Facetable** (Kategorizálható) tulajdonsággal is rendelkeznek.

A mezőtulajdonságok határozzák meg egy mező használati módját, például hogy használva lesz-e teljes szöveges keresésben, kategorizált navigációban, rendezési műveletekben stb. Az alábbi táblázat külön ismerteti az egyes tulajdonságokat.

|Attribútum|Leírás|  
|---------------|-----------------|  
|**searchable**|Kereshető – Teljes szöveges keresésre alkalmas, az indexelés során lexikai elemzés, például szavakra bontás végezhető rajta. Ha egy kereshető mező értékének például a „sunny day” szöveget adja meg, akkor az két különálló tokenre lesz bontva: „sunny” és „day”. További részletek az [A teljes szöveges keresés működése](search-lucene-query-architecture.md) című cikkben.|  
|**filterable**|Szűrhető – A **$filter** lekérdezések hivatkoznak rá. Az `Edm.String` vagy `Collection(Edm.String)` típusú szűrhető mezők nem lesznek szavakra bontva, ezért összehasonlítások csak pontos egyezésekre végezhetők. Ha egy ilyen mező értékének például a „sunny day” szöveget adja meg, akkor a `$filter=f eq 'sunny'` keresés nem talál egyezést, a `$filter=f eq 'sunny day'` viszont igen. |  
|**sortable**|Rendezhető – A rendszer alapértelmezés szerint érték szerint rendezi a találatokat, de a dokumentumok mezői alapján végzett rendezés is konfigurálható. A `Collection(Edm.String)` típusú mezőkhöz nem adható meg a **sortable** tulajdonság. |  
|**facetable**|Kategorizálható – Általában akkor használják, ha a keresési eredményeket a kategóriánkénti találatok számával együtt kell bemutatni (például szállodák egy adott városban). Ez a lehetőség az `Edm.GeographyPoint` típusú mezők esetén nem használható. A **filterable** (szűrhető), **sortable** (rendezhető) vagy **facetable** (kategorizálható) tulajdonsággal rendelkező `Edm.String` típusú mezők hossza legfeljebb 32 kilobájt lehet. Részletek az [Index létrehozása (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) című cikkben.|  
|**key**|Kulcs – Az indexen belüli dokumentumok egyedi azonosítója. Kulcsmezőként egyetlen `Edm.String` típusú mezőt kell megadni.|  
|**retrievable**|Lekérdezhető – Megadja, hogy a mező visszaadható-e egy keresési eredményben. Ez akkor hasznos, ha egy mező (például *nyereség*) szűrésre, rendezésre vagy értékelésre szolgál, de a végfelhasználó számára nem kell megjelennie. Ennek a tulajdonságnak az értéke `key` tulajdonságú mezők esetén csak `true` lehet.|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>A példa API-szakaszokban használt szálloda-index létrehozása

Az Azure Search API dokumentációja egy egyszerű *szálloda*-indexet használó kódpéldákat is tartalmaz. Az alábbi képernyőképeken az index definíciója és annak során a francia nyelvi elemző megadása látható. Ezt gyakorlásként Ön is elkészítheti a portálon.

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>További lépések

Miután létrehozott egy Azure Search-indexet, folytathatja a következő lépéssel, ami a [kereshető adatok feltöltése az indexbe](search-what-is-data-import.md).

Úgy is dönthet, hogy inkább az indexeket tanulmányozza alaposabban. Az indexek a Mezőkollekción kívül megadják az elemzőket, javaslattevőket, értékelési profilokat és CORS-beállításokat is. A portál lapokra osztott oldalakat kínál a leggyakrabban használt elemek definiálása: A mezők, elemzők és javaslattevők. Más elemek létrehozására vagy módosítására a REST API vagy a .NET SDK használható.

## <a name="see-also"></a>Lásd még

 [A teljes szöveges keresés működése](search-lucene-query-architecture.md)  
 [Keresési szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

