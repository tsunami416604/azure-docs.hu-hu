---
title: Tartalomtípusok - QnA Maker
description: A tartalomtípusok számos szabványos strukturált dokumentumot tartalmaznak, például PDF, DOC és TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650197"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Tudásbázishoz hozzáadható dokumentumtípusok
A tartalomtípusok számos szabványos strukturált dokumentumot tartalmaznak, például PDF, DOC és TXT.

## <a name="file-and-url-data-types"></a>Fájl- és URL-adattípusok

Az alábbi táblázat összefoglalja a QnA Maker által támogatott tartalomtípusokat és fájlformátumokat.

|Source Type (Forrás típusa)|Tartalomtípus| Példák|
|--|--|--|
|URL-cím|Gyakori kérdések<br> (Lakás, a szakaszok vagy a téma honlapja)<br>Támogatási lapok <br> (Egyoldalas útmutatócikkek, hibaelhárítási cikkek stb.)|[Egyszerű GYIK](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), <br>[Gyakran feltett kérdések a hivatkozásokkal](https://www.microsoft.com/en-us/software-download/faq),<br> [Gyakran feltett kérdések a témák kezdőlapjával](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Támogatási cikk](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|Gyik<br> Termék kézikönyv,<br> Brosúrák<br> Papír<br> Szórólap szabályzat,<br> Támogatási útmutató,<br> Strukturált minőségbiztosítás,<br> stb.|**Multi-turn nélkül**<br>[Strukturált QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Minta termék Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Minta félig strukturált.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Példa a tanulmány.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Többfordulatos:**<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso előnyei (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso előnyei (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|Strukturált Minőségbiztosítási fájl<br> (beleértve az RTF-et, a HTML-támogatást)|[Minta QnA GYIK.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|*TXT/TSV|Strukturált Minőségbiztosítási fájl|[Minta chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Ha hitelesítésre van szüksége az adatforráshoz, vegye figyelembe az alábbi módszereket a tartalom QnA Maker beolvasásához:

* Töltse le a fájlt manuálisan, és importálja a QnA Maker
* Fájl hozzáadása hitelesített [SharePoint-helyről](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>URL-tartalom

Kétféle dokumentum importálható **URL-címen** keresztül a QnA Maker alkalmazásban:

* GYIK URL-ek
* Támogatási URL-ek

Minden típus a várt formátumot jelzi.

## <a name="file-based-content"></a>Fájlalapú tartalom

A [QnA Maker portálon](https://www.qnamaker.ai)nyilvános forrásból vagy a helyi fájlrendszerből adhat hozzá fájlokat a tudásbázishoz.

## <a name="content-format-guidelines"></a>Tartalomformátumra vonatkozó irányelvek

További információ a különböző fájlok [formátumra vonatkozó irányelveiről.](../reference-document-format-guidelines.md)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogy milyen információkat tárol a [kérdés és válasz (QnA) készlet.](question-answer-set.md)