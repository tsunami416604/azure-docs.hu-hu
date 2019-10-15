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
ms.openlocfilehash: 1aec65ab08cd1c0711e51a222a8e674ef56ef508
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312183"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Teljes szöveges keresés hozzáadása az Azure-Blobok adataihoz Azure Search használatával

Az Azure Blob Storage-ban tárolt különböző tartalomtípusok közötti keresés nehéz megoldás lehet. Az [Azure Search](search-what-is-azure-search.md)használatával azonban néhány kattintással indexelheti és keresheti meg a Blobok tartalmát. A Azure Search beépített integrációval rendelkezik a blob Storage-hoz való indexeléshez [ *, amely*](search-howto-indexing-azure-blob-storage.md) adatforrással kompatibilis funkciókat biztosít az indexeléshez.

## <a name="supported-content-types"></a>Támogatott tartalomtípusok

A blob Indexer tárolón való futtatásával a következő tartalomtípusokból származó szövegeket és metaadatokat lehet kinyerni egyetlen lekérdezéssel:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

A [mesterséges intelligenciát](search-blob-ai-integration.md) *készségkészlet* formájában is csatolhatja, hogy új információkat és struktúrát hozzon létre a blobokból, beleértve a képfájlok szövegének ábrázolását is. A AI-bővítés hozzáadásával kibonthatja a tartalomtípus-listát, hogy tartalmazza a JPEG és a PNG-t. Olyan képfeldolgozási képességeket biztosít, mint például az [optikai karakterfelismerés (OCR)](cognitive-search-skill-ocr.md) és a [vizuális funkciók](cognitive-search-skill-image-analysis.md) azonosítása, amelyek segítségével indexelheti az egyes képekben található vizuális tartalmat.

## <a name="search-through-your-blob-metadata"></a>Keresés a blob metaadatainak között
Egy gyakori forgatókönyv, amely megkönnyíti a tetszőleges tartalomtípusú Blobok rendezését, az egyéni metaadatok és a Rendszertulajdonságok indexelése az egyes blobokhoz. Így az összes blobra vonatkozó információ indexelve van, tekintet nélkül a dokumentum típusára. Ezután folytathatja az összes blob Storage-tartalom rendezését, szűrését és aspektusát.

[További információ a blob-metaadatok indexeléséről.](https://aka.ms/azsblobmetadataindexing)

## <a name="index-and-search-through-json-blobs"></a>Indexelés és keresés JSON-blobokon
Azure Search konfigurálható a JSON-t tartalmazó blobokban található strukturált tartalom kinyeréséhez. A Azure Search beolvashatja a JSON-blobokat, és elemezheti a strukturált tartalmat egy Azure Search dokumentum megfelelő mezőibe. A Azure Search olyan blobokat is tartalmazhat, amelyek JSON-objektumok tömbjét tartalmazzák, és az egyes elemeket egy külön Azure Search dokumentumra képezik le.

A JSON-elemzés jelenleg nem konfigurálható a portálon keresztül. [További információ a JSON-elemzésről Azure Searchban.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Gyors útmutató
Azure Search közvetlenül a blob Storage portál lapról adható hozzá a blobokhoz.

![](./media/search-blob-storage-integration/blob-blade.png)

Kattintson a **Azure Search hozzáadása** elemre egy olyan folyamat elindításához, amelyen kiválaszthat egy meglévő Azure Search szolgáltatást, vagy létrehozhat egy új szolgáltatást. Ha létrehoz egy új szolgáltatást, a rendszer kikeresi a Storage-fiók portáljának felhasználói felületét. Váltson vissza a Storage Portal lapra, és válassza újra a **Azure Search hozzáadása** lehetőséget, ahol kiválaszthatja a meglévő szolgáltatást.

## <a name="next-steps"></a>Következő lépések
További információ a Azure Search blob indexelő a teljes [dokumentációban](https://aka.ms/azsblobindexer).
