---
title: API HTTP válaszkódok - LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS authoring és az endpoint API-k http-válaszkódjainak ismertetése
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 405381a2d80ee6469b2b90bcb25923004fe55882
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270246"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Közös API-válaszkódok és jelentésük

A [szerzői](https://go.microsoft.com/fwlink/?linkid=2092087) és [végponti](https://go.microsoft.com/fwlink/?linkid=2092356) API-k HTTP-válaszkódokat adnak vissza. Bár a válaszüzenetek egy kérésre vonatkozó információkat tartalmaznak, a HTTP-válasz állapotkódja általános.

## <a name="common-status-codes"></a>Közös állapotkódok
Az alábbi táblázat a [szerzői](https://go.microsoft.com/fwlink/?linkid=2092087) és [végponti](https://go.microsoft.com/fwlink/?linkid=2092356) API-k leggyakoribb HTTP-válaszállapot-kódjait sorolja fel:

|Kód|API|Magyarázat|
|:--|--|--|
|400|Szerzői, végpont|a kérelem paraméterei helytelenek, ami azt jelenti, hogy a szükséges paraméterek hiányoznak, hibásak vagy túl nagyok|
|400|Szerzői, végpont|a kérelem törzse helytelen, ami azt jelenti, hogy a JSON hiányzik, hibás vagy túl nagy|
|401|Tartalomkészítés|használt végpont-előfizetési kulcs a kulcs készítése helyett|
|401|Szerzői, végpont|érvénytelen, hibásan formázott vagy üres kulcs|
|401|Szerzői, végpont| a kulcs nem egyezik a régióval|
|401|Tartalomkészítés|nem ön a tulajdonos vagy munkatárs|
|401|Tartalomkészítés|érvénytelen API-hívások sorrendje|
|403|Szerzői, végpont|a havi kulcskvóta-korlát túllépése|
|409|Végpont|alkalmazás még mindig betöltődik|
|410|Végpont|újra kell képezni, és újra közzé kell tenni|
|414|Végpont|A lekérdezés túllépi a maximális karakterkorlátot|
|429|Szerzői, végpont|Túllépte a sebességkorlátot (kérelmek/másodperc)|

## <a name="next-steps"></a>További lépések

* REST API [szerzői](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) és [végpontdokumentációja](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
