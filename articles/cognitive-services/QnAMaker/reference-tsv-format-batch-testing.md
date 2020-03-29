---
title: Tételteszt TSV formátum - QnA Maker
titleSuffix: Azure Cognitive Services
description: A kötegelt tesztelést szolgáló TSV-formátum ismertetése
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: dccf034b6497651db70b6cc09fff3f1b1d645a1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507824"
---
# <a name="batch-testing-tsv-format"></a>Kötegelt tesztelés TSV formátum

A kötegelt tesztelés [forráskódból](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) vagy [letölthető végrehajtható fájlként érhető](https://aka.ms/qna_btzip)el. A kötegelt teszt futtatásához szükséges parancs formátuma a következő:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Várt érték|
|--|--|
|1|[TSV beviteli mezőkkel](#tsv-input-fields) formázott tsv fájl neve|
|2|A végpont URI-ja, a YOUR-HOST a QnA Maker portál Közzététel lapján.|
|3|ENDPOINT-KEY, a QnA Maker portál Közzététel lapján található.|
|4|az eredmények kötegelt tesztjével létrehozott tsv-fájl neve.|

Az alábbi információk segítségével megismerheti és megvalósíthatja a TSV-formátumot a kötegelt teszteléshez. 

## <a name="tsv-input-fields"></a>TSV beviteli mezők

|TSV bemeneti fájl mezők|Megjegyzések|
|--|--|
|KBID|A közzététel lapon található tudásbázisazonosító.|
|Kérdés|Az a kérdés, amelyet a felhasználó megadna.|
|Metaadatcímkék|választható|
|Felső paraméter|választható| 
|Várt válaszazonosító|választható|

![A TSV-fájl bemeneti formátuma kötegelt teszteléshez.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>TSV kimeneti mezők 

|TSV kimeneti fájl paraméterei|Megjegyzések|
|--|--|
|KBID|A közzététel lapon található tudásbázisazonosító.|
|Kérdés|A bemeneti fájlból beírt kérdés.|
|Válasz|Legjobb válasz a tudásbázisból.|
|Válasz azonosítója|Válasz azonosítója|
|Pontszám|Jóslás pontszám a válaszhoz. |
|Metaadatcímkék|visszaküldött válaszhoz társítva|
|Várt válaszazonosító|választható (csak akkor, ha a várt válaszazonosító van megadva)|
|Ítélet címkéje|nem kötelező, az értékek lehetnek: helyesek vagy helytelenek (csak akkor, ha a várt válasz van megadva)|
