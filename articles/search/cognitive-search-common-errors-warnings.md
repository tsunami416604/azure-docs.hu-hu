---
title: Indexelőkkel kapcsolatos hibák és figyelmeztetések
titleSuffix: Azure Cognitive Search
description: Ez a cikk az Azure Cognitive Search a közös hibákés figyelmeztetések rekedése és megoldása.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ed10e998ea05b6687190b1f87095f8bc28265905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086611"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Gyakori indexelőhibák és figyelmeztetések elhárítása az Azure Cognitive Search szolgáltatásban

Ez a cikk az Azure Cognitive Search indexelése és a ai-bővítés során előforduló gyakori hibákés figyelmeztetések információkat és megoldásokat tartalmaz.

Az indexelés akkor áll le, ha a hibaszám meghaladja a ["maxFailedItems" elemet.](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures) 

Ha azt szeretné, hogy az indexelők figyelmen kívül hagyják `maxFailedItems` `maxFailedItemsPerBatch` ezeket a hibákat (és ugorják át a "sikertelen dokumentumokat"), fontolja meg az [itt](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)leírtak szerint történő frissítés.

> [!NOTE]
> Minden sikertelen dokumentum a dokumentumkulcsával együtt (ha elérhető) hibaként jelenik meg az indexelő végrehajtási állapotában. Használhatja az [index API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) manuálisan feltölteni a dokumentumokat egy későbbi időpontban, ha beállította az indexelő, hogy tolerálja a hibákat.

A cikkben található hibainformációk segíthetnek a hibák megoldásában, lehetővé téve az indexelés folytatását.

A figyelmeztetések nem állnak le az indexeléssel, de olyan feltételeket jeleznek, amelyek váratlan következményekkel járhatnak. Az, hogy végrehajt-e műveletet, az adatoktól és a forgatókönyvtől függ.

Az API-verziótól `2019-05-06`kezdve az elemszintű indexelő hibák és figyelmeztetések strukturáltak, hogy nagyobb egyértelműséget biztosítsanak az okok és a következő lépések körül. A következő tulajdonságokat tartalmazzák:

| Tulajdonság | Leírás | Példa |
| --- | --- | --- |
| kulcs | A hiba vagy figyelmeztetés által érintett dokumentum dokumentumazonosítója. | https:\//coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| név | A művelet neve, amely leírja, hogy hol történt a hiba vagy figyelmeztetés. Ezt a következő struktúra hozza létre: [kategória]. [alkategória]. [resourceType]. [resourceName] | DocumentExtraction.azureblob.myBlobContainerName Enrichment.WebApiSkill.mySkillName Projection.SearchIndex.OutputFieldMapping.myOutputFieldName Projection.SearchIndex.MergeOrUpload.myIndexName Projection.KnowledgeStore.Table.myTableName |
| message | A hiba vagy figyelmeztetés magas szintű leírása. | Nem sikerült végrehajtani a szakértelem, mert a Web Api-kérelem nem sikerült. |
| Részletek | További részleteket, amelyek hasznosak lehetnek a probléma diagnosztizálása, például a WebApi-válasz, ha egy egyéni szakértelem végrehajtása sikertelen. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 forrás,`2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` Func ... a többi verem nyom... |
| dokumentációLink | A megfelelő dokumentációra mutató hivatkozás, amely részletes információkat tartalmaz a hibakereséshez és a probléma megoldásához. Ez a hivatkozás gyakran az oldal alábbi szakaszainak egyikére mutat. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"/>

## <a name="error-could-not-read-document"></a>Hiba: Nem olvasható a dokumentum

Az Indexelő nem tudta olvasni a dokumentumot az adatforrásból. Ez a következők miatt fordulhat elő:

| Ok | Részletek/Példa | Megoldás: |
| --- | --- | --- |
| Inkonzisztens mezőtípusok a különböző dokumentumokban | "Az érték típusa nem egyezik az oszloptípussal. Nem lehet `'{47.6,-122.1}'` tárolni a szerzők oszlopában.  A várt típus JArray."  "Hiba az nvarchar adattípus lebegésre történő konvertálása közben."  "Az átalakítás nem sikerült, amikor a nvarchar értéket int típusúra konvertálta."  "A számtani túlcsordulási hiba a kifejezés adattípussá konvertálása int." | Győződjön meg arról, hogy az egyes mezőtípusok azonosak a különböző dokumentumokban. Ha például az `'startTime'` első dokumentummező datetime, a második dokumentumban pedig karakterlánc, akkor ez a hiba ellesz találva. |
| hibák az adatforrás alapjául szolgáló szolgáltatásból | (innen: Cosmos DB)`{"Errors":["Request rate is large"]}` | Ellenőrizze a tárolási példányt, hogy megbizonyosodjon arról, hogy kifogástalan állapotú. Előfordulhat, hogy módosítania kell a skálázást/particionálást. |
| átmeneti problémák | Átviteli szintű hiba történt a kiszolgálótól érkező eredmények fogadásakor. (szolgáltató: TCP-szolgáltató, hiba: 0 - A távoli állomás kényszerítetten zárt le egy meglévő kapcsolatot | Időnként váratlan kapcsolódási problémák merülnek fel. Próbálja meg később újra futtatni a dokumentumot az indexelőn keresztül. |

<a name="could-not-extract-document-content"/>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>Hiba: Nem lehetett kinyerni a tartalmat vagy a metaadatokat a dokumentumból
A Blob adatforrással rendelkező indexelő nem tudta kinyerni a tartalmat vagy a metaadatokat a dokumentumból (például egy PDF-fájlból). Ez a következők miatt fordulhat elő:

| Ok | Részletek/Példa | Megoldás: |
| --- | --- | --- |
| blob túllépte a méretkorlátot | A `'150441598'` dokumentum bájt, amely meghaladja `'134217728'` az aktuális szolgáltatási szint dokumentumkinyeréséhez szükséges maximális bájtok bájtjait. | [blob indexelési hibái](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| A blob nem támogatott tartalomtípussal rendelkezik | A dokumentum nem támogatott tartalomtípussal rendelkezik`'image/png'` | [blob indexelési hibái](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob titkosított | A dokumentumot nem lehetett feldolgozni – lehet, hogy titkosítva van, vagy jelszóval védett. | Kihagyhatja a blobot a [blobbeállításokkal.](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| átmeneti problémák | "Hiba a blob feldolgozása: A kérés megszakadt: A kérés megszakadt." "A dokumentum a feldolgozás során időzést ért el." | Időnként váratlan kapcsolódási problémák merülnek fel. Próbálja meg később újra futtatni a dokumentumot az indexelőn keresztül. |

<a name="could-not-parse-document"/>

## <a name="error-could-not-parse-document"></a>Hiba: Nem elemezhető a dokumentum
Az indexelő az adatforrásból olvasta be a dokumentumot, de hiba történt a dokumentum tartalmának a megadott mezőleképezési sémává történő konvertálása miatt. Ez a következők miatt fordulhat elő:

| Ok | Részletek/Példa | Megoldás: |
| --- | --- | --- |
| Hiányzik a dokumentumkulcs | A dokumentumkulcs nem hiányzik vagy nem lehet üres | Annak ellenőrzése, hogy minden dokumentum érvényes dokumentumkulcs-okkal rendelkezik-e |
| A dokumentumkulcs érvénytelen | A dokumentumkulcs nem lehet hosszabb 1024 karakternél | Módosítsa a dokumentumkulcsot az érvényesítési követelményeknek megfelelően. |
| Nem alkalmazható mezőleképezés egy mezőre | Nem lehet leképezési függvényt `'functionName'` alkalmazni a mezőre. `'fieldName'` A tömb nem lehet null értékű. Paraméter neve: bájt | Ellenőrizze az indexelőn definiált [mezőleképezéseket, és hasonlítsa](search-indexer-field-mappings.md) össze a sikertelen bizonylat megadott mezőjének adataival. Szükség lehet a mezőleképezések vagy a dokumentumadatok módosítására. |
| Nem olvasható a mezőértéke | Nem lehetett beolvasni `'fieldName'` az `'fieldIndex'`oszlop értékét az indexben. Átviteli szintű hiba történt a kiszolgálótól érkező eredmények fogadásakor. (szolgáltató: TCP-szolgáltató, hiba: 0 - A távoli állomás kényszerítetten zárt le egy meglévő kapcsolatot.) | Ezek a hibák általában az adatforrás alapjául szolgáló szolgáltatással kapcsolatos váratlan kapcsolódási problémák miatt jelentkeznek. Próbálja meg később újra futtatni a dokumentumot az indexelőn keresztül. |

<a name="could-not-execute-skill"/>

## <a name="error-could-not-execute-skill"></a>Hiba: Nem hajtható végre szakértelem
Indexelő nem sikerült futtatni egy készség a skillset.

| Ok | Részletek/Példa | Megoldás: |
| --- | --- | --- |
| Átmeneti kapcsolódási problémák | Átmeneti hiba történt. Próbálkozzon újra később. | Időnként váratlan kapcsolódási problémák merülnek fel. Próbálja meg később újra futtatni a dokumentumot az indexelőn keresztül. |
| Lehetséges termékhiba | Váratlan hiba történt. | Ez egy ismeretlen hibaosztályt jelez, és azt jelentheti, hogy termékhiba van. Kérjük, nyújtson be [támogatási jegyet,](https://ms.portal.azure.com/#create/Microsoft.Support) hogy segítséget kapjon. |
| Egy szakértelem hibát észlelt a végrehajtás során | (Szakértelem egyesítése) Egy vagy több eltolási érték érvénytelen, ezért nem elemezhető. A szöveg végére elemeket szúrt be | A probléma megoldásához használja a hibaüzenetben található információkat. Az ilyen típusú hiba megoldásához beavatkozásra van szükség. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"/>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>Hiba: Nem sikerült végrehajtani a szakértelem, mert a webes API-kérelem sikertelen volt
A szakértelem végrehajtása nem sikerült, mert a webes API-hívás nem sikerült. Ez a hibaosztály általában egyéni képességek használatba valónak ordibban történik, ebben az esetben a probléma megoldásához hibakeresésre van szükség az egyéni kód ban. Ha ehelyett a hiba egy beépített szakértelemből származik, a probléma megoldásához a hibaüzenetben tájékozódhat.

A probléma hibakeresése közben ügyeljen arra, hogy figyeljen a [szakértelem beviteli figyelmeztetéseire.](#warning-skill-input-was-invalid) Előfordulhat, hogy a webes API-végpont meghibásodik, mert az indexelő nem várt bemenetet ad át.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"/>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>Hiba: Nem hajtható végre szakértelem, mert a webes API-szakértelem válasza érvénytelen.
A szakértelem végrehajtása nem sikerült, mert a webes API-hívás érvénytelen választ adott. Ez a hibaosztály általában egyéni képességek használatba valónak ordibban történik, ebben az esetben a probléma megoldásához hibakeresésre van szükség az egyéni kód ban. Ha ehelyett a hiba egy beépített szakértelemből származik, kérjük, nyújtson be [támogatási jegyet](https://ms.portal.azure.com/#create/Microsoft.Support) a segítséghez.

<a name="skill-did-not-execute-within-the-time-limit"/>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>Hiba: A szakértelem nem hajtott végre a megadott határidőn belül
Két olyan eset van, amikor ez a hibaüzenet találkozhat, amelyek mindegyikét eltérően kell kezelni. Kérjük, kövesse az alábbi utasításokat attól függően, hogy milyen készség adta vissza ezt a hibát az Ön számára.

### <a name="built-in-cognitive-service-skills"></a>Beépített kognitív szolgáltatási ismeretek
A beépített kognitív képességek, például a nyelvfelismerés, az entitásfelismerés vagy az OCR számos részét egy Cognitive Service API-végpont támogatja. Néha átmeneti problémák vannak ezekkel a végpontokkal, és egy kérés időtúlodik. Átmeneti problémák esetén nincs más megoldás, mint várni, és újra próbálkozni. Ennek érdekében fontolja meg az indexelő ütemezés szerinti [futtatásának beállítását.](search-howto-schedule-indexers.md) Az ütemezett indexelés ott folytatja, ahol abbahagyta. Feltételezve, hogy az átmeneti problémák megoldódnak, indexelés és a kognitív szakértelem feldolgozása képesnek kell lennie arra, hogy továbbra is a következő ütemezett futtatás.

Ha továbbra is látja ezt a hibát ugyanazon a dokumentumon egy beépített kognitív képesség, kérjük, nyújtson be egy [támogatási jegyet,](https://ms.portal.azure.com/#create/Microsoft.Support) hogy segítséget kapjon, mivel ez nem várható.

### <a name="custom-skills"></a>Egyéni készségek
Ha időtúlírási hibába ütközik egy létrehozott egyéni szakértelemmel, néhány dologgal próbálkozhat. Először is, tekintse át az egyéni készség, és győződjön meg arról, hogy nem elakad egy végtelen ciklus, és hogy ez visszatér az eredmény következetesen. Miután megerősítette, hogy ez a helyzet, határozza meg, mi a végrehajtási idő a készség. Ha nem állított be `timeout` explicit értéket az egyéni szakértelem-definícióhoz, akkor az alapértelmezett `timeout` érték 30 másodperc. Ha 30 másodperc nem elég a szakértelem végrehajtásához, `timeout` magasabb értéket adhat meg az egyéni szakértelem-definícióhoz. Íme egy példa egy egyéni szakértelem-definícióra, ahol az időhosszabbítás 90 másodpercre van állítva:

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

A `timeout` paraméterhez beállítható maximális érték 230 másodperc.  Ha az egyéni szakértelem nem tudja következetesen végrehajtani 230 `batchSize` másodpercen belül, érdemes lehet csökkenteni az egyéni szakértelem, hogy kevesebb dokumentumot kell feldolgozni egy végrehajtáson belül.  Ha már beállította `batchSize` a 1,, akkor át kell írnia a készség, hogy képes legyen végrehajtani a 230 másodperc alatt, vagy más módon ossza fel több egyéni képességek, hogy a végrehajtási idő minden egyes egyéni szakértelem legfeljebb 230 másodperc. További információkért tekintse át az [egyéni szakértelem dokumentációját.](cognitive-search-custom-skill-web-api.md)

<a name="could-not-mergeorupload--delete-document-to-the-search-index"/>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>Hiba: Nem`MergeOrUpload`lehet ' | '`Delete`dokumentum a keresési indexhez

A dokumentum olvasása és feldolgozása történt, de az indexelő nem tudta hozzáadni a keresési indexhez. Ez a következők miatt fordulhat elő:

| Ok | Részletek/Példa | Megoldás: |
| --- | --- | --- |
| A mező túl nagy kifejezést tartalmaz | A dokumentumban lévő kifejezés nagyobb, mint a [32 KB-os korlát](search-limits-quotas-capacity.md#api-request-limits) | Ezt a korlátozást elkerülheti, ha biztosítja, hogy a mező nincs szűrhető, lapverhető vagy rendezhető ként konfigurálva.
| A dokumentum túl nagy az indexeléshez | Egy dokumentum nagyobb, mint a [maximális API-kérelemméret](search-limits-quotas-capacity.md#api-request-limits) | [Nagy adatkészletek indexelése](search-howto-large-index.md)
| A dokumentum túl sok objektumot tartalmaz a gyűjteményben | A dokumentumban lévő gyűjtemény meghaladja az összes összetett gyűjtemény maximális `'1000052'` `'4303'` [elemét](search-limits-quotas-capacity.md#index-limits) "A kulccsal rendelkező dokumentum gyűjteményekben (JSON-tömbökben) lévő objektumokat tartalmaz. A `'3000'` legtöbb objektum a teljes dokumentumban lehet gyűjteményekben. Távolítsa el az objektumokat a gyűjteményekből, és próbálja meg újra indexelni a dokumentumot." | Javasoljuk, hogy csökkentse a dokumentumban lévő összetett gyűjtemény méretét a korlát alá, és kerülje a magas tárolási kihasználtságot.
| Hiba a célindexhez való kapcsolódás (amely az újrapróbálkozások után is megmarad), mert a szolgáltatás más terhelés alatt áll, például lekérdezés vagy indexelés alatt. | Nem sikerült kapcsolatot létesíteni az index frissítésével. A keresési szolgáltatás nagy terhelés alatt áll. | [A keresési szolgáltatás felskálázása](search-capacity-planning.md)
| A keresési szolgáltatás javítás alatt áll a szolgáltatás frissítéséhez, vagy egy topológia-újrakonfigurálás közepén van. | Nem sikerült kapcsolatot létesíteni az index frissítésével. A keresési szolgáltatás jelenleg nem működik,/a keresési szolgáltatás átalakulóban van. | Szolgáltatás konfigurálása legalább 3 replikával az SLA dokumentációnkénti 99,9%-os rendelkezésre [állásérdekében](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Az alapul szolgáló számítási/hálózati erőforrás hibája (ritka) | Nem sikerült kapcsolatot létesíteni az index frissítésével. Ismeretlen hiba történt. | Konfigurálja úgy az indexelőket, hogy [ütemezés szerint fussanak,](search-howto-schedule-indexers.md) hogy egy sikertelen állapotból vegyenek fel.
| A célindexre irányuló indexelési kérelmet hálózati problémák miatt nem nyugtázták el egy időbeli elévülési időszakon belül. | Nem volt időben kapcsolatot létesíteni a keresési indexszel. | Konfigurálja úgy az indexelőket, hogy [ütemezés szerint fussanak,](search-howto-schedule-indexers.md) hogy egy sikertelen állapotból vegyenek fel. Ezenkívül próbálja meg csökkenteni az indexelő [kötegméretét,](https://docs.microsoft.com/rest/api/searchservice/create-indexer#parameters) ha ez a hibafeltétel továbbra is fennáll.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"/>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>Hiba: Nem lehet indexelni a dokumentumot, mert a dokumentum egyes adatai érvénytelenek voltak

A dokumentumot az indexelő elolvasta és feldolgozta, de az indexmezők és az indexelő által kinyert és feldolgozott adatok konfigurációjának eltérése miatt nem lehetett hozzáadni a keresési indexhez. Ez a következők miatt fordulhat elő:

| Ok | Részletek/Példa
| --- | ---
| Az indexelő által kinyert mező(k) adattípusa nem kompatibilis a megfelelő célindexmező adatmodelljével. | A "888" kulcsú dokumentum " adatmezője _"_"Data" ("Edm.String" típusú ' adatmezőnek érvénytelen értéke van. A várt típus a "Collection(Edm.String)" volt. |
| Nem sikerült kinyerni egy JSON-entitást egy karakterlánc-értékből. | JSON-objektumként nem sikerült elemezni az "Edm.String" típusú "Edm.String" értéket a "_mező_" adataiból.'T Coulds Hiba:'Érték elemzése után váratlan karakter történt: ''. Út "_elérési út',_ 1. |
| Nem sikerült json-entitások gyűjteményét kinyerni egy karakterlánc-értékből.  | Nem sikerült json tömbként elemezni az "Edm.String" típusú "Edm.String" értéket a "_data_' mezőből. Hiba:'Érték elemzése után váratlan karakter történt: ''. Path "[0]", 1. |
| Ismeretlen típust talált a forrásdokumentum. | Ismeretlen típus :_'ismeretlen_' nem indexelhető. |
| A forrásdokumentumban a földrajzi pontok nem kompatibilis jelölése volt. | A WKT POINT karakterlánc-konstansok nem támogatottak. Kérjük, használja GeoJson pont konstansok helyett |

Ezekben az esetekben tekintse meg a [Támogatott adattípusokat](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) és [az adattípus-leképezést az indexelők számára,](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) hogy az indexsémát megfelelően hozza létre, és megfelelő [indexelő mezőleképezéseket](search-indexer-field-mappings.md)állítson be. A hibaüzenet olyan részleteket tartalmaz, amelyek segíthetnek az eltérés forrásának nyomon követésében.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>Hiba: Az integrált változáskövetési házirend nem használható, mert a tábla összetett elsődleges kulccsal rendelkezik

Ez az SQL-táblákra vonatkozik, és általában akkor történik, ha a kulcs vagy összetett kulcsként van definiálva, vagy ha a tábla egyedi fürtözött indexet határozott meg (mint egy SQL indexben, nem pedig egy Azure Search-indexben). Ennek fő oka az, hogy a kulcsattribútum összetett elsődleges kulcsként módosul egyedi [fürtözött index](https://docs.microsoft.com/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15)esetén. Ebben az esetben győződjön meg arról, hogy az SQL-tábla nem rendelkezik egyedi fürtözött indexszel, vagy hogy a kulcsmezőt olyan mezőhöz rendeli hozzá, amely garantáltan nem rendelkezik ismétlődő értékekkel.

<a name="could-not-process-document-within-indexer-max-run-time"/>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>Hiba: Nem volt feldolgozni a dokumentumot az indexelő maximális futási idején belül

Ez a hiba akkor fordul elő, ha az indexelő nem tudja befejezni az adatforrásból származó egyetlen dokumentum feldolgozását az engedélyezett végrehajtási időn belül. [A maximális futási idő](search-limits-quotas-capacity.md#indexer-limits) rövidebb, ha skillsets használják. Ha ez a hiba történik, ha a maxFailedItems értéke nem 0, az indexelő megkerüli a dokumentumot a jövőbeli futtatásoksorán, hogy az indexelés előrehaladjon. Ha nem engedheti meg magának, hogy kihagyja a dokumentumot, vagy ha ezt a hibát következetesen látja, fontolja meg a dokumentumok kisebb dokumentumokra bontását, hogy részleges előrehaladást lehessen elérni egyetlen indexelő végrehajtáson belül.

<a name="could-not-project-document"/>

## <a name="error-could-not-project-document"></a>Hiba: Nem lehetett kivetíteni a dokumentumot

Ez a hiba akkor fordul elő, amikor az indexelő adatokat próbál [kivetíteni egy tudástárolóba,](knowledge-store-projection-overview.md) és hiba történt az erre irányuló kísérletünkben.  Ez a hiba lehet konzisztens és javítható, vagy lehet egy átmeneti hiba a vetítési kimeneti fogadó, amely előfordulhat, hogy várnia kell, és újra a megoldáshoz.  Az alábbiakban ismert hibaállapotokat és lehetséges megoldásokat ismertet.

| Ok | Részletek/Példa | Megoldás: |
| --- | --- | --- |
| Nem lehet frissíteni `'blobUri'` a vetítési blobot a tárolóban`'containerName'` |A megadott tároló nem létezik. | Az indexelő ellenőrzi, hogy a megadott tároló korábban már létre, és létrehozza, ha szükséges, de csak egyszer hajtja végre ezt az ellenőrzést indexelő futtatásonként. Ez a hiba azt jelenti, hogy valami törölte a tárolót a lépés után.  A hiba elhárításához próbálkozzon a következővel: hagyja békén a tárfiók adatait, várja meg, amíg az indexelő befejeződik, majd futtassa újra az indexelőt. |
| Nem lehet frissíteni `'blobUri'` a vetítési blobot a tárolóban`'containerName'` |Nem lehet adatokat írni az átviteli kapcsolatra: A távoli állomás kényszerítetten zárt le egy meglévő kapcsolatot. | Ez várhatóan egy átmeneti hiba az Azure Storage, és így meg kell oldani az indexelő újrafuttatásával. Ha következetesen találkozik ezzel a hibával, nyújtson be [egy támogatási jegyet,](https://ms.portal.azure.com/#create/Microsoft.Support) hogy tovább vizsgálhassa.  |
| Nem lehet `'projectionRow'` frissíteni a tábla sorát`'tableName'` | A kiszolgáló foglalt. | Ez várhatóan egy átmeneti hiba az Azure Storage, és így meg kell oldani az indexelő újrafuttatásával. Ha következetesen találkozik ezzel a hibával, nyújtson be [egy támogatási jegyet,](https://ms.portal.azure.com/#create/Microsoft.Support) hogy tovább vizsgálhassa.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"/>

## <a name="warning-skill-input-was-invalid"></a>Figyelmeztetés: A szakértelem bevitele érvénytelen volt
Hiányzott a szakértelem bemenete, nem megfelelő típusú vagy más módon érvénytelen. A figyelmeztető üzenet a következő hatást jelzi:
1) Nem lehet végrehajtani a készséget
2) Szakértelem végrehajtása, de nem várt eredményeket hozhat

Kognitív képességek szükséges bemenetek és opcionális bemenetek. Például a [kulcskifejezés kinyerési szakértelem](cognitive-search-skill-keyphrases.md) két szükséges bemenettel rendelkezik `text`, `languageCode`és nem választható bemenetekkel. Egyéni szakértelem bemenetek mind választható bemenetek.

Ha valamelyik szükséges bemenet hiányzik, vagy ha a bemenet nem a megfelelő típusú, a szakértelem kimarad, és figyelmeztetést generál. A kihagyott szakértelem nem hoz létre kimeneteket, így ha más szakértelem használja a kihagyott szakértelem kimeneteit, további figyelmeztetéseket generálhatnak.

Ha egy nem kötelező bemenet hiányzik, a szakértelem továbbra is fut, de a hiányzó bemenet miatt váratlan kimenetet okozhat.

Ez a figyelmeztetés mindkét esetben az adatok alakja miatt várható. Ha például van egy dokumentuma, amely a `firstName` `middleName`mezőkkel `lastName`rendelkező személyekadatait tartalmazza, és a `middleName`, előfordulhat, hogy vannak olyan dokumentumok, amelyekhez nem szükséges a. Ha adja `middleName` át, mint egy bemeneti egy szakértelem a folyamatban, akkor várható, hogy ez a szakértelem bemeneti előfordulhat, hogy hiányzik az idő egy részét. Ki kell értékelnie az adatokat és a forgatókönyvet annak meghatározásához, hogy szükség van-e valamilyen műveletre a figyelmeztetés eredményeként.

Ha hiányzó bemenet esetén alapértelmezett értéket szeretne megadni, a [feltételes szakértelem](cognitive-search-skill-conditional.md) segítségével létrehozhat egy alapértelmezett értéket, majd használhatja a feltételes [szakértelem](cognitive-search-skill-conditional.md) kimenetét szakértelem-bemenetként.


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

| Ok | Részletek/Példa | Megoldás: |
| --- | --- | --- |
| A szakértelem bevitele nem megfelelő típusú | "A szükséges szakértelem-bevitel nem `String`a várt típusú volt. Név: `text`, `/document/merged_content`Forrás: "  "A szükséges szakértelem-bevitel nem a várt formátumú volt. Név: `text`, `/document/merged_content`Forrás: "  "Nem lehet nem tömbösítésre itatni." `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`  "Nem lehet `0` kijelölni `/document/normalized_images/0/imageCelebrities/0/detail/celebrities`a nem tömbös" | Bizonyos képességek bizonyos típusú bemeneteket várnak el, például [a Hangulati szakértelem](cognitive-search-skill-sentiment.md) karakterláncot vár. `text` Ha a bemenet nem karakterlánc-értéket ad meg, akkor a szakértelem nem hajtható végre, és nem hoz létre kimeneteket. Győződjön meg arról, hogy az adatkészlet bemeneti értékek egységes típusú, vagy egy [egyéni webes API-szakértelem](cognitive-search-custom-skill-web-api.md) a bemeneti folyamat előfeldolgozásához. Ha a szakértelem egy tömbön keresztül iterálja a képzettséget, ellenőrizze a képzettségi környezetet és a bemeneti beállításokat `*` a megfelelő pozíciókban. Általában mind a környezet, mind `*` a bemeneti forrás nak tömbökhöz kell érnie. |
| Hiányzik a szakértelem bevitele | "Hiányzik a szükséges szakértelem-bevitel. Név: `text`, `/document/merged_content`Forrás: " `/document/normalized_images/0/imageTags`"Hiányzó érték"  "Nem lehet `0` kijelölni `/document/pages` `0`a tömb hosszát." | Ha az összes dokumentum megkapja ezt a figyelmeztetést, valószínűleg elírás van a beviteli útvonalakban, `*` és ellenőrizze a tulajdonság névházát, az extra vagy hiányzik az elérési úton, és győződjön meg arról, hogy az adatforrásból származó dokumentumok biztosítják a szükséges bemeneteket. |
| A képzettségi nyelv kódbemenete érvénytelen | A `languageCode` szakértelem-bevitel a `X,Y,Z`következő nyelvkódokkal rendelkezik, amelyek közül legalább az egyik érvénytelen. | További részletek [alább](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"/>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>Figyelmeztetés: A "languageCode" készségbevitel a következő "X,Y,Z" nyelvkóddal rendelkezik, amelyek közül legalább az egyik érvénytelen.
Egy vagy több, egy alsóbb rétegbeli szakértelem nem kötelező `languageCode` bemenetének átadott értékek nem támogatottak. Ez akkor fordulhat elő, ha a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) kimenetét továbbadja a későbbi képességeknek, és a kimenet több nyelvből áll, mint amennyit ezek az alsóbb rétegbeli készségek támogatnak.

Ha tudja, hogy az adatkészlet egy nyelven, távolítsa el a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) és a `languageCode` szakértelem bemeneti és használja a szakértelem paraméter, hogy a `defaultLanguageCode` szakértelem helyett, feltéve, hogy a nyelv támogatott az adott szakértelem.

Ha tudja, hogy az adatkészlet több nyelvet tartalmaz, és `languageCode` így szüksége van a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) és a bemenet, fontolja meg egy [ConditionalSkill](cognitive-search-skill-conditional.md) kiszűrni a szöveget olyan nyelvekkel, amelyek nem támogatottak, mielőtt a szöveget az alsóbb rétegbeli szakértelem.  Íme egy példa arra, hogy ez hogyan nézhet ki az EntityRecognitionSkill esetében:

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

Íme néhány hivatkozás a jelenleg támogatott nyelvekhez az egyes készségekhez, amelyek ezt a hibaüzenetet eredményezhetik:
* [Szövegelemzés által támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages) (a [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md), [SentimentSkill](cognitive-search-skill-sentiment.md)és [PIIDetectionSkill esetében)](cognitive-search-skill-pii-detection.md)
* [Fordító által támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/translator/language-support) (a [Text TranslationSkill](cognitive-search-skill-text-translation.md)esetében)
* [Szöveg SplitSkill](cognitive-search-skill-textsplit.md) Támogatott nyelvek:`da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"/>

## <a name="warning-skill-input-was-truncated"></a>Figyelmeztetés: A szakértelem bemenete csonkolva lett
A kognitív képességek a szöveg egyszerre elemezhető hosszára vannak korlátozva. Ha ezeknek a képességeknek a szövegbevitele túllépi ezt a korlátot, akkor csonkoljuk a szöveget, hogy megfeleljen a korlátnak, majd végrehajtjuk a dúsítást a csonkított szövegen. Ez azt jelenti, hogy a szakértelem végrehajtása, de nem az összes adatot.

Az alábbi LanguageDetectionSkill példában a beviteli mező kiválthatja ezt a `'text'` figyelmeztetést, ha túllépte a karakterkorlátot. A szakértelem beviteli korlátait a [készségdokumentációban](cognitive-search-predefined-skills.md)találja.

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

Ha biztosítani szeretné, hogy az összes szöveg et elemezzék, fontolja meg a [Felosztás igézet](cognitive-search-skill-textsplit.md)használatát.

<a name="web-api-skill-response-contains-warnings"/>

## <a name="warning-web-api-skill-response-contains-warnings"></a>Figyelmeztetés: A webes API szakértelemre adott válasza figyelmeztetéseket tartalmaz
Indexelő képes volt futtatni egy készség a skillset, de a válasz a webes API-kérelem jelezte, hogy voltak figyelmeztetések a végrehajtás során. Tekintse át a figyelmeztetéseket, hogy tisztában legyen az adatok érintettségével, valamint hogy szükség van-e műveletre.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"/>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>Figyelmeztetés: Az aktuális indexelő konfiguráció nem támogatja a növekményes folyamatot

Ez a figyelmeztetés csak cosmos DB adatforrások esetén fordul elő.

Az indexelés során a növekményes folyamat biztosítja, hogy ha az indexelő végrehajtását átmeneti hibák vagy végrehajtási időkorlát szakítja meg, az indexelő a következő futtatáskor folytathatja, ahelyett, hogy újra indexelnie kellene a teljes gyűjteményt a semmiből. Ez különösen fontos nagy gyűjtemények indexelésekén.

A befejezetlen indexelési feladat folytatásának lehetősége az `_ts` oszlop által rendezett dokumentumokon alapul. Az indexelő az időbélyeg segítségével határozza meg, hogy melyik dokumentumot vegye fel a következő. Ha `_ts` az oszlop hiányzik, vagy ha az indexelő nem tudja megállapítani, hogy egy egyéni lekérdezés tana, az indexelő kezdődik az elején, és látni fogja ezt a figyelmeztetést.

Ezt a viselkedést felül lehet bírni, lehetővé téve a `assumeOrderByHighWatermarkColumn` növekményes folyamatot, és letiltva ezt a figyelmeztetést a konfigurációs tulajdonság használatával.

További információt a [Növekményes folyamat és egyéni lekérdezések című témakörben talál.](search-howto-index-cosmosdb.md#IncrementalProgress)

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"/>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>Figyelmeztetés: Néhány adat elveszett a kivetítés során. Az "Y" tábla "X" sorában a "Z" karakterlánc-tulajdonság túl hosszú volt.

A [Table Storage szolgáltatás](https://azure.microsoft.com/services/storage/tables) korlátozza, hogy milyen nagy [entitástulajdonságok](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model#property-types) lehetnek. A karakterláncok legkülönújban legelhetőbek lehetnek 32 000 karakterrel. Ha egy 32 000 karakternél hosszabb karakterlánctulajdonsággal rendelkező sor kivetítése történik, csak az első 32 000 karakter marad meg. A probléma kerülő megoldásához kerülje a 32 000 karakternél hosszabb karakterlánctulajdonságokkal rendelkező sorok kivetítését.

<a name="truncated-extracted-text-to-x-characters"/>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>Figyelmeztetés: Csonkított kibontott szöveg X karakterekre
Az indexelők korlátozzák, hogy mennyi szöveg nyerhető ki egy dokumentumból. Ez a korlát a tarifacsomagtól függ: 32 000 karakter az ingyenes szinthez, 64 000 alapszintű, 4 millió standard, 8 millió standard S2 és 16 millió standard S3. A csonkolt szöveg nem lesz indexelve. A figyelmeztetés elkerülése érdekében szakítsa meg a nagy mennyiségű szöveget tartalmazó dokumentumokat több, kisebb dokumentumra. 

További információ: [Indexer limits](search-limits-quotas-capacity.md#indexer-limits).

<a name="could-not-map-output-field-x-to-search-index"/>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>Figyelmeztetés: Nem volt leképezve az "X" kimeneti mezőt az index kereséséhez
A nem létező/null adatokra hivatkozó kimeneti mezőleképezések minden dokumentumhoz figyelmeztetést adnak, és üres indexmezőt eredményeznek. A probléma kerülő megoldásához ellenőrizze újra a kimeneti mező leképezési forrásútvonalait a lehetséges elírások miatt, vagy állítson be alapértelmezett értéket a [Feltételes szakértelem](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)használatával.

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"/>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>Figyelmeztetés: Az adatváltozás-észlelési házirend az "X" kulcsoszlop használatára van konfigurálva.
[Az adatváltozás-észlelési házirendek](https://docs.microsoft.com/rest/api/searchservice/create-data-source#data-change-detection-policies) speciális követelményekkel rendelkeznek a változások észlelésére használt oszlopokra vonatkozóan. Az egyik követelmény az, hogy ez az oszlop minden alkalommal frissül, amikor a forráscikk módosul. Egy másik követelmény, hogy az oszlop új értéke nagyobb, mint az előző érték. A kulcsoszlopok nem felelnek meg ennek a követelménynek, mert nem változnak minden frissítésnél. A probléma kerülő megoldásához válasszon egy másik oszlopot a változásészlelési házirendhez.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"/>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>Figyelmeztetés: A dokumentum szövege UTF-16 kódolásúnak tűnik, de hiányzik egy bájtrendelési jel

Az [indexelő elemzési módok](https://docs.microsoft.com/rest/api/searchservice/create-indexer#blob-configuration-parameters) tudnia kell, hogyan van kódolva a szöveg elemzés előtt. A szöveg kódolásának két leggyakoribb módja az UTF-16 és az UTF-8. Az UTF-8 egy változó hosszúságú kódolás, ahol minden karakter 1 és 4 bájt közötti hosszúságú. Az UTF-16 egy rögzített hosszúságú kódolás, ahol minden karakter 2 bájt hosszú. Az UTF-16-nak két különböző változata van: "nagy endian" és "kis endian". A szövegkódolást a szöveg előtti bájtrendelési jel határozza meg.

| Encoding | Bájtrendelés-jel |
| --- | --- |
| UTF-16 Nagy Endian | 0xFE 0xFF |
| UTF-16 Kis Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0xBB 0xBF |

Ha nincs jelen bájtrendelési jel, a program utf-8-ként kódolja a szöveget.

A figyelmeztetés kerülő megoldásához határozza meg, hogy mi a blob szövegkódolása, és adja hozzá a megfelelő bájtrendelési jelet.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"/>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>Figyelmeztetés: Cosmos DB gyűjtemény "X" egy lusta indexelési házirend. Egyes adatok elveszhetnek

[A lusta](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) indexelési szabályzatokkal rendelkező gyűjtemények nem kérdezhetők le következetesen, ami azt eredményezi, hogy az indexelő ből hiányoznak az adatok. A figyelmeztetés kerülő megoldásához módosítsa az indexelési szabályzatot Konzisztens re.
