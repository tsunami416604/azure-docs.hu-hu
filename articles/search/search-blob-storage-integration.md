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
ms.openlocfilehash: 7e5eb73cc6abc72689bbc674b29f4d288dd66b6f
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302895"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Teljes szöveges keresés hozzáadása az Azure-Blobok adataihoz Azure Search használatával

Az Azure Blob Storage-ban tárolt különböző tartalomtípusok közötti keresés nehéz megoldás lehet. Az Azure Search használatával azonban néhány kattintással indexelheti és keresheti meg a Blobok tartalmát. A blob Storage-on való kereséshez Azure Search szolgáltatást kell kiépíteni. A [díjszabási oldalon](https://aka.ms/azspricing)a Azure Search különböző szolgáltatási korlátai és díjszabása található.

## <a name="what-is-azure-search"></a>Mi az Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) egy keresési szolgáltatás, amely megkönnyíti a fejlesztők számára a webes és mobil alkalmazások robusztus teljes szöveges keresési funkcióinak hozzáadását. Szolgáltatásként a Azure Search eltávolítja a keresési infrastruktúra felügyeletének szükségességét, és [99,9%-os rendelkezésre állási SLA](https://aka.ms/azuresearchsla)-t kínál.

## <a name="index-and-search-enterprise-document-formats"></a>Index és keresés a vállalati dokumentumok formátuma
Az Azure Blob Storage-ban a [dokumentumok kinyerésének](https://aka.ms/azsblobindexer) támogatásával a következő tartalmakat indexelheti:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

A szöveg és a metaadatok ezen fájltípusokból való kibontásával egyetlen lekérdezéssel több fájlformátumban is kereshet. 

## <a name="search-through-your-blob-metadata"></a>Keresés a blob metaadatainak között
Egy gyakori forgatókönyv, amely megkönnyíti a tetszőleges tartalomtípusú Blobok rendezését, az egyéni metaadatok és a Rendszertulajdonságok indexelése az egyes blobokhoz. Így az összes blobra vonatkozó információ indexelve van, tekintet nélkül a dokumentum típusára. Ezután folytathatja az összes blob Storage-tartalom rendezését, szűrését és aspektusát.

[További információ a blob-metaadatok indexeléséről.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Képkeresés
Az Azure Search teljes szöveges keresési, sokoldalú navigációs és rendezési képességei mostantól alkalmazhatók a blobokban tárolt rendszerképek metaadataira.

A kognitív keresés olyan képfeldolgozási képességeket tartalmaz, mint például az [optikai karakterfelismerés (OCR)](cognitive-search-skill-ocr.md) és a [vizualizációs funkciók](cognitive-search-skill-image-analysis.md) azonosítása, amelyek segítségével indexelheti az egyes képekben található vizuális tartalmat.

## <a name="index-and-search-through-json-blobs"></a>Indexelés és keresés JSON-blobokon
Azure Search konfigurálható a JSON-t tartalmazó blobokban található strukturált tartalom kinyeréséhez. A Azure Search beolvashatja a JSON-blobokat, és elemezheti a strukturált tartalmat egy Azure Search dokumentum megfelelő mezőibe. A Azure Search olyan blobokat is tartalmazhat, amelyek JSON-objektumok tömbjét tartalmazzák, és az egyes elemeket egy külön Azure Search dokumentumra képezik le.

A JSON-elemzés jelenleg nem konfigurálható a portálon keresztül. [További információ a JSON-elemzésről Azure Searchban.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Gyors útmutató
Azure Search közvetlenül a blob Storage portál lapról adható hozzá a blobokhoz.

![](./media/search-blob-storage-integration/blob-blade.png)

Kattintson a **Azure Search hozzáadása** elemre egy olyan folyamat elindításához, amelyen kiválaszthat egy meglévő Azure Search szolgáltatást, vagy létrehozhat egy új szolgáltatást. Ha létrehoz egy új szolgáltatást, a rendszer kikeresi a Storage-fiók portáljának felhasználói felületét. Váltson vissza a Storage Portal lapra, és válassza újra a **Azure Search hozzáadása** lehetőséget, ahol kiválaszthatja a meglévő szolgáltatást.

## <a name="next-steps"></a>Következő lépések
További információ a Azure Search blob indexelő a teljes [dokumentációban](https://aka.ms/azsblobindexer).
