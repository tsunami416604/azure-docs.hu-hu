---
title: Tanulási szabályzat kezelése – személyre szabás
description: Ez a cikk a személyre szabással kapcsolatos gyakran ismételt hibaelhárítási kérdésekre adott válaszokat tartalmazza.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 707ba9fe7b73ef74e21321533ba02b11f2bad850
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75843481"
---
# <a name="manage-learning-policy"></a>Tanulási szabályzat kezelése

A képzési szabályzat beállításai határozzák meg a _hiperparaméterek beállításaát_ . A tanulási szabályzat egy `.json` fájlban van definiálva.

## <a name="import-a-new-learning-policy"></a>Új képzési szabályzat importálása

1. Nyissa meg a [Azure Portal](https://portal.azure.com), és válassza ki a személyre szabott erőforrást.
1. Válassza ki a **modell-és tanulási beállításokat** az **Erőforrás-kezelés** szakaszban.
1. A **tanulási beállítások importálása** lehetőségnél válassza ki a fent megadott JSON-formátummal létrehozott fájlt, majd kattintson a **feltöltés** gombra.

    Várjon, amíg befejeződik a tanulási szabályzat feltöltése.

## <a name="export-a-learning-policy"></a>Képzési szabályzat exportálása

1. Nyissa meg a [Azure Portal](https://portal.azure.com), és válassza ki a személyre szabott erőforrást.
1. Válassza ki a **modell-és tanulási beállításokat** az **Erőforrás-kezelés** szakaszban.
1. A **tanulási beállítások importálása** gombra kattintva válassza a **tanulási Beállítások exportálása** gombot. Ezzel menti a `json` fájlt a helyi számítógépre.

## <a name="next-steps"></a>Következő lépések

Tudnivalók a tanulási szabályzatokkal kapcsolatos [fogalmakról](concept-active-learning.md#learning-settings)

[A régió elérhetőségének megismerése](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)