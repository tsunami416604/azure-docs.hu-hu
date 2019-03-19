---
title: Az Azure Media Services egy miniatűr sprite készítése |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozhat létre az Azure Media Services egy miniatűr sprite.
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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57991660"
---
# <a name="generate-a-thumbnail-sprite"></a>Hozzon létre egy miniatűr sprite  

Media Encoder Standard használatával hozzon létre egy miniatűr sprite, amely egy JPEG-fájlt, amely több kis feloldási miniatűrök stitched együtt lemezképpel egy egyetlen (nagy), és a egy VTT fájlt tartalmazza. Ez a fájl VTT az időtartományt a bemeneti videó, amely az egyes miniatűrökre jelöli, és a méretét és a koordináták a miniatűr belül a nagyméretű JPEG-fájlok adja meg. Videolejátszók VTT fájl- és sprite rendszerképét használhatják a vizuális visszajelzést az megjelenítő számára, amikor újra a tisztítási egy "vizuális" seekbar megjelenítése, és továbbítsa a videó ütemtervét mentén.

Annak érdekében, hogy a miniatűr Sprite, a készlet létrehozásához használja a Media Encoder Standard:

1. JPG asztalnak a miniatűrjére formátumot kell használnia
2. Meg kell adnia vagy időbélyegeket Start/lépés vagy Karaktertartomány értékeket, vagy % értékek (és nem keret száma) 
    
    1. Mikor kombinálhatók az időbélyegeket és a % értékek

3. Rendelkeznie kell a SpriteColumn érték egy nem negatív számot nagyobb vagy egyenlő 1

    1. Ha SpriteColumn M > = 1, a kimeneti lemezkép egy téglalapot M oszlopokkal. #2-n keresztül létrehozott miniatűrök értéke nem egy pontos több m, ha az utolsó sor lesz a nem teljes, és fekete képpontok a bal oldali.  

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

1.  Már nem a rendszerképek egyetlen sor sprite képek létrehozása (SpriteColumn = 1 eredmények egy oszloppal a kép).
2.  A közepesen méretű JPEG-képek sprite rendszerképet darabolás még nem támogatott. Ezért ügyelni kell a miniatűrök és azok méretét, számát úgy, hogy a eredő stitched miniatűr Sprite körül 8 M képpont vagy annál kisebb.
3.  Az Azure Media Player sprites támogatja a Microsoft Edge, Chrome és a Firefox böngészőben. Az IE11 VTT elemzés nem támogatott.

## <a name="next-steps"></a>További lépések

[Tartalom kódolása](media-services-encode-asset.md)
