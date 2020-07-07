---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838758"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech-erőforrás létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Állítsa be a fejlesztési környezetet, és hozzon létre egy üres projektet](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Győződjön meg arról, hogy van hozzáférése egy mikrofonhoz a hangrögzítéshez

## <a name="source-code"></a>Forráskód

Hozzon létre egy *Quickstart.py* nevű fájlt, és illessze be a következő Python-kódot.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Kód magyarázata

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Alkalmazás létrehozása és futtatása

Most már készen áll a beszédfelismerés tesztelésére a beszédfelismerési szolgáltatás használatával. 

Ha a macOS rendszeren fut, és ez az első olyan Python-alkalmazás, amely mikrofont használ, valószínűleg terminál-hozzáférést kell biztosítania a mikrofonhoz. Nyissa meg a **Rendszerbeállítások** elemet, és válassza a **biztonsági & adatvédelem**lehetőséget. Ezután válassza az **Adatvédelem** lehetőséget, és keresse meg a kívánt **mikrofont** a listában. Végül válassza a **terminál** és a mentés lehetőséget. 

1. **Indítsa el az alkalmazást** – a parancssorból írja be a következőt:
    ```bash
    python quickstart.py
    ```
2. **Felismerés elindítása** – a rendszer felszólítja, hogy beszéljen egy angol nyelvű kifejezéssel. A rendszer elküldje a beszédet a beszédfelismerési szolgáltatásnak, amelyet szövegként leír, és a konzolon jeleníti meg.

## <a name="next-steps"></a>További lépések

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
