---
title: Miniatűr sprite létrehozása az Azure Media Services szolgáltatással | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan hozhat létre egy miniatűr sprite az Azure Media Services használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e5d32d1bc3bd704b03e58c62251a323ed3f4662c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61229050"
---
# <a name="generate-a-thumbnail-sprite"></a>Miniatűr sprite létrehozása  

A Media Encoder Standard segítségével miniatűr sprite-ot hozhat létre, amely egy JPEG-fájl, amely több kis felbontású bélyegképet tartalmaz, egyetlen (nagy) képbe fűzve, egy VTT-fájllal együtt. Ez a VTT-fájl határozza meg az egyes miniatűrök által képviselt bemeneti videó időtartományát, valamint a miniatűr méretét és koordinátáit a nagy JPEG-fájlban. A videólejátszók a VTT fájlt és a sprite képet használják a "vizuális" keresősáv megjelenítéséhez, vizuális visszajelzést adva a nézőnek, amikor a videó idővonala mentén súrolja a vissza és előre.

Annak érdekében, hogy a Media Encoder Standard segítségével létrehozhassa a Miniatűr Sprite-ot, az előre beállított:

1. JPG miniatűr képformátumot kell használnia
2. Meg kell adnia a Kezdő/Lépés/Tartomány értékeket időbélyegként vagy % értékként (és nem keretszámként) 
    
    1. Az időbélyegek és a % értékek összekeverése nem baj

3. A SpriteColumn értéknek 1-nél nagyobb vagy azzal egyenlő nem negatív számmal kell rendelkeznie

    1. Ha a SpriteColumn értéke M >= 1, a kimeneti kép egy M oszlopokkal rendelkező téglalap. Ha a #2 keresztül létrehozott bélyegképek száma nem pontosan többszöröse az M-nek, az utolsó sor nem lesz teljes, és fekete képpontokkal marad.  

Például:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>Ismert problémák

1.  Egyetlen képsort tartalmazó sprite-kép nem generálhat sprite-képet (a SpriteColumn = 1 egyetlen oszlopos képet eredményez).
2.  A sprite-képek közepes méretű JPEG-képekbe való darabolása még nem támogatott. Ezért ügyelni kell arra, hogy korlátozzák a miniatűrök számát és méretét, hogy az így keletkezett varrott Thumbnail Sprite körülbelül 8M pixel vagy annál kevesebb.
3.  Az Azure Media Player támogatja a sprite-okat a Microsoft Edge, a Chrome és a Firefox böngészőkben. A VTT-elemzés nem támogatott az IE11-ben.

## <a name="next-steps"></a>További lépések

[Tartalom kódolása](media-services-encode-asset.md)
