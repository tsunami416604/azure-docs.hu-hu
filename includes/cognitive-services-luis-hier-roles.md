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
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: dd99218765c873120c4117a3ec1712fe0a605e66
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480368"
---
**Kérdés**: érdemes használni egy hierarchikus entitást vagy a minta egy egyszerű entitás szerepkörökkel rendelkező? 

**Válasz**: attól függ, hogy. Minták és példa utterances összehasonlítható, hogy a felhasználó utterance (kifejezés) képviselnek, és adott megjelölésű.  

Ha megcímkézzen nincs egyértelmű mintát, használja a hierarchikus entitásokat. 

|hierarchikus entitások|A szerepkörök egyszerű entitás|
|--|--|
|kell a gyermekentitások példa utterances címkézte leképezések a|Példa utterances, rendelkeznie kell **szerepkörök nem címkével, a leképezések**|
|használhatja a minták|**kell** minták használata|
|Előfordulhat, hogy kell **további** példa utterances a leképezés|Előfordulhat, hogy kell ** kevesebb példa utterances a leképezés|