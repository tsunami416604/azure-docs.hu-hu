---
title: Korlátozások és határok – QnA Maker
description: A QnA Maker részei a Tudásbázis és a szolgáltatás metaadat-korlátokkal rendelkeznek. Fontos, hogy a Tudásbázis tesztelése és közzététele érdekében ezeket korlátokon belül.
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 6375a6c6efc0c7016d9947e04e9479385aa80af5
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273335"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>A QnA Maker Tudásbázis korlátok és határok

Az alább megadott QnA Maker korlátok az [Azure Cognitive Search díjszabási szintjei](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) és a [QnA Maker díjszabási szintjeinek](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)kombinációja. Ismernie kell mindkét korlátot, hogy tisztában legyen azzal, hogy hány tudásbázist hozhat létre erőforrás alapján, és hogy mekkora az egyes tudásbázisok mérete.

## <a name="knowledge-bases"></a>Tudásbázisok

A tudásbázisok maximális száma az [Azure Cognitive Search szintű korlátokon](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)alapul.

|**Azure Cognitive Search-szintű** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|A közzétett tudásbázisok maximális száma engedélyezett|2|14|49|199|199|2,999|

 Ha például a réteg 15 engedélyezett indextel rendelkezik, a közzétett Tudásbázisban 14 tudásbázist (1 indexet) tehet közzé. A rendszer a tizenötödik indexet (`testkb`) használja a szerzői és tesztelési célú összes tudásbázishoz.

## <a name="extraction-limits"></a>Kibontási korlátok

### <a name="file-naming-constraints"></a>Fájl elnevezési korlátozásai

A fájlnevek nem tartalmazhatják a következő karaktereket:

|Ne használja a karaktert|
|--|
|Szimpla idézőjelek `'`|
|Idézőjelek `"`|

### <a name="maximum-file-size"></a>Maximális fájlméret

|Formátum|Maximális fájlméret (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Fájlok maximális száma

A kinyerhető fájlok maximális száma és a maximális fájlméret a **[QnA Maker díjszabási szintjétől](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** függ.

### <a name="maximum-number-of-deep-links-from-url"></a>Az URL-címekről származó mély hivatkozások maximális száma

A QnAs egy URL-lapról való kinyeréséhez feltérképezhető mély hivatkozások maximális száma **20**.

## <a name="metadata-limits"></a>Metaadat-korlátok

A metaadatok szöveges kulcsként jelennek meg: érték pár, például `product:windows 10`. A rendszer kisbetűvel tárolja és hasonlítja össze.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Az Azure Cognitive Search díjszabási szintje szerint

A metaadatok mezőinek maximális száma a Tudásbázisban az Azure- **[Cognitive Search szint korlátain](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** alapul.

|**Azure Cognitive Search-szintű** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximális metaadatokat tartalmazó mezőket a QnA Maker szolgáltatás (között az összes Tudásbázis)|1,000|100 *|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Név és érték szerint

A metaadatok nevének és értékének hosszát és elfogadható karaktereit a következő táblázat tartalmazza.

|Elem|Engedélyezett karakterek|Regex-minta egyezése|Karakterek maximális száma|
|--|--|--|--|
|Név (kulcs)|Lehetővé teszi<br>alfanumerikus karakterek (betűk és számjegyek)<br>`_` (aláhúzás)<br> Nem tartalmazhat szóközt.|`^[a-zA-Z0-9_]+$`|100|
|Érték|Mindent lehetővé tesz, kivéve<br>`:` (kettőspont)<br>`|` (függőleges cső)<br>Csak egy érték engedélyezett.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Tudásbázis-tartalmat korlátok
Általános korlátozások a tartalmat a Tudásbázis:
* Válasz szöveg hossza: 25 000
* Kérdés szöveg hossza: 1000
* Metaadatok kulcs/érték szöveg hossza: 100
* A metaadatok nevének támogatott karakterei: ábécék, számjegyek és `_`
* A metaadatok értékének támogatott karaktereinek száma: `:` és `|` kivételével
* Fájl nevének hossza: 200
* Támogatott fájlformátumok: ".tsv", ".pdf", "txt", ".docx", ".xlsx".
* Alternatív kérdések maximális száma: 300
* A kérdés-válasz párok maximális száma: a kiválasztott **[Azure Cognitive Search szinttől](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** függ. A kérdések és válaszok pár egy Azure Cognitive Search indexre vonatkozó dokumentumra mutat.
* URL-cím/HTML-lap: 1 000 000 karakter

## <a name="create-knowledge-base-call-limits"></a>Hozza létre a Tudásbázis hívás korlátai:
Ezek az egyes Create Tudásbázis-műveletek korlátai. Ehhez kattintson a *Tudásbázis létrehozása* vagy a CreateKnowledgeBase API meghívása elemre.
* Válaszoló alternatív kérdések maximális száma: 300
* URL-ek maximális száma: 10
* Fájlok maximális száma: 10

## <a name="update-knowledge-base-call-limits"></a>Tudásbázis hívás vonatkozó korlátok módosítása
Ezek a frissítési műveletek korlátainak felelnek meg; a UpdateKnowledgeBase API *mentése és betanítása* vagy meghívása elemre kattintva.
* Minden adatforrás nevének hossza: 300
* A hozzáadott vagy törölt alternatív kérdések maximális száma: 300
* Metaadatok mezők maximális száma felvett vagy törölt: 10
* Frissíthető URL-címek maximális száma: 5

## <a name="next-steps"></a>Következő lépések

Megtudhatja, mikor és hogyan módosíthatja a [szolgáltatás díjszabási szintjeit](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku).
