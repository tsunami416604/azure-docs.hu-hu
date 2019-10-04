---
title: Conversation Learner alapértelmezett konfiguráció – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Az alapértelmezett Conversation Learner konfiguráció ismertetése.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: aaef6f5498e5a8da65d1c829feae8b3e85dba0fd
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705294"
---
# <a name="default-values-and-boundaries"></a>Alapértelmezett értékek és határok

Ez a dokumentum a Conversation Learner és a legfontosabb szolgáltatási határok alapértelmezett konfigurációját ismerteti.

## <a name="default-configuration"></a>Alapértelmezett konfiguráció

Paraméter | Alapértelmezett érték
--- | --- 
Alapértelmezett munkamenet időkorlátja | 30 perc

## <a name="boundaries"></a>Határok

Paraméter | Korlát
--- | --- 
API-k készítése, maximális HTTP-hívások havonta | 5P
API-k készítése, max. HTTP-hívások másodpercenként | 25
Munkamenet-API, maximális HTTP-hívások havonta | 500 000
Munkamenet-API, HTTP-hívások maximális száma másodpercenként | 10
Egyéni (nem programozott) entitások maximális száma modell szerint | Lásd: [Luis határok doc](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries); a gyakorlatban a tényleges szám lehet valamivel kisebb
Előre elkészített entitások maximális száma modell szerint | Lásd: [Luis határok doc](https://docs.microsoft.com/azure/cognitive-services/luis/luis-boundaries)
Entitások maximális száma (összesen)/modell | 100
Műveletek maximális száma modell szerint | 32
A betanítási párbeszédpanelek maximális száma modell szerint | 1000
A felhasználó által kiváltott felhasználók maximális száma | 100
Naplózási párbeszédpanelek maximális száma egy modellben | Nincs előre beállított korlát, de a naplózási párbeszédpanelek csak rögzített ideig maradnak, mielőtt elveti őket.  Emellett a Conversation Learner felhasználói felületén egyszerre az 100-es naplózási párbeszédpanel jelenik meg. 
Modellek maximális száma felhasználónként | Nincs előre beállított korlát
Szekvenciális nem várakozási műveletek maximális száma | 5 (*)

(*) 5 szekvenciális nem várakozási művelet után a rendszer az összes nem várt műveletet eltakarja, és Conversation Learner az elérhető várakozási műveletek közül választhat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Conversation Learner](./quickstart.md)
