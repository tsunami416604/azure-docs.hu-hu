---
title: Gyakori keresési indexelő problémák elhárítása
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search indexelő hibáival kapcsolatos hibák és gyakori problémák megoldása, beleértve az adatforrás-kapcsolatokat, a tűzfalat és a hiányzó dokumentumokat.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c5a16d957f1e0414f92d0cc03442d88d438e4c92
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793629"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Az Azure Cognitive Search gyakori indexelő problémáinak elhárítása

Az indexelő több problémát is futtathat az Azure Cognitive Searchba való adatindexelés során. A hiba fő kategóriái a következők:

* [Kapcsolódás adatforráshoz](#data-source-connection-errors)
* [Dokumentumok feldolgozása](#document-processing-errors)
* [Dokumentumok betöltése egy indexbe](#index-errors)

## <a name="data-source-connection-errors"></a>Adatforrás-csatlakoztatási hibák

### <a name="blob-storage"></a>Blob Storage

#### <a name="storage-account-firewall"></a>Storage-fiók tűzfala

Az Azure Storage konfigurálható tűzfalat biztosít. Alapértelmezés szerint a tűzfal le van tiltva, így az Azure Cognitive Search tud csatlakozni a Storage-fiókhoz.

Nincs konkrét hibaüzenet, ha a tűzfal engedélyezve van. A tűzfal hibái általában úgy néznek ki, mint a `The remote server returned an error: (403) Forbidden`.

A [portálon](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)ellenőrizheti, hogy a tűzfal engedélyezve van-e. Az egyetlen támogatott megkerülő megoldás, ha letiltja a tűzfalat úgy, hogy engedélyezi a hozzáférést az [összes hálózatról](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Ha az indexelő nem rendelkezik csatolt készségkészlet, akkor _Előfordulhat_ , hogy a keresési szolgáltatás IP-címeihez [kivételt próbál hozzáadni](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) . Ez a forgatókönyv azonban nem támogatott, és nem garantáltan működik.

A keresési szolgáltatás IP-címét a teljes tartománynév (`<your-search-service-name>.search.windows.net`) pingelésével tekintheti meg.

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>Az indexelés nincs engedélyezve

Az Azure Cognitive Search implicit függőséggel rendelkezik Cosmos DB indexeléshez. Ha kikapcsolja az automatikus indexelést Cosmos DBban, az Azure Cognitive Search sikeres állapotot ad vissza, de nem tudja indexelni a tároló tartalmát. A beállítások vizsgálatával és az indexelés bekapcsolásával kapcsolatos utasításokért lásd: [az indexelés kezelése Azure Cosmos DBban](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Dokumentált feldolgozási hibák

### <a name="unprocessable-or-unsupported-documents"></a>Feldolgozható vagy nem támogatott dokumentumok

A blob indexelő [dokumentumai, melyeket a dokumentumok formátumai kifejezetten támogatnak.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) Előfordulhat, hogy a blob Storage-tároló nem támogatott dokumentumokat tartalmaz. Egyéb esetekben problémás dokumentumok is előfordulhatnak. A [konfigurációs beállítások módosításával](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)elkerülheti az indexelő leállítását ezen dokumentumokon:

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Hiányzó dokumentum tartalma

A blob indexelő [megkeresi és kiolvassa a tárolóban lévő Blobok szövegét](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). A szöveg kinyerésével kapcsolatos problémák például a következők:

* A dokumentum csak a beolvasott képeket tartalmazza. A nem szöveges tartalmú PDF-Blobok, például a beolvasott képek (jpg-EK) nem eredményeznek eredményt a szabványos blob-indexelési folyamatokban. Ha szöveges elemekkel rendelkező képtartalommal rendelkezik, a [kognitív keresés](cognitive-search-concept-image-scenarios.md) használatával megkeresheti és kinyerheti a szöveget.
* A blob-indexelő úgy van beállítva, hogy csak a metaadatok indexelésére legyen konfigurálva. A tartalom kinyeréséhez a blob indexelő úgy kell konfigurálni, hogy [mindkét tartalmat és metaadatot kinyerje](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Indexelési hibák

### <a name="missing-documents"></a>Hiányzó dokumentumok

Az indexelő a dokumentumokat egy [adatforrásból](https://docs.microsoft.com/rest/api/searchservice/create-data-source)keresi meg. Időnként előfordulhat, hogy az adatforrásból olyan dokumentum van, amelyet indexelni kellett volna egy indexből. A hibák néhány gyakori oka lehet:

* A dokumentum nem lett indexelve. A sikeres indexelő futtatásához keresse meg a portált.
* A dokumentum frissült az indexelő futtatása után. Ha az indexelő [ütemezett](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), akkor a rendszer végül Újrafuttatja és felveszi a dokumentumot.
* Az adatforrásban megadott [lekérdezés](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) kizárja a dokumentumot. Az indexelő nem tudja indexelni azokat a dokumentumokat, amelyek nem részei az adatforrásnak.
* A [mező-hozzárendelések](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) vagy a [mesterséges intelligencia-gazdagítás](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) megváltoztatta a dokumentumot, és a várttól eltérőnek tűnik.
* A dokumentum kereséséhez használja a [keresési dokumentum API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) -t.
