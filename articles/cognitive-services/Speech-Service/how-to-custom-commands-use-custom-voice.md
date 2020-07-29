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
ms.openlocfilehash: b1e488653f210089ee5ed6757fb2f3d1e4fb0fe4
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294297"
---
# <a name="use-custom-commands-with-custom-voice"></a>Egyéni parancsok használata Custom Voice szolgáltatással

Ebből a cikkből megtudhatja, hogyan választhatja ki az egyéni parancsok alkalmazás egyéni kimeneti hangját.

## <a name="select-a-custom-voice"></a>Egyéni hang kiválasztása

1. Az egyéni parancsok alkalmazásban válassza a bal oldali ablaktábla **Beállítások** elemét.
1. Válassza az **egyéni hang** lehetőséget a középső ablaktáblán.
1. Válassza ki a kívánt egyéni vagy nyilvános hangot a táblából.
1. Válassza a **Mentés** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Minta mondatok paraméterekkel](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - A **nyilvános hangok**esetében az **neurális típusok** csak bizonyos régiókban érhetők el. A rendelkezésre állás ellenőrzéséhez tekintse meg a [standard és a neurális hangok régiónként/végpont szerint](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)című témakört.
> - Az **Egyéni hangokat**az egyéni hangprojektek lapról lehet létrehozni. Lásd: Ismerkedés [az egyéni hanggal](./how-to-custom-voice.md).

Mostantól az alkalmazás az alapértelmezett hang helyett a kiválasztott hangon fog válaszolni.
