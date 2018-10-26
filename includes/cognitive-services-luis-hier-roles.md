---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 0ad3b360611a12b95568e9a20f13a57c93b2e603
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50086119"
---
## <a name="roles-versus-hierarchical-entities"></a>Szerepkörök és a hierarchikus entitások

Használja a hierarchikus entitás vagy minta egy egyszerű entitás szerepkörökkel rendelkező? 

Attól függ, hogy. Minták és példa utterances összehasonlítható, hogy a felhasználó utterance (kifejezés) képviselnek, és adott megjelölésű.  

Ha megcímkézzen nincs egyértelmű mintát, használja a hierarchikus entitásokat. 

|hierarchikus entitások|A szerepkörök egyszerű entitás|
|--|--|
|A leképezés a példa utterances és minták|Csak a minták érhető el|
|Kell leképezést gyermekentitások és a példa utterances címkézte|Szerepkörök nem címkézhetők a példa utterances a leképezés|
|Előfordulhat, hogy kell **további** példa utterances a célja, hogy az entitás kinyerése|Kell **kevesebb** példa utterances a leképezés|
