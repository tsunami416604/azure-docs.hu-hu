---
title: 'Gyors útmutató: egyéni parancsok használata egyéni hanggal (előzetes verzió) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben megadhatja az egyéni parancsok alkalmazás kimeneti hangját.
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: b0ecbc4dc030fa8e7fbe362c1304c3c97278bdf5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456409"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>Gyors útmutató: egyéni parancsok használata egyéni hanggal (előzetes verzió)

Az [előző cikkben](./quickstart-custom-speech-commands-create-parameters.md)egy új egyéni parancs-projektet hoztunk létre, amely paraméterekkel válaszol a parancsokra.

Ebben a cikkben kiválasztunk egy egyéni kimeneti hangot az általunk létrehozott alkalmazáshoz.

## <a name="select-a-custom-voice"></a>Egyéni hang kiválasztása

1. Nyissa meg a [korábban létrehozott](./quickstart-custom-speech-commands-create-parameters.md) projektet
1. A bal oldali panelen válassza a **Beállítások** lehetőséget.
1. Válassza az **egyéni hang** lehetőséget a középső ablaktáblán
1. Válassza ki a kívánt egyéni vagy nyilvános hangot a táblából.
1. Kattintson a **Mentés** gombra

> [!div class="mx-imgBorder"]
> ![minta mondatok paraméterekkel](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> Egyéni hangok hozhatók létre az egyéni hangprojektek lapról. Válassza ki a **Speech Studio** -hivatkozást, majd a kezdéshez **Egyéni hangfelvételt** .

Mostantól az alkalmazás az alapértelmezett hang helyett a kiválasztott hangon fog válaszolni.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Rövid útmutató: Kapcsolódás egyéni parancssori alkalmazáshoz a Speech SDK-val (előzetes verzió)](./quickstart-custom-speech-commands-speech-sdk.md)

