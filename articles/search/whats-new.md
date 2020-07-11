---
title: Az Azure Cognitive Search újdonságai
description: Új és továbbfejlesztett funkciókkal kapcsolatos bejelentések, beleértve a Azure Search az Azure Cognitive Search-ba történő átnevezését.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 07/10/2020
ms.openlocfilehash: 01968335ffcaffb26cabd2a5174d00e5471409c1
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232032"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Az Azure Cognitive Search újdonságai

Ismerje meg a szolgáltatás újdonságait. Könyvjelző ezen az oldalon naprakészen tarthatja a szolgáltatást.

## <a name="feature-announcements"></a>Szolgáltatások hirdetményei

### <a name="june-2020"></a>2020. június

|Vonás&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategória | Leírás | Rendelkezésre állás  |
|---------|------------------|-------------|---------------|
[**Tudástár**](knowledge-store-concept-intro.md) | MI-bővítés | Egy mesterséges intelligenciával rendelkező indexelő kimenete, amely az Azure Storage-ban tárol tartalmat más alkalmazásokban és folyamatokban való használatra. | Általánosan elérhető. </br> Használja a [Search REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) vagy újabb, vagy a portált. |
| [**Keresés REST API 2020-06-30**](https://docs.microsoft.com/rest/api/searchservice/) | REST | A REST API-k új stabil verziója. A tudásbázison kívül ez a verzió a keresés relevanciájának és pontozásának fejlesztéseit is tartalmazza. | Általánosan elérhető. |
| [**Okapi BM25 relevanciás algoritmus**](https://en.wikipedia.org/wiki/Okapi_BM25) | Lekérdezés | Az új relevancia-rangsorolási algoritmus automatikusan használatban van minden új, július 15. után létrehozott keresési szolgáltatáshoz. A korábban létrehozott szolgáltatások esetében a `similarity` tulajdonságot a tárgymutató mezőinek beállításával engedélyezheti. | Általánosan elérhető. </br> Használja a [Search REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) vagy újabb, vagy REST API 2019-05-06. |
| **executionEnvironment** | Biztonság (indexelő) | Explicit módon állítsa be ezt az indexelő konfigurációs tulajdonságot úgy, hogy az a `private` külső adatforrásokhoz való összes kapcsolatot egy privát végponton keresztül kényszerítse. Csak olyan keresési szolgáltatásokra vonatkozik, amelyek az Azure Private link szolgáltatást használják. | Általánosan elérhető. </br> Ezt az általános konfigurációs paramétert a [Search REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) paranccsal állíthatja be. |
| [**Azure Machine Learning (pénzmosás) ismerete**](cognitive-search-aml-skill.md) | MI-bővítés| Egy új, az Azure Machine Learning-ból származó következtetési végpont integrálására szolgáló képzettségi típus. Ismerkedjen meg az [oktatóanyaggal](cognitive-search-tutorial-aml-custom-skill.md). | Általánosan elérhető. </br> A [Search REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) vagy újabb verzió használata. A portálon is elérhető a készségkészlet-kialakításban, feltéve, hogy a Cognitive Search és az Azure ML-szolgáltatások ugyanabban az előfizetésben vannak telepítve. |

### <a name="may-2020-microsoft-build"></a>Május 2020 (Microsoft Build)

|Vonás&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategória | Leírás | Rendelkezésre állás  |
|---------|------------------|-------------|---------------|
| [**Munkamenetek hibakeresése**](cognitive-search-debug-session.md) | MI-bővítés | A hibakeresési munkamenetek egy portálon alapuló felületet biztosítanak a meglévő készségkészlet kapcsolatos problémák kivizsgálásához és megoldásához. A hibakeresési munkamenetben létrehozott javítások az éles szakértelmével menthetők. Ismerkedjen meg az [oktatóanyaggal](cognitive-search-tutorial-debug-sessions.md). | Nyilvános előzetes verzió a portálon. |
| [**A kötött tűzfal-támogatás IP-szabályai**](service-configure-firewall.md) | Biztonság | Egy keresési szolgáltatási végpont elérésének korlátozása adott IP-címekre. | Általánosan elérhető. </br> A [felügyeleti REST API 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) -es vagy újabb verzióját, vagy a portált használja. |
| [**Azure Private-hivatkozás privát keresési végponthoz**](service-create-private-endpoint.md) | Biztonság| Egy keresési szolgáltatást a nyilvános internetről privát kapcsolati erőforrásként futtatva, amely csak az ügyfélalkalmazások és más Azure-szolgáltatások számára érhető el ugyanazon a virtuális hálózaton. | Általánosan elérhető. </br> A [felügyeleti REST API 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) -es vagy újabb verzióját, vagy a portált használja. |
| [**rendszer által felügyelt identitás (előzetes verzió)**](search-howto-managed-identities-data-sources.md) | Biztonság (indexelő) | Egy keresési szolgáltatás megbízható szolgáltatásként való regisztrálása a Azure Active Directory használatával a támogatott Azure-adatforrásokhoz való csatlakozások indexeléshez való beállításához. Az olyan [Indexelő](search-indexer-overview.md) anyagokra vonatkozik, amelyek az Azure-adatforrásokból (például Azure SQL Database, Azure Cosmos db és Azure Storage) származó tartalmakat töltenek be. | Nyilvános előzetes verzió. </br> Regisztrálja a keresési szolgáltatást a portál használatával. |
| [**munkamenet-lekérdezési paraméter**](index-similarity-and-scoring.md), [scoringStatistics = globális paraméter](index-similarity-and-scoring.md#scoring-statistics) | Lekérdezés (relevancia) | Adja hozzá a munkamenet-azonosítókat egy lekérdezéshez, és hozzon létre egy munkafolyamatot a számítástechnikai keresési pontszámok számára, és a scoringStatistics = Global (pontszámok gyűjtése az összes szegmensből) a további konzisztens keresési pontszám kiszámítása | Általánosan elérhető. </br> Használja a [Search REST API 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) vagy újabb, vagy REST API 2019-05-06. |
| [**featuresMode (előzetes verzió)**](index-similarity-and-scoring.md#featuresMode-param) | Lekérdezés | Adja hozzá ezt a lekérdezési paramétert a relevancia pontszám kibontásához a további részletek megjelenítéséhez: mező szerinti hasonlósági pontszám, mező szerinti kifejezés gyakorisága, valamint az egyedi tokenek által egyeztetve. Ezeket az adatpontokat egyéni pontozási algoritmusokban is felhasználhatja. A képességet bemutató minta: a [Machine learning (LearnToRank) hozzáadása a relevancia kereséséhez](https://github.com/Azure-Samples/search-ranking-tutorial). | Nyilvános előzetes verzió. </br> Használja a [Search REST API 2020-06-30 – Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) vagy REST API 2019-05-06-Preview lehetőséget. |

### <a name="march-2020"></a>2020. március

|Vonás&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategória | Leírás | Rendelkezésre állás  |
|---------|------------------|-------------|---------------|
| [**Natív blob – Soft Delete (előzetes verzió)**](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) | Indexelők | Egy Azure Blob Storage indexelő az Azure-ban Cognitive Search felismeri a törölt állapotban lévő blobokat, és eltávolítja a megfelelő keresési dokumentumot az indexelés során. | Nyilvános előzetes verzió. </br> A [Search REST API 2020-06-30 – Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) és a REST API 2019-05-06-Preview, a Run indexelő paranccsal olyan Azure Blob-adatforrást használhat, amelyen engedélyezve van a "Soft Delete" elem. |
| [**Felügyeleti REST API (2020-03-13)**](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) | REST | Új stabil REST API keresési szolgáltatás létrehozásához és kezeléséhez. IP-tűzfal és privát hivatkozás támogatása | Általánosan elérhető. |

### <a name="february-2020"></a>2020. február

|Vonás&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategória | Leírás | Rendelkezésre állás  |
|---------|------------------|-------------|---------------|
| [**Személyes adatok észlelése (előzetes verzió)**](cognitive-search-skill-pii-detection.md) | MI-bővítés | Új kognitív képesség az indexelés során, amely a személyazonosításra alkalmas adatokat egy bemeneti szövegből kigyűjti, és lehetővé teszi, hogy az adott szövegtől különböző módokon maszkot adjon. | Nyilvános előzetes verzió. </br> Használja a portált vagy a [Search REST API 2020-06-30 – Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) vagy REST API 2019-05-06-preview. |
| [**Egyéni entitások keresése (előzetes verzió)**](cognitive-search-skill-custom-entity-lookup.md )| MI-bővítés | Új kognitív képesség, amely szövegeket keres a szavak és kifejezések egyéni, felhasználó által meghatározott listájából. Ezzel a listával minden olyan dokumentumot felcímkéz, amely minden egyező entitással rendelkezik. A képesség a hasonló, de nem pontos egyezések keresésére is használható. | Nyilvános előzetes verzió. </br> Használja a portált vagy a [Search REST API 2020-06-30 – Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview) vagy REST API 2019-05-06-preview. |

### <a name="january-2020"></a>2020. január

|Vonás&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategória | Leírás | Rendelkezésre állás  |
|---------|------------------|-------------|---------------|
| [**Ügyfél által felügyelt titkosítási kulcsok**](search-security-manage-encryption-keys.md) |Biztonság | További titkosítási réteget is felvesz a platform beépített titkosítása mellett. Egy Ön által létrehozott és kezelt titkosítási kulcs használatával titkosíthatja az indexelési tartalmat és a szinonimákat, mielőtt a hasznos adatok elérnék a keresési szolgáltatást. | Általánosan elérhető. </br> A Search REST API 2019-05-06 vagy újabb verzió használata. A felügyelt kód esetében a megfelelő csomag még mindig a [.net SDK 8,0-es verziója – előzetes verzió](search-dotnet-sdk-migration-version-9.md) , bár a funkció nem előzetes verzió. |
| [**A kötött tűzfalak támogatásának IP-szabályai (előzetes verzió)**](service-configure-firewall.md) | Biztonság | Egy keresési szolgáltatási végpont elérésének korlátozása adott IP-címekre. Az előzetes verziójú API új **IpRule** és **NetworkRuleSet** tulajdonságokkal rendelkezik a [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service)-ban. Ez az előzetes verziójú funkció a kiválasztott régiókban érhető el. |  Nyilvános előzetes verzió az API-Version = 2019-10 -01-Preview használatával.  |
| [**Azure Private-hivatkozás privát keresési végponthoz (előzetes verzió)**](service-create-private-endpoint.md) | Biztonság| Egy keresési szolgáltatást a nyilvános internetről privát kapcsolati erőforrásként futtatva, amely csak az ügyfélalkalmazások és más Azure-szolgáltatások számára érhető el ugyanazon a virtuális hálózaton. | Nyilvános előzetes verzió az API-Version = 2019-10 -01-Preview használatával.  |

## <a name="feature-announcements-in-2019"></a>Szolgáltatások hirdetményei a 2019-ben

### <a name="december-2019"></a>2019. december

+ A [demo-alkalmazás létrehozása (előzetes verzió)](search-create-app-portal.md) egy új varázsló a portálon, amely egy, a lekérdezéssel (írásvédett) rendelkező letölthető HTML-fájlt hoz létre egy indexhez. A fájl beágyazott parancsfájlt tartalmaz, amely egy operatív "localhost" stílusú webalkalmazást jelenít meg, amely egy indexhez van kötve a keresési szolgáltatásban. A lapok konfigurálhatók a varázslóban, és tartalmazhatnak egy keresősáv, az eredmények, az oldalsáv és a typeahead-lekérdezés támogatását. A HTML-t a munkafolyamat vagy a megjelenés kibővítéséhez vagy testreszabásához offline módon módosíthatja. A bemutató alkalmazások nem könnyen bővíthetők olyan biztonsági és üzemeltetési rétegekkel, amelyek jellemzően az éles környezetekben szükségesek. A teljes ügyfélalkalmazás helyett érdemes ellenőrzési és tesztelési eszközt figyelembe venni.

+ [Hozzon létre egy privát végpontot a biztonságos kapcsolatokhoz (előzetes verzió)](service-create-private-endpoint.md) ismerteti, hogyan állíthat be egy privát hivatkozást a keresési szolgáltatáshoz való biztonságos csatlakozáshoz. Ez az előzetes verziójú funkció igény szerint elérhető, és az Azure [Private link](../private-link/private-link-overview.md) és az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) használja a megoldás részeként.

### <a name="november-2019---ignite-conference"></a>November 2019 – Ignite konferencia

+ A [növekményes bővítés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) gyorsítótárazást és statefullness tesz elérhetővé egy alkoholtartalom-növelési folyamathoz, így adott lépéseken vagy fázisokon dolgozhat a már feldolgozott tartalmak elvesztése nélkül. Korábban a dúsítási folyamat bármilyen módosítása teljes Újraépítés szükséges. A növekményes bővítéssel a költséges elemzések, különösen a képelemzések kimenete megmarad.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ A [dokumentumok kinyerése (előzetes verzió)](cognitive-search-skill-document-extraction.md) az indexelés során használt kognitív képesség, amely lehetővé teszi egy fájl tartalmának kicsomagolását egy készségkészlet belülről. Korábban a készségkészlet végrehajtása előtt csak a csinos dokumentum történt. Ennek a képességnek a hozzáadásával ezt a műveletet a készségkészlet végrehajtásán belül is végrehajthatja.

+ A [szöveg fordítása](cognitive-search-skill-text-translation.md) olyan kognitív képesség, amely a szöveget kiértékelő indexelés során használatos, és minden rekord esetében a megadott nyelvre fordított szöveget adja vissza.

+ A [Power bi-sablonok](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) a Power bi Desktopban található Tudásbázisban megadhatják a gazdagított tartalmak vizualizációit és elemzését. Ez a sablon az [adatimportálás varázsló](knowledge-store-create-portal.md)segítségével létrehozott Azure Table-kivetítésekhez készült.

+ A [Azure Data Lake Storage Gen2 (előzetes verzió)](search-howto-index-azure-data-lake-storage.md), a [Cosmos db Gremlin API (előzetes verzió)](search-howto-index-cosmosdb.md)és a [Cosmos db Cassandra API (előzetes verzió)](search-howto-index-cosmosdb.md) mostantól támogatott az indexelő szolgáltatásban. [Az űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)használatával regisztrálhat. Ha elfogadja az előzetes programot, egy visszaigazoló e-mailt fog kapni.

### <a name="july-2019"></a>2019. július

+ Általánosan elérhető [Azure Government felhőben](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Új szolgáltatásnév

A Azure Search most átnevezve lett az **Azure Cognitive Searchra** , hogy tükrözze a kognitív képességek kibővített (de opcionális) használatát a fő műveletekben. Az API-verziók, a NuGet-csomagok, a névterek és a végpontok változatlanok. Az új és a meglévő keresési megoldásokat nem érinti a szolgáltatás nevének változása.

## <a name="service-updates"></a>Szolgáltatási hírek

Az Azure Cognitive Search [Service Update-hirdetményei](https://azure.microsoft.com/updates/?product=search&status=all) megtalálhatók az Azure webhelyén.