---
title: Az Emotion API – áttekintés |} A Microsoft Docs
description: A Microsoft legmodernebb, felhőalapú érzelemfelismerési felismerés algoritmus használatával személyre szabottabb alkalmazásokat készíthet, a Cognitive Services Emotion API-val.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 210990b0f436fd75cb36e71ea28928c457a5232e
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573554"
---
# <a name="what-is-emotion-api"></a>Mi az Emotion API?

> [!IMPORTANT]
> Az Emotion API 2017. október 30 kivezettük. A funkció egyelőre részét képezi [Face API](https://docs.microsoft.com/azure/cognitive-services/face/).

Üdvözli a Microsoft Emotion API, amellyel személyre szabottabb alkalmazásokat készíthet a Microsoft felhőalapú érzelemfelismerési felismerés algoritmus.

### <a name="emotion-recognition"></a>Érzelemfelismerés

Az Emotion API béta bemenetként kép fogadja, és magabiztosan illenek különböző érzelmek minden lap a képen, valamint a face határolókeret a Face API adja vissza. Az észlelt érzelmek boldogság, szomorúság, meglepetés, düh, félelem, megvetés, undor vagy semleges. Ezek az érzelmek kommunikálja kultúrában és egységesen keresztül ugyanazon alapvető arckifejezések, ahol az Emotion API azonosítja. 

**Eredmények értelmezése során:** 

Az Emotion API eredményeinek értelmezése során, az észlelt emotion úgy kell értelmezni, az a legnagyobb pontszámot, az emotion pontszámok van normalizálva, az összeg egy. Előfordulhat, hogy a felhasználók megadhatják való saját alkalmazásukba, attól függően, saját igényeinek megfelelő magasabb megbízhatósági küszöbértéket állíthat be. 

Érzelemfelismerő kapcsolatos további információkért tekintse meg az API-referencia: 
  * Alapszintű: Egy felhasználó már meghívta a Face API, ha azok elküldheti a bemenetként négyszög meghatározása és az alapszintű csomagot használja. [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Ha a felhasználó nem küldenek egy négyszög meghatározása, használjon standard üzemeltetési módra.  [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Az Emotion API streamelt videók elemzéséhez egy minta: [videók valós idejű elemzése hogyan](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
