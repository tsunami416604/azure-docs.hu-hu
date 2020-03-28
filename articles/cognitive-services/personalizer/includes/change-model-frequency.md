---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122893"
---
## <a name="change-the-model-update-frequency"></a>A modellfrissítés gyakoriságának módosítása

Az Azure Portalon a Configuration **(Szabó)** erőforrás a konfigurációs lapon módosítsa a **modell frissítési gyakoriságát** 10 másodpercre. Ez a rövid időtartam gyorsan betanítja a szolgáltatást, lehetővé téve, hogy lássa, hogyan változik a legfelső művelet az egyes iterációkhoz.

![Modellfrissítési gyakoriság módosítása](../media/settings/configure-model-update-frequency-settings.png)

Amikor egy Personalizer hurok első példányosított, nincs modell, mivel nem volt Jutalom API-hívások a vonat. A ranghívások minden elemesetében egyenlő valószínűségeket adnak vissza. Az alkalmazás továbbra is rangsorolja a tartalmat a RewardActionId kimenetével.