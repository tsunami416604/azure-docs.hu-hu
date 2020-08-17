---
title: Tartalomtípusok – QnA Maker
description: A tartalomtípusok számos szabványos strukturált dokumentumot tartalmaznak, mint például a PDF, a DOCX és a TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 04d7c46a7aa347718cf6bc593a92a2f0430eb6fd
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263163"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>A tudásbázisba felvehető dokumentumok tartalomtípusai
A tartalomtípusok számos szabványos strukturált dokumentumot tartalmaznak, mint például a PDF, a DOC és a TXT.

## <a name="file-and-url-data-types"></a>Fájl-és URL-adattípusok

Az alábbi táblázat összefoglalja a QnA Maker által támogatott tartalom-és fájlformátumok típusait.

|Source Type (Forrás típusa)|Tartalom típusa| Példák|
|--|--|--|
|URL-cím|Gyakori kérdések<br> (Lapos, szakaszok vagy témakörök kezdőlapja)<br>Támogatási lapok <br> (Egyoldalas útmutató cikkek, hibaelhárítási cikkek stb.)|[Egyszerű gyakori kérdések](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[Gyakori kérdések a hivatkozásokkal kapcsolatban](https://www.microsoft.com/en-us/software-download/faq)<br> [GYIK a témakörök kezdőlapján](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Támogatási cikk](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|GYIK<br> Termék kézi,<br> Brosúrák<br> Tanulmány<br> Szórólap-szabályzat,<br> Támogatási útmutató,<br> Strukturált QnA,<br> stb.|**Többszörös kapcsolás nélkül**<br>[Strukturált QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Minta termék Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Minta semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Minta fehér paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Többszörös kapcsolás**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso-előnyök (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso-előnyök (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|Strukturált QnA-fájl<br> (többek között az RTF, a HTML-támogatás)|**Többszörös kapcsolás nélkül**:<br>[Minta QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Többszörös kapcsolás**:<br>[Strukturált egyszerű FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface laptop FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|Strukturált QnA-fájl|[Példa: Chit-Chat. TSV](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Ha hitelesítésre van szüksége az adatforráshoz, vegye figyelembe a következő módszereket a tartalom QnA Makerba való beszerzéséhez:

* Fájl manuális letöltése és importálása QnA Maker
* Fájl hozzáadása a hitelesített [SharePoint-helyről](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>URL-tartalom

Két típusú dokumentum importálható QnA Maker URL- **cím** használatával:

* Gyakori kérdések URL-címei
* Támogatási URL-címek

Mindegyik típus egy várt formátumot jelez.

## <a name="file-based-content"></a>Fájl alapú tartalom

A [QnA Maker-portálon](https://www.qnamaker.ai)hozzáadhat fájlokat egy nyilvános forrásból vagy a helyi fájlrendszerből is.

## <a name="content-format-guidelines"></a>Tartalomformátumra vonatkozó útmutatók

További információ a különböző fájlokra vonatkozó [formátumokról](../reference-document-format-guidelines.md) .

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy milyen információkat tárol a [kérdés-és válasz (QnA) pár](question-answer-set.md).
