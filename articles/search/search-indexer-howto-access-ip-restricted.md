---
title: Indexelő IP-címtartományok elérésének engedélyezése
titleSuffix: Azure Cognitive Search
description: Útmutató, amely leírja, hogyan állítható be az IP-tűzfalszabályok, hogy az indexelő hozzáférhessenek.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: c80462707d3dccbb8fccff244017053c25ad46e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463582"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>Az IP-tűzfalszabályok beállítása az indexelő hozzáférésének engedélyezéséhez

Az Azure-erőforrások (például a Storage-fiókok, a Cosmos DB-fiókok és az Azure SQL-kiszolgálók) IP-tűzfalszabályok csak adott IP-tartományokból származó forgalmat engedélyezik az adatok eléréséhez.

Ez a cikk azt ismerteti, hogyan konfigurálható az IP-szabályok Azure Portalon keresztül egy Storage-fiókhoz, hogy az Azure Cognitive Search indexelő biztonságosan hozzáférhessenek az adatszolgáltatásokhoz. A tárterületre jellemző módon ez az útmutató közvetlenül lefordítható más Azure-erőforrásokra is, amelyek az adathozzáférés biztonságossá tételéhez is biztosítanak IP-tűzfalszabályok.

> [!NOTE]
> A Storage-fiók IP-tűzfalszabályok csak akkor lépnek érvénybe, ha a Storage-fiók és a keresési szolgáltatás különböző régiókban található. Ha a telepítő nem engedélyezi ezt a beállítást, javasoljuk, hogy használja a [megbízható szolgáltatás kivételét](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>A keresési szolgáltatás IP-címének beolvasása

Szerezze be a keresési szolgáltatás teljes tartománynevét (FQDN). Ez a következőképpen fog kinézni: `<search-service-name>.search.windows.net` . A teljes tartománynevet úgy tekintheti meg, ha megkeresi a keresési szolgáltatást a Azure Portalon.

   ![Szolgáltatás teljes tartománynevének beszerzése](media\search-indexer-howto-secure-access\search-service-portal.png "Szolgáltatás teljes tartománynevének beszerzése")

A keresési szolgáltatás IP-címe `nslookup` a teljes tartománynév (vagy a) használatával szerezhető be `ping` . Ez lesz a tűzfalszabályok számára hozzáadandó IP-címek egyike.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Az IP-címtartományok beolvasása a "AzureCognitiveSearch" szolgáltatás címkéjéhez

A szolgáltatás címkéjének IP-címtartományok a `AzureCognitiveSearch` [Discovery API (előzetes verzió)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) vagy a [letölthető JSON-fájl](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)segítségével szerezhetők be.

Ebben az útmutatóban, feltéve, hogy a Search szolgáltatás az Azure nyilvános felhő, le kell tölteni az [Azure nyilvános JSON-fájlját](https://www.microsoft.com/download/details.aspx?id=56519) .

   ![JSON-fájl letöltése](media\search-indexer-howto-secure-access\service-tag.png "JSON-fájl letöltése")

A JSON-fájlból, feltéve, hogy a Search szolgáltatás az USA nyugati középső régiójában található, az alábbi lista tartalmazza a több-bérlős indexelő végrehajtási környezet IP-címeinek listáját.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

A/32 IP-címek esetében dobja el a "/32" (52.253.133.74/32-> 52.253.133.74), mások pedig szó szerint használhatók.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Az IP-címtartományok hozzáadása az IP-tűzfalszabályok számára

Az IP-címtartományok a Storage-fiók tűzfalszabály-szabályához való hozzáadásának legegyszerűbb módja a Azure Portal. Keresse meg a Storage-fiókot a portálon, és keresse meg a "**tűzfalak és virtuális hálózatok**" lapot.

   ![Tűzfal és virtuális hálózatok](media\search-indexer-howto-secure-access\storage-firewall.png "Tűzfal és virtuális hálózatok")

Adja hozzá a korábban beszerzett három IP-címet (1 a keresési szolgáltatás IP-címéhez, 2 a `AzureCognitiveSearch` szolgáltatási címkéhez) a címtartomány területen, majd kattintson a**Mentés**gombra.

   ![Tűzfal IP-szabályai](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Tűzfal IP-szabályai")

A tűzfalszabályok 5-10 percet vesznek igénybe, amely után frissülnek az indexelő a Storage-fiókban tárolt információkkal.

## <a name="next-steps"></a>Következő lépések

Most, hogy tudja, hogyan kérheti le a két IP-címet az indexekhez való hozzáférés engedélyezéséhez, az alábbi hivatkozásokkal frissítheti az IP-tűzfalszabályok egyes gyakori adatforrások esetében.

- [Azure Storage-tűzfalak konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- [IP-tűzfal konfigurálása a CosmosDB-hez](https://docs.microsoft.com/azure/cosmos-db/firewall-support)
- [Az IP-tűzfal konfigurálása az Azure SQL Serverhez](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)