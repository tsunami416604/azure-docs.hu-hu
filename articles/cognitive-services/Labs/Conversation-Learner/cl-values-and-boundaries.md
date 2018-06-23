---
title: Beszélgetés tanuló alapértelmezett konfiguráció – Microsoft kognitív szolgáltatások |} Microsoft Docs
titleSuffix: Azure
description: Ismerje meg az alapértelmezett téma tanuló konfigurációt.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 56e2140b83bf1c5722a459c14f31b2b4b0ba6b15
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348583"
---
# <a name="default-values-and-boundaries"></a>Az alapértelmezett értékeket, és határokat

Ez a dokumentum ismerteti a beszélgetés tanuló, és a kulcsszolgáltató határok használható alapértelmezett konfigurációt.

## <a name="default-configuration"></a>Alapértelmezett konfigurációja

Paraméter | Alapértelmezett érték
--- | --- 
Alapértelmezett munkamenet időkorlátja | 30 perc

## <a name="boundaries"></a>Határok

Paraméter | Korlát
--- | --- 
Szerzői API, maximális HTTP hívások száma havonta | 5M
Szerzői API, maximális HTTP hívások száma másodpercenként | 25
Munkamenet API-t maximális HTTP-hívások száma havonta | 500 000
Munkamenet API-t maximális HTTP-hívások száma másodpercenként | 10
Egyéni (nem programozott) entitások alkalmazásonként maximális száma | Lásd: [LUIS határok doc](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries); a gyakorlatban, tényleges szám valamivel kisebb lehet
Előre elkészített entitások alkalmazásonként maximális száma | Lásd: [LUIS határok doc](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-boundaries)
Entitások (összesen) alkalmazásonként maximális száma | 100
Műveletek alkalmazásonként maximális száma | 32
Maximális száma alkalmazásonként vonat párbeszédpanelek | 1000
Felhasználók maximális száma fordulat / vonat párbeszédpanel | 100
Napló párbeszédpanelek alkalmazásonként maximális száma | Nincs előre beállított korlátot, de a napló párbeszédpanelek csak megmaradnak megsemmisülne meghatározott időtartamra.  A beszélgetés tanuló felhasználói felület is, megjelenítése a 100 napló párbeszédpanelek egyszerre. 
Felhasználónkénti alkalmazások maximális számát | Nincs előre beállított korlát
Szekvenciális nem-wait-műveletek maximális száma | 5 (*)

(*) 5 szekvenciális nem várakozási műveletek az összes nem-wait műveletek maszkolva és beszélgetés tanuló fogja választani a rendelkezésre álló várakozási műveletek között.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ismerkedés a beszélgetés tanuló](./quickstart.md)
