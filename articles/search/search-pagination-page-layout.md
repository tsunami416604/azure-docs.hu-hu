---
title: Az Azure Search keresési eredmények oldalakra |} A Microsoft Docs
description: Az Azure Search szolgáltatásban a Microsoft Azure-ban üzemeltetett felhőalapú keresési szolgáltatás tördelés.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 08/29/2016
ms.author: heidist
ms.openlocfilehash: 8953be2be77c14a82294e56ac60b8bc993ec6c2f
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527069"
---
# <a name="how-to-page-search-results-in-azure-search"></a>A keresési eredmények oldalakra tördelése az Azure Search-ben
Ez a cikk útmutatást nyújt a keresési eredmények, például a teljes száma, a dokumentum lekéréséhez, a rendezési sorrend és navigációs oldal szokásos megoldások szabványos elemeit megvalósítása az Azure Search szolgáltatás REST API használatával.

Az alábbiakban említett minden esetben keresztül megadott oldal kapcsolatos beállításokat, amely hozzájárul az adatok és információk a keresési eredmények oldalát az [dokumentum keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) az Azure Search szolgáltatás küldött kérelmeket. A kérelemnek tartalmaznia egy GET parancs, elérési út, és tájékoztatja a szolgáltatást milyen kérelem lekérdezési paraméterek és hogyan határozhatja meg a választ.

> [!NOTE]
> Kérés számos olyan elemek, például a szolgáltatás URL-CÍMÉT és az elérési út, HTTP-műveletet, `api-version`, és így tovább. Kihagytuk hogy vágott a példákat, jelölje ki, amely fontos tördelés szintaxisát. Tekintse át a [Azure Search szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice) részletesen ismertető szintaxis.
> 
> 

## <a name="total-hits-and-page-counts"></a>Találatok és a lapok száma
Bemutató egy lekérdezés által visszaadott eredmények teljes száma, és a kisebb adattömbökben, majd ezeket a eredményt adnak vissza az alapvető fontosságú, gyakorlatilag az összes keresési oldalakba.

![][1]

Az Azure Search használata a `$count`, `$top`, és `$skip` vissza ezeket az értékeket a paramétereket. Az alábbi példa bemutatja egy mintakérelmet a visszaadott találatok `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

15 csoportokban lévő dokumentumok lekéréséhez, és is megjelenítheti a találatok, az első oldal kezdőár:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Eredmények oldalankénti két feltétel szükséges `$top` és `$skip`, ahol `$top` Itt adhatja meg, hány elemek egy kötegben visszaadandó és `$skip` Itt adhatja meg, hány elem kihagyásához. A következő példában a növekményes ezzel a művelettel az egyes lapon látható a következő 15 elemek jelzi a `$skip` paraméter.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Elrendezés
A keresési eredmények oldalon érdemes asztalnak a miniatűrjére, a mezőket egy részhalmazát és a egy teljes termékkiadásban lapra mutató hivatkozást.

 ![][2]

Használja az Azure Search szolgáltatásban `$select` és a egy keresési parancsot a felület megvalósítása.

A mozaik elrendezés mezőket egy részhalmazát adja vissza:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Képek és adathordozó-fájlok nem közvetlenül kereshető, és egy másik tárolási platform, például az Azure Blob storage, a költségek csökkentése érdekében kell tárolni. Az index és a dokumentumok egy mezőt, amely tárolja a külső tartalom URL-címét határozza meg. Ezután használhatja a mezőt, a lemezkép-hivatkozás. A dokumentum a kép URL-Címének kell lennie.

Beolvasni a leírást termékoldalán egy **onClick** esemény, használja [keresési dokumentum](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) megadni a dokumentum lekérni a kulcsot. A kulcs az adattípus `Edm.String`. Ebben a példában van *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Rendezés találati pontosságot, minősítés vagy ár
Rendezés találati pontosság gyakran alapértelmezés szerint orders, de a gyakori, hogy alternatív rendezési rendelések könnyen elérhető, hogy az ügyfelek egy eltérő sorrend be gyorsan is átütemezésével meglévő eredmények.

 ![][3]

Az Azure Search szolgáltatásban rendezés alapján a `$orderby` összes mezőt indexeli, amelyek kifejezése `"Sortable": true.`

Relevancia alapján végzett erősen kapcsolódik a pontozási profilok. Segítségével az alapértelmezett pontozási, amely szövegelemzés és statisztikai támaszkodik egy keresési kifejezést a dokumentumok, amelyek további vagy erősebb egyezés fog magasabb pontszámok a rangsorolt típusú minden eredmény.

Alternatív a rendezési sorrend azok **onClick** eseményeket, amelyek visszahívást olyan módszer, amely összeállítja a rendezési sorrendet. Ha például adott ezt oldal elem:

 ![][4]

Akkor kell létrehoznia egy metódushoz, amely fogad el bemenetként a kijelölt rendezési beállítás, és a egy társított ezt a lehetőséget a feltétel számára rendezett listát ad vissza.

 ![][5]

> [!NOTE]
> Bár az alapértelmezett pontozási számos forgatókönyv esetében elegendő, javasoljuk, hogy inkább az egyéni a pontozási profilok relevancia alapján végzett egyik. Egy egyéni relevanciaprofil boost elemek, amelyek több előnyös, ha az üzleti egy megoldást kínál. Lásd: [a relevanciaprofil felvétele](https://docs.microsoft.com/rest/api/searchservice/Add-scoring-profiles-to-a-search-index) további információt. 
> 
> 

## <a name="faceted-navigation"></a>Jellemzőalapú navigáció
Keresés a navigációban szokás eredmények oldalon gyakran oldalán vagy a lap tetején található. Az Azure Search szolgáltatásban a jellemzőalapú navigáció biztosít önállóan irányított keresés előre definiált szűrők alapján. Lásd: [Jellemzőalapú navigáció az Azure Search](search-faceted-navigation.md) részleteiről.

## <a name="filters-at-the-page-level"></a>A lapszintű szűrők
Ha a megoldás kialakításának tartalmazza az adott típusú tartalom (például olyan online kereskedelmi alkalmazás megjelenik az oldal tetején lévő részlegek) dedikált keresési oldalak, szúrhat be egy kifejezést mellett egy **onClick**esemény előszűrt állapotban lévő oldal megnyitásához. 

Egy szűrő vagy anélkül egy keresési kifejezést is elküldheti. Például a következő kérelmet szűrést márkanév, csak a megfelelő, dokumentumok visszaadása.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Lásd: [dokumentumok keresése (Azure Search API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) bővebben `$filter` kifejezéseket.

## <a name="see-also"></a>Lásd még:
* [Az Azure Search szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/searchservice)
* [Indexműveletek](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
* [A dokumentum műveletek](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
* [Jellemzőalapú navigáció az Azure Search szolgáltatásban](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
