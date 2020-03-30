---
title: Teljes szöveges keresés hozzáadása az Azure Blob Storage-hoz
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive earch teljes szöveges keresési indexének létrehozásakor kinyerheti a tartalmat, és struktúrát adhat hozzá az Azure-blobokhoz.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496493"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Teljes szöveges keresés hozzáadása az Azure blobadataihoz az Azure Cognitive Search használatával

Az Azure Blob storage-ban tárolt tartalomtípusok között való keresés nehéz probléma lehet. Az [Azure Cognitive Search](search-what-is-azure-search.md)használatával azonban néhány kattintással indexelheti és keresheti a blobok tartalmát. Az Azure Cognitive Search beépített integrációval rendelkezik a Blob storage-ból való indexeléshez egy [*Blob indexelőn*](search-howto-indexing-azure-blob-storage.md) keresztül, amely adatforrás-figyelő képességekkel növeli az indexelést.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Mit jelent a teljes szöveges keresés hozzáadása a blobadatokhoz?

Az Azure Cognitive Search egy felhőalapú keresési szolgáltatás, amely indexelési és lekérdezési motorok, amelyek a keresési szolgáltatásban üzemeltetett felhasználó által definiált indexek felett működnek. A kereshető tartalom és a felhőlekérdezési motor közös elhelyezése szükséges a teljesítményhez, és olyan sebességgel tér vissza az eredményekhez, amelyeket a felhasználók a keresési lekérdezésektől elvárnak.

Az Azure Cognitive Search integrálható az Azure Blob storage az indexelő rétegen, importálja a blob tartalmát keresési dokumentumok, amelyek indexelt *fordított indexek* és más lekérdezési struktúrák, amelyek támogatják a szabad formátumú szöveges lekérdezések és a szűrő kifejezések. Mivel a blob tartalom indexelve van egy keresési index, a blob tartalomhoz való hozzáférés az Azure Cognitive Search lekérdezési funkciók teljes körét használhatja ki.

Az index létrehozása és feltöltése után a blobtárolótól függetlenül létezik, de újrafuttathatja az indexet az index az alapul szolgáló tároló módosításaival. Az egyes blobok időbélyeg-adatait a rendszer a változásészleléshez használja. Választhat az ütemezett végrehajtás vagy az igény szerinti indexelés, mint a frissítési mechanizmus.

Bemenetek a blobok, egyetlen tárolóban, az Azure Blob storage-ban. A blobok szinte bármilyen szöveges adat lehetnek. Ha a blobok képeket tartalmaznak, hozzáadhat [jat-bővítés blob indexelés](search-blob-ai-integration.md) létrehozásához és kibontásához szöveget képekből.

A kimenet mindig egy Azure Cognitive Search index, amelyet gyors szöveges kereséshez, visszakereséshez és ügyfélalkalmazások feltárásához használnak. A kettő között maga az indexelő folyamatarchitektúra. A folyamat az *indexelő* szolgáltatáson alapul, amelyet a cikkben tovább tárgyalunk.

## <a name="start-with-services"></a>Kezdje a szolgáltatásokkal

Az Azure Cognitive Search és az Azure Blob storage szükséges. A Blob storage-on belül olyan tárolóra van szüksége, amely forrástartalmat biztosít.

Közvetlenül a tárfiók portálján indítható el. A bal oldali navigációs lapon a **Blob szolgáltatás** csoportban kattintson az Azure Cognitive **Search hozzáadása** elemre egy új szolgáltatás létrehozásához, vagy válasszon ki egy meglévőt. 

Miután hozzáadja az Azure Cognitive Search-et a tárfiókhoz, a szabványos folyamatot követheti a blobadatok indexeléséhez. Azt javasoljuk, hogy az **Adatok importálása** varázsló az Azure Cognitive Search egy egyszerű kezdeti bevezetés, vagy hívja meg a REST API-k segítségével egy eszköz, mint a Postman. Ez az oktatóanyag végigvezeti a REST API hívásának lépésein a Postman: [Index és a keresés félig strukturált adatok (JSON-blobok) az Azure Cognitive Search.](search-semi-structured-data.md) 

## <a name="use-a-blob-indexer"></a>Blob indexelő használata

Az *indexelő* egy adatforrás-érzékeny alszolgáltatás, amely belső logikával rendelkezik az adatok mintavételezéséhez, a metaadatok olvasásához, az adatok beolvasásához és a natív formátumokból származó adatok JSON-dokumentumokba történő szerializálásához későbbi importáláshoz. 

Az Azure Storage-ban lévő blobok indexelése az [Azure Cognitive Search Blob storage indexelővel lesz indexelve.](search-howto-indexing-azure-blob-storage.md) Ezt az indexelőt az **Adatok importálása** varázslóval, egy REST API-val vagy a .NET SDK-val hívhatja meg. A kódban ezt az indexelőt a típus beállításával és az Azure Storage-fiókot és egy blobtárolóval együtt tartalmazó kapcsolati adatok megadásával használja. A blobok részhalmaza egy virtuális könyvtár létrehozásával, amelyet paraméterként továbbíthat, vagy szűrhet egy fájltípus-kiterjesztésre.

Az indexelő nem a "dokumentum repedés", megnyitva egy blobot, hogy vizsgálja meg a tartalmat. Az adatforráshoz való csatlakozás után ez az első lépés a folyamatban. Blob-adatok esetén itt található a PDF-dokumentum, az irodai dokumentumok és más tartalomtípusok észlelése. Dokumentum csinos szöveg kinyerése nem számít. Ha a blobok képtartalmat tartalmaznak, a rendszer figyelmen kívül hagyja a képeket, hacsak nem [ad hozzá AI-bővítést.](search-blob-ai-integration.md) A szabványos indexelés csak a szöveges tartalomra vonatkozik.

A Blob indexelő jön a konfigurációs paramétereket, és támogatja a változás követés, ha az alapul szolgáló adatok elegendő információt szolgáltatnak. Az Azure Cognitive Search Blob [storage indexelőjének](search-howto-indexing-azure-blob-storage.md)alapvető funkcióiról többet is megtudhat.

### <a name="supported-content-types"></a>Támogatott tartalomtípusok

Ha egy Blob-indexelőt futtat egy tárolón, egyetlen lekérdezéssel kinyerheti a szöveget és a metaadatokat a következő tartalomtípusokból:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Blob metaadatainak indexelése

Egy gyakori forgatókönyv, amely megkönnyíti a blobok bármilyen tartalomtípussal történő rendezését, az egyéni metaadatok és a rendszertulajdonságok indexelése az egyes blobokhoz. Ily módon az összes blob adatait a dokumentum típusától függetlenül indexeli a keresési szolgáltatás ban tárolt index. Az új index használatával ezután folytathatja a rendezést, a szűrést és a facet-t az összes Blob storage-tartalomban.

### <a name="indexing-json-blobs"></a>JSON-blobok indexelése
Indexelők konfigurálható a JSON-t tartalmazó blobokban található strukturált tartalom kinyerése. Az indexelő olvashatja a JSON-blobokat, és elemezheti a strukturált tartalmat a keresési dokumentum megfelelő mezőibe. Indexelők is hozhat blobok, amelyek egy tömb JSON-objektumok és leképezheti az egyes elemek egy külön keresési dokumentum. Az elemzési módot beállíthatja úgy, hogy befolyásolja az indexelő által létrehozott JSON-objektum típusát.

## <a name="search-blob-content-in-a-search-index"></a>Keresési blobtartalom keresési indexben 

Az indexelés kimenete egy keresési index, amelyet szabad szöveg és az ügyfélalkalmazásban lévő szűrt lekérdezések használatával interaktív feltárásra használnak. A tartalom kezdeti feltárásához és ellenőrzéséhez javasoljuk, hogy kezdje a [Keresési intézővel](search-explorer.md) a portálon a dokumentumszerkezet vizsgálatához. A Keresés kezelőjében [használhatja az egyszerű lekérdezésszintaxist](query-simple-syntax.md), [a teljes lekérdezés szintaxisát](query-lucene-syntax.md)és a [kifejezés szintaxisát.](query-odata-filter-orderby-syntax.md)

Egy állandó megoldás a lekérdezési bemenetek összegyűjtése és a válasz megjelenítése keresési eredményekként egy ügyfélalkalmazásban. A következő C# oktatóanyag bemutatja, hogyan hozhat létre egy keresési alkalmazást: [Az első alkalmazás létrehozása az Azure Cognitive Search alkalmazásban.](tutorial-csharp-create-first-app.md)

## <a name="next-steps"></a>További lépések

+ [Blobok feltöltése, letöltése és listázása az Azure Portalon (Azure Blob storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Blob indexelő beállítása (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
