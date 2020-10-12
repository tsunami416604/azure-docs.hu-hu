---
title: Indexelő erőforrások biztonságos elérése
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search indexelő szolgáltatásával elérhető hálózati szintű biztonsági beállítások fogalmi áttekintése.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 5075c4858f9584cb19442e19d9009d46d0e00ff8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463551"
---
# <a name="indexer-access-to-data-sources-using-azure-network-security-features"></a>Indexelő hozzáférése az adatforrásokhoz az Azure hálózati biztonsági funkcióival

Az Azure Cognitive Search-indexelő a végrehajtás során különböző Azure-erőforrásokhoz is elvégezhetik a kimenő hívásokat. Ez a cikk az indexelő által az erőforrásokhoz való hozzáféréshez szükséges fogalmakat ismerteti, ha ezeket az erőforrásokat IP-tűzfalak, privát végpontok és egyéb hálózati szintű biztonsági mechanizmusok védik. Az indexelő által egy tipikus futtatáshoz hozzáférő lehetséges erőforrástípusok az alábbi táblázatban láthatók.

| Erőforrás | Az indexelő futtatásának célja |
| --- | --- |
| Azure Storage (Blobok, táblák, ADLS Gen 2) | Adatforrás |
| Azure Storage (Blobok, táblák) | Szakértelmével (a bővített dokumentumok gyorsítótárazása és a Knowledge Store-előrejelzések tárolása) |
| Azure Cosmos DB (különböző API-k) | Adatforrás |
| Azure SQL Database | Adatforrás |
| Azure IaaS virtuális gépeken futó SQL Server | Adatforrás |
| Felügyelt SQL-példányok | Adatforrás |
| Azure Functions | Az egyéni webes API-ismeretek üzemeltetése |
| Cognitive Services | Csatolva a készségkészlet-hez, amely a 20 szabad dokumentumra vonatkozó korláton felüli kiszámlázást fogja használni |

> [!NOTE]
> A készségkészlet csatolt kognitív szolgáltatási erőforrás a számlázásra szolgál, a keresési indexbe írt alkoholtartalom-növelés alapján. Nem használatos a Cognitive Services API-khoz való hozzáféréshez. Az indexelő bővítési folyamatáról Cognitive Services API-kre való hozzáférés biztonságos kommunikációs csatornán keresztül történik, ahol az adatok erősen titkosítva vannak az átvitel során, és soha nem tárolják a nyugalmi állapotban.

Az ügyfelek az Azure által kínált számos hálózati elkülönítési mechanizmuson keresztül biztonságossá tehetik ezeket az erőforrásokat. A kognitív szolgáltatás erőforrásának kivételével az indexelő korlátozott mértékben férnek hozzá az összes többi erőforráshoz, még akkor is, ha az alábbi táblázatban láthatók az elkülönített hálózatok.

| Erőforrás | IP-korlátozás | Privát végpont |
| --- | --- | ---- |
| Azure Storage (Blobok, táblák, ADLS Gen 2) | Csak akkor támogatott, ha a Storage-fiók és a keresési szolgáltatás különböző régiókban található | Támogatott |
| Azure Cosmos DB-SQL API | Támogatott | Támogatott |
| Azure Cosmos DB-Cassandra, Mongo és Gremlin API | Támogatott | Nem támogatott |
| Azure SQL Database | Támogatott | Támogatott |
| Azure IaaS virtuális gépeken futó SQL Server | Támogatott | N.A. |
| Felügyelt SQL-példányok | Támogatott | N.A. |
| Azure Functions | Támogatott | Támogatott, csak az Azure-függvények bizonyos SKU-i esetében |

> [!NOTE]
> A fent felsorolt lehetőségeken kívül a hálózattal védett Azure Storage-fiókok esetében az ügyfelek kihasználhatják azt a tényt, hogy az Azure Cognitive Search [megbízható Microsoft-szolgáltatás](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services). Ez azt jelenti, hogy egy adott keresési szolgáltatás megkerülheti a virtuális hálózat vagy az IP-korlátozásokat a Storage-fiókban, és hozzáférhet a Storage-fiókban található adathoz, ha a megfelelő szerepköralapú hozzáférés-vezérlés engedélyezve van a Storage-fiókban. A részletek a [útmutatóban](search-indexer-howto-access-trusted-service-exception.md)olvashatók. Ez a beállítás az IP-korlátozási útvonal helyett használható, abban az esetben, ha a Storage-fiók vagy a keresési szolgáltatás nem helyezhető át másik régióba.

Az indexelő által használandó biztonságos hozzáférési mechanizmus kiválasztásakor vegye figyelembe a következő korlátozásokat:

- A [szolgáltatási végpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) nem támogatottak az Azure-erőforrások esetében.
- Egy keresési szolgáltatás nem építhető ki egy adott virtuális hálózatba – ezt a funkciót az Azure Cognitive Search nem fogja felajánlani.
- Ha az indexelő (kimenő) magánhálózati végpontokat használ az erőforrásokhoz való hozzáféréshez, előfordulhat, hogy további [privát kapcsolati költségek](https://azure.microsoft.com/pricing/details/search/) is érvényesek.

## <a name="indexer-execution-environment"></a>Indexelő végrehajtási környezet

Az Azure Cognitive Search indexelő képes hatékonyan kinyerni a tartalmakat az adatforrásokból, bővíteni a kinyert tartalmat, és opcionálisan generálja az eredményeket a keresési indexbe való írás előtt. Az indexelő által hozzárendelt felelősségek számától függően a következő két környezet egyikében futhat:

- Egy adott keresési szolgáltatáshoz tartozó saját környezet. Az ilyen környezetekben futó indexelő más számítási feladatokkal is megoszthatják az erőforrásokat (például más ügyfél által kezdeményezett indexelési vagy lekérdezési számítási feladatok). Általában csak olyan indexelő futnak, amelyek nem igényelnek sok erőforrást (például nem használnak készségkészlet) ebben a környezetben.
- Több-bérlős környezet, amely erőforrás-éhesek, például készségkészlet rendelkező indexelő üzemeltet. Az erőforrás-éhes erőforrások ezen a környezetben futnak az optimális teljesítmény érdekében, miközben biztosítják, hogy a keresési szolgáltatás erőforrásai elérhetők legyenek más számítási feladatokhoz. Ezt a több-bérlős környezetet az Azure Cognitive Search felügyeli és biztosítja, külön díj nélkül az ügyfélnek.

Az Azure Cognitive Search az indexelő futtatásához szükséges legjobb környezetet határozza meg.

## <a name="granting-access-to-indexer-ip-ranges"></a>Hozzáférés biztosítása az indexelő IP-tartományokhoz

Ha az indexelő által elérni kívánt erőforrás csak az IP-címtartományok bizonyos készletére korlátozódik, akkor ki kell bővíteni a készletet, hogy tartalmazza a lehetséges IP-tartományokat, amelyekből az indexelő kérelme származhat. Ahogy azt fentebb említettük, két lehetséges környezet létezik, amelyekben az indexelő fut, és ahonnan a hozzáférési kérelmek származhatnak. __Mindkét__ környezet IP-címét fel kell vennie az indexelő hozzáférésének működéséhez.

- A keresési szolgáltatás megadott privát környezetének IP-címének `nslookup` (vagy `ping` ) a keresési szolgáltatás teljes tartománynevének (FQDN) beszerzése. A nyilvános felhőben található keresési szolgáltatás teljes tartományneve, például: `<service-name>.search.windows.net` . Ezek az információk a Azure Portal érhetők el.
- A több-bérlős környezetek IP-címei a `AzureCognitiveSearch` szolgáltatás címkéjén keresztül érhetők el. Az [Azure-szolgáltatás címkéi](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) az egyes szolgáltatások IP-címeinek közzétett tartományával rendelkeznek – ez egy [felderítési API-n (előzetes verzió)](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview) vagy egy [letölthető JSON-fájlon](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files)keresztül érhető el. Mindkét esetben az IP-címtartományok régiónként vannak lebontva – csak azokat az IP-tartományokat választhatja ki, amelyek ahhoz a régióhoz vannak rendelve, amelyben a keresési szolgáltatást kiépítik.

Bizonyos adatforrások esetében maga a szolgáltatási címke közvetlenül is használható az IP-címtartományok listájának enumerálása helyett (a keresési szolgáltatás IP-címét explicit módon kell használni). Ezek az adatforrások korlátozzák a hozzáférést egy olyan [hálózati biztonsági csoport szabályának](https://docs.microsoft.com/azure/virtual-network/security-overview)beállításával, amely natív módon támogatja a szolgáltatási címkék hozzáadását, az olyan IP-szabályoktól eltérően, mint az Azure Storage, a CosmosDB, az Azure SQL stb., a `AzureCognitiveSearch` szolgáltatás címkét közvetlenül a keresési szolgáltatás IP-címe mellett felhasználó adatforrások:

- [IaaS virtuális gépeken futó SQL Server](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers#restrict-access-to-the-azure-cognitive-search)

- [SQL felügyelt példányai](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers#verify-nsg-rules)

A részleteket a [útmutató](search-indexer-howto-access-ip-restricted.md)című témakör ismerteti.

## <a name="granting-access-via-private-endpoints"></a>Hozzáférés biztosítása privát végpontokon keresztül

Az indexelő használhatnak [privát végpontokat](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) az erőforrásokhoz való hozzáféréshez, a hozzáféréshez, amelyekhez a virtuális hálózatokat kiválasztva vagy nincs engedélyezve a nyilvános hozzáférés.
Ez a funkció csak a fizetős szolgáltatások esetében érhető el, a létrehozott magánhálózati végpontok számának korlátozásával. A határértékekről a [Azure Search Limits oldalon](search-limits-quotas-capacity.md)olvashat bővebben.

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>1. lépés: hozzon létre egy privát végpontot a biztonságos erőforráshoz

Az ügyfeleknek meg kell hívniuk a keresési kezelési műveletet, [létre kell hozniuk vagy frissíteniük kell a *megosztott magánhálózati kapcsolati erőforrás* -API](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) -t a biztonságos erőforráshoz (például egy Storage-fiókhoz) való saját végponti kapcsolat létrehozásához. Az erre a (kimenő) privát végponti kapcsolatra irányuló forgalom csak a keresési szolgáltatásban megadott "privát" indexelő végrehajtási környezetben található virtuális hálózatból származik.

Az Azure Cognitive Search ellenőrzi, hogy az API hívói jogosultak-e a privát végpontok kapcsolódási kéréseinek a biztonságos erőforrásba való jóváhagyására. Ha például privát végponti kapcsolatot kér egy olyan Storage-fiókkal, amelyhez nem rendelkezik hozzáféréssel, a rendszer elutasítja a hívást.

### <a name="step-2-approve-the-private-endpoint-connection"></a>2. lépés: a magánhálózati végponti kapcsolatok jóváhagyása

Ha a megosztott magánhálózati kapcsolati erőforrást létrehozó (aszinkron) művelet befejeződik, a rendszer egy privát végponti kapcsolatot hoz létre "függőben" állapotba. Még nem folyik forgalom a kapcsolaton keresztül.
Az ügyfél ezután megkeresi a kérést a biztonságos erőforráson, és "jóváhagyja". Ez általában a portálon vagy a [Rest APIon](https://docs.microsoft.com/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection)keresztül végezhető el.

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>3. lépés: az indexelő kényszerített futtatása a "privát" környezetben

A jóváhagyott privát végpontok lehetővé teszik a keresési szolgáltatás kimenő hívásait olyan erőforrásra, amely valamilyen hálózati szintű hozzáférési korlátozással rendelkezik (például egy olyan Storage-fiók adatforrása, amely csak bizonyos virtuális hálózatokról érhető el) a sikeres elérés érdekében.
Ez azt jelenti, hogy a privát végponton keresztül egy ilyen adatforráshoz hozzáférő indexelő is sikeres lesz.
Ha a titkos végpont nincs jóváhagyva, vagy ha az indexelő nem használja fel a magánhálózati végponti kapcsolatokat, akkor az indexelő futtatja a-t `transientFailure` .

Ha engedélyezni szeretné az indexelő számára, hogy privát végponti kapcsolatokon keresztül hozzáférjenek az erőforrásokhoz, akkor kötelező beállítani az indexelő annak érdekében, hogy az `executionEnvironment` `"Private"` Indexelő minden futtatása képes legyen használni a privát végpontot. Ennek az az oka, hogy a privát végpontok a privát keresési szolgáltatás-specifikus környezetben vannak kiépítve.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Ezeket a lépéseket részletesebben ismertetjük a [útmutatóban](search-indexer-howto-access-private.md).
Miután jóváhagyta a privát végpontot egy erőforráshoz, az indexelő úgy van beállítva, hogy *a privát* végponti kapcsolaton keresztül hozzáférjen a hozzáféréshez.

### <a name="limits"></a>Korlátok

A keresési szolgáltatás optimális teljesítményének és stabilitásának biztosítása érdekében a korlátozásokat (a Search Service SKU alapján) a következő dimenziókban kell kivetni:

- *Magánjellegűként*beállítható indexelő típus.
- A létrehozható megosztott magánhálózati kapcsolati erőforrások száma.
- Azon különálló erőforrástípusok száma, amelyekhez megosztott magánhálózati kapcsolati erőforrások hozhatók létre.

Ezek a korlátok a [szolgáltatási korlátokban](search-limits-quotas-capacity.md)vannak dokumentálva.
