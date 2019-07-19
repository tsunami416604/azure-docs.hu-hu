---
title: A keresési eredmények használata – Azure Search
description: A keresési eredmények strukturálása és rendezése, a dokumentumok számának beolvasása, valamint a tartalom navigációjának hozzáadása a Azure Search keresési eredményeihez.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb86a75be464cb78a16170626bc96778d43bb8b6
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67974626"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Keresési eredmények használata Azure Search
Ez a cikk útmutatást nyújt a keresési eredmények oldalának szabványos elemeinek megvalósításához, például az összesített számokhoz, a dokumentumok lekéréséhez, a rendezési sorrendekhez és a navigáláshoz. Az oldalhoz kapcsolódó olyan beállítások, amelyek a keresési eredményeknek megfelelő adatokat vagy információkat járulnak hozzá, a Azure Search szolgáltatásnak küldött [keresési dokumentumok](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) között adhatók meg. 

A REST API a kérelmek között szerepel a GET parancs, az elérési út és a lekérdezési paraméterek, amelyek tájékoztatják a szolgáltatást a kért szolgáltatásról, valamint a válasz kialakításának módjáról. A .NET SDK-ban az egyenértékű API a [DocumentSearchResult osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Számos mintakód tartalmaz egy webes frontend felületet, amelyet itt talál: [New York City Jobs bemutató alkalmazás](https://azjobsdemo.azurewebsites.net/) -és [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> Egy érvényes kérelem számos elemet tartalmaz, például egy szolgáltatás URL-címét és elérési útját, http `api-version`-műveletét stb. A rövidség kedvéért a példákat úgy vágja ki, hogy csak a tördeléshez kapcsolódó szintaxist emelje ki. További információ a kérelem szintaxisáról: [Azure Search szolgáltatás Rest](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Találatok és oldalszámok összesen

A lekérdezésből visszaadott eredmények teljes számát jeleníti meg, majd az eredményeket kisebb adattömbökben adja vissza, ami gyakorlatilag minden keresési oldalhoz elengedhetetlen.

![][1]

A Azure Search a, `$count` `$top`a és `$skip` a paramétereket használja az értékek visszaadásához. Az alábbi példa egy "online-katalógus" nevű index összes találatára vonatkozó minta-kérést mutat be, `@odata.count`amelyet a következőként adott vissza:

    GET /indexes/online-catalog/docs?$count=true

Kérje le a dokumentumokat 15 csoportba, és jelenítse meg az összes találatot, az első oldalon kezdődően:

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

Az oldalszámozási eredményekhez `$top` a `$skip`és a `$top` is szükség van, ahol a meghatározza, hogy hány elemet `$skip` szeretne visszaadni egy kötegben, és megadja, hogy hány elemet szeretne kihagyni. A következő példában az egyes lapok a következő 15 elemet jelenítik meg, amelyeket a `$skip` paraméter növekményes ugrásai jeleznek.

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*&$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout

A keresési eredmények lapon lehet, hogy meg szeretné jeleníteni a miniatűr képét, a mezők egy részhalmazát, valamint egy teljes termékre mutató hivatkozást.

 ![][2]

A Azure Search a és egy `$select` [keresési API-kérést](https://docs.microsoft.com/rest/api/searchservice/search-documents) használ a felhasználói élmény megvalósításához.

Mezők egy részhalmazának visszaküldése mozaik elrendezéshez:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

A képek és a médiafájlok nem kereshetők közvetlenül, és egy másik tárolási platformon, például az Azure Blob Storage-ban tárolódnak a költségek csökkentése érdekében. Az index és a dokumentumok területen adjon meg egy mezőt, amely a külső tartalom URL-címét tárolja. Ezt követően a mezőt képhivatkozásként is használhatja. A rendszerkép URL-címének szerepelnie kell a dokumentumban.

Egy **OnClick** esemény termékleírási oldalának lekéréséhez használja a [keresési dokumentumot](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) a beolvasandó dokumentum kulcsaként. A kulcs `Edm.String`adattípusa:. Ebben a példában ez *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Rendezés relevancia, minősítés vagy ár szerint

A rendezési sorrendek gyakran az alapértelmezett érték, de gyakori, hogy az alternatív rendezési sorrendek azonnal elérhetők legyenek, így az ügyfelek gyorsan átrendezhetik a meglévő eredményeket egy másik rangsorba.

 ![][3]

Azure Search a rendezés a `$orderby` kifejezésen alapul, az összes olyan mező esetében, amely `"Sortable": true.` egy `$orderby` záradékként indexelt, OData kifejezés. A szintaxissal kapcsolatos további információkért lásd: [OData kifejezés szintaxisa szűrőkhöz és Order-by záradékokhoz](query-odata-filter-orderby-syntax.md).

A relevancia erősen társítva van pontozási profilokhoz. Használhatja az alapértelmezett pontozást, amely a szöveges elemzésre és a statisztikákra támaszkodik az összes eredmény rangsorolása érdekében, és a keresési kifejezés több vagy erősebb egyezést eredményező dokumentumoknál magasabb pontszámot fog keresni.

Az alternatív rendezési sorrendek általában olyan **OnClick** -eseményekhez vannak társítva, amelyek a rendezési sorrendet felépítő metódusnak hívnak vissza. Például az oldal eleme:

 ![][4]

Hozzon létre egy metódust, amely fogadja a kiválasztott rendezési lehetőséget bemenetként, és egy rendezett listát ad vissza a beállításhoz társított feltételekhez.

 ![][5]

> [!NOTE]
> Habár az alapértelmezett pontozás számos forgatókönyv esetében elegendő, javasoljuk, hogy az egyéni pontozási profilra alapozza a fontosságot. Az egyéni pontozási profilok lehetővé teszik az üzleti szempontból előnyös elemek növelését. További információ: [pontozási profilok hozzáadása](index-add-scoring-profiles.md) .
>

## <a name="faceted-navigation"></a>Jellemzőalapú navigáció

A keresési navigáció gyakori az eredmények oldalon, gyakran az oldal oldalán vagy tetején található. Azure Search a csiszolatlan navigálás az előre definiált szűrők alapján önálló irányított keresést tesz lehetővé. A részletekért tekintse [meg a Azure Search csiszolt navigáció](search-faceted-navigation.md) című témakört.

## <a name="filters-at-the-page-level"></a>Szűrők az oldal szintjén

Ha a megoldás kialakítása speciális keresési lapokat tartalmaz bizonyos típusú tartalmakhoz (például egy online kereskedelmi alkalmazáshoz, amely az oldal tetején található részlegeket tartalmazza), akkor egy **OnClick** eseményhez kapcsolódó [szűrési kifejezést](search-filters.md) szúrhat be a következőre: egy oldal megnyitása előre szűrt állapotban.

A szűrőket keresési kifejezéssel vagy anélkül is elküldheti. A következő kérelem például a márkanév alapján fog szűrni, és csak azokat a dokumentumokat adja vissza, amelyek megfelelnek a névnek.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

A `$filter` kifejezésekkel kapcsolatos további információkért tekintse meg a [dokumentumok keresése (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) című témakört.

## <a name="see-also"></a>Lásd még:

- [Azure Search szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice)
- [Indexelési műveletek](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Dokumentumok műveletei](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Sokoldalú Navigálás Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
