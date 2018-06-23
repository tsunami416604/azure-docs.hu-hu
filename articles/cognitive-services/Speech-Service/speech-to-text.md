---
title: Beszédfelismerés szöveg kapcsolatos |} Microsoft Docs
description: Beszéd szöveg API funkcióinak áttekintése.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 2eee1c6f9158f128ed5ffe575f8f498f1d3eb5e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349743"
---
# <a name="about-the-speech-to-text-api"></a>A beszédfelismerés szöveg API kapcsolatos

A **szöveg beszédfelismerés** API *transcribes* szöveggé, amely az alkalmazás képes megjeleníteni a felhasználó vagy hajthat végre műveletet illetve hang-adatfolyamok bemeneti parancsot. Az API-kat is használható, az SDK-ügyfél könyvtár (a támogatott platformok és nyelvek) vagy a REST API-t.

A **szöveg beszédfelismerés** API a következő szolgáltatásokat nyújtja:

- Beszéd felismerés technológia a Microsoft Advanced – Cortana, Office és más Microsoft-termékek által használt azonos.

- Valós idejű folyamatos használatát. **Beszédfelismerés szöveg** lehetővé teszi a felhasználók audio átírni szöveggé valós időben. Azt is fogadását támogatja az eddigi felismerhető szavakat köztes eredményeit. A szolgáltatás automatikusan felismeri a beszédfelismerés végén. Felhasználók is beállíthatja a további formázási beállításokat, beleértve a kis-és nagybetűk és írásjelek, Profanitás maszkolás és a szöveg normalizálási.

- Optimalizált **szöveg beszédfelismerés** interaktív, beszélgetés, eredményeit és Diktálás forgatókönyvek. 

- A több nyelvjárást sok szóbeli nyelvek támogatása. A támogatott nyelveket mindegyik mód teljes listájáért lásd: [támogatott nyelv](supported-languages.md#speech-to-text).

- Testre szabott nyelvi és akusztikus modellek, így az alkalmazás számára, és beszéljen, a környezet és a speciális szóhasználatának beszéd a felhasználók módon szabhatja.

- Természetes nyelvű ismertetése. Való integrálásán keresztül [nyelvi ismertetése](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), akkor is származik leképezések és entitások beszéd. Felhasználók tudni, hogy az alkalmazás szóhasználatának nem rendelkezik, de mi meg kívánják a saját szavak is ismertetik.

## <a name="api-capabilities"></a>API-képességek

Bizonyos képességeit a **szöveg beszédfelismerés** API REST-en keresztül nem érhetők el. A következő táblázat összefoglalja az egyes módszerek férnek hozzá a az API-t képességeit.

| Használati eset | REST | SDK-k |
|-----|-----|-----|----|
| Átvezeti rövid utterance, például a parancs (hossz < 15 s). Nincs köztes eredmények | Igen | Igen |
| Átvezeti egy hosszabb utterance (> 15 s) | Nem | Igen |
| Nem kötelező, ideiglenes eredményekkel adatfolyam hang átvezeti | Nem | Igen |
| Beszélőfelismerési leképezések keresztül LUIS ismertetése | nem\* | Igen |

\* *LUIS leképezések és entitások származtathatók egy külön LUIS az előfizetést. Az ehhez az előfizetéshez az SDK LUIS hívja meg, és megadja az entitáshoz és szándéka eredményeket, valamint a beszédfelismerés transcriptions. A REST API-val LUIS hívása a leképezések és LUIS előfizetése rendelkező entitások származtatni.*

## <a name="next-steps"></a>További lépések

* [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Lásd: how to a C# beszéd felismerésére](quickstart-csharp-windows.md)
