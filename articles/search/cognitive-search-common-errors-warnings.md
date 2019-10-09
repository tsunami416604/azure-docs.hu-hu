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
ms.openlocfilehash: b5a161e570489e6382f2226ab5dc9a1c34dc67df
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028320"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Gyakori hibák és figyelmeztetések a mesterséges intelligencia-bővítési folyamatról Azure Search

Ez a cikk olyan gyakori hibákkal és figyelmeztetésekkel kapcsolatos információkat és megoldásokat tartalmaz, amelyeket az AI-gazdagítás során a Azure Searchban lehet megtapasztalni.

## <a name="errors"></a>Hibák
Az indexelés leáll, ha a hibák száma meghaladja a ["maxFailedItems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). 

Ha azt szeretné, hogy az indexelő figyelmen kívül hagyják ezeket a hibákat (és ugorjon a "sikertelen dokumentumok" elemre), akkor érdemes frissíteni a `maxFailedItems` és `maxFailedItemsPerBatch` értéket az [itt](https://docs.microsoft.com/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)leírtak szerint.

> [!NOTE]
> Minden sikertelen dokumentum, valamint a dokumentum kulcsa (ha elérhető) az indexelő végrehajtási állapotában hibát jelez. Az [index API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) használatával manuálisan is feltöltheti a dokumentumokat egy későbbi időpontban, ha úgy állította be az indexelő, hogy eltűri a hibákat.

A következő fejezetek segíthetnek a hibák megoldásában, így az indexelés a folytatáshoz is használható.

### <a name="could-not-read-document"></a>A dokumentum nem olvasható
Az indexelő nem tudta olvasni a dokumentumot az adatforrásból. Ez az alábbiak miatt fordulhat elő:

| Reason | Példa | Action |
| --- | --- | --- |
| inkonzisztens mezők típusai különböző dokumentumokban | Az érték típusa nem egyezik az oszlop típusával. Nem sikerült tárolni a `'{47.6,-122.1}'` értéket a szerzők oszlopban.  A várt típus: JArray. | Ügyeljen arra, hogy az egyes mezők típusa azonos legyen a különböző dokumentumok között. Ha például az első dokumentum `'startTime'` mező egy DateTime típusú, a másodikban pedig egy karakterlánc, akkor ez a hiba fog megjelenni. |
| hibák az adatforrás mögöttes szolgáltatásában | (Cosmos DB) @no__t – 0 | Ellenőrizze, hogy a tárolási példány kifogástalan állapotú-e. Előfordulhat, hogy módosítania kell a skálázást/particionálást. |
| átmeneti problémák | Átviteli szintű hiba történt a kiszolgáló eredményeinek fogadása során. Szolgáltató TCP-szolgáltató, hiba: 0 – a távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat | Esetenként váratlan kapcsolódási problémák léptek fel. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

### <a name="could-not-extract-document-content"></a>Nem lehetett kibontani a dokumentum tartalmát
A blob-adatforrással rendelkező indexelő nem tudta kinyerni a tartalmat a dokumentumból (például egy PDF-fájlt). Ez az alábbiak miatt fordulhat elő:

| Reason | Példa | Action |
| --- | --- | --- |
| a blob mérete meghaladja a méretkorlátot | A dokumentum @no__t – 0 bájt, ami meghaladja a maximális méretet, `'134217728'` bájtot a jelenlegi szolgáltatási réteghez tartozó dokumentumok kinyeréséhez. | [BLOB-indexelési hibák](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| a blob nem támogatott tartalomtípust tartalmaz | A dokumentum nem támogatott tartalomtípussal rendelkezik `'image/png'` | [BLOB-indexelési hibák](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| a blob titkosított | A dokumentumot nem lehetett feldolgozni – titkosított vagy jelszóval védett. | [BLOB beállításai](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed) |
| átmeneti problémák | Hiba történt a blob feldolgozásakor: A kérés megszakadt: A kérést megszakították. | Esetenként váratlan kapcsolódási problémák léptek fel. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

### <a name="could-not-parse-document"></a>Nem sikerült elemezni a dokumentumot
Az indexelő beolvassa a dokumentumot az adatforrásból, de hiba történt a dokumentum tartalmának a megadott mező-hozzárendelési sémába való konvertálása során. Ez az alábbiak miatt fordulhat elő:

| Reason | Példa | Action |
| --- | --- | --- |
| A dokumentum kulcsa hiányzik | A dokumentum kulcsa nem lehet hiányzik vagy üres. | Győződjön meg arról, hogy minden dokumentum rendelkezik érvényes dokumentum-kulcsokkal |
| A dokumentum kulcsa érvénytelen | A dokumentum kulcsa nem lehet hosszabb 1024 karakternél | Módosítsa a dokumentum kulcsát az érvényesítési követelmények teljesítéséhez. |
| A mező leképezése nem alkalmazható egy mezőre | Nem lehet alkalmazni a leképezési függvényt `'functionName'` értékre a következő mezőhöz: `'fieldName'`. A tömb nem lehet null. Paraméter neve: bájtok | Ellenőrizze az indexelő által definiált [mező-hozzárendeléseket](search-indexer-field-mappings.md) , és hasonlítsa össze a hibás dokumentum megadott mezőjének értékével. Előfordulhat, hogy módosítania kell a mező-hozzárendeléseket vagy a dokumentum-adattípust. |
| Nem olvasható be a mező értéke | Nem lehetett beolvasni a (z) `'fieldName'` oszlop értékét a következő indexnél: `'fieldIndex'`. Átviteli szintű hiba történt a kiszolgáló eredményeinek fogadása során. Szolgáltató TCP-szolgáltató, hiba: 0 – a távoli gazdagép kényszerített módon lezárta a meglévő kapcsolatokat.) | Ezek a hibák általában az adatforrás mögöttes szolgáltatásával kapcsolatos váratlan kapcsolódási problémák miatt jelentkeznek. Próbálja meg később futtatni a dokumentumot az indexelő használatával. |

### <a name="could-not-index-document"></a>A dokumentum nem indexelhető
A dokumentum olvasása és feldolgozása megtörtént, de az indexelő nem tudja felvenni a keresési indexbe. Ez az alábbiak miatt fordulhat elő:

| Reason | Példa | Action |
| --- | --- | --- |
| Egy mező túl nagy kifejezést tartalmaz | A dokumentum egy kifejezése nagyobb az [32 KB-os korlátnál](search-limits-quotas-capacity.md#api-request-limits) | Ezt a korlátozást elkerülheti, ha úgy látja, hogy a mező nem szűrhető, sokoldalú vagy rendezhető.
| A dokumentum túl nagy az indexeléshez | A dokumentum nagyobb, mint az [API-kérelmek maximális mérete](search-limits-quotas-capacity.md#api-request-limits) | [Nagyméretű adathalmazok indexelése](search-howto-large-index.md)

### <a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>A "languageCode" képzettségi bemenethez a következő nyelvi kódok szerepelnek: "X, Y, Z", amelyek közül legalább az egyik érvénytelen.
Az alárendelt képességek opcionális `languageCode` bemenetének egy vagy több értéke nem támogatott. Ez akkor fordulhat elő, ha a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) kimenetét átadja a következő szakismereteknek, a kimenet pedig több nyelvből áll, mint amennyit az adott alsóbb rétegbeli készségek támogatnak.

Ha tudja, hogy az adatkészlete egyetlen nyelven van, távolítsa el a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) és a `languageCode` skill-bemenetet, és használja a `defaultLanguageCode` Skill paramétert ehhez a szaktudáshoz, feltéve, hogy az adott szakértelem nyelve támogatott.

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

### <a name="skill-did-not-execute-within-the-time-limit"></a>A szakértelem nem volt végrehajtva az időkorláton belül
Ez a hibaüzenet két esetben fordulhat elő, amelyek mindegyikét másképp kell kezelni. Kövesse az alábbi utasításokat attól függően, hogy milyen képességgel tért vissza ez a hiba.

#### <a name="built-in-cognitive-service-skills"></a>Beépített kognitív szolgáltatásokkal kapcsolatos ismeretek
Számos beépített kognitív képesség, például a nyelvfelismerés, az entitások felismerése vagy az OCR, a kognitív szolgáltatás API-végpontja támogatja. Időnként átmeneti problémák léptek fel ezekkel a végpontokkal, és a kérés időtúllépést eredményez. Átmeneti problémák esetén nincs szükség jogorvoslatra, kivéve a várakozást, és próbálkozzon újra. Enyhítő megoldásként érdemes lehet úgy beállítani az indexelő, hogy [menetrend szerint fusson](search-howto-schedule-indexers.md). Az ütemezett indexelés megkeresi, hogy hol maradt. Az átmeneti problémák megoldása érdekében az indexelést és a kognitív képességek feldolgozását folytatni kell a következő ütemezett futtatáskor.

#### <a name="custom-skills"></a>Egyéni készségek
Ha az Ön által létrehozott egyéni szakértelem időtúllépési hibába ütközik, néhány dolog kipróbálható. Először tekintse át az egyéni szaktudást, és győződjön meg arról, hogy nem ragadt meg egy végtelen hurokban, és hogy az eredmény következetesen tér vissza. Miután meggyőződött róla, hogy a helyzet megtörtént, állapítsa meg, hogy milyen végrehajtási idő van a szakértelemben. Ha nem adott meg explicit módon egy `timeout` értéket az egyéni szaktudás definíciójában, akkor az alapértelmezett @no__t – 1 érték 30 másodperc. Ha 30 másodperc nem elég hosszú a szaktudás végrehajtásához, akkor magasabb @no__t – 0 értéket adhat meg az egyéni szakértelem-definícióban. Íme egy példa egy egyéni képesség-definícióra, amelyben az időtúllépés 90 másodpercre van állítva:

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

A `timeout` paraméter számára beállítható maximális érték 230 másodperc.  Ha az egyéni képesség nem hajtható végre következetesen az 230 másodpercen belül, érdemes lehet az egyéni képességek `batchSize` értékének csökkentésére, hogy kevesebb dokumentum legyen feldolgozható egyetlen végrehajtáson belül.  Ha már beállította a `batchSize` értéket 1-re, újra kell írnia a képességet, hogy el tudja végezni a végrehajtást a 230 másodperc alatt, vagy más módon feloszthatja azt több egyéni képességre, hogy az egyetlen egyéni képesség végrehajtásának ideje legfeljebb 230 másodperc legyen. További információért tekintse át az [Egyéni szakértelem dokumentációját](cognitive-search-custom-skill-web-api.md) .

### <a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>Nem lehetett "`MergeOrUpload`" | "`Delete`" dokumentum a keresési indexhez

A dokumentum olvasása és feldolgozása megtörtént, de az indexelő nem tudja felvenni a keresési indexbe. Ez az alábbiak miatt fordulhat elő:

| Reason | Példa | Action |
| --- | --- | --- |
| A dokumentum egy kifejezése nagyobb az [32 KB-os korlátnál](search-limits-quotas-capacity.md#api-request-limits) | Egy mező túl nagy kifejezést tartalmaz | Ezt a korlátozást elkerülheti, ha úgy látja, hogy a mező nem szűrhető, sokoldalú vagy rendezhető.
| A dokumentum nagyobb, mint az [API-kérelmek maximális mérete](search-limits-quotas-capacity.md#api-request-limits) | A dokumentum túl nagy az indexeléshez | [Nagyméretű adathalmazok indexelése](search-howto-large-index.md)
| Hiba történt a célként megadott indexhez való kapcsolódáskor (amely az újrapróbálkozások után is fennmarad), mert a szolgáltatás más terhelés alá esik, például lekérdezés vagy indexelés. | Nem sikerült kapcsolatot létesíteni az index frissítésével. A Search szolgáltatás nagy terhelés alatt áll. | [A keresési szolgáltatás vertikális felskálázása](search-capacity-planning.md)
| A keresési szolgáltatás javítás alatt áll a szolgáltatás frissítésére, vagy a topológia újrakonfigurálásának közepén található. | Nem sikerült kapcsolatot létesíteni az index frissítésével. A keresési szolgáltatás jelenleg nem érhető el, a keresési szolgáltatás pedig átmeneti állapotba kerül. | Állítsa be a szolgáltatást legalább 3 replikával az 99,9%-os rendelkezésre álláshoz az [SLA dokumentációjában](https://azure.microsoft.com/support/legal/sla/search/v1_0/)
| Hiba történt az alapul szolgáló számítási/hálózati erőforrásban (ritka) | Nem sikerült kapcsolatot létesíteni az index frissítésével. Ismeretlen hiba történt. | Az indexelő úgy konfigurálható, hogy a sikertelen állapotból való kiválasztáshoz [ütemezett ütemtervet futtasson](search-howto-schedule-indexers.md) .

##  <a name="warnings"></a>Figyelmeztetések
A figyelmeztetések nem állíthatják le az indexelést, de olyan feltételeket jeleznek, amelyek váratlan eredményekhez vezethetnek. Függetlenül attól, hogy végrehajtja-e a műveletet, vagy nem függ az adatoktól és a forgatókönyvtől.

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