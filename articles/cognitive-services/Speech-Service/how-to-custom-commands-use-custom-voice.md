---
title: 'Útmutató: egyéni parancsok használata egyéni hang-beszédfelismerési szolgáltatással'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben megadhatja az egyéni parancsok alkalmazás kimeneti hangját.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: sausin
ms.openlocfilehash: 4a5c14909606dcb862fcf53d99bc5bc00fba63bd
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025684"
---
# <a name="use-custom-commands-with-custom-voice"></a>Egyéni parancsok használata Custom Voice szolgáltatással

Ebből a cikkből megtudhatja, hogyan választhatja ki az egyéni parancsok alkalmazás egyéni kimeneti hangját.

## <a name="select-a-custom-voice"></a>Egyéni hang kiválasztása

1. Az egyéni parancsok alkalmazásban válassza a bal oldali ablaktábla **Beállítások** elemét.
1. Válassza az **egyéni hang** lehetőséget a középső ablaktáblán.
1. Válassza ki a kívánt egyéni vagy nyilvános hangot a táblából.
1. Kattintson a **Mentés** gombra.

> [!div class="mx-imgBorder"]
> ![Minta mondatok paraméterekkel](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - A **nyilvános hangok** esetében az **neurális típusok** csak bizonyos régiókban érhetők el. A rendelkezésre állás ellenőrzéséhez tekintse meg a [standard és a neurális hangok régiónként/végpont szerint](./regions.md#standard-and-neural-voices)című témakört.
> - Az **Egyéni hangokat** az egyéni hangprojektek lapról lehet létrehozni. Lásd: Ismerkedés [az egyéni hanggal](./how-to-custom-voice.md).

Mostantól az alkalmazás az alapértelmezett hang helyett a kiválasztott hangon fog válaszolni.