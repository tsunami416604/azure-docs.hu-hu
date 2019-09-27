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
ms.subservice: cognitive-search
ms.openlocfilehash: 4e31f818e96ae9f13e3ce8892e575318831848f6
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71329382"
---
# <a name="common-errors-and-warnings-of-the-ai-enrichment-pipeline-in-azure-search"></a>Gyakori hibák és figyelmeztetések a mesterséges intelligencia-bővítési folyamatról Azure Search

Ez a cikk olyan gyakori hibákkal és figyelmeztetésekkel kapcsolatos információkat és megoldásokat tartalmaz, amelyeket az AI-gazdagítás során a Azure Searchban lehet megtapasztalni.

## <a name="errors"></a>Hibák
Az indexelés leáll, ha a hibák száma meghaladja a ["maxfaileditems"](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures). A következő fejezetek segíthetnek a hibák megoldásában, így az indexelés a folytatáshoz is használható.

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