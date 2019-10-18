---
title: Teljes szöveges keresés hozzáadása az Azure Blob Storage
titleSuffix: Azure Search
description: Kinyerheti a tartalmat, és felépítheti az Azure-blobokat a teljes szöveges keresési index kiépítésekor Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: db54c7886f5256dab41325cd12f1b893be732d72
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529046"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Teljes szöveges keresés hozzáadása az Azure-Blobok adataihoz Azure Search használatával

Az Azure Blob Storage-ban tárolt különböző tartalomtípusok közötti keresés nehéz megoldás lehet. Az [Azure Search](search-what-is-azure-search.md)használatával azonban néhány kattintással indexelheti és keresheti meg a Blobok tartalmát. A Azure Search beépített integrációval rendelkezik a blob Storage-hoz való indexeléshez [ *, amely*](search-howto-indexing-azure-blob-storage.md) adatforrással kompatibilis funkciókat biztosít az indexeléshez.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Mit jelent a teljes szöveges keresés hozzáadása a blob-adathoz

Azure Search egy felhőalapú keresési szolgáltatás, amely indexelési és lekérdezési motorokat biztosít, amelyek a keresési szolgáltatásban üzemeltetett felhasználó által definiált indexeken működnek. A kereshető tartalmat a felhőben a lekérdezési motorral közösen keresheti meg a teljesítmény, a sebességű felhasználók pedig a keresési lekérdezésekből várt eredményekkel térnek vissza.

A Azure Search az indexelési rétegben integrálva van az Azure Blob Storage szolgáltatással, így a blob tartalmát olyan keresési dokumentumokként importálhatja, amelyek az ingyenes szöveges lekérdezéseket *és a* szűrési kifejezéseket támogató lekérdezési struktúrákba vannak indexelve. Mivel a blob tartalma egy keresési indexbe van indexelve, a blob-tartalmakhoz való hozzáférés a Azure Search lekérdezési funkcióinak teljes skáláját kihasználhatja.

Miután létrehozta és feltölti az indexet, a blob-tárolótól függetlenül létezik, de az indexelési műveletek újrafuttatásával frissítheti az indexet az alapul szolgáló tároló módosításaival. Az egyes Blobok időbélyeg-információi a változás észlelésére szolgálnak. Az ütemezett végrehajtást vagy igény szerinti indexelést a frissítési mechanizmusként is választhatja.

A bemenetek a Blobok egyetlen tárolóban, az Azure Blob Storage-ban. A Blobok szinte bármilyen típusú szöveges adattal rendelkezhetnek. Ha a Blobok képeket tartalmaznak, hozzáadhat AI-bővítést [a blob indexeléshez](search-blob-ai-integration.md) a képekből származó szövegek létrehozásához és kinyeréséhez.

A kimenet mindig Azure Search index, amely a gyors szöveges kereséshez, a lekérésekhez és a feltáráshoz használható az ügyfélalkalmazások számára. A között az indexelő folyamat architektúrája. A folyamat az *Indexelő* szolgáltatáson alapul, amelyet a cikk további részében ismertetett.

## <a name="start-with-services"></a>Kezdés a szolgáltatásokkal

Azure Search és Azure Blob Storage szükséges. A blob Storage-ban olyan tárolóra van szükség, amely a forrás tartalmát biztosítja.

A Storage-fiók portálján közvetlenül is elindíthatja a lapot. A bal oldali navigációs oldal **blob Service** területén kattintson a **Azure Search hozzáadása** elemre egy új szolgáltatás létrehozásához, vagy válasszon ki egy meglévőt. 

Miután hozzáadta Azure Search a Storage-fiókjához, a szabványos folyamat használatával indexelheti a Blobok adatait. Javasoljuk, hogy az **adatok importálása** varázslót Azure Search egy egyszerű kezdeti bevezetést, vagy hívja meg a REST API-kat egy olyan eszköz használatával, mint a Poster. Ez az oktatóanyag végigvezeti azon lépéseken, amelyekkel meghívja a REST API a Poster alkalmazásban: [index és keresés részben strukturált adatfájlok (JSON-Blobok) a Azure Searchban](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>BLOB-indexelő használata

Az *Indexelő* egy adatforrást támogató alszolgáltatás, amely belső logikával rendelkezik a mintavételi adatokhoz, a metaadatok beolvasásához, az adatok lekéréséhez és az adatok natív formátumokból való szerializálásához a további importáláshoz. 

Az Azure Storage-beli Blobok indexelése a [Azure Search blob Storage indexelő](search-howto-indexing-azure-blob-storage.md)használatával történik. Az indexelő az **adatimportálás** varázsló, a REST API vagy a .net SDK használatával hívható meg. A kódban ezt az indexelő kell használnia a típus megadásával, valamint az Azure Storage-fiókkal és a blob-tárolóval együtt tartalmazó kapcsolatok adatainak biztosításával. A blobokat egy virtuális könyvtár létrehozásával, amelyet aztán paraméterként adhat át, vagy szűrheti a fájltípusok kiterjesztését.

Az indexelő a "dokumentum repedése", a Blobok megnyitása a tartalom vizsgálatához. Az adatforráshoz való csatlakozás után ez a folyamat első lépése. A Blobok esetében ez az a hely, ahol a rendszer PDF-fájlokat, Office-dokumentumokat és más típusú tartalmakat észlel. A szöveg kibontásával nem számítunk fel díjat. Ha a Blobok képtartalmat tartalmaznak, a rendszer figyelmen kívül hagyja a képeket, hacsak nem [ad hozzá mesterséges intelligenciát](search-blob-ai-integration.md). A normál indexelés csak szöveges tartalomra vonatkozik.

A blob indexelő konfigurációs paramétereket tartalmaz, és támogatja a változások követését, ha az alapul szolgáló adatok elegendő információt biztosítanak. További információt a [Azure Search blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md)alapfunkciói című témakörben olvashat.

### <a name="supported-content-types"></a>Támogatott tartalomtípusok

A blob Indexer tárolón való futtatásával a következő tartalomtípusokból származó szövegeket és metaadatokat lehet kinyerni egyetlen lekérdezéssel:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>BLOB metaadatainak indexelése

Egy gyakori forgatókönyv, amely megkönnyíti a tetszőleges tartalomtípusú Blobok rendezését, az egyéni metaadatok és a Rendszertulajdonságok indexelése az egyes blobokhoz. Így az összes blobra vonatkozó információ indexelve van, tekintet nélkül a Azure Search indexében tárolt adatokra. Az új index használatával folytathatja az összes blob Storage-tartalom rendezését, szűrését és aspektusát.

### <a name="indexing-json-blobs"></a>JSON-Blobok indexelése
Azure Search konfigurálható a JSON-t tartalmazó blobokban található strukturált tartalom kinyeréséhez. A Azure Search beolvashatja a JSON-blobokat, és elemezheti a strukturált tartalmat egy Azure Search dokumentum megfelelő mezőibe. A Azure Search olyan blobokat is tartalmazhat, amelyek JSON-objektumok tömbjét tartalmazzák, és az egyes elemeket egy külön Azure Search dokumentumra képezik le. Az elemzési mód beállítható úgy, hogy az az indexelő által létrehozott JSON-objektum típusát is befolyásolja.

## <a name="search-blob-content-in-a-search-index"></a>A blob tartalmának keresése keresési indexben 

Az indexelés kimenete egy keresési index, amely az interaktív feltáráshoz használatos az ügyfélalkalmazás ingyenes szöveges és szűrt lekérdezéseit használva. A tartalom kezdeti feltárásához és ellenőrzéséhez javasoljuk, hogy a portálon kezdjen a [keresési tallózóval](search-explorer.md) a dokumentum-struktúra vizsgálatához. Használhatja az [egyszerű lekérdezési szintaxist](query-simple-syntax.md), a [teljes lekérdezési szintaxist](query-lucene-syntax.md)és a [szűrési kifejezés szintaxisát](query-odata-filter-orderby-syntax.md) a keresési Explorerben.

A több állandó megoldás a lekérdezési bemenetek összegyűjtése és a válasz megjelenítése keresési eredményekként egy ügyfélalkalmazás számára. Az alábbi C# oktatóanyag azt ismerteti, hogyan lehet keresési alkalmazást [létrehozni: hozza létre első alkalmazását Azure Searchban](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [BLOB-indexelő beállítása](search-howto-indexing-azure-blob-storage.md) 
