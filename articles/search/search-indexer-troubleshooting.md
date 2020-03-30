---
title: Gyakori keresési indexelő problémák elhárítása
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search indexelőivel kapcsolatos hibák és gyakori problémák kijavítása, beleértve az adatforrás-kapcsolatot, a tűzfalat és a hiányzó dokumentumokat.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190933"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Gyakori indexelő problémák elhárítása az Azure Cognitive Search szolgáltatásban

Az indexelők számos problémát okozhatnak az Azure Cognitive Search adatok indexelésekén. A hiba fő kategóriái a következők:

* [Csatlakozás adatforráshoz vagy más erőforráshoz](#connection-errors)
* [Dokumentumfeldolgozás](#document-processing-errors)
* [Dokumentum betöltése indexbe](#index-errors)

## <a name="connection-errors"></a>Csatlakozási hibák

> [!NOTE]
> Az indexelők korlátozott anamforokat támogatnak az Azure hálózati biztonsági mechanizmusai által védett adatforrások és egyéb erőforrások eléréséhez. Jelenleg az indexelők csak a megfelelő IP-címtartomány-korlátozási mechanizmusokon vagy NSG-szabályokon keresztül férhetnek hozzá az adatforrásokhoz, ha alkalmazható. Az egyes támogatott adatforrások elérésének részletei az alábbiakban találhatók.
>
> Megtudhatja, hogy az IP-címét a keresési szolgáltatás pingelésével `<your-search-service-name>.search.windows.net`a teljesen minősített domain név (pl., ).
>
> A `AzureCognitiveSearch` [szolgáltatáscímke](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) IP-címtartományát a [Letölthető JSON-fájlok](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) vagy a [Service Tag Discovery API](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)segítségével találhatja meg. Az IP-címtartomány hetente frissül.

### <a name="configure-firewall-rules"></a>Tűzfalszabályok konfigurálása

Az Azure Storage, a CosmosDB és az Azure SQL konfigurálható tűzfalat biztosít. Nincs konkrét hibaüzenet, ha a tűzfal engedélyezve van. A tűzfalhibák általában általánosak, és így néznek `The remote server returned an error: (403) Forbidden` ki: vagy `Credentials provided in the connection string are invalid or have expired`a .

Az indexelők számára két lehetőség van arra, hogy ilyen példányban hozzáférjenek ezekhez az erőforrásokhoz:

* Tiltsa le a tűzfalat, lehetővé téve a hozzáférést **az összes hálózatról** (ha lehetséges).
* Másik lehetőségként engedélyezheti a hozzáférést a keresési szolgáltatás IP-címéhez `AzureCognitiveSearch` és az erőforrás tűzfalszabályaiban található [IP-címcímhez](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) (IP-címtartomány korlátozása).

Az egyes adatforrástípusok IP-címtartomány-korlátozásainak konfigurálásáról az alábbi hivatkozásokon talál részleteket:

* [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Korlátozás:** Amint azt a fenti dokumentációban az Azure Storage, IP-címtartomány korlátozások csak akkor működnek, ha a keresési szolgáltatás és a tárfiók különböző régiókban található.

Az Azure-függvények (amelyek [egyéni webapi-készségként](cognitive-search-custom-skill-web-api.md)használhatók) szintén [támogatják az IP-címkorlátozásokat.](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions) A konfigurálandó IP-címek listája a keresési szolgáltatás IP-címe és `AzureCognitiveSearch` a szolgáltatáscímke IP-címtartománya.

Az ADATOK Azure-beli virtuális gépSQL-kiszolgálón való elérésének részleteit [itt](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) ismerteti

### <a name="configure-network-security-group-nsg-rules"></a>Hálózati biztonsági csoport (NSG) szabályainak konfigurálása

Amikor egy SQL felügyelt példányban fér hozzá az adatokhoz, vagy ha egy Azure virtuális gép egy [egyéni webapi-szakértelem](cognitive-search-custom-skill-web-api.md)webszolgáltatás-URI-jaként használatos, az ügyfeleknek nem kell adott IP-címekkel foglalkozniuk.

Ilyen esetekben az Azure virtuális gép, vagy az SQL felügyelt példány konfigurálható, hogy egy virtuális hálózaton belül található. Ezután egy hálózati biztonsági csoport beállítható a virtuális hálózati alhálózatokba és hálózati adapterekbe be- és kiáramló hálózati forgalom típusának szűrésére.

A `AzureCognitiveSearch` szolgáltatáscímke közvetlenül használható a bejövő [NSG-szabályok](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) ban anélkül, hogy meg kellene keresnie az IP-címtartományát.

Az SQL felügyelt példányok adatainak elérésével kapcsolatos további részleteket [itt](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md) ismertetjük.

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "Indexelés" nincs engedélyezve

Az Azure Cognitive Search implicit függa Cosmos DB indexelés. Ha kikapcsolja az automatikus indexelést a Cosmos DB-ben, az Azure Cognitive Search sikeres állapotot ad vissza, de nem indexeli a tároló tartalmát. A beállítások ellenőrzéséről és az indexelés bekapcsolásával kapcsolatos tudnivalókért olvassa el [az Indexelés kezelése az Azure Cosmos DB-ben](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Dokumentumfeldolgozási hibák

### <a name="unprocessable-or-unsupported-documents"></a>Feldolgozhatatlan vagy nem támogatott dokumentumok

A blob indexelő [dokumentumok, amelyek dokumentumformátumok kifejezetten támogatott.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). . Néha egy blob storage tároló nem támogatott dokumentumokat tartalmaz. Máskor lehetnek problémás dokumentumok. A [konfigurációs beállítások módosításával](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)elkerülheti az indexelő leállítását ezeken a dokumentumokon:

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Hiányzó dokumentumtartalom

A blob indexelő [megkeresi és kibontja a szöveget a tárolóban lévő blobokból.](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs) A szöveg kibontásával kapcsolatos problémák a következők:

* A dokumentum csak beolvasott képeket tartalmaz. A nem szöveges tartalommal rendelkező PDF-blobok, például a beolvasott képek (JG-k) nem eredményeznek eredményt egy szabványos blobindexelő folyamatban. Ha szöveges elemeket tartalmazó képtartalma van, [a kognitív kereséssel](cognitive-search-concept-image-scenarios.md) megkeresheti és kibonthatja a szöveget.
* A blob indexelő van beállítva, hogy csak index elmetaadatok. A tartalom kinyeréséhez a blob indexelőt úgy kell konfigurálni, hogy [kinyerje a tartalmat és a metaadatokat:](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Indexhibák

### <a name="missing-documents"></a>Hiányzó dokumentumok

Az indexelők [adatforrásból](https://docs.microsoft.com/rest/api/searchservice/create-data-source)származó dokumentumokat keresnek. Néha úgy tűnik, hogy az adatforrásból származó, indexelendő dokumentum hiányzik egy indexből. A hibák több gyakori oka is előfordulhat:

* A dokumentum nincs indexelve. Ellenőrizze a portálon a sikeres indexelő futtatás.
* A dokumentum az indexelő futtatása után frissült. Ha az indexelő [ütemezés szerint](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule)van, akkor végül újrafut, és felveszi a dokumentumot.
* Az adatforrásban megadott [lekérdezés](/rest/api/searchservice/create-data-source) kizárja a dokumentumot. Az indexelők nem indexelhetik azadatforrásrészét nem lévő dokumentumokat.
* [A mezőleképezések](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) vagy [a a ai-dúsítás](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) módosította a dokumentumot, és a várttól eltérően néz ki.
* A [lookup document API segítségével](https://docs.microsoft.com/rest/api/searchservice/lookup-document) megkeresheti a dokumentumot.
