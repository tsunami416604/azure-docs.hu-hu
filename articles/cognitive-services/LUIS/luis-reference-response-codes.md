---
title: API HTTP-reagálási kódok – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS Authoring and Endpoint API-k által visszaadott HTTP-válaszi kódok ismertetése
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270246"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Általános API-válaszok kódjai és azok jelentése

A [szerzői](https://go.microsoft.com/fwlink/?linkid=2092087) és [végponti](https://go.microsoft.com/fwlink/?linkid=2092356) API-k http-válasz kódokat adnak vissza. Míg a válaszüzenetek a kérelemre vonatkozó információkat tartalmaznak, a HTTP-válasz állapotkód általános.

## <a name="common-status-codes"></a>Common status codes
A következő táblázat a [szerzői műveletek](https://go.microsoft.com/fwlink/?linkid=2092087) és a [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) API-jai leggyakoribb http-válasz-állapotkódok listáját tartalmazza:

|Kód|API|Magyarázat|
|:--|--|--|
|400|Szerzői műveletek, végpont|a kérelem paraméterei helytelenek, mert a szükséges paraméterek hiányoznak, helytelenül formázottak vagy túl nagyok.|
|400|Szerzői műveletek, végpont|a kérelem törzse helytelen, mert a JSON hiányzik, helytelenül formázott vagy túl nagy.|
|401|Tartalomkészítés|használt végpont-előfizetési kulcs, a szerzői kulcs helyett|
|401|Szerzői műveletek, végpont|Érvénytelen, helytelen formátumú vagy üres kulcs|
|401|Szerzői műveletek, végpont| a kulcs nem egyezik a régióval|
|401|Tartalomkészítés|Ön nem a tulajdonos vagy a közreműködő|
|401|Tartalomkészítés|az API-hívások sorrendje érvénytelen|
|403|Szerzői műveletek, végpont|túllépte a havi kulcsokra vonatkozó kvóta maximális számát|
|409|Végpont|az alkalmazás még betöltődik|
|410|Végpont|az alkalmazást újra kell képezni és újra közzé kell tenni|
|414|Végpont|a lekérdezés meghaladja a maximális karakteres korlátot|
|429|Szerzői műveletek, végpont|Túllépte a díjszabási korlátot (kérelmek/másodperc)|

## <a name="next-steps"></a>További lépések

* A [szerzői](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) és [végponti](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) dokumentáció REST API
