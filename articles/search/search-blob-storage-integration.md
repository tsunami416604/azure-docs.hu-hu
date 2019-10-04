---
title: Teljes szöveges keresés hozzáadása az Azure Blob Storage-Azure Search
description: Az Azure Blob Storage-ban lévő szöveges tartalmak feltérképezése Azure Search indexeléshez a kódban a HTTP-REST API használatával.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/01/2019
author: mgottein
manager: nitinme
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: f0801931b57302ae1d627dab783a40d2407c19ac
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650085"
---
# <a name="searching-blob-storage-with-azure-search"></a>Keresés a Blob Storage-tárolókban az Azure Search szolgáltatással

Az Azure Blob Storage-ban tárolt különböző tartalomtípusok közötti keresés nehéz megoldás lehet. Az Azure Search használatával azonban néhány kattintással indexelheti és keresheti meg a Blobok tartalmát. A blob Storage-on való kereséshez Azure Search szolgáltatást kell kiépíteni. A [díjszabási oldalon](https://aka.ms/azspricing)a Azure Search különböző szolgáltatási korlátai és díjszabása található.

## <a name="what-is-azure-search"></a>Mi az az Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) egy keresési szolgáltatás, amely megkönnyíti a fejlesztők számára a webes és mobil alkalmazások robusztus teljes szöveges keresési funkcióinak hozzáadását. Szolgáltatásként a Azure Search eltávolítja a keresési infrastruktúra felügyeletének szükségességét, és [99,9%-os rendelkezésre állási SLA](https://aka.ms/azuresearchsla)-t kínál.

## <a name="index-and-search-enterprise-document-formats"></a>Index és keresés a vállalati dokumentumok formátuma
Az Azure Blob Storage-ban a [dokumentumok](https://aka.ms/azsblobindexer) kinyerésének támogatásával a következő tartalmakat indexelheti:

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

## <a name="quick-start"></a>Gyors üzembe helyezés
Azure Search közvetlenül a blob Storage portál lapról adható hozzá a blobokhoz.

![](./media/search-blob-storage-integration/blob-blade.png)

Kattintson a **Azure Search hozzáadása** elemre egy olyan folyamat elindításához, amelyen kiválaszthat egy meglévő Azure Search szolgáltatást, vagy létrehozhat egy új szolgáltatást. Ha létrehoz egy új szolgáltatást, a rendszer kikeresi a Storage-fiók portáljának felhasználói felületét. Váltson vissza a Storage Portal lapra, és válassza újra a **Azure Search hozzáadása** lehetőséget, ahol kiválaszthatja a meglévő szolgáltatást.

## <a name="next-steps"></a>További lépések
További információ a Azure Search blob indexelő a teljes dokumentációban [](https://aka.ms/azsblobindexer).
