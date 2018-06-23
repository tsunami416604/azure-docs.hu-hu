---
title: Érzelemfelismerési API áttekintése |} Microsoft Docs
description: A Microsoft a legmodernebb, felhőalapú érzelemfelismerési felismerés algoritmus segítségével több személyre szabott alkalmazások kognitív szolgáltatásokban Érzelemfelismerési API-val.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 8383370cba3f78060e809f444f4ad3dab7380f4e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347659"
---
# <a name="what-is-emotion-api"></a>Mi az a Érzelemfelismerési API-t?

> [!IMPORTANT]
> Érzelemfelismerési API elavulttá vált a 2017. október 30. A funkció most részét képezi [Arcfelismerési API](https://docs.microsoft.com/en-us/azure/cognitive-services/face/).

Üdvözli a Microsoft Érzelemfelismerési API, amely lehetővé teszi a Microsoft felhőalapú érzelemfelismerési felismerés algoritmussal több személyre szabott alkalmazásokat lehet készíteni.

### <a name="emotion-recognition"></a>Érzelemfelismerési felismerés

Érzelemfelismerési API bétaverzió veszi a lemezkép bemenetként, és adja vissza az vetett bizalmat érzelmek minden lap, a lemezkép, valamint a felülethez határolókeret csoportja között a Arcfelismerési API-t. A érzelmek észlelte: Boldogsága, sadness, jelzés nélküli, utasítás, félelem, contempt, Undort, vagy a semleges. Ezek érzelmek közli cross-culturally és egységesen keresztül az ugyanazon alapvető arcfelismerést kifejezések, ahol Érzelemfelismerési API azonosítja. 

**Eredmények értelmezéséhez:** 

A Érzelemfelismerési API származó eredmények értelmezése során, az észlelt érzelemfelismerési úgy kell értelmezni a legmagasabb pontszámmal rendelkező érzelemfelismerési, pontszámokat normalizálják egy összegével. Előfordulhat, hogy a felhasználók eldönthetik abban, hogy az alkalmazásban, attól függően, hogy igényeiknek magasabb küszöbértéket beállítani. 

Érzelemfelismerés kapcsolatos további információkért tekintse meg az API-referencia: 
  * Alapszintű: Ha egy felhasználó hívása már a Arcfelismerési API-t, hogy küldje el a tapasztalt téglalap bemenetként, és az alapszintű rétegben. [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Ha a felhasználó nem küldenek egy oldallal téglalap, azok módot kell használniuk normál.  [API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Egy minta elemzéséhez adatfolyam videó Érzelemfelismerési API-t, tekintse meg [valós idejű elemzése videók hogyan](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
