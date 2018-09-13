---
title: Bemutató kép
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Computer Vision segítségével az Azure Cognitive Servicesben lemezképek leíró kapcsolatos fogalmakat.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: be59055a2c6cd1366c8c52370fa97158ab8d6c88
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725397"
---
# <a name="describing-images"></a>Bemutató kép

Számítógépes Látástechnológiai algoritmus elemezheti a tartalmat a képet. Ez az elemzés egy "leírás" teljes mondatokban emberek számára olvasható nyelveként megjelenik a alapját képezi. A leírás összefoglalja, hogy mi az a képen található. Számítógépes Látástechnológiai algoritmus alapuló a vizuális jellemzőket azonosítja a kép különböző leírások létrehozásához. A leírásokat a rendszer kiértékeli, majd megbízhatósági pontszámot hoz létre hozzájuk. A megbízhatósági pontszámok listája csökkenő sorrendbe rendezve érkezik vissza.

## <a name="image-description-example"></a>Kép leírását bemutató példa

A következő JSON-választ mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha a alapuló a vizuális jellemzőket példaképen leíró.

![B & W épületek](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

A robot, amely ezt a technológiát használja, a lemezkép feliratok létrehozása egy példát találhat [Itt](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

## <a name="next-steps"></a>További lépések

Fogalmak ismertetése [rendszerképek címkézése](concept-tagging-images.md) és [lemezképek kategorizálásához](concept-categorizing-images.md).