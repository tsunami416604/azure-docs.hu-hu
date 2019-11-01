---
title: Gyakori hibák és figyelmeztetések – Azure Search
description: Ez a cikk olyan gyakori hibákkal és figyelmeztetésekkel kapcsolatos információkat és megoldásokat tartalmaz, amelyeket az AI-gazdagítás során a Azure Searchban lehet megtapasztalni.
services: search
manager: heidist
author: amotley
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: abmotley
ms.openlocfilehash: 6455ac9dbe0933f6d46d1137e0a19dcc388d8c80
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73243041"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Gyakori hibák és figyelmeztetések a mesterséges intelligencia-bővítési folyamatról Azure Search

Ez a cikk olyan gyakori hibákkal és figyelmeztetésekkel kapcsolatos információkat és megoldásokat tartalmaz, amelyeket az AI-gazdagítás során a Azure Searchban lehet megtapasztalni.

## <a name="errors"></a>Hibák
Az indexelés leáll, ha a hibák száma meghaladja a ["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Ha azt szeretné, hogy az indexelő figyelmen kívül hagyják ezeket a hibákat (és ugorjon a "sikertelen dokumentumok" elemre), akkor érdemes frissíteni a `maxFailedItems` és `maxFailedItemsPerBatch` az [itt](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)leírtak szerint.

> [!NOTE]
> Minden sikertelen dokumentum, valamint a dokumentum kulcsa (ha elérhető) az indexelő végrehajtási állapotában hibát jelez. Az [index API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) használatával manuálisan is feltöltheti a dokumentumokat egy későbbi időpontban, ha úgy állította be az indexelő, hogy eltűri a hibákat.

A következő fejezetek segíthetnek a hibák megoldásában, így az indexelés a folytatáshoz is használható.

### <a name="could-not-read-document"></a>A dokumentum nem olvasható
Az indexelő nem tudta olvasni a dokumentumot az adatforrásból. Ez az alábbiak miatt fordulhat elő:

| Ok | Példa | Műveletek |
| --- | --- | --- |
| inkonzisztens mezők típusai különböző dokumentumokban | Az érték típusa nem egyezik az oszlop típusával. Nem sikerült tárolni a `'{47.6,-122.1}'` a szerzők oszlopban.  A várt típus: JArray. | Ügyeljen arra, hogy az egyes mezők típusa azonos legyen a különböző dokumentumok között. Ha például az első dokumentum `'startTime'` a dátum DateTime, és a második dokumentumban ez egy karakterlánc, akkor ez a hiba fog megjelenni. |
| hibák az adatforrás mögöttes szolgáltatásában | (Cosmos DB) `{"Errors":["Request rate is large"]}` | Ellenőrizze, hogy a tárolási példány kifogástalan állapotú-e. Előfordulhat, hogy módosítania kell a skálázást/particionálást. |
| átmeneti problémák | Átviteli szintű hiba történt a kiszolgáló eredményeinek fogadása során. (szolgáltató: TCP-szolgáltató, hiba: 0 – a távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat | Esetenként váratlan kapcsolódási problémák léptek fel. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

### <a name="could-not-extract-document-content"></a>Nem lehetett kibontani a dokumentum tartalmát
A blob-adatforrással rendelkező indexelő nem tudta kinyerni a tartalmat a dokumentumból (például egy PDF-fájlt). Ez az alábbiak miatt fordulhat elő:

| Ok | Példa | Műveletek |
| --- | --- | --- |
| a blob mérete meghaladja a méretkorlátot | A dokumentum `'150441598'` bájt, ami meghaladja a jelenlegi szolgáltatási szinten a dokumentumok kinyeréséhez szükséges maximális méretet `'134217728'` bájtban. | [BLOB-indexelési hibák](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| a blob nem támogatott tartalomtípust tartalmaz | A dokumentum tartalma nem támogatott `'image/png'` | [BLOB-indexelési hibák](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| a blob titkosított | A dokumentumot nem lehetett feldolgozni – titkosított vagy jelszóval védett. | [BLOB beállításai](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| átmeneti problémák | Hiba történt a blob feldolgozása közben: a kérést megszakították: a kérést megszakították. | Esetenként váratlan kapcsolódási problémák léptek fel. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

### <a name="could-not-parse-document"></a>Nem sikerült elemezni a dokumentumot
Az indexelő beolvassa a dokumentumot az adatforrásból, de hiba történt a dokumentum tartalmának a megadott mező-hozzárendelési sémába való konvertálása során. Ez az alábbiak miatt fordulhat elő:

| Ok | Példa | Műveletek |
| --- | --- | --- |
| A dokumentum kulcsa hiányzik | A dokumentum kulcsa nem lehet hiányzik vagy üres. | Győződjön meg arról, hogy minden dokumentum rendelkezik érvényes dokumentum-kulcsokkal |
| A dokumentum kulcsa érvénytelen | A dokumentum kulcsa nem lehet hosszabb 1024 karakternél | Módosítsa a dokumentum kulcsát az érvényesítési követelmények teljesítéséhez. |
| A mező leképezése nem alkalmazható egy mezőre | A leképezési függvény nem alkalmazható a (`'fieldName'`) mezőre `'functionName'`. A tömb nem lehet null. Paraméter neve: bájtok | Ellenőrizze az indexelő által definiált [mező-hozzárendeléseket](search-indexer-field-mappings.md) , és hasonlítsa össze a hibás dokumentum megadott mezőjének értékével. Előfordulhat, hogy módosítania kell a mező-hozzárendeléseket vagy a dokumentum-adattípust. |
| Nem olvasható be a mező értéke | Nem lehetett beolvasni a (z) `'fieldName'` oszlop értékét a következő indexnél: `'fieldIndex'`. Átviteli szintű hiba történt a kiszolgáló eredményeinek fogadása során. (szolgáltató: TCP-szolgáltató, hiba: 0 – a távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat.) | Ezek a hibák általában az adatforrás mögöttes szolgáltatásával kapcsolatos váratlan kapcsolódási problémák miatt jelentkeznek. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

### <a name="could-not-execute-skill"></a>Nem hajtható végre a szaktudás
Az indexelő nem tudott futtatni egy képességet a készségkészlet.

| Ok | Példa | Műveletek |
| --- | --- | --- |
| Átmeneti kapcsolódási problémák | Átmeneti hiba történt. Később próbálja meg újra. | Esetenként váratlan kapcsolódási problémák léptek fel. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |
| Lehetséges termék hibája | Váratlan hiba történt. | Ez ismeretlen osztályt jelez, és a termék meghibásodását is jelentheti. Kérjen segítséget a [támogatási jegyen](https://ms.portal.azure.com/#create/Microsoft.Support) . |
| A rendszer hibát észlelt a végrehajtás során | (Egyesítési képességből) Egy vagy több eltolási érték érvénytelen, és nem elemezhető. Elemek beszúrása a szöveg végére | A probléma megoldásához használja a hibaüzenetben található információkat. Ilyen hiba esetén a megoldáshoz beavatkozás szükséges. |

### <a name="could-not-execute-skill-because-the-web-api-request-failed"></a>Nem sikerült végrehajtani a képességet, mert a webes API-kérelem sikertelen volt.
A szaktudás végrehajtása nem sikerült, mert a webes API-hívás sikertelen volt. Ez az osztály általában egyéni szaktudás használata esetén fordul elő, ebben az esetben a probléma megoldásához hibakeresést kell végeznie az Egyéni kódban. Ha ehelyett a hiba egy beépített képességből származik, a probléma megoldásához a hibaüzenetben tájékozódhat.

### <a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>A képesség nem hajtható végre, mert a webes API-ügyességi válasz érvénytelen.
A szaktudás végrehajtása nem sikerült, mert a webes API hívása érvénytelen választ adott vissza. Ez az osztály általában egyéni szaktudás használata esetén fordul elő, ebben az esetben a probléma megoldásához hibakeresést kell végeznie az Egyéni kódban. Ha ehelyett a hiba egy beépített képességből származik, akkor kérjen segítséget a [támogatási jegyen](https://ms.portal.azure.com/#create/Microsoft.Support) .

### <a name="skill-did-not-execute-within-the-time-limit"></a>A szakértelem nem volt végrehajtva az időkorláton belül
Ez a hibaüzenet két esetben fordulhat elő, amelyek mindegyikét másképp kell kezelni. Kövesse az alábbi utasításokat attól függően, hogy milyen képességgel tért vissza ez a hiba.

#### <a name="built-in-cognitive-service-skills"></a>Beépített kognitív szolgáltatásokkal kapcsolatos ismeretek
Számos beépített kognitív képesség, például a nyelvfelismerés, az entitások felismerése vagy az OCR, a kognitív szolgáltatás API-végpontja támogatja. Időnként átmeneti problémák léptek fel ezekkel a végpontokkal, és a kérés időtúllépést eredményez. Átmeneti problémák esetén nincs szükség jogorvoslatra, kivéve a várakozást, és próbálkozzon újra. Enyhítő megoldásként érdemes lehet úgy beállítani az indexelő, hogy [menetrend szerint fusson](search-howto-schedule-indexers.md). Az ütemezett indexelés megkeresi, hogy hol maradt. Az átmeneti problémák megoldása érdekében az indexelést és a kognitív képességek feldolgozását folytatni kell a következő ütemezett futtatáskor.

#### <a name="custom-skills"></a>Egyéni készségek
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

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>Nem lehetett "`MergeOrUpload`" | "`Delete`" dokumentum a keresési indexhez

A dokumentum olvasása és feldolgozása megtörtént, de az indexelő nem tudja felvenni a keresési indexbe. Ez az alábbiak miatt fordulhat elő:

| Ok | Példa | Műveletek |
| --- | --- | --- |
| A dokumentum egy kifejezése nagyobb az [32 KB-os korlátnál](search-limits-quotas-capacity.md#api-request-limits) | Egy mező túl nagy kifejezést tartalmaz | Ezt a korlátozást elkerülheti, ha úgy látja, hogy a mező nem szűrhető, sokoldalú vagy rendezhető.
| A dokumentum nagyobb, mint az [API-kérelmek maximális mérete](search-limits-quotas-capacity.md#api-request-limits) | A dokumentum túl nagy az indexeléshez | [Nagyméretű adathalmazok indexelése](search-howto-large-index.md)
| Hiba történt a célként megadott indexhez való kapcsolódáskor (amely az újrapróbálkozások után is fennmarad), mert a szolgáltatás más terhelés alá esik, például lekérdezés vagy indexelés. | Nem sikerült kapcsolatot létesíteni az index frissítésével. A Search szolgáltatás nagy terhelés alatt áll. | [A keresési szolgáltatás vertikális felskálázása](search-capacity-planning.md)
| A keresési szolgáltatás javítás alatt áll a szolgáltatás frissítésére, vagy a topológia újrakonfigurálásának közepén található. | Nem sikerült kapcsolatot létesíteni az index frissítésével. A keresési szolgáltatás jelenleg nem érhető el, a keresési szolgáltatás pedig átmeneti állapotba kerül. | Állítsa be a szolgáltatást legalább 3 replikával az 99,9%-os rendelkezésre álláshoz az [SLA dokumentációjában](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Hiba történt az alapul szolgáló számítási/hálózati erőforrásban (ritka) | Nem sikerült kapcsolatot létesíteni az index frissítésével. Ismeretlen hiba történt. | Az indexelő úgy konfigurálható, hogy a sikertelen állapotból való kiválasztáshoz [ütemezett ütemtervet futtasson](search-howto-schedule-indexers.md) .

### <a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>A dokumentum nem indexelhető, mert az indexelő által indexelt adatbázis érvénytelen.

A dokumentumot beolvasták és feldolgozták, de az index mezőinek és az indexelő által kinyert adat természetének eltérése miatt nem lehetett felvenni a keresési indexbe. Ez az alábbiak miatt fordulhat elő:

| Ok | Példa
| --- | ---
| Az indexelő által kinyert mező (k) adattípusa nem kompatibilis a megfelelő cél index mező adatmodelljével. | A _"value" kulccsal_rendelkező dokumentumban lévő "adatmező"_érvénytelen "EDM_. String" típusú. A várt típus: "Collection (EDM. String)". |
| Nem sikerült kibontani a JSON-entitásokat egy karakterlánc-értékből. | Nem sikerült elemezni a (z) ""_típusú ""_ EDM. String "" érték "MEZŐjét JSON-objektumként. Hiba: "az érték elemzése után a rendszer váratlan karaktert észlelt:" ". Elérési_út_útvonala, 1. sor, pozíció: 3162. |
| Nem sikerült kibontani a JSON-entitások gyűjteményét egy karakterlánc-értékből.  | Nem sikerült értelmezni a (z) ""_típusú ""_ EDM. String "" érték "MEZŐjét JSON-tömbként. Hiba: "az érték elemzése után a rendszer váratlan karaktert észlelt:" ". Elérési út: "[0]", 1. sor, 27. pozíció |
| Ismeretlen típus lett felderítve a forrásbizonylat dokumentumban. | Ismeretlen típus (_ismeretlen_) nem indexelhető |
| A forrás dokumentumban nem kompatibilis jelölés szerepel a földrajzi pontokhoz. | A WKT pont karakterlánca nem támogatott. Ehelyett használjon GeoJson pont literálokat |

Ebben az esetben tekintse meg a [támogatott adattípusok (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) és az [adattípusok leképezése a Azure Search indexelő](https://docs.microsoft.com/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) számára lehetőséget, és győződjön meg arról, hogy helyesen hozza létre az index-sémát, és beállította a megfelelő [Indexelő mező-hozzárendeléseket](search-indexer-field-mappings.md). A hibaüzenet olyan részleteket tartalmaz, amelyek segítségével nyomon követheti az eltérés forrását.

### <a name="could-not-process-document-within-indexer-max-run-time"></a>A dokumentum nem dolgozható fel az indexelő maximális futási idején belül

Ez a hiba akkor fordul elő, ha az indexelő nem tudja befejezni egyetlen dokumentum feldolgozását az adatforrásból az engedélyezett végrehajtási időn belül. A szakértelmével használata esetén a [maximális futási idő](search-limits-quotas-capacity.md#indexer-limits) rövidebb. Ha ez a hiba akkor fordul elő, ha a maxFailedItems értéke nem 0, az indexelő megkerüli a dokumentumot a jövőben, hogy az indexelés előrehaladást érjen el. Ha nem engedheti meg, hogy kihagyjon bármilyen dokumentumot, vagy ha konzisztensen látja ezt a hibát, érdemes lehet kisebb dokumentumokra feltörni a dokumentumokat, hogy a részleges előrehaladás egyetlen indexelő végrehajtáson belül is elvégezhető legyen.

##  <a name="warnings"></a>Figyelmeztetések
A figyelmeztetések nem állíthatják le az indexelést, de olyan feltételeket jeleznek, amelyek váratlan eredményekhez vezethetnek. Függetlenül attól, hogy végrehajtja-e a műveletet, vagy nem függ az adatoktól és a forgatókönyvtől.

### <a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>A szaktudás nem hajtható végre, mert a képzettségi bevitel érvénytelen volt.
Az indexelő nem tudott futtatni egy képességet a készségkészlet, mert hiányzik a szakértelembe való bevitel, a helytelen típus vagy egyéb érvénytelen.

A kognitív képességek kötelező bemenetekkel és opcionális bemenetekkel rendelkeznek. A [kinyerési képességnek](cognitive-search-skill-keyphrases.md) például két kötelező bemenete van `text`, `languageCode`, és nem választható bemenet. Ha a szükséges bemenetek érvénytelenek, a rendszer kihagyja a képességet, és figyelmeztetést generál. A kihagyott képességek nem eredményeznek kimeneteket, így ha más ismeretek is a kihagyott képességek kimenetét használják, további figyelmeztetések is készíthetők.

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

| Ok | Példa | Műveletek |
| --- | --- | --- |
| A szakértelem bemenete nem megfelelő típusú. | A szükséges szaktudás-beviteli `X` nem a várt típusú `String`. A szükséges szaktudás-beviteli `X` nem a várt formátumban volt. | Bizonyos készségek várhatóan adott típusok bemeneteit számítják fel, például az [érzelmi képességek](cognitive-search-skill-sentiment.md) `text` karakterláncnak számítanak. Ha a bemenet nem sztring értéket ad meg, akkor a rendszer nem hajtja végre, és nem hoz létre kimenetet. Győződjön meg arról, hogy az adatkészletben a bemeneti értékek egységesek a típusban, vagy használjon [egyéni webes API-képességet](cognitive-search-custom-skill-web-api.md) a bemenet előfeldolgozásához. Ha a képességet egy tömbön keresztül ismétli meg, ellenőrizze a szakértelem kontextusát, és a megfelelő pozícióban `*`. A környezetnek és a bemeneti forrásnak általában a tömbök `*` kell végződnie. |
| Hiányzik a szaktudás bemenete | Hiányzik a szükséges szakértelem-beviteli `X`. | Ha az összes dokumentum ezt a figyelmeztetést kapja, valószínűleg van egy elírás a bemeneti elérési utakon, és az elérési úton ellenőrizze, hogy az elérési út tartalmazza-e a tulajdonság nevét, a további vagy a hiányzó `*`t, és az adatforrás dokumentumai határozzák meg a szükséges bemeneteket. |
| A képzettségi nyelvi kód bemenete érvénytelen. | A skill input `languageCode` a következő nyelvi kódokat `X,Y,Z`, amelyek közül legalább egy érvénytelen. | További [Részletek:](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) |

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>A "languageCode" képzettségi bemenethez a következő nyelvi kódok szerepelnek: "X, Y, Z", amelyek közül legalább az egyik érvénytelen.
Egy vagy több alsóbb rétegbeli képesség nem kötelező `languageCode`ba átadott érték nem támogatott. Ez akkor fordulhat elő, ha a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) kimenetét átadja a következő szakismereteknek, a kimenet pedig több nyelvből áll, mint amennyit az adott alsóbb rétegbeli készségek támogatnak.

Ha tudja, hogy az adatkészlete egy adott nyelven van, távolítsa el a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) és a `languageCode` skill inputot, és használja a skill `defaultLanguageCode` skill paramétert, feltéve, hogy az adott szakértelem nyelve támogatott.

Ha tudja, hogy az adathalmaz több nyelvet tartalmaz, ezért a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) és a `languageCode` bemenetre van szüksége, vegyen fel egy [ConditionalSkill](cognitive-search-skill-conditional.md) , hogy kiszűrje a szöveget olyan nyelvekkel, amelyek nem támogatottak a az alárendelt képességek szövege.  Íme egy példa arra, hogy ez hogyan néz ki a EntityRecognitionSkill:

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

### <a name="skill-input-was-truncated"></a>A szaktudás bemenete csonkolt
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

### <a name="web-api-skill-response-contains-warnings"></a>A webes API-ügyességi válasza figyelmeztetéseket tartalmaz
Az indexelő egy képességet tudott futtatni a készségkészlet, de a webes API-kérelemtől kapott válasz a végrehajtás során figyelmeztetéseket jelzett. Tekintse át a figyelmeztetéseket, és Ismerje meg, hogy milyen hatással van az adatai, és hogy van-e szükség beavatkozásra.

### <a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>Az aktuális indexelő konfiguráció nem támogatja a növekményes előrehaladást
Ez a figyelmeztetés csak Cosmos DB adatforrások esetén fordul elő.

Az indexelés során fellépő növekményes előrehaladás biztosítja, hogy ha az indexelő végrehajtását átmeneti hibák vagy végrehajtási időkorlát miatt megszakítja, az indexelő elvégezheti a következő futása után, hogy a teljes gyűjteményt ne kelljen újból indexelni. Ez különösen fontos a nagyméretű gyűjtemények indexelése során.

A befejezetlen indexelési feladatok folytatásának lehetősége a `_ts` oszlop által megrendelt dokumentumok alapján történik. Az indexelő az időbélyeg használatával határozza meg, hogy melyik dokumentumot kell felvenni a következőre. Ha a `_ts` oszlop hiányzik, vagy az indexelő nem tudja megállapítani, hogy az adott egyéni lekérdezést rendeli-e meg, az indexelő elindul, és látni fogja ezt a figyelmeztetést.

Felülbírálhatja ezt a viselkedést, és engedélyezheti a növekményes előrehaladást, és letilthatja ezt a figyelmeztetést a `assumeOrderByHighWatermarkColumn` konfigurációs tulajdonság használatával.

[További információ a növekményes és egyéni lekérdezésekkel Cosmos DB.](https://go.microsoft.com/fwlink/?linkid=2099593)