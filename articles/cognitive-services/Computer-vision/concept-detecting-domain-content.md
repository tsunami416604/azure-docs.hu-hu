---
title: Tartomány-specifikus tartalommal – a Computer Vision észlelése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan adjon meg egy képet kategorizálási tartományt kép részletesebb információt ad vissza.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 76484a2340e527dc016f321dbafa29adb7c358b5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55157533"
---
# <a name="detecting-domain-specific-content"></a>Tartományspecifikus tartalom észlelése

Emellett a címkézési és a legfelső szintű kategorizálási, Computer Vision is támogatja a speciális (vagy tartomány-specifikus) információt. Speciális információk kinyerése önálló metódusként, vagy magas szintű kategorizálással is megvalósítható. A 86 kategóriára épülő besorolás további finomítására szolgál fogalomkör-specifikus modellek hozzáadásával.

A fogalomkör-specifikus modellek két módon használhatók:

* Hatókörön belüli elemzés  
  Csak a kiválasztott modell elemzése egy HTTP POST-hívás meghívásával. Ha tudja, melyik modellt érdemes használni, adja meg a modell neve. Csak kap információkat megfelelő ehhez a modellhez. Ezt a lehetőséget használhatja például akkor, ha a cél csak a hírességek felismerése. A válasz az esetleg felismert hírességek listáját adja vissza, a felismerésük megbízhatósági értékével együtt.
* Fejlett elemzés  
  Az elemzés további, a 86 besorolási kategóriához kapcsolódó részleteket szolgáltat. Ez a lehetőség olyan alkalmazásokban használható, amelyekben a felhasználó az egy vagy több fogalomkör-specifikus modellből származó részleteken kívül általános képelemzést is szeretne. Ennek a metódusnak a hívásakor először a 86 kategóriaelnevezést használó besorolás lesz meghíva. Az ismert vagy egyező modellek, amelyek egyeznek, kategóriákra az osztályozó által igénybe vett meghívásához egy második fázishoz a következő. Például ha a `details` paraméter a HTTP POST-hívás vagy értéke "all" vagy "hírességek" tartalmazza, a metódus meghívja a hírességek osztályozó után a 86-kategória osztályozó nevezzük. Ha a kép akkor minősül `people_` vagy egy kategóriát, majd a hírességek osztályozó alkategóriája nevezzük.

## <a name="listing-domain-specific-models"></a>Tartomány-specifikus modelleket listázása

A Computer Vision által támogatott tartomány-specifikus modelleket listázhatja. Jelenleg a Computer Vision támogatja a következő tartomány-specifikus modelleket a tartomány-specifikus tartalom észlelése:

| Name (Név) | Leírás |
|------|-------------|
| hírességek | Hírességek felismerése, támogatja a lemezképek besorolni a `people_` kategória |
| Arcrész | Tereptárgyak felismerése, támogatja a lemezképek besorolni a `outdoor_` vagy `building_` kategóriák |

### <a name="domain-model-list-example"></a>Tartományi modell példa

A következő JSON-választ a Computer Vision által támogatott tartomány-specifikus modelleket sorolja fel.

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [lemezképek kategorizálásához](concept-categorizing-images.md).
