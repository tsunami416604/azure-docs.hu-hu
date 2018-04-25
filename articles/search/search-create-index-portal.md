---
title: Index létrehozása (Portal – Azure Search) | Microsoft Docs
description: Index létrehozása az Azure Portal használatával.
manager: cgronlun
author: heidisteen
ms.service: search
ms.devlang: NA
ms.topic: quickstart
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: ab0352b8c830e875afc9b1d1b006ba4d2a512d7a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Azure Search-index létrehozása az Azure Portal használatával
> [!div class="op_single_selector"]
> * [Áttekintés](search-what-is-an-index.md)
> * [Portál](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Az Azure Portal beépített indextervezőjével az Azure Search szolgáltatáson futtatható [keresési index](search-what-is-an-index.md) tervezhető vagy hozható létre. 

## <a name="prerequisites"></a>Előfeltételek

A cikk feltételezi, hogy rendelkezik [Azure-előfizetéssel](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) és az [Azure Search szolgáltatással](search-create-service-portal.md).  

## <a name="find-your-search-service"></a>A Search szolgáltatás megkeresése
1. Jelentkezzen be az Azure Portal oldalán, és tekintse át az [előfizetéséhez elérhető keresési szolgáltatásokat](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Válassza ki az Azure Search szolgáltatást.

## <a name="name-the-index"></a>Az index elnevezése

1. Kattintson az **Index hozzáadása** gombra a lap tetején található parancssávon.
2. Adjon nevet az Azure Search-indexnek. 
   * Kezdje betűvel.
   * Csak kisbetűket, számjegyeket vagy kötőjeleket ("-") használjon.
   * A név ne legyen 60 karakternél hosszabb.

  Az index neve része lesz az indexhez való kapcsolódáshoz és az Azure Search REST API-ban HTTP-kérelmek küldéséhez használt végpont URL-címének.

## <a name="define-the-fields-of-your-index"></a>Az index mezőinek definiálása

Az index összeállításához tartozik egy *Mezőkollekció*, amely az indexben kereshető adatok körét határozza meg. Még pontosabban ez adja meg a külön feltöltött dokumentumok struktúráját. A mezőkollekcióhoz kötelező és választható, névvel és típussal rendelkező mezők is tartoznak, a mezők felhasználási módját meghatározó index-tulajdonságokkal.

1. Nyissa meg a meződefiníciós panelt az **Index hozzáadása** panel **Mezők >** elemére kattintva. 

2. Fogadja el a generált Edm.String típusú *key* (kulcs) mezőt. A mező neve alapértelmezés szerint *id*, de az [elnevezési szabályok](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) betartása mellett át is nevezhető. A kulcs mező minden Azure Search-indexhez kötelező, és karakterláncnak (string) kell lennie.

3. Adja meg a feltölteni kívánt dokumentumokat teljesen leíró mezőket. Ha a dokumentumok tartalma *azonosító*, *szálloda neve*, *cím*, *város* és *régió*, akkor mindegyikhez hozza létre a megfelelő mezőt az indexben. A tulajdonságok beállításához [a lenti tervezési útmutató](#design) nyújt segítséget.

4. Belső használatra a szűrőkifejezésekben használt mezőket is hozzáadhat. A mezőtulajdonságok beállíthatók úgy, hogy kizárja a mezőket a keresési műveletekből.

5. Ha elkészült, kattintson az **OK** gombra az index mentéséhez és létrehozásához.

## <a name="tips-for-adding-fields"></a>Tippek a mezők hozzáadásához

A portálon az indexek létrehozása billentyűzetfüggő. A szükséges műveletek száma a következő munkafolyamat követésével csökkenthető:

1. Először építse fel a mezők listáját a nevek beírásával és az adattípusok beállításával.

2. Ez után az egyes tulajdonságok feletti jelölőmezőket használva egyszerre engedélyezheti az adott beállítást az összes mezőre, majd egyesével törölheti a jelöléseket az olyan mezők mellett, amelyekhez nem szükséges. A karakterlánc-mezők például általában kereshetők. Ilyen esetben érdemes bejelölni a **Retrievable** (Lekérdezhető) és **Searchable** (Kereshető) tulajdonságokat, hogy a keresési eredményben megjelenjen a mező tartalma és lehetséges legyen a mezőn a teljes szöveges keresés. 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>Tervezési útmutató a tulajdonságok beállításához

Új mezők bármikor megadhatók, a meglévő meződefiníciók azonban az index élettartamára rögzülnek. A fejlesztők ezért általában egyszerű indexek létrehozására vagy ötletek kipróbálására használják a portált, vagy a portál oldalain néznek utána egy beállításnak. Egy index rendszeres kiegészítése hatékonyabb az index újraépítését megkönnyítő kódalapú megközelítéssel.

Az index mentése előtt elemzők és javaslattevők lesznek a mezőkhöz rendelve. Mindenképpen kattintsa végig az összes lapot, hogy az index definíciójához megadja a nyelvi elemzőket vagy javaslattevőket.

A karakterlánc típusú mezők gyakran mint **Searchable** (Kereshető) és **Retrievable** (Lekérdezhető)vannak megjelölve.

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

Úgy is dönthet, hogy inkább az indexeket tanulmányozza alaposabban. Az indexek a Mezőkollekción kívül megadják az elemzőket, javaslattevőket, értékelési profilokat és CORS-beállításokat is. A portál lapokra osztott oldalakat kínál a leggyakrabban használt elemek: a mezők, elemzők és javaslattevők megadására. Más elemek létrehozására vagy módosítására a REST API vagy a .NET SDK használható.

## <a name="see-also"></a>Lásd még

 [A teljes szöveges keresés működése](search-lucene-query-architecture.md)  
 [Keresési szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

