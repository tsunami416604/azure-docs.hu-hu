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
ms.devlang: na
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 25d26db3cd11fff7f7e9ba472247a920ecddea33
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47090734"
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
