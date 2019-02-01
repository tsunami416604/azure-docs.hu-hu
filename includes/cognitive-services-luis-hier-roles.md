---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: dee31fca841ea309128681637cc41fa0fb1e7aea
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55480519"
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
