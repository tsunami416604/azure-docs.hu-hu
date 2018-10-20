---
title: Támogatás és Súgó
titlesuffix: Azure Cognitive Services
description: Súgó és támogatja a kérdések és problémák, a Speech szolgáltatással együttműködő alkalmazásokat létrehozásakor
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: wolfma
ms.openlocfilehash: 58fb5d4be2137eed7d62e702f08bdd66c80be347
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49468058"
---
# <a name="support-and-help-options"></a>Támogatás és Súgó

Akkor most használja először a beszédfelismerési szolgáltatás működésének megismerése? Fontolgatja egy új szolgáltatást szeretne az alkalmazáshoz? Az alábbiakban ahol is kaphat segítséget a fejlesztők kapcsolatos javaslatokat.

> [!div class="checklist"]
> * Az új fejlesztések kapcsolatos tájékozott *Azure Cognitive Services*, vagy keresse meg a kapcsolódó legújabb híreket *beszédszolgáltatás*.
> * Keresés a tekintse meg, ha a probléma megoldásához a Közösség által hozzászóló, vagy a szolgáltatás már a megvalósítani kívánt vonatkozó meglévő dokumentáció van-e.
> * Ha nem találja a megfelelő választ, a kérdés feltevése *Stack Overflow*.
> * Ha probléma egy mintát a Githubon találja, emelje egy *GitHub* probléma.
> * Keresse meg a megoldást a *UserVoice fórumot*.

## <a name="stay-informed"></a>Naprakész lehet

Cognitive Services kapcsolatos híreket az összegyűjtött a [a Cognitive Services blogja](https://azure.microsoft.com/blog/topics/cognitive-services/). Beszédszolgáltatás kapcsolatos legújabb információkért nyomon követheti a [Speech service blog](https://azure.microsoft.com/blog/tag/speech-service/).

## <a name="search"></a>Keresés

Előfordulhat, hogy keresse meg a választ kell a dokumentáció, minták, vagy választ [Stack overflow-n](https://www.stackoverflow.com) kérdések vagy minták.

### <a name="scoped-search"></a>Hatókörön belüli keresés

A gyorsabb eredmény érdekében a keresést a Stack overflow-n, a dokumentáció és Kódminták a következő lekérdezést a használatával a [kedvenc keresőmotor](https://bing.com):

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples)
```

Ahol *{Your keresőkifejezéseket}* van a keresési kulcsszavakat.

## <a name="create-an-azure-support-request"></a>Azure-támogatási kérelem létrehozása

Azure-ban létrehozhat és az Azure Portalon a támogatási kérések kezelése.

* [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
* [Az Egyesült Államok kormányának az Azure Portalon](https://portal.azure.us)

## <a name="post-a-question-to-stack-overflow"></a>Új kérdést tenne fel a Stack overflow-n

A stack Overflow az előnyben részesített csatorna fejlesztéssel kapcsolatos kérdéseket. Fontos, ahol a Közösség tagjai, és a Microsoft a csapattagok közvetlenül érintett segít a problémák megoldásában.

Ha nem talál választ a problémára felvétellel, küldje el a Stack Overflow egy másik kérdést. Használja a következő címkék egyikét, ha Ön a kérdés megfogalmazásához:

|Összetevő/terület  |Címkék  |
|---------|---------|
|Beszédfelismerés |[[a microsoft-cognitive + hang-szöveg transzformációs]](http://stackoverflow.com/questions/tagged/microsoft-cognitive+speech-to-text)|
|Beszédszintézishez |[[a microsoft-cognitive + szöveg-,-beszéd átalakítás]](http://stackoverflow.com/questions/tagged/microsoft-cognitive+text-to-speech)|
|Beszédfordítás |[[a microsoft-fordítási cognitive +]](http://stackoverflow.com/questions/tagged/microsoft-cognitive+translation)|
|Beszéd leképezés |[[a microsoft-cognitive + luis]](http://stackoverflow.com/questions/tagged/microsoft-cognitive+luis)|
|Általános beszéd SDK |[[a microsoft-cognitive + microsoft-speech-api]](http://stackoverflow.com/questions/tagged/microsoft-cognitive+microsoft-speech-api)|

> [!TIP]
> A Stack overflow-n a következő bejegyzések tippekkel szolgál az űrlapon kérdések tartalmazhat, és hozzáadása a forráskódja. Ezen irányelvek betartása érdekében előfordulhat, hogy növelheti annak esélyét, hogy a Közösség tagjai mérje fel, és gyorsan válaszolni:  
> * [Hogyan jó kérdések feltevése?](https://stackoverflow.com/help/how-to-ask)
> * [A minimális, a teljes és a ellenőrizhető példa létrehozása](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Egy GitHub-probléma létrehozása

A minták gyakran feladja a nyílt forráskódú. Kérdések és problémák, hozzon létre egy *probléma* a megfelelő GitHub-adattárában. A lekéréses kérelem túl is nyújt. Az alábbi lista tartalmazza a minta tárház mutató hivatkozásokat:

* [Beszéd SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues)
* [Eszköz SDK-t](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/issues)

Hozzon létre egy hibajelentéseit funkcióra vonatkozó kérést, vagy kérje meg egy általános kérdés és megoszthatók az ajánlott eljárások. Hibajelentéseire kövesse a megadott sablon:

**A hiba leírása**

Világos és tömör leírása a bejelentett hiba van.

**Reprodukálnia**

Viselkedés a reprodukálás lépései:
1. ...
2. ...

**Várt működése**

Világos és tömör leírása várt fordulhat elő.

**A Cognitive Services beszédfelismerő SDK verziója**

Az SDK melyik verzióját használja.

**Platform, operációs rendszer és programozási nyelv**

 - Operációs rendszer: [példa: Windows, Linux, Android, iOS,...] – kérjük, az adott
 - Hardver – x64, x86, ARM...
 - Böngésző [példa: Chrome, a Safari] (ha alkalmazható) – kérjük, az adott

**További környezet**

 - Hibaüzenetek és naplóadatok híváslánc...
 - Ha jelenti a egy adott szolgáltatás kapcsolati hiba, jelentse a munkamenet-azonosító és az időt (beleértve a időzóna), az incidensek. Összes hívás-készít biztonsági másolatot/eseményt kap a munkamenet-azonosító jelenti.
 - További adatok


## <a name="uservoice-forum"></a>UserVoice fórumot

Ossza meg ötleteit, hogy a Cognitive Services és a hozzájuk tartozó API-k egyre jobban működnek az alkalmazások fejlesztése. Gyakori kérdésekre adott válaszok a növekvő Tudásbázisunkhoz használatával:

[UserVoice-on](https://cognitive.uservoice.com/)
