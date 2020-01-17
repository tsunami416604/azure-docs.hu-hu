---
title: Indexelőkkel kapcsolatos hibák és figyelmeztetések
titleSuffix: Azure Cognitive Search
description: Ez a cikk olyan gyakori hibákkal és figyelmeztetésekkel kapcsolatos információkat és megoldásokat tartalmaz, amelyeket az AI az Azure Cognitive Search-ban való gyarapítása során felmerülhet.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 74d209adf745d1a3c319ef6567b2a7818a5fd514
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152256"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Általános indexelő hibák és figyelmeztetések hibaelhárítása Az Azure Cognitive Search

Ez a cikk a gyakori hibákkal és figyelmeztetésekkel kapcsolatos információkat és figyelmeztetéseket tartalmaz, amelyeket az indexelés és az AI-bővítés során találkozhat az Azure Cognitive Searchban.

Az indexelés leáll, ha a hibák száma meghaladja a ["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Ha azt szeretné, hogy az indexelő figyelmen kívül hagyják ezeket a hibákat (és ugorjon a "sikertelen dokumentumok" elemre), akkor érdemes frissíteni a `maxFailedItems` és `maxFailedItemsPerBatch` az [itt](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)leírtak szerint.

> [!NOTE]
> Minden sikertelen dokumentum, valamint a dokumentum kulcsa (ha elérhető) az indexelő végrehajtási állapotában hibát jelez. Az [index API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) használatával manuálisan is feltöltheti a dokumentumokat egy későbbi időpontban, ha úgy állította be az indexelő, hogy eltűri a hibákat.

A cikkben szereplő hibaüzenetek segíthetnek a hibák megoldásában, így az indexelés a folytatáshoz is használható.

A figyelmeztetések nem állíthatják le az indexelést, de olyan feltételeket jeleznek, amelyek váratlan eredményekhez vezethetnek. Függetlenül attól, hogy végrehajtja-e a műveletet, vagy nem függ az adatoktól és a forgatókönyvtől.

Az API-verziótól kezdődően a `2019-05-06`az elemszintű indexelő hibái és a figyelmeztetések strukturálva vannak, így az okok és a következő lépések nagyobb átláthatóságot biztosítanak. A következő tulajdonságokat tartalmazzák:

| Tulajdonság | Leírás | Példa |
| --- | --- | --- |
| kulcs | A hibát vagy figyelmeztetést érintő dokumentum AZONOSÍTÓját. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| név | Annak a műveletnek a neve, amelyben a hiba vagy a figyelmeztetés történt. Ezt a következő struktúra hozza létre: [category]. [Alkategória]. [resourceType]. ResourceName | DocumentExtraction. azureblob. myBlobContainerName alkoholtartalom-növelés. WebApiSkill. mySkillName vetítés. SearchIndex. OutputFieldMapping. myOutputFieldName vetítés. SearchIndex. MergeOrUpload. myIndexName Vetület. KnowledgeStore. table. myTableName |
| message | A hiba vagy figyelmeztetés magas szintű leírása. | A képesség nem hajtható végre, mert a webes API-kérelem sikertelen volt. |
| Részletek | További részletek, amelyek hasznosak lehetnek a probléma diagnosztizálásához, például a WebApi-válaszhoz, ha az egyéni képességet nem sikerült végrehajtani. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 forrás, függvény`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.`... fennmaradó verem nyomkövetése... |
| documentationLink | A kapcsolódó dokumentációra mutató hivatkozás, amely részletes információkat tartalmaz a hibakereséshez és a probléma megoldásához. Ez a hivatkozás gyakran a lap alábbi részeinek egyikére mutat. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Hiba: a dokumentum nem olvasható

Az indexelő nem tudta olvasni a dokumentumot az adatforrásból. Ez az alábbiak miatt fordulhat elő:

| Ok | Részletek/példa | Felbontás |
| --- | --- | --- |
| inkonzisztens mezők típusai különböző dokumentumokban | Az érték típusa nem egyezik az oszlop típusával. Nem sikerült tárolni a `'{47.6,-122.1}'` a szerzők oszlopban.  A várt típus: JArray. | Ügyeljen arra, hogy az egyes mezők típusa azonos legyen a különböző dokumentumok között. Ha például az első dokumentum `'startTime'` a dátum DateTime, és a második dokumentumban ez egy karakterlánc, akkor ez a hiba fog megjelenni. |
| hibák az adatforrás mögöttes szolgáltatásában | (Cosmos DB) `{"Errors":["Request rate is large"]}` | Ellenőrizze, hogy a tárolási példány kifogástalan állapotú-e. Előfordulhat, hogy módosítania kell a skálázást/particionálást. |
| átmeneti problémák | Átviteli szintű hiba történt a kiszolgáló eredményeinek fogadása során. (szolgáltató: TCP-szolgáltató, hiba: 0 – a távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat | Esetenként váratlan kapcsolódási problémák léptek fel. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Hiba: nem sikerült kinyerni a dokumentum tartalmát vagy metaadatait
A blob-adatforrással rendelkező indexelő nem tudta kinyerni a tartalmat vagy a metaadatokat a dokumentumból (például egy PDF-fájlt). Ez az alábbiak miatt fordulhat elő:

| Ok | Részletek/példa | Felbontás |
| --- | --- | --- |
| a blob mérete meghaladja a méretkorlátot | A dokumentum `'150441598'` bájt, ami meghaladja a jelenlegi szolgáltatási szinten a dokumentumok kinyeréséhez szükséges maximális méretet `'134217728'` bájtban. | [BLOB-indexelési hibák](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| a blob nem támogatott tartalomtípust tartalmaz | A dokumentum tartalma nem támogatott `'image/png'` | [BLOB-indexelési hibák](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| a blob titkosított | A dokumentumot nem lehetett feldolgozni – titkosított vagy jelszóval védett. | A blobot a blob- [beállításokkal](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)lehet kihagyni. |
| átmeneti problémák | "Hiba történt a blob feldolgozásakor: a kérés megszakadt: a kérést megszakították." "A dokumentum időkorlátja lejárt a feldolgozás során." | Esetenként váratlan kapcsolódási problémák léptek fel. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Hiba: a dokumentum nem elemezhető
Az indexelő beolvassa a dokumentumot az adatforrásból, de hiba történt a dokumentum tartalmának a megadott mező-hozzárendelési sémába való konvertálása során. Ez az alábbiak miatt fordulhat elő:

| Ok | Részletek/példa | Felbontás |
| --- | --- | --- |
| A dokumentum kulcsa hiányzik | A dokumentum kulcsa nem lehet hiányzik vagy üres. | Győződjön meg arról, hogy minden dokumentum rendelkezik érvényes dokumentum-kulcsokkal |
| A dokumentum kulcsa érvénytelen | A dokumentum kulcsa nem lehet hosszabb 1024 karakternél | Módosítsa a dokumentum kulcsát az érvényesítési követelmények teljesítéséhez. |
| A mező leképezése nem alkalmazható egy mezőre | A leképezési függvény nem alkalmazható a (`'fieldName'`) mezőre `'functionName'`. A tömb nem lehet null. Paraméter neve: bájtok | Ellenőrizze az indexelő által definiált [mező-hozzárendeléseket](search-indexer-field-mappings.md) , és hasonlítsa össze a hibás dokumentum megadott mezőjének értékével. Előfordulhat, hogy módosítania kell a mező-hozzárendeléseket vagy a dokumentum-adattípust. |
| Nem olvasható be a mező értéke | Nem lehetett beolvasni a (z) `'fieldName'` oszlop értékét a következő indexnél: `'fieldIndex'`. Átviteli szintű hiba történt a kiszolgáló eredményeinek fogadása során. (szolgáltató: TCP-szolgáltató, hiba: 0 – a távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat.) | Ezek a hibák általában az adatforrás mögöttes szolgáltatásával kapcsolatos váratlan kapcsolódási problémák miatt jelentkeznek. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Hiba: nem sikerült végrehajtani a képességet
Az indexelő nem tudott futtatni egy képességet a készségkészlet.

| Ok | Részletek/példa | Felbontás |
| --- | --- | --- |
| Átmeneti kapcsolódási problémák | Átmeneti hiba történt. Később próbálja meg újra. | Esetenként váratlan kapcsolódási problémák léptek fel. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |
| Lehetséges termék hibája | Váratlan hiba történt. | Ez ismeretlen osztályt jelez, és a termék meghibásodását is jelentheti. Kérjen segítséget a [támogatási jegyen](https://ms.portal.azure.com/#create/Microsoft.Support) . |
| A rendszer hibát észlelt a végrehajtás során | (Egyesítési képességből) Egy vagy több eltolási érték érvénytelen, és nem elemezhető. Elemek beszúrása a szöveg végére | A probléma megoldásához használja a hibaüzenetben található információkat. Ilyen hiba esetén a megoldáshoz beavatkozás szükséges. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Hiba: nem sikerült végrehajtani a képességet, mert a webes API-kérelem sikertelen volt.
A szaktudás végrehajtása nem sikerült, mert a webes API-hívás sikertelen volt. Ez az osztály általában egyéni szaktudás használata esetén fordul elő, ebben az esetben a probléma megoldásához hibakeresést kell végeznie az Egyéni kódban. Ha ehelyett a hiba egy beépített képességből származik, a probléma megoldásához a hibaüzenetben tájékozódhat.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Hiba: a képesség nem hajtható végre, mert a webes API-ügyességi válasz érvénytelen.
A szaktudás végrehajtása nem sikerült, mert a webes API hívása érvénytelen választ adott vissza. Ez az osztály általában egyéni szaktudás használata esetén fordul elő, ebben az esetben a probléma megoldásához hibakeresést kell végeznie az Egyéni kódban. Ha ehelyett a hiba egy beépített képességből származik, akkor kérjen segítséget a [támogatási jegyen](https://ms.portal.azure.com/#create/Microsoft.Support) .

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Hiba: a szakértelem nem lett végrehajtva az időkorláton belül
Ez a hibaüzenet két esetben fordulhat elő, amelyek mindegyikét másképp kell kezelni. Kövesse az alábbi utasításokat attól függően, hogy milyen képességgel tért vissza ez a hiba.

### <a name="built-in-cognitive-service-skills"></a>Beépített kognitív szolgáltatásokkal kapcsolatos ismeretek
Számos beépített kognitív képesség, például a nyelvfelismerés, az entitások felismerése vagy az OCR, a kognitív szolgáltatás API-végpontja támogatja. Időnként átmeneti problémák léptek fel ezekkel a végpontokkal, és a kérés időtúllépést eredményez. Átmeneti problémák esetén nincs szükség jogorvoslatra, kivéve a várakozást, és próbálkozzon újra. Enyhítő megoldásként érdemes lehet úgy beállítani az indexelő, hogy [menetrend szerint fusson](search-howto-schedule-indexers.md). Az ütemezett indexelés megkeresi, hogy hol maradt. Az átmeneti problémák megoldása érdekében az indexelést és a kognitív képességek feldolgozását folytatni kell a következő ütemezett futtatáskor.

Ha továbbra is ezt a hibát látja egy beépített kognitív szakértelmet tartalmazó dokumentumon, egy [támogatási jegyet](https://ms.portal.azure.com/#create/Microsoft.Support) kell benyújtania a segítség kéréséhez, mivel ez nem várható.

### <a name="custom-skills"></a>Egyéni készségek
Ha az Ön által létrehozott egyéni szakértelem időtúllépési hibába ütközik, néhány dolog kipróbálható. Először tekintse át az egyéni szaktudást, és győződjön meg arról, hogy nem ragadt meg egy végtelen hurokban, és hogy az eredmény következetesen tér vissza. Miután meggyőződött róla, hogy a helyzet megtörtént, állapítsa meg, hogy milyen végrehajtási idő van a szakértelemben. Ha nem adott meg explicit módon egy `timeout` értéket az egyéni szakértelem definíciójában, akkor az alapértelmezett `timeout` 30 másodperc. Ha 30 másodperc nem elég hosszú a szaktudás végrehajtásához, akkor magasabb `timeout` értéket adhat meg az egyéni képesség definíciójában. Íme egy példa egy egyéni képesség-definícióra, amelyben az időtúllépés 90 másodpercre van állítva:

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

A `timeout` paraméter számára beállítható maximális érték 230 másodperc.  Ha az egyéni képesség nem hajtható végre következetesen 230 másodpercen belül, érdemes lehet csökkenteni az egyéni képesség `batchSize`ét, hogy kevesebb dokumentum legyen feldolgozható egyetlen végrehajtáson belül.  Ha már beállította a `batchSize` 1-re, újra kell írnia a képességet, hogy 230 másodpercen belül végre tudja hajtani a végrehajtást, vagy más módon feloszthatja azt több egyéni képességre, hogy az egyetlen egyéni képesség végrehajtásának ideje legfeljebb 230 másodperc legyen. További információért tekintse át az [Egyéni szakértelem dokumentációját](cognitive-search-custom-skill-web-api.md) .

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Hiba: nem sikerült a (z) "`MergeOrUpload`" | "`Delete`" dokumentum a keresési indexhez

A dokumentum olvasása és feldolgozása megtörtént, de az indexelő nem tudja felvenni a keresési indexbe. Ez az alábbiak miatt fordulhat elő:

| Ok | Részletek/példa | Felbontás |
| --- | --- | --- |
| Egy mező túl nagy kifejezést tartalmaz | A dokumentum egy kifejezése nagyobb az [32 KB-os korlátnál](search-limits-quotas-capacity.md#api-request-limits) | Ezt a korlátozást elkerülheti, ha úgy látja, hogy a mező nem szűrhető, sokoldalú vagy rendezhető.
| A dokumentum túl nagy az indexeléshez | A dokumentum nagyobb, mint az [API-kérelmek maximális mérete](search-limits-quotas-capacity.md#api-request-limits) | [Nagyméretű adathalmazok indexelése](search-howto-large-index.md)
| A dokumentum túl sok objektumot tartalmaz a gyűjteményben | A dokumentum egy gyűjteménye meghaladja a [maximális elemeket az összes összetett gyűjteménynél](search-limits-quotas-capacity.md#index-limits) , amely korlátozza a (z) `'1000052'` kulccsal rendelkező dokumentum `'4303'` objektumait gyűjteményekben (JSON-tömbökben). A legtöbb `'3000'` objektum a teljes dokumentumban lévő gyűjteményekben szerepelhet. Távolítsa el az objektumokat a gyűjteményből, majd próbálja meg újból indexelni a dokumentumot. " | Javasoljuk, hogy csökkentse a dokumentum összetett gyűjteményének méretét a korlát alá, és elkerülje a nagy tárterület-kihasználtságot.
| Hiba történt a célként megadott indexhez való kapcsolódáskor (amely az újrapróbálkozások után is fennmarad), mert a szolgáltatás más terhelés alá esik, például lekérdezés vagy indexelés. | Nem sikerült kapcsolatot létesíteni az index frissítésével. A Search szolgáltatás nagy terhelés alatt áll. | [A keresési szolgáltatás vertikális felskálázása](search-capacity-planning.md)
| A keresési szolgáltatás javítás alatt áll a szolgáltatás frissítésére, vagy a topológia újrakonfigurálásának közepén található. | Nem sikerült kapcsolatot létesíteni az index frissítésével. A keresési szolgáltatás jelenleg nem érhető el, a keresési szolgáltatás pedig átmeneti állapotba kerül. | Állítsa be a szolgáltatást legalább 3 replikával az 99,9%-os rendelkezésre álláshoz az [SLA dokumentációjában](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Hiba történt az alapul szolgáló számítási/hálózati erőforrásban (ritka) | Nem sikerült kapcsolatot létesíteni az index frissítésével. Ismeretlen hiba történt. | Az indexelő úgy konfigurálható, hogy a sikertelen állapotból való kiválasztáshoz [ütemezett ütemtervet futtasson](search-howto-schedule-indexers.md) .
| A rendszer nem ismerte fel a célként megadott indexre vonatkozó indexelési kérelmet a hálózati problémák miatti időtúllépési időszakon belül. | Nem lehet időben kapcsolatot létesíteni a keresési indexszel. | Az indexelő úgy konfigurálható, hogy a sikertelen állapotból való kiválasztáshoz [ütemezett ütemtervet futtasson](search-howto-schedule-indexers.md) . Ha ez a hiba nem szűnik meg, csökkentse az indexelő [köteg méretének](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) csökkentését.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>Hiba: a dokumentum nem indexelhető, mert az indexelő által indexelt adatbázis érvénytelen.

A dokumentumot beolvasták és feldolgozták, de az index mezőinek és az indexelő által kinyert adat természetének eltérése miatt nem lehetett felvenni a keresési indexbe. Ez az alábbiak miatt fordulhat elő:

| Ok | Részletek/példa
| --- | ---
| Az indexelő által kinyert mező (k) adattípusa nem kompatibilis a megfelelő cél index mező adatmodelljével. | A (z) "888" kulccsal rendelkező dokumentumban levő "adatmező"_érvénytelen "EDM_. String" értékű. A várt típus: "Collection (EDM. String)". |
| Nem sikerült kibontani a JSON-entitásokat egy karakterlánc-értékből. | Nem sikerült elemezni a (z) ""_típusú ""_ EDM. String "" érték "MEZŐjét JSON-objektumként. Hiba: "az érték elemzése után a rendszer váratlan karaktert észlelt:" ". Elérési_út_útvonala, 1. sor, pozíció: 3162. |
| Nem sikerült kibontani a JSON-entitások gyűjteményét egy karakterlánc-értékből.  | Nem sikerült értelmezni a (z) ""_típusú ""_ EDM. String "" érték "MEZŐjét JSON-tömbként. Hiba: "az érték elemzése után a rendszer váratlan karaktert észlelt:" ". Elérési út: "[0]", 1. sor, 27. pozíció |
| Ismeretlen típus lett felderítve a forrásbizonylat dokumentumban. | Ismeretlen típus (_ismeretlen_) nem indexelhető |
| A forrás dokumentumban nem kompatibilis jelölés szerepel a földrajzi pontokhoz. | A WKT pont karakterlánca nem támogatott. Ehelyett használjon GeoJson pont literálokat |

Ebben az esetben tekintse át a [támogatott adattípusokat](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) és [adattípus-leképezést az indexelő](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) számára, és győződjön meg arról, hogy helyesen hozza létre az index-sémát, és beállította a megfelelő [Indexelő mező-hozzárendeléseket](search-indexer-field-mappings.md). A hibaüzenet olyan részleteket tartalmaz, amelyek segítségével nyomon követheti az eltérés forrását.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Hiba: a dokumentum nem dolgozható fel az indexelő maximális futási idején belül

Ez a hiba akkor fordul elő, ha az indexelő nem tudja befejezni egyetlen dokumentum feldolgozását az adatforrásból az engedélyezett végrehajtási időn belül. A szakértelmével használata esetén a [maximális futási idő](search-limits-quotas-capacity.md#indexer-limits) rövidebb. Ha ez a hiba akkor fordul elő, ha a maxFailedItems értéke nem 0, az indexelő megkerüli a dokumentumot a jövőben, hogy az indexelés előrehaladást érjen el. Ha nem engedheti meg, hogy kihagyjon bármilyen dokumentumot, vagy ha konzisztensen látja ezt a hibát, érdemes lehet kisebb dokumentumokra feltörni a dokumentumokat, hogy a részleges előrehaladás egyetlen indexelő végrehajtáson belül is elvégezhető legyen.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>Hiba: nem sikerült a Project Document

Ez a hiba akkor fordul elő, amikor az indexelő az [adatait egy tudásbázisba](knowledge-store-projection-overview.md) kísérli meg, és hiba történt a kísérlet során.  Ez a hiba lehet konzisztens és javítható, vagy előfordulhat, hogy átmeneti hiba történt a kivetítési kimeneti fogadónál, amelyet várnia kell, és újra kell próbálkoznia a feloldáshoz.  Az alábbiakban az ismert meghibásodási állapotok és a lehetséges megoldások halmaza látható.

| Ok | Részletek/példa | Felbontás |
| --- | --- | --- |
| Nem lehetett frissíteni a kivetítési blobot `'blobUri'` a tárolóban `'containerName'` |A megadott tároló nem létezik. | Az indexelő ellenőrzi, hogy a megadott tárolót korábban hozták-e létre, és szükség esetén létrehozza-e, de csak egyszer hajtja végre ezt az ellenőrzést az indexelő futtatásakor. Ez a hiba azt jelenti, hogy valami törölte a tárolót a lépés után.  A hiba elhárításához próbálja meg a következőt: ne csak a Storage-fiók adatait adja meg, várjon, amíg az indexelő befejeződik, majd futtassa újra az indexelő. |
| Nem lehetett frissíteni a kivetítési blobot `'blobUri'` a tárolóban `'containerName'` |Az adatátvitel nem írható a szállítási kapcsolatban: a távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat. | Ez várhatóan átmeneti hiba az Azure Storage-ban, ezért az indexelő újbóli futtatásával kell megoldani. Ha konzisztensen Tapasztalja ezt a hibát, küldjön egy [támogatási jegyet](https://ms.portal.azure.com/#create/Microsoft.Support) , hogy később is megvizsgálható legyen.  |
| Nem lehetett frissíteni a sor `'projectionRow'` a táblában `'tableName'` | A kiszolgáló foglalt. | Ez várhatóan átmeneti hiba az Azure Storage-ban, ezért az indexelő újbóli futtatásával kell megoldani. Ha konzisztensen Tapasztalja ezt a hibát, küldjön egy [támogatási jegyet](https://ms.portal.azure.com/#create/Microsoft.Support) , hogy később is megvizsgálható legyen.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Figyelmeztetés: a szakértelem bemenete érvénytelen volt.
A szakértelem bemenete hiányzik, nem megfelelő típusú, vagy más módon érvénytelen. A figyelmeztető üzenet a következő hatást fogja jelezni:
1) Nem hajtható végre a szaktudás
2) A végrehajtott szaktudás, de nem várt eredmények

A kognitív képességek kötelező bemenetekkel és opcionális bemenetekkel rendelkeznek. A [kinyerési képességnek](cognitive-search-skill-keyphrases.md) például két kötelező bemenete van `text`, `languageCode`, és nem választható bemenet. Az egyéni szaktudás bemenetei mindegyike opcionális bemenetnek minősül.

Ha bármilyen szükséges bemenet hiányzik, vagy ha bármelyik bemenet nem megfelelő típusú, a rendszer kihagyja a szakértelmet, és figyelmeztetést generál. A kihagyott képességek nem eredményeznek kimeneteket, így ha más ismeretek is a kihagyott képességek kimenetét használják, további figyelmeztetések is készíthetők.

Ha egy opcionális bemenet hiányzik, a rendszer továbbra is futtatja a képességet, de a hiányzó bevitel miatt váratlan kimenetet eredményezhet.

Mindkét esetben ez a figyelmeztetés az adatszerkezet miatt várható. Ha például olyan dokumentummal rendelkezik, amely `firstName`, `middleName`és `lastName`mezőkkel rendelkező személyekkel kapcsolatos információkat tartalmaz, előfordulhat, hogy olyan dokumentumokkal rendelkezik, amelyek nem rendelkeznek `middleName`-bejegyzéssel. Ha a folyamat egy képességének bemenetként adja át a `middleName`t, akkor előfordulhat, hogy ez a szakértelem-bevitel néhány idő elteltével hiányzik. Ki kell értékelnie az adatait és a forgatókönyvet annak meghatározásához, hogy szükséges-e valamilyen művelet a figyelmeztetés eredményeképpen.

Ha hiányzó bemenet esetén szeretné megadni az alapértelmezett értéket, a [feltételes képesség](cognitive-search-skill-conditional.md) használatával létrehozhat egy alapértelmezett értéket, majd használhatja a [feltételes képesség](cognitive-search-skill-conditional.md) kimenetét a szaktudás bemenetének megfelelően.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| Ok | Részletek/példa | Felbontás |
| --- | --- | --- |
| A szakértelem bemenete nem megfelelő típusú. | "A szükséges szakértelem-bevitel nem a várt típusú `String`. Név: `text`, forrás: `/document/merged_content`. "  "A szükséges szakértelem-bevitel nem a várt formátumú. Név: `text`, forrás: `/document/merged_content`. "  "Nem lehet megismételni a nem tömb `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`."  "Nem lehet kijelölni `0` nem tömb `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`" | Bizonyos készségek várhatóan adott típusok bemeneteit számítják fel, például az [érzelmi képességek](cognitive-search-skill-sentiment.md) `text` karakterláncnak számítanak. Ha a bemenet nem sztring értéket ad meg, akkor a rendszer nem hajtja végre, és nem hoz létre kimenetet. Győződjön meg arról, hogy az adatkészletben a bemeneti értékek egységesek a típusban, vagy használjon [egyéni webes API-képességet](cognitive-search-custom-skill-web-api.md) a bemenet előfeldolgozásához. Ha a képességet egy tömbön keresztül ismétli meg, ellenőrizze a szakértelem kontextusát, és a megfelelő pozícióban `*`. A környezetnek és a bemeneti forrásnak általában a tömbök `*` kell végződnie. |
| Hiányzik a szaktudás bemenete | "Hiányzik a szükséges képzettségi bevitel. Név: `text`, forrás: `/document/merged_content`"" hiányzó érték `/document/normalized_images/0/imageTags`. "  "Nem lehet kijelölni `0`t a (z) `0``/document/pages` hosszúságú tömbben." | Ha az összes dokumentum ezt a figyelmeztetést kapja, valószínűleg van egy elírás a bemeneti elérési utakon, és az elérési úton ellenőrizze, hogy az elérési útban van-e a tulajdonság neve, a további vagy a hiányzó `*`. Ellenőrizze, hogy az adatforrásból származó dokumentumok biztosítják-e a szükséges adatokat. |
| A képzettségi nyelvi kód bemenete érvénytelen. | A skill input `languageCode` a következő nyelvi kódokat `X,Y,Z`, amelyek közül legalább egy érvénytelen. | További [Részletek:](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Figyelmeztetés: a (z) "languageCode" képzettségi bemenethez a következő nyelvi kódok szerepelnek: "X, Y, Z", amelyek közül legalább az egyik érvénytelen.
Egy vagy több alsóbb rétegbeli képesség nem kötelező `languageCode`ba átadott érték nem támogatott. Ez akkor fordulhat elő, ha a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) kimenetét átadja a következő szakismereteknek, a kimenet pedig több nyelvből áll, mint amennyit az adott alsóbb rétegbeli készségek támogatnak.

Ha tudja, hogy az adatkészlete egy adott nyelven van, távolítsa el a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) és a `languageCode` skill inputot, és használja a skill `defaultLanguageCode` skill paramétert, feltéve, hogy az adott szakértelem nyelve támogatott.

Ha tudja, hogy az adathalmaz több nyelvet tartalmaz, ezért a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) és a `languageCode` bemenetre van szüksége, vegyen fel egy [ConditionalSkill](cognitive-search-skill-conditional.md) a szöveg kiszűréséhez olyan nyelvekkel, amelyek nem támogatottak, mielőtt átadná a szöveget az alsóbb rétegbeli képességbe.  Íme egy példa arra, hogy ez hogyan néz ki a EntityRecognitionSkill:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

Íme néhány hivatkozás a jelenleg támogatott nyelvekhez minden olyan szaktudáshoz, amely a következő hibaüzenetet eredményezheti:
* [Text Analytics támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (a [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), a [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)és a [SentimentSkill](cognitive-search-skill-sentiment.md)esetében)
* [Fordító által támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (a [szöveg TranslationSkill](cognitive-search-skill-text-translation.md))
* [Szöveges SplitSkill](cognitive-search-skill-textsplit.md) Támogatott nyelvek: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Figyelmeztetés: a szaktudás bemenete csonkolt
A kognitív képességek az egyszerre elemezhető szöveg hosszát korlátozzák. Ha ezen képességek szövegének bevitele meghaladja ezt a korlátot, a rendszer csonkolja a szöveget, hogy megfeleljen a határértéknek, majd végrehajtja a dúsítást a csonkolt szövegen. Ez azt jelenti, hogy a rendszer végrehajtja a képességet, de az összes adatát nem.

Az alábbi példában szereplő LanguageDetectionSkill a `'text'` beviteli mező kiválthatja ezt a figyelmeztetést, ha az meghaladja a karakteres korlátot. A képzettséggel kapcsolatos [dokumentációban](cognitive-search-predefined-skills.md)megtalálja a képességek bemeneti korlátait.

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

Ha biztosítani szeretné, hogy az összes szöveg elemzése megtörténjen, érdemes lehet a [felosztott képességet](cognitive-search-skill-textsplit.md)használni.

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Figyelmeztetés: a webes API skill-válasza figyelmeztetéseket tartalmaz
Az indexelő egy képességet tudott futtatni a készségkészlet, de a webes API-kérelemtől kapott válasz a végrehajtás során figyelmeztetéseket jelzett. Tekintse át a figyelmeztetéseket, és Ismerje meg, hogy milyen hatással van az adatai, és hogy van-e szükség beavatkozásra.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Figyelmeztetés: az indexelő jelenlegi konfigurációja nem támogatja a növekményes előrehaladást

Ez a figyelmeztetés csak Cosmos DB adatforrások esetén fordul elő.

Az indexelés során fellépő növekményes előrehaladás biztosítja, hogy ha az indexelő végrehajtását átmeneti hibák vagy végrehajtási időkorlát miatt megszakítja, az indexelő elvégezheti a következő futása után, hogy a teljes gyűjteményt ne kelljen újból indexelni. Ez különösen fontos a nagyméretű gyűjtemények indexelése során.

A befejezetlen indexelési feladatok folytatásának lehetősége a `_ts` oszlop által megrendelt dokumentumok alapján történik. Az indexelő az időbélyeg használatával határozza meg, hogy melyik dokumentumot kell felvenni a következőre. Ha a `_ts` oszlop hiányzik, vagy az indexelő nem tudja megállapítani, hogy az adott egyéni lekérdezést rendeli-e meg, az indexelő elindul, és látni fogja ezt a figyelmeztetést.

Felülbírálhatja ezt a viselkedést, és engedélyezheti a növekményes előrehaladást, és letilthatja ezt a figyelmeztetést a `assumeOrderByHighWatermarkColumn` konfigurációs tulajdonság használatával.

További információ: [növekményes folyamat és egyéni lekérdezések](search-howto-index-cosmosdb.md#IncrementalProgress).

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Figyelmeztetés: a kivetítés során egyes adatvesztések elvesznek. A (Z) "Y" tábla "X" sora túl hosszú karakterlánc-tulajdonságot tartalmaz.

A [Table Storage szolgáltatás](https://azure.microsoft.com/services/storage/tables) korlátozásokkal rendelkezik, hogy az [entitások mekkora tulajdonságai](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) lehetnek. A karakterláncok legfeljebb 32 000 karakterből állhatnak. Ha az 32 000 karakternél hosszabb karakterlánc-tulajdonságú sorok vannak kialakítva, csak az első 32 000 karaktert őrzi meg a rendszer. A probléma megkerüléséhez Kerülje a sorok kivetítését 32 000 karakternél hosszabb karakterlánc-tulajdonságokkal.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Figyelmeztetés: a kinyert szöveg X karakterre csonkítva
Az indexek megszabják, hogy az egyes dokumentumokból milyen mennyiségű szöveget lehet kinyerni. Ez a korlát a következő árképzési szinttől függ: az ingyenes szint, a 64 000 az alapszintű, a 4 000 000 a standard, a standard S2 és a standard S3 szint esetében a 32 000. A csonkolt szöveg nem lesz indexelve. A figyelmeztetés elkerüléséhez próbálkozzon szét a dokumentumok nagy mennyiségű szöveggel több, kisebb dokumentumba való bontásával. 

További információ: [Indexelő korlátai](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Figyelmeztetés: a (z) "X" kimeneti mező nem képezhető le a keresési indexre
A nem létező/null értékű adatokat hivatkozó kimeneti mezők leképezései minden dokumentumhoz figyelmeztetést hoznak létre, és üres index mezőt eredményeznek. A probléma megoldásához ellenőrizze a kimeneti mező leképezési forrásának elérési útját a lehetséges elírásokhoz, vagy állítson be egy alapértelmezett értéket a [feltételes képesség](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)használatával.

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Figyelmeztetés: az adatváltozás-észlelési házirend az "X" kulcs oszlop használatára van konfigurálva.
Az [adatváltozás-észlelési szabályzatok](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) konkrét követelményekkel rendelkeznek a változás észleléséhez használt oszlopokra vonatkozóan. Ezen követelmények egyike az, hogy ez az oszlop minden alkalommal frissül, amikor módosul a forrás eleme. Egy másik követelmény, hogy az oszlop új értéke nagyobb, mint az előző érték. A kulcs oszlopai nem felelnek meg ennek a követelménynek, mert nem változnak minden frissítésnél. A probléma megkerüléséhez válasszon egy másik oszlopot a Change észlelési házirendhez.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Figyelmeztetés: úgy tűnik, hogy a dokumentum szövege UTF-16 kódolású, de hiányzik egy byte Order jel.

Az [Indexelő elemzési módjának](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) tudnia kell, hogy a szöveg hogyan legyen kódolva az elemzés előtt. A kódolási szövegek két leggyakoribb módja az UTF-16 és az UTF-8. Az UTF-8 egy változó hosszúságú kódolás, amelyben minden karakter 1 bájt és 4 bájt hosszú lehet. Az UTF-16 egy rögzített hosszúságú kódolás, amelyben minden karakter 2 bájt hosszúságú. Az UTF-16 két különböző változatot tartalmaz: "big endian" és "Little endian". A szöveges kódolást egy "byte Order Mark", egy bájtos sorozat határozza meg a szöveg előtt.

| Encoding | Byte Order Mark |
| --- | --- |
| UTF-16 big endian | 0xFE 0xFF |
| UTF-16 kis endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Ha nincs megadva a bájtok sorrendje, a rendszer az UTF-8 kódolású szöveget feltételezi.

A figyelmeztetés megkerüléséhez határozza meg, hogy mi a blob szöveges kódolása, és adja hozzá a megfelelő bájtos megrendelési jelet.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Figyelmeztetés: Cosmos DB "X" gyűjteménynek van egy lusta indexelési házirendje. Egyes adatvesztések elveszhetnek

A [lusta](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) indexelési házirendekkel rendelkező gyűjteményeket nem lehet következetesen lekérdezni, így az indexelő adatai hiányoznak. A figyelmeztetés megkerüléséhez módosítsa az indexelési házirendet Konzisztensre.
