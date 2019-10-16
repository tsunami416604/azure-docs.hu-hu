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
ms.openlocfilehash: 24886d8b8a7b679f6474789748a002c8d045a746
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331278"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Teljes szöveges keresés hozzáadása az Azure-Blobok adataihoz Azure Search használatával

Az Azure Blob Storage-ban tárolt különböző tartalomtípusok közötti keresés nehéz megoldás lehet. Az [Azure Search](search-what-is-azure-search.md)használatával azonban néhány kattintással indexelheti és keresheti meg a Blobok tartalmát. A Azure Search beépített integrációval rendelkezik a blob Storage-hoz való indexeléshez [ *, amely*](search-howto-indexing-azure-blob-storage.md) adatforrással kompatibilis funkciókat biztosít az indexeléshez.

## <a name="supported-content-types"></a>Támogatott tartalomtípusok

A blob Indexer tárolón való futtatásával a következő tartalomtípusokból származó szövegeket és metaadatokat lehet kinyerni egyetlen lekérdezéssel:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

A [mesterséges intelligenciát](search-blob-ai-integration.md) *készségkészlet* formájában is csatolhatja, hogy új információkat és struktúrát hozzon létre a blobokból, beleértve a képfájlok szövegének ábrázolását is. A AI-bővítés hozzáadásával kibonthatja a tartalomtípus-listát, hogy tartalmazza a JPEG és a PNG-t. Olyan képfeldolgozási képességeket biztosít, mint például az [optikai karakterfelismerés (OCR)](cognitive-search-skill-ocr.md) és a [vizuális funkciók](cognitive-search-skill-image-analysis.md) azonosítása, amelyek segítségével indexelheti az egyes képekben található vizuális tartalmat.

## <a name="search-through-your-blob-metadata"></a>Keresés a blob metaadatainak között
Egy gyakori forgatókönyv, amely megkönnyíti a tetszőleges tartalomtípusú Blobok rendezését, az egyéni metaadatok és a Rendszertulajdonságok indexelése az egyes blobokhoz. Így az összes blobra vonatkozó információ indexelve van, tekintet nélkül a Azure Search indexében tárolt adatokra. Az új index használatával folytathatja az összes blob Storage-tartalom rendezését, szűrését és aspektusát.

### <a name="indexing-json-blobs"></a>JSON-Blobok indexelése
Azure Search konfigurálható a JSON-t tartalmazó blobokban található strukturált tartalom kinyeréséhez. A Azure Search beolvashatja a JSON-blobokat, és elemezheti a strukturált tartalmat egy Azure Search dokumentum megfelelő mezőibe. A Azure Search olyan blobokat is tartalmazhat, amelyek JSON-objektumok tömbjét tartalmazzák, és az egyes elemeket egy külön Azure Search dokumentumra képezik le. Az elemzési mód beállítható úgy, hogy az az indexelő által létrehozott JSON-objektum típusát is befolyásolja.

## <a name="how-to-get-started"></a>A szolgáltatások használatba vétele

A Storage-fiók portálján közvetlenül is elindíthatja a lapot. Kattintson a **Azure Search hozzáadása** elemre egy olyan folyamat elindításához, amelyen kiválaszthat egy meglévő Azure Search szolgáltatást, vagy létrehozhat egy új szolgáltatást. Ha létrehoz egy új szolgáltatást, a rendszer kikeresi a Storage-fiók portáljának felhasználói felületét. Váltson vissza a Storage Portal lapra, és válassza újra a **Azure Search hozzáadása** lehetőséget, ahol kiválaszthatja a meglévő szolgáltatást. 

![](./media/search-blob-storage-integration/blob-blade.png)

Ha már rendelkezik egy meglévő keresési szolgáltatással ugyanahhoz az előfizetéshez, a **Azure Search Hozzáadás** gombra kattintva megnyithatja az adatimportálás varázslót, így azonnal áttekintheti az indexelést, a bővítést és az index definícióját.

[Létrehozhat egy Azure Search szolgáltatást](search-create-index-portal.md) is, és meghatározhatja azokat a blob-indexelő, amelyek a blob-tárolók tartalmát lehívhatják.

A következő rövid útmutatók és oktatóanyagok a blob-adatgyűjtést használják:

+ [Félig strukturált Blobok indexelése REST API-k használatával](search-semi-structured-data.md)
+ [AI-dúsítási folyamat létrehozása a portálon](cognitive-search-quickstart-blob.md)
+ [AI-dúsítási folyamat létrehozása a-benC#](cognitive-search-tutorial-blob-dotnet.md)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [BLOB-indexelő beállítása](search-howto-indexing-azure-blob-storage.md) 
