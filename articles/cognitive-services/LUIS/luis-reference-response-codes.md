---
title: Nyelvi ismertetése (LUIS) API HTTP válaszkódot - Azure |} Microsoft Docs
titleSuffix: Azure
description: Milyen HTTP válaszkódot ad vissza a LUIS jelentéskészítő és -végpont API-k ismertetése
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 04/16/2018
ms.author: v-geberr
ms.openlocfilehash: 9c7381d9dc2ecf302c85c6b4f1f24b24a28d9ebe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347754"
---
# <a name="luis-api-http-response-codes"></a>LUIS API HTTP válaszkódot.
A [szerzői](https://aka.ms/luis-authoring-apis) és [végpont](https://aka.ms/luis-endpoint-apis) API-kat a HTTP válaszkódot adja vissza. Amíg válaszüzenetek információval kérelemre adott, a HTTP-válasz állapotkódja általános. 

## <a name="common-status-codes"></a>Közös állapotkódok
A következő táblázat felsorolja a leggyakrabban használt HTTP állapotkódok az egyes a [szerzői](https://aka.ms/luis-authoring-apis) és [végpont](https://aka.ms/luis-endpoint-apis) API-kat:

|Kód|API|Magyarázat|
|:--|--|--|
|400|Szerzői, a végpont|kérelem paraméterei helytelen, ami azt jelenti, a szükséges paramétereket: hiányzó, helytelen formátumú vagy túl nagy|
|400|Szerzői, a végpont|kérelem törzse nem megfelelő, ami azt jelenti, a JSON-ja hiányzik, helytelen formátumú, vagy túl nagy|
|401|Szerzői műveletek|előfizetés végpontkulcs használt kulcs szerzői helyett|
|401|Szerzői, a végpont|Érvénytelen, nem megfelelően formázott vagy üres kulcs|
|401|Szerzői, a végpont| kulcs nem egyezik meg a régió|
|401|Szerzői műveletek|nem áll a tulajdonos vagy közreműködő|
|401|Szerzői műveletek|az API-hívások érvénytelen sorrendje|
|403|Szerzői, a végpont|teljes havi kulcs kvótakorlátot túllépve|
|409|Végpont|alkalmazás még betöltés|
|410|Végpont|alkalmazást retrained és újból közzé kell|
|414|Végpont|lekérdezés meghaladja a megengedett karakterek maximális számát|
|429|Szerzői, a végpont|Sávszélesség-korlátjának túllépése (kérelmek/másodperc)|