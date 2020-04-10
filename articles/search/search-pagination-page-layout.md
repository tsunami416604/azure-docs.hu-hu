---
title: A keresési eredmények kelvaló megoldása
titleSuffix: Azure Cognitive Search
description: A keresési eredmények strukturálása és rendezése, a dokumentumok számának leése, valamint a tartalomnavigáció hozzáadása a keresési eredményekhez az Azure Cognitive Search alkalmazásban.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 451e83fa6ab547536a4cfd85304930e749a8247f
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998399"
---
# <a name="how-to-work-with-search-results-in-azure-cognitive-search"></a>A keresési eredmények működése az Azure Cognitive Search szolgáltatásban

Ebből a cikkből megtudhatja, hogyan kaphat olyan lekérdezési választ, amely az egyező dokumentumok, a lapszámozott eredmények, a rendezett eredmények és a levédett kifejezések teljes számát adja vissza.

A válasz szerkezetét a lekérdezés paraméterei határozzák meg: [Keresési dokumentum](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) a REST API-ban vagy [DocumentSearchResult osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) a .NET SDK-ban.

## <a name="result-composition"></a>Eredmény összetétele

Bár a keresési dokumentum nagy számú mezőből állhat, általában csak néhányra van szükség ahhoz, hogy az egyes dokumentumokat az eredményhalmazban képviselje. Lekérdezési kérelem esetén `$select=<field list>` fűzze hozzá, hogy mely mezők jelenjenek meg a válaszban. Az eredménybe való bekerüléshez a mezőt az indexben **visszakereshetőként** kell hozzárendelni. 

A legjobban bevált mezők közé tartoznak azok, amelyek ellentétben állnak a dokumentumokkal, és megkülönböztetik őket, így elegendő információt szolgáltatnak ahhoz, hogy a felhasználó átkattintási választ adjon. Egy e-kereskedelmi webhelyen lehet terméknév, leírás, márka, szín, méret, ár és minősítés. A hotel-minta-index beépített minta, lehet, hogy mezők a következő példában:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",
      "select": "HotelId, HotelName, Description, Rating, Address/City"
      "count": true
    }
```

> [!NOTE]
> Ha egy eredménybe, például egy termékfotóba vagy emblémába szeretne képfájlokat felvenni, tárolja őket az Azure Cognitive Search-en kívül, de vegyen fel egy mezőt az indexbe, amely a kép URL-címére hivatkozik a keresési dokumentumban. Minta-indexek, amelyek támogatják a képeket az eredmények közé tartozik a **realestate-sample-us** demo, szerepelt ebben a [rövid útmutató,](search-create-app-portal.md)és a [New York-i Jobs demo app.](https://aka.ms/azjobsdemo)

## <a name="paging-results"></a>Lapozás eredményei

Alapértelmezés szerint a keresőmotor az első 50 egyezésig visszatér, akeresési pontszám szerint, ha a lekérdezés teljes szöveges keresés, vagy tetszőleges sorrendben a pontos egyezéses lekérdezésekhez.

Ha más számú egyező `$top` dokumentumot szeretne visszaadni, adja hozzá és `$skip` adja meg a paramétereket a lekérdezési kérelemhez. Az alábbi lista ismerteti a logikát.

+ A `$count=true` Hozzáadás hoz a mutatón belüli egyező dokumentumok teljes számának leszámítása.

+ Az első 15 egyező dokumentumkészletet és az összes egyezésszámát adja vissza:`GET /indexes/<INDEX-NAME>/docs?search=<QUERY STRING>&$top=15&$skip=0&$count=true`

+ Vissza a második szett, kihagyva az első 15-hoz kap a következő 15: `$top=15&$skip=15`. Tegye ugyanezt a harmadik 15-ös készletesetében is:`$top=15&$skip=30`

A lapszámozott lekérdezések eredményei nem garantáltan stabilak, ha az alapul szolgáló index változik. A lapozás módosítja az egyes lapok értékét, `$skip` de minden lekérdezés független, és az adatok aktuális nézetében működik, ahogy az lekérdezéskor az indexben létezik (más szóval nincs gyorsítótárazás vagy pillanatkép az eredményekről, például egy általános célú adatbázisban).
 
Az alábbiakban egy példa látható arra, hogyan kaphat másolatokat. Tegyük fel, hogy egy index négy dokumentumot:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
    { "id": "4", "rating": 1 }
 
Most tegyük fel, hogy az eredményeket egyszerre kettőre szeretné visszaadni, minősítés szerint rendezve. Ezt a lekérdezést az eredmények első `$top=2&$skip=0&$orderby=rating desc`oldalának lekérdezéséhez hajthatja végre: , a következő eredményeket hozva létre:

    { "id": "1", "rating": 5 }
    { "id": "2", "rating": 3 }
 
A szolgáltatásban tegyük fel, hogy egy ötödik dokumentum `{ "id": "5", "rating": 4 }`kerül az indexbe a lekérdezési hívások között: .  Nem sokkal ezután egy lekérdezést hajt `$top=2&$skip=2&$orderby=rating desc`végre a második oldal lekéréséhez: , és megkapja az alábbi eredményeket:

    { "id": "2", "rating": 3 }
    { "id": "3", "rating": 2 }
 
Figyelje meg, hogy a 2. Ennek az az oka, hogy az új 5 dokumentum nak nagyobb értéke van a minősítéshez, ezért a 2. Bár ez a viselkedés lehet váratlan, ez jellemző, hogy a keresőmotor viselkedik.

## <a name="ordering-results"></a>Az eredmények rendezése

A teljes szöveges keresési lekérdezések esetében az eredmények automatikusan rangsorolva vannak a keresési pontszám alapján, amelyet a dokumentum kifejezésgyakorisága és közelsége alapján számítanak ki, és a magasabb pontszámok olyan dokumentumokhoz kerülnek, amelyek több vagy erősebb egyezést tartalmaznak egy keresési kifejezésen. 

A keresési pontszámok általános relevanciát közvetítenek, ami tükrözi az egyezés erősségét az azonos eredményhalmazban lévő többi dokumentumhoz képest. A pontszámok nem mindig konzisztensek az egyik lekérdezéstől a másikig, így a lekérdezésekkel végzett munka során kisebb eltéréseket észlelhet a keresési dokumentumok rendezési módjában. Számos magyarázat van arra, hogy ez miért fordulhat elő.

| Ok | Leírás |
|-----------|-------------|
| Adatvolatilitás | A tárgymutató tartalma a dokumentumok hozzáadásakor, módosításában és törlésében változik. A kifejezésgyakoriságok az indexfrissítések idővel való feldolgozásával változnak, ami hatással van az egyező dokumentumok keresési pontszámaira. |
| Több kópia | A több replikát használó szolgáltatások esetében a lekérdezések párhuzamosan kerülnek kiadásra az egyes kópiákhoz. A keresési pontszám kiszámításához használt indexstatisztikák at replika alapon számítja ki a rendszer, az eredményeket egyesítve és a lekérdezési válaszban rendezve. A replikák többnyire egymás tükrei, de a statisztikák eltérhetnek az állapot kis különbségei miatt. Előfordulhat például, hogy az egyik kópia törölte a statisztikáikhoz hozzájáruló dokumentumokat, amelyeket más kópiákból egyesítettek. A replikastatisztikák közötti különbségek általában jobban észrevehetők a kisebb indexekben. |
| Azonos pontszámok | Ha több dokumentum pontszáma megegyezik, akkor bármelyik ük jelenhet meg először.  |

### <a name="consistent-ordering"></a>Konzisztens sorrend

Tekintettel a rugalmas idő az eredmények sorrendje, érdemes lehet más lehetőségeket is, ha a konzisztencia egy alkalmazás követelmény. A legegyszerűbb módszer a mezőérték szerinti rendezés, például minősítés vagy dátum szerint. Olyan esetekben, amikor egy adott mező , például egy minősítés vagy dátum szerint szeretne rendezni, explicit módon definiálhat egy [ `$orderby` kifejezést,](query-odata-filter-orderby-syntax.md)amely bármely **rendezhető**mezőre alkalmazható.

Egy másik lehetőség [egy egyéni pontozási profil](index-add-scoring-profiles.md)használata. A pontozási profilok segítségével jobban szabályozhatja a keresési eredményekben szereplő elemek rangsorolását, és növelheti az adott mezőkben található egyezéseket. A további pontozási logika segíthet felülírni a replikák közötti kisebb különbségeket, mivel az egyes dokumentumok keresési pontszámai távolabb vannak egymástól. Ehhez a megközelítéshez javasoljuk a [rangsorolási algoritmust.](index-ranking-similarity.md)

## <a name="hit-highlighting"></a>Találatok kiemelése

A találatkiemelés az eredmény egyező kifejezésére alkalmazott szövegformázásra (például félkövér vagy sárga kiemelésre) utal, így könnyen kiszúrható az egyezés. A lekéréshez a [lekéréshez](https://docs.microsoft.com/rest/api/searchservice/search-documents)a lekéréshez a lekéréshez található a leemelési utasítások. A keresőmotor címkékbe foglalja az `highlightPreTag` `highlightPostTag`egyező kifejezést, és a kód kezeli a választ (például félkövér betűtípust alkalmaz).

A formázás teljes kifejezéslekérdezésekre vonatkozik. A következő példában a leírás mezőben található "homokos", "homok", "strandok", "strand" kifejezések vannak megjelölve a kiemeléshez. Lekérdezések bővítése a motorban, mint például a fuzzy és a helyettesítő keresés, korlátozott mértékben támogatja a találat kiemelése.

```http
GET /indexes/hotels-sample-index/docs/search=sandy beaches&highlight=Description?api-version=2019-05-06 
```

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2019-05-06 
    {  
      "search": "sandy beaches",  
      "highlight": "Description"
    }
```

> [!IMPORTANT]
> A 2020. Az ezen időpont előtt létrehozott szolgáltatások nem változnak a kiemelési viselkedésükben. Ezzel a módosítással csak a teljes kifejezéslekérdezésnek megfelelő kifejezések et adja vissza a rendszer. Emellett meg lehet adni a kiemeléshez visszaadott töredékméretet is.
>
> Amikor olyan ügyfélkódot ír, amely a leírás kiemelését valósítja meg, vegye figyelembe ezt a változást. Ne feledje, hogy ez nem lesz hatással, ha nem hoz létre egy teljesen új keresési szolgáltatást.

## <a name="next-steps"></a>További lépések

Ha gyorsan szeretne keresési oldalt létrehozni az ügyfél számára, vegye figyelembe az alábbi lehetőségeket:

+ Az Application Generator ( [alkalmazásgenerátor)](search-create-app-portal.md)a portálon létrehoz egy HTML-oldalt keresősávval, csiszolt navigációval és képeket tartalmazó találati területtel.
+ [Hozza létre az első app c#](tutorial-csharp-create-first-app.md) egy oktatóanyag, amely épít egy funkcionális kliens. A mintakód a lapszámozott lekérdezéseket, a leütéskiemelését és a rendezést mutatja be.

Számos kódminta tartalmaz egy webes előtér-felületet, amely itt található: [New York City Jobs demo app](https://aka.ms/azjobsdemo), [JavaScript mintakód egy élő demo site](https://github.com/liamca/azure-search-javascript-samples), és [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).