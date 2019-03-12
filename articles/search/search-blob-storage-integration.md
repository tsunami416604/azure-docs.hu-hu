---
title: Az Azure Blob Storage – Azure Search teljes szöveges keresés hozzáadása
description: Az Azure Blob storage-indexelő Azure Search HTTP REST API használatával kódban bejárás szöveges tartalommal.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: b7e7ecd2a82a8d64967288def9c6ede7a292f72a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759391"
---
# <a name="searching-blob-storage-with-azure-search"></a>Keresés a Blob Storage-tárolókban az Azure Search szolgáltatással

Keresés az Azure Blob storage-ban tárolt tartalomtípusok számos különböző, lehet, hogy nehéz probléma megoldására. Azonban index, és a Blobok tartalmának keresés mindössze néhány kattintással az Azure Search használatával. A Blob storage között keres szükséges kiépítése az Azure Search szolgáltatást. A különböző szolgáltatási korlátai és az Azure Search tarifacsomagok találhatók a [díjszabását ismertető lapon](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Mi az az Azure Search?
[Az Azure Search](https://aka.ms/whatisazsearch) egy keresési szolgáltatás, amely megkönnyíti a fejlesztők számára a hatékony teljes szöveges keresési megoldásokat hozzáadása a webes és mobilalkalmazások számára. Szolgáltatásként az Azure Search köszönhetően nem kell közben ajánlat minden olyan keresési infrastruktúra felügyelete egy [99,9 %-os üzemidő SLA](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Index és a keresési vállalati dokumentum formátumok
Támogatja a [kinyerési dokumentum](https://aka.ms/azsblobindexer) az Azure Blob storage, indexelésére használhatja, az alábbi tartalommal:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Oly módon, szöveg és a metaadatokat az ilyen típusú, az egyetlen lekérdezést több fájlformátumok is kereshet. 

## <a name="search-through-your-blob-metadata"></a>A blob metaadatainak kereshet
Egy gyakori forgatókönyv, amely megkönnyíti a tartalom típustól blobok végigvesszük az egyéni metaadatok és a rendszer minden egyes blob tulajdonságainak index. Ily módon az összes információt indexelt dokumentum típusától függetlenül. Ezután folytassa rendezés, szűrés és értékkorlátozás összes Blob storage-tartalmak között.

[Ismerje meg bővebben az indexelés a blobmetaadatokat.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Képkeresés
Az Azure Search teljes szöveges keresés, a jellemzőalapú navigáció és rendezési lehetőségeket is érvényesek a metaadatokat a blobokban tárolt képek.

Kognitív keresés például tartalmazza a rendszerkép feldolgozási képességek [optikai karakterfelismerés (OCR)](cognitive-search-skill-ocr.md) és azonosítása [vizuális jellemzőket](cognitive-search-skill-image-analysis.md) , amelyek lehetővé teszik, hogy az egyes vizuális tartalmáról indexelése kép.

## <a name="index-and-search-through-json-blobs"></a>Index és keresést végezhet a JSON-blobok
Az Azure Search beállítható úgy, hogy a blobokat tartalmazó JSON-ban található strukturálatlan tartalom kinyeréséhez. Az Azure Search olvashatja a JSON-blobok és a strukturálatlan tartalom használatával elemzi az Azure Search-dokumentum, a megfelelő mezőkbe. Az Azure Search is igénybe vehet a blobokat, amelyekre a JSON-objektumok egy tömbjét tartalmazza, és egyes elemei leképezése egy külön Azure Search-dokumentumot is.

JSON-elemzés nem jelenleg konfigurálható a portálon keresztül. [További információ a JSON-elemzés az Azure Search szolgáltatásban.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Első lépések
Az Azure Search BLOB közvetlenül a Blob storage portáloldalán a lehet hozzáadni.

![](./media/search-blob-storage-integration/blob-blade.png)

Kattintson a **Azure Search hozzáadása** , válasszon ki egy meglévő Azure Search szolgáltatást, vagy hozzon létre egy új folyamat elindításához. Ha létrehoz egy új szolgáltatás, akkor ki a tárfiók portálja léptetését. Lépjen vissza a tároló portáloldalán, és jelölje ki újra a **Azure Search hozzáadása** lehetőség, ahol kiválaszthatja a meglévő szolgáltatás.

## <a name="next-steps"></a>További lépések
További információ az Azure Search Blob Indexelőjével a teljes [dokumentáció](https://aka.ms/azsblobindexer).
