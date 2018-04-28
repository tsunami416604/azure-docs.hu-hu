---
title: Az Azure Search ad hozzá a Blob Storage |} Microsoft Docs
description: Index létrehozása kódból az Azure Search HTTP REST API használatával.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/04/2017
author: chaosrealm
manager: jlembicz
ms.author: eugenesh
ms.openlocfilehash: 71e43920f0e6a64beb7cdb28d0707dd30502bf05
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="searching-blob-storage-with-azure-search"></a>Keresés a Blob Storage-tárolókban az Azure Search szolgáltatással

A különböző Azure Blob storage-ban tárolt tartalomtípusok teljes megoldására nehéz problémát okozhat. Azonban index és a BLOB tartalmát keresni néhány kattintással Azure Search használatával. Egy Azure Search szolgáltatás kiépítését keres a Blob-tároló igényli. A különböző szolgáltatások korlátait és az Azure Search árképzési szinteket megtalálható a [árképzést ismertető oldalra](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Mi az az Azure Search?
[Az Azure Search](https://aka.ms/whatisazsearch) keresés szolgáltatás, amely megkönnyíti, hogy a fejlesztők számára a hatékony teljes szöveges keresés észlel hozzáadása webes és mobilalkalmazásokhoz. Szolgáltatásként, Azure Search szükségtelenné ajánlat során bármilyen keresési infrastruktúra kezelése érdekében egy [99,9 %-os hasznos üzemidő SLA](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Index és a keresési vállalati dokumentum formátuma
Támogatja a [dokumentálja a kibontási](https://aka.ms/azsblobindexer) az Azure Blob storage indexelheti a következőket:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Az ilyen típusú szöveg és a metaadatok beolvasásával egy-egy lekérdezéshez több fájlformátumok végezhet keresést. 

## <a name="search-through-your-blob-metadata"></a>A blob-metaadatok közötti keresésre
Egy gyakori forgatókönyv, amely megkönnyíti a blobok bármely tartalomtípus rendszerezése egyéni metaadatok és a rendszer minden egyes blob tulajdonságainak indexelésre. Ezzel a módszerrel összes BLOB információi indexelt dokumentumtípus függetlenül. Majd lépne rendezési, szűrési és dimenzió összes Blob storage tartalma között.

[Ismerje meg, további információk az indexelő blob-metaadatok.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Kép keresése
Az Azure Search teljes szöveges keresés, jellemzőalapú navigáció és rendezési lehetőségeket is érvényesek a blobok tárolt képek metaadatait.

Ha ezeket a lemezképeket előre feldolgozása használatával a [számítógép Látástechnológiai API](https://www.microsoft.com/cognitive-services/computer-vision-api) a Microsoft kognitív szolgáltatást, majd lehetőség a vizuális tartalom található egyes lemezképek és a kézírás-felismerési beleértve az index. Jelenleg is dolgozunk OCR hozzáadása és egyéb kép feldolgozási funkciói közvetlenül az Azure Search, ha ezek a képességek érdekli kérelmet küldeni a a [UserVoice](https://aka.ms/azsuv) vagy [nekünk e-mailben](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Index és a keresési JSON-blobok keresztül
Az Azure Search beállítható úgy, hogy a kibontani strukturált blobok JSON tartalmazó található. Az Azure Search JSON-blobok olvashatja, és a strukturált tartalom elemzése az Azure Search dokumentum a megfelelő mezőkbe. Az Azure Search is számos blobot, amely tartalmazza JSON-objektumok tömbje és hozzárendelését az egyes elemei egy külön Azure Search-dokumentumot.

JSON elemzése nincs jelenleg konfigurálható a portálon keresztül. [További tudnivalók az Azure Search elemzése JSON.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Első lépések
Az Azure Search közvetlenül a Blob storage portállapon a blobok is hozzáadhatók.

![](./media/search-blob-storage-integration/blob-blade.png)

Kattintson a **hozzáadása Azure Search** ahol válasszon ki egy meglévő Azure Search szolgáltatást, vagy hozzon létre egy új szolgáltatást, a folyamat elindításához. Ha létrehoz egy új szolgáltatás, akkor kívül a tárfiók portál élmény léptetését. Lépjen vissza a tárolási portálon, és jelölje ki újból a **hozzáadása Azure Search** beállítás, ahol kiválaszthatja a meglévő szolgáltatást.

### <a name="next-steps"></a>További lépések
További tudnivalók az Azure Search Blob indexelő a teljes [dokumentáció](https://aka.ms/azsblobindexer).
