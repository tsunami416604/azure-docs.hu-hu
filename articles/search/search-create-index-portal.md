---
title: Azure Search-index létrehozása az Azure portal – Azure Search
description: Ismerje meg, hogyan tervezőkkel egy beépített portál index Azure Search-index létrehozása.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6a2bac71c37cc750eb24e3492ecdcdf0b2333cce
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338835"
---
# <a name="create-an-azure-search-index-in-the-portal"></a>Az Azure Search-index létrehozása a portálon

Az Azure Search tartalmaz egy beépített index Lekérdezéstervező prototípusok hasznos, ha a portálon vagy létrehozni egy [search-index](search-what-is-an-index.md) az Azure Search szolgáltatás található. Az eszköz séma konstrukció szolgál. Amikor menti a definíció, üres index válik teljesen ki, a az Azure Search szolgáltatásban. Hogyan betöltésük kereshető adatok van szerint strukturálhatja.

Az index-Tervező csak egy indexet létrehozni csak egy megközelítés. Programozott módon, is létrehozhat egy indexet a [.NET](search-create-index-dotnet.md) vagy [REST](search-create-index-rest-api.md) API-k.

## <a name="start-index-designer"></a>Indítsa el az index-Tervező

1. Jelentkezzen be a [Azure Portalra](https://portal.azure.com), és nyissa meg a szolgáltatás irányítópultját. A gyorselérési sávon a **Minden szolgáltatás** elemre kattintva kereshet az aktuális előfizetésben meglévő „keresési szolgáltatásokat”. 

2. Kattintson a **index hozzáadása** hivatkozásra az oldal tetején lévő parancssávon.

   ![A parancssávon az indexet hivatkozás hozzáadása](media/search-create-index-portal/add-index.png "a parancssávon az indexet hivatkozás hozzáadása")

3. Adjon nevet az Azure Search-indexnek. Indexelés és a lekérdezési műveletek hivatkozott index neve. Az index neve része lesz az indexhez való kapcsolódáshoz és az Azure Search REST API-ban HTTP-kérelmek küldéséhez használt végpont URL-címének.

   * Kezdje betűvel.
   * Csak kisbetűket, számjegyeket vagy kötőjeleket ("-") használjon.
   * A név ne legyen 60 karakternél hosszabb.

## <a name="add-fields"></a>Mezők hozzáadása

Az index összeállításához tartozik egy *Mezőkollekció*, amely az indexben kereshető adatok körét határozza meg. A mezők gyűjteménye érvényesítette, a külön feltöltött dokumentumok struktúráját adja meg. A mezők is tartoznak, szükséges és választható mezők neve és írta be az index attribútumainak, amelyek meghatározzák, hogy miként legyen használható a mező.

1. Teljes körűen adja meg a feltölteni kívánt dokumentumokat, beállítás-mezők hozzáadása egy [adattípus](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) minden egyes. Például, ha a dokumentumok tartalma egy *Szálloda-id*, *Szálloda neve*, *cím*, *Város*, és *régió*, hozzon létre a megfelelő mezőt az indexben mindegyikhez. Tekintse át a [tervezési útmutató a lenti](#design) segítség a tulajdonságok beállításához.

2. Adjon meg egy *kulcs* Edm.String típusú mezőt. Ez a mező értékeit kell egyes dokumentumok egyedi azonosításához. A mező neve alapértelmezés szerint *id*, de az [elnevezési szabályok](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) betartása mellett át is nevezhető. Ha a mezők is tartoznak, például *Szálloda-id*, kellene kiválasztania, amely a kulcshoz. A kulcs mező minden Azure Search-indexhez kötelező, és sztringnek (string) kell lennie.

3. Attribútumok beállítása minden mező. Az index-tervező nem tartalmazza az adattípus érvénytelen attribútumokat, de nem javasolt, mit kell tartalmaznia. Olvassa el a következő szakaszban megismeréséhez az attribútumok az útmutatást.

    Az Azure Search API dokumentációja egy egyszerű *szálloda*-indexet használó kódpéldákat is tartalmaz. Az alábbi képernyőképen látható az index definícióját, beleértve az index definíciója és annak során megadott francia nyelvi elemző, amely szerint a gyakorlat során a portálon újra létrehozhatja.

    ![Hotels bemutató index](media/search-create-index-portal/field-definitions.png "Hotels bemutató index")

4. Ha befejezte, kattintson a **létrehozás** mentéséhez, és hozza létre az indexet.

<a name="design"></a>

## <a name="set-attributes"></a>Attribútumainak beállítása

Új mezők bármikor megadhatók, a meglévő meződefiníciók azonban az index élettartamára rögzülnek. A fejlesztők ezért általában egyszerű indexek létrehozására vagy ötletek kipróbálására használják a portált, vagy a portál oldalain néznek utána egy beállításnak. Egy index rendszeres kiegészítése hatékonyabb az index újraépítését megkönnyítő kódalapú megközelítéssel.

Az index mentése előtt elemzők és javaslattevők lesznek a mezőkhöz rendelve. Mindenképp vegye fel a nyelvi elemzőket vagy javaslattevőket az index definícióját a létrehozása közben.

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

## <a name="next-steps"></a>További lépések

Miután létrehozott egy Azure Search-indexet, folytathatja a következő lépéssel, ami a [kereshető adatok feltöltése az indexbe](search-what-is-data-import.md).

Azt is megteheti, hogy is is igénybe vehet egy [alaposabban indexek](search-what-is-an-index.md). Az indexek a Mezőkollekción kívül megadják az elemzőket, javaslattevőket, értékelési profilokat és CORS-beállításokat is. A portál lapokra osztott oldalakat kínál a leggyakrabban használt elemek definiálása: A mezők, elemzők és javaslattevők. Más elemek létrehozására vagy módosítására a REST API vagy a .NET SDK használható.

## <a name="see-also"></a>Lásd még

 [A teljes szöveges keresés működése](search-lucene-query-architecture.md)  
 [Keresési szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

