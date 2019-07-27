---
title: API HTTP-reagálási kódok – LUIS
titleSuffix: Azure Cognitive Services
description: Mely HTTP-válaszkódot kapott a LUIS szerzői és végponti API-k ismertetése
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: c92bcf55b571c37efa308d9121ee4aee714e684a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560070"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Általános API-válaszok kódjai és azok jelentése

A [szerzői](https://go.microsoft.com/fwlink/?linkid=2092087) és [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) API-k HTTP-válaszkódot adja vissza. Parancsválasz-üzeneteket egy kérelem-re vonatkozó információk közé tartozik, míg a HTTP-válaszként kapott állapotkód általános. 

## <a name="common-status-codes"></a>Közös állapotkódok
Az alábbi táblázat a leggyakoribb HTTP-válasz állapota kódokat a némelyike a [szerzői](https://go.microsoft.com/fwlink/?linkid=2092087) és [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) API-kat:

|Kód|API|Magyarázat|
|:--|--|--|
|400|Szerzői műveletek, a végpont|kérés paraméterei helytelen, ami azt jelenti, a szükséges paraméterek: hiányzó, hibás vagy túl nagy|
|400|Szerzői műveletek, a végpont|kérelem törzse nem megfelelő, ami azt jelenti, a JSON-ja hiányzik, hibás vagy túl nagy|
|401|Szerzői műveletek|előfizetés végpontkulcsának használt kulcs létrehozási helyett|
|401|Szerzői műveletek, a végpont|Érvénytelen, hibás vagy üres kulccsal|
|401|Szerzői műveletek, a végpont| kulcs nem egyezik meg a régiót|
|401|Szerzői műveletek|Ön nem a tulajdonos vagy közreműködő|
|401|Szerzői műveletek|Érvénytelen order az API-hívások|
|403|Szerzői műveletek, a végpont|teljes havi kulcs kvótakorlát túllépve|
|409|Végpont|alkalmazások betöltése folyamatban van|
|410|Végpont|retrained és újra közzé kell Application|
|414|Végpont|lekérdezés meghaladja a maximális karakterszámot.|
|429|Szerzői műveletek, a végpont|Költési korlát túllépve (kérelem/másodperc)|

## <a name="next-steps"></a>További lépések

* A [szerzői](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) és [végponti](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) dokumentáció REST API
