---
title: Intelligens detektálás – Azure Application Insights által észlelt potenciális memóriavesztést |} A Microsoft Docs
description: Alkalmazások az Azure Application Insights a potenciális memóriavesztést figyeléséhez.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: e430b1e976ac26f7320b28d50dd39923066cfa41
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028486"
---
# <a name="memory-leak-detection-preview"></a>Memória észlelését (előzetes verzió)

Az Application Insights automatikusan elemzi az alkalmazás minden egyes folyamat memóriahasználatát, és képes figyelmeztessen a potenciális memóriavesztést vagy nagyobb memória használata.

Ez a funkció nem speciális beállítás nem szükséges [konfigurálása teljesítményszámlálók](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) az alkalmazáshoz. Ez akkor aktív, ha az alkalmazása elég memória teljesítményét számlálók telemetriai adatok (például a saját memória) hoz létre.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Ha szeretné be intelligens detektálás értesítést az ilyen típusú?
Egy tipikus értesítés egy hosszú időn át, egy vagy több folyamat és/vagy egy vagy több gépet, az alkalmazás részét képező memóriát konzisztens növekedése követi. Machine learning-algoritmusok felismeri a nagyobb memóriát, amely megfelel a mintának az memóriavesztés szolgálnak.

## <a name="does-my-app-really-have-a-problem"></a>Az alkalmazás valóban rendelkezik a probléma?
Nem, egy értesítés nem jelenti azt, hogy az alkalmazás mindenképp van probléma. Memória adatszivárgást minták általában alkalmazásproblémák azt jelzik, bár ezek a minták oka az lehet az adott folyamat tipikus vagy természetes üzleti indoklásának rendelkezhet, és figyelmen kívül hagyható.

## <a name="how-do-i-fix-it"></a>Hogyan javíthatom?
Az értesítések közé tartoznak a diagnosztikai információk a diagnosztikai elemzési folyamat támogatásához:
1. **Osztályozási.** Az értesítés (gigabájtban) növelje a memória mennyiségét, és az időtartományt, amelyben nőtt a memória jeleníti meg. Ez segíthet a probléma prioritást rendelhet.
2. **A hatókör.** Hány gépet működtető a memória adatszivárgást minta? Hány kivételek által gyűjtött a potenciális memóriavesztést során? Ez az információ szerezhető az értesítést.
3. **Diagnosztizálja a.** Az észlelés a memória adatszivárgást mintát, idővel a folyamat memóriahasználatát bemutató tartalmaz. Is használhatja a kapcsolódó elemek és jelentések összekapcsolása támogatási információk, további segítséget a probléma diagnosztizálásához.
