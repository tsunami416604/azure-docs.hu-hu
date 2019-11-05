---
title: A dúsított tartalom-alapú változások követésének növekményes indexelésének beállítása
titleSuffix: Azure Cognitive Search
description: A változások nyomon követésének engedélyezése és a dúsított tartalom állapotának megőrzése egy kognitív készségkészlet.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ac082d6ecb6624dc0d5bc0ab927ff8b91ebdabce
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512183"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>A dúsított dokumentumok növekményes indexelésének beállítása az Azure-ban Cognitive Search

Ebből a cikkből megtudhatja, hogyan adhat állapotot és gyorsítótárazást az Azure Cognitive Search-bővítési folyamaton áthaladó, dúsított dokumentumokhoz, így fokozatosan indexelheti a dokumentumokat a támogatott adatforrásokból. Alapértelmezés szerint a készségkészlet állapota állapot nélküli, és az összeállítás bármely részének megváltoztatásához az indexelő teljes újrafuttatására van szükség. A növekményes indexeléssel az indexelő meghatározhatja, hogy a folyamat mely részeit módosították, a meglévő bővítéseket újra felhasználhatja a változatlan részekhez, és áttekintheti a módosításokat a módosítások végrehajtásához. A gyorsítótárazott tartalom az Azure Storage-ba kerül.

Ha még nem ismeri az indexelő beállítását, kezdje az [Indexelő áttekintésével](search-indexer-overview.md) , majd folytassa a [szakértelmével](cognitive-search-working-with-skillsets.md) , hogy megismerje a dúsítási folyamatokat. A főbb fogalmakkal kapcsolatos további háttérért lásd: [növekményes indexelés](cognitive-search-incremental-indexing-conceptual.md).

A növekményes indexelés a [keresési REST API-Version = 2019-05 -06-Preview](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)használatával van konfigurálva.

> [!NOTE]
> Ez a funkció még nem érhető el a portálon, és programozott módon kell használni.
>

## <a name="modify-an-existing-indexer"></a>Meglévő indexelő módosítása

Ha van meglévő indexelő, a növekményes indexelés engedélyezéséhez kövesse az alábbi lépéseket.

### <a name="step-1-get-the-indexer"></a>1\. lépés: az indexelő beszerzése

Kezdje egy érvényes, meglévő indexelő, amely rendelkezik a szükséges adatforrással és indextel már definiálva. Az indexelő futtatható kell lennie. Egy API-ügyfél használatával szerezzen be egy Get kérelmet az indexelő aktuális konfigurációjának [lekéréséhez](https://docs.microsoft.com/rest/api/searchservice/get-indexer) , amelyhez növekményes indexelést kíván hozzáadni.

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>2\. lépés: a cache tulajdonság hozzáadása

Szerkessze a GET kérelem válaszát, hogy hozzáadja a `cache` tulajdonságot az indexelő számára. A gyorsítótár objektum csak egyetlen tulajdonságot igényel, amely egy Azure Storage-fiók kapcsolati karakterlánca.

```json
    "cache": {
        "storageConnectionString": "[your storage connection string]"
    }
```

### <a name="step-3-reset-the-indexer"></a>3\. lépés: az indexelő alaphelyzetbe állítása

> [!NOTE]
> Az indexelő alaphelyzetbe állítása azt eredményezi, hogy az adatforrás minden dokumentuma újra fel lesz dolgozva, hogy a tartalom gyorsítótárazható legyen. Minden kognitív gazdagodás minden dokumentumon újra futni fog.
>

A meglévő indexelő növekményes indexelésének beállításakor az indexelő alaphelyzetbe állítása szükséges, hogy minden dokumentum konzisztens állapotban legyen. Az indexelő alaphelyzetbe állítása a [REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)használatával.

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>4\. lépés: a frissített definíció mentése

Frissítse az indexelő definícióját egy PUT-kérelemmel, a kérelem törzsének tartalmaznia kell a frissített indexelő definíciót.

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

Ha most egy másik GET-kérést ad ki az indexelő számára, a szolgáltatás válasza tartalmazni fog egy `cacheId` tulajdonságot a gyorsítótár objektumban. A `cacheId` a tároló neve, amely az indexelő által feldolgozott összes dokumentum gyorsítótárazott eredményeit és köztes állapotát fogja tartalmazni.

## <a name="enable-incremental-indexing-on-new-indexers"></a>Növekményes indexelés engedélyezése az új indexelő eszközökön

Egy új indexelő növekményes indexelésének beállításához adja meg a `cache` tulajdonságot az indexelő definíciós adattartalomban. Győződjön meg arról, hogy az API `2019-05-06-Preview` verzióját használja.

## <a name="overriding-incremental-indexing"></a>Növekményes indexelés felülbírálása

Ha be van állítva, a növekményes indexelés nyomon követi az indexelési folyamat változásait, és a dokumentumokat az index és a kivetítések végleges konzisztenciájára állítja át. Bizonyos esetekben felül kell bírálnia ezt a viselkedést annak biztosítása érdekében, hogy az indexelő ne végezzen további munkát az indexelési folyamat frissítésének eredményeképpen. Az adatforrás-kapcsolatok karakterláncának frissítése például megköveteli az indexelő alaphelyzetbe állítását és az összes dokumentum újraindexelését, mivel az adatforrás megváltozott. Ha azonban csak egy új kulccsal frissítette a kapcsolódási karakterláncot, nem szeretné, hogy a módosítás a meglévő dokumentumok frissítését okozza. Ezzel szemben előfordulhat, hogy az indexelő érvényteleníti a gyorsítótárat, és gazdagítja a dokumentumokat, még akkor is, ha nem történt változás az indexelési folyamat során. Előfordulhat például, hogy érvényteleníteni szeretné az indexelő, ha egy új modellel szeretné újból üzembe helyezni egy egyéni képességet, és az összes dokumentumon újra akartam futtatni a szakértelmet.

### <a name="override-reset-requirement"></a>Felülbírálás-visszaállítási követelmény

Az indexelési folyamat módosításakor a gyorsítótár érvénytelenítését eredményező módosítások szükségesek az indexelő alaphelyzetbe állításához. Ha módosítja az indexelő folyamatot, és nem szeretné, hogy a változások követése érvénytelenítse a gyorsítótárat, a `ignoreResetRequirement` querystring paramétert kell megadnia, hogy az indexelő vagy az adatforrás műveleteihez `true`.

### <a name="override-change-detection"></a>Felülbírálási változások észlelése

Ha olyan frissítéseket végez a készségkészlet, amelyek inkonzisztensként megjelölik a dokumentumokat, például egy egyéni ügyességi URL-cím frissítését a szaktudás újratelepítésekor, állítsa be a `disableCacheReprocessingChangeDetection` lekérdezési karakterlánc paramétert a készségkészlet-frissítések `true`.

### <a name="force-change-detection"></a>Kényszerített változások észlelése

A példány akkor jelenik meg, ha azt szeretné, hogy az indexelési folyamat felismerje a külső entitások módosítását, például az egyéni képességek új verziójának telepítését, frissítenie kell a készségkészlet, és az "érintés" tulajdonságot a szaktudás definíciójának szerkesztésével, pontosabban a kényszerített URL-címmel kell megadnia. módosítsa az észlelést, és érvénytelenítse az adott szakértelem gyorsítótárát.

## <a name="next-steps"></a>További lépések

Ez a cikk a szakértelmével tartalmazó indexelő növekményes indexelését ismerteti. Az ismeretek további fellépéséhez tekintse át az általános, az Azure Cognitive Search összes indexelési forgatókönyvére vonatkozó cikket.

+ [Azure Cognitive Search index újraépítése](search-howto-reindex.md). 
+ [Nagyméretű adathalmazok indexelése az Azure Cognitive Searchban](search-howto-large-index.md). 
