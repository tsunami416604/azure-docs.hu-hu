---
title: Korlátozások és határok – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker részei a Tudásbázis és a szolgáltatás metaadat-korlátokkal rendelkeznek. Fontos, hogy a Tudásbázis tesztelése és közzététele érdekében ezeket korlátokon belül.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 07/22/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b90b4806e86ed0ba33500cf31a6ed892241ceabe
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423458"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>A QnA Maker Tudásbázis korlátok és határok

Az alább megadott QnA Maker korlátok a [Azure Search díjszabási szintjei](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) és a [QnA Maker díjszabási szintjeinek](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)kombinációja. Ismernie kell mindkét korlátot, hogy tisztában legyen azzal, hogy hány tudásbázist hozhat létre erőforrás alapján, és hogy mekkora az egyes tudásbázisok mérete.

## <a name="knowledge-bases"></a>Tudásbázisok

A tudásbázisok maximális száma [Azure Search szintű korlátokon](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)alapul.

|**Az Azure Search szolgáltatásréteg** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|A közzétett tudásbázisok maximális száma engedélyezett|2|14|49|199|199|2,999|

 Ha például a réteg 15 engedélyezett indextel rendelkezik, a közzétett Tudásbázisban 14 tudásbázist (1 indexet) tehet közzé. A tizenötödik index `testkb`a szerzői és tesztelési célú összes tudásbázishoz használatos. 

## <a name="extraction-limits"></a>Kibontási korlátok

### <a name="maximum-number-of-files"></a>Fájlok maximális száma

A kinyerhető fájlok maximális száma és a maximális fájlméret a **[QnA Maker díjszabási szintjétől](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** függ.

### <a name="maximum-number-of-deep-links-from-url"></a>Az URL-címekről származó mély hivatkozások maximális száma

A QnAs egy URL-lapról való kinyeréséhez feltérképezhető mély hivatkozások maximális száma **20**.

## <a name="metadata-limits"></a>Metaadat-korlátok

A metaadatok mezőinek maximális száma a Tudásbázisban a **[Azure Search szint korlátain](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** alapul.

|**Az Azure Search szolgáltatásréteg** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximális metaadatokat tartalmazó mezőket a QnA Maker szolgáltatás (között az összes Tudásbázis)|1,000|100 *|1,000|1,000|1,000|1,000|

## <a name="knowledge-base-content-limits"></a>Tudásbázis-tartalmat korlátok
Általános korlátozások a tartalmat a Tudásbázis:
* Válasz szövegének hossza: 25,000
* A szóban forgó szöveg hossza: 1,000
* A metaadat-kulcs/érték szövegének hossza: 100
* A metaadatok neve támogatott karakterek: Ábécék, számjegyek és _  
* A metaadatok értékének támogatott karakterei: Az összes kivétel: és | 
* Fájlnév hossza: 200
* Támogatott fájlformátumok: ".tsv", ".pdf", "txt", ".docx", ".xlsx".
* Alternatív kérdések maximális száma: 300
* Kérdés-válasz párok maximális száma: A kiválasztott **[Azure Search szinttől](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** függ. A kérdések és válaszok pár a Azure Search index dokumentumára mutat. 
* URL-cím/HTML-lap: 1 000 000 karakter

## <a name="create-knowledge-base-call-limits"></a>Hozza létre a Tudásbázis hívás korlátai:
Ezen felelnek a minden korlátokat Tudásbázis művelet; létrehozása azt jelenti, kattintson a *létrehozása KB* vagy a CreateKnowledgeBase API-t hívná.
* Az alternatív kérdések maximális száma egy adott válaszban: 300
* URL-címek maximális száma: 10
* Fájlok maximális száma: 10

## <a name="update-knowledge-base-call-limits"></a>Tudásbázis hívás vonatkozó korlátok módosítása
Ezek képviselik a frissítési műveletek; korlátai azt jelenti, kattintson a *mentéséhez és betanítunk* vagy a UpdateKnowledgeBase API-t hívná.
* Az egyes források nevének hossza: 300
* A hozzáadott vagy törölt alternatív kérdések maximális száma: 300
* A hozzáadott vagy törölt metaadat-mezők maximális száma: 10
* A frissíthető URL-címek maximális száma: 5

## <a name="next-steps"></a>További lépések

A szolgáltatási szintek változásának megismerése:

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): Ha további forrásfájlok vagy nagyobb dokumentumok szükségesek a Tudásbázisban, az aktuális szinten túlmutatva frissítse a QnA Maker Service díjszabási szintjét.
* [App Service](how-to/upgrade-qnamaker-service.md#upgrade-app-service): Ha a Tudásbázisban több kérést kell kiszolgálni az ügyfélalkalmazástól, frissítse az App Service díjszabási szintjét.
* [Azure Search](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): Ha sok tudásbázist tervez, frissítse a Azure Search Service díjszabási szintjét.
