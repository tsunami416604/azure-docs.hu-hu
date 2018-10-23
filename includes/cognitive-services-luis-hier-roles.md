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
ms.openlocfilehash: 1d7723f356274bbd18b1ea08e34046da82a1057c
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646354"
---
**Kérdés**: érdemes használni egy hierarchikus entitást vagy a minta egy egyszerű entitás szerepkörökkel rendelkező? 

**Válasz**: attól függ, hogy. Minták és példa utterances összehasonlítható, hogy a felhasználó utterance (kifejezés) képviselnek, és adott megjelölésű.  

Ha megcímkézzen nincs egyértelmű mintát, használja a hierarchikus entitásokat. 

|hierarchikus entitások|A szerepkörök egyszerű entitás|
|--|--|
|kell a gyermekentitások példa utterances címkézte leképezések a|Példa utterances, rendelkeznie kell **szerepkörök nem címkével, a leképezések**|
|használhatja a minták|**kell** minták használata|
|Előfordulhat, hogy kell **további** példa utterances a leképezés|Előfordulhat, hogy kell **kevesebb** példa utterances a leképezés|
