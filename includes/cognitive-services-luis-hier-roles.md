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
ms.openlocfilehash: 3803bc7f1e0da01fbaa8aa11bc6e8fc5c508b5ae
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528834"
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
