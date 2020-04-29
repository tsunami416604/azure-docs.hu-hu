---
title: Batch-teszt TSV formátuma – QnA Maker
titleSuffix: Azure Cognitive Services
description: A Batch-tesztek TSV formátumának megismerése
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73507824"
---
# <a name="batch-testing-tsv-format"></a>Batch-tesztelés TSV formátuma

A Batch-teszt [forráskódból](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) vagy [letölthető végrehajtható zip-fájlból](https://aka.ms/qna_btzip)érhető el. A Batch-teszt futtatására szolgáló parancs formátuma a következő:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Várt érték|
|--|--|
|1|[TSV-beviteli mezőkkel](#tsv-input-fields) formázott TSV-fájl neve|
|2|A végpont URI-ja a QnA Maker portál közzététel lapjáról.|
|3|VÉGPONT – kulcs, a QnA Maker portál közzététel lapján található.|
|4|a Batch test által az eredményekhez létrehozott TSV-fájl neve.|

A következő információk segítségével megismerheti és implementálhatja a Batch-teszteléshez használható TSV formátumot. 

## <a name="tsv-input-fields"></a>TSV beviteli mezők

|TSV bemeneti fájl mezői|Megjegyzések|
|--|--|
|KBID|A TUDÁSBÁZIS-azonosító a közzétételi oldalon található.|
|Kérdés|A felhasználó által megadott kérdés.|
|Metaadatcímkék|választható|
|Felső paraméter|választható| 
|Várt válasz azonosítója|választható|

![A kötegelt teszteléshez használható TSV-fájl bemeneti formátuma.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV kimeneti mezők 

|TSV kimeneti fájl paraméterei|Megjegyzések|
|--|--|
|KBID|A TUDÁSBÁZIS-azonosító a közzétételi oldalon található.|
|Kérdés|A bemeneti fájlban megadott kérdés.|
|Válasz|Az Ön tudásbázisának legfontosabb válasza.|
|Válasz azonosítója|Válasz azonosítója|
|Pontszám|A válasz előrejelzési pontszáma. |
|Metaadatcímkék|visszaadott válasz társítva|
|Várt válasz azonosítója|nem kötelező (csak akkor, ha a várt válasz azonosítója meg van adva)|
|Ítélet címkéje|nem kötelező, az értékek a következőek lehetnek: helyes vagy helytelen (csak akkor, ha a várt válasz meg van adva)|
