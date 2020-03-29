---
title: Korlátok és határok - QnA Maker
description: A QnA Maker metakorlátokkal rendelkezik a tudásbázis és a szolgáltatás egyes részeire vonatkozóan. Fontos, hogy a tesztelés és a közzététel érdekében tartsa a tudásbázist ezeken a korlátokon belül.
ms.topic: reference
ms.date: 02/14/2020
ms.openlocfilehash: 6375a6c6efc0c7016d9947e04e9479385aa80af5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273335"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>A QnA Maker tudásbáziskorlátai és határai

Az alábbiakban megadott QnA Maker-korlátok az [Azure Cognitive Search díjszabási csomagkorlátai](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) és a [QnA Maker díjszabási szint korlátainak](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)kombinációja. Mindkét korlátkészletet ismernie kell, hogy megértse, hány tudásbázist hozhat létre erőforrásonként, és hogy az egyes tudásbázisok milyen nagyok növekedhetnek.

## <a name="knowledge-bases"></a>Tudásbázisok

A tudásbázisok maximális száma az [Azure Cognitive Search réteg korlátain](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)alapul.

|**Azure Cognitive Search szint** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|A közzétett tudásbázisok maximális száma megengedett|2|14|49|199|199|2,999|

 Ha például a réteg 15 engedélyezett indexszel rendelkezik, 14 tudásbázist tehet közzé (közzétett tudásbázisonként 1 indexet). A tizenötödik `testkb`index , a szerzői és tesztelési tudásbázisok hoz használatos.

## <a name="extraction-limits"></a>Kivonási határértékek

### <a name="file-naming-constraints"></a>Fájlelnevezési megkötések

A fájlnevek nem tartalmazhatják a következő karaktereket:

|Ne használjon karaktert|
|--|
|Egyszeres idézőjel`'`|
|Dupla idézőjel`"`|

### <a name="maximum-file-size"></a>Maximális fájlméret

|Formátum|Maximális fájlméret (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Fájlok maximális száma

A kibontható fájlok maximális száma és a maximális fájlméret a **[QnA Maker tarifacsomag-korlátain](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** alapul.

### <a name="maximum-number-of-deep-links-from-url"></a>Az URL-címből származó mélyhivatkozások maximális száma

A QnA-k URL-oldalról történő kinyeréséhez bejárható mélyhivatkozások maximális száma **20**.

## <a name="metadata-limits"></a>Metaadat-korlátok

A metaadatok szövegalapú kulcs:értékpárként jelennek `product:windows 10`meg, például . Ez tárolja, és összehasonlították kisbetűvel.

### <a name="by-azure-cognitive-search-pricing-tier"></a>Az Azure Cognitive Search díjszabási szintje szerint

A metaadatmezők maximális száma tudásbázisonként az **[Azure Cognitive Search réteg korlátain](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** alapul.

|**Azure Cognitive Search szint** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximális metaadatmezők QnA Maker szolgáltatásonként (az összes kb-ben)|1,000|100*|1,000|1,000|1,000|1,000|

### <a name="by-name-and-value"></a>Név és érték szerint

A metaadatok nevének és értékének hossza és elfogadható karakterei az alábbi táblázatban találhatók.

|Elem|Engedélyezett karakter|Regex minta egyezés|Maximális karakter|
|--|--|--|--|
|Név (kulcs)|Lehetővé teszi<br>alfanumerikus (betűk és számjegyek)<br>`_`(aláhúzás)<br> Nem tartalmazhat szóközt.|`^[a-zA-Z0-9_]+$`|100|
|Érték|Lehetővé teszi, hogy mindent, kivéve<br>`:`(kettőspont)<br>`|`(függőleges cső)<br>Csak egy érték engedélyezett.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Tudásbázis-tartalomkorlátok
A tudásbázis tartalmának általános korlátai:
* A válasz szövegének hossza: 25 000
* A kérdés szövegének hossza: 1000
* A metaadatkulcs/érték szövegének hossza: 100
* A metaadatok nevének támogatott karakterei: Ábécék, számjegyek és`_`
* A metaadat-érték támogatott `:` karakterei: Kivételével a`|`
* A fájlnév hossza: 200
* Támogatott fájlformátumok: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Alternatív kérdések maximális száma: 300
* A kérdés-válasz párok maximális száma: Az **[Azure Cognitive Search kiválasztott szintjétől](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** függ. A kérdés-válasz pár leképezi az Azure Cognitive Search index egy dokumentumot.
* URL/HTML oldal: 1 millió karakter

## <a name="create-knowledge-base-call-limits"></a>Tudásbázis-hívási korlátok létrehozása:
Ezek jelentik az egyes létrehozási tudásbázis-műveletek korlátait; vagy a CreateKnowledgeBase API hívása elemre. *Create KB*
* Alternatív kérdések maximális száma válaszonként: 300
* Az URL-címek maximális száma: 10
* Fájlok maximális száma: 10

## <a name="update-knowledge-base-call-limits"></a>Tudásbázis-hívási korlátok frissítése
Ezek jelentik az egyes frissítési műveletek korlátait; vagyis kattintson a *Mentés és betanítás* gombra, vagy hívja meg az UpdateKnowledgeBase API-t.
* Minden forrásnév hossza: 300
* A hozzáadott vagy törölt alternatív kérdések maximális száma: 300
* Hozzáadott vagy törölt metaadatmezők maximális száma: 10
* Frissíthető URL-címek maximális száma: 5

## <a name="next-steps"></a>További lépések

Megtudhatja, hogy mikor és hogyan módosíthatja a [szolgáltatási tarifacsomagokat.](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku)
