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
ms.openlocfilehash: 5f646b4cef782b569910bdf881208c9984194589
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931119"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Az Azure Cognitive Search gyakori indexelő problémáinak elhárítása

Az indexelő több problémát is futtathat az Azure Cognitive Searchba való adatindexelés során. A hiba fő kategóriái a következők:

* [Csatlakozás adatforráshoz vagy más erőforráshoz](#connection-errors)
* [Dokumentumok feldolgozása](#document-processing-errors)
* [Dokumentumok betöltése egy indexbe](#index-errors)

## <a name="connection-errors"></a>Csatlakozási hibák

> [!NOTE]
> Az indexelő korlátozott támogatást biztosít az Azure hálózati biztonsági mechanizmusok által védett adatforrásokhoz és egyéb erőforrásokhoz való hozzáféréshez. Jelenleg az indexelő csak a megfelelő IP-címtartomány korlátozási mechanizmusai vagy NSG-szabályok segítségével férhetnek hozzá az adatforrásokhoz, ha van ilyen. Az egyes támogatott adatforrásokhoz való hozzáférés részleteit alább találja.
>
> A keresési szolgáltatás IP-címét a teljes tartománynév (például `<your-search-service-name>.search.windows.net`) pingelésével tekintheti meg.
>
> A `AzureCognitiveSearch` [Service tag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) IP-címtartomány a [letölthető JSON-fájlokkal](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) vagy a [Service tag Discovery API](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview)-val is megtekinthető. Az IP-címtartomány hetente frissül.

### <a name="configure-firewall-rules"></a>Tűzfalszabályok konfigurálása

Az Azure Storage, a CosmosDB és az Azure SQL konfigurálható tűzfalat biztosít. Nincs konkrét hibaüzenet, ha a tűzfal engedélyezve van. Általában a tűzfal hibái általánosak, és úgy néznek ki, mint a `The remote server returned an error: (403) Forbidden` vagy `Credentials provided in the connection string are invalid or have expired`.

A következő két lehetőség közül választhat, amelyek lehetővé teszik az indexelő számára ezen erőforrások elérését egy adott példányban:

* Tiltsa le a tűzfalat azáltal, hogy engedélyezi a hozzáférést az **összes hálózatról** (ha lehetséges).
* Azt is megteheti, hogy engedélyezi a keresési szolgáltatás IP-címét, valamint az erőforrás tűzfalszabályok (az IP-címtartomány korlátozása) `AzureCognitiveSearch` [szolgáltatás címkéjének](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) IP-címtartomány használatát.

Az IP-címtartomány korlátozásának az egyes adatforrások esetében történő konfigurálásával kapcsolatos részletek a következő hivatkozásokban találhatók:

* [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Korlátozás**: az Azure Storage fenti dokumentációjában leírtaknak megfelelően az IP-címtartomány korlátozásai csak akkor működnek, ha a keresési szolgáltatás és a Storage-fiók különböző régiókban található.

Az Azure functions (amely [egyéni webes API-képességként](cognitive-search-custom-skill-web-api.md)használható) az [IP-címek korlátozásait](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions)is támogatja. A konfigurálni kívánt IP-címek listája a keresési szolgáltatás IP-címe és az `AzureCognitiveSearch` szolgáltatási címke IP-címtartomány.

Az Azure-beli virtuális gépen futó SQL Server-adatokhoz való hozzáférés részleteit [itt](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) találja:

### <a name="configure-network-security-group-nsg-rules"></a>Hálózati biztonsági csoport (NSG) szabályainak konfigurálása

Ha egy SQL felügyelt példányban lévő adatokhoz fér hozzá, vagy ha egy Azure-beli virtuális gépet webszolgáltatási URI-ként használ egy [egyéni webes API-képességhez](cognitive-search-custom-skill-web-api.md), az ügyfeleknek nem kell konkrét IP-címmel foglalkoznia.

Ilyen esetekben az Azure-beli virtuális gép vagy az SQL felügyelt példánya konfigurálható úgy, hogy a virtuális hálózaton belül legyen. Ezután egy hálózati biztonsági csoport konfigurálható úgy, hogy szűrje a virtuális hálózati alhálózatok és hálózati adapterek bejövő és kimenő hálózati forgalmának típusát.

A `AzureCognitiveSearch` szolgáltatás címkéje közvetlenül is használható a bejövő [NSG-szabályokban](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) anélkül, hogy meg kellene keresnie az IP-címtartományt.

A felügyelt SQL-példányokban tárolt adatokhoz való hozzáférés további részleteit [itt](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md) találja

### <a name="cosmosdb-indexing-isnt-enabled"></a>Az "indexelés" CosmosDB nincs engedélyezve

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
