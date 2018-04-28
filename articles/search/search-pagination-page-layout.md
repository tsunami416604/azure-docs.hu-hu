---
title: Útmutató az Azure Search keresési eredmények lapon |} Microsoft Docs
description: Az Azure Search, egy üzemeltetett felhőalapú keresőszolgáltatás, a Microsoft Azure tördelési.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 08/29/2016
ms.author: heidist
ms.openlocfilehash: 3ef946c6c0ab9c111932b3145fd46ae6ef2684cd
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-page-search-results-in-azure-search"></a>A keresési eredmények oldalakra tördelése az Azure Search-ben
Ez a cikk az Azure Search szolgáltatás REST API használatával valósítja meg a keresési eredmények oldalának, például az érintett teljes, a dokumentum beolvasása, a rendezési sorrend és a navigációs szokásos megoldások szabványos elemeit nyújt útmutatást.

Az alábbiakban leírt minden esetben keresztül megadott lapokkal kapcsolatos beállításokat, amelyek adatokat vagy a keresési eredmények oldalának információkat a [keresés a dokumentum](http://msdn.microsoft.com/library/azure/dn798927.aspx) az Azure Search szolgáltatás küldött kérelmeket. A kérelemnek tartalmaznia egy GET parancs, a elérési utat, és a lekérdezési paraméterek, amely tájékoztatja a szolgáltatás milyen vonatkozó kérelem és a hogyan állítson össze a választ.

> [!NOTE]
> Kérés számos olyan elemeket, például egy URL-címe és az elérési út, HTTP-műveletet, `api-version`, és így tovább. Kivonatosan mutatja azt levágja a példák, jelölje ki, amely fontos tördelési szintaxisát. Tekintse meg a [Azure Search szolgáltatás REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx) dokumentációjában szintaxis található.
> 
> 

## <a name="total-hits-and-page-counts"></a>Találatok és a lap száma
Egy lekérdezés által visszaadott eredmények száma megjelenítő, és majd visszaadni az eredmények kisebb csoportjai, az alapvető gyakorlatilag az összes keresési lapra.

![][1]

Az Azure Search, használja a `$count`, `$top`, és `$skip` paramétereit, és ezeket az értékeket adja vissza. A következő példa bemutatja, adja vissza a találatok összesen mintát kér `@OData.count`:

        GET /indexes/onlineCatalog/docs?$count=true

15 csoportokban dokumentumok beolvasása, és a találatok, az első lap kezdődő is megjelenítése:

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

Eredmények paginating szükséges az `$top` és `$skip`, ahol `$top` határozza meg, hány elemek egy kötegelt válasz és `$skip` határozza meg, hány elemek kihagyhatja. A következő példában a növekményes ugráskor minden lapon látható a következő 15 elemek jelzi a `$skip` paraméter.

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=0&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=15&$count=true

        GET /indexes/onlineCatalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Elrendezés
A keresési eredmények oldalát előfordulhat, hogy kívánja megjeleníteni a miniatűr képére, mezők és a teljes termék lapra mutató hivatkozást.

 ![][2]

Használja az Azure Search `$select` és a keresési parancs, ez a felület megvalósítása.

Egy mozaik elrendezés mezők vissza:

        GET /indexes/ onlineCatalog/docs?search=*&$select=productName,imageFile,description,price,rating 

Képek és médiafájlok nem közvetlenül kereshető, és egy másik tárolási platform, például az Azure Blob Storage tárolóban, költségek csökkentése érdekében kell tárolni. Az index és a dokumentumok határozza meg az URL-címet a külső tartalom tárol. A mezőben egy Képhivatkozás módon használhatja. A kép URL-című dokumentumban kell lennie.

A termék leírását tartalmazó lapon a beolvasandó egy **onClick** esemény, használja [keresési dokumentum](http://msdn.microsoft.com/library/azure/dn798929.aspx) felelt meg a kulcs a dokumentum beolvasása. A kulcs adattípusa `Edm.String`. Az ebben a példában is *246810*. 

        GET /indexes/onlineCatalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Rendezési szempont relevanciájának, minősítés vagy ár
Sorrendjét relevanciájának gyakran alapértelmezett, de a gyakori, hogy alternatív rendezési rendelések azonnal elérhetők, hogy az ügyfelek is gyorsan átütemezésével a meglévő eredményeit azokat egy másik sorrend.

 ![][3]

Az Azure Search rendezés alapul a `$orderby` kifejezés indexeli, mezők `"Sortable": true.`

Relevanciájának erősen kapcsolódik a pontozási profil. Segítségével az alapértelmezett pontozási, amely szöveg elemzése és a statisztika támaszkodik a dokumentumok további vagy erősebb egyezés Ugrás a kívánt keresőkifejezést a magasabb pontszámok rangsorolni levő összes eredményt.

Alternatív a rendezési sorrend társított általában **onClick** eseményeket, amelyek a számon olyan módszer, amelyik a rendezési sorrend alkot. Például adja meg a page eleme:

 ![][4]

Fogad el bemenetként a kijelölt rendezési beállítást, és ez a lehetőség társított feltétel rendezett listák metódus hozna létre.

 ![][5]

> [!NOTE]
> Az alapértelmezett pontozási nem elegendő-e több forgatókönyv, de javasolt relevanciájának alapozva egyéni pontozási profil helyette. Egyéni pontozási profil akadályozható meg, amelyek az üzleti több hasznos program elemekre. Lásd: [a relevanciaprofil felvétele](http://msdn.microsoft.com/library/azure/dn798928.aspx) további információt. 
> 
> 

## <a name="faceted-navigation"></a>Jellemzőalapú navigáció
Keresési navigációs esetében gyakori, a találatokat tartalmazó lap, gyakran oldalán vagy a lap tetején található meg. Az Azure Search jellemzőalapú navigációs biztosít irányuló keresési előre definiált szűrők alapján. Lásd: [az Azure Search Jellemzőalapú navigációs](search-faceted-navigation.md) részleteiről.

## <a name="filters-at-the-page-level"></a>A lap szintjén szűrők
Ha a megoldás kialakításának részét dedikált keresési lapok az adott típusú tartalmakat (például egy online kereskedelmi alkalmazás, amely rendelkezik a lap tetején felsorolt osztályok), egy kifejezést mellett is beszúrhat egy **onClick**esemény előszűrt állapotban oldal megnyitásához. 

Egy szűrőt, vagy egy keresési kifejezés nélkül küldhet. Például a következő kérés márka neve csak a megfelelő azt dokumentumok visszaadó szűrést.

        GET /indexes/onlineCatalog/docs?$filter=brandname eq ‘Microsoft’ and category eq ‘Games’

Lásd: [dokumentumok keresése (Azure Search API)](http://msdn.microsoft.com/library/azure/dn798927.aspx) kapcsolatos további információk `$filter` kifejezések.

## <a name="see-also"></a>Lásd még:
* [Az Azure Search szolgáltatás REST API](http://msdn.microsoft.com/library/azure/dn798935.aspx)
* [Indexművelet](http://msdn.microsoft.com/library/azure/dn798918.aspx)
* [A dokumentum műveletek](http://msdn.microsoft.com/library/azure/dn800962.aspx)
* [Videók és oktatóanyagok Azure Search kapcsolatos](search-video-demo-tutorial-list.md)
* [Az Azure Search jellemzőalapú navigáció](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png 
