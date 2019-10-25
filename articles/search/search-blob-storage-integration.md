---
title: Teljes szöveges keresés hozzáadása az Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Kinyerheti a tartalmat, és hozzáadhat struktúrát az Azure-blobokhoz, amikor teljes szöveges keresési indexet hoz létre az Azure kognitív keresése.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 6bd96df1a2ed4ff7cde637a646ab514e89340b38
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792485"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Teljes szöveges keresés hozzáadása az Azure Blob-adataihoz az Azure Cognitive Search

Az Azure Blob Storage-ban tárolt különböző tartalomtípusok közötti keresés nehéz megoldás lehet. Az [Azure Cognitive Search](search-what-is-azure-search.md)használatával azonban néhány kattintással elvégezheti a Blobok tartalmának indexelését és keresését. Az Azure Cognitive Search beépített integrációval rendelkezik a blob Storage-hoz való indexeléshez [ *, amely*](search-howto-indexing-azure-blob-storage.md) adatforrással kompatibilis funkciókat biztosít az indexeléshez.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Mit jelent a teljes szöveges keresés hozzáadása a blob-adathoz

Az Azure Cognitive Search egy felhőalapú keresési szolgáltatás, amely indexelési és lekérdezési motorokat biztosít, amelyek a keresési szolgáltatásban üzemeltetett felhasználó által definiált indexeken működnek. A kereshető tartalmat a felhőben a lekérdezési motorral közösen keresheti meg a teljesítmény, a sebességű felhasználók pedig a keresési lekérdezésekből várt eredményekkel térnek vissza.

Az Azure Cognitive Search az indexelési rétegben integrálva van az Azure Blob Storage szolgáltatással, így a blob tartalmát olyan keresési dokumentumokként importálhatja, amelyek az ingyenes szöveges lekérdezéseket és szűrési kifejezéseket támogató lekérdezési struktúrákba vannak indexelve. . Mivel a blob tartalma egy keresési indexbe van indexelve, a blob-tartalmakhoz való hozzáférés az Azure Cognitive Search összes lekérdezési funkcióját kihasználhatja.

Miután létrehozta és feltölti az indexet, a blob-tárolótól függetlenül létezik, de az indexelési műveletek újrafuttatásával frissítheti az indexet az alapul szolgáló tároló módosításaival. Az egyes Blobok időbélyeg-információi a változás észlelésére szolgálnak. Az ütemezett végrehajtást vagy igény szerinti indexelést a frissítési mechanizmusként is választhatja.

A bemenetek a Blobok egyetlen tárolóban, az Azure Blob Storage-ban. A Blobok szinte bármilyen típusú szöveges adattal rendelkezhetnek. Ha a Blobok képeket tartalmaznak, hozzáadhat AI-bővítést [a blob indexeléshez](search-blob-ai-integration.md) a képekből származó szövegek létrehozásához és kinyeréséhez.

A kimenet mindig egy Azure Cognitive Search index, amely a gyors szöveges kereséshez, a lekérésekhez és a feltáráshoz használható az ügyfélalkalmazások számára. A között az indexelő folyamat architektúrája. A folyamat az *Indexelő* szolgáltatáson alapul, amelyet a cikk további részében ismertetett.

## <a name="start-with-services"></a>Kezdés a szolgáltatásokkal

Az Azure Cognitive Search és az Azure Blob Storage szükséges. A blob Storage-ban olyan tárolóra van szükség, amely a forrás tartalmát biztosítja.

A Storage-fiók portálján közvetlenül is elindíthatja a lapot. A bal oldali navigációs oldal **blob Service** területén kattintson az **Azure Cognitive Search hozzáadása** elemre egy új szolgáltatás létrehozásához, vagy válasszon ki egy meglévőt. 

Miután hozzáadta az Azure-Cognitive Search a Storage-fiókjához, a szabványos folyamat használatával indexelheti a Blobok adatait. Javasoljuk, hogy az **adatok importálása** varázslót az Azure Cognitive Search egyszerű kezdeti bevezetésre használja, vagy hívja meg a REST API-kat egy olyan eszközzel, mint például a Poster. Ez az oktatóanyag végigvezeti azon lépéseken, amelyekkel meghívja a REST API a Poster [szolgáltatásban: index és keresés részben strukturált adatfájlok (JSON-Blobok) az Azure Cognitive Searchban](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>BLOB-indexelő használata

Az *Indexelő* egy adatforrást támogató alszolgáltatás, amely belső logikával rendelkezik a mintavételi adatokhoz, a metaadatok beolvasásához, az adatok lekéréséhez és az adatok natív formátumokból való szerializálásához a további importáláshoz. 

Az Azure Storage-beli Blobok indexelése az [azure Cognitive Search blob Storage indexelő](search-howto-indexing-azure-blob-storage.md)használatával történik. Az indexelő az **adatimportálás** varázsló, a REST API vagy a .net SDK használatával hívható meg. A kódban ezt az indexelő kell használnia a típus megadásával, valamint az Azure Storage-fiókkal és a blob-tárolóval együtt tartalmazó kapcsolatok adatainak biztosításával. A blobokat egy virtuális könyvtár létrehozásával, amelyet aztán paraméterként adhat át, vagy szűrheti a fájltípusok kiterjesztését.

Az indexelő a "dokumentum repedése", a Blobok megnyitása a tartalom vizsgálatához. Az adatforráshoz való csatlakozás után ez a folyamat első lépése. A Blobok esetében ez az a hely, ahol a rendszer PDF-fájlokat, Office-dokumentumokat és más típusú tartalmakat észlel. A szöveg kibontásával nem számítunk fel díjat. Ha a Blobok képtartalmat tartalmaznak, a rendszer figyelmen kívül hagyja a képeket, hacsak nem [ad hozzá mesterséges intelligenciát](search-blob-ai-integration.md). A normál indexelés csak szöveges tartalomra vonatkozik.

A blob indexelő konfigurációs paramétereket tartalmaz, és támogatja a változások követését, ha az alapul szolgáló adatok elegendő információt biztosítanak. További információ az [Azure Cognitive Search blob Storage indexelő](search-howto-indexing-azure-blob-storage.md)alapfunkcióival kapcsolatban.

### <a name="supported-content-types"></a>Támogatott tartalomtípusok

A blob Indexer tárolón való futtatásával a következő tartalomtípusokból származó szövegeket és metaadatokat lehet kinyerni egyetlen lekérdezéssel:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>BLOB metaadatainak indexelése

Egy gyakori forgatókönyv, amely megkönnyíti a tetszőleges tartalomtípusú Blobok rendezését, az egyéni metaadatok és a Rendszertulajdonságok indexelése az egyes blobokhoz. Így az összes blobra vonatkozó információ indexelve van, tekintet nélkül a keresési szolgáltatásban tárolt indexbe. Az új index használatával folytathatja az összes blob Storage-tartalom rendezését, szűrését és aspektusát.

### <a name="indexing-json-blobs"></a>JSON-Blobok indexelése
Az indexelő úgy konfigurálható, hogy kinyerje a JSON-t tartalmazó blobokban található strukturált tartalmat. Az indexelő képes beolvasni a JSON-blobokat, és elemezni a strukturált tartalmat egy keresési dokumentum megfelelő mezőiben. Az indexelő olyan blobokat is tartalmazhat, amelyek JSON-objektumok tömbjét tartalmazzák, és az egyes elemeket egy külön keresési dokumentumra képezik le. Az elemzési mód beállítható úgy, hogy az az indexelő által létrehozott JSON-objektum típusát is befolyásolja.

## <a name="search-blob-content-in-a-search-index"></a>A blob tartalmának keresése keresési indexben 

Az indexelés kimenete egy keresési index, amely az interaktív feltáráshoz használatos az ügyfélalkalmazás ingyenes szöveges és szűrt lekérdezéseit használva. A tartalom kezdeti feltárásához és ellenőrzéséhez javasoljuk, hogy a portálon kezdjen a [keresési tallózóval](search-explorer.md) a dokumentum-struktúra vizsgálatához. Használhatja az [egyszerű lekérdezési szintaxist](query-simple-syntax.md), a [teljes lekérdezési szintaxist](query-lucene-syntax.md)és a [szűrési kifejezés szintaxisát](query-odata-filter-orderby-syntax.md) a keresési Explorerben.

A több állandó megoldás a lekérdezési bemenetek összegyűjtése és a válasz megjelenítése keresési eredményekként egy ügyfélalkalmazás számára. Az alábbi C# oktatóanyag azt ismerteti, hogyan lehet keresési alkalmazást [létrehozni: hozza létre első alkalmazását az Azure Cognitive Searchban](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Következő lépések

+ [Blobok feltöltése, letöltése és listázása a Azure Portal (Azure Blob Storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [BLOB-indexelő beállítása (Azure Search)](search-howto-indexing-azure-blob-storage.md) 
