---
title: Language Understanding (LUIS) API HTTP-válaszkódot – Azure |} A Microsoft Docs
titleSuffix: Azure
description: Mely HTTP-válaszkódot kapott a LUIS szerzői és végponti API-k ismertetése
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: 6ffab581420c3c74ed659be2b82e2f9c7fda13d0
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443831"
---
# <a name="luis-api-http-response-codes"></a>Intelligens HANGFELISMERÉSI API HTTP-válaszkódot
A [szerzői](https://aka.ms/luis-authoring-apis) és [végpont](https://aka.ms/luis-endpoint-apis) API-k HTTP-válaszkódot adja vissza. Parancsválasz-üzeneteket egy kérelem-re vonatkozó információk közé tartozik, míg a HTTP-válaszként kapott állapotkód általános. 

## <a name="common-status-codes"></a>Közös állapotkódok
Az alábbi táblázat a leggyakoribb HTTP-válasz állapota kódokat a némelyike a [szerzői](https://aka.ms/luis-authoring-apis) és [végpont](https://aka.ms/luis-endpoint-apis) API-kat:

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